# 🏷️ Etiquetado de Datos

## 🎯 Objetivo de la clase

Comprender cómo influye el proceso de **etiquetado de datos** en la calidad del modelo de ML.  
Explorar **tipos de etiquetas, fuentes, estrategias de etiquetado, ambigüedades y técnicas avanzadas** como Active Learning, Transfer Learning, Weak Supervision y Data Augmentation.

> "f(💩) = 💩" → Si las etiquetas son malas, los resultados también lo serán.

---

## 🔁 Ciclo de gestión de datos

Recolectar → Procesar → Almacenar → Recuperar  
El etiquetado se ubica dentro de este ciclo y es **clave para garantizar datos útiles**.

---

## 🧩 Fuentes de datos etiquetados

1. **Datos propios**
   - ¿Qué tipo de datos ya tengo?
   - ¿Con qué frecuencia se actualizan?
   - ¿Ya están etiquetados o hay que hacerlo?

2. **Etiquetado manual**
   - ¿Quién etiqueta? ¿In-house, tercerizados, crowdsourced?
   - ¿Requiere expertos del dominio o generalistas?

3. **Compra de datasets**
   - Ej: imágenes médicas ya anotadas, bases de texto etiquetado.

---

## ⚖️ Problemas comunes en los datos

| Categoría          | Desafíos clave                                                   |
|--------------------|------------------------------------------------------------------|
| No estructurados   | Difíciles de procesar, etiquetar requiere juicio humano          |
| Estructurados      | Normalmente más fáciles de etiquetar, pero puede haber ruido     |
| Small data         | Pocos ejemplos, difícil generalizar                              |
| Big data           | Ruido, inconsistencias, etiquetar manualmente es inviable        |

> Ej:  
> - 50 millones de datos para reconocimiento de voz = Big Data  
> - 100 imágenes para inspección visual en manufactura = Small Data


![[Pasted image 20250604164209.png]]


## ✅ Calidad vs Cantidad

|                     | Etiquetas ruidosas  | Etiquetas limpias      |
|---------------------|---------------------|-------------------------|
| **Pocos datos**     | ❌ Mucho overfitting | ✅ Mejor opción posible |
| **Muchos datos**    | 🤔 Mejora, pero puede dañar | ✅ Ideal, aunque costoso |
![[Pasted image 20250604164257.png]]
---

## 🖼️ Ejemplos prácticos de etiquetado

- Bounding boxes en imágenes: marcar iguanas 🦎
![[Pasted image 20250604164321.png]]


- Detección de fallas en smartphones
![[Pasted image 20250604164348.png]]


- Captchas: segmentación manual de texto/imágenes

---

## 👥 Votación y crowdsourcing

- Etiquetadores múltiples ≠ siempre mismo resultado
- Soluciones:
  - Etiquetador extra decide
  - Clase neutral
  - Voto por mayoría

> En tareas multilabel u object detection también aplica este problema.

---

## 🧠 Dilemas en el etiquetado humano

- ¿Quién es el ground truth?
- ¿Qué es precisión si hay múltiples opiniones?
- ¿Cómo se entrena a los etiquetadores?
- ¿Qué protocolo deben seguir?

### Consideraciones Claves en el etiquetado humano

- Caro, especialmente si se precisan expertos en el dominio.
- Es muy lento.
- Privacidad de los datos es un gran problema.
- Múltiples fuente de datos etiquetados.
- La ambigüedad es un problema grande. 

---

## 👤 Tipos de etiquetadores

| Tipo             | Descripción                                         |
|------------------|-----------------------------------------------------|
| Expertos         | Dominio especializado (ej. médicos)                 |
| Generalistas     | Personal técnico entrenado para tareas específicas |
| Crowdsourcing    | Usuarios comunes o plataformas externas             |

---

## 🔍 Ambigüedad y multiplicidad de etiquetas

Para mitigarla:
- Usar varios etiquetadores
- Definir claramente el problema
- Establecer un protocolo claro
- Entrenar a los etiquetadores
- Registrar decisiones y trazabilidad

---

## 🧠 Human Level Performance (HLP)

- HLP se usa como benchmark: ¿el modelo rinde igual o mejor que un humano?
- Si el ground truth fue generado por humanos, HLP = nivel de acuerdo entre etiquetadores.
- Si el ground truth viene de fuentes externas (ej: biopsia), el HLP es más objetivo.

---

## 🎯 Etiquetas naturales

Cuando el target **surge de forma automática** del sistema.

### Ejemplos:
- Tiempo estimado de llegada (ETA)
- Clicks, vistas, tiempo de interacción
- Precio de una acción

> ⚠️ A veces implican sesgos (ej: click ≠ preferencia real)

---

## 🖱️ Feedback en sistemas de recomendación

| Tipo      | Ejemplos                                      |
|-----------|-----------------------------------------------|
| Explícito | Ratings, reseñas, NPS                         |
| Implícito | Clicks, vistas, tiempo de permanencia         |

---

## ⚙️ ML en producción vs academia (recordatorio)

| Academia                       | Producción                             |
|-------------------------------|----------------------------------------|
| Dataset limpio, fijo          | Datos dinámicos, sucios                |
| Métricas académicas (F1, acc) | Restricciones reales (latencia, costo) |
| SOTA                          | Simplicidad, mantenibilidad            |

---

## 📉 Cuello de botella: los datos

| Etapa           | Tiempo típico  | Impacto en rendimiento |
|-----------------|----------------|-------------------------|
| Obtener datos   | Semanas        | Alta                    |
| Ajustar modelo  | Días           | Baja                    |

> 🚀 Mejor invertir en buenos datos que en fine-tuning eterno.

![[Pasted image 20250604165258.png]]

---

## ⏱️ ¿Cuánto tiempo dedicar al etiquetado?

En lugar de preguntar “¿cuántos datos necesito?”,  
preguntar: **¿cuántos datos puedo etiquetar en X días?**

> Este cambio de mentalidad permite iterar más rápido.

---

## 🧠 Técnicas avanzadas de etiquetado

| Método              | ¿Cómo funciona?                                  | ¿Necesita ground truth? |
|---------------------|--------------------------------------------------|--------------------------|
| Semi-supervision    | Propaga etiquetas desde un conjunto pequeño      | ✅                       |
| Transfer learning   | Usa modelos preentrenados en otras tareas        | ✅/❌                    |
| Weak supervision    | Usa reglas, keywords, modelos externos           | ❌                      |
| Active learning     | Elige qué datos conviene etiquetar               | ✅                       |

---

## 🔄 Semi-supervised learning

- Propaga etiquetas a partir de ejemplos con etiquetas
- Usa estructura o similitud entre datos (ej: clustering)

![[Pasted image 20250604165403.png]]

> Ejemplo: Label Propagation (Iscen et al., 2019)

---

## 🔁 Transfer Learning

- Modelos entrenados en tareas grandes y generales (ej: BERT, ResNet)
- Se reutilizan en tareas específicas: análisis de sentimiento, detección de fallas, etc.

Ventajas:
- Reduce necesidad de datos etiquetados
- Ahorra cómputo

¿Necesitan Ground Truth?
- **No**, para zero-shot
- **Si** para transfer-learning, pero menos que entrenar desde cero

---

## 🤖 Weak Supervision

Etiquetado ruidoso basado en heurísticas:

- Palabras clave
- Regex
- Bases externas
- Modelos anteriores

Ej: Si una nota médica menciona “neumonía”, marcar como emergencia

> Herramientas: **Snorkel**

![[Pasted image 20250604165433.png]]

![[Pasted image 20250604165444.png]]


---

## 🧠 Active Learning

El modelo pide etiquetas para **los ejemplos más informativos**:

- Los que menos confianza tiene
- Donde hay más desacuerdo entre modelos
- Donde el modelo comete errores frecuentes

> Ideal cuando etiquetar es caro

![[Pasted image 20250604165501.png]]

---

### Técnicas de Active Learning

- **Margen:** ejemplos con baja diferencia entre clases más probables
- **Clustering:** samplea de clusters bien formados para cubrir todo el espacio de los datos
- **Query-by-committee:** usar un ensemble y etiquetar donde discrepan
- **Por regiones:** aplicar técnicas distintas en distintas zonas del espacio de datos

---

## ➕ Data Augmentation

> Crear nuevos datos sintéticos preservando las etiquetas

### Objetivos:
- Aumentar el dataset
- Mejorar robustez y generalización
- Resistir ataques adversarios

---

### Técnicas en imágenes

- Rotación, flipping, cropping
- Brillo, saturación, contraste
- Cortar zonas (Cutout, GridMask)
- Mezclar imágenes (Mixup, CutMix)

---

### Técnicas en texto

- Reemplazo de sinónimos
- Plantillas con slots
  - Ej: “Busco restaurante [tipo] en [barrio]”

> También se usan LLMs para generar variantes
