# 📚 Muestreo y Repaso

Esta clase se enfoca en la importancia crítica de los datos en el ciclo de vida de un proyecto de Machine Learning, profundizando en cómo el **muestreo adecuado** y la gestión de **sesgos** son fundamentales para el éxito en producción.

## 🔄 Ciclo de Vida de un Proyecto de ML y Gestión de Datos

El diagrama del ciclo de vida de un proyecto de ML destaca la **Gestión de Datos** como un componente central. Esta gestión es crucial en todas las etapas, desde la definición del alcance del proyecto (Project Scoping) hasta el despliegue (Deployment) y el monitoreo y mantenimiento del modelo (Monitoring & Maintenance).
![[Pasted image 20250717051726.png]]

## 📊 Datos de Entrenamiento: Fundamento Crítico

La preparación de datos es esencial para el **Data Science**. Los datos en la realidad suelen ser complejos y son el factor determinante en cualquier operación de ML. Es vital tener **"ojo con los datos"** porque pueden estar potencialmente sesgados debido a cómo se recolectan, el método de muestreo utilizado, la forma en que fueron etiquetados o porque la realidad misma cambia.

## 🧪 Muestreo: Una Clave en el Flujo de Trabajo de ML

El muestreo es una parte clave del flujo de trabajo de ML, fundamental para:
* Obtener datos de entrenamiento.
* Validar Pruebas de Concepto (PoC).
* Crear divisiones de datos para entrenamiento, validación y prueba (`train/val/test splits`).
* Monitorear los eventos del sistema de ML.

Comprender y aplicar correctamente las diferentes técnicas de muestreo ayuda a **evitar sesgos** en los modelos.

### Tipos de Muestreo

Existen dos categorías principales de muestreo:
* **No probabilístico**
* **Aleatorio**

### Muestreo No Probabilístico

Este tipo de muestreo se caracteriza por estar **"lleno de sesgos"** y, en general, **NO es bueno para ML** en producción debido a su falta de confiabilidad. Sin embargo, puede ser una manera rápida y fácil de experimentar.

* **Por conveniencia:** Se basa en la disponibilidad de los datos.
* **Snowball (bola de nieve):** Se parte de muestras ya obtenidas para conseguir más a partir de ellas (ejemplo: web scraping).
* **Juicio de Expertos:** Se basa en la opinión o selección de expertos en el dominio.
* **Cuotas:** Se basa en establecer cuotas por segmento de los datos (ejemplo: encuestas separadas por franja etaria o nivel socio-económico).

### Muestreo Aleatorio

* **Pros:** Es más fácil de implementar.
* **Contras:** Se complica cuando hay categorías raras o poco frecuentes en los datos.

### Muestreo Estratificado

Consiste en obtener muestras por **grupo de datos (estrato)**.
* Asegura que todas las clases sean muestreadas
* Se puede obtener en la misma proporción para representar la población original.
* Se complica con problemas de *multi-label*.

![[Pasted image 20250530132207.png]]

### Muestreo Ponderado

A cada muestra se le asigna una **probabilidad de ser seleccionada**.
* Permite definir de antemano una probabilidad por clase (ejemplo: dar mayor peso a datos más recientes si se consideran más importantes).
* Aprovecha el conocimiento del dominio para ajustar la distribución de los datos si no está alineada con la realidad, cambiando los pesos asignados.

### Reservoir Sampling

Particularmente útil para **datos de streaming** (flujo continuo de datos) y cuando existen limitaciones de memoria (ejemplo: procesamiento de tweets).
* **Objetivo:** Asegurar que cada ítem tenga la misma probabilidad de ser seleccionado.
* Se mantiene una premisa o aserción invariante en todo momento.
* **Algoritmo R (solución con array de reserva):**
    * Los primeros `K` ítems van directamente a un array de reserva.
    * Para cada nuevo elemento `i` (donde `i > K`), se genera un número aleatorio `j` entre 1 e `i`.
    * Si `j < K`, el elemento `j` en la reserva es reemplazado por el nuevo elemento; de lo contrario, el nuevo elemento se descarta.
* Esto implica que cada elemento `n` tiene una probabilidad de `k/n` de entrar en la reserva.

![[Pasted image 20250530132335.png]]

## 📉 Datos No Balanceados

Los modelos de Machine Learning funcionan mejor con **distribuciones de datos balanceadas**. Las distribuciones desequilibradas, donde algunas clases tienen muchos más ejemplos que otras, complican el rendimiento del modelo.

![[Pasted image 20250530132351.png]]

### Formas de Resolver Problemas con Datos No Balanceados

Existen tres enfoques principales para abordar los datos no balanceados:
1.  Eligiendo las **métricas correctas**.
2.  Atacando la **distribución de los datos**.
3.  Utilizando **métodos a nivel de algoritmo**.

#### 1. Eligiendo las Métricas Correctas

Cuando los datos están desbalanceados, la métrica de **Accuracy (precisión general)** puede ser engañosa. Un modelo puede tener una alta precisión simplemente por predecir la clase mayoritaria.

![[Pasted image 20250530132502.png]]

Es fundamental utilizar métricas más adecuadas como:
* **Precision**.
* **Recall**.
* **F1-Score** (una media armónica de Precision y Recall).
* **AUC (Area Under the ROC Curve)**. Un valor de AUC más cercano a 1.0 indica un mejor rendimiento del modelo, siendo 0.5 similar a una predicción aleatoria.
![[Pasted image 20250530132519.png]]
#### 2. Atacando la Distribución de Datos


Se busca modificar el dataset para balancear las clases:
* **Undersampling:** Reduce el número de muestras de la clase mayoritaria.
* **Oversampling:** Aumenta el número de muestras de la clase minoritaria, a menudo mediante la replicación o generación sintética de datos..
    * Una biblioteca recomendada para esto es `imbalanced-learn`.

![[Pasted image 20250530132705.png]]


* **Entrenamiento en dos etapas (para Deep Learning):**
    * Primero, se entrena el modelo con datos re-muestreados (por ejemplo, haciendo undersampling de las clases mayoritarias hasta balancear el dataset).
    * Luego, se realiza un *fine-tuning* con el dataset original completo.
* **Muestreo dinámico durante el entrenamiento:**
    * Realiza oversampling de las clases con menor rendimiento.
    * Realiza undersampling de las clases con mejor rendimiento.

#### 3. Métodos a Nivel de Algoritmo

En lugar de cambiar la distribución de los datos, se modifica cómo el modelo los aprende:
* **Aprendizaje sensible al costo (Cost-sensitive learning):** Se asigna un costo mayor a clasificar mal ciertas clases (ejemplo: un falso negativo de cáncer es más costoso que un falso positivo).
* **Loss ponderada por clase:** Se pondera el impacto en la función de pérdida (loss) según la relación entre la cantidad de datos por clase.
* **Loss enfocada (Focal Loss):** Se da mayor impacto en la loss si la confianza en la predicción es menor, lo que ayuda a que el modelo se enfoque en ejemplos difíciles o mal clasificados.

## 🎯 Sesgo en ML

El **sesgo** es diferente a un dataset no balanceado, aunque pueden estar relacionados.

### Fuentes de Sesgo

* **Sesgo de selección:** Ocurre cuando se excluyen ciertas categorías o se entrena con datos que no representan la diversidad del entorno de producción (ejemplo: entrenar con imágenes de un catálogo propio y no considerar las de socios). A menudo, algunas clases son más fáciles de recolectar que otras.
* **Sesgo de medición:** Sucede cuando la calidad o las características de los datos de entrenamiento difieren de los datos en producción (ejemplo: entrenar con fotos de alta resolución y usar fotos de menor calidad en producción). También puede ocurrir si el contexto de las imágenes o datos influye en la etiqueta en lugar de la característica principal (ejemplo: zorros siempre con nieve y perros con pasto, llevando al modelo a centrarse en el fondo).
* **Sesgo de confirmación:** Es más complejo y amplifica los sesgos sociales ya presentes en el dataset.

### Detección de Sesgo

Para detectar el sesgo, es fundamental **evaluar el modelo sobre "slices" (segmentos) de los datos**, comparando el rendimiento entre diferentes grupos.

