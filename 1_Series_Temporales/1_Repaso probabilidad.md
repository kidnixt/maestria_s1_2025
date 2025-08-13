---
aliases: []
---
Un espacio de probabilidad es una terna $(\Omega, \mathcal{A}, P)$ donde:
- $\Omega$ es un conjunto: el *espacio muestral* que representa los resultados de experimentos.
- $\mathcal{A}$ es una familia de subconjuntos de $\Omega$ a los cuales asignaremos probabilidad, los denominados *eventos*.
- $P$ es una *medida de probabilidad*, es decir cumple:
	- $P(\Omega) = 1$
	- $P(\bigcup_n A_n) = \sum_n P(A_n)$, si los eventos $A_n \in \mathcal{A}$  son disjuntos

La medida de probabilidad representa *todo nuestro conocimiento previo* del modelo sobre lo que puede pasar en los experimentos.

## Propiedades usuales
Las medidas de probabilidad cumplen todas las propiedades lógicas que uno espera:
- $P(\emptyset) = 0$
- $P(A^c) = 1 - P(A)$
- Si $A \subset B, P(A) \leq P(B)$ 
- $P(A \cup B = P(A) + P(B) - P(A \cap B)$ 

### Probabilidad condicional
Es cuando queremos redefinir la probabilidad sabiendo que ya ocurrió un evento antes. Esto es, si sabemos que ocurrió el evento $A$ (nueva información), ¿cuál es la chance de observar $B$ también? Se define como:
$$
P(B|A) = \frac{P(A \cap B)}{P(A)}
$$

## Variable aleatoria
En general, uno está interesado en los *resultados numéricos* de un experimento. Eso lleva a la definición del concepto de *variable aleatoria*. Una v.a. $X$ es una función (medible) $X: \Omega \rightarrow \mathbb{R}$, que de cada experimento extrae un valor numérico.

**Ejemplos:**
- Exp: Tirar una moneda. $X=1$ si sale cara, $X=0$ si sale cruz (v.a. discreta)
- Exp: Tirar un dado. $X=$ no. que sale (v.a. discreta)
- Exp: Elegir una persona al azar de una población, $X=$ altura de la persona. $Y=$ peso de la persona (v.a. continuas).
- Exp: observar el mercado cambiario, $X_t =$ valor del dólar en tiempo $t$. $t$ representa cada día (v.a. continuas, una sucesión de ellas).

## Densidad y distribución
Una v.a. $X$ queda caracterizada por su *función de distribución acumulada $F(x)$* definida como:
$$
F(x) = P(X \leq x)\ \forall x \in \mathbb{R}
$$

En el caso continuo, en general es más intuitivo trabajar con la *densidad de probabilidad $f(x)$*. $f$ es la densidad de $X$ si:
$$
P(X \in (a,b)) = \int_a^b f(x)dx\ \forall a,b \in \mathbb{R}, a < b
$$
En particular:
$$
\begin{align}
&F(x) = \int_{-\infty}^x f(y)dy & f(x)=\frac{d}{dx}F(x) 
\end{align}
$$

### Ejemplo: Distribución normal
$$
f(x) = \frac{1}{\sqrt{2\pi\sigma}} e^{-\frac{(x-\mu)^2}{2\sigma^2}}
$$

```python
x = np.linspace(-3, 3, 500)

plt.subplot(1, 2, 1)
plt.plot(x, stats.norm.pdf(x))
plt.title("Densidad")
plt.subplot(1,2,2)
plt.plot(x, stats.norm.cdf(x))
plt.title("Distribución acumulada");
```
![[6dc42751cd84634095c065b38320a6b3a51e920dfb51fbccb22053d0b6d5479e.png]]

## Histograma
Si uno tiene un conjunto de datos $x_1, ..., x_n$ que son «realizaciones» de una cierta v.a. $X$, el histograma es un estimador de la densidad de la misma.

El histograma se construye:
- Partiendo el recorrido de la variable en intervalos.
- Calculando la frecuencia de puntos observados en la muestra en cada intervalo.
- Se realiza un gráfico de barras con el resultado.

Si además, normalizamos la altura de las barras para que el área total sea 1, se vuelve un estimador de la densidad.

### Ejemplo: Distribución normal
```python
X = np.random.normal(size=500)

plt.subplot(1, 2, 1)
plt.hist(X, bins=20, label="Histograma")
plt.legend()
plt.subplot(1,2,2)
plt.hist(X,bins=20, density=True, label="Histograma normalizado")
plt.plot(x, stats.norm.pdf(x), label="Densidad Teórica")
plt.legend();
```
![[18e539ea641329a5ca7b359af6c273e0f0478513ad6993ac0c7f84a381ed9d19.png]]

### Ejemplo: Distribución exponencial
$$
f(x) = \lambda e^{-\lambda x}
$$
```python
#Sorteo 500 realizaciones de una exponencial de media theta
theta = 2
X = np.random.exponential(scale=theta, size=500)

plt.subplot(1, 2, 1)
plt.hist(X, bins=20, label="Histograma")
plt.legend()
plt.subplot(1,2,2)
plt.hist(X,bins=20, density=True, label="Histograma normalizado")
x=np.linspace(0,10,500)
plt.plot(x, (1/theta)*np.exp(-x/theta), label="Densidad Teórica")
plt.legend();
```
![[f562fca43142e7dcfeefd71161fe5c0c6ab36b95d2f064a9b1ced9aeadf2e461.png]]

### Ejemplo: Distribución lognormal
$$
X \thicksim \mbox{lognormal}(\mu, \sigma^2) \Leftrightarrow log(X) \thicksim N(\mu,\sigma^2)$$
```python
X = np.random.lognormal(mean=5.0, sigma=.5, size=500)

plt.subplot(1, 2, 1)
plt.hist(X, bins=20, label="Histograma de X")
plt.legend()
plt.subplot(1,2,2)
plt.hist(np.log(X),bins=20, density=True, label=r"Histograma de $\log(X)$")
plt.legend();
```
![[1b4ee7fa9fb3ba9760b69dc71e815d9e3437ad152679281cab79187354d61ea4.png]]

## Esperanza de una variable aleatoria
La *esperanza o media* de una variable aleatorias es una medida del "centro" o "primedio" de la misma. Se define en el caso continuo como:
$$
E[X] = \int_{-\infty}^\infty x f(x) dx
$$
Más en general, para cualquier función $g: \mathbb{R} \rightarrow \mathbb{R}$, si $X$ es una v.a., entonces $Y = g(X)$ es una nueva v.a. cuya media es:
$$
E[Y] = E[g(X)] = \int_{-\infty}^\infty g(x) f(x) dx
$$
**Ejemplos:**
- Si $X \thicksim N(\mu,\sigma^2)$, entonces $E[X] = \mu$
- Si $X \thicksim exp(\lambda)$, entonces $E[X] = \frac{1}{\lambda}$
- Si $X \thicksim lognormal(\mu, \sigma^2)$, entonces $E[X] = e^{\mu+\frac{\sigma^2}{2}}$

### Relación con el promedio de datos
La *ley de grandes números* dice que si tengo una muestra de datos $x_1, ..., x_n$ obtenidos de manera independiente de una v.a. $X$, entonces se verifica:
$$
\lim_{n\to\infty} \frac{1}{n}\sum_{i=1}^n x_i = E[X] \quad \text{con probabilidad }1.
$$
Es decir, para cualquier muestra que podamos sortear en la práctica, el promedio de los datos converge a la esperanza.

**Nota:** Esta ley vale a veces para promedios de variables *dependientes* también.

#### Ejemplo
$X \thicksim N(10, 100)$

```python
X = np.random.normal(size=10000,loc=10,scale=10) #sorteo 10000 muestras. Atención: R recibe mu y sigma, no mu y sigma^2

for n in (10,20,50,100,1000,5000,10000):
    print("Para n =",n,"\tel promedio es: ",np.mean(X[0:n]))
```

```
Para n = 10 	el promedio es:  6.866796509335286
Para n = 20 	el promedio es:  5.464659213210547
Para n = 50 	el promedio es:  7.127731413299702
Para n = 100 	el promedio es:  8.629866140726696
Para n = 1000 	el promedio es:  9.930193682543065
Para n = 5000 	el promedio es:  10.186370788763478
Para n = 10000 	el promedio es:  10.130356025540804
```

## Varianza de una variable aleatoria
La *varianza* es en cambio una medida de la *dispersión* de los resultados de la v.a. alrededor de su media. A mayor varianza, más "variabilidad" podemos esperar en los sorteos.
$$
Var(X) = E[(X - E[X])^2] = E[X^2] - (E[X])^2
$$
A la varianza se le denota en general por $\sigma^2$, y a su raíz $\sigma$ se le denomina *desvío estándar*.

**Ejemplo:** Si $X \thicksim N(\mu,\sigma^2)$ precisamente $\sigma^2$ es la varianza de la distribución. En este caso es uno de los parámetros. En el caso gaussiano (y solo en este caso), la mayor parte de la muestra (95%) de las muestras se concentran a $\pm2\sigma$ de la media.

```python
mu=10
sigma = 10
X = np.random.normal(size=10000,loc=mu,scale=sigma) #sorteo 10000 muestras.

plt.hist(X, bins = 100);
plt.vlines([mu-2*sigma,mu+2*sigma], ymin=0, ymax=350, color="teal")
plt.fill_between([mu+2*sigma,mu+4*sigma],0,350, alpha=0.2, color="gray")
plt.fill_between([mu-4*sigma,mu-2*sigma],0,350, alpha=0.2, color="gray")
plt.title(r"Histograma de $X\sim N(10,10)$, en gris la zona a dos desvíos estándar");
```
![[89dd16dd0f503baf38089c5bcc4670d34a3e828838ce404eb740be89b5d46f78.png]]

### Estimación de la varianza
Así como el promedio converge a la esperanza, podemos usar la ley de grandes números nuevamente para estimar la varianza y el desvío estándar a partir de una muestra $x_1, ..., x_n$ mediante la siguiente cuenta

$$\sigma_n^2 = \frac{1}{n} \sum_{i=1}^n (x_i-\bar{x}_n)^2, \quad \quad \sigma_n = \sqrt{\sigma_n^2}.\quad \bar{x}_n\ \text{es la media de la muestra}$$
Sin embargo, el mejor estimador (insesgado) para la varianza surge de normalizar por $n-1$ y es lo que generalmente se implementa en software:
$$s_n^2 = \frac{1}{n-1} \sum_{i=1}^n (x_i-\bar{x}_n)^2, \quad \quad s_n = \sqrt{s_n^2}.$$
```python
mu=10
sigma = 10 #desvio estandar
X = np.random.normal(size=10000,loc=mu,scale=sigma) #sorteo 10000 muestras.

print("Varianza estimada s^2_n: ", np.var(X))
print("Desvío estimado s_n: ", np.std(X))
```

```
Varianza estimada s^2_n:  98.62367760566976
Desvío estimado s_n:  9.930945453765707
```

