## Introducción:
- Generalmente los [[4_Modelos lineales|modelos lineales]] y [[5_Regresión entre series y regresiones con lag|regresiones entre series y regresiones con lag]] son *insuficientes* para explicar el comportamiento de una serie con dinámica interna.
- Se puede observar que "quedaba correlación" luego de los ajustes.

## Objetivos:
- Introducir la correlación como parte intrínseca de la serie. Hacer que los valores anteriores del proceso influyan directamente en el valor observado actual (autorregresivo, AR), y que valores anteriores del ruido influyan en el valor actual observado (media móvil, MA).
- Combinaciones de los dos anteriores da lugar a los modelos **[[1_Series_Temporales/9 Modelos ARMA.md|ARMA]]**.
- Veremos cómo es posible:
	- Usar modelos **ARMA** para predecir valores futuros de una serie.
	- Ajustar los parámetros de un modelo **ARMA** a partir de observaciones.
- Si agregamos el caso no estacionario, veremos como *diferenciar* una serie puede producir estacionariedad. Esto da lugar a los modelos **ARIMA**.

## Modelos autorregresivos (AR)

**Definición:** ver [[2.1_Ejemplos Series Temporales#Autorregresiones en general|Autorregresiones en general]]

## Ejemplo:
Consideremos el proceso:
$$
x_t = x_{t-1}-0.9x_{t-2} + w_t
$$
o bien
$$
x_t - x_{t-1} + 0.9x_{t-2} = w_t
$$
con $x_0$ y $x_{-1}$ dados (por ejemplo $0$) y $\sigma_w^2 = 1$.

Este es un proceso *autorregresivo de orden 2*. En este caso $a=(1,-1,0.9)$ y $b_0=\sigma_w = 1$.

```python
from scipy.signal import lfilter

x = lfilter([1],[1,-1,0.9],w)
plt.plot(x)
plt.title("Proceso autorregresivo de orden 2");
```

### Autocorrelación del proceso:

Uno tiene a pensar que la autocorrelación del proceso solo tendrá valores significativos para los 2 anteriores. **FALSO**
```python
plot_acf(x, bartlett_confint=False);
```
![[Pasted image 20250527153429.png]]

## Ejemplo
Consideremos el proceso:
$$x_t = 0.9 x_{t-1} + w_t.$$

 Con $x_{0}$ dado (por ejemplo 0).
 
Este es un proceso *autorregresivo de orden 1*. Cada muestra recuerda a la anterior.
```python
w = np.random.normal(loc=0,scale=1,size=500)
x = lfilter([1], [1,-0.9],w)
plt.plot(x)
plt.title("Proceso autorregresivo de orden 1");
```
![[Pasted image 20250527153519.png]]
```python
plot_acf(x, bartlett_confint=False);
```
![[Pasted image 20250527153537.png]]

**Pregunta:** ¿Cómo podemos determinar el orden de un proceso de este tipo? ¿Cómo podemos determinar sus parámetros?

## Ajuste de modelos autorregresivos

### Estimación de un AR(1)
Supongamos que sabemos que el orden es conocido. Por ejemplo, para este caso, de orden 1.
Probemos mirar la correlación entre un valor y el anterior:

```python
#Este comando de pandas plotea x_t vs x_t-1
x=pd.Series(x) #transformo x en una serie temporal
pd.plotting.lag_plot(x,lag=1);
plt.title(f"Coeficiente de correlación: {x.autocorr(1)}");
```
![[Pasted image 20250527154046.png]]

Más precisamente, podemos ajustar un [[5_Regresión entre series y regresiones con lag|modelo lineal]] entre los datos de la serie $x_t$ y la serie "laggeada":

```python
from statsmodels.formula.api import ols
#Podemos llamar a fit haciendo referencia a las columnas de un dataframe.
data = pd.concat([x, x.shift(1)], axis=1).dropna()
data.columns = ["x", "xL1"]

fit = ols("x ~ 0+xL1", data=data).fit()
print(f"MSE de los residuos: {fit.mse_resid}")
fit.summary()
```
```
MSE de los residuos: 0.9355830439467617
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>x</td>        <th>  R-squared (uncentered):</th>      <td>   0.844</td> 
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared (uncentered):</th> <td>   0.844</td> 
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th>          <td>   2694.</td> 
</tr>
<tr>
  <th>Date:</th>             <td>Mon, 19 May 2025</td> <th>  Prob (F-statistic):</th>          <td>4.74e-203</td>
</tr>
<tr>
  <th>Time:</th>                 <td>16:18:19</td>     <th>  Log-Likelihood:    </th>          <td> -690.94</td> 
</tr>
<tr>
  <th>No. Observations:</th>      <td>   499</td>      <th>  AIC:               </th>          <td>   1384.</td> 
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   498</td>      <th>  BIC:               </th>          <td>   1388.</td> 
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
  <th>xL1</th> <td>    0.9188</td> <td>    0.018</td> <td>   51.907</td> <td> 0.000</td> <td>    0.884</td> <td>    0.954</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 1.192</td> <th>  Durbin-Watson:     </th> <td>   2.017</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.551</td> <th>  Jarque-Bera (JB):  </th> <td>   1.012</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.097</td> <th>  Prob(JB):          </th> <td>   0.603</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 3.106</td> <th>  Cond. No.          </th> <td>    1.00</td>
</tr>
</table><br><br>Notes:<br>[1] R² is computed without centering (uncentered) since the model does not contain a constant.<br>[2] Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
#Miramos los residuos del ajuste
residuos = fit.resid
plot_acf(residuos, bartlett_confint=False);
```
![[Pasted image 20250527154516.png]]

### Estimación de un AR(2)
$$
x_t = x_{t-1} - 0.9x_{t-2} + w_t
$$
```python
x = lfilter([1],[1,-1,0.9],w)
plt.plot(x)
plt.title("Proceso autorregresivo de orden 2");
```
![[Pasted image 20250527154605.png]]

Correlaciones con los dos lags anteriores:
```python
x=pd.Series(x) #transformo x en una serie temporal
pd.plotting.lag_plot(x,lag=1);
plt.title(f"Coeficiente de correlación: {x.autocorr(1)}");
```
![[Pasted image 20250527154636.png]]
```python
pd.plotting.lag_plot(x,lag=2);
plt.title(f"Coeficiente de correlación: {x.autocorr(2)}");
```
![[Pasted image 20250527154647.png]]

Nuevamente, ajustamos por mínimos cuadrados un modelo lineal de la serie $x_t$, en función de los dos lags anteriores:
```python
x=pd.Series(x)
data = pd.concat([x, x.shift(1), x.shift(2)], axis=1).dropna()
data.columns = ["x", "xL1", "xL2"]

fit = ols("x ~ 0+xL1+xL2", data=data).fit()
print(f"MSE de los residuos: {fit.mse_resid}")
fit.summary()
```
```
MSE de los residuos: 0.9394490858244653
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>x</td>        <th>  R-squared (uncentered):</th>      <td>   0.869</td> 
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared (uncentered):</th> <td>   0.869</td> 
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th>          <td>   1648.</td> 
</tr>
<tr>
  <th>Date:</th>             <td>Mon, 19 May 2025</td> <th>  Prob (F-statistic):</th>          <td>8.18e-220</td>
</tr>
<tr>
  <th>Time:</th>                 <td>16:18:20</td>     <th>  Log-Likelihood:    </th>          <td> -690.08</td> 
</tr>
<tr>
  <th>No. Observations:</th>      <td>   498</td>      <th>  AIC:               </th>          <td>   1384.</td> 
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   496</td>      <th>  BIC:               </th>          <td>   1393.</td> 
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
  <th>xL1</th> <td>    0.9882</td> <td>    0.019</td> <td>   52.035</td> <td> 0.000</td> <td>    0.951</td> <td>    1.026</td>
</tr>
<tr>
  <th>xL2</th> <td>   -0.9073</td> <td>    0.019</td> <td>  -47.728</td> <td> 0.000</td> <td>   -0.945</td> <td>   -0.870</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 1.013</td> <th>  Durbin-Watson:     </th> <td>   1.953</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.603</td> <th>  Jarque-Bera (JB):  </th> <td>   0.862</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.094</td> <th>  Prob(JB):          </th> <td>   0.650</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 3.079</td> <th>  Cond. No.          </th> <td>    1.78</td>
</tr>
</table><br><br>Notes:<br>[1] R² is computed without centering (uncentered) since the model does not contain a constant.<br>[2] Standard Errors assume that the covariance matrix of the errors is correctly specified.

**Observación:** Los residuos quedan sin autocorrelación (ruido blanco):
```python
plot_acf(fit.resid, bartlett_confint=False);
```
![[Pasted image 20250527154901.png]]

Podemos ver además que los residuos siguen una distribución gaussiana:
```python
from statsmodels.graphics.api import qqplot
qqplot(fit.resid,line='s');
```
![[Pasted image 20250527154924.png]]

De hecho, si el ajuste es bueno, los **residuos** del ajuste deberían parecerse al ruido blanco que dio origen a la serie:
```python
plt.plot(fit.resid, color="teal")
plt.scatter(fit.resid.index, w[2:], color="blue");
```
![[Pasted image 20250527154957.png]]

### Conclusiones:
- En un modelo **AR(p)**, cada muestra depende del ruido actual y de una combinación lineal de las observaciones anteriores.
- La autocorrelación de todos modos depende de todas las muestras que vinieron antes.
- **Si conocemos el *orden* del modelo**, podemos hacer una regresión lineal para explicar $x_t$ a partir de $x_{t-1}$ hasta $x_{t-p}$. De ese modo se obtienen los valores de los coeficientes. 
- Si el proceso ajusta, los residuos deberían ser aproximadamente ruido blanco y parecerse al ruido que dio origen a la serie.

**Pregunta:** ¿Cuál es el orden adecuado?

## Función de autocorrelación parcial (PACF)

**Idea:** Construir una función de correlación que solo contenga los aportes "incrementales" de agregar un lag, y no la correlación completa.

Se explica mejor a través de un ejemplo, dejaremos la teoría para más adelante.
Retomemos la serie:
$$x_t = x_{t-1} - 0.9 x_{t-2} + w_t.$$

Autorregresivo de **orden 2**.

Recordemos la función de autocorrelación (ACF) de la serie:
![[Pasted image 20250527155429.png]]

**Ajustamos $x_t$ a $x_{t-1}$**
```python
#Corto la serie x con la serie x laggeada

data = pd.concat([x, x.shift(1)], axis=1).dropna()
data.columns = ["x", "xL1"]

#Ajusto
fit = ols("x ~ 0+xL1", data=data).fit()
fit.summary() #el coef. hallado es la correlación de xt y xt-1, es decir acf(1)
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>x</td>        <th>  R-squared (uncentered):</th>      <td>   0.269</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared (uncentered):</th> <td>   0.267</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th>          <td>   182.8</td>
</tr>
<tr>
  <th>Date:</th>             <td>Mon, 19 May 2025</td> <th>  Prob (F-statistic):</th>          <td>1.05e-35</td>
</tr>
<tr>
  <th>Time:</th>                 <td>16:18:20</td>     <th>  Log-Likelihood:    </th>          <td> -1120.5</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   499</td>      <th>  AIC:               </th>          <td>   2243.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   498</td>      <th>  BIC:               </th>          <td>   2247.</td>
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
  <th>xL1</th> <td>    0.5182</td> <td>    0.038</td> <td>   13.521</td> <td> 0.000</td> <td>    0.443</td> <td>    0.593</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 0.037</td> <th>  Durbin-Watson:     </th> <td>   1.061</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.982</td> <th>  Jarque-Bera (JB):  </th> <td>   0.018</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.014</td> <th>  Prob(JB):          </th> <td>   0.991</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.996</td> <th>  Cond. No.          </th> <td>    1.00</td>
</tr>
</table><br><br>Notes:<br>[1] R² is computed without centering (uncentered) since the model does not contain a constant.<br>[2] Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
pd.plotting.lag_plot(x,lag=1);
plt.plot(x[0:-1],fit.fittedvalues);
```
![[Pasted image 20250527161103.png]]

```python
#capturo los residuos del ajuste anterior
r1 = fit.resid
r1.plot();
```
![[Pasted image 20250527161116.png]]

- Si el proceso fuera de orden $1$, entonces una vez que conozco $x_{t-1}$, $x_t$ queda determinado a menos del ruido, por lo que el residuo no debería tener información.
- La serie anterior todavía no está del todo "blanca" quiere decir que deberíamos seguir un paso más.
- **Problema:** No alcanza con mirar otro lag, debemos "extraer" la información de $x_{t-2}$ que persiste en $x_t$.
- Para ello, miramos los residuos de los dos ajustes siguientes:
	- Ajusto $x_t$ a $x_{t-1}$, obtengo el residuo $r_1$.
	- Ajusto $x_{t-2}$ a $x_{t-1}$ para extraer la info propia de $x_{t-2}$. Obtengo $r_2$
	- Calculo la correlación entre $r_1$ y $r_2$ (o bien ajusto lineal $r_2$ a $r_1$, es lo mismo). Al resultado se le llama *autocorrelación parcial* entre $x_t$ y $x_{t-2}$ (o de lag 2).

```python
#Realizo el procedimiento anterior
data = pd.concat([x, x.shift(1), x.shift(2)], axis=1).dropna()
data.columns = ["x", "xL1", "xL2"]

#Ajusto r1
fit1 = ols("x ~ 0+xL1", data=data).fit()
r1=fit1.resid
fit1.summary() #el coef. hallado es la correlación de xt y xt-1, es decir acf(1)
#No muestro el summary porque no es relevante
```
```python
#Ajusto r2
fit2 = ols("xL2 ~ 0+xL1", data=data).fit()
r2=fit2.resid
fit2.summary() #el coef. hallado es la correlación de xt y xt-1, es decir acf(1)
# No muestro el summary porque no es relevante
``` 

```python
plt.scatter(r1,r2);
plt.title(f"Correlación remanente: {np.corrcoef(r1,r2)[0,1]}");
```
![[Pasted image 20250527161929.png]]

El método anterior puede repetirse para todos los lags. Simplemente:
- Ajusto $x_t$ a $x_{t-1},...,x_{t-p+1}$, obtengo $r1$
- Ajusto $x_{t-p} a $x_{t-1},\ldots,x_{t-p+1}$, obtengo $r_2$
- Calculo la correlación entre $r_1$ y $r_2$ (o bien ajusto lineal $r_2$ a $r_1$, es lo mismo). Al resultado se le llama *autocorrelación parcial* entre $x_t$ y $x_{t-p}$ (o de lag $p$).

En el ejemplo hagamos un paso más ($p=3$) para ver que ya no queda información
```python
#Realizo el procedimiento anterior
data = pd.concat([x, x.shift(1), x.shift(2), x.shift(3)], axis=1).dropna()
data.columns = ["x", "xL1", "xL2", "xL3"]

#Ajusto r1
fit1 = ols("x ~ 0+xL1+xL2", data=data).fit()
r1=fit1.resid

#Ajusto r2
fit2 = ols("xL3 ~ 0+xL1+xL2", data=data).fit()
r2=fit2.resid

plt.scatter(r1,r2);
plt.title(f"Correlación remanente: {np.corrcoef(r1,r2)[0,1]}");
```
![[Pasted image 20250527162349.png]]

### Función de autocorrelación parcial (PACF)
- A la función así construida recursivamente se le denomina Función de Autocorrelación Parcial
- En el ejemplo anterior, la PACF(1) coincide con la ACF(1). La PACF(2) *NO* (es otra información).
- En el proceso AR(2), la PACF(2) es significativa, la PACF(3) se vuelve 0.
- En general, si un proceso es AR($p$), entonces la PACF se "apaga" luego de $p$.

![[Pasted image 20250527162625.png]]

Apliquemos la misma idea al proceso AR(1) que teníamos antes
![[Pasted image 20250527162657.png]]
```python
plot_acf(x,bartlett_confint=False);
```
![[Pasted image 20250527162710.png]]
```python
plot_pacf(x);
```
![[Pasted image 20250527162723.png]]

## Resumen
- En un proceso autorregresivo puro, el valor de la serie en tiempo $t$ depende de los $p$ anteriores más una innovación o ruido en ese punto.
- Por lo tanto, todas las muestras anteriores tienen influencia en la muestra actual (aunque decae exponencialmente).
- La función de autocorrelación entonces decae a $0$ exponencialmente, pero no se anula.
- La función de autocorrelación *parcial* elimina las dependencias internas y permite estimar el orden de un autorregresivo puro.
- *Una vez que conocemos el orden*, podemos hacer regresión para hallar los coeficientes y el $\sigma_w^2$.

## Predicción a futuro

El siguiente paso es, una vez ajustado un proceso autorregresivo, predecir sus valores a futuro. Idealmente, queremos hallar:
$$
\hat{x}_n+m = g(x_1,...,x_n)
$$
de modo que:
$$
MSE = E[(x_{n+m}-\hat{x}_{n+m})^2]
$$
de minimice.

El estimador ideal es la esperanza condicional:
$$
\hat{x}_{n+m} = E[x_{n+m}|x_1,...,x_n]
$$
En el caso de los procesos lineales ARMA, este estimador se puede calcular mediante el algoritmo de *Durbin-Levinson*. Sin embargo, en el caso del autorregresivo puro es más simple. Una vez que se conocen los parámetros del modelo:
$$x_{t} = \phi_1 x_{t-1} + \phi_2 x_{t-2} + \ldots + \phi_p x_{t-p} + w_t.$$

Entonces la mejor predicción de $x_{p+1}$ es simplemente realizar la combinación lineal de los últimos $p$ valores:

$$\hat{x}_{n+1} = \phi_1 x_{n} + \phi_2 x_{n-1} + \ldots + \phi_p x_{n-p+1}$$
y luego recursivamente se pueden construir las estimaciones de $x_{n+2},x_{n+3},\ldots$ etc.
$$\hat{x}_{n+2} = \phi_1 \hat{x}_{n+1} + \phi_2 x_{n} + \ldots + \phi_p x_{n-p+2}$$

### Ejemplo

Para este ejemplo, simularemos directamente el proceso AR(2) usando una función de Python en lugar de construirlo a partir de una serie de ruido blanco conocida:

```python
##Simulamos un AR(2) usando ahora la función propia de statsmodels
from statsmodels.tsa.arima_process import arma_generate_sample

#primer parámetro: coeficientes autorregresivos. Segundo parámetro: coeficientes de media móvil
#Para el AR puro este segundo parámetro es 1
#El tercer parámetro es la cantidad de muestras.
n=500; #train
m=20; #test
x=arma_generate_sample([1,-1,.9],[1],n+m)

#lo transformo en una serie para poder hacer shift
x=pd.Series(x)
x.plot();
```
![[Pasted image 20250527163711.png]]

Vamos a ajustar los coeficientes con los primeros 500 datos y predecimos los otros 20.

```python
## Recorto la serie
x_train = x.iloc[0:n]
## Chequeo la PACF
plot_pacf(x_train);
```
![[Pasted image 20250527163810.png]]
```python
#Estimo los coeficientes por regresión en el conjunto de train
data = pd.concat([x_train, x_train.shift(1), x_train.shift(2)], axis=1).dropna()
data.columns = ["x", "xL1", "xL2"]

#Ajusto
fit = ols("x ~ 0+xL1+xL2", data=data).fit()
residuo=fit.resid
fit.summary() #el coef. hallado es la correlación de xt y xt-1, es decir acf(1)
# No voy a mostrar el summary otra vez porque no es relevante
```

```python
phi1 = fit.params["xL1"]
phi2 = fit.params["xL2"]

print("Coeficientes estimados:")
print(f"phi1: {phi1}")
print(f"phi2: {phi2}")
```
```
Coeficientes estimados:
phi1: 1.001125252883416
phi2: -0.8950251720111233
```

```python
#Construyo la prediccion recursiva
x_test = x.iloc[n:]
x_hat = np.empty(m)

x_hat[0] = phi1 * x_train[n-1] + phi2*x_train[n-2]
x_hat[1] = phi1 * x_hat[0] + phi2*x_train[n-1]

for i in range(2,m):
    x_hat[i] = phi1 * x_hat[i-1] + phi2*x_hat[i-2]

#lo convierto en una serie
x_hat = pd.Series(x_hat,index=range(n,n+m))
```

```python
plt.plot(x_test, label="Valores de test")
plt.plot(x_hat, label="Prediccion");
plt.legend();
```
![[Pasted image 20250527164114.png]]

**Observaciones:**
- La predicción solo es razonable a pocos intervalos
- A la larga vuelve a la media ya que promedia los valores que va obteniendo.
- Dicho de otro modo, como la influencia de los datos hasta $n$ decae exponencialmente después de $n$, es muy difícil predecir más allá de unos pocos pasos.

### Predicción recursiva
De todos modos, si bien no permite predecir mucho hacia el futuro, podemos ir recursivamente prediciendo el valor de $x_{n+1}$ una vez que conocemos $x_n, x_{n-1},...,x_{n-p+1}$, es decir los últimos $p$.

Luego si recibimos un dato nuevo podemos actualizar la predicción.

#### Ejemplo
```python
##Simulamos un AR(2) usando la función propia de python
x=arma_generate_sample([1,-1,.9],[1],n)
x=pd.Series(x)
x.plot();
```
![[Pasted image 20250527164713.png]]

Supongamos que sabemos exactamente los parámetros, en este caso media $\mu=0$, $\phi_1=1.0$ y $\phi_2=-0.9$.
 * Si solo predecimos por la media, el error MSE es el desvío estándar de la serie.
 * Si predecimos recursivamente $x_{t+1}$ por $\hat{x}_{t+1} = \phi_1 x_t + \phi_2 x_{t-1}$ entonces deberíamos poder reducir el error.

```python
phi1 = 1.0
phi2 = -0.9

#aca va la predicción
x_hat = np.empty(n)

#los valores anteriores a t=0 los asumo 0 (la media)
x_hat[0] = phi1*0 + phi2*0
x_hat[1] = phi1 * x[0] + phi2*0

for i in range(2,n):
    x_hat[i] = phi1 * x[i-1] + phi2*x[i-2]

#lo convierto en una serie
x_hat = pd.Series(x_hat)

x.plot()
x_hat.plot();
```
![[Pasted image 20250527164926.png]]

```python
##Residuo
residuo = x-x_hat
MSE = np.var(residuo)
R2 = (np.var(x)-MSE)/np.var(x)
residuo.plot()
plt.title(f"Residuos de la predicción: MSE = {MSE}, R2 = {R2}");
```
![[Pasted image 20250527164943.png]]
```python
plot_acf(residuo);
```
![[Pasted image 20250527164958.png]]
