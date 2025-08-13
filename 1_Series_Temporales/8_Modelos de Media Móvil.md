## Repaso
- Ver [[2.1_Ejemplos Series Temporales#Media móvil|Media Móvil]]
- Ver [[2.1_Ejemplos Series Temporales#Autorregresiones|Autorregresiones]]

## Ejemplo: Media móvil de orden variable
Consideremos para cada $n$ el proceso:
$$x_t = \frac{1}{n} \left(w_{t} + w_{t-1} + \ldots + w_{t-n+1}\right)$$

con $w_t$ ruido blanco de varianza $\sigma^2_w=1$.

Observar que este proceso es una media móvil de orden $q = n-1$. Lo podemos escribir como:

$$n x_t = w_{t} + w_{t-1} + \ldots + w_{t-n+1}$$

Esta es la parametrización que le pasamos a `arma_generate_sample`:

```python
## Ejemplo: media movil de orden variable
j=1
cmap = ['turquoise','lightgreen','teal','darkgreen']

for n in [1,5,10,20]:
    q=n-1
    x=arma_generate_sample([n],np.ones(n),200)
    x=pd.Series(x)
    plt.subplot(4,1,j)
    plt.plot(x,label=f"n={n}, q={q}", color=cmap[j-1]);
    plt.ylim(-2.5,2.5)
    plt.legend(loc='upper right');
    j=j+1
```
![[Pasted image 20250602200311.png]]

## Ejemplo: MA(1)

Consideremos el proceso:
$$
x_t = w_t + \theta w_{t-1}
$$
con $w_t$ ruido blanco de varianza $\sigma^2_w = 1$ y $\theta = 0.9$.

```python
## Ejemplo: media movil de orden 1
from statsmodels.tsa.arima_process import arma_generate_sample

theta = 0.9
#primer parámetro: coeficientes autorregresivos. Segundo parámetro: coeficientes de media móvil
#El tercer parámetro es la cantidad de muestras.
x=arma_generate_sample([1],[1,theta],200)
x=pd.Series(x)
x.plot();
plt.title(f"Media móvil de orden 1, con parámetros [1,{theta}]");
```
![[Pasted image 20250602200154.png]]

**Propiedades**:
* $E[x_t] = E[w_t] + \theta E[w_{t-1}] = 0$.
* $E[x_t^2] = E[w_t^2] +2\theta E[w_tw_{t-1}] + \theta^2 E[w_{t-1}^2] = (1+\theta^2)\sigma^2_w.$
* $E[x_t x_{t-1}] = E[(w_t + \theta w_{t-1})(w_{t-1} + \theta w_{t-2})] = \theta \sigma^2_w.$
* $E[x_t x_{t-h}] = 0$ si $h>1$ porque los ruidos que participan no están correlacionados.

Resumiendo, la función de autocovarianza de un MA(1) tiene la forma:
$$\gamma(h) = \left\{\begin{array}{ll}(1+\theta^2)\sigma_w^2 & h=0\\ \theta\sigma_w^2 & h=\pm 1 \\ 0 & \text{en otro caso}\end{array}\right.$$

La [[3.2_Estimación de la correlación a partir de muestras|function de autocorrelación]] es entonces:
$$\rho(h) = \left\{\begin{array}{ll}1 & h=0\\ \frac{\theta}{1+\theta^2} & h=\pm 1 \\ 0 & \text{en otro caso}\end{array}\right.$$

```python
from statsmodels.graphics.tsaplots import plot_acf,plot_pacf
theta=0.9
x=arma_generate_sample([1],[1,theta],500)
x=pd.Series(x)
plot_acf(x,bartlett_confint=False, label="Empirica");
plt.scatter(range(0,26),np.concatenate(([1],[theta/(1+theta**2)], np.zeros(24))), label="Teorica");
plt.legend();
```
![[Pasted image 20250602202104.png]]

**Observación:**
La propiedad anterior es general, es decir, si $x_t$ es un proceso $MA(q)$ entonces la ACF de $x_t$ es $0$ para lags $h>q$. Probemos con uno de orden $q=5$ y coeficientes arbitrario

```python
x=arma_generate_sample([1],[1,.5,1,.5,1,.5],500)
x=pd.Series(x)
plot_acf(x,bartlett_confint=False);
```
![[Pasted image 20250602202136.png]]

En cambio, la [[7_Modelos autorregresivos y autocorrelación parcial#Función de autocorrelación parcial (PACF)|PACF]] no tiene el mismo comportamiento.
```python
plot_pacf(x);
```
![[Pasted image 20250602202613.png]]


## Resumen
En el procesoo $MA(q)$:
- La función de autocorrelación tiene $q$ lags activos.
- La función de autocorrelación parcial decae lentamente.

Es decir, a la inversa de lo que ocurre en AR.

## Ajuste de un proceso $MA(q)$
Comencemos por $MA(1)$:
$$
x_t = w_t + \theta w_{t-1}.
$$

El proceso $w_t$ no es directamente observable, entonces no podemos hacer una regresión a sus valores anteriores. Deberíamos "despejar" $w_t$ de algún modo.

Observemos la siguiente recursión:
$$\left.\begin{array}{c}

    x_t = w_t + \theta w_{t-1} \\

    x_{t-1} = w_{t-1} + \theta w_{t-2}\end{array}\right\} \Rightarrow x_t = w_t + \theta x_{t-1} -\theta^2 w_{t-2}$$
Siguiendo la recursión, si $|\theta| < 1$ el modelo se dice *invertible* y podemos escribir:
$$
x_t = \sum_{j=1}^\infty{(-\theta)^jx_{t-j}+w_t}
$$
es decir, convertimos un $MA(1)$ en un $AR(\infty)$ en cierto modo. Como $\theta^j$ es decreciente, podríamos hacer una regresión y tratar de adivinar $\theta$.

**Problema:** Estamos buscando un $\theta$ que **no es lineal**.

### Ejemplo

Voy estimando una $MA(1)$ como regresiones con cada vez más parámetros.

```python
## Ejemplo: simulo un MA(1) con theta = 0.5 y hago varios fits
theta=0.5
x=arma_generate_sample([1],[1,theta],1000)
x=pd.Series(x)
x.plot(title=f"Media móvil de orden 1, coeficientes [1,{theta}]");
```
![[Pasted image 20250603181707.png]]

```python
from statsmodels.formula.api import ols

data = pd.concat([x, x.shift(1)], axis=1).dropna()
data.columns = ["x", "xL1"]

fit = ols("x ~ 0+xL1", data=data).fit()
print(f"MSE de los residuos: {fit.mse_resid}")
fit.summary()
```
```
MSE de los residuos: 1.0609713538139918
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>x</td>        <th>  R-squared (uncentered):</th>      <td>   0.145</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared (uncentered):</th> <td>   0.144</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th>          <td>   168.6</td>
</tr>
<tr>
  <th>Date:</th>             <td>Mon, 19 May 2025</td> <th>  Prob (F-statistic):</th>          <td>9.87e-36</td>
</tr>
<tr>
  <th>Time:</th>                 <td>16:23:04</td>     <th>  Log-Likelihood:    </th>          <td> -1446.6</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   999</td>      <th>  AIC:               </th>          <td>   2895.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   998</td>      <th>  BIC:               </th>          <td>   2900.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     1</td>      <th>                     </th>              <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>              <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
   <td></td>      <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>xL1</th> <td>    0.3801</td> <td>    0.029</td> <td>   12.984</td> <td> 0.000</td> <td>    0.323</td> <td>    0.438</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 0.871</td> <th>  Durbin-Watson:     </th> <td>   1.891</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.647</td> <th>  Jarque-Bera (JB):  </th> <td>   0.951</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.060</td> <th>  Prob(JB):          </th> <td>   0.622</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.909</td> <th>  Cond. No.          </th> <td>    1.00</td>
</tr>
</table><br><br>Notes:<br>[1] R² is computed without centering (uncentered) since the model does not contain a constant.<br>[2] Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
data = pd.concat([x, x.shift(1), x.shift(2)], axis=1).dropna()
data.columns = ["x", "xL1", "xL2"]

fit = ols("x ~ 0+xL1+xL2", data=data).fit()
print(f"MSE de los residuos: {fit.mse_resid}")
fit.summary()
```
```
MSE de los residuos: 1.0398252548742162
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>x</td>        <th>  R-squared (uncentered):</th>      <td>   0.162</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared (uncentered):</th> <td>   0.160</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th>          <td>   96.22</td>
</tr>
<tr>
  <th>Date:</th>             <td>Mon, 19 May 2025</td> <th>  Prob (F-statistic):</th>          <td>6.22e-39</td>
</tr>
<tr>
  <th>Time:</th>                 <td>16:23:04</td>     <th>  Log-Likelihood:    </th>          <td> -1434.6</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   998</td>      <th>  AIC:               </th>          <td>   2873.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   996</td>      <th>  BIC:               </th>          <td>   2883.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     2</td>      <th>                     </th>              <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>              <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
   <td></td>      <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>xL1</th> <td>    0.4341</td> <td>    0.031</td> <td>   13.851</td> <td> 0.000</td> <td>    0.373</td> <td>    0.496</td>
</tr>
<tr>
  <th>xL2</th> <td>   -0.1427</td> <td>    0.031</td> <td>   -4.553</td> <td> 0.000</td> <td>   -0.204</td> <td>   -0.081</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 1.410</td> <th>  Durbin-Watson:     </th> <td>   1.962</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.494</td> <th>  Jarque-Bera (JB):  </th> <td>   1.400</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.030</td> <th>  Prob(JB):          </th> <td>   0.497</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.826</td> <th>  Cond. No.          </th> <td>    1.49</td>
</tr>
</table><br><br>Notes:<br>[1] R² is computed without centering (uncentered) since the model does not contain a constant.<br>[2] Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
data = pd.concat([x, x.shift(1), x.shift(2), x.shift(3)], axis=1).dropna()
data.columns = ["x", "xL1", "xL2", "xL3"]

fit = ols("x ~ 0+xL1+xL2+xL3", data=data).fit()
print(f"MSE de los residuos: {fit.mse_resid}")
fit.summary()
```
```
MSE de los residuos: 1.0243813974085563
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>x</td>        <th>  R-squared (uncentered):</th>      <td>   0.176</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared (uncentered):</th> <td>   0.174</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th>          <td>   70.79</td>
</tr>
<tr>
  <th>Date:</th>             <td>Mon, 19 May 2025</td> <th>  Prob (F-statistic):</th>          <td>1.71e-41</td>
</tr>
<tr>
  <th>Time:</th>                 <td>16:23:04</td>     <th>  Log-Likelihood:    </th>          <td> -1425.2</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   997</td>      <th>  AIC:               </th>          <td>   2856.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   994</td>      <th>  BIC:               </th>          <td>   2871.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     3</td>      <th>                     </th>              <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>              <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
   <td></td>      <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>xL1</th> <td>    0.4531</td> <td>    0.031</td> <td>   14.406</td> <td> 0.000</td> <td>    0.391</td> <td>    0.515</td>
</tr>
<tr>
  <th>xL2</th> <td>   -0.1987</td> <td>    0.034</td> <td>   -5.848</td> <td> 0.000</td> <td>   -0.265</td> <td>   -0.132</td>
</tr>
<tr>
  <th>xL3</th> <td>    0.1288</td> <td>    0.031</td> <td>    4.095</td> <td> 0.000</td> <td>    0.067</td> <td>    0.190</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 2.844</td> <th>  Durbin-Watson:     </th> <td>   1.982</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.241</td> <th>  Jarque-Bera (JB):  </th> <td>   2.494</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.030</td> <th>  Prob(JB):          </th> <td>   0.287</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.762</td> <th>  Cond. No.          </th> <td>    1.81</td>
</tr>
</table><br><br>Notes:<br>[1] R² is computed without centering (uncentered) since the model does not contain a constant.<br>[2] Standard Errors assume that the covariance matrix of the errors is correctly specified.

**Problema:** el procedimiento anterior no es muy práctico, y si quisiéramos hacer regresión no lineal necesitamos un buen estimador inicial de $\theta$. Además no generaliza bien a $MA(q)$.

### Método de los momentos
Recordemos la función de autocorrelación del $MA(1)$.  
$$\rho(h) = \left\{\begin{array}{ll}1 & h=0\\ \frac{\theta}{1+\theta^2} & h=\pm 1 \\ 0 & \text{en otro caso}\end{array}\right.$$

Si estimamos la correlación de orden uno de los datos (es decir la `acf(x)` en el lag 1) podemos plantear:
$$
\hat{\rho}(1) = \hat{\rho} = \frac{\theta}{1+\theta^2},
$$
y despejar $\theta$:
$$
\hat{\rho}\theta^2 - \theta + \hat{rho} = 0
$$
Es decir:
$$
\theta = \frac{1 \pm \sqrt{1-4\hat{\rho}^2}}{2\hat{\rho}} 
$$
**Observación:** Tenemos dos soluciones. ¿Cuál elijo? ¿Qué pasa si da complejo?

- Se elije la "invertible", es decir aquella que da menor a 1.
- En teoría $\rho(1) < 0.5$ si $\theta < 1$ entonces no puede dar complejo. Pero al estimar $\rho$ por $\hat{\rho}$ puede ocurrir.
- Para estimar $\sigma_w^2$ la varianza del ruido, podemos usar $\sigma_x^2 = (1 + \theta^2)\sigma_w^2$ y usar el valor de $\theta$ estimado.

#### Ejemplo
```python
#### Ejemplo
from statsmodels.tsa.api import acf
plot_acf(x)
rho = acf(x)[1]
theta = (1-np.sqrt(1-4*rho**2))/(2*rho)

print(f"Autocorrelación estimada a lag 1: ρ(1)={rho}")
print(f"Ajuste por método de los momentos: θ={theta}")
```
```
Autocorrelación estimada a lag 1: ρ(1)=0.3776200114621945
Ajuste por método de los momentos: θ=0.4562147976238853
```
![[Pasted image 20250603183432.png]]
```python
# Estimo la varianza del ruido por método de los momentos despejando (debería dar 1 en este caso).
sigma_w2 = np.var(x)/(1+theta**2)
print(f"Varianza estimada de wt: {sigma_w2}")
```
```
Varianza estimada de wt: 1.0204510256747432
```

- El método de los momentos se puede generalizar a mayor orden, y también se puede utilizar en el caso $AR(p)$ (donde se denominan Ecuaciones de Yule-Walker)
- Sin embargo, en la práctica, estos estimadores no son los más eficientes. En general, si los ruidos son gaussianos, conviene usar un enfoque de *máxima verosimilitud*. Este es el método que utiliza la bilbioteca `statsmodels` de `python`
- Discutiremos más este método en relación al modelo completo [[9 Modelos ARMA|ARMA]].
