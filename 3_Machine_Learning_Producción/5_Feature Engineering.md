# 📚 Feature Engineering

Esta clase profundiza en el **Feature Engineering**, un paso crucial en el ciclo de vida de un proyecto de Machine Learning, que se enfoca en la transformación de los datos crudos en características (features) que los modelos de ML puedan entender y utilizar eficazmente.

## 🔄 Ciclo de Vida de un Proyecto de ML: Rol de Feature Engineering

Dentro del ciclo de vida de un proyecto de ML, el **Feature Engineering** está intrínsecamente ligado a la **Gestión de Datos** y al **Desarrollo del Modelo de ML (ML Model Development)**. Es el puente entre los datos tal como se recolectan y la forma en que el modelo los consume para aprender y hacer predicciones.

## 🕵️‍♀️ Feature Engineering: Manejo de Valores Faltantes

Los valores faltantes en un dataset son un desafío común y su manejo es una parte vital del Feature Engineering. Es crucial entender la naturaleza de estos datos faltantes:

* **No aleatoriamente (Missing Not At Random - MNAR):** La razón por la que un valor falta está directamente relacionada con el valor en sí mismo.
    * *Ejemplo:* Personas con salarios muy altos o muy bajos pueden ser menos propensas a revelarlos en una encuesta.
* **Aleatoriamente por segmento (Missing At Random - MAR):** Los valores faltantes afectan solo a un segmento de los datos.
    * *Ejemplo:* La edad de una persona puede faltar más frecuentemente en una encuesta si la persona pertenece a un género específico.
* **Totalmente aleatorio (Missing Completely At Random - MCAR):** No hay un patrón discernible detrás de los datos faltantes; son eliminados de forma puramente aleatoria. Esto es lo menos común, ya que generalmente hay una razón o mecanismo que explica la ausencia de datos.

### Estrategias para Valores Faltantes

Existen dos estrategias principales para manejar valores faltantes:

1.  **Eliminación:** Consiste en remover los datos que contienen valores faltantes.
    * **Por columna:** Eliminar una columna si tiene muchos valores faltantes.
        * *Riesgo:* Puede hacer que el modelo pierda señal y sea menos preciso.
    * **Por fila:** Eliminar filas completas con valores faltantes.
        * *Ventaja:* Puede funcionar bien si los datos faltantes son totalmente aleatorios o muy pocos, ya que no deberían afectar significativamente.
        * *Riesgo:* Si los datos faltantes son aleatorios por segmento, puede eliminar datos importantes o generar sesgos.

2.  **Imputación:** Rellenar los valores faltantes con un valor definido.
    * **Valores por defecto:** Como un string vacío para datos categóricos.
    * **Estadísticas descriptivas:** Promedio, mediana o moda de la columna correspondiente.
    * *Riesgos de la imputación:*
        * **Introducir sesgo:** Si el valor imputado no refleja la realidad.
        * **Agregar ruido:** Si el valor imputado no es preciso.
        * **Data leakage:** Si la imputación se hace usando estadísticas del conjunto de prueba (ver más adelante).

## 🛑 ¡Cuidado con los Outliers!

Es importante distinguir entre deshacerse de **entradas inválidas** y deshacerse de **datos válidos** que son *outliers*. Eliminar outliers válidos o aplicarles *clipping* (recortar valores extremos) puede hacer que un modelo trate casos diferentes de igual manera, perdiendo la capacidad de generalizar para esas situaciones.

* *Ejemplo:* Aplicar *clipping* a la edad de una madre puede hacer que un modelo trate igual a una madre de 45 años que a una de 50, cuando biológicamente son casos muy distintos.

## 🛠️ ¿Qué hace el Feature Engineering?

El Feature Engineering es el proceso de transformar datos crudos para crear **vectores de features** que un modelo de ML pueda utilizar. Esto implica:
* Mapear datos crudos a formatos numéricos o vectoriales.
* Convertir enteros a puntos flotantes o viceversa.
* Normalizar valores numéricos.
* Transformar strings y datos categóricos en vectores.
* Mapear datos de un espacio a otro.

### Ejemplos de Transformaciones Comunes

| Instancia        | Todo el dataset |
| :--------------- | :-------------- |
| Clipping         | Scaling         |
| Hashing encoding | OHE             |
| Binning          | Bucketizing     |
| Binary encoding  | Label encoding  |
| Quantization     | TF-IDF          |

## 🧠 Embeddings: Representación de Datos Aprendible

Los **Embeddings** son representaciones aprendibles de datos que mapean información de **alta cardinalidad** (como imágenes o texto) a un **espacio de menores dimensiones**.

* **Propósito:** Preservar la información relevante para la tarea de aprendizaje.
* **Beneficios:** Permiten usar datos complejos para tareas como clustering o clasificación.
* **Problema principal:** Pueden conllevar una pérdida de la representación original de los datos.
* **Ganancia:** Proporcionan información sobre la cercanía entre ítems y su contexto (ej. relaciones semánticas entre palabras).
    * *Ejemplo:* En el espacio de embeddings de palabras, la distancia vectorial entre "hombre" y "mujer" puede ser similar a la distancia entre "rey" y "reina", o entre verbos en diferentes tiempos ("caminando" y "caminó").

![[Pasted image 20250602184225.png]]

Los **Autoencoders** son un tipo de red neuronal utilizada para aprender representaciones (embeddings) de datos de forma no supervisada, comprimiendo la entrada en un "cuello de botella" (bottleneck) y luego reconstruyéndola.

![[Pasted image 20250602184240.png]]

## 🧺 Data Leakage (Filtración de Datos)

El **Data Leakage** ocurre cuando se utiliza información del conjunto de prueba (o validación) para entrenar el modelo, lo que resulta en una sobreestimación del rendimiento del modelo en datos nuevos. Es crucial entender la diferencia entre sesgos y data leakage.

### Causas Comunes de Data Leakage 

* **Splitting de datos correlacionados por tiempo:** Si los datos tienen una dependencia temporal (ej. series de tiempo), un splitting aleatorio puede exponer el modelo a información "futura" en el conjunto de entrenamiento.
* **Preprocesamiento global antes de splitting:** Realizar operaciones como *scaling* o normalización en todo el dataset antes de dividirlo en conjuntos de entrenamiento, validación y prueba, hace que las estadísticas de transformación (ej. media, desviación estándar) incluyan información del conjunto de prueba.
* **Imputación de datos con estadísticas de test:** Usar la media o mediana del conjunto de test para imputar valores faltantes en el conjunto de entrenamiento.
* **Mal manejo de datos duplicados previo a splitting:** Si hay datos duplicados y estos se reparten entre training y test, el modelo "ve" datos del test durante el entrenamiento.
* **Filtración de datos de grupos:** Si hay grupos o identificadores únicos en los datos, es importante asegurar que las divisiones se hagan a nivel de grupo para evitar que información de un grupo se filtre a través de los splits.
* **Features correlacionadas con el target:** Algunas features pueden estar demasiado correlacionadas con la variable objetivo de una manera que no se generalizará a datos nuevos.

### Atacando la Filtración (Mitigación)

* **Medir la correlación entre features y el target:** Investigar si alguna feature muestra una correlación inusualmente alta, ya que podría indicar un leakage.
* **Estudios de ablación:** Quitar una feature del modelo y observar el impacto en el rendimiento. Si una feature es excesivamente determinante, investiga si hay leakage.
* **Ídem para nuevas features:** Aplicar el mismo escrutinio a cualquier nueva feature que se incorpore.
* **Usar el test split únicamente para evaluación final:** La regla de oro es que el conjunto de test solo se utilice una única vez al final del proceso para una evaluación imparcial del modelo final.

## ⚠️ "Ojo": Advertencias sobre el Número de Features. 

Cuantas más features se utilizan en un modelo, mayores son los riesgos y complejidades:
* Más chances de **filtración de datos (data leakage)**.
* Puede causar **overfitting** (el modelo aprende el ruido de los datos de entrenamiento en lugar del patrón real).
* Mayor necesidad de **memoria y cómputo**.
* Mayor **latencia** en la inferencia.
* Aumento de la **dependencia de datos** y la **deuda técnica**.

Es fundamental considerar la **importancia de la feature y su capacidad de generalización**.

## 🧐 Selección de Features (Feature Selection)

La selección de features es el proceso de identificar el subconjunto más relevante de features en los datos de entrenamiento para construir un modelo.

![[Pasted image 20250602184355.png]]

### ¿Por qué es Necesaria la Selección de Features?

* **Reducir almacenamiento:** Menos datos para guardar.
* **Reducir input/output:** Menos datos que leer y escribir.
* **Minimizar costos de entrenamiento e inferencia:** Menos cálculos.
* **Reducir dependencias de datos:** Menos complejidad en el pipeline de datos.

### Tipos de Selección de Features

1.  **Supervisada:** Utiliza la variable objetivo (target) para seleccionar las features que más contribuyen al rendimiento del modelo.
2.  **No supervisada:** No utiliza el target; se enfoca en remover features correlacionadas o redundantes.

#### Métodos de Selección de Features Supervisada

* **Métodos de Filtro:** Evalúan cada feature individualmente sin entrenar un modelo. Son rápidos y se usan como primera pasada.
    * **Correlación entre features:** Detecta redundancia calculando la correlación entre variables independientes. Si dos features están altamente correlacionadas (ej. > 0.9), se puede conservar solo una.
    * **Correlación con el target:** Mide cómo se relaciona cada feature con la variable objetivo.
        * *Ejemplo:* Correlación de Pearson para regresión, o Chi-cuadrado/Mutual Information para clasificación.
    * **Selección de features univariada:** Aplica un test estadístico (ej. ANOVA, Chi-cuadrado) a cada variable y selecciona las más significativas.
* **Métodos Wrapper:** Entrenan múltiples modelos con diferentes combinaciones de features para elegir la mejor. Son más precisos pero más lentos.
    * **Forward Selection:** Empieza sin features y agrega una por una la que más mejora el rendimiento hasta que no haya más mejoras.
    * **Backward Elimination:** Empieza con todas las features y quita una por una la que menos impacta el rendimiento, hasta que quitar una empeora el modelo.
    * **Selección Recursiva de Features (RFE):** Usa un modelo (ej. regresión, árbol) para estimar la importancia de las features y elimina recursivamente la menos importante.
* **Métodos Embebidos:** La selección de features ocurre durante el entrenamiento del modelo, como parte de su mecanismo interno.
    * **Regularización L1 (Lasso):** Penaliza los coeficientes del modelo lineal, haciendo que algunos se vuelvan cero, lo que equivale a eliminar automáticamente esas features.
    * **Importancia de las features:** Algunos modelos basados en árboles (Random Forest, XGBoost) calculan automáticamente la importancia de cada feature (ej. cuántas veces se usa para dividir los datos). Este ranking puede usarse para seleccionar las más relevantes.

## 📊 Feature Importance y SHAP

### Generalización de la Importancia de Features 

* **Extensión (Global vs Local):** La importancia puede ser general para todo el modelo o específica para una predicción individual.
* **Distribución:** Cómo se distribuye la importancia entre las features.
* **Conocimiento de dominio:** Siempre es crucial revisar las variables junto con expertos del dominio.

### Herramientas para Entender la Importancia.

* La importancia en **árboles de decisión** (y ensambles como Random Forest) es rápida y simple de obtener.
* **SHAP (SHapley Additive exPlanations):** Una técnica que mide la contribución de cada feature a una predicción individual (`f(x)`) y también la contribución general de las features al modelo. Permite entender por qué un modelo hizo una predicción específica y cómo influyen las features en el comportamiento general del modelo.

## 📝 Resumen Clave: Consideraciones en Feature Engineering

* Generalmente requiere **conocimiento de dominio** y el input de expertos que no son ingenieros.
* Dividir los **datos temporales correctamente por tiempo** para evitar leakage.
* Aplicar **transformaciones basadas en estadísticas globales después de realizar el splitting** de los datos (ej. scaling, normalización).
* Asegurar el **linaje de datos y la trazabilidad** para saber de dónde provienen las features y cómo se transformaron.
* Entender la **importancia de las features y su capacidad de generalización**.
* **Quitar features que no son útiles** mejora el uso de recursos y previene problemas como overfitting o data leakage.
* El Feature Engineering es una **fuente común de bugs difíciles de detectar** en los sistemas de ML.

## ⚙️ Preprocesamiento de Datos

El preprocesamiento es el paso de **preparar los datos** para que sean útiles para entrenar modelos y para que los modelos puedan aprender de ellos eficazmente.

### Preprocesando Datos a Gran Escala

Para manejar terabytes de datos, se requieren:
* **Frameworks de procesamiento para grandes volúmenes de datos** (ej. Apache Spark, Google Dataflow, Apache Beam).
* **Reproducibilidad:** Es crucial asegurar que las transformaciones de datos puedan ser replicadas consistentemente. Esto implica manejar la aleatoriedad (semillas aleatorias, splitting repetible), el linaje de datos y evitar el *training-serving skew*.
* **Transformaciones consistentes entre training y serving:** Las mismas transformaciones deben aplicarse tanto en el entrenamiento como en la inferencia para evitar desviaciones (skew).

### Pipelines de ETL para Procesamiento de Datos

* **Batch (por lotes):**
    * **Extract (Extraer):** Datos de fuentes como CSVs, XML, JSON, APIs.
    * **Transform (Transformar):** Procesamiento de los datos.
    * **Load (Cargar):** Los datos transformados se cargan en un Data Lake o Data Warehouse (ej. BigQuery).
    * Resulta en un job de predicción/entrenamiento batch con los datos preparados.

![[Pasted image 20250602184747.png]]


* **Streaming (flujo continuo):**
    * **Extract (Extraer):** Datos de streaming en tiempo real (ej. Apache Kafka, Google Cloud Pub/Sub).
    * **Transform (Transformar):** Procesamiento en tiempo real.
    * **Load (Cargar):** Los datos transformados pueden ir a otra pipeline de streaming, Data Warehouse o Data Lake.
    * Consumido por pipelines de predicción/entrenamiento en tiempo real.

![[Pasted image 20250602184757.png]]

## 🚧 Training-Serving Skew (Sesgo de Entrenamiento-Servicio)

El *training-serving skew* ocurre cuando hay una **discrepancia en el rendimiento entre el modelo entrenado y el modelo en producción**, debido a diferencias en cómo se manejan los datos en las fases de entrenamiento e inferencia. Para evitarlo, las transformaciones sobre el input **deben ser las mismas** en ambas fases.

### Estrategias para Evitar el Skew 

* **Reuso de funciones:** Utilizar las mismas funciones de preprocesamiento tanto en entrenamiento como en inferencia.
    * *Ejemplo:* Preprocesar con Apache Beam y guardar las transformaciones de `tf.transform` como *artifacts* que luego pueden ser usadas en serving
* **Incluir el preprocesamiento dentro del modelo:**
    * En Keras, usar capas de preprocesamiento personalizadas (`custom preprocessing layers`).
    * En Scikit-learn, integrar el preprocesamiento en pipelines y transformadores.

## 🗺️ ¿Dónde y Cuándo Realizar el Preprocesamiento?

La decisión de dónde realizar el preprocesamiento (como parte de la pipeline de datos o dentro del modelo) implica un *trade-off* entre **eficiencia, experimentación, mantenibilidad y flexibilidad** 

| Etapa                                          | Pros                                                                              | Contras                                                                          |
| :--------------------------------------------- | :-------------------------------------------------------------------------------- | :------------------------------------------------------------------------------- |
| **Preprocesamiento (en la pipeline de datos)** | Corre una única vez, considera todo el dataset.                                   | Requiere reproducir las transformaciones en serving, iteración lenta.            |
| **Con el modelo (en capas del modelo)**        | Iteración fácil, garantías sobre las transformaciones (se aplican siempre igual). | Transformaciones más costosas, puede aumentar la latencia, riesgo de batch skew. |

**TF Transform** es una herramienta que facilita la consistencia de las transformaciones entre entrenamiento y serving, permitiendo aplicar las mismas lógicas definidas en el pipeline de Beam directamente en el grafo de TensorFlow para inferencia.

![[Pasted image 20250602185001.png]]

## 🗄️ Feature Store

Una **Feature Store** es una plataforma centralizada que facilita a los equipos de ML el **descubrimiento, uso y compartición** de features.

* **Beneficios Clave:**
    * Evita la **duplicación** de esfuerzos en Feature Engineering.
    * Permite **monitorear la calidad de los datos** de las features.
    * Gestiona **permisos de acceso**.
    * Facilita el cumplimiento de **regulaciones** (ej. GDPR).
    * **Precomputa y almacena features** en modo batch y en tiempo real.
    * Es **escalable y performante** tanto para entrenamiento *offline* como para inferencia *online*.

Una Feature Store actúa como un puente entre el Feature Engineering y el Desarrollo del Modelo, almacenando y sirviendo las features de manera consistente y eficiente.