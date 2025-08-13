(Ver [[1_Repaso probabilidad|repaso probabilidad]] y [[1.1_Variables aleatorias conjuntas|variables aleatorias conjuntas]].)
## Función media de una [[2_Series Temporales|serie temporal]]
> **Definición:** La *función media* (mean function) de un proceso estocástico o serie temporal $\{x_t\}$ se define como:
$$\mu_x(t) = \mu_{xt} = E[x_t] = \int x f_t(x)dx.$$
Notar que en principio depende del tiempo. Esto va a ser un problema porque queremos calcular la media para cada $t$, pero generalmente tenemos 1 solo valor. Esto veremos como resolverlo más adelante.

### Ejemplo: Ruido blanco
Si $x_t = w_t$ ruido blanco **Gaussiano**, entonces $E[w_t] = 0$ por lo que $\mu_t = 0$ para todo $t$.

```python
w = np.random.normal(size=1000,loc=0,scale=1)  # 1000 N(0,1) variates
plt.plot(w)
plt.axhline(y=0, color='b')
plt.title(r"Ruido Blanco Gaussiano $w(t)$");
```
![[7d87c43374bafa6001ed43805a28e89dc12e345c144095588f06b4c5808a1984.png]]

### Ejemplo: Media móvil
Si $w_t$ es ruido blanco Gaussiano y definimos el proceso de media móvil:
$$x_t = \frac{1}{3}[w_{t-2} + w_{t-1} + w_t]$$

Entonces $E[x_t] = \frac{1}{3}[E[w_{t-2}] + E[w_{t-1}] + E[w_t] = 0$ por lo que también $\mu_t = 0$ para todo $t$.

```python
n=3
x = sp.signal.lfilter(1/n*np.ones(n),1,w) #Aplica el filtro de media móvil
plt.plot(x, label=r"$x(t)$");
plt.axhline(y=0, color='b')
plt.legend();
```
![[Pasted image 20250414024833.png]]

### Ejemplo: Paseo al azar con deriva
Si el proceso es:
$$x_t = \delta t + \sum_{j=1}^t w_j$$

con $\{w_j\}$ ruido blanco, entonces $\mu_t = E[x_t] = \delta t$.

### Ejemplo: Señal más ruido.
Si el proceso es de la forma:
$$x_t = s_t + w_t$$

con $s_t$ una señal dada (ej.: función coseno del ejemplo del cuaderno anterior), y $w_t$ ruido de media $0$, entonces $E[x_t] = s_t$.

### Observaciones:
* El comportamiento completo de un proceso estocástico queda definida por *toda la familia* de distribuciones conjuntas de las variables que lo integran $\{x_t\}$.
* Dichas distribuciones deben ser consistentes entre sí (muy difícil de chequear).
* En general es muy difícil trabajar con la distribución conjunta salvo casos excepcionales (ej.: Gaussiana).
* Si la media no cambia con el tiempo, podemos promediar sobre todo el $t$. (Ver [[#Ejemplo Ruido blanco]])
* La *[[1.1_Variables aleatorias conjuntas#Densidad marginal|densidad marginal]]* de $x_t$ **solo nos dice** cómo se comporta el proceso en tiempo $t$, pero no su interrelación con los demás tiempos.

¿Cómo podemos medir la relación de los valores del proceso en *diferentes momentos del tiempo*?

## Autocovarianza de una [[2_Series Temporales|serie temporal]]
La autocovarianza es una medida de la *estructura de dependencia interna* del proceso.

> **Definición:** La *función de autocovarianza* de una serie temporal es la covarianza entre dos instantes de tiempo: $$\gamma_x(s,t) = \textrm{Cov}(x_s,x_t) = E[(x_s-\mu_s)(x_t-\mu_t)].$$para cada $s$ y $t$.

**Propiedades:**
* Para $s=t$, $\gamma_x(s,s) = \textrm{Cov}(x_s,x_s) = \textrm{Var}(x_s)$.  
* $\gamma_x(s,t) = \gamma_x(t,s)$ por definición por lo que la función es simétrica.
* Si $\gamma(s,t) = 0$ quiere decir que no hay dependencia lineal (pero no que son independientes).
* Si $\gamma(s,t) = 0$ y $x_s,x_t$ son conjuntamente Gaussianos, entonces $x_s$ y $x_t$ son independientes.

### Ejemplo: Ruido blanco
Si $x_t = w_t$ ruido blanco, por definición $w_s$ y $w_t$ son no correlacionados, es decir $\textrm{Cov}(w_s,w_t) = 0$. A su vez, a $\textrm{Cov}(w_s,w_s) = \textrm{Var}(w_s) = \sigma^2_w$ se le denomina *potencia* del ruido blanco.

Por lo tanto:
$$\gamma(s,t) = \left\{\begin{array}{ll} \sigma^2_w & s=t \\ 0 & s\neq t \end{array}\right.$$

### Ejemplo: Media móvil
Considere nuevamente proceso de media móvil:
$$x_t = \frac{1}{3}[w_{t-2} + w_{t-1} + w_t]$$

Aplicando la propiedad de combinaciones lineales citada antes se obtiene:
$$\gamma(s,t) = \begin{cases} 1/9 \sigma_w^2 & s=t-2 \\
2/9 \sigma_w^2 & s=t-1 \\
3/9 \sigma_w^2 & s=t \\
2/9 \sigma_w^2 & s=t+1 \\
1/9 \sigma_w^2 & s=t+2 \\
0 & \text{en otro caso}. \end{cases}$$

Notar que en los dos casos anteriores, $\gamma(s,t)$ solo depende de $t-s$, el *intervalo de tiempo entre las observaciones*.

### Ejemplo: Paseo al azar
Consideremos un paseo al azar sin deriva $x_t = \sum_{j=1}^t w_j$ con $\{w_j\}$ ruido blanco. La autocovarianza queda:
$$\gamma_x(s,t) = \textrm{Cov}(x_s,x_t) = \textrm{Cov}\left(\sum_{j=1}^s w_j, \sum_{k=1}^t w_k\right) = \min\{s,t\} \sigma_w^2.$$

*Prueba:* usar la propiedad de combinaciones lineales y contar la cantidad de términos que sobreviven usando que $\textrm{Cov}(w_s,w_t)=0$ si $s\neq t$.

Observar que en este caso $\gamma_x$ depende explícitamente de $s$ y $t$ (no solo la diferencia) y que $\textrm{Var}(x_t) = t\sigma_w^2$ es creciente con $t$ (porque acumulamos más términos).

```python
#Paseo al azar sin deriva
w = np.random.normal(size=500,loc=0,scale=1)
x = np.cumsum(w)

plt.plot(x)
plt.title("Varias realizaciones de un paseo al azar sin deriva")

#agrego mas realizaciones
for i in range(1,20):
    w = np.random.normal(size=500,loc=0,scale=1)
    x = np.cumsum(w)
    plt.plot(x,alpha=0.3)

plt.plot(ylims=(-10,10));
```
![[Pasted image 20250414031030.png]]

## Autocorrelación de una [[2_Series Temporales|serie temporal]]
Como la covarianza depende de la escala del proceso, conviene también definir:

> **Definición:** La función de autocorrelación (ACF) de un proceso o serie de tiempo $x_t$ se define como:$$ \rho(s,t) = \frac{\gamma(s,t)}{\sqrt{\gamma(s,s)\gamma(t,t)}}.$$

Nuevamente, mide la relación lineal de la serie en tiempo $t$ a partir de su valor en $s$.

Igual que antes, se cumple que $-1\leq \rho(s,t) \leq 1$ para todo $s,t$. Si $x_t = ax_s+b$, entonces $\rho$ es $1$ o $-1$ dependiendo del signo de $a$.

### Ejemplo: Media móvil
Volvamos al proceso de media móvil:

$$x_t = \frac{1}{3}[w_{t-2} + w_{t-1} + w_t]$$
del cual ya calculamos la covarianza. También sabemos que $\gamma(t,t) = 3/9 \sigma_w^2$ pues así lo calculamos para $s=t$. Por lo tanto:
$$\rho(s,t) = \left\{\begin{array}{ll} 1/3 & s=t-2 \\

2/3 & s=t-1 \\

1 & s=t \\

2/3 & s=t+1 \\

1/3 & s=t+2 \\

0 & \text{en otro caso} \end{array}\right..$$

Notar que nuevamente $\rho(s,t)$ solo depende de $t-s$ y a su vez ahora es independiente del tamaño del ruido.

## Correlación cruzada de series.
La idea aquí es definir la misma idea de correlación de una serie pero para relacionar una serie $\{x_t\}$ con otra $\{y_t\}$.

> **Definición:** La *covarianza cruzada* (o cross-covariance) de dos series se define como:$$ \gamma_{xy}(s,t) = E[(x_s - \mu_{xs})(y_t - \mu_{yt})].$$

> **Definición:** La *correlación cruzada* (o cross-correlation) de dos series se define como:$$ \rho_{xy}(s,t) = \frac{\gamma_{xy}(s,t)}{\sqrt{\gamma_x(s,s)\gamma_y(t,t)}}.$$

