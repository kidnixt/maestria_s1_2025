Cuando el modelo es no [[1_Series_Temporales/4_Modelos lineales.md|lineal]], ¿qué podemos hacer?

**Ejemplo:** En el caso de la combinación de $sin$ y $cos$ ya sabíamos la frecuencia, pero si la dejamos libre el modelo:

$$
x_t = \beta_1 cos(2\pi ft) + \beta_2 sin(2\pi ft)
$$
pasa a ser no lineal, con parámetros $\theta = (\beta_1, \beta_2, f)$ 

**Ejemplo:** Crecimiento logístico, en estudio de poblaciones, muchas veces es útil ajustar un modelo de la forma:
$$
f(t;\theta) = \frac{A}{1+e^{-b(t-t_0)}}
$$
siendo los parámetros en este caso $\theta = (A, b, t_0)$

## Idea: aplicar el mismo criterio de mínimos cuadrados

Es decir si tengo una función no lineal de los parámetros desconocidos $y_t(\theta)$, buscamos resolver.

$$
\min_\theta \sum_{t=1}^n (x_t - y_t(\theta))^2
$$
**Problema:** la optimización anterior no necesariamente es fácil de hacer. No hay garantías de convexidad, puede tener mínimos locales.

El algoritmo más utilizado para resolver el problema anterior es el de *Gauss-Newton* que converge a un mínimo local. Solo si tenemos una buena aproximación inicial de la solución, converge al óptimo.

**Observación:** La elección de la condición inicial es *completamente independiente* del modelo utilizado. 
- A veces es posible hacer una aproximación lineal de primer orden. 
- Otra idea es utilizar propiedades de la función a ajustar para elegir buenas condiciones iniciales. 
- También es buena idea "sacudir" un poco el proceso cambiando las condiciones para ver cuán robusto es el ajuste.

### Ejemplo:

Analicemos los ingresos a CTI por COVID en Uruguay en el período 15/3 -- 30/4
```python
import pandas as pd

datos = pd.read_csv("https://raw.githubusercontent.com/GUIAD-COVID/datos-y-visualizaciones-GUIAD/master/datos/estadisticasUY_cti.csv", header=0, index_col=[1], parse_dates=[1], date_format="%d/%m/%Y")

cti = datos["ingresos"].iloc[40:120]
cti.plot();
```
![[Pasted image 20250527145245.png]]

### Modelo:
Proponemos el siguiente modelo no lineal para ajustar una "meseta":
$$
f(t,\theta) = a + be^{-ct}
$$
siendo $\theta = (a,b,c)$

### Implementación en Python
- La biblioteca `scipy` ofrece una función `scipy.optimize.least_squares`. Recibe como input una función $g(\theta)$ que a partir de los valores de los parámetros $\theta$ devuelve el **vector** de residuos.
- Es decir, en nuestro caso debemos construir una función $f(t,\theta)$ que evalúe la función no lineal a optimizar.
- Luego realizar una función $g$ que realice lo mismo evaluando $f$ en cada valor de $t$ y construya un vector:
$$
g(\theta) = (f(t_1,\theta) - x_{t_1},...,f(t_n,\theta)-x_{t_n})
$$
- Por último, se llama a `least_squares` pasando como parámetros la función $g$ y la condición inicial de búsqueda $\theta_0$.

```python
def f(theta, t):
    return theta[0] + theta[1]*np.exp(- theta[2] * t)

ts = np.arange(0,cti.size)
xs = cti.values

#En la implementación le llamamos residuos a la función g anterior
def residuos(theta):
    return f(theta, ts) - xs
```
```python
from scipy.optimize import least_squares
theta0 = [50,-50,0.1]
fit = least_squares(residuos, theta0)
fit
```
![[Pasted image 20250527150322.png]]
### Resultados
Del fit anterior podemos extraer los coeficientes y el error.
```python
a = fit.x[0]
b = fit.x[1]
c = fit.x[2]

#Least squares usa como loss 1/2 de la suma de los residuos
#por lo que a fit.cost hay que multiplicarlo por dos para obtener la suma de cuadrados
MSE_fit = 2*fit.cost/xs.size

print("Coeficientes estimados:")
print(f"a = {a}")
print(f"b = {b}")
print(f"c = {c}")
print(f"Mean square error: {MSE_fit}")
```
```
Coeficientes estimados: 
a = 47.19848397604239 
b = -39.148174713716685 
c = 0.07958170675096753 
Mean square error: 53.413796828588985
```

```python
plt.plot(ts,xs, label="Datos");\
plt.plot(ts,f(fit.x,ts), label="Estimacion")
plt.legend();
plt.title(f"Ajuste: RMSE = {np.sqrt(MSE_fit)}");
```
![[Pasted image 20250527150601.png]]

```python
#en fit.fun me guarda los residuos que alcanzan el mínimo.
#es equivalente a evaluar la función ajustada y restarle la serie original.

residuos = fit.fun
plt.plot(ts,residuos);
plt.title(f"Residuos: RMSE = {np.std(residuos)}"); #o bien sqrt(loss) debería dar lo mismo
```
![[Pasted image 20250527150641.png]]

```python
sm.qqplot(residuos, line="s");
```
![[Pasted image 20250527150654.png]]

```python
from statsmodels.graphics.tsaplots import plot_acf
plot_acf(residuos, bartlett_confint=False);
```
![[Pasted image 20250527150708.png]]

```python
#Calculo el R^2
n=cti.size

RSS = np.sum(residuos**2)/n
SSE = np.sum((cti-np.mean(cti))**2)/n
R2 = (SSE-RSS)/SSE

print(f"RSS = {RSS}");
print(f"SSE = {SSE}");
print(f"R2 = {R2}");
```
