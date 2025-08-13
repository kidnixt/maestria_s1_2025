Nos interesa estudiar:
- El uso de [[4_Modelos lineales|modelos lineales]] para hallar relaciones *entre series* que permitan explicar una a partir de la otra.
- Cómo ajustar componentes periódicas o estacionales. En particular cómo determinar sus frecuencias.

## Ejemplo: relación entre mortalidad, temperatura y polución

El ejemplo tomado del [[Time Series A Data Analysis Approach Using R by Robert Shumway, David Stoffe.pdf|libro]], consiste en tres series:
- $M_t$ = mortalidad cardiovascular semanal reportada en Los Angeles durante 10 años.
- $T_t$ = temperatura media registrada en Fahrenheit
- $P_t$ = conteo de niveles de partículas en el aire.

Graficas de las series:
```python
M=astsa.cmort
T=astsa.tempr
P=astsa.part

fig, axs = plt.subplots(3, 1, figsize=[10,6])
M.plot(ax=axs[0], title="Mortalidad Cardiovascular", legend=False)
T.plot(ax=axs[1], title="Temperatura", color="teal", legend=False);
P.plot(ax=axs[2], xlabel="Año", title="Partículas", color="firebrick", legend=False);
```
![[eee5ced99e154721a7822014d895bf9dfd6f562f9af96f0b2a1a975ec3f5c00a.png]]

**Observaciones:**
- Se ve una tendencia decreciente de mortalidad.
- Se ven ciclos estacionales claros (anuales).
- Mortalidad y temperatura parecen estar "en contrafase" $\rightarrow$ ¿la gente muere más en invierno?
- Temperatura y partículas parece estar "en contrafase" $\rightarrow$ ¿la lluvia en invierno limpia?

Para analizar estas relaciones se puede hacer un gráfico de correlaciones, con el comando `scatter_matrix` de `pandas.plotting`.

```python
import pandas as pd
import numpy as np
import scipy as sp

data = pd.concat([M, T, P], axis=1)
data.columns = ["Mortalidad", "Temperatura", "Particulas"]

pd.plotting.scatter_matrix(data, figsize=(9, 6), diagonal="kde") #kde pone estimaciones de la densidad en la diagonal
plt.show()
```
![[bc29838ccc5af47606cd9e061c5ab94b46eb572203b2dcd72079725d4d4cd606.png]]

Se propone entonces ajustar algunos modelos que buscan explicar la seria de mortalidad *a partir de las otras*:
1. $M_t = \beta_0 + \beta_1t+w_t$
2. $M_t = \beta_0 + \beta_1t+ \beta_2(T_t - \overline{T}) + w_t$
3. $M_t = \beta_0 + \beta_1t+ \beta_2(T_t - \overline{T}) + \beta_3(T_t - \overline{T})^2 + w_t$
4. $M_t = \beta_0 + \beta_1t+ \beta_2(T_t - \overline{T}) + \beta_3(T_t - \overline{T})^2 + \beta_4P_t +  w_t$

### Modelo 1
```python
import statsmodels.api as sm #para el qqplot
from statsmodels.formula.api import ols

time = pd.Series([idx.ordinal for idx in M.index], index=M.index, name="Semana")
datos = pd.concat([time,M], axis=1)
datos.columns = ["Semana","Mortalidad"]

fit = ols(formula="M~Semana", data=datos).fit()
fit.summary()
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>M</td>        <th>  R-squared:         </th> <td>   0.211</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.209</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   135.0</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sun, 10 Nov 2024</td> <th>  Prob (F-statistic):</th> <td>8.03e-28</td>
</tr>
<tr>
  <th>Time:</th>                 <td>20:45:47</td>     <th>  Log-Likelihood:    </th> <td> -1829.9</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   508</td>      <th>  AIC:               </th> <td>   3664.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   506</td>      <th>  BIC:               </th> <td>   3672.</td>
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
  <th>Intercept</th> <td>   96.6510</td> <td>    0.790</td> <td>  122.335</td> <td> 0.000</td> <td>   95.099</td> <td>   98.203</td>
</tr>
<tr>
  <th>Semana</th>    <td>   -0.0312</td> <td>    0.003</td> <td>  -11.618</td> <td> 0.000</td> <td>   -0.036</td> <td>   -0.026</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>67.579</td> <th>  Durbin-Watson:     </th> <td>   0.576</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  97.699</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.906</td> <th>  Prob(JB):          </th> <td>6.09e-22</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 4.156</td> <th>  Cond. No.          </th> <td>    590.</td>
</tr>
</table><br><br>Notes:<br>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
ax = M.plot(xlabel="Año", ylabel="Mortalidad", legend=False)
ax.plot(fit.fittedvalues)
plt.title("Modelo 1")
plt.ylabel("Mortalidad")
plt.xlabel("Año");
```
![[58316d1bd48c50b646c22cd51b9178a119d7572e266aa209b12a44cdca98f9bb.png]]
```python
fit.resid.plot()
plt.title("Residuos modelo 1")
plt.ylabel("Residuos")
plt.xlabel("Año");
```

### Modelo 2
```python
#  Modelo 2
temp  = T-np.mean(T)           # temperatura centrada

datos = pd.concat([time,M, temp], axis=1)
datos.columns = ["Semana","Mortalidad", "Temperatura"]

fit = ols(formula="M~Semana+Temperatura", data=datos).fit()
fit.summary()
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>M</td>        <th>  R-squared:         </th> <td>   0.380</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.378</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   155.0</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sun, 10 Nov 2024</td> <th>  Prob (F-statistic):</th> <td>3.28e-53</td>
</tr>
<tr>
  <th>Time:</th>                 <td>20:45:48</td>     <th>  Log-Likelihood:    </th> <td> -1768.4</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   508</td>      <th>  AIC:               </th> <td>   3543.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   505</td>      <th>  BIC:               </th> <td>   3555.</td>
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
       <td></td>          <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>   <td>   96.2251</td> <td>    0.702</td> <td>  137.154</td> <td> 0.000</td> <td>   94.847</td> <td>   97.604</td>
</tr>
<tr>
  <th>Semana</th>      <td>   -0.0295</td> <td>    0.002</td> <td>  -12.377</td> <td> 0.000</td> <td>   -0.034</td> <td>   -0.025</td>
</tr>
<tr>
  <th>Temperatura</th> <td>   -0.4579</td> <td>    0.039</td> <td>  -11.763</td> <td> 0.000</td> <td>   -0.534</td> <td>   -0.381</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>58.204</td> <th>  Durbin-Watson:     </th> <td>   1.214</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  80.792</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.818</td> <th>  Prob(JB):          </th> <td>2.86e-18</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 4.068</td> <th>  Cond. No.          </th> <td>    591.</td>
</tr>
</table><br>Notes:<br>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
ax = M.plot(xlabel="Año", ylabel="Mortalidad", legend=False)
ax.plot(fit.fittedvalues)
plt.title("Modelo 2")
plt.ylabel("Mortalidad")
plt.xlabel("Año");
```
![[Pasted image 20250515171034.png]]

```python
fit.resid.plot()
plt.title("Residuos modelo 2")
plt.ylabel("Residuos")
plt.xlabel("Año");
```
![[Pasted image 20250515171051.png]]

### Modelo 3
```python
temp2 = temp**2 #cuadrados

datos = pd.concat([time, M, temp, temp2], axis=1)
datos.columns = ["Semana", "Mortalidad", "Temperatura", "Temperatura2"]

fit = ols(formula="Mortalidad~Semana+Temperatura+Temperatura2",
data=datos).fit()
fit.summary()
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>       <td>Mortalidad</td>    <th>  R-squared:         </th> <td>   0.448</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.445</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   136.3</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sun, 10 Nov 2024</td> <th>  Prob (F-statistic):</th> <td>1.14e-64</td>
</tr>
<tr>
  <th>Time:</th>                 <td>20:45:48</td>     <th>  Log-Likelihood:    </th> <td> -1739.1</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   508</td>      <th>  AIC:               </th> <td>   3486.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   504</td>      <th>  BIC:               </th> <td>   3503.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     3</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
        <td></td>          <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>    <td>   93.9179</td> <td>    0.725</td> <td>  129.543</td> <td> 0.000</td> <td>   92.493</td> <td>   95.342</td>
</tr>
<tr>
  <th>Semana</th>       <td>   -0.0286</td> <td>    0.002</td> <td>  -12.714</td> <td> 0.000</td> <td>   -0.033</td> <td>   -0.024</td>
</tr>
<tr>
  <th>Temperatura</th>  <td>   -0.4808</td> <td>    0.037</td> <td>  -13.032</td> <td> 0.000</td> <td>   -0.553</td> <td>   -0.408</td>
</tr>
<tr>
  <th>Temperatura2</th> <td>    0.0258</td> <td>    0.003</td> <td>    7.857</td> <td> 0.000</td> <td>    0.019</td> <td>    0.032</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>64.188</td> <th>  Durbin-Watson:     </th> <td>   1.326</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  99.520</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.826</td> <th>  Prob(JB):          </th> <td>2.45e-22</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 4.405</td> <th>  Cond. No.          </th> <td>    666.</td>
</tr>
</table><br>Notes:<br>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
ax = M.plot(xlabel="Año", ylabel="Mortalidad", legend=False)
ax.plot(fit.fittedvalues)
plt.title("Modelo 3")
plt.ylabel("Mortalidad")
plt.xlabel("Año");
```
![[Pasted image 20250515171344.png]]

```python
fit.resid.plot()
plt.title("Residuos modelo 3")
plt.ylabel("Residuos")
plt.xlabel("Año");
```
![[Pasted image 20250515171401.png]]

### Modelo 4
```python
datos = pd.concat([time,M, temp, temp2, P-np.mean(P)], axis=1)
datos.columns = ["Semana","Mortalidad", "Temperatura", "Temperatura2", "Particulas"]

fit = ols(formula="Mortalidad~Semana+Temperatura+Temperatura2+Particulas", data=datos).fit()
fit.summary()
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>       <td>Mortalidad</td>    <th>  R-squared:         </th> <td>   0.595</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.592</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   185.1</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sun, 10 Nov 2024</td> <th>  Prob (F-statistic):</th> <td>2.20e-97</td>
</tr>
<tr>
  <th>Time:</th>                 <td>20:45:49</td>     <th>  Log-Likelihood:    </th> <td> -1660.1</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   508</td>      <th>  AIC:               </th> <td>   3330.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   503</td>      <th>  BIC:               </th> <td>   3351.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     4</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
        <td></td>          <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>    <td>   93.6979</td> <td>    0.622</td> <td>  150.760</td> <td> 0.000</td> <td>   92.477</td> <td>   94.919</td>
</tr>
<tr>
  <th>Semana</th>       <td>   -0.0268</td> <td>    0.002</td> <td>  -13.821</td> <td> 0.000</td> <td>   -0.031</td> <td>   -0.023</td>
</tr>
<tr>
  <th>Temperatura</th>  <td>   -0.4725</td> <td>    0.032</td> <td>  -14.941</td> <td> 0.000</td> <td>   -0.535</td> <td>   -0.410</td>
</tr>
<tr>
  <th>Temperatura2</th> <td>    0.0226</td> <td>    0.003</td> <td>    7.989</td> <td> 0.000</td> <td>    0.017</td> <td>    0.028</td>
</tr>
<tr>
  <th>Particulas</th>   <td>    0.2553</td> <td>    0.019</td> <td>   13.539</td> <td> 0.000</td> <td>    0.218</td> <td>    0.292</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>47.363</td> <th>  Durbin-Watson:     </th> <td>   1.311</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  69.807</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.661</td> <th>  Prob(JB):          </th> <td>6.94e-16</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 4.245</td> <th>  Cond. No.          </th> <td>    666.</td>
</tr>
</table><br>Notes:<br>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
ax = M.plot(xlabel="Año", ylabel="Mortalidad", legend=False)
ax.plot(fit.fittedvalues)
plt.title("Modelo 4")
plt.ylabel("Mortalidad")
plt.xlabel("Año");
```
![[Pasted image 20250515171505.png]]

```python
fit.resid.plot()
plt.title("Residuos modelo 4")
plt.ylabel("Residuos")
plt.xlabel("Año");
```
![[Pasted image 20250515171522.png]]

```python
## QQ-plot es una verificación de gaussianidad.
sm.qqplot(fit.resid, line="s");
```
![[Pasted image 20250515171535.png]]

> **Nota:**
> Para calcular la acf, conviene utilizar `statsmodels.tsa.graphics.plot_acf()` con la opción `bartlett_confint=False` para usar el intervalo de confianza correcto

```python
from statsmodels.graphics.tsaplots import plot_acf
plot_acf(fit.resid, bartlett_confint=False, lags=55);
```
![[Pasted image 20250515171602.png]]

Agreguemos ahora un modelo donde usamos también la variable $P_t^2$ además de $P_t$, para ver cómo el modelo comienza a empeorar en términos del $AIC/BIC$.

```python
#  Modelo 5 (para ver que el AIC/BIC empeora)

P2 = P**2                    # particulas al cuadrado

datos = pd.concat([time,M, temp, temp2, P, P2], axis=1)
datos.columns = ["Semana","Mortalidad", "Temperatura", "Temperatura2", "Particulas", "Particulas2"]

fit = ols(formula="M~Semana+Temperatura+Temperatura2+Particulas+Particulas2",data=datos).fit()
fit.summary()
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>M</td>        <th>  R-squared:         </th> <td>   0.596</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.592</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   148.2</td>
</tr>
<tr>
  <th>Date:</th>             <td>Sun, 10 Nov 2024</td> <th>  Prob (F-statistic):</th> <td>2.06e-96</td>
</tr>
<tr>
  <th>Time:</th>                 <td>20:45:49</td>     <th>  Log-Likelihood:    </th> <td> -1659.7</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   508</td>      <th>  AIC:               </th> <td>   3331.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   502</td>      <th>  BIC:               </th> <td>   3357.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     5</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
        <td></td>          <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>    <td>   78.9871</td> <td>    2.987</td> <td>   26.445</td> <td> 0.000</td> <td>   73.119</td> <td>   84.855</td>
</tr>
<tr>
  <th>Semana</th>       <td>   -0.0267</td> <td>    0.002</td> <td>  -13.801</td> <td> 0.000</td> <td>   -0.031</td> <td>   -0.023</td>
</tr>
<tr>
  <th>Temperatura</th>  <td>   -0.4756</td> <td>    0.032</td> <td>  -14.955</td> <td> 0.000</td> <td>   -0.538</td> <td>   -0.413</td>
</tr>
<tr>
  <th>Temperatura2</th> <td>    0.0221</td> <td>    0.003</td> <td>    7.726</td> <td> 0.000</td> <td>    0.017</td> <td>    0.028</td>
</tr>
<tr>
  <th>Particulas</th>   <td>    0.3672</td> <td>    0.121</td> <td>    3.042</td> <td> 0.002</td> <td>    0.130</td> <td>    0.604</td>
</tr>
<tr>
  <th>Particulas2</th>  <td>   -0.0011</td> <td>    0.001</td> <td>   -0.939</td> <td> 0.348</td> <td>   -0.003</td> <td>    0.001</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>46.388</td> <th>  Durbin-Watson:     </th> <td>   1.309</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>  68.681</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 0.648</td> <th>  Prob(JB):          </th> <td>1.22e-15</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 4.251</td> <th>  Cond. No.          </th> <td>3.11e+04</td>
</tr>
</table><br>Notes:<br>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br>[2] The condition number is large, 3.11e+04. This might indicate that there are<br>strong multicollinearity or other numerical problems.

Podemos ver que el coeficiente para $P_t^2$ nos dio no significativo $P>|t| = 0.348$ 
## Ejemplo: Regresión con "lag"

Series: Southern Oscillation Index - SOI (Índice del niño) y Recruitment (Población de peces).

```python
soi = astsa.soi
rec = astsa.rec

fig, axs = plt.subplots(2, 1, constrained_layout = True)

soi.plot(ax=axs[0], xlabel="Year", title="Southern Oscillation Index")
rec.plot(ax=axs[1], xlabel="Year", title="Recruitement index", color="teal");
```
![[Pasted image 20250515172410.png]]

Analicemos la autocorrelación de cada serie, y la correlación cruzada:
```python
plot_acf(soi, bartlett_confint=False);
plt.title("Southern oscillation index");
```
![[Pasted image 20250515172452.png]]

```python
plot_acf(rec, bartlett_confint=False)
plt.title("Recruitment");
```
![[Pasted image 20250515172945.png]]
### Cálculo de correlación cruzada:
Esta función grafica la correlación cruzada de manera adecuada, considerando lags positivos y negativos.

```python
def ccf(x, y, max_lag, ax=None, **kwargs):
    lags = np.arange(-max_lag, max_lag + 1)

	backwards = sm.tsa.ccf(x[::-1], y[::-1], adjusted=False)[max_lag::-1]
    forwards = sm.tsa.ccf(x, y, adjusted=False)[:max_lag + 1]
    ccf = np.r_[backwards[:-1], forwards]
    ylabel = "CCF"

    if ax is None:
        ax = plt.gca()
    ax.stem(lags, ccf, basefmt="k")
    ax.axvline(0, color="black", linestyle="--", linewidth=1)
    conf_level = 1.96 / np.sqrt(x.shape[0])
    ax.fill_between(lags,conf_level,-conf_level,alpha=0.25)
    ax.set_xlabel("LAG")
    ax.set_ylabel(ylabel)

    return ax
```

```python
ccf(soi,rec, max_lag=30)
plt.title("SOI vs Recruitment");
```
![[Pasted image 20250515172627.png]]

Lo que observamos en la gráfica es que moviendo la serie SOI, a la izquierda tenemos lags negativos y a la derecha lags positivos. Como nos interesa utilizar SOI para explicar Recruitment, miramos solo el lado izquierdo de la gráfica.

**Observaciones:**
- La serie del SOI muestra correlaciones fuerte cada 12 meses.
- El libro dice que la de recruitment también pero no es evidente (más adelante lo veremos)
- La correlación cruzada tiene un pico (negativo) en -6 meses. Esto indica que la serie SOI puede predecir la serie Recruitment usando el valor de 6 meses antes.

Se propone entonces un modelo de la forma:
$$
R_t = \beta_0 + \beta_1S_{t-6} + w_t
$$
Es decir, estimar el recruitment actual por una media más algo que depende del SOI 6 meses antes. Observemos que esto es un modelo lineal como antes, donde simplemente figuran como "features" el intercept u ordenada de origen, y los valores de SOI corridos.

> **Problema:** ¿Cómo logramos alinear la serie $R_t$ en tiempo $t$ y la serie $S_{t-6}$ laggeada?
> Usamos el comando `shift` para correr la serie

```python
lag=6
plt.scatter(soi.shift(lag), rec, label="datos");
#Agrego un suavizado local para ver la tendencia
lowess = sm.nonparametric.lowess(rec.iloc[:, 0], soi.iloc[:, 0].shift(lag))
plt.plot(lowess[:, 0], lowess[:, 1], color="firebrick", label="loess");
plt.legend()
plt.xlabel("SOI(t-6)")
plt.ylabel("Rec(t)");
```
![[Pasted image 20250515173336.png]]

**Nota:** la curva roja es una regresión "losses" simple para ver aproximadamente la correlación.

```python
#Podemos llamar a fit haciendo referencia a las columnas del dataframe (fish en este caso).
fish = pd.concat([rec, soi.shift(6)], axis=1).dropna()
fish.columns = ["rec", "soiL6"]

fit = ols("rec ~ soiL6", data=fish).fit()
fit.summary()
```
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>           <td>rec</td>       <th>  R-squared:         </th> <td>   0.363</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.362</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   253.5</td>
</tr>
<tr>
  <th>Date:</th>             <td>Tue, 22 Apr 2025</td> <th>  Prob (F-statistic):</th> <td>1.68e-45</td>
</tr>
<tr>
  <th>Time:</th>                 <td>15:38:05</td>     <th>  Log-Likelihood:    </th> <td> -2024.9</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   447</td>      <th>  AIC:               </th> <td>   4054.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   445</td>      <th>  BIC:               </th> <td>   4062.</td>
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
  <th>Intercept</th> <td>   65.7898</td> <td>    1.088</td> <td>   60.469</td> <td> 0.000</td> <td>   63.652</td> <td>   67.928</td>
</tr>
<tr>
  <th>soiL6</th>     <td>  -44.2826</td> <td>    2.781</td> <td>  -15.923</td> <td> 0.000</td> <td>  -49.748</td> <td>  -38.817</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>16.029</td> <th>  Durbin-Watson:     </th> <td>   0.547</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td> <th>  Jarque-Bera (JB):  </th> <td>   8.017</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.088</td> <th>  Prob(JB):          </th> <td>  0.0182</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.368</td> <th>  Cond. No.          </th> <td>    2.63</td>
</tr>
</table><br>Notes:<br>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
plt.plot(fish.soiL6,fit.fittedvalues, color="firebrick")
plt.scatter(soi.shift(lag), rec);
```
![[Pasted image 20250515173443.png]]

```python
rec.plot()
fit.fittedvalues.plot().legend(["Recruitment","Ajuste"]);
```
![[Pasted image 20250515173455.png]]

### Análisis de residuos del ajuste
```python
fit.resid.plot();
plt.title("Residuos del ajuste");
print(f"RMSE del residuo: {np.std(fit.resid)}")
```
![[Pasted image 20250515173522.png]]

```python
## QQ-plot es una verificación de gaussianidad.
sm.qqplot(fit.resid, line="s");
```
![[Pasted image 20250515173535.png]]

```python
plot_acf(fit.resid, bartlett_confint=False);
```
![[Pasted image 20250515173555.png]]

### Mejorando el modelo
De la gráfica de $R_t$ vs. $S_{t-6}$ queda claro que el comportamiento es distinto dependiendo de si es positivo o negativo el valor de SOI. Podemos mejorar el modelo incluyendo una variable indicatriz para esto, es decir:
$$
R_t = \beta_0 + \beta_1S_{t-6} + \beta_2D_{t-6} + B_3S_{t-6}D_{t-6} + w_t
$$
siendo $D_t = 1$ si $S_t > 0$ y $0$ en otro caso. Esto es equivalente a ajustar dos rectas en las dos regiones.

```python
dummy = soi>0
fish = pd.concat([rec, soi.shift(6), dummy.shift(6)], axis=1).dropna()
fish.columns = ["rec", "soiL6", "DL6"]

fit = ols("rec ~ soiL6+DL6+soiL6*DL6", data=fish).fit()
print(f"RMSE de los residuos: {np.sqrt(fit.mse_resid)}")
fit.summary()
```
RMSE de los residuos: 21.83859202064757
<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>           <td>rec</td>       <th>  R-squared:         </th> <td>   0.402</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.398</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   99.43</td>
</tr>
<tr>
  <th>Date:</th>             <td>Tue, 22 Apr 2025</td> <th>  Prob (F-statistic):</th> <td>3.20e-49</td>
</tr>
<tr>
  <th>Time:</th>                 <td>15:38:06</td>     <th>  Log-Likelihood:    </th> <td> -2010.7</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   447</td>      <th>  AIC:               </th> <td>   4029.</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   443</td>      <th>  BIC:               </th> <td>   4046.</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     3</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
          <td></td>             <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>         <td>   74.4794</td> <td>    2.865</td> <td>   25.998</td> <td> 0.000</td> <td>   68.849</td> <td>   80.110</td>
</tr>
<tr>
  <th>DL6[T.True]</th>       <td>   -1.1394</td> <td>    3.711</td> <td>   -0.307</td> <td> 0.759</td> <td>   -8.433</td> <td>    6.155</td>
</tr>
<tr>
  <th>soiL6</th>             <td>  -15.3583</td> <td>    7.401</td> <td>   -2.075</td> <td> 0.039</td> <td>  -29.904</td> <td>   -0.812</td>
</tr>
<tr>
  <th>soiL6:DL6[T.True]</th> <td>  -51.2441</td> <td>    9.523</td> <td>   -5.381</td> <td> 0.000</td> <td>  -69.959</td> <td>  -32.529</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 4.767</td> <th>  Durbin-Watson:     </th> <td>   0.610</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.092</td> <th>  Jarque-Bera (JB):  </th> <td>   4.682</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.215</td> <th>  Prob(JB):          </th> <td>  0.0962</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.741</td> <th>  Cond. No.          </th> <td>    14.1</td>
</tr>
</table><br>Notes:<br>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.

```python
plt.scatter(fish.soiL6,fit.fittedvalues, color="firebrick")
plt.scatter(soi.shift(lag), rec);
```
![[Pasted image 20250515173913.png]]

```python
rec.plot()
fit.fittedvalues.plot().legend(["Recruitment","Ajuste"]);
```
![[Pasted image 20250515173926.png]]

### Análisis de residuos del ajuste
```python
fit.resid.plot();
plt.title("Residuos del ajuste");
print(f"RMSE del residuo: {np.std(fit.resid)}")
```
RMSE del residuo: 21.74066062129114
![[Pasted image 20250515173955.png]]

```python
## QQ-plot es una verificación de gaussianidad.
sm.qqplot(fit.resid, line="s");
```
![[Pasted image 20250515174007.png]]

```python
plot_acf(fit.resid, bartlett_confint=False);
```
![[Pasted image 20250515174021.png]]

