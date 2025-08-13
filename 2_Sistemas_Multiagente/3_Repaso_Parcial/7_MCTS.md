# 📚 Guía de Estudio Detallada: Aproximación de Valor y MCTS 🎲

---

### **Diapositiva 1: Portada**

* **Resumen Estricto:**
    * **Curso:** Sistemas Multiagente.
    * **Tema:** Juegos Alternados: Funciones de evaluación - Aproximación del valor.
    * **Autor:** Sergio Yovine, Universidad ORT Uruguay.
    * **Fecha:** 7 de mayo de 2025.

---

### **Diapositiva 2: Minimax con Funciones de Evaluación (Repaso)**

* **Resumen Estricto:**
    * Se repasa la fórmula recursiva del algoritmo Minimax cuando se utiliza una **función de evaluación ($Eval(s)$)** y se limita la búsqueda a una **profundidad máxima ($d$)**.
    * Se enfatiza que el valor calculado, $\hat{V}$, es una **aproximación** del verdadero valor Minimax.

* **Ecuaciones de la Diapositiva:**
    * Fórmula de Minimax aproximado:
        $$ \hat{V}_{\pi_{minimax},\pi_{min}}(s,d)= \begin{cases} Utilidad(s) & \text{si EsFinal(s)} \\ Eval(s) & \text{si } d=0 \\ max_{a\in Acciones(s)}\hat{V}_{\pi_{minimax},\pi_{min}}(Suc(s,a),d-1) & \text{si Jugador(s) = Agente} \\ min_{a\in Acciones(s)}\hat{V}_{\pi_{minimax},\pi_{min}}(Suc(s,a),d-1) & \text{si Jugador(s) = Oponente} \end{cases} $$
    * Relación con el valor real:
        $$ \hat{V}_{\pi_{minimax},\pi_{min}} \text{ es una aproximación de } V_{\pi_{minimax},\pi_{min}} $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Por qué el valor calculado con una función de evaluación se considera una "aproximación"?
        > **Respuesta:** Porque no explora el juego hasta el final. La función `Eval(s)` es una "conjetura" o "estimación" de qué tan buena es una posición, no su valor real y definitivo.

---

### **Diapositiva 3: Funciones de Evaluación con Estrategias Mixtas**

* **Resumen Estricto:**
    * Se propone una forma de definir una función de evaluación: $Eval(s)$ puede ser el valor esperado del juego desde el estado `s`, asumiendo que a partir de ese punto ambos jugadores siguen unas **estrategias mixtas** fijas ($\pi_a, \pi_o$).
    * Se compara el resultado de Minimax (valor 1) con el resultado de un oponente que juega al azar (valor esperado 2.3), mostrando que la evaluación cambia según la política del oponente.
    * Se advierte que calcular este valor esperado de forma exacta es computacionalmente "Caro!".

* **Ecuaciones de la Diapositiva:**
    * Definición de la función de evaluación:
        $$ Eval(s)=V_{\pi_{a},\pi_{o}}(s) $$
    * Fórmula recursiva para calcular dicho valor:
        $$ V_{\pi_{a},\pi_{o}}(s)= \begin{cases} Utilidad(s) & \text{si EsFinal(s)} \\ \sum_{a\in Acciones(s)}\pi_{a}(s,a)V_{\pi_{a},\pi_{o}}(Suc(s,a)) & \text{si Jugador(s) = Agente} \\ \sum_{a\in Acciones(s)}\pi_{o}(s,a)V_{\pi_{a},\pi_{o}}(Suc(s,a)) & \text{si Jugador(s) = Oponente} \end{cases} $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es una forma de crear una función de evaluación que no sea simplemente contar piezas?
        > **Respuesta:** Asumir que desde la posición actual, ambos jugadores jugarán con una política fija (por ejemplo, aleatoria) y calcular el valor esperado del resultado.

---

### **Diapositiva 4: Funciones de Evaluación: Monte Carlo**

* **Resumen Estricto:**
    * Introduce el método de **Monte Carlo** para *aproximar* la función de evaluación sin tener que calcularla de forma exacta.
    * **Idea:** Para evaluar un estado `s`, se simulan `m` episodios completos (partidas) desde `s`. En estas simulaciones, los jugadores siguen unas políticas fijas.
    * La estimación del valor de `s` es simplemente el **promedio** de los resultados (utilidades) obtenidos en esas `m` simulaciones.

* **Ecuaciones de la Diapositiva:**
    * Estimación del valor:
        $$ Eval(s)=\hat{V}_{\pi_{a},\pi_{o}}(s) $$
    * Valor del j-ésimo episodio simulado:
        $$ V_{\pi_{a},\pi_{o}}^{j}(s) $$
    * Fórmula de la media de Monte Carlo:
        $$ Eval(s)=\hat{V}_{\pi_{a},\pi_{o}}(s)=\frac{1}{m}\sum_{j=1}^{m}V_{\pi_{a},\pi_{o}}^{j}(s) $$

* **Ejemplo Didáctico:**
    * Quieres saber si una posición de Ta-Te-Ti es buena. En lugar de pensar, simplemente juegas 100 partidas hasta el final desde esa posición, haciendo jugadas al azar para ambos jugadores. Si ganas 70, empatas 20 y pierdes 10, tu estimación Monte Carlo del valor de esa posición es $(70 \times 1 + 20 \times 0 + 10 \times -1) / 100 = 0.6$.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la idea principal del método de Monte Carlo para evaluar un estado?
        > **Respuesta:** Simular muchas partidas al azar desde ese estado y promediar los resultados para estimar su valor.

---

### **Diapositiva 5: Funciones de Evaluación: Rollout**

* **Resumen Estricto:**
    * **Rollout** es el término que se le da al proceso de simular un episodio desde un estado `s` hasta un estado terminal.
    * El método consiste en repetir el rollout `m` veces y promediar los resultados.
    * Se señalan dos problemas: puede ser lento si los episodios son largos, y para solucionar esto se puede acotar la longitud del rollout y usar otra función de evaluación más simple al final.

* **Ecuaciones de la Diapositiva:**
    * Actualización del valor acumulado en un bucle:
        $$ V_{rollout}(s)=V_{rollout}(s)+V_{\pi_{a},\pi_{o}}^{j}(s) $$
    * Valor final:
        $$ V_{rollout}(s)/m $$

* **Ejemplo Didáctico:**
    * "Rollout" es la simulación en sí. Cuando un programa como AlphaGo está "pensando", una de las cosas que hace es realizar miles de estos "rollouts" para cada jugada posible. Un rollout es una partida rápida y "tonta" (usando una política simple) para tener una idea rápida de si la jugada lleva a una victoria o a una derrota.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué es un "rollout"?
        > **Respuesta:** Es la simulación de un único episodio o trayectoria desde un estado hasta el final del juego, generalmente usando una política simple y rápida.

---

### **Diapositiva 6 y 7: Monte Carlo Tree Search (MCTS)**

* **Resumen Estricto:**
    * Se introduce **Monte Carlo Tree Search (MCTS)**, un algoritmo que combina la búsqueda en árboles con las simulaciones de Monte Carlo.
    * MCTS construye un árbol de búsqueda de forma asimétrica, centrándose en las zonas más prometedoras.
    * Para decidir qué rama explorar, utiliza una "política del árbol", como **Upper-Confidence Bound (UCB)**, que equilibra la **explotación** (elegir la jugada que parece mejor hasta ahora) y la **exploración** (probar jugadas menos exploradas por si resultan ser buenas).
    * Dentro del árbol se usa UCB, y al llegar a una hoja del árbol construido, se hace un rollout para estimar su valor.

* **Ecuaciones de la Diapositiva:**
    * Fórmula de Upper-Confidence Bound (UCB) para un nodo `n`:
        $$ UCB(n)=\underbrace{X_{n,m}}_{\text{Explotación}} + \underbrace{C\sqrt{\frac{log~m}{T_{n}(m)}}}_{\text{Exploración (Incertidumbre)}} $$
    * **$X_{n,m}$**: Valor promedio del nodo `n` (su "tasa de victorias").
    * **$m$**: Número de visitas al nodo padre de `n`.
    * **$T_n(m)$**: Número de visitas al propio nodo `n`.
    * **$C$**: Constante que ajusta el nivel de exploración.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué dos conceptos equilibra la fórmula UCB en MCTS?
        > **Respuesta:** Equilibra la **explotación** (preferir los movimientos que han demostrado ser buenos) y la **exploración** (probar movimientos sobre los que se tiene más incertidumbre).
    * **Pregunta:** ¿Cuál es la diferencia principal entre MCTS y un simple Monte Carlo?
        > **Respuesta:** El Monte Carlo simple simula desde la raíz y ya. MCTS construye un árbol, guardando estadísticas en cada nodo y usando esa información para guiar las futuras simulaciones hacia las partes más interesantes del juego.

---

### **Diapositiva 8 y 9: Los 4 Pasos de MCTS**

* **Resumen Estricto:**
    * Se describen los cuatro pasos que se repiten en cada iteración de MCTS:
    1.  **Selección (Selection):** Se parte de la raíz y se desciende por el árbol existente usando la política UCB para elegir el camino más prometedor.
    2.  **Expansión (Expansion):** Al llegar a un nodo hoja del árbol actual, se expande añadiendo uno o más nodos hijos nuevos.
    3.  **Simulación (Simulation):** Desde uno de estos nuevos nodos, se realiza un rollout (simulación rápida y aleatoria) hasta el final del juego para obtener un resultado (victoria/derrota).
    4.  **Propagación hacia arriba (Backup):** El resultado de la simulación se propaga hacia atrás por el camino seleccionado, actualizando las estadísticas (visitas y victorias) de todos los nodos en ese camino.

* **Ejemplo Didáctico:**
    * Imagina que MCTS es un explorador de cuevas.
    1.  **Selección:** Va por los túneles que ya conoce y que parecen más prometedores.
    2.  **Expansión:** Llega a un punto sin explorar y abre un nuevo túnel.
    3.  **Simulación:** Envía un dron rápido por el nuevo túnel para ver si lleva a un tesoro o a un callejón sin salida.
    4.  **Propagación:** Vuelve sobre sus pasos y va marcando en su mapa: "este camino llevó a algo bueno" o "este camino llevó a algo malo".
    * Repite esto miles de veces para construir un mapa muy detallado de la cueva.

* **Flashcards 🧠:**
    * **Pregunta:** Nombra los cuatro pasos del bucle de MCTS.
        > **Respuesta:** Selección, Expansión, Simulación y Propagación (Backup).

---

### **Diapositiva 10: Implementación de MCTS**

* **Resumen Estricto:**
    * Se presenta un pseudo-código de alto nivel para el algoritmo MCTS.
    * Se crea un nodo raíz. Luego, se itera un número determinado de veces realizando los 4 pasos (Select, Expand, Rollout, Backprop).
    * Al final, la mejor acción se elige desde el nodo raíz, basándose en las estadísticas acumuladas (normalmente, la acción que lleva al hijo más visitado).

* **Ecuaciones de la Diapositiva (Pseudo-código):**
    ```
    function mcts(G, s0, t, r):
      Crear nodo raíz n0 con estado s0
      for i = 1 to t do:
        // 1. Selección
        n <- Select(n0)
        // 2. Expansión
        n' <- Expand(G, n)
        // 3. Simulación
        v <- Rollout(G, n', r)
        // 4. Propagación
        Backprop(n', v)
      // Decisión final
      return BestAction(n0)
    ```

* **Flashcards 🧠:**
    * **Pregunta:** Después de miles de iteraciones de MCTS, ¿cómo se elige finalmente la mejor jugada?
        > **Respuesta:** Generalmente se elige la acción que lleva al nodo hijo más visitado desde la raíz, ya que esto indica que el algoritmo ha determinado que es la opción más robusta y prometedora.

---

### **Diapositiva 11: Bibliografía**

* **Resumen Estricto:**
    * Se recomienda el famoso libro de "Reinforcement Learning" de Sutton y Barto, y un paper de referencia sobre MCTS.