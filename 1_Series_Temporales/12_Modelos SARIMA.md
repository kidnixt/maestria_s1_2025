Una variante muy utilizada de los modelos [[10_Modelos ARIMA|ARIMA]] es aquella que agrega dependencia *estacional*. Es decir, existe algún período conocido $s$ de la serie llamado *componente estacional* que se conoce tiene influencia en el valor actual. Por ejemplo $s = 12$ en series anuales muestreadas mensualmente.

## Ejemplo (AR estacional puro)

Supongamos que la serie sigue la siguiente ecuación:
$$
x_t = \Phi x_{t-12} + w_t
$$
con $\Phi$ un coeficiente y $w_t$ ruido blanco Gaussiano de varianza $\sigma_w^2$.

El proceso anterior lo podemos pensar simplemente como un proceso $AR(12)$ pero cuyos coeficientes intermedios son todos $0$.

```python
Phi=0.9

coefs = np.concatenate(([1],np.zeros(11),[-Phi]))
print(f"Coeficientes autorregresivos = {coefs}")

x = arma_generate_sample(coefs,[1],480)
x = pd.Series(x)
x.plot();
```
```
Coeficientes autorregresivos = [ 1.   0.   0.   0.   0.   0.   0.   0.   0.   0.   0.   0.  -0.9]
```
![[Pasted image 20250605180634.png]]

Miremos ACF y PACF de la serie:
```python
plot_acf(x, lags=36,bartlett_confint=False);
```
![[Pasted image 20250605180652.png]]

```python
plot_pacf(x);
```
![[Pasted image 20250605180702.png]]

En principio se puede realizar el ajuste de la misma manera que antes:
```python
fit = ARIMA(x,order=(12,0,0), trend="n").fit()
fit.summary()
```
<table class="simpletable">
<caption>SARIMAX Results</caption>
<tr>
  <th>Dep. Variable:</th>           <td>y</td>        <th>  No. Observations:  </th>    <td>480</td>  
</tr>
<tr>
  <th>Model:</th>            <td>ARIMA(12, 0, 0)</td> <th>  Log Likelihood     </th> <td>-671.830</td>
</tr>
<tr>
  <th>Date:</th>            <td>Mon, 19 May 2025</td> <th>  AIC                </th> <td>1369.660</td>
</tr>
<tr>
  <th>Time:</th>                <td>16:25:53</td>     <th>  BIC                </th> <td>1423.919</td>
</tr>
<tr>
  <th>Sample:</th>                  <td>0</td>        <th>  HQIC               </th> <td>1390.988</td>
</tr>
<tr>
  <th></th>                      <td> - 480</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>        <td>opg</td>       <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
     <td></td>       <th>coef</th>     <th>std err</th>      <th>z</th>      <th>P>|z|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>ar.L1</th>  <td>    0.0207</td> <td>    0.020</td> <td>    1.034</td> <td> 0.301</td> <td>   -0.019</td> <td>    0.060</td>
</tr>
<tr>
  <th>ar.L2</th>  <td>    0.0091</td> <td>    0.023</td> <td>    0.393</td> <td> 0.694</td> <td>   -0.036</td> <td>    0.054</td>
</tr>
<tr>
  <th>ar.L3</th>  <td>    0.0010</td> <td>    0.022</td> <td>    0.046</td> <td> 0.964</td> <td>   -0.042</td> <td>    0.044</td>
</tr>
<tr>
  <th>ar.L4</th>  <td>    0.0138</td> <td>    0.023</td> <td>    0.587</td> <td> 0.557</td> <td>   -0.032</td> <td>    0.060</td>
</tr>
<tr>
  <th>ar.L5</th>  <td>   -0.0115</td> <td>    0.023</td> <td>   -0.505</td> <td> 0.614</td> <td>   -0.056</td> <td>    0.033</td>
</tr>
<tr>
  <th>ar.L6</th>  <td>    0.0140</td> <td>    0.024</td> <td>    0.590</td> <td> 0.555</td> <td>   -0.033</td> <td>    0.061</td>
</tr>
<tr>
  <th>ar.L7</th>  <td>   -0.0031</td> <td>    0.022</td> <td>   -0.143</td> <td> 0.886</td> <td>   -0.046</td> <td>    0.040</td>
</tr>
<tr>
  <th>ar.L8</th>  <td>    0.0025</td> <td>    0.021</td> <td>    0.120</td> <td> 0.904</td> <td>   -0.038</td> <td>    0.043</td>
</tr>
<tr>
  <th>ar.L9</th>  <td>    0.0145</td> <td>    0.022</td> <td>    0.672</td> <td> 0.502</td> <td>   -0.028</td> <td>    0.057</td>
</tr>
<tr>
  <th>ar.L10</th> <td>   -0.0187</td> <td>    0.021</td> <td>   -0.910</td> <td> 0.363</td> <td>   -0.059</td> <td>    0.022</td>
</tr>
<tr>
  <th>ar.L11</th> <td>    0.0251</td> <td>    0.020</td> <td>    1.253</td> <td> 0.210</td> <td>   -0.014</td> <td>    0.064</td>
</tr>
<tr>
  <th>ar.L12</th> <td>    0.8835</td> <td>    0.020</td> <td>   43.494</td> <td> 0.000</td> <td>    0.844</td> <td>    0.923</td>
</tr>
<tr>
  <th>sigma2</th> <td>    0.9248</td> <td>    0.065</td> <td>   14.189</td> <td> 0.000</td> <td>    0.797</td> <td>    1.053</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Ljung-Box (L1) (Q):</th>     <td>1.21</td> <th>  Jarque-Bera (JB):  </th> <td>0.90</td>
</tr>
<tr>
  <th>Prob(Q):</th>                <td>0.27</td> <th>  Prob(JB):          </th> <td>0.64</td>
</tr>
<tr>
  <th>Heteroskedasticity (H):</th> <td>1.06</td> <th>  Skew:              </th> <td>0.08</td>
</tr>
<tr>
  <th>Prob(H) (two-sided):</th>    <td>0.73</td> <th>  Kurtosis:          </th> <td>2.86</td>
</tr>
</table><br><br>Warnings:<br>[1] Covariance matrix calculated using the outer product of gradients (complex-step).

Sin embargo, si uno sabe que los coeficientes son $0$, es mejor obviarlos para lograr un mejor ajuste del coeficiente no nulo. Esto se logra proponiendo un modelo $ARMA(p,q) \times (P,Q)_s$. Aqui $(p,q)$ son las componentes ARMA como antes, y $(P,Q)_s$ indican dependencia a estaciones pasadas (dadas por el período $s$).

En la función `ARIMA` de `statsmodels` esto se hace pasándole el parámetro `seasonal_order=(P,D,Q,s)` donde $P$ es la parte autorregresiva estacional, $D$ la diferenciación estacional (no va en este caso), $Q$ la parte media móvil estacionanl, y $s$ el período, en este caso $12$.

```python
fit = ARIMA(x,order=(0,0,0), seasonal_order=(1, 0, 0, 12),trend="n").fit()

fit.summary()
```
<details>
<table class="simpletable">
<caption>SARIMAX Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>y</td>         <th>  No. Observations:  </th>    <td>480</td>  
</tr>
<tr>
  <th>Model:</th>           <td>ARIMA(1, 0, 0, 12)</td> <th>  Log Likelihood     </th> <td>-674.201</td>
</tr>
<tr>
  <th>Date:</th>             <td>Mon, 19 May 2025</td>  <th>  AIC                </th> <td>1352.402</td>
</tr>
<tr>
  <th>Time:</th>                 <td>16:25:55</td>      <th>  BIC                </th> <td>1360.750</td>
</tr>
<tr>
  <th>Sample:</th>                   <td>0</td>         <th>  HQIC               </th> <td>1355.683</td>
</tr>
<tr>
  <th></th>                       <td> - 480</td>       <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>         <td>opg</td>        <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
      <td></td>        <th>coef</th>     <th>std err</th>      <th>z</th>      <th>P>|z|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>ar.S.L12</th> <td>    0.8966</td> <td>    0.019</td> <td>   46.546</td> <td> 0.000</td> <td>    0.859</td> <td>    0.934</td>
</tr>
<tr>
  <th>sigma2</th>   <td>    0.9329</td> <td>    0.063</td> <td>   14.899</td> <td> 0.000</td> <td>    0.810</td> <td>    1.056</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Ljung-Box (L1) (Q):</th>     <td>0.47</td> <th>  Jarque-Bera (JB):  </th> <td>0.86</td>
</tr>
<tr>
  <th>Prob(Q):</th>                <td>0.49</td> <th>  Prob(JB):          </th> <td>0.65</td>
</tr>
<tr>
  <th>Heteroskedasticity (H):</th> <td>1.07</td> <th>  Skew:              </th> <td>0.09</td>
</tr>
<tr>
  <th>Prob(H) (two-sided):</th>    <td>0.68</td> <th>  Kurtosis:          </th> <td>2.89</td>
</tr>
</table><br><br>Warnings:<br>[1] Covariance matrix calculated using the outer product of gradients (complex-step).
</details>

## Ejemplo: combinación de AR estacional y MA local

Consideremos el proceso:
$$
x_t = \Phi x_{t-12} + w_t + \theta w_{t-1}
$$
En este caso tenemos un proceso $ARMA(0,1) \times (1,0)_{12}$

Consideremos la siguiente serie de nacimientos en EEUU durante el "baby boom":

```python
birth = astsa.birth
birth.plot();
```
![[Pasted image 20250605182226.png]]

Diferenciamos una vez para lograr estacionariedad:
```python
x = birth.diff().dropna()
x.plot();
```
![[Pasted image 20250605182247.png]]

Analizamos ACF y PACF
```python
plot_acf(x, bartlett_confint=False);
```
![[Pasted image 20250605182307.png]]

```python
plot_pacf(x);
```
![[Pasted image 20250605182315.png]]

Ajustamos ahora el modelo anterior $x_t = \Phi x_{t-12} + w_t + \theta w_{t-1}$, es decir, $ARMA(0,1) \times (1,0)_12$

```python
fit = ARIMA(x,order=(0,0,1), seasonal_order=(1, 0, 0, 12),trend="n").fit()

fit.summary()
```

**Predicción a futuro:**
```python
#Usamos el modelo ajustado para predecir a futuro
h = 24 #horizonte de predicción

predicciones = fit.get_prediction(start=birth.size,end=birth.size+h)
xhat = predicciones.predicted_mean
confint = predicciones.conf_int(alpha=0.05) #alpha es la confianza del intervalo

x.plot()
xhat.plot()
plt.legend(["Observado","Predicción"])
plt.fill_between(xhat.index,confint["lower value"], confint["upper value"], alpha=0.2);
plt.title(f"Predicción de la serie diff(birth) a {h} meses");
```
![[Pasted image 20250605182444.png]]

## Modelo SARIMA general

En el ejemplo anterior, tuvimos que *diferenciar* la serie `birth` para lograr algo estacionario. Esto nos lleva al modelo $SARIMA$ general, en el cual los parámetros son:
- $(p,d,q)$, las componentes locales del modelo SARIMA. $d$ es la cantidad de veces que hay que diferenciar con la muestra anterior.
- $(P,D,Q)$, las componentes estacionales del modelo SARIMA. $D$ es la cantidad de veces que hay que diferenciar con la estación anterior.
- $s$ es la frecuencia de las estaciones.

Para el [[#Ejemplo combinación de AR estacional y MA local|caso anterior]], podemos directamente ajustar un modelo $SARIMA(0,1,1) \times (1,0,0)_{12}$ pasándole el problema de diferenciar al ajuste.

```python
fit = ARIMA(birth,order=(0,1,1), seasonal_order=(1, 0, 0, 12),trend="n").fit()
fit.summary()
```

**Predicción**
```python
#Usamos el modelo ajustado para predecir a futuro
h = 24 #horizonte de predicción

predicciones = fit.get_prediction(start=birth.size,end=birth.size+h)
xhat = predicciones.predicted_mean
confint = predicciones.conf_int(alpha=0.05) #alpha es la confianza del intervalo

birth.plot()
xhat.plot()
plt.legend(["Observado","Predicción"])
plt.fill_between(xhat.index,confint["lower value"], confint["upper value"], alpha=0.2);
plt.title(f"Predicción de la serie diff(birth) a {h} meses");
```
![[Pasted image 20250605182920.png]]