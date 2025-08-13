# 🧠 Elección y Ajuste de Modelos en Producción

## 🔄 Ciclo de Vida de un Proyecto de ML: Rol de la Elección del Modelo

 En el ciclo de vida de un proyecto de ML, la **Elección del Modelo (ML Model Development)** es una etapa central que interactúa con la **Gestión de Datos (Data Management)**, el **Análisis de Negocio (Business Analysis)** y el **Despliegue (Deployment)**. La decisión sobre qué modelo usar impacta directamente en cómo se gestionan los datos, cómo se mide el éxito de negocio y cómo se pone en producción.
![[Pasted image 20250717065209.png]]
   
---

## 🤔 ¿Cómo elegir un modelo?

Aunque el hype actual está en los modelos grandes (redes neuronales profundas, LLMs, etc.), no siempre son la mejor opción para producción.
### Ejemplos de tareas y modelos frecuentes:

| Tarea                         | Modelos comunes                                       |
|------------------------------|-------------------------------------------------------|
| Recomendación                 | Collaborative Filtering, Matrix Factorization         |
| Clasificación                 | Árboles, Random Forest, Gradient Boosted Trees        |
| Análisis de texto             | BERT, GPT, pero también Naive Bayes o Logistic Reg.   |
| Detección de fraude/anomalías| KNN, Isolation Forest, Clustering, NNs                |

> 📌 Consejo: empezá con algo simple que funcione, después iterás.

---

## 🧭 Enmarcando el Problema: Regresión vs. Clasificación

La forma en que se enmarca el problema define el tipo de modelo a usar. Por ejemplo, la predicción de precipitaciones puede ser abordada de dos maneras:
* **Regresión:** Si se predice la cantidad exacta de precipitación (ej., 1.7 mm).
* **Clasificación multiclase:** Si se predice la probabilidad de que la precipitación caiga dentro de rangos específicos (ej., 85% de probabilidad de que sea entre 1.5-2.0 mm)

> 🔄 El framing del problema **cambia el tipo de modelo**, la métrica, y la arquitectura.

---

## 🔠 Tipos de clasificación

| Tipo              | Descripción                                           | Ejemplo                                |
|-------------------|-------------------------------------------------------|----------------------------------------|
| Clasificación binaria | 2 clases posibles                                 | ¿Spam o no spam?                       |
| Clasificación multiclase | Una clase entre varias posibles              | Tipo de prenda: camiseta, jeans, buzo |
| Clasificación multilabel | Múltiples etiquetas por muestra              | Imagen con “playa” y “atardecer”       |

---

## 🧩 Factores a considerar al elegir un modelo

- ¿Cuántos datos tengo?
- ¿Cuánto cómputo disponible?
- ¿Cuánta latencia tolera la aplicación?
- ¿Necesito interpretabilidad?
- ¿Se puede mantener en producción?

* **Consideraciones clave:**
    * **Costos de entrenamiento:** En algunos casos, un modelo más complejo puede ser muy costoso de entrenar. 
    * **Latencia de inferencia:** Un modelo más lento en producción puede impactar negativamente la experiencia del usuario.
    * **Interpretabilidad:** En áreas como la medicina o las finanzas, entender por qué el modelo toma una decisión es tan importante como la decisión misma.

> Ejemplo: en un sistema médico se prioriza **interpretabilidad**; en una app de recomendaciones, **latencia**.

---

## ✅ Tips para elegir el modelo

- Evitá elegir solo por el SOTA (*state-of-the-art*).
- Empezá simple y escale gradualmente.
- Compará modelos bajo las mismas condiciones.
- Considerá:
  - FP vs FN
  - Accuracy vs recursos
  - Interpretabilidad vs performance
- Entendé qué **supuestos** hace el modelo sobre los datos.

---

## 🔁 Transfer Learning

Usar un modelo preentrenado en una tarea relacionada y ajustarlo a tu problema.  
> Ej: usar BERT entrenado en Wikipedia para clasificar tweets.

---

## 🎯 Entrenamiento: Overfitting y Regularización

- El modelo **debe poder overfittear** (memorizar) al dataset antes de poder generalizar.
- Si no puede hacerlo, necesita **más capacidad**.
- Una vez que overfittea, usamos regularización:

### Métodos de regularización:
- Penalización L1/L2
- Dropout
- Data augmentation
- Batch Normalization

> 🎯 Regularizar = evitar que el modelo aprenda ruido

---

## 🛑 Guardar estado del modelo: Early Stopping

- Cortar el entrenamiento cuando el rendimiento en validación deja de mejorar.
- Previene overfitting y ahorra recursos.

---

## 🧮 Parámetros vs Hiperparámetros

| Tipo               | ¿Se entrena? | Ejemplos                                 |
|--------------------|--------------|------------------------------------------|
| Parámetros         | ✅           | Pesos de una red neuronal                |
| Hiperparámetros    | ❌ (se fijan antes) | Learning rate, capas ocultas, batch size |

---

## 🔧 Ajuste de Hiperparámetros

- Puede ser complejo incluso en modelos chicos.
- Algunos hiperparámetros comunes:
  - Número de capas/neuronas
  - Función de activación
  - Learning rate
  - Criterios de parada

### Métodos de ajuste:

| Método          | Descripción                                     |
|-----------------|-------------------------------------------------|
| Grid search     | Recorre todas las combinaciones posibles        |
| Random search   | Toma combinaciones aleatorias                   |
| Bayesian opt.   | Usa modelos probabilísticos para elegir mejor   |

> 📦 Frameworks útiles: `Auto-sklearn`, `Optuna`, `Keras Tuner`

---

## 🔁 Ensembles

### ¿Qué es?

Combinar **varios modelos** para obtener mejores predicciones.

### Ejemplo:

- Si tres modelos predicen con 70% de precisión, la combinación puede aumentar la certeza global.

### Tipos:

| Técnica  | Descripción                                         |
| -------- | --------------------------------------------------- |
| Bagging  | Entrenar modelos sobre subconjuntos aleatorios      |
| Boosting | Entrenar modelos secuenciales para corregir errores |
| Stacking | Un meta-modelo combina las salidas de otros modelos |

> 📌 Ensembles mejoran performance pero:
- Son difíciles de servir en producción
- Requieren más cómputo
- Reducen interpretabilidad

---

## 🤖 AutoML

> ¿Podemos automatizar todo el pipeline de ML?

- Visión: **modelo = datos + cómputo**, sin intervención humana.
- Aún es costoso, pero avanza rápido.
- Herramientas: `AutoML`, `AutoKeras`, `TPOT`

---

## 🧬 NAS – Neural Architecture Search

- Técnica que busca automáticamente la mejor arquitectura para una tarea.
- Define un **espacio de búsqueda** y prueba múltiples configuraciones.

> Ejemplo: elegir cuántas capas, activaciones, conexiones para una red.

---
