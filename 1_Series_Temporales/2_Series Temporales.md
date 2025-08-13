## Definición
Una ***serie temporal*** es una sucesión de variables aleatorias o ***proceso estocástico*** $x_0, x_1, x_2, ...$. La variable aleatoria $x_t$ denota el valor del proceso al tiempo $t$.

- Al proceso estocástico o serie temporal se lo denota por {$x_t$}.
- Típicamente se consideran indexados por $t$ entero (..., -2, -1, 0, 1, 2, ...) o $t$ entero positivo.
- A los valores particulares que toma una serie temporal cuando la observamos se le denomina *realización* del proceso, y lo denotaremos por $x_t$ abusando un poco de la notación.

## Análisis y predicción de series temporales
- En general, una serie de datos que obtenemos $x_t$ podemos pensarla como la *realización* de algún proceso estocástico subyacente, el cuál desconocemos.
- Podemos entonces formular un **modelo** para la serie. Por ejemplo, [[autorregresivo (AR)]], [[autorregresivo-media móvil (ARMA)]], modelos en espacios de estados, etc.
- En base a los datos buscamos ajustar los parámetros de dicho modelo.
- Usando la estructura del modelo tratamos luego de extrapolar a futuro el comportamiento.
- La característica visual fundamental para distinguir diferentes series es el grado de smoothness. Una explicación de ese smoothness es que los puntos adyacentes en el tiempo estan correlacionados. Entonces el valor de la serie en un tiempo $t:\ x_t$, depende en alguna medida de valores pasados $x_{t-1}, x_{t-2}, ...$.   

## Observaciones básicas
- Normalmente **graficar** la evolución de una realización nos da pistas de cómo analizarla.
- A veces graficaremos como puntos, otras veces como líneas continuas, sobre todo cuando se trata de la realización de algún proceso continuo subyacente (por ejemplo, sismógrafo, sonido en los ejemplos de la introducción).
- En general series continuas de todos modos se *muestrean* a intervalos discretos, y al analizarlas en una computadora esto es *forzosamente* así.
- **Observación:** Elegir el intervalo de muestreo es un arte en sí mismo para capturar la dinámica de la serie.
	- Si queremos observar fenómenos que varían mensualmente, tomar muestras trimestrales no alcanza. Necesitamos mirar "más rápido".
	- Quizás tampoco es necesario tomar muestras diarias que introducen fenómenos extra.
	- **Resumen:** Hay que elegir cuidadosamente la escala.

[[2.1_Ejemplos Series Temporales]]