# 📊 Definición y Recolección de Datos

## 🎯 Objetivo de la clase

Entender por qué los **datos son el insumo más crítico** para proyectos de ML. Se explora cómo se **definen**, **recolectan**, **almacenan** y **procesan** datos para que estén listos para modelos de ML en producción.

> "f(💩) = 💩"  
> Si tus datos son malos, no importa qué modelo uses: los resultados también lo serán.

![[Pasted image 20250529195227.png]]

---

## 🧱 Importancia de los datos en ML

- Los datos son **la parte más difícil** y más **importante** del ciclo de vida de ML.
- La mayoría de los errores en producción se deben a **datos pobres o mal gestionados**.
- Mejorar la calidad de los datos tiene un **retorno de inversión altísimo**.

> Ejemplo: en un sistema de recomendaciones de películas, si los datos de visualización están incompletos o mal etiquetados, el modelo recomendará contenido irrelevante.

---

## 🔁 Ciclo de gestión de datos

Recolectar → Procesar → Almacenar → Recuperar

Cada etapa implica decisiones técnicas clave: desde cómo obtenemos los datos hasta cómo los accedemos para entrenar modelos.

---

## 🔍 Fuentes de datos típicas

- **Ingresados por usuarios:** formularios, encuestas, preferencias de perfil.  
  _Ej:_ edad, género, ubicación en una app.
- **Comportamiento de usuarios:** clics, navegación, tiempo en página.  
  _Ej:_ productos vistos o ignorados en un e-commerce.
- **Generados por el sistema:** logs de errores, métricas de backend.
- **Bases internas:** datos existentes en bases de datos de la organización.
- **Datos de terceros:** APIs (ej. clima, finanzas) o datasets comprados.

---

## 🗂️ Formatos de datos y serialización

### 📄 Formatos comunes

| Formato   | Tipo        | Legible | Usos típicos                  |
|-----------|-------------|---------|-------------------------------|
| JSON      | Texto       | ✅      | APIs, configuraciones         |
| CSV       | Texto       | ✅      | Datasets planos, tabulados    |
| Parquet   | Binario     | ❌      | Procesamiento en Big Data     |
| Avro      | Binario     | ❌      | Kafka, Datastreams            |
| Protobuf  | Binario     | ❌      | Serialización en Google, TF   |
| Pickle    | Binario     | ❌      | Serialización en Python       |

---

### 🧠 Row-major vs Column-major

| Estrategia    | Ideal para...         | Ejemplo         |
|---------------|-----------------------|------------------|
| Row-major     | Acceder por muestras  | CSV              |
| Column-major  | Acceder por features  | Parquet, Pandas  |

> En Pandas (column-major) es más rápido acceder a columnas que a filas.  
> En NumPy, por defecto es row-major, pero se puede modificar.

---

## 💾 Texto vs Binario

| Característica         | Texto (JSON, CSV) | Binario (Parquet, Avro) |
|------------------------|-------------------|--------------------------|
| Legibilidad            | ✅                | ❌                       |
| Tamaño (espacio)       | ❌                | ✅                       |
| Velocidad de lectura   | ❌                | ✅                       |
| Ejemplo (guardar 1M)   | 7 bytes           | 4 bytes (`int32`)        |

> En proyectos reales, usar binario (como Parquet) puede reducir el almacenamiento a la mitad.

---

## 🧮 Modelos de datos

### Relacional (SQL)

- Basado en tablas con columnas y filas.
- Requiere normalización para evitar duplicación.
	- **PROS:** 
		- Menos Errores de modificación.
		- Más fácil de actualizar.
		- Más fácil de localizar datos.
	- **CONTRAS:**
		- Datos distribuidos entre varias relaciones.
		- Joins se vuelven más caros computacionalmente.
- Usa joins para combinar datos relacionados.
- Schema rígido y explícito.
- Ejemplo: una tabla de `clientes` relacionada con una tabla de `pedidos`.

### No Relacional (NoSQL)

- Basado en documentos flexibles (JSON, XML).
- No requiere schema fijo.
- Ideal para estructuras anidadas y datos semi-estructurados.
- Ejemplo: guardar todo sobre un cliente (nombre, pedidos, preferencias) en un solo documento.

---

### 📊 SQL vs NoSQL

| SQL (Estructurados)                   | NoSQL (No estructurados)                                                               |
| ------------------------------------- | -------------------------------------------------------------------------------------- |
| Fácil de hacer búsquedas y análisis   | Fácil de insertar nuevos datos                                                         |
| Schema rígido                         | Schema flexible (no tiene por qué cumplirlo)                                           |
| Datos tabulares                       | Datos en documentos                                                                    |
| Cambios en el schema causan problemas | No hay que preocuparse por el schema, lo manejan las aplicaciones que usan los datos.  |
| Se almacenan en Data Warehouses       | Se almacenan en Data Lakes                                                             |
| Ideal para datos estructurados        | Ideal para datos semi/no estructurados                                                 |

---

## ⚙️ OLTP vs OLAP

| OLTP (Transacciones)                        |
| ------------------------------------------- |
| Grandes volúmenes de datos transaccionales. |
| En tiempo real                              |
| Alta disponibilidad y baja latencia.        |
| Interactúan con los usuarios                |
| ACID, BASE                                  |
| Row-major                                   |
| Ej: PostgreSQL, MySQL                       |

| OLAP (Analítica)                                             |
| ------------------------------------------------------------ |
| Procesan y analizan gran cantidad de datos rápido            |
| Pensado para análitica de negocio.                           |
| Interactúan con lado de negocio, incluyendo Data Scientists. |
| BASE o ACID                                                  |
| Column-major                                                 |
| Ej: Redshift, BigQuery                                       |

> OLTP: Muchas operacioens pequeñas y frecuentes, como insertar o actualizar datos en tiempo real.
> OLAP: Consultas pesadas y agregadas, como reportes o dashboards que analizan muchos datos. 

---

## ⚖️ ACID vs BASE

**ACID (ideal para OLTP tradicionales)**  
1. Atomicidad: todo o nada  
2. Consistencia: deja la BD en un estado válido  
3. Aislamiento: sin interferencias entre transacciones (no se pisan)  
4. Durabilidad: persistencia en el tiempo (una vez confirmada no se pierde)

**BASE (más flexible, usado en bases NoSQL)**  
1. Basically Available  (sistema casi siempre disponible)
2. Soft State  (el estado puede cambiar, incluso sin nuevas entradas)
3. Eventual Consistency  (eventualmente los datos están actualizados en todos lados)

> BASE prioriza escalabilidad y tolerancia a fallos sobre consistencia inmediata.

> OLTP tradicionales van más por **ACID**, aunque sistemas distribuidos modernos a veces prefieren **BASE** para ganar rendimiento o escalabilidad.

---
## 🪣 Data Warehouse

- Agrega o acumula diferentes fuentes de datos.
- Procesa y analiza.
- Optimizado para lecturas complejas.
- No es real-time.
- Datos estructurasdos, **cumple un schema**
### 🪣 Data Warehouse vs Base de Datos

| **Data Warehouse**                           | **Base de Datos**                      |
| -------------------------------------------- | -------------------------------------- |
| OLAP                                         | OLTP                                   |
| Posible demora entre almacenamiento y acceso | Datos disponibles en tiempo real       |
| Almacena datos históricos y actuales.        | Almacena únicamente los datos actuales |
| Datos escalan a terabytes.                   | Solo en el entorno de gigabytes.       |
| Queries complejas, para análisis.            | Queries simples para transaccioens     |
| Queries llevan tiempo                        | Queries son en tiempo real.            |

## 🪣 Data Warehouse vs Data Lake


| Característica | Data Warehouse           | Data Lake                   |
| -------------- | ------------------------ | --------------------------- |
| Estructura     | Procesado y estructurado | Datos crudos (raw)          |
| Usuarios       | Analistas de negocio     | Data scientists / Engineers |
| Accesibilidad  | Más controlado           | Muy accesible               |
| Ejemplos       | Redshift, BigQuery       | S3, GCS, Azure Data Lake    |

> DW para dashboards/reportes. DL para exploración, modelado, y ML.

---

## 🔁 ETL – Extract, Transform, Load

- **Extract:** desde CSVs, APIs, bases de datos, logs, etc.
- **Transform:** limpieza de datos, deduplicado, joins, enriquecimiento.
- **Load:** cargar en un DW o Data Lake.

> Ejemplo: combinar datos de clientes y compras, limpiarlos, y cargarlos en BigQuery.

---

## 🔄 Modos de flujo de datos

- **Base de datos intermediaria:** microservicios escriben en una base compartida.
- **Request / response:** un servicio consulta directamente a otro.
- **Mensajería (Kafka, Pub/Sub):** arquitectura orientada a eventos, asincrónica.

> Kafka se usa para procesar millones de eventos por segundo en apps modernas.

---

## 🕒 Procesamiento Batch vs Streaming

| Batch                         | Stream                              |
|-------------------------------|--------------------------------------|
| Datos históricos              | Datos en tiempo real                 |
| Ej: Spark, MapReduce          | Ej: Flink, Kafka Streams             |
| Ej. ML: features estáticos    | Ej. ML: features dinámicos           |

> Batch: generar reportes diarios.  
> Stream: actualizar predicciones de riesgo en tiempo real.

---
