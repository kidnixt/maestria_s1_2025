# ✅ Clase 10 – Calidad en Sistemas de ML


---
## 🎯 Objetivo de la clase

Entender cómo asegurar la **calidad de los sistemas de ML en producción**, qué se considera una **falla**, cómo detectarla, y cómo mantener un sistema robusto y confiable ante cambios.

---

## 🔧 Tipos de fallas en sistemas ML

> Una falla es cuando **no se cumple una expectativa** respecto a requerimientos.

### 1. Requerimientos funcionales

- Se mide con métricas de ML (accuracy, F1, etc).
- Ej: clasificación incorrecta, predicción errónea.

### 2. Requerimientos no funcionales

- Se refiere a la operación del sistema:
  - Latencia
  - Throughput
  - Uptime

---

## ⚠️ Ejemplos de fallas

| Sistema                 | Falla operacional         | Falla de modelo (funcional)         |
|-------------------------|---------------------------|--------------------------------------|
| Traductor automático    | No responde                | Traducción incoherente              |
| Búsqueda de imágenes    | No carga resultados        | Resultados irrelevantes             |
| OCR de documentos       | No escanea                 | Clasificación incorrecta            |
| Vehículo autónomo       | No enciende                | Falla en evitar colisión (grave)    |

> 🧠 “Bueno” ≠ “Útil” → un modelo con F1=0.98 puede no ser suficiente en producción

---

## 💥 Fallas frecuentes en producción

- **Casos de borde** no contemplados
- Diferencia entre datos de entrenamiento y de producción:
  - `Train-serving skew`

![[Pasted image 20250612190529.png]]


  - `Data drift`
  - `Concept drift`
  - `Label drift`
- **Feedback loops degenerativos**

---

## 📉 Drift: cambios en los datos

Los modelos operan en ambientes **dinámicos**, por ende el **ground truth** también cambia.
→ si el modelo queda estático, su rendimiento se degrada (se aleja del ground truth)


![[Pasted image 20250612190903.png]]
### Distribuciones

- `Distribución fuente`: datos con los que entrena el modelo.
- `Distribución objetivo`: datos sobre los que hace inferencia el modelo (producción)

---

## 📊 Tipos de Drift en Sistemas de ML

Cuando los datos cambian con el tiempo, el rendimiento del modelo se puede degradar. Esto se conoce como **drift**, y puede manifestarse de varias formas según qué parte del sistema cambia.

---

### 🟠 Covariate Drift

En estadística, una covariable es una variable que posiblemente predice el resultado bajo estudio. En ML supervisado las features son **covariables.**

**¿Qué cambia?**  
- Cambia la distribución fuente (inputs): `P(X)`  
- Pero dado un input, su output no cambia: `P(Y|X)`

**Ejemplo:**  
- En producción, llegan más usuarios mayores de 60 años que en entrenamiento.
- El modelo aún sabe predecir bien para este grupo, pero como su proporción crece, el dataset ya no representa adecuadamente la realidad.
- Predecir P(cancer | paciente)
- P (edad > 60) es mayor en training que en producción.
- Pero P(cancer | edad > 60) es lo mismo en training que en producción.

![[Pasted image 20250612192254.png]]

#### Causas de Covariate Shift

**Durante Entrenamiento:**
- Sesgos por muestreo:
	- Se aconseja a personas mayores a hacerse más chequeos.
	- Over/undersampling (muchos pacientes viejos de un hospital)

**En Producción:**
- Cambios en el ambiente:
	- P (convertir un usuario gratis a pago | usuario gratuito)
		- Se realiza una campaña que atrae a usuarios de mayor poder adquisitivo.
		- P (mayor poder adquisitivo) aumenta en los datos.
		- Pero P (convertir un usuario gratis a pago | nivel adquisitivo) se mantiene igual

**Consecuencias:**  
- El modelo puede seguir siendo válido, pero el **peso de cada observación** cambia.
- Puede afectar la representatividad y precisión general.

**Cómo detectarlo:**  
- Comparar estadísticas de features entre entrenamiento y producción.
- Tests como KS test, PSI (Population Stability Index).

---

### 🔴 Label Drift

**¿Qué cambia?**  
- Cambia la distribución del output: `P(Y)`  
- Pero dado un output, la distribución : `P(X|Y)` se mantiene
- En general Covariate Shift deviene en Label Shift (Shift es lo mismo que drift)

**Ejemplo:**  
- En un hospital, aparece una nueva ola de una enfermedad.
- Aumentan los casos positivos sin cambiar las características de los pacientes.
- Si se inventa una medicina preventiva contra el cáncer
	- Se reduce la probabilidad de P (cancer | paciente ) para todas las edades.

**Consecuencias:**  
- Si se usa una muestra aleatoria para evaluación, se verá **una caída de accuracy** por desbalance.
- Puede llevar a decisiones mal calibradas (ej: modelos de riesgo mal evaluados).

**Cómo detectarlo:**  
- Comparar frecuencia de clases entre entrenamiento y producción.
- Requiere acceso a ground truth actualizado.

---

### 🟡 Data Drift (también llamado Feature Drift)

**¿Qué cambia?**  
- Cambian las estadísticas de los features `X` (media, varianza, etc.) O sea, propiedades estadísticas de las features cambian. **El modelo entrenado ya no es relevante una vez que los datos cambiaron** 

**¿Qué no necesariamente cambia?**  
- No implica directamente un cambio en `P(Y|X)`

**Ejemplo:**  
- Cambia el país de origen de los usuarios.
- La edad promedio sube de 30 a 45 años.

![[Pasted image 20250612193110.png]]


**Consecuencias:**  
- Puede indicar **desalineación entre datasets**.
- También puede anticipar covariate o concept drift.

**Cómo detectarlo:**  
- Medir media, varianza, outliers, histograma de features.
- Técnicas de detección no supervisadas.

---

### 🔵 Concept Drift

**¿Qué cambia?**  
- Cambia la relación entre input y output: `P(Y|X)`  
- Se puede mantener la distribución de inputs `P(X)`
- Relación entre las features y las etiquetas cambia.
- El significado en sí de lo que estamos intentando predecir cambia.

![[Pasted image 20250612193603.png]]

![[Pasted image 20250612193618.png]]

**Ejemplo:**  
- Antes, comprar pijamas durante el día era típico de personas desempleadas.  
- En pandemia, todos lo hacen: el concepto de “persona con disponibilidad diurna” cambia.

**Consecuencias:**  
- **Grave degradación de performance**
- Los supuestos aprendidos ya no se sostienen.

**Cómo detectarlo:**  
- Solo puede detectarse con **ground truth actualizado**.
- Monitorear métricas de desempeño en producción (accuracy, F1, etc.)

---

### 📘 Comparación General

| Tipo            | Cambia...         | Se mantiene... | Requiere etiquetas para detectar |
| --------------- | ----------------- | -------------- | -------------------------------- |
| Covariate Drift | `P(X)`            | `P(Y\X)`       | ❌ (puede ser no supervisado)     |
| Label Drift     | `P(Y)`            | `P(X\Y)        | ✅                                |
| Data Drift      | Estadísticas de X | -              | ❌                                |
| Concept Drift   | `P(Y\X)`          | `P(X)`         | ✅                                |

---

### 🧠 Observaciones clave

- **Concept drift** puede ser:
  - *Repentino* (un cambio brusco en la relación)
  - *Gradual* (cambia lentamente con el tiempo)
  - *Estacional* (se repite cíclicamente)

- **Covariate drift** puede parecer inofensivo pero puede causar **bias** en el modelo.

- **Label drift** suele indicar un cambio en el contexto o distribución real del problema.

---

---

## 🧪 Cómo detectar drift

- **Observabilidad**: inputs, outputs y métricas deben ser logueados
- Las **requests de predicciones** deben ser logueadas.
	- O por lo menos loguear los datos de la request
	- Analizar estos datos es clave para detectar el drift que cause el deterioro de los modelos. 
- Loguear también el ground truth asociado.
	- Así luego se puede usar para entrenar.
- Analizar estadísticamente:
  - Cambios en distribuciones
  - Shift en predicciones
  - Comparar ground truth vs predicho
- Visualización con dashboards

### Herramientas

- `TF Data Validation`
- `Scikit-multiflow`
- `WhyLabs`
- Herramientas nativas (GCP, AWS, Azure)

---

## 🔁 Retroalimentación degenerativa (degenerate feedback loop)

> Las **predicciones influyen los datos futuros**  
> (el modelo afecta el mundo que luego lo alimenta)

### Ejemplo: recomendadores

- Recomendás un ítem
- Usuario lo ve → se registra como "buena predicción"
- El sistema refuerza ese ítem → **homogeneización**

---

## 🛠️ Cómo detectar loops degenerativos

- Medir popularidad media de los ítems recomendados
- % de ítems de la long-tail (menos populares)
- Comparar `hit-rate` vs popularidad

📄 Chia et al. (2021) – *RecList: Behavioral Testing of Recommenders*  
https://arxiv.org/abs/2111.09963

---

## 🛡️ Cómo prevenir loops degenerativos

- **Aleatoriedad controlada**
  - Ej: TikTok muestra videos nuevos a grupos aleatorios al principio

- **Features posicionales**
  - Posición en el feed: no es lo mismo estar 1º que 5º

---

## 🧬 Otros cambios en datos

- Cambios en las features (se agregan o eliminan)
- Cambios en las etiquetas (p. ej. agregar clase “neutral” a análisis de sentimiento)

### Estrategias

- **Schema bridge**
- **Imputación**: media, mediana, moda o clase más frecuente

---

## 🔍 Por qué monitorear

- Los datos de entrenamiento son **una foto de la realidad**
- El entorno **cambia constantemente**, los modelos no mejoran con el tiempo.
- Queremos:
  - Detectar degradación
  - Alertar rápido
  - Tomar decisiones de reentrenamiento

---

## 📊 Qué monitorear

| Elemento          | Ejemplos                                           |
|-------------------|----------------------------------------------------|
| Accuracy           | Métricas offline vs online                         |
| Predicciones       | Distribución de clases, confianza promedio         |
| Features           | Rangos, outliers, frecuencia por segmento          |
| Sistema            | Latencia, throughput, uptime                       |

---

## 📺 Monitoreo

- **Datos cambiantes:**
	- Los datos de entrenamiento son tan viejos que ya no representan la realidad.
- **Obsolencia de los modelos:**
	- El ambiente cambia
	- El comportamiento de los usuarios cambia
	- Escenarios adversarios.
- **Retroalimentación negativa de los modelos**

---

## 🧩 Monitoreo de input

- ¿Los inputs son válidos? (tipos, rangos)
- ¿Hay cambios significativos en la distribución con la historica?
- ¿Cambios en segmentos clave? Como se ve la evolución a nivel de segmentos

---

## 🧪 Monitoreo de predicciones

- Comparar distribución de predicciones actuales vs históricas
- Si se tiene ground truth: calcular accuracy, F1, etc.

---

## 🖥️ Monitoreo funcional vs no funcional

| Tipo                     | Incluye                                       |
|--------------------------|-----------------------------------------------|
| Funcional (ML)           | Accuracy, cambios en datos de serving         |
| No funcional (sistema)   | Latencia, throughput, uptime                  |

![[Pasted image 20250612195356.png]]

---

## 🧰 Herramientas y sugerencias

- Usar `logging` de plataformas:
  - `Google Cloud Ops`, `AWS CloudWatch`, `Azure Monitor`
- Centralizar logs en un solo sistema
- Crear alertas basadas en logs
- Visualizar métricas relevantes (Dashboards)

---

## 📋 ¿Qué loguear?

- Inputs y predicciones
- Ground truth (si está disponible)
- Errores del sistema
- Casos especiales o anómalos

> Logging sirve también para crear datasets futuros para reentrenar


![[Pasted image 20250612200942.png]]
---

## 🔄 ¿Qué hacer cuando se detecta drift?

1. **Determinar qué datos aún son válidos**
   - Filtrar temporalmente (ej: últimos 30 días)
   - Descartar los malos y agregar nuevos datos.
   - Eliminar segmentos irrelevantes.
   - Crear un nuevo dataset.

2. **Reentrenamiento**
   - Fine-tune del modelo existente
   - Entrenamiento desde cero con nuevos datos

---

## 🔁 Políticas de reentrenamiento

| Tipo       | Cuándo se activa                                                                                                    |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| Manual     | Bajo demanda, análisis humano, cuando hay suficientes datos nuevos.                                                 |
| Automático | - Periódico (ej: semanal, mensual)<br>- Cuando se detecta que el rendimiento baja<br>- Cuando se detecta Data Drift |
![[Pasted image 20250612201346.png]]

![[Pasted image 20250612201406.png]]

![[Pasted image 20250612201421.png]]
---

## 🧪 Evaluación en producción

> Siempre evaluar **offline primero**

### Estrategias

- **Shadow deployment**: se predice en paralelo, sin afectar producción
- **Canary release**: se expone a un % pequeño de usuarios
- **A/B testing**: comparar dos versiones del modelo
- **Bandits**: balanceo dinámico según performance
