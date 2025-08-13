bl## Receta mistica
- **Inspeccionar datos**
	- Graficar
	- Mirar la frecuencia (imprimir un head)
	- **Periodograma:**
		- **Cuándo usarlo:** Úsalo al principio si sospechas que hay ciclos o estacionalidad, pero no estás seguro de sus períodos.
		- **Qué buscar:** Picos de alta **potencia**. La **frecuencia** donde ocurren esos picos te revela los ciclos dominantes en tus datos (ej. un pico en `f=1` en datos anuales indica un ciclo de 12 meses). Esto es muy útil para confirmar la estacionalidad `m` del modelo (ej. `m=12` o `m=4`) y para modelos de regresión armónica.


- **Transformar los datos:**
	- Si es heterocedastica $\rightarrow$ Transformación logarítmica (log, sqrt)
		- Usa `log` cuando la varianza de tu serie crece de forma exponencial o proporcional al cuadrado de su nivel. Usa `sqrt` cuando la varianza crece de forma lineal con el nivel de la serie.
	- Si tiene tendencia lineal $\rightarrow$ Detrend
	- Si tiene tendencia no lineal $\rightarrow$ Diferenciación
	- Si es estacionaria $\rightarrow$ No aplicar transformación

- **Identificar los órdenes de dependencia ([[3.2_Estimación de la correlación a partir de muestras#Propiedad (distribución asintótica de la ACF)|ACF]], [[7_Modelos autorregresivos y autocorrelación parcial#Función de autocorrelación parcial (PACF)|PACF]]).**
	- Si ACF decae expoencialmente y PACF corta abruptamente: 
		- Modelo AR(p), siendo p los "cortes abruptos".
	- Si PACF decae exponencialmente y ACF corta abruptamente:
		- Modelo MA(q), siendo q los "cortes abruptos"
	- Si ACF y PACF decaen exponencialmente:
			- ARMA(p,q) Requiere probar combinaciones y usar AIC/BIC para decidir.
	- Si ACF y PACF tienen picos en lags lejanos:
		- SARIMA, estacional. p y q, adivinalos.
	- **Órdenes estacionales (P, Q):**
		- Mira los picos **únicamente en los lags estacionales** (ej. 12, 24, 36...).
		- **SAR(P):** El PACF tiene un pico significativo en el lag `12`, `24`, etc., y el ACF decae en esos lags. El orden `P` es el número de picos significativos.
	    - **SMA(Q):** El ACF tiene un pico significativo en el lag `12`, `24`, etc., y el PACF decae en esos lags. El orden `Q` es el número de picos significativos.
- 
- **Estimación de parámetros (Mínimos cuadrados o máxima verosimilitud - esto en general ya esta implementado en las librerías de `python`, el `fit()`)**
	- **Tip extra:** Presta atención a las advertencias de convergencia (`convergence warnings`). Si aparecen, es una señal de que el modelo podría estar mal especificado.


- **Diagnóstico (análisis de residuos por ejemplo)**

	- **Análisis de autocorrelación:** El ACF de los residuos no debe tener picos significativos.
	- **Test de Ljung-Box:** Para confirmar que no hay autocorrelación (un p-valor > 0.05 es bueno).
	- **Test de Jarque-Bera:** Para confirmar la normalidad de los residuos (un p-valor > 0.05 es bueno).

- **Elección del modelo (criterio de información de tipo [[4_Modelos lineales#Akaike information criterion|AIC]], evitar overfitting, etc.)**
	- **Principio de parsimonia:** Entre modelos con ajuste similar, elige siempre el más simple (menos parámetros).

- Predicción en base a estimadores lineales calculados recursivamente e intervalos de confianza.