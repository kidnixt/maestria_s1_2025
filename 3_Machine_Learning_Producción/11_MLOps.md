# 🛠️ Clase 11 – Introducción a MLOps

---

## 🎯 ¿Qué es MLOps?

**MLOps** es la práctica de integrar **Machine Learning con DevOps**, buscando:

- **Estandarización** del ciclo de vida de ML  
- **Automatización** de procesos: entrenamiento, validación, despliegue  
- **Organización efectiva** de los equipos y herramientas para ML

---

## 🧱 Capas de Infraestructura en ML

MLOps abarca múltiples capas:

1. **Infraestructura (Cloud, hardware, containers)**
2. **Ambientes de desarrollo**
3. **Orquestación de workflows**
4. **Gestión de features**
5. **Registro y monitoreo de modelos**
6. **CI/CD aplicado a ML**

![[Pasted image 20250619192605.png]]

---

## ☁️ Herramientas clave en ML Engineering

### Cloud Computing

| Categoría                | Ejemplos                                 |
|--------------------------|------------------------------------------|
| Plataformas cloud        | AWS, GCP, Azure                          |
| Containers               | Docker, Kubernetes, Container Registry   |
| Serverless               | AWS Lambda, Cloud Functions              |
| Hardware especializado   | GPUs, TPUs                               |
| Big Data                 | Databricks, Spark, BigQuery, Snowflake   |

---

## 💻 Ambientes de desarrollo

| Elemento   | Ejemplos                                                                                     |
| ---------- | -------------------------------------------------------------------------------------------- |
| IDEs       | VSCode, Vim                                                                                  |
| Notebooks  | Jupyter, Google Colab                                                                        |
| Versionado | Código: `git` <br>Datos: `DVC`, `Git LFS` <br>Experimentos: `Weights & Biases`, `Comet` <br> |
| CI/CD      | GitHub Actions, Jenkins, Code Build                                                          |

## Ambientes de desarrollo Estandarizado

> Estándarizar el entorno (dependencias, datos, herramientas) mejora el soporte, seguridad y portabilidad.

### Ventajas 
- Los proyectos son más fáciles de matener.
- La rotación de personas es más rápida.
- Los proyectos pueden llegar más rápido de desarrollo a producción.

### Desventajas
- Tiene un costo mayor inicialmente
- Estás pagando deuda técnica (generalmente es bueno)



---

## 📦 Contenerización con Docker

- Define paso a paso:
  - SO base, paquetes, variables de entorno, artefactos, comandos.
- Permite reproducibilidad entre desarrollo y producción.

> ¿Querés correrlo periódicamente o con varios módulos? → necesitás **orquestación**.

---

## ⚙️ Orquestadores de tareas

### ● Schedulers
- `cron` (local o en GCP/AWS)
- Agenda tareas repetitivas en tiempos predefinidos (ej: todos los días a las 3 AM)
- ❓ ¿Cómo se define cuántos recursos asignar a una tarea?  
  → Con cron se complica
- ❓ ¿Qué pasa si las tareas están interrelacionadas o tienen dependencias entre sí?  
  → Se puede definir un **DAG**

### ● Gestión de carga de trabajo
- Algunos schedulers permiten definir cuánta carga le corresponde a cada tarea
- ❓ ¿Qué pasa si las tareas corren en diferentes plataformas o aplicaciones?

### ● Orquestadores
- Ejemplos: `Airflow`, `Argo`, `Prefect`, `Kubeflow`
- ❓ ¿Qué pasa si es una data pipeline?  
  → No confundir orquestadores de tareas con data pipelines

> No confundir *task orchestrator* con *data pipeline* (aunque se solapan).

---

## 🐳 Orquestación de containers

Kubernetes (u otros orquestadores):

- Escala contenedores automáticamente
- Los distribuye entre nodos
- Monitorea su estado
- Controla recursos y recuperación

---

## 🧪 Workflows de ML: herramientas


| Herramienta | Uso principal                       |
| ----------- | ----------------------------------- |
| TFX         | Pipelines en TensorFlow             |
| MLflow      | Seguimiento, registro y despliegue  |
| Metaflow    | Simplicidad en flujos de datos + ML |
| Kubeflow    | ML sobre Kubernetes                 |

**Kubeflow** permite desplegar flujos de trabajo de ML en Kubernetes de manera simple, portable y escalable. Donde sea que tengas un cluster de kubernetes corriendo, podés correr Kubeflow. Entonces puede correr on-premise o en la nube.


---

## 🧱 Componentes de una plataforma ML

- **Feature store**
- **Registro de modelos**
- **Despliegue de modelos**
- **Monitoreo de rendimiento**

---

## 🧠 Feature Store

Centraliza y estandariza la gestión de features:

- Descubrimiento y documentación
	- **Cómputo de features:** Precomputar las features para tenerlas prontas al momento de realizar predicciones, data warehouse.
- **Consistencia:** Unifican la lógica para las features que se computan de manera batch y de streaming.
- Validación y monitoreo
- Ejemplos: Feast, Vertex AI, SageMaker, Databricks

---

## 📋 Registro de modelos (Model Registry)

- Más que guardar archivos `.pkl` o `.h5`
- También incluye:
  - Fecha y versión
  - Hiperparámetros
  - Features usadas
  - Métricas
  - Código fuente
  - Dependencias
  - Responsables

> Herramientas: MLflow, Comet, W&B, Vertex AI, SageMaker

---

## 🌍 Despliegue de modelos

Pasos típicos:

1. Empaquetar modelo + dependencias
2. Crear imagen/container (desplegar)
3. Exponer vía API
4. Escalar y monitorear

> Herramientas: SageMaker, Vertex AI, MLflow, Seldon, Ray Serve, Cortex

---

## ⚙️ DevOps + MLOps

### DevOps clásico

- **CI**: construir, testear y empaquetar automáticamente  
- **CD**: desplegar código en producción  

### Infraestructura como código (IaC)

- Automatiza la creación del entorno
- Ej: Terraform, Pulumi

---

## 🔁 CI/CD en ML

| Fase                        | Descripción                                                                                                       |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| CI (Continuous Integration) | Al pushear código → tests, construcción de paquetes, imágenes. Despliega los paquetes finales a la pipeline de CD |
| CD (Continuous Delivery)    | Despliegue automático al entorno de destino, con validaciones                                                     |

---

## 🚀 Delivery progresivo

Despliegue **en etapas**, útil para reducir riesgos:

1. Desplegar a usuarios internos o de bajo riesgo
2. Escalar gradualmente a toda la base de usuarios

![[Pasted image 20250619194027.png]]

Estrategias:  
- **Shadow deployment**  
- **Canary release**  
- **A/B testing**  
- **Multi-armed bandits**

---

### 📥 Shadow

- Corre modelo nuevo en paralelo al viejo
- No impacta decisiones reales
- Útil para validación silenciosa

![[Pasted image 20250619194054.png]]

---

### 🐦 Canary

- Despliega nuevo modelo a un pequeño % de usuarios
- Si va bien, se expande gradualmente

![[Pasted image 20250619194104.png]]

---

### 🧪 A/B Testing

- División aleatoria de usuarios en dos grupos
- Comparar métricas de negocio entre versiones

![[Pasted image 20250619194140.png]]

---

### 🎰 Multi-armed Bandits

- Rutea dinámicamente las requests al mejor modelo
- Al final, todas las requests van para un solo modelo.
- Aprende durante la prueba
- Minimiza exposición a modelos subóptimos

---

### 🧠 Contextual Bandits

- Similar a MAB, pero considera **contexto**
  - Ej: ubicación, horario, perfil del usuario

---

## 🎚️ Niveles de madurez de MLOps (según Google)

El **nivel de automatización** de los pasos del ciclo de vida de ML define el grado de madurez de un sistema de MLOps.

---

### 🔁 Pasos en el proceso de Data Science para ML

#### 1. 🗂️ Extracción de datos
- Seleccionar e integrar datos relevantes desde diversas fuentes.

#### 2. 📊 Análisis de datos
- Realizar EDA (Análisis Exploratorio) para comprender la estructura, distribución y calidad de los datos.

#### 3. 🧹 Preparación de datos
- Limpieza, transformación, normalización, división en sets (train/test/val), etc.

#### 4. 🧠 Entrenamiento del modelo
- Implementar algoritmos, entrenar modelos y ajustar hiperparámetros.

#### 5. 🧪 Evaluación del modelo
- Usar un conjunto de prueba y calcular métricas (accuracy, F1, AUC, etc).

#### 6. ✅ Validación del modelo
- Verificar si el modelo es adecuado para producción (bias, overfitting, performance real).

#### 7. 🚀 Implementación del modelo
- Desplegar el modelo en un entorno destino para servir predicciones.

#### 8. 📉 Monitoreo del modelo
- Observar rendimiento, detectar drift o degradación, y reentrenar si es necesario.

---

> ⚙️ **Nota:**  
> Cuanto más automatizados estén estos pasos, mayor será la madurez del sistema de MLOps.

| Nivel | Descripción                                           |
|-------|--------------------------------------------------------|
| 0     | Manual: scripts aislados, sin trazabilidad             |
| 1     | Automación parcial: CI/CD para entrenamiento y testeo |
| 2     | CI/CD/CT completo: deploy automático + monitoreo       |
**Nivel 0**
![[Pasted image 20250717074125.png]]

**Nivel 1**
![[Pasted image 20250717074139.png]]

**Nivel 2**
![[Pasted image 20250717074154.png]]

---
## 🧬 Pipeline de CI/CD automatizado

Pasos clave:

1. Extracción y análisis de datos
2. Preprocesamiento
3. Entrenamiento
4. Evaluación y validación
5. Despliegue automático
6. Monitoreo
7. Reentrenamiento si es necesario

---

## 🧱 Recomendaciones para implementar MLOps

- Empezar chico (regla 80/20)
- Automatizar desde el inicio
- Usar la nube (sin reinventar la rueda)
- Priorizar la solución de negocio sobre la sofisticación
- Mejora continua de la pipeline
- Tomar en serio la **gobernanza de datos** y la **seguridad**

---

## 🛡️ Gobernanza y Ciberseguridad

- Principio del menor privilegio (PLP)
- Encriptar datos sensibles
- Automatizar = más seguro
- Usar la seguridad nativa de la cloud
- Entrenar a los equipos
- Adoptar una cultura de seguridad

---
