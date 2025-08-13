Un modelo $ARMA(p,q)$ es simplemente la combinación de los dos modelos vistos anteriormente: [[7_Modelos autorregresivos y autocorrelación parcial|AR]] y [[8_Modelos de Media Móvil|MA]]. En términos matemáticos, $x_t$ es un modelo $ARMA$ si se verifica:
$$x_t = \phi_1 x_{t-1} + \ldots + \phi_p x_{t-p} + w_t + \theta_1 w_{t-1} + \ldots + \theta_q w_{t-q}$$
con $\phi_p$ y $\theta_q$ distintos de 0. Los coeficientes $\phi_j$ son los coeficientes de la **parte autorregresiva**, y los $\theta_j$ los de la **parte media móvil**. Como siempre, $w_t$ es [[2.1_Ejemplos Series Temporales#Ruido blanco|ruido blanco]] (gaussiano) de varianza $\sigma_w^2$.

## Notación alternativa
Podemos reescribir la ecuación anterior como:
$$x_t - \phi_1 x_{t-1} - \ldots - \phi_p x_{t-p} =w_t + \theta_1 w_{t-1} + \ldots + \theta_q w_{t-q}$$

O bien:
$$\sum_{i=0}^p a_i x_t = \sum_{j=0}^q b_j w_j$$
Identificando $a_0=1$, $a_i = -\phi_i$, $b_0=1$ y $b_j = \theta_j$. Esta es la parametrización estándar en `python.statsmodels`.

## Ejemplo: $ARMA(1,1)$
Consideremos el siguiente proceso:
$$x_t = \phi x_{t-1} + w_t + \theta w_{t-1},$$
o bien
$$x_t - \phi x_{t-1} = w_t + \theta w_{t-1}$$

```python
## Ejemplo: ARMA(1,1)
phi=0.9
theta=0.5
x=arma_generate_sample([1,-phi],[1,theta],1000)
x=pd.Series(x)
x.plot(title=f"Modelo ARMA(1,1) con coeficientes ϕ={phi}, θ={theta}");
```
![[Pasted image 20250603184425.png]]

```python
plot_acf(x,bartlett_confint=False);
```
![[Pasted image 20250603184438.png]]

```python
plot_pacf(x);
```
![[Pasted image 20250603184447.png]]

**Problema:** Ahora la ACF y PACF no son tan claros estimadores del orden del modelo.

En general, para un modelo $ARMA(p,q)$ tanto la ACF como la PACF **decaen a 0 exponencialmente**.

Aqui no queda otra que *probar* el orden y cortar en base a algún criterio como AIC o BIC.

## Ajuste de un modelo $ARMA(1,1)$
```python
fit = ARIMA(x,order=(1,0,1), trend="n").fit()
fit.summary()
```
<table class="simpletable">
<caption>SARIMAX Results</caption>
<tr>
  <th>Dep. Variable:</th>           <td>y</td>        <th>  No. Observations:  </th>   <td>1000</td>   
</tr>
<tr>
  <th>Model:</th>            <td>ARIMA(1, 0, 1)</td>  <th>  Log Likelihood     </th> <td>-1437.233</td>
</tr>
<tr>
  <th>Date:</th>            <td>Mon, 19 May 2025</td> <th>  AIC                </th> <td>2880.466</td> 
</tr>
<tr>
  <th>Time:</th>                <td>16:23:04</td>     <th>  BIC                </th> <td>2895.189</td> 
</tr>
<tr>
  <th>Sample:</th>                  <td>0</td>        <th>  HQIC               </th> <td>2886.062</td> 
</tr>
<tr>
  <th></th>                      <td> - 1000</td>     <th>                     </th>     <td> </td>    
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
  <th>ar.L1</th>  <td>    0.9077</td> <td>    0.013</td> <td>   67.711</td> <td> 0.000</td> <td>    0.881</td> <td>    0.934</td>
</tr>
<tr>
  <th>ma.L1</th>  <td>    0.4810</td> <td>    0.029</td> <td>   16.499</td> <td> 0.000</td> <td>    0.424</td> <td>    0.538</td>
</tr>
<tr>
  <th>sigma2</th> <td>    1.0344</td> <td>    0.046</td> <td>   22.353</td> <td> 0.000</td> <td>    0.944</td> <td>    1.125</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Ljung-Box (L1) (Q):</th>     <td>0.03</td> <th>  Jarque-Bera (JB):  </th> <td>0.58</td>
</tr>
<tr>
  <th>Prob(Q):</th>                <td>0.87</td> <th>  Prob(JB):          </th> <td>0.75</td>
</tr>
<tr>
  <th>Heteroskedasticity (H):</th> <td>0.93</td> <th>  Skew:              </th> <td>0.06</td>
</tr>
<tr>
  <th>Prob(H) (two-sided):</th>    <td>0.54</td> <th>  Kurtosis:          </th> <td>2.99</td>
</tr>
</table><br><br>Warnings:<br>[1] Covariance matrix calculated using the outer product of gradients (complex-step).

Podemos observar que la autocorrelación de los residuos se asemeja al ruido blanco:
```python
res = fit.resid
plot_acf(res, bartlett_confint=False);
```
![[Pasted image 20250603184704.png]]

Y que los residuos son aproximadamente Gaussianos:
```python
from statsmodels.graphics.api import qqplot
qqplot(res,line="s");
```
![[Pasted image 20250603184851.png]]

### Causalidad e invertibilidad
**Observación:** No todos los juegos de parámetros $\phi_j, \theta_j$ son posibles. Algunos juegos de parámetros pueden producir procesos *no estacionarios*, o con parámetros no bien definidos.

**Ejemplo:** Consideremos un proceso $AR(1)$ con $|\phi| > 1$, se tiene que:
$$
x_t = \phi_1 x_{t-1} + w_t
$$

Este proceso amplifica el valor de la muestra anterior. Eventualmente explota.
```python
# AR con explosion
phi=-1.1
x=arma_generate_sample([1,-phi],[1],100)
x=pd.Series(x)
x.plot()
plt.title(f"Modelo AR(1) con ϕ = {phi}");
```
![[Pasted image 20250603185634.png]]

#### Polinomios del AR y MA:
Se definen los siguientes polinomios, asociados a los coeficientes de la parte AR y MA:
$$ \phi(z) = 1-\phi_1z-\phi_2z^2-...-\phi_pz^p $$$$ \theta(z) = 1 + \theta_1z + \theta_2 z^2 + ... + \theta_q z^q$$
#### Causalidad:
Un proceso $ARMA$ es causal si se puede escribir solo como función de las innovaciones pasadas:
$$
x_t = \sum_{j=0}^\infty{\psi_j\phi_{t-j}}
$$
con coeficientes $\psi$ a determinar y $\sum_{j=0}^\infty {\psi_j} < \infty$.

**Teorema:** Un proceso $ARMA(p,q)$ es causal (no explosivo) si y solo si las raíces del polinomio $\phi(z)$ están todas fuera del círculo unitario (es decir, $|z| > 1$ en toda raíz).

**Ejemplos:** Si $x_t$ es un $AR(1)$, entonces $\phi(z) = 1-\phi_1z$, y la raíz está fuera de $[-1, 1]$ si y solo si $|\phi_1| < 1$.

### Invertibilidad:
Un proceso $ARMA$ es invertible si se puede "despejar" $w_t$ de los valores pasados del proceso:
$$
w_t = \sum_{j=0}^\infty{\pi_j x_{t-j}},
$$
con coeficientes $\pi$ a determinar y $\sum_{j=0}^\infty{|\pi_j|} < \infty$.

**Teorema:** Un proceso $ARMA(p,q)$ es invertible si y solo si las raíces del polinomio $\theta(z)$ están todas fuera del círculo unitario (es decir, |z| > 1 en toda raíz).

```python
## Ejemplos
from statsmodels.tsa.api import ArmaProcess

print("Ejemplo 1: AR(1) inestable")
proc = ArmaProcess.from_coeffs(-1.1,0)
print(proc)
print(f"Es estacionario? -> {proc.isstationary}")

print("Ejemplo 2: ARMA(1,1) como antes")
proc = ArmaProcess.from_coeffs(.9,.5)
print(proc)
print(f"Es estacionario? -> {proc.isstationary}")
print(f"Es invertible? -> {proc.isinvertible}")
```
```
Ejemplo 1: AR(1) inestable
ArmaProcess
AR: [1.0, 1.1]
MA: [1.0, 0.0]
Es estacionario? -> False
Ejemplo 2: ARMA(1,1) como antes
ArmaProcess
AR: [1.0, -0.9]
MA: [1.0, 0.5]
Es estacionario? -> True
Es invertible? -> True
```

### Parámetros del ajuste de un modelo ARMA
El ajuste se realiza mediante *máxima verosimilitud* o *[[8_Modelos de Media Móvil#Método de los momentos|método de los momentos]]*, buscando siempre un modelo *causal* e *invertible.*

## Predicción en modelos ARMA

Dado un modelo $ARMA(p,q)$ de parámetros $\phi_1,...,\phi_p$ y $\theta_1,...,\theta_q$, y posiblemente una media $\mu$, es decir un proceso:
$$ \begin{align*}
x_t - \mu &= \phi_1 (x_{t-1} - \mu) + \ldots + \phi_p(x_{t-p}-\mu) \\
          &\quad +\ w_t + \theta_1 w_{t-1} + \ldots +\theta_q w_{t-q},
\end{align*}
$$
donde $w_t$ es ruido blanco Gaussiano de varianza $\sigma_w^2$.

**Pregunta:**
¿Cuál es la mejor predicción que uno puede hacer de $x_{t+1}$ conociendo los valores del proceso $x_0,...,x_t$ y los parámetros?

### El caso $AR(p)$

Supongamos $\mu = 0$ (en otro caso, simplemente centramos por la media). Este caso ya lo discutimos, si conocemos el proceso hasta tiempo $n$, entonces sabemos que:
$$
x_{n+1} = \phi_1 x_n + ... + \phi_p x_{n+1-p} + w_{n+1},
$$

El mejor predictor $\hat{x}_{n+1}^n$ en términos de error cuadrático medio es un predictor lineal:
$$
\hat{x}_{n+1}^n = \alpha_0 + \sum_{k=1}^n{\alpha_k x_k},
$$
que debe satisfacer las *ecuaciones de predicción:*
$$
E[(x_{n+1}-\hat{x}_{n+1}^n)x_k] = 0 \quad \forall k=0,...,n
$$
es decir, el error de predicción no debe estar correlacionados con ninguna de las muestra anteriores (extrajimos "toda la información").

> **Nota:** La notación $\hat{x}_{n+1}^n$ representa la predicción que hacemos de $x_{n+1}$ utilizando los primeros n datos.

Notemos que es muy fácil resolver esto en el caso de $AR(p)$ ya que tomando como predicción:
$$\hat{x}^n_{n+1} = \phi_1 x_n +\ldots + \phi_p x_{n-p+1},$$
se tiene que $x_{n+1} - \hat{x}_{n+1}^n = w_{n+1}$ que sabemos no está correlacionado con ninguna muestra anterior.

Es decir, simplemente propagando la recursión como si el ruido fuera $0$ obtenemos la mejor predicción. (Tambien se puede demostrar esto para $n+2, n+3$, etc.)

### El caso $ARMA(p,q)$
En el caso general no resulta tan sencillo resolver las ecuaciones de predicción, y debemos usar toda la historia del proceso. Consideremos el caso de predicción a un paso:

Construyo un estimador:
$$\hat{x}^n_{n+1} = \phi_{n1}x_n + \ldots +\phi_{nn}x_1 = \phi_n^T x$$
siendo $x=(x_1,\ldots,x_n)$ el vector con todos los valores anteriores.

Planteando la ecuación de predicción queda:
$$E[(x_{n+1} - \sum_{j=1}^n \phi_{nj}x_{n+1-j})x_{n+1-k}] = 0, \quad k=1,\ldots,n.$$

Haciendo la distributiva queda:
$$E[x_{n+1}x_{n+1-k}] =  \sum_{j=1}^n \phi_{nj}E[x_{n+1-j}x_{n+1-k}] = 0, \quad k=1,\ldots,n.$$
Identificando las covarianzas nos queda el siguiente sistema de ecuaciones:
$$\gamma(k) =  \sum_{j=1}^n \phi_{nj}\gamma(k-j), \quad k=1,\ldots,n.$$
Lo anterior es un sistema de ecuaciones de la forma:
$$\Gamma_n \phi_n = \gamma_n.$$
Resolviendo el sistema salen los coeficientes.

**Nota:** debemos tener calculadas las [[1.1_Variables aleatorias conjuntas#Covarianza una medida de dependencia||covarianzas]] del modelo

También se puede calcular la *varianza* del error de predicción para hacer intervalos de confianza, y queda:

$$P_{n+1}^n = \gamma(0) - \gamma_n^T \Gamma_n^{-1} \gamma_n.$$

**Problema**
La ecuación anterior solo explica como calcular "el paso siguiente" y aún así es muy difícil de resolver si el conjunto de datos es grande (sistema lineal grande). Afortunadamente ambos inconvenientes se pueden levantar usando métodos recursivos:
- Algoritmo de Durbin-Levinson
- Algoritmo de innovaciones

Estos son los algoritmos que usan bibliotecas de `python` para predecir. En particular, calculan:
- El mejor predictor lineal $\hat{x}_{n+m}^n$ $m$ muestras adelante. 
- La varianza $P_{n+m}^n$ del error, lo que permite construir intervalos de confianza.

## Ejemplo: La serie de Recruitment
```python
rec=pd.Series(astsa.rec["value"])
rec.plot()
plt.title(f"Recruitment. Media = {np.mean(rec)}");
```
![[Pasted image 20250605162633.png]]
```python
plot_pacf(rec);
```
![[Pasted image 20250605162640.png]]

```python
#Usamos la función de statsmodels para ajustar.
#trend="c" nos permite incorporar una tendencia constante (media) al fit.

from statsmodels.tsa.api import ARIMA
fit = ARIMA(rec,order=(2,0,0), trend="c").fit()
fit.summary()
```
```python
fit.resid.plot()
plt.title(f"Residuos del ajsute, RMSE = {np.sqrt(fit.mse)}");
```
![[Pasted image 20250605162732.png]]

Podemos ver que la autocorrelación de los ruidos se asemeja al ruido blanco:
```python
res = fit.resid
plot_acf(res, bartlett_confint=False);
```
![[Pasted image 20250605162755.png]]

Y que los ruidos son aproximadamente Gaussianos, pero ya no del todo. Tienen algunos outliers
```python
from statsmodels.graphics.api import qqplot
qqplot(res,line="s");
```
![[Pasted image 20250605162826.png]]
### Predicción a futuro
Ahora que tenemos el modelo ajustado, podemos agregar nuevas predicciones usando los algoritmos mencionados.
```python
#Usamos el modelo ajustado para predecir a futuro
h = 24 #horizonte de predicción

predicciones = fit.get_prediction(start=rec.size,end=rec.size+h)
xhat = predicciones.predicted_mean
confint = predicciones.conf_int(alpha=0.05) #alpha es la confianza del intervalo

rec.plot()
xhat.plot()
plt.legend(["Observado","Predicción"])
plt.fill_between(xhat.index,confint["lower value"], confint["upper value"], alpha=0.2);
plt.title("Predicción de la serie rec a 2 años");
```
![[Pasted image 20250605162926.png]]

### Predicción endógena de la serie
```python
#Usamos el modelo ajustado para predecir los propios valores de la serie.
predicciones = fit.get_prediction(start=0,end=rec.size)
xhat = predicciones.predicted_mean
confint = predicciones.conf_int(alpha=0.05) #alpha es la confianza del intervalo

rec.plot()
xhat.plot()
plt.legend(["Observado","Predicción"])
plt.fill_between(xhat.index,confint["lower value"], confint["upper value"], alpha=0.2);
plt.title("Predicción endógena");
```
![[Pasted image 20250605163017.png]]

El error en predicción son exactamente **los residuos del fit**
```python
error = rec-xhat
error.plot()
plt.title(f"Error de predicción, RMSE = {np.std(error)}");
```
![[Pasted image 20250605163049.png]]

