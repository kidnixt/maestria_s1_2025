# ⚙️ Clase 7 – Entrenamiento y Evaluación Offline

---

## 🎯 Objetivo de la clase

- Comprender cómo entrenar modelos de forma eficiente en la práctica.
- Aprovechar al máximo los recursos de cómputo disponibles.
- Conocer técnicas de entrenamiento distribuido y evaluación offline.
- Aprender a trazar y versionar experimentos de ML.

---

## 🧠 Entrenamiento eficiente

### Problemas actuales

- El entrenamiento consume **cada vez más recursos**:
  - Más datos
  - Modelos más grandes
- Más tiempo implica más `epochs`, pero también más ineficiencia.

### Soluciones

- **Optimizar la ingesta de datos**:  
  - Usar formatos como `TFRecords`
  - Preprocesar embeddings si es Transfer Learning
  - Procesamiento paralelo durante el entrenamiento

- **Aprovechar la GPU al máximo**:  
  - Usar herramientas de monitoreo (`nvidia-smi`, AMD Monitor)
  - Entrenar con múltiples GPUs o nodos

![[Pasted image 20250607132825.png]]

---

## 🧱 Entrenamiento distribuido

### Objetivo:
Mantener GPUs y procesadores **ocupados todo el tiempo**.

### Tipos de paralelismo:

| Tipo                  | Descripción                                                  |
| --------------------- | ------------------------------------------------------------ |
| Paralelismo de datos  | Cada máquina entrena el mismo modelo con diferentes datos.   |
| Paralelismo de modelo | Se divide el modelo en partes, cada nodo entrena una parte.  |

> 💡 Paralelismo de modelo permite usar modelos más grandes, pero es más complejo.


![[Pasted image 20250607132937.png]]
---

## ⚙️ Estrategias de paralelismo con TensorFlow

| Estrategia                  | Descripción                                          |
|-----------------------------|------------------------------------------------------|
| `MirroredStrategy`          | Copia el modelo en múltiples GPUs locales           |
| `MultiWorkerMirroredStrategy` | Igual, pero entre varias máquinas                   |
| `TPUStrategy`               | Para entrenar en TPUs                                |
| `OneDeviceStrategy`         | Útil para debugging (una sola GPU o CPU)            |

---

## 🔁 Ciclo en paralelismo de datos

1. Cada GPU toma un fragmento del batch.
2. Cada GPU hace `forward` en paralelo.
3. Cada GPU hace `backpropagation`.
4. Se hace **all-reduce** para sincronizar gradientes.
5. Se actualizan los parámetros del modelo en conjunto.

> Todo el modelo se mantiene sincronizado entre GPUs.

---

## ⚠️ Desafíos del paralelismo

- **Scaling del learning rate**: requiere ajustes si el batch size cambia.
- **Sincronización**:
  - Sincrónico: más preciso, pero puede haber “stragglers”.
  - Asincrónico: más rápido, pero puede generar inconsistencia.

---

## 🧩 Paralelismo de modelo y pipeline

- Se divide el **modelo** entre varias máquinas.
- Cada máquina procesa una parte del modelo.
- Se puede combinar con paralelismo de datos.

### GPipe – Paralelismo por pipeline

- Divide el minibatch en **microbatches**.
- Cada nodo procesa un microbatch en una parte del modelo.
- Permite entrenar **redes gigantes** que no caben en una sola GPU.

---

## 🧠 Técnicas adicionales

- **Gradient accumulation**: divide un batch en mini-batches, suma gradientes y actualiza una vez.
- **Memory swapping**: mueve activaciones de GPU a RAM externa cuando no se usan.

---

## 🌐 Federated Learning

Entrenamiento colaborativo sin centralizar los datos.

- Cada cliente (ej. celular) entrena con sus datos locales.
- Solo se comparten los gradientes.
- Evita problemas de privacidad y reduce tráfico de datos.

![[Pasted image 20250607133100.png]]

---

## 📏 Evaluación offline

### Propósito

- Medir el rendimiento de un modelo **antes de ponerlo en producción**.
- Evaluar **robustez, fairness y generalización**.

---

## 🧪 Baselines

| Tipo                    | Ejemplo                          |
|-------------------------|----------------------------------|
| Aleatorio               | Output aleatorio                 |
| Regla heurística        | Predecir clase más común         |
| Modelos simples         | Logistic Regression              |
| Humano                  | Performance humano (si aplica)   |
| Sistema anterior        | Comparar contra solución previa  |

> ⚠️ “Bueno” no siempre significa “útil”.

---

## 🧬 Métodos de evaluación

### 1. Perturbaciones

- Agregar ruido o modificar inputs.
- Ej: ruido en audio, clipping de texto.
- Objetivo: ver si el modelo **resiste cambios leves**. Queremos ver si es robusto.

### 2. Invariantes

- Cambiar features irrelevantes: el output **debe mantenerse**. No debe ser propenso a sesgos o ser injusto con ciertos segmentos.
- Ej: cambiar nombre/género en un CV no debería afectar el screening o la decisión tomada por un modelo.

### 3. Expectativa direccional

- Cambios en el input deben provocar cambios lógicos en el output.
- Ej: más superficie → precio más alto (en modelos de casas)

---

## 🧮 Evaluación por segmentos

Evaluar desempeño **por subgrupo** (género, edad, clase, etc.)

| Modelo   | Clase mayoritaria | Clase minoritaria | Global |
|----------|-------------------|-------------------|--------|
| A        | 98%               | 80%               | 96.2%  |
| B        | 95%               | 95%               | 95.0%  |

> A puede parecer mejor globalmente, pero tiene un sesgo claro.

---

## 🧠 Paradoja de Simpson

Un modelo puede ser **peor en cada subgrupo**, pero mejor en el total.  
Ejemplo clásico: admisiones en UC Berkeley.

> Evaluar solo a nivel global puede ocultar **discriminación estructural**.

---

## 🧩 Identificando segmentos críticos

- Conocimiento del dominio
- Análisis manual de errores
- Herramientas como:
  - `Slice Finder`
  - Algoritmos de detección de subgrupos

---

## 📏 Calibración de modelos

Un modelo calibrado:  
> Si predice un 70% de probabilidad, acierta el 70% de las veces.

### Aplicaciones:
- Publicidad (CTR): predecir probabilidad de clic
- Recomendaciones: ranking de ítems

### Herramientas:
- `sklearn.calibration.CalibratedClassifierCV`
- Temperature scaling

---

## 🔍 Debug y trazabilidad en ML

### Desafíos:

- Los errores pueden no ser obvios (fallos silenciosos).
- Arreglos requieren reentrenamiento.
- Muchos componentes interdependientes.

---

## 🔁 ¿Qué trazar?

- Curvas de pérdida (loss)
- Métricas (accuracy, F1)
- Logs por muestra (input, predicción, etiqueta)
- Tiempo de entrenamiento
- Recursos usados
- Hiperparámetros y configuración

---

## 🧰 Herramientas para trazabilidad

- Open source: `TensorBoard`
- Comerciales: `Weights & Biases`, `Comet.ml`

---

## 📓 Notebooks y producción

- Buenísimos para experimentar, pero:
  - No suelen ir a producción.
  - Se recomienda convertirlos:
    - `nbconvert`: `.ipynb` → `.py`
    - `jupytext`: control de versiones
    - `nbdime`: comparaciones entre versiones

---

## 🧬 Versionado en ML

### ¿Qué versionar?

- **Código**: `Git`
- **Modelos entrenados**
- **Datos**
- **Parámetros e hiperparámetros**

### Herramientas

| Elemento      | Herramienta                     |
|---------------|---------------------------------|
| Datos         | `DVC`, `Git LFS`, `Pachyderm`   |
| Modelos       | `MLflow`, `Azure ML`, `Comet`   |

---

## 🧱 Registro y linaje de modelos

- **Linaje:** ¿Cómo llegamos a este modelo?
  - Dataset, código, parámetros, entorno
- **Model Registry:**
  - Repositorio central de modelos
  - Guarda artefactos, versiones, metadatos

