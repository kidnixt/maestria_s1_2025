Con lo visto ([[9 Modelos ARMA|ARMA]] y [[10_Modelos ARIMA|ARIMA]]), hemos construido una especie de receta (debida de Box-Jenkins) para trabajar con este tipo de modelos, a saber:
- Graficar los datos
- Transformar los datos (por ejemplo, transformación logarítmica, detrend, diferenciación o una combinación de transformaciones).
- Identificar los órdenes de dependencia ([[3.2_Estimación de la correlación a partir de muestras#Propiedad (distribución asintótica de la ACF)|ACF]], [[7_Modelos autorregresivos y autocorrelación parcial#Función de autocorrelación parcial (PACF)|PACF]]).
- Estimación de parámetros (Mínimos cuadrados o máxima verosimilitud - esto en general ya esta implementado en las librerías de `python`, el `fit()`)
- Diagnóstico (análisis de residuos por ejemplo)
- Elección del modelo (criterio de información de tipo [[4_Modelos lineales#Akaike information criterion|AIC]], evitar overfitting, etc.)
- Predicción en base a estimadores lineales calculados recursivamente e intervalos de confianza.

## Ejemplo completo: La serie GNP
Esta serie contiene el Producto Nacional Bruto (GNP) de Estados Unidos medido trimestralmente desde 1947 a 2022.

### Graficar los datos
```python
gnp = astsa.gnp
gnp.plot();
```

### Transformar datos
```python
#Transformo por log
x=np.log(gnp)
x.plot();
```
![[Pasted image 20250605174629.png]]

### Identificar órdenes de dependencia

Graficamos la ACF para ver que la serie no es estacionaria
```python
plot_acf(x, bartlett_confint=False);
```
![[Pasted image 20250605174654.png]]

Diferenciamos una vez para lograr estacionariedad
```python
y=x.diff().dropna()

y.plot()
plt.title(f"Serie de diferencias, μ = {np.mean(y)}");
```
![[Pasted image 20250605174828.png]]

Analizamos ACF y PACF
```python
plot_acf(y, bartlett_confint=False);
```
![[Pasted image 20250605174842.png]]
```python
plot_pacf(y);
```
![[Pasted image 20250605174914.png]]

Mirando lo anterior podemos inclinarnos por varios modelos para $x$:
- $ARIMA(1,1,0)$ ya que diferenciamos una vez y vemos que la PACF corta en 1.
- $ARIMA(0,1,2)$ ya que diferenciamos una vez y vemos que la ACF corta en 2.
- Otras posibilidades a testear. Probemos y hagamos diagnóstico.

### Modelo 1: ARIMA(1,1,0)
```python
fit = ARIMA(x,order=(1,1,0), trend="t").fit()
res = fit.resid[1:]; #acordarse de sacar el primero
print(f"RMSE: {np.std(res)}")
fit.summary()
```
```
RMSE: 0.009502645075030325
```

```python
fig, axs = plt.subplots(1, 3)
res.plot(ax=axs[0])

axs[0].title.set_text(f"RMSE: {np.std(res)}")

plot_acf(res, ax=axs[1], bartlett_confint=False)
qqplot(res,line='s', ax=axs[2]);
```
![[Pasted image 20250605175143.png]]

### Modelo 2: ARIMA(0,1,2)
```python
fit = ARIMA(x,order=(0,1,2), trend="t").fit()
res = fit.resid[1:]; #acordarse de sacar el primero
print(f"RMSE: {np.std(res)}")
fit.summary()
```
```
RMSE: 0.00944678036519239
```

```python
fig, axs = plt.subplots(1, 3)
res.plot(ax=axs[0])

axs[0].title.set_text(f"RMSE: {np.std(res)}")

plot_acf(res, ax=axs[1], bartlett_confint=False)
qqplot(res,line='s', ax=axs[2]);
```

Como vemos, dan bastante parecidos, con leve ventaja para el $ARIMA(0,1,2)$. Probemos combinar ambas en un $ARIMA(1,1,1)$.

### Modelo 3: ARIMA(1,1,1)
```python
fit = ARIMA(x,order=(1,1,1), trend="t").fit()
res = fit.resid[1:]; #acordarse de sacar el primero
print(f"RMSE: {np.std(res)}")
fit.summary()
```
```
RMSE: 0.009484904399350236
```

```python
fig, axs = plt.subplots(1, 3)

res.plot(ax=axs[0])

axs[0].title.set_text(f"RMSE: {np.std(res)}")
plot_acf(res, ax=axs[1], bartlett_confint=False)
qqplot(res,line='s', ax=axs[2]);
```
![[Pasted image 20250605175437.png]]

## Validación de residuos: estadístico de Ljung-Box-Pierce
La serie anterior de residuos parece haber quedado "blanca". Sin embargo, es bueno disponer de un test que permita evaluar si la ACF en su conjunto es razonablemente blanca en lugar de mirar lag a lag. Para ello se usa el *estadístico de Ljung-Box-Pierce*.

$$
Q = n(n+2)\sum_{h=1}^H{\frac{\hat{\rho}^2_e(h)}{n-h}}
$$
donde $H$ es una ventna. La idea de este estadístico es acumular varias correlaciones en la ventana $H$ para ver si en su conjunto son todas despreciables (en lugar de una a una).

El estadistico $Q$ es asintóticamente $\chi^2_{H-p-q}$ por lo que si el valor de $Q$ es grande (más que el cuantil $\alpha$ de la $\chi^2$) rechazamos la hipótesis de independencia.

En general lo que se hace es mirar los $p$-valores, es decir cuánta probabilidad queda a la derecha de $Q$. Es es pequeño (ej: $p < 0.05$) se rechaza la hipótesis.

```python
from statsmodels.stats.api import acorr_ljungbox

acorr_ljungbox(res,10)
```
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>lb_stat</th>
      <th>lb_pvalue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>0.003281</td>
      <td>0.954324</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.197290</td>
      <td>0.549556</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.811029</td>
      <td>0.612538</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3.849914</td>
      <td>0.426699</td>
    </tr>
    <tr>
      <th>5</th>
      <td>8.023301</td>
      <td>0.154956</td>
    </tr>
    <tr>
      <th>6</th>
      <td>8.289443</td>
      <td>0.217656</td>
    </tr>
    <tr>
      <th>7</th>
      <td>8.906550</td>
      <td>0.259435</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9.149590</td>
      <td>0.329834</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9.838461</td>
      <td>0.363720</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10.428248</td>
      <td>0.403758</td>
    </tr>
  </tbody>
</table>
</div>

### Predicción:
Como última paso pasamos a la predicción de $2$ años (8 trimestres):

```python
#Usamos el modelo ajustado para predecir a futuro

h = 32 #horizonte de predicción

predicciones = fit.get_prediction(start=gnp.size,end=gnp.size+h)
xhat = predicciones.predicted_mean
confint = predicciones.conf_int(alpha=0.05) #alpha es la confianza del intervalo

x.plot()
xhat.plot()
plt.legend(["Observado","Predicción"])
plt.fill_between(xhat.index,confint["lower value"], confint["upper value"], alpha=0.2);
plt.title(f"Predicción de la serie log(gnp) a {h} trimestres");
```
![[Pasted image 20250605180037.png]]

### Deshacer la transformación
Ahora podemos volver a la variable original, deshaciendo la transformación $x \rightarrow \log(x)$ mediante $\hat{x} \rightarrow \exp(\hat{x})$: 

```python
gnp.plot()

estimated_gnp = np.exp(xhat) #aplicamos exp
confint_exp = np.exp(confint) #aplicamos exp

estimated_gnp.plot()
plt.legend(["Observado","Predicción"])
plt.fill_between(xhat.index,confint_exp["lower value"], confint_exp["upper value"], alpha=0.2);

plt.title(f"Predicción de la serie gnp a {h} trimestres");
```
![[Pasted image 20250605180243.png]]