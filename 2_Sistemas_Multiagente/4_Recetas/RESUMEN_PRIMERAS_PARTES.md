# Guía de Estudio Práctica: Dominando Equilibrios en Juegos

*Este resumen se enfoca en los conceptos y, más importante, en los **métodos de cálculo** de las dos primeras clases. El objetivo es que sepas qué hacer cuando te enfrentes a una matriz de juego.*

---
---

## **Módulo 1: Juegos de Información Perfecta (Estrategias Puras)**

*Aquí, los jugadores eligen una sola acción y la solución es un punto fijo y predecible.*

### **1. El Concepto Clave: Equilibrio de Nash (EN) 👑**

* **La Idea:** Un resultado es un EN si **nadie se arrepiente**. Ningún jugador puede mejorar su puntuación cambiando su jugada, asumiendo que los demás no cambian la suya.
* **Guía Práctica - Cómo Encontrarlo "a Ojo":**
    1.  Ve celda por celda.
    2.  **Para la celda (Fila X, Columna Y):**
        * **Pregúntale al Jugador de Fila:** "Si el otro se queda en la Columna Y, ¿hay alguna otra fila que me dé más puntos que la Fila X?". Si la respuesta es no, subraya la puntuación de la fila.
        * **Pregúntale al Jugador de Columna:** "Si el otro se queda en la Fila X, ¿hay alguna otra columna que me dé más puntos que la Columna Y?". Si la respuesta es no, subraya la puntuación de la columna.
    3.  **Veredicto:** Si **ambos números** en una celda están subrayados, ¡has encontrado un Equilibrio de Nash!

### **2. La Herramienta de Simplificación: Dominancia 💪**

* **La Idea:** A veces, una jugada es objetivamente mejor que otra. Identificar esto te permite simplificar el juego.
* **La Diferencia Clave:**
    * **Dominancia Estricta (>):** La jugada A **siempre** te da más puntos que la jugada B, en todos los escenarios.
    * **Dominancia Débil (≥):** La jugada A **nunca** te da menos puntos que B, y al menos una vez te da más.
* **Guía Práctica - Cómo Detectar Dominancia (para un jugador de filas):**
    1.  Elige dos de sus filas para comparar (ej: Fila U vs. Fila D).
    2.  Ve columna por columna:
        * ¿Es la puntuación de U mayor que la de D en la Columna L?
        * ¿Es la puntuación de U mayor que la de D en la Columna C?
        * ¿Es la puntuación de U mayor que la de D en la Columna R?
    3.  **Conclusión:**
        * Si la respuesta fue "Sí" las tres veces, entonces **U domina estrictamente a D**.
        * Si las respuestas fueron una mezcla de "Sí" y "Son iguales", entonces **U domina débilmente a D**.

### **3. Eliminación Iterada (IDSDS): Resolviendo por Lógica 🧠**

* **La Idea:** Simplificar el juego tachando estrategias estrictamente dominadas en rondas sucesivas.
* **Guía Práctica - Cómo Aplicarla:**
    1.  **Busca una estrategia estrictamente dominada** para CUALQUIER jugador.
    2.  Si la encuentras, **táchala**. Borra esa fila o columna de la matriz. Ya no existe.
    3.  En la **nueva matriz reducida**, vuelve al paso 1. Busca otra estrategia estrictamente dominada.
    4.  Repite hasta que no puedas eliminar más. Lo que queda es el equilibrio.
    * **Advertencia:** Solo hazlo con dominancia **estricta**. La eliminación con dominancia débil (IDWDS) puede dar resultados diferentes según el orden en que taches las cosas.

---
---

## **Módulo 2: El Mundo Probabilístico (Estrategias Mixtas)**

*Aquí es donde los jugadores introducen el azar para ser impredecibles. El objetivo es maximizar la ganancia promedio.*

### **1. Estrategias Mixtas y Valor Esperado 🎲**

* **La Idea:** Una estrategia mixta es una distribución de probabilidad sobre tus acciones. El **Valor Esperado ($V_p$)** es tu puntuación promedio si jugaras con esa estrategia muchas veces.
* **Guía Práctica - Cómo Calcular el Valor Esperado ($V_1$) para el Jugador 1:**
    Imagina esta matriz y que las estrategias son:
    * $\pi_1 = (0.6, 0.4)$ para {U, D}
    * $\pi_2 = (0.3, 0.7)$ para {L, R}

| | L | R |
|:---|:---:|:---:|
| **U** | (10, 2) | (0, 4) |
| **D** | (5, 3) | (8, 1) |

**Calcula la probabilidad de cada celda:** Multiplica las probabilidades de la fila y la columna correspondientes.
        * P(U,L) = 0.6 * 0.3 = 0.18
        * P(U,R) = 0.6 * 0.7 = 0.42
        * P(D,L) = 0.4 * 0.3 = 0.12
        * P(D,R) = 0.4 * 0.7 = 0.28
        * *(Verifica que suman 1: 0.18+0.42+0.12+0.28 = 1.0)*
**Calcula la contribución de cada celda a tu valor:** Multiplica la probabilidad de la celda por tu recompensa en esa celda.
        * Contribución de (U,L) = 0.18 * **10** = 1.8
        * Contribución de (U,R) = 0.42 * **0** = 0
        * Contribución de (D,L) = 0.12 * **5** = 0.6
        * Contribución de (D,R) = 0.28 * **8** = 2.24
**Suma las contribuciones:**
        * $V_1 = 1.8 + 0 + 0.6 + 2.24 = 4.64$. Tu ganancia promedio con esta estrategia es 4.64.

### **2. El Principio de Indiferencia: La "Llave Maestra" 🔑**

* **La Idea:** La herramienta más importante para **encontrar** un Equilibrio de Nash mixto. Un jugador solo mezclará acciones si **todas le dan el mismo valor esperado**.
* **Guía Práctica - Cómo Encontrar la Estrategia de Equilibrio del Jugador 2:**
    1.  **Define la estrategia del Jugador 2 con una variable:** Digamos que J2 juega L con probabilidad `$p` y R con probabilidad `$1-p$`. Nuestra meta es encontrar `$p$`.
    2.  **Ponte en los zapatos del Jugador 1:** Calcula el valor esperado para el Jugador 1 de cada una de sus acciones puras, *en función de `p`*.
        * $V_1(U)$ = (Prob. de que J2 juegue L) * (Mi recompensa en U,L) + (Prob. de que J2 juegue R) * (Mi recompensa en U,R)
            $V_1(U) = p \cdot (10) + (1-p) \cdot (0) = 10p$
        * $V_1(D) = p \cdot (5) + (1-p) \cdot (8) = 5p + 8 - 8p = 8 - 3p$
    3.  **Aplica la indiferencia:** Para que el Jugador 1 esté dispuesto a mezclar U y D, su valor esperado debe ser el mismo para ambas. **Iguala las dos ecuaciones**.
        * $10p = 8 - 3p$
    4.  **Resuelve para `p`:**
        * $13p = 8 \implies p = 8/13$.
    5.  **Conclusión:** La estrategia de equilibrio para el Jugador 2 es `(8/13, 5/13)`. Este es el único "plan de azar" que puede seguir para evitar que el Jugador 1 tenga una única mejor respuesta y lo explote.