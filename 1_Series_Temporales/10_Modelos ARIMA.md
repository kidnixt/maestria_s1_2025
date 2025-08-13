En algunos casos, la serie de datos $x_t$ no es estacionaria, y no alcanza con "sacarle el trend" para volverla estacionaria.

## Ejemplo: [[2.1_Ejemplos Series Temporales#Paseo al azar con deriva|Paseo al azar]]
$$
x_t = x_{t-1} + \delta + w_t
$$
con $w_t$ ruido blanco de varianza $\sigma^2_w$. Este proceso no es estacionario (tiene una tendencia $\delta t$ de pendiente $\delta$).
```python
## Simulación de un paseo al azar con deriva
delta = 0.1
w = np.random.normal(loc=0,scale=1,size=2000)
x = np.cumsum(w+delta)

x = pd.Series(x)
x.plot()
plt.title(f"Paseo al azar con deriva δ = {delta} y varianza del ruido unitaria");
```
![[Pasted image 20250605164741.png]]

### Idea 1: Ajuste lineal
$$
x_t = \beta_0 + \beta_1t + w_t
$$
con $w_t$ ruido blanco gaussiano.
```python
from statsmodels.formula.api import ols
fit = ols("x~x.index.values", data=x).fit()
fit.summary()
```
```python
x.plot()
fit.fittedvalues.plot();
```
![[Pasted image 20250605164850.png]]

Miremos los residuos del ajuste:
```python
fit.resid.plot();
plt.title("Residuos del ajuste");
```
![[Pasted image 20250605164937.png]]

Autocorrelación de los residuos
```python
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf
plot_acf(fit.resid, bartlett_confint=False);
```
![[Pasted image 20250605165009.png]]

Claramente los residuos no son ruido blanco y en la autocorrelación aun queda información.

### Idea 2: Tomar diferencias de la serie
Sea:
$$
y_t = x_t - x_{t-1} = x_{t-1} + \delta + w_t - x_{t-1} = \delta + w_t
$$
Es decir, al tomar las diferencias la serie se vuelve ruido blanco puro en este caso (estacionario), a menos de la media.

```python
y = x.diff().dropna() #diferencio la serie en pandas directamente. El dropna es para eliminar el primer valor que no tengo.
y.plot()
plt.title(f"Serie de diferencias, media = {np.mean(y)}");
```
![[Pasted image 20250605165218.png]]

Miremos ahora la autocorrelación de las diferencias:
```python
plot_acf(y,bartlett_confint=False);
```
![[Pasted image 20250605165805.png]]

Tambien chequeamos sin son aproximadamente Gaussianos:
```python
from statsmodels.graphics.api import qqplot
qqplot(y,line='s');
```
![[Pasted image 20250605165828.png]]

## Ejemplo: Serie con trend lineal
Supongamos que:
$$
x_t = \mu_t + y_t
$$
con $\mu_t = \beta_0 + \beta_1 t$ e $y_t$ estacionario. Entonces (introduciendo el operador $\nabla$ para las diferencias):
$$
\nabla x_t = x_t - x_{t-1} = \mu_t + y_t - \mu_{t-1} - y_{t-1} = \beta_1 + \nabla y_t
$$

Es decir, la nueva serie "diferenciada" tiene una media $\beta_1$ que sale de la pendiente de la recta de tendencia y una componente estacionaria compuesta por las diferencias de la seris $y$ anterior.

**Nota:** En general, si el "trend" es un polinomio de grado $n$, diferencias $n$ veces elimina el trend.

## Definición Modelo ARIMA

**Definición:** Decimos que una serie $x_t$ es $ARIMA(p,d,q)$ si:
$$
y_t = \nabla^d x_t 
$$
es un $ARMA(p,q)$. Es decir, si diferenciar la serie $d$ veces produce un proceso [[9 Modelos ARMA|ARMA]]. El nuevo parámetro $d$ es la cantidad de veces a diferenciar.

### Ajuste de modelos ARIMA
Para el ajuste de modelos $ARIMA$, simplemente podemos reutilizar todo lo visto para $ARMA(p,q)$ una vez que sabemos cuántas veces hay que diferenciar $(d)$. O sea, lo único nuevo es "descubrir" el $d$ necesario para que la serie quede estacionaria. Típicamente no se usa $d$ muy grande.

### Ejemplo

Consideremos que tenemos una serie $x_t$ de la forma:
$$
x_t = \beta_0 + \beta_1 t + y_t
$$
con $y_t$ tal que $z_t = \nabla y_t$ es autorregresivo de orden $1$ y coeficiente $\phi$.  

```python
from statsmodels.tsa.api import arma_generate_sample

n=500
beta0 = 50
beta1 = 3
phi=0.9

z = arma_generate_sample([1,-phi],[1],n)
y = np.cumsum(z) #Esto genera la serie y a partir de la z, acumulando que es la operación contraria a diferenciar.

x = beta0+beta1*np.arange(0,n)+y
x = pd.Series(x)
x.plot();
```
![[Pasted image 20250605171948.png]]

Analicemos ahora el proceso $\nabla x$, es decir las diferencias.
```python
#Al diferenciar recupero z pero con media beta1.
dx = x.diff().dropna()

dx.plot()
plt.title(f"Proceso de diferencias, μ = {np.mean(dx)}");
```
![[Pasted image 20250605172014.png]]
```python
#las correlaciones confirman la estacionariedad (parece un AR(1) en este caso)
plot_pacf(dx);
```
![[Pasted image 20250605172027.png]]

Al aplicar diferencias podemos ver que los residuos parecen un $AR(1)$, por lo tanto el siguiente paso sería ajustar un modelo para los residuos. Sin embargo, lo que tiene más sentido una vez que sabemos el valor de $d$ del $ARIMA$ ($1$ en este caso) podemos ajustar directamente un ARIMA con estos parámetros.

Esto es la mejor forma de hacerlo ya que el algoritmo debería encontrar los mejores parámetros teniendo en cuenta la diferenciación, cosa que si hacemos por separado puede dar ligeramente peor. Además es más comodo tener un solo modelo que ya haga todo.

```python
##Ajusto directo un ARIMA a la serie x usando statsplots
## el parámetro trend="t" permite que la serie diferenciada conserve el drift
## la media del resultado de diferenciar

from statsmodels.tsa.api import ARIMA

fit = ARIMA(x,order=(1,1,0), trend="t").fit()
fit.summary()
```

Analizamos los residuos del ajuste:
```python
res = fit.resid.iloc[1:] #saco el primer residuo porque no tiene sentido luego de diferenciar
res.plot();
```
![[Pasted image 20250605172342.png]]
```python
plot_acf(res, bartlett_confint=False);
```
![[Pasted image 20250605172403.png]]
```python
qqplot(res,line="s");
```
![[Pasted image 20250605172413.png]]

**Predicción a futuro:**
```python
#Usamos el modelo ajustado para predecir a futuro

h = 50 #horizonte de predicción

predicciones = fit.get_prediction(start=x.size,end=x.size+h)
xhat = predicciones.predicted_mean
confint = predicciones.conf_int(alpha=0.05) #alpha es la confianza del intervalo

x.plot()
xhat.plot()
plt.legend(["Observado","Predicción"])
plt.fill_between(xhat.index,confint["lower y"], confint["upper y"], alpha=0.2);
plt.title(f"Predicción de la serie x a {h} muestras");
```
![[Pasted image 20250605172445.png]]
