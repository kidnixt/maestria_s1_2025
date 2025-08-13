El *modelo linear* es una herramienta básica de estimación estadística, en particular para estudiar fenómenos *causa-efecto* y realizar regresión.

En el contexto de *[[2_Series Temporales|series temporales]]* usaremos modelos lineales (y no lineales) con diferentes objetivos:
- Obtener información de tendencia y estacionalidad de series.
- Ajustar señales de una familia a una observación dada.
- Ajustar valores de una serie a una función de los valores anteriores (ej.: [[7_Modelos autorregresivos y autocorrelación parcial]]).

La idea es repasar entonces cómo se realiza el ajuste de modelos lineales y aplicarlo a diferentes series.

## Regresión lineal en el contexto de series temporales.
Sea ${x_t}, t=1,...,n$ una series temporal. Queremos explicar su comportamiento a partir de variables independientes $z_1,...,z_{t_q}$ donde $q$ es la cantidad de variables explicativas en cada $t$.

**Nota:** En la regresión clásica estas variables son completamente independientes, pero en series temporales luego veremos como relajar esto.

Se tiene entonces el **Modelo de Regresión Lineal:**
$$
x_t = \beta_1 z_{t_1} + ... + \beta_q z_{t_q} + w_t 
$$
- $\beta_1 ... \beta_q$ son los parámetros a ajustar.
- $z_{t_i}$ son las funciones de regresión a usar (a veces llamadas "features"), evaluadas en cada $t$.
- $w_t$ es ruido blanco (gaussiano) de varianza $\sigma_w^2$.

Si mis datos son algo + un ruido blanco **gaussiano** entonces lo mejor que puedo hacer es aproximarlo con una recta haciendo *regresión lineal*. Si el ruido **no** es gaussiano entonces puede haber una mejor solución.

### Ejemplo: estimación de una tendencia lineal
Consideremos los datos de temperatura `gtemp` disponibles en la biblioteca `astsa`. Al parecer estos datos tienen una tendencia creciente. Podemos usar un modelo lineal del tipo:
$$
x_t = \beta_1 + \beta_2 t + w_t
$$
Aqui $z_t = 1$ para todo $t$ y $z_{t_2} = t$. Tenemos dos parámetros ($q = 2$)
```python
gtemp = astsa.gtemp
gtemp.plot(legend=False);
plt.title("Desvios de temperatura respecto al promedio 1940-1970")
plt.ylabel("°C")
plt.xlabel("Año");
```
![[5449cd1355bb56e4b0645fd5a8f7706316d55f4c75f54acd96718ef4aac63258.png]]

```python
import numpy as np
import pandas as pd
import statsmodels.api as sm
from statsmodels.formula.api import ols

time = pd.Series([idx.ordinal for idx in gtemp.index], index=gtemp.index)
data_reg = pd.concat([time, gtemp], axis=1)
data_reg.columns = ["time", "gtemp"]

fit = ols(formula="gtemp ~ time", data=data_reg).fit()
fit.summary()
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>          <td>gtemp</td>      <th>  R-squared:         </th> <td>   0.751</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.749</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   386.3</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sun, 10 Nov 2024</td> <th>  Prob (F-statistic):</th> <td>1.80e-40</td>
</tr>
<tr>
  <th>Time:</th>                 <td>20:45:34</td>     <th>  Log-Likelihood:    </th> <td>  86.718</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   130</td>      <th>  AIC:               </th> <td>  -169.4</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   128</td>      <th>  BIC:               </th> <td>  -163.7</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     1</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
      <td></td>         <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th> <td>    0.1250</td> <td>    0.013</td> <td>    9.417</td> <td> 0.000</td> <td>    0.099</td> <td>    0.151</td>
</tr>
<tr>
  <th>time</th>      <td>    0.0057</td> <td>    0.000</td> <td>   19.653</td> <td> 0.000</td> <td>    0.005</td> <td>    0.006</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 0.646</td> <th>  Durbin-Watson:     </th> <td>   0.768</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.724</td> <th>  Jarque-Bera (JB):  </th> <td>   0.738</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.013</td> <th>  Prob(JB):          </th> <td>   0.691</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.632</td> <th>  Cond. No.          </th> <td>    54.9</td>
</tr>
</table>**Notes:** Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
ax = gtemp.plot(xlabel="Time", ylabel="Temp deviation", legend=False)
ax.plot(fit.fittedvalues)
plt.title("Desvios de temperatura respecto al promedio 1940-1970")
plt.ylabel("°C")
plt.xlabel("Año");
```
![[53c25425d910dad29979be047b205fe07acdd562f2986e3b80cc29787f3e6f89.png]]

**¿Cómo funciona?**
Conviene pasarse a notación vectorial, sean los vectores columna:
$$ z_t = (z_{t_1},\ldots,z_{t_q}), \quad \beta = (\beta_1,\ldots,\beta_q).$$
Entonces el valor en $t$ de la serie puede calcularse como:
$$ x_t = \beta^T z_t + w_t $$
(producto de los vectores $\beta$ y $z_t$ más el ruido)

**Pregunta:** ¿Cómo podemos estimar $\beta$?

### Criterio de mínimos cuadrados
Se trata de minimizar la suma de los errores de predicción al cuadrado sobre todos los datos:
$$
Q = \sum^n_{t=1} (x_t - \beta^T z_t)^2
$$
La minimización se hace en las variables $\beta_i$. El óptimo se denota $\hat{\beta}$, el estimador por mínimos cuadrados del modelo.

**Observación:** la minimización anterior tiene fórmula exacta.

#### Ecuaciones Normales
Derivando la ecuación anterior respecto a cada $\beta_i$ e imponiendo que las derivadas sean $0$ (condición de punto estacionario), se llega al siguiente sistema de ecuaciones para hallar $\beta_i$:
$$
\left( \sum^n_{t=1} \right) \hat{\beta} = \sum^n_{t=1} z_tx_t 
$$
A dicho sistema lineal (de $q$ incógnitas) se le denominan "ecuaciones normales".

#### Versión matricial
Se puede simplificar aún más la notación definiendo la matriz:
$$
Z = [z_1|...|z_n]
$$
y la solución explícita es:
$$
\hat{\beta} = (Z^TZ)^{-1}Z^Tx
$$
Esto vale siempre que la matriz $(Z^TZ)$ sea invertible (en general si no lo es formulamos mal el modelo).

#### Ejemplo: ajuste lineal
Para el caso de un ajuste lineal: $x_t = \beta_1 + \beta_2t + x_t$ la matriz $Z$ en este caso es:
$$
Z = \begin{pmatrix}
1 & t_1 \\
1 & t_2 \\
1 & t_3 \\
\vdots & \vdots \\
1 & t_n
\end{pmatrix}
$$
siendo $t_i$ los tiempos de muestra de la series (ej.: el año en `gtemp`))

#### Error cometido y Mean Square Error
El error cometido en la aproximación es simplemente la suma de los cuadrados:

$$\begin{align}
SSE&= \sum_{t=1}^n (x_t - \hat{\beta}^T z_t)^2 = (x-Z\hat{\beta})^T (x-Z\hat{\beta}) = x^Tx - \hat{\beta}^TZ^T x\\
&= x^Tx - x^TZ(Z^TZ)^{-1}Z^T x
\end{align}$$

El estimador $\hat{\beta}$ de mínimos cuadrados es insesgado, y si los errores son Gaussianos, es el estimador de máxima verosimilitud. En dicho caso podemos calcular la distribución esperada del estimador, la cual resulta una gaussiana de matriz de covarianzas:
$$Cov(\hat{\beta}) = \sigma^2_w (Z^TZ)^{-1} = \sigma^2_w C$$

El **Error Cuadrático Medio (MSE)** es simplemente:
$$s^2_w = MSE = \frac{SSE}{n-q}$$
y es un estimador insesgado para $\sigma_w^2$, la varianza del error.

### ¿Cómo podemos ver si el ajuste fue bueno?
1. Plotear los residuos. Deberían tener un comportamiento homogéneo.
2. Mirar si los residuos son gaussianos (qqNorm). Esto no es necesario pero si lo son nos ajustamos al modelo anterior perfectamente.
3. Mirar si los residuos son "ruido blanco". Esta es una regla general: si luego de aplicar un modelo lo que queda es "ruido" es que logramos extraer toda la información.
4. Mirar si los mejoramos el ajuste respecto a algo simple (por ejemplo, la media) $\to$ Criterio $R^2$
5. Observar que no hubo sobreajuste (overfitting). Esto puede pasar si tengo demasiados parámetros. $\to$ Criterio de información ($AIC$, $BIC$).

Si los residuos no son ruido blanco entonces mi hipótesis es errónea (no es una recta) o todavía queda información para sacar.

```python
residuos = fit.resid
residuos.plot();
plt.title("Residuos del ajuste");
```
![[Pasted image 20250416211851.png]]
```python
## QQ-plot es una verificación de gaussianidad.
sm.qqplot(residuos, line="s");
```
![[Pasted image 20250416211909.png]]
#### Nota:
Para calcular la acf, conviene utilizar `statsmodels.tsa.graphics.plot_acf()` con la opción `bartlett_confint=False` para usar el intervalo de confianza correcto

```python
from statsmodels.graphics.tsaplots import plot_acf
plot_acf(residuos, bartlett_confint=False);
```

### Evaluación:
Deseamos evaluar si mejoramos el ajuste respecto a algo simple. El modelo lineal más simple es:
$$x_t = \beta_1 + w_t$$

Es decir, media más ruido. En este caso, la solución es $\hat{\beta}_1 = \bar{x}$, la media de los datos, y el error cuadrático $SSE_1$ es:
$$SSE_1 = \sum_{t=1}^n (x_t - \bar{x})^2$$
Definimos:
$$R^2 = \frac{SSE_1 - SSE}{SSE_1}$$
Entonces $R^2$ es una medida de correlación de nuestras variables $z$ con $x$, o bien cuánto mejora el ajuste en términos relativos respecto a la media.

```python
SSE1 = np.sum((gtemp.values-np.mean(gtemp.values))**2, axis=0)
SSE = np.sum(fit.resid**2)
MSE = SSE/(gtemp.size - 2)
RMSE = np.sqrt(MSE)
R2 = (SSE1-SSE)/SSE1

#muestro los valores
print(f"SSE1: {SSE1}")
print(f"SSE: {SSE}")
print(f"MSE: {MSE}")
print(f"RMSE: {RMSE}")
print(f"R2: {R2}")
```

```
SSE1: [8.05436077] 
SSE: 2.004767366480493 
MSE: 0.01566224505062885 
RMSE: 0.12514889152776723 
R2: [0.75109541]
```

**Comparemos con el resumen del ajuste anterior:**
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>          <td>gtemp</td>      <th>  R-squared:         </th> <td>   0.751</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.749</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   386.3</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sun, 10 Nov 2024</td> <th>  Prob (F-statistic):</th> <td>1.80e-40</td>
</tr>
<tr>
  <th>Time:</th>                 <td>20:45:34</td>     <th>  Log-Likelihood:    </th> <td>  86.718</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   130</td>      <th>  AIC:               </th> <td>  -169.4</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   128</td>      <th>  BIC:               </th> <td>  -163.7</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     1</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
      <td></td>         <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th> <td>    0.1250</td> <td>    0.013</td> <td>    9.417</td> <td> 0.000</td> <td>    0.099</td> <td>    0.151</td>
</tr>
<tr>
  <th>time</th>      <td>    0.0057</td> <td>    0.000</td> <td>   19.653</td> <td> 0.000</td> <td>    0.005</td> <td>    0.006</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 0.646</td> <th>  Durbin-Watson:     </th> <td>   0.768</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.724</td> <th>  Jarque-Bera (JB):  </th> <td>   0.738</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.013</td> <th>  Prob(JB):          </th> <td>   0.691</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.632</td> <th>  Cond. No.          </th> <td>    54.9</td>
</tr>
</table>**Notes:**
\[1\] Standard Errors assume that the covariance matrix of the errors is correctly specified.

## Modelos lineales más complejos
Observemos que el modelo lineal general:
$$x_t = \beta_1z_{t_1} + \ldots + \beta_qz_{t_q} + w_t,$$
nada dice que la aproximación tenga que ser por una recta. De hecho es bastante general. Eligiendo los $z_{t_i}$ podemos hacer ajustes más complicados. La razón por la que se llama *lineal* es que los pesos $\beta_i$ entran linealmente en la ecuación, y admiten entonces resolverse por ecuaciones normales.

### Ejemplo: ajustar una tendencia cuadrática a las temperaturas.
Es simplemente considerar:
$$x_t = \beta_1 + \beta_2 t + \beta_3 t^2 + w_t$$

Aquí $z_{t_1} = 1$ para todo $t$, $z_{t_2} = t$ y $z_{t_3} = t^2$. Tenemos tres parámetros ($q=3$)

```python
time2 = time**2

data_reg = pd.concat([time, time2, gtemp], axis=1)
data_reg.columns = ["time", "time2", "gtemp"]

fit2 = ols(formula="gtemp ~ time + time2", data=data_reg).fit()
fit2.summary()
```

<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>          <td>gtemp</td>      <th>  R-squared:         </th> <td>   0.807</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.803</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   264.7</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sun, 10 Nov 2024</td> <th>  Prob (F-statistic):</th> <td>5.02e-46</td>
</tr>
<tr>
  <th>Time:</th>                 <td>20:45:34</td>     <th>  Log-Likelihood:    </th> <td>  103.09</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   130</td>      <th>  AIC:               </th> <td>  -200.2</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   127</td>      <th>  BIC:               </th> <td>  -191.6</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     2</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
      <td></td>         <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th> <td>    0.0897</td> <td>    0.013</td> <td>    6.837</td> <td> 0.000</td> <td>    0.064</td> <td>    0.116</td>
</tr>
<tr>
  <th>time</th>      <td>    0.0081</td> <td>    0.000</td> <td>   17.245</td> <td> 0.000</td> <td>    0.007</td> <td>    0.009</td>
</tr>
<tr>
  <th>time2</th>     <td> 4.653e-05</td> <td> 7.71e-06</td> <td>    6.032</td> <td> 0.000</td> <td> 3.13e-05</td> <td> 6.18e-05</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 0.679</td> <th>  Durbin-Watson:     </th> <td>   0.987</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.712</td> <th>  Jarque-Bera (JB):  </th> <td>   0.765</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.028</td> <th>  Prob(JB):          </th> <td>   0.682</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.629</td> <th>  Cond. No.          </th> <td>4.16e+03</td>
</tr>
</table>**Notes:**
\[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
\[2] The condition number is large, 4.16e+03. This might indicate that there are strong multicollinearity or other numerical problems.

```python
res2 = fit2.resid
res2.plot();
plt.title("Residuos del ajuste");
```
![[Pasted image 20250416212407.png]]

```python
## QQ-plot es una verificación de gaussianidad.
sm.qqplot(res2, line="s");
```
![[Pasted image 20250416212420.png]]

```python
plot_acf(res2, bartlett_confint=False);
```
![[Pasted image 20250416212433.png]]

```python
SSE1 = np.sum((gtemp.values-np.mean(gtemp.values))**2, axis=0)
SSE = np.sum(res2**2)
MSE = SSE/(gtemp.size - 3)
RMSE = np.sqrt(MSE)
R2 = (SSE1-SSE)/SSE1

#muestro los valores
print(f"SSE1: {SSE1}")
print(f"SSE: {SSE}")
print(f"MSE: {MSE}")
print(f"RMSE: {RMSE}")
print(f"R2: {R2}")
```

```
SSE1: [8.05436077]
SSE: 1.5583233958153064
MSE: 0.01227026295917564
RMSE: 0.1107712190019395
R2: [0.80652426]
```

## Akaike information criterion
Es una herramienta para selección de modelos. La idea es ponderar en parte la *verosimilitud del modelo* (calidad del ajuste) con la cantidad de parámetros involucrados (para prevenir el overfitting).

> **Definición (Akaike Information Criterion):** Se define el AIC de un ajuste como:$$AIC = -2\log \hat{L}_q + 2q.$$siendo $\hat{L}_q$ la log-verosimilitud. Cuanto más pequeño el AIC mejor.

En el caso de ajuste lineal con residuos gaussianos, la (log-)verosimilitud depende de la varianza de los residuos, es decir:
$$\hat{\sigma}_q^2 = \frac{SSE_q}{n},$$
siendo $q$ la cantidad de parámetros y $n$ la cantidad de muestras de la serie, como veremos a continuación.

### Verosimilitud del modelo
La hipótesis del modelo lineal es que los errores respecto al modelo son Gaussianas independientes de varianza $\sigma_w^2$. La verosimilitud se define entonces como:

$$L_q = \prod_{i=1}^n \frac{1}{\sqrt{2\pi}\sigma_w} e^{-r_t^2/(2\sigma_w^2)}$$
es decir, la densidad conjunta de los errores evaluada en los valores observados. A mayor densidad conjunta, más "chance" de que el modelo observado sea correcto (más verosimilitud).

En general conviene trabajar con la log-verosimilitud:
$$\log L_q = \sum_{t=1}^n \log\left[\frac{1}{\sqrt{2\pi}\sigma_w}\right] - \frac{r_t^2}{2\sigma_w^2} = -\frac{n}{2}\log(2\pi \sigma_w^2) - \sum_{t=1}^n \frac{r_t^2}{2\sigma_w^2}$$

Sustituyendo $\sigma_w^2$ por el valor estimado $\hat{\sigma}_w = SSE^2_q/n$ se tiene el estimador de la log-verosimilitud $\log \hat{L}_q$.

### Ejemplo:
Calculemos el AIC del ajuste lineal guardado en la variable `fit`:
```python
n=gtemp.size
sigmaw2 = sum(res2**2)/n

logver = -n*np.log(np.sqrt(2*np.pi*sigmaw2)) - sum(res2**2/(2*sigmaw2))

logver
```

```output
np.float64(103.09304765571144)
```

De aquí se deduce el AIC:
```python
q=3 #parámetros estimados: intercept + trend + varianza del ruido
AIC = -2*logver + 2*q

AIC
```

```output
np.float64(-200.1860953114229)
```

## Bayesian information criterion
Es una herramienta análoga a la anterior, que penaliza distinto los parámetros involucrados.

> **Definición (Bayesian Information Criterion):** Se define el BIC de un ajuste como: $$BIC = -2\log \hat{L}_q + q\log(n)$$

Cuanto más pequeño el BIC mejor. Funciona mejor que el AIC en muestras grandes.

```python
q=3 #parámetros estimados: intercept + trend + varianza del ruido
BIC = -2*logver + q*np.log(n)
BIC
```

```output
np.float64(-191.58349196005614)
```

### Comparación de ajustes
```python
print(f"AIC:  Modelo lineal: {fit.aic} | Modelo cuadrático: {fit2.aic}")
print(f"AIC:  Modelo lineal: {fit.bic} | Modelo cuadrático: {fit2.bic}")
```

```output
AIC:  Modelo lineal: -169.43681637260454 | Modelo cuadrático: -200.18609531142283
AIC:  Modelo lineal: -163.70174747169338 | Modelo cuadrático: -191.58349196005608
```

Vemos que el $AIC$ y el $BIC$ mejoran levemente en el ajuste cuadrático.

## Ejercicio de notebook: Medidas de dependencia
Ejercicio resuelto en clase: **Tendencia lineal más ruido**

Consideremos la serie temporal dada por:
$$x_t = \beta_0 + \beta_1 t + w_t,$$
con $\beta_0$ y $\beta_1$ coeficientes y $w_t$ es ruido blanco gaussiano de varianza $\sigma_w^2$.
1. Simule y grafique $n=100$ valores de esta serie, tomando $\beta_0=50$, $\beta_1=0.2$, $\sigma^2_w =9$, $t=(1,\ldots,100)$.
2. ¿La serie es estacionaria?
3. ¿Cómo podría estimar $\beta_0$ y $\beta_1$ a partir de las observaciones?
4. Mostrar que el proceso $y_t = x_t − x_{t−1}$ es estacionario. ¿Qué media tiene? Utilice el comando `diff` para calcular $y_t$ y graficarlo. Estimar su autocorrelación.

**Solución:**
1. TODO poner el código
2. La serie no es estacionaria ya que va aumentando con el tiempo
3. Se pueden proponer dos formas de estimarla:

**Aplicar [[#Regresión lineal en el contexto de series temporales.|regresión lineal]]**
- Hallar $\hat{\beta_0}$ y $\hat{\beta_1}$ tales que se minimize el [[#Error cometido y Mean Square Error|MSE]].
- Si tengo una buena estimación de $\hat{\beta_0}$ y $\hat{\beta_1}$: $$\Rightarrow \hat{w_t} = x_t + (\hat{\beta_0} + \hat{\beta_1} t) \approx w_t$$El residuo de mi operación debería darme el ruido. En ese caso puedo usar la varianza $\sigma^2_w$ para estimar el error que cometo en mi estimación.

**Aplicar diferencias**
$$
\begin{align}
y_t &= x_t - x_{t-1} \\
y_t &= \beta_0 + \beta_1 t + w_t - \beta_0 - \beta_1(t-1) - w_{t-1}\\
y_t &= \beta_1 + \underbrace{w_t - w_{t-1}}_{v_t} \\ 
y_t &= \beta_1 + v_t \rightarrow E[y_t] = \beta_1 + \overbrace{E[v_t]}^{=\ 0} = \beta_1 \\
\end{align}$$

Lo interesante aca es aplicar las dos formas y ver cuál es mejor, esto se va a definir porque el residuo de una debería dar ruido blanco y la otra no.

En este ejercicio nos dio que la regresión lineal es mejor, lo cuál tiene sentido porque la premisa que teníamos era $x_t = \beta_0 + \beta_1 t + w_t$, es decir que nuestra serie temporal era una recta + ruido blanco gaussiano.
Para observar esto podemos estimar [[3.2_Estimación de la correlación a partir de muestras |la correlación]].

**Nota:** La parte cuatro esta relacionada a aplicar diferencias, pero las preguntas se hacen no se respondieron en la clase.