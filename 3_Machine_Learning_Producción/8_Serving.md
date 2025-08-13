# 🚀 Clase 8 – Serving de Modelos


---

## 🎯 Objetivo de la clase

- Entender cómo **llevar modelos a producción** y exponer predicciones.
- Comparar estrategias de despliegue: batch, online, streaming.
- Explorar trade-offs entre latencia, throughput y costo.
- Conocer herramientas para empaquetar y servir modelos (Docker, servicios gestionados, etc).

---

## 🔧 Etapas para poner un modelo en producción

1. Entrenamiento del modelo.
2. Elección del entorno de ejecución.
3. Empaquetado (container, web service, etc).
4. **Despliegue (serving)**.
5. Exposición de predicciones a otros sistemas.
6. Monitoreo y mantenimiento.

---

## 🧭 Opciones de despliegue

- **On-premise:** infraestructura propia.
- **Cloud:** servicios como AWS, GCP, Azure.
- **Edge:** dispositivos físicos (celulares, sensores).

> Ej: Un modelo de detección de defectos puede correr en una fábrica (*edge*) sin depender de conexión a la nube.

---

## 🌐 Web app vs API

- Web app: muestra resultados al usuario final.
- **API/servidor de modelo:** expone un endpoint que recibe inputs y devuelve predicciones.

> Ej: `/predict` → recibe JSON, responde JSON con resultados.

---

## 🧱 Sistemas de despliegue

Sistemas que:

- Centralizan el despliegue de modelos.
- Exponen endpoints para hacer predicciones.
- No requieren crear aplicaciones específicas.
- Permiten actualizar o revertir modelos fácilmente.
- Automatizan escalabilidad y versionado.

> 📦 Ejemplos: TensorFlow Serving, TorchServe, SageMaker endpoints

---

## 🏗️ Arquitecturas de despliegue

### VM tradicional

- Sistema operativo completo, configuración manual.
- Más pesado y menos portátil.

### Contenedores (Docker)

- Livianos y replicables.
- Permiten definir exactamente:
  - SO base
  - Dependencias (paquetes)
  - Variables de entorno
  - Modelo y código de inferencia
  - Comandos a ejecutar

> 📌 Ideal para despliegue cloud o CI/CD

![[Pasted image 20250605212540.png]]

---

## 🐳 Docker: empaquetar la solución

- Define en un `Dockerfile` los pasos para construir la imagen.
- Facilita compartir modelos en cualquier entorno.
- Compatible con servicios cloud, edge o locales.

> Ej: montar un servicio Flask + modelo en Docker y subirlo a AWS.

---

## ☁️ Servicios gestionados (MaaS)

Plataformas cloud nativas:

- Ej: **Vertex AI (GCP)**, **SageMaker (AWS)**

Características:

- Predicciones **de baja latencia**
- Batching dinámico
- Escalado automático
- Uso de GPU/TPU si es necesario
- Alta disponibilidad

---

## 📈 Métricas clave en serving

| Métrica     | Descripción                            |
|-------------|----------------------------------------|
| **Latencia**| Tiempo de respuesta de la predicción   |
| **Throughput**| Cantidad de requests por segundo     |
| **Costo**   | Uso de recursos (CPU, RAM, transferencia) |

> 🔁 Siempre hay que balancear: más throughput o menos latencia = más costo.

---

## ⚖️ Optimización de recursos

Estrategias para reducir costos:

- **Compartir GPU** entre múltiples modelos.
- Servir varios modelos en el mismo contenedor.
- Optimizar los modelos para inferencia (quantization, pruning).
- Usar modelos especializados para tareas frecuentes.

---

## 🔮 Tipos de predicción

### Predicciones Batch

- Predicciones en bloque, en momentos programados (ej. cada 4h).
- Datos generalmente históricos.
- Permite usar modelos más pesados.

> Ej: generar recomendaciones nocturnas para todos los usuarios.

![[Pasted image 20250605212903.png]]

---

### Predicciones Online

- Respuesta inmediata ante cada request.
- Debe garantizar baja latencia.
- Usa features precomputadas (batch) o en tiempo real (streaming).

> Ej: recomendar productos en una app en tiempo real.

---

### Predicciones con Streaming Features

- Usa features generadas a medida que ocurren eventos.
- Ej: clics recientes, ubicación en vivo.

> ⚙️ Requiere infraestructura robusta (ej: Kafka, Pub/Sub).

---

## 🧮 Comparación: Batch vs Online

| Aspecto             | Batch (asíncrono)               | Online (síncrono)               |
|---------------------|----------------------------------|---------------------------------|
| Frecuencia          | Programada (cada X horas)        | Tiempo real                     |
| Uso típico          | Análisis agregados, scoring masivo| Personalización, tiempo real   |
| Requiere cache de features | No                         | A veces sí                      |
| Optimizado para     | Throughput                       | Latencia                        |

---

## ❄️ Cold start problem

Problema: no hay información del usuario nuevo.

- Común en sistemas de recomendación.
- Soluciones:
  - Usar datos poblacionales (ubicación, edad)
  - Preguntar preferencias iniciales
  - Recomendaciones populares

---

## 🔀 Desafíos del streaming en serving

- Entrenar modelos con features que **no existían** al momento del entrenamiento.
- Alinear tiempos entre datos de entrada y features.
- Garantizar consistencia entre entornos de entrenamiento e inferencia.

---

## 🔧 Pipeline de predicciones online

1. Request entra con datos del usuario.
2. Se consultan features batch y streaming.
3. Se combinan y se arman inputs del modelo.
4. El modelo devuelve predicción.
5. Se registra o actúa según resultado.

---

## ⚙️ Beam y pipelines mixtos

Ejemplo: Google Cloud Dataflow / Apache Beam

- Permiten definir pipelines que manejan tanto batch como streaming.
- Útiles para feature engineering unificado y consistente.

