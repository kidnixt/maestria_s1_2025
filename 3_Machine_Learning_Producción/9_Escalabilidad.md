# 📈 Clase 9 – Escalabilidad de Modelos de ML

---

## 🎯 Objetivo de la clase

- Entender los desafíos de **servir modelos a gran escala**.
- Analizar estrategias de **escalado horizontal y vertical**.
- Aprender técnicas para **reducir el tamaño y costo de inferencia**.
- Conocer herramientas y prácticas para el **despliegue en dispositivos Edge**.

---

## 🏗️ Requerimientos de infraestructura en sistemas ML reales

- Hasta **63.000 requests/segundo**
- TBs de datos por día
- Equipos de 10s–100s de data scientists
- Múltiples modelos en paralelo
![[Pasted image 20250605210602.png]]

---

## ☁️ Nube pública vs Data centers privados

![[Pasted image 20250605210616.png]]

### Nube pública

**Pros**:
- Fácil de empezar
- Escala automáticamente
- Pago por uso (on-demand)

**Contras**:
- Puede volverse **muy costoso** a gran escala


---

## 📊 Escalabilidad

### Horizontal (scale-out)

- Agregar más máquinas (CPUs/GPUs)
- Elástico: se ajusta según carga
	- Cuando se precisa
	- Permite reducir la infraestructura al mínimo
- Escala virtualmente ilimitada (en cloud)
	- No hay límite de capacidad. A medida que es necesario se agregan nodos a un costo adicional


### Vertical (scale-up)

- Mejorar hardware existente
  - Más RAM, mejor GPU/CPU
  - Almacenamiento más rápido

> ⚠️ Tiene un límite físico/económico.

---

## ⚙️ Hardware: CPU vs GPU vs TPU

- **CPU**: General-purpose, bajo paralelismo.
- **GPU**: Alta paralelización, ideal para ML.
- **TPU**: Chips especializados para TensorFlow.

![[Pasted image 20250605210812.png]]

---

## ⚡ Cómo lograr inferencia más rápida

1. Mejor hardware
2. Optimización del modelo
3. Reducción del tamaño del modelo

---

## 🔧 Técnicas para reducir tamaño del modelo

| Técnica             | Descripción                                         |
|---------------------|-----------------------------------------------------|
| Quantization        | Convertir pesos de float32 a int8                   |
| Pruning             | Eliminar pesos o neuronas innecesarias              |
| Knowledge distillation | Entrenar un modelo pequeño a partir de uno grande |

---

## 📉 Quantization

![[Pasted image 20250605211153.png]]

### ¿Por qué usarla?

- Reducir tamaño del modelo
- Menor uso de memoria
- Inferencia más rápida
- Menor consumo energético

### Contras

- Puede **reducir el accuracy**
- Difícil predecir impacto
- Afecta interpretabilidad

---

### Tipos de quantization

- **Post Entrenamiento**:
	- Reduce la precisión de la representación.
	- El modelo pierde un poco de accuracy
	- Considera el rendimiento del modelo y su latencia
- **Entrenamiento considerando Quantization:**
	- Inserta nodos que simulan quantization en el fordward pass.
	- Reescribe el grafo para emular la inferencia con quantization.
	- Reduce la pérdida de accuracy debido a quantization.
	- El modelo resultante ya contiene la especificación para su quantization.

📚 Referencia: [Quantization Aware Training – TensorFlow Blog](https://blog.tensorflow.org/2020/04/quantization-aware-training-with-tensorflow-model-optimization-toolkit.html)

---

## ✂️ Pruning

- Elimina **conexiones** o **neuronas** poco importantes.
![[Pasted image 20250605211508.png]]
### Tipos:

- **No estructurado:** elimina pesos individuales.
- **Estructurado:** elimina neuronas completas.

### Beneficios:

- Mejora velocidad en CPU y algunos aceleradores
- Reduce memoria y transferencia
- Puede **mejorar accuracy** en algunos casos
- Puede combinarse con quantization.

---

## 🎟️ Lottery Ticket Hypothesis (LTH)

> Una subred dentro de una red grande puede igualar el rendimiento de la red completa si fue bien inicializada.


*"Una red neuronal densa e inicializada al azar contiene una subred que fue inicializada de manera tal que, al entrenarse de forma aislada, puede igualar la precisión en el conjunto de prueba de la red original después de entrenarse durante, como máximo, el mismo número de iteraciones.”*
 
📄 Frankle & Carbin, ICLR 2019 – ["The Lottery Ticket Hypothesis"](https://arxiv.org/pdf/1803.03635.pdf)

---

## 👨‍🏫 Knowledge Distillation

- Entrenar un **modelo simple (estudiante)** para imitar uno complejo (maestro)
- Reduce tamaño y cómputo manteniendo buena performance

> Muy útil para servir modelos en producción con restricciones


![[Pasted image 20250605211634.png]]
---

## 📱 Edge ML

Es la tendencia de adopción de dispositivos inteligentes.

![[Pasted image 20250605211716.png]]


### Inferencia en edge vs cloud

| Aspecto              | Edge device                            | Cloud/servidor                       |
|----------------------|-----------------------------------------|--------------------------------------|
| Latencia             | Muy baja                               | Depende de conexión                  |
| Conectividad         | No requiere                             | Necesaria                            |
| Privacidad           | Alta                                    | Riesgo de exposición                 |
| Recursos             | Limitados                               | Ilimitados (en cloud)                |

---

## 📉 Limitaciones de dispositivos Edge

- Poca RAM, CPU/GPU limitada
- Consumo energético y temperatura
- Privacidad y sin conexión a internet (o muy poca)
- Los casos de uso generalmente requieren que sea de bajo costo, pequeños, usen poco cómputo y no se calienten.
- Tamaño reducido de la app/modelo (ej: <4 GB)

---

## 🧠 Predicciones en dos fases

1. Modelo ligero en el dispositivo (ej: wake word: "OK Google")
2. Modelo pesado en la nube, solo si es necesario

> Ej: Google Home, Alexa → el dispositivo escucha, pero solo envía la consulta si se activa

![[Pasted image 20250605211841.png]]

---

## 🏎️ Modelos optimizados para Edge

- Ej: **MobileNet**  
  - 40x más rápido que Inception  
  - Diseñado específicamente para visión en celulares

📄 Howard et al. (2017) – *MobileNets: Efficient CNNs for Mobile Vision*

---

## 🛠️ Técnicas adicionales para mejorar performance

- **Profiling** y benchmarking
- Optimización de operadores
- Paralelización
- Reducir precision o memoria intermedia

---

## 📦 Despliegue en Edge

- **Core ML (Apple)**: herramienta para correr modelos en iOS
- También existen:
  - TensorFlow Lite (Android)
  - MLKit 

---
