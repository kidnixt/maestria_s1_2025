# 📚 Introducción a Machine Learning en Producción


## 🎯 Objetivos del Curso

Este curso no busca enseñar nuevos modelos o técnicas de ML, sino **cómo llevar modelos a producción**, enfrentando desafíos reales:

### Casos donde el curso te va a ayudar
- Tenés datos crudos y querés resolver un problema de negocio.
- Tu modelo funciona offline y querés servirlo online.
- Tenés un sistema en producción y necesitás monitorear/debuguear.
- Querés estandarizar procesos en tu organización.
- Te preocupa el sesgo en tus modelos.

### Qué **no** cubre el curso
- Teoría de ML y estadística.
- Diferentes tipos de métricas.
- Detalles sobre tareas de ML (clasificación, regresión, etc.).

---

## ⚙️ Enfoque del Curso

> Nos enfocamos en **el proceso completo de ML en producción**.

### Componentes de un sistema de ML
![[Pasted image 20250529182140.png]]
- Requerimientos de negocio
- Recolección y procesamiento de datos
- Feature Engineering
- Algoritmos y entrenamiento
- Evaluación
- Infraestructura
- *Serving* y actualizaciones
- Monitoreo

---

## ❓ ¿Cuándo usar Machine Learning?

> ML es útil cuando necesitás **aprender patrones complejos de datos y generalizar** a nuevos datos.

### Criterios para usar ML
- Aprendizaje repetitivo
- Patrones Complejos y/o cambiantes
- **Gran escala de datos**
- **Costos bajos por error**
- **Repetitivo**
- Los datos nuevos se parecen a los existentes

### Criterios para *no* usar ML
- No es ético
- Existen soluciones más simples y eficientes
- El retorno no justifica la inversión

### Ejemplos reales (casos de uso)
| Usuario Final          | Enterprise                  |
|------------------------|-----------------------------|
| Recomendaciones        | Optimización de inventario  |
| Asistentes personales  | Detección de fraude         |
| ETA / Autocompletado   | Pricing dinámico            |

---

## 🧪 Academia vs Producción

| Dimensión         | Academia                          | Producción                            |
|-------------------|-----------------------------------|----------------------------------------|
| Requerimientos    | Lograr SOTA en datasets de prueba | Resolver problemas reales              |
| Prioridades       | Entrenamiento eficiente           | Inferencia rápida y confiable          |
| Datos             | Estáticos, limpios                | Cambiantes, desordenados, streaming    |
| Fairness          | Generalmente ignorado             | Debe ser considerado                   |
| Interpretabilidad | Poco importante                   | Fundamental en muchas aplicaciones     |
### Partes Involucradas en ML Prod.

- **Equipo de ML:** Alta precisión.
- **Producto:** Predicciones rápidas.
- **Manager:** Eficiencia, métricas de negocio
- **Ejecutivos:** Ganancias $

| Dimension               | Academia                | Produccion                    |
| ----------------------- | ----------------------- | ----------------------------- |
| Prioridad Computacional | Entrenamiento eficiente | Inferencia rápida y confiable |
### Serving Queries: Latency vs Throughput
![[Pasted image 20250529183725.png]]


> 🔍 *Ejemplo:* En academia, lo importante es si tu modelo supera el accuracy de otro en ImageNet. En producción, importa si mejora las ganancias o reduce el churn.

---

## 🧠 Desafíos de ML en Producción

- **Desalineación** entre métricas de negocio y métricas de ML (ej: accuracy vs. ingresos).
- **Costo de errores**: pequeños en test offline, grandes en producción.
- **Complejidad técnica**: infraestructura, latencia, escalabilidad.

### Software tradicional vs ML

| Software Tradicional                | Machine Learning                         |
|------------------------------------|------------------------------------------|
| Input + reglas → Output            | Input + Output → Se infieren patrones    |
| Mantenible, determinístico         | Difícil de depurar, no determinístico    |

> 🎯 *Analogía:* Programar una calculadora (software clásico) vs. entrenar un modelo que prediga precios (ML): lo segundo requiere datos, entrenamiento, validación y supervisión constante.

---

## 🛠️ MLOps y Model Serving

### ¿Qué implica servir un modelo?
- Exponerlo mediante una API
- Asegurar baja latencia y escalabilidad
- Monitorear drift, errores y performance

### ¿Qué es MLOps?

> Prácticas para **integrar, desplegar y mantener** modelos de ML de forma confiable, eficiente y escalable.

---

## 🗂️ Fases de un Proyecto de ML

1. **Enmarcar el problema**
   - ¿Qué queremos predecir o automatizar?
   - ¿Qué tipo de tarea de ML corresponde?
   - ¿Hay más maneras de hacerlo?

2. **Definir alcance y objetivos**
   - Métricas de ML vs métricas de negocio
   - Recursos necesarios: datos, talento, presupuesto

3. **Ejemplo de e-commerce**
   - Problema: aumentar ventas
   - Posibles soluciones ML: recomendador, mejor buscador, inventario inteligente

| Métricas ML                  | Métricas de negocio            |
|-----------------------------|-------------------------------|
| Precision a nivel de query  | Engagement del usuario         |
| Recall                      | Aumento de ingresos            |
![[Pasted image 20250529184216.png]]
4. **Hitos**
   - PoC inicial
   - Comparar métricas de ML (precisio/recall)
   - Comparar métricas de Software (latencia, throughput, computo)
   - Comprar métricas de negocio (ganancias, ingresos)
   - Evaluación de impacto y recursos
   - Comparación con soluciones existentes

5. **Diligencia debida**
   - ¿El problema es resoluble con ML?
   - ¿Viene mejorando o se está estancando?
   - ¿Las features disponibles son informativas?
   - ¿El impacto justifica el esfuerzo?
	   - Impacto en la sociedad, justo y sin sesgos. 

---
## 🔚 Conclusión

La clave del curso es entender que **poner un modelo en producción no es el fin del camino, sino el principio**. El verdadero desafío comienza cuando el modelo debe **sobrevivir, adaptarse y aportar valor real** en un entorno dinámico, impredecible y lleno de restricciones técnicas, éticas y de negocio.

---
