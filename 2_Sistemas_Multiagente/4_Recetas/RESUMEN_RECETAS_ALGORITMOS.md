# Recetario de Algoritmos para el Agente Racional (Versión Definitiva)

*Aquí tienes una guía práctica, paso a paso, para cada algoritmo que hemos estudiado. Úsala como tu "libro de cocina" para resolver cualquier problema del parcial.*

---
---

## **Familia 1: Recetas para Juegos Matriciales (Un Solo Turno)**

### **Receta 1: Eliminación Iterada (IDSDS)**
* **Objetivo:** Simplificar un juego tachando las jugadas "malas".
* **Ingredientes:** Una matriz de juego.
* **Preparación (Pasos):**
    1.  **Encuentra una Víctima:** Revisa la matriz buscando una estrategia (una fila o una columna) que esté **estrictamente dominada** por otra.
    2.  **Elimina:** Tacha por completo esa fila o columna.
    3.  **Repite:** En la nueva matriz reducida, vuelve al Paso 1.
    4.  **Finaliza:** Cuando ya no puedas eliminar más, la celda (o celdas) que sobreviven son el equilibrio.

### **Receta 2: Encontrar el Equilibrio de Nash (Puro)**
* **Objetivo:** Encontrar los "puntos de no arrepentimiento".
* **Ingredientes:** Una matriz de juego.
* **Preparación (El "Método del Subrayado"):**
    1.  **Analiza al Jugador de Filas:** Ve columna por columna. En cada columna, encuentra la **mayor recompensa** para el jugador de las filas y **subraya ese número**.
    2.  **Analiza al Jugador de Columnas:** Ahora, ve fila por fila. En cada fila, encuentra la **mayor recompensa** para el jugador de las columnas y **subraya ese número**.
    3.  **Encuentra el Equilibrio:** Cualquier celda donde **ambos números** estén subrayados es un Equilibrio de Nash.

---
---

## **Familia 2: Recetas para Juegos por Turnos (Búsqueda en Árbol)**

### **Receta 3: Algoritmo Minimax**
* **Objetivo:** Encontrar la jugada óptima asumiendo un oponente perfecto.
* **Ingredientes:** Un árbol de juego con valores en las hojas.
* **Preparación (De abajo hacia arriba):**
    1.  **Empieza por las Hojas:** Asigna a los nodos finales sus valores de utilidad.
    2.  **Sube por los Nodos MIN:** Para cada nodo que sea un turno del oponente, su valor es el **mínimo** de los valores de sus hijos.
    3.  **Sube por los Nodos MAX:** Para cada nodo que sea tu turno, su valor es el **máximo** de los valores de sus hijos.
    4.  **Repite:** Continúa subiendo hasta llegar a la raíz. La mejor jugada es la primera rama que lleva a ese valor final.

### **Receta 4: Poda Alfa-Beta (Minimax Optimizado)**
* **Objetivo:** Hacer lo mismo que Minimax, pero más rápido.
* **Ingredientes:** Un árbol de juego, una variable `alfa` ($-\infty$) y una `beta` ($+\infty$).
* **Preparación (Mientras se explora):**
    * **En un nodo MAX:** Actualiza `alfa` con el máximo valor encontrado hasta ahora. **Poda** si `alfa >= beta`.
    * **En un nodo MIN:** Actualiza `beta` con el mínimo valor encontrado hasta ahora. **Poda** si `alfa >= beta`.

### **Receta 5: Maxⁿ**
* **Objetivo:** Resolver juegos por turnos con 3 o más jugadores.
* **Ingredientes:** Un árbol de juego donde las utilidades son **vectores** (ej: (5, 2, 8)).
* **Preparación (De abajo hacia arriba):**
    1.  **Empieza por las Hojas:** Asigna los vectores de utilidad a los nodos finales.
    2.  **Sube por los Nodos:** En cada nodo, averigua a qué jugador (`p`) le toca mover.
    3.  **Decisión Egoísta:** El valor de ese nodo será el vector completo del hijo que ofrezca la **mayor recompensa para el jugador `p`** (mirando solo su componente en el vector).
    4.  **Repite:** Continúa hasta la raíz.

### **Receta 6: Monte Carlo Tree Search (MCTS)**
* **Objetivo:** Estimar la mejor jugada en juegos con árboles demasiado grandes.
* **Ingredientes:** Un "presupuesto" de tiempo o de simulaciones.
* **Preparación (El Ciclo de 4 Pasos):**
    1.  **Selección:** Desciende por el árbol existente usando la fórmula UCB (que equilibra explotación y exploración) para encontrar un nodo prometedor.
    2.  **Expansión:** Añade un nuevo nodo (una nueva jugada) al árbol.
    3.  **Simulación (Rollout):** Desde ese nuevo nodo, juega una partida rápida y aleatoria hasta el final para ver quién gana.
    4.  **Propagación (Backup):** Usa el resultado (victoria/derrota) para actualizar las estadísticas de todos los nodos en el camino de vuelta a la raíz.
    5.  **Repite** este ciclo miles de veces. La mejor jugada final será la que lleva al hijo más visitado de la raíz.

---
---

## **Familia 3: Recetas para Algoritmos de Aprendizaje**

### **Receta 7: Fictitious Play (FP)**
* **Objetivo:** Aprender a jugar contra un oponente de estrategia fija y desconocida.
* **Ingredientes:** Una "libreta" para llevar contadores del historial del oponente.
* **Preparación (En cada ronda):**
    1.  **Observa y Actualiza Contadores:** Mira qué jugó tu oponente y suma 1 a su contador.
    2.  **Estima la Estrategia Rival:** Convierte los contadores en probabilidades (ej: `Prob(A) = count(A) / total`).
    3.  **Juega tu Mejor Respuesta:** Calcula y juega la acción que te da el mayor valor esperado contra esa estrategia estimada.

### **Receta 8: Regret Matching (RM)**
* **Objetivo:** Aprender a jugar basándote en las oportunidades perdidas.
* **Ingredientes:** Un "marcador" para el arrepentimiento acumulado de cada acción.
* **Preparación (En cada ronda):**
    1.  **Calcula el Regret Instantáneo:** Para cada una de tus acciones, calcula: `(Recompensa si la hubieras jugado) - (Recompensa que obtuviste)`.
    2.  **Actualiza el Regret Acumulado:** Suma este nuevo regret al marcador total de cada acción.
    3.  **Calcula la Nueva Estrategia:** La probabilidad de jugar una acción es `(su regret acumulado positivo) / (la suma de todos los regrets positivos)`.
    4.  **Juega:** Elige una acción según esta nueva distribución de probabilidad.

### **Receta 9: Independent Q-Learning (IQL)**
* **Objetivo:** Aplicar aprendizaje por refuerzo en un entorno multiagente de la forma más simple posible.
* **Ingredientes:** Una tabla de Q-valores para cada agente: `$Q_i(s, a_i)$`.
* **Preparación (En cada paso del juego):**
    1.  **Elige una Acción:** Usa una política como $\epsilon$-greedy (la mayoría de las veces explota eligiendo la acción con el mayor Q-valor, a veces explora eligiendo una al azar).
    2.  **Observa y Actualiza:** Después de actuar, observa la recompensa `r` y el nuevo estado `s'`.
    3.  **Aplica la Fórmula Q-Learning:** Actualiza `$Q_i(s, a_i)$` usando la recompensa `r` y el valor máximo que puedes obtener en el siguiente estado, `$\max Q_i(s', a'_i)$`. Cada agente hace esto ignorando a los demás.

### **Receta 10: JAL-GT y JAL-AM**
* **Objetivo:** Aprender de forma más inteligente que IQL, considerando a los otros agentes.
* **Ingredientes:** Una tabla de Q-valores más grande para las **acciones conjuntas**: `$Q_i(s, a)$`.
* **Preparación (La Diferencia Clave en la Decisión):**
    * **JAL-GT (El Teórico):** En el estado `s`, construye una matriz de juego con los Q-valores, **resuelve el Equilibrio de Nash** de esa matriz y juega su parte de la estrategia de equilibrio.
    * **JAL-AM (El Psicólogo):** Mantiene un **modelo estadístico** de sus oponentes (como en Fictitious Play). Para decidir, calcula el valor esperado de sus acciones contra lo que **cree** que harán los demás según su modelo.

### **Receta 11: Counterfactual Regret Minimization (CFR)**
* **Objetivo:** Aprender a jugar juegos de información imperfecta (como el póker).
* **Ingredientes:** Un árbol de juego con sus **infosets**.
* **Preparación (La idea general):**
    1.  **Simula:** Realiza millones de exploraciones del árbol de juego.
    2.  **En cada Infoset:** A medida que la simulación "vuelve" hacia la raíz, se detiene en cada infoset y calcula el **arrepentimiento contrafáctico**.
    3.  **Aplica Regret Matching:** Usa ese arrepentimiento para actualizar la estrategia mixta de ese infoset.
    4.  **Repite:** Millones de veces. La estrategia promedio resultante converge a un Equilibrio de Nash.