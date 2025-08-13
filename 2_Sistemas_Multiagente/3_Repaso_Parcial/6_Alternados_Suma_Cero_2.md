# 📚 Guía de Estudio Detallada: Minimax, Poda α-β y Funciones de Evaluación 🧠

---

### **Diapositiva 1: Portada**

* **Resumen Estricto:**
    * **Curso:** Sistemas Multiagente.
    * **Tema:** Juegos Alternados: Minimax - Poda - Funciones de evaluación.
    * **Autor:** Sergio Yovine, Universidad ORT Uruguay.
    * **Fecha:** 30 de abril de 2025.

---

### **Diapositiva 2: El Problema de Minimax: La Complejidad**

* **Resumen Estricto:**
    * El algoritmo Minimax requiere recorrer el árbol de juego completo.
    * Su complejidad temporal es exponencial: $O(b^d)$, donde `b` es el factor de ramificación (número de jugadas posibles en promedio) y `d` es la profundidad de la búsqueda (cuántos turnos hacia adelante se mira).
    * Para juegos complejos como el Ajedrez o el Go, esta complejidad hace que sea imposible explorar el árbol completo.
    * Esto motiva la necesidad de usar técnicas para acelerar la búsqueda, como la **Poda** y las **Funciones de Evaluación**.

* **Ecuaciones de la Diapositiva:**
    * Complejidad en tiempo: $O(b^{d})$
    * Parámetros para Ajedrez: $b \sim 35, d \sim 50$
    * Parámetros para Go: $b \sim 361, d \sim 100$

* **Ejemplo Didáctico:**
    * Imagina un juego simple donde en cada turno hay 10 jugadas posibles (`b=10`). Si quieres planificar solo 4 turnos hacia adelante (`d=4`), necesitas evaluar $10^4 = 10,000$ posibles desenlaces. Si quieres mirar 8 turnos, el número escala a $10^8 = 100,000,000$. Para el ajedrez, con $b \approx 35$, es computacionalmente inviable.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la complejidad temporal del algoritmo Minimax y qué significan sus parámetros `b` y `d`?
        > **Respuesta:** La complejidad es $O(b^d)$, donde `b` es el factor de ramificación (movimientos promedio por turno) y `d` es la profundidad de la búsqueda (turnos a futuro).
    * **Pregunta:** ¿Qué dos técnicas se proponen para mitigar la complejidad de Minimax?
        > **Respuesta:** 1. La Poda (para evitar explorar ramas inútiles). 2. Las Funciones de Evaluación (para no tener que explorar hasta el final del juego).

---

### **Diapositiva 3: Introducción a la Poda α-β**

* **Resumen Estricto:**
    * La **Poda Alfa-Beta** ($\alpha-\beta$) es un algoritmo de optimización para Minimax que pertenece a la familia de algoritmos "branch-and-bound".
    * La idea es dejar de explorar una rama del árbol de juego tan pronto como se demuestra que no puede influir en la decisión final.
    * En el ejemplo, después de analizar la rama A, el jugador MAX sabe que puede obtener un valor de **al menos 3**. Al empezar a analizar la rama B, el jugador MIN puede forzar un valor de **como máximo 2**. Como MAX nunca elegirá una rama que le da $\le 2$ si ya tiene una que le da $\ge 3$, no es necesario seguir explorando la rama B. Se puede "podar".

* **Ecuaciones de la Diapositiva:**
    * Cálculo del valor en la raíz (sin poda): $R=max\{A,B\}=max\{min\{3,5\},min\{2,10\}\}$
    * Inferencia tras evaluar A: $R \ge 3$
    * Inferencia tras evaluar el primer hijo de B: $B \le 2$
    * Decisión final de R: $R=max\{\ge3,\le2\}=\ge3$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la idea fundamental de la poda Alfa-Beta?
        > **Respuesta:** Es ignorar (podar) partes del árbol de búsqueda que no pueden mejorar el resultado de la decisión que ya se ha encontrado.

---

### **Diapositiva 4: Valores Alfa (α) y Beta (β)**

* **Resumen Estricto:**
    * Se definen los valores $\alpha$ y $\beta$ que dan nombre al algoritmo.
    * **$\alpha$ (alpha):** Es el mejor valor (el más alto) que el jugador **MAX** puede garantizarse hasta el momento en el camino actual. Es una cota inferior.
    * **$\beta$ (beta):** Es el mejor valor (el más bajo) que el jugador **MIN** puede garantizarse hasta el momento en el camino actual. Es una cota superior.

* **Ecuaciones de la Diapositiva (Notación):**
    * La diapositiva usa $a_s$ para $\alpha$ y $b_s$ para $\beta$.
    * $a_s$: cota inferior en un nodo MAX. Ejemplo: $a_R = 3$.
    * $b_s$: cota superior en un nodo MIN. Ejemplo: $b_A = 3$, $b_B = 2$.

* **Ejemplo Didáctico:**
    * **Valor $\alpha$ (MAX):** Imagina que estás jugando ajedrez y encuentras una secuencia de jugadas que te garantiza ganar al menos un peón (un valor de +1). Fijas tu $\alpha = 1$. A partir de ahora, solo te interesarán las jugadas que te den un resultado > 1.
    * **Valor $\beta$ (MIN):** Ahora es el turno de tu oponente. Él encuentra una jugada que le garantiza que tú no ganarás más de una torre (+5). Fija su $\beta = 5$.
    * La búsqueda continúa manteniendo estos valores.

---

### **Diapositiva 5: Condición de Poda α-β**

* **Resumen Estricto:**
    * Se generaliza la condición de poda. Se mantienen los valores $\alpha$ (el máximo de los valores de nodos MAX ancestros) y $\beta$ (el mínimo de los valores de nodos MIN ancestros).
    * Se puede podar una rama cuando $\alpha \ge \beta$. Esto significa que el mejor resultado que MAX puede forzar ($\alpha$) ya es mayor o igual al peor resultado que MIN puede forzar ($\beta$) en la rama actual, por lo que MAX nunca elegirá esta rama.
    * Se asegura que el algoritmo sigue calculando el valor minimax correcto.

* **Ecuaciones de la Diapositiva:**
    * Definición de $\alpha$ y $\beta$ en un nodo `s`:
        $$ \alpha_{s}=max_{s^{\prime}\in ancestros(s)\cup\{s\}}a_{s^{\prime}} $$
        $$ \beta_{s}=min_{s^{\prime}\in ancestros(s)\cup\{s\}}b_{s^{\prime}} $$
    * Condición de poda: Ocurre si el intervalo $[\alpha_s, \beta_s]$ no es válido, es decir, $\alpha_s \ge \beta_s$.
    * Ejemplo de la condición: $\alpha_{D}=max\{6,3\}=6$ y $\beta_{D}=min\{8,5\}=5$. Como $6 \ge 5$, se poda.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la condición matemática que permite realizar una poda Alfa-Beta?
        > **Respuesta:** Se puede podar una rama cuando $\alpha \ge \beta$.

---

### **Diapositiva 6: Efectividad de la Poda α-β**

* **Resumen Estricto:**
    * La eficiencia de la poda depende críticamente del **orden** en que se exploran los nodos hijos.
    * **Mejor caso:** Si siempre se exploran primero las mejores jugadas, la complejidad se reduce a $O(b^{d/2})$. Esto permite en la práctica buscar el doble de profundo con los mismos recursos.
    * **Peor caso:** Si se exploran las peores jugadas primero, no se realiza ninguna poda y la complejidad sigue siendo $O(b^d)$, igual que Minimax simple.

* **Ecuaciones de la Diapositiva:**
    * Complejidad en el peor orden: $O(b^{d})$
    * Complejidad en el mejor orden: $O(b^{d/2})$
    * Complejidad en orden aleatorio: $O(b^{3d/4})$ *(Corregido de la diapositiva que tenía un typo)*.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cómo afecta el orden de evaluación de movimientos a la poda Alfa-Beta?
        > **Respuesta:** Un buen orden (explorar las mejores jugadas primero) maximiza la cantidad de podas y mejora drásticamente el rendimiento. Un mal orden puede no producir ninguna poda.

---

### **Diapositiva 7 y 8: Funciones de Evaluación**

* **Resumen Estricto:**
    * Para juegos con árboles demasiado grandes, se introduce una técnica para cortar la búsqueda a una **profundidad máxima ($d_{max}$)**.
    * Cuando se alcanza esta profundidad, en lugar de tener un valor terminal (victoria/derrota), se utiliza una **Función de Evaluación ($Eval(s)$)** que estima qué tan buena es esa posición.
    * El algoritmo Minimax se ejecuta sobre este árbol de profundidad limitada, usando los valores de `Eval(s)` como si fueran los de las hojas. El resultado es un valor Minimax aproximado.

* **Ecuaciones de la Diapositiva (Recurrencia de Minimax con Límite de Profundidad):**
    * $V(s,d) = \begin{cases} Utilidad(s) & \text{si EsFinal(s)} \\ Eval(s) & \text{si } d=0 \\ max_{a} V(Suc(s,a), d-1) & \text{si Jugador(s)=Agente} \\ min_{a} V(Suc(s,a), d-1) & \text{si Jugador(s)=Oponente} \end{cases}$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es el propósito de una función de evaluación en el contexto de la búsqueda en árboles de juego?
        > **Respuesta:** Permitir que el algoritmo tome una decisión sin explorar el árbol hasta el final. Estima el valor de una posición a una profundidad de corte predefinida.

---

### **Diapositiva 9: Diseño de Funciones de Evaluación**

* **Resumen Estricto:**
    * Se listan tres propiedades deseables para una buena función de evaluación `Eval(s)`:
        1.  Debe ser consistente con la utilidad real en estados terminales (un estado de victoria debe tener mayor valor que uno de empate, y este mayor que uno de derrota).
        2.  Debe ser computacionalmente rápida, ya que se calcula muchas veces.
        3.  Su valor para estados no terminales debe estar fuertemente correlacionado con la probabilidad real de ganar desde ese estado.

* **Ecuaciones de la Diapositiva:**
    * $Eval(win) > Eval(draw) > Eval(loss)$

---

### **Diapositiva 10 y 11: Ejemplo de Función de Evaluación para Ajedrez**

* **Resumen Estricto:**
    * Se propone una función de evaluación simple para ajedrez basada en el **valor material** de las piezas.
    * Esta primera función es defectuosa porque no distingue entre un jaque mate y un empate si el material es el mismo.
    * Se redefine para incluir un término muy grande ($\aleph$) que representa la victoria o derrota, haciendo que el resultado del juego sea el factor más importante, y el material un criterio de desempate.

* **Ecuaciones de la Diapositiva:**
    * Función simple:
        $$ Eval(s)=9(D_{a}-D_{o})+5(T_{a}-T_{o}) + \dots $$
    * Función redefinida y mejorada:
        $$ Eval(s)=\aleph(R_{a}(s)-R_{o}(s)) + 9(D_{a}(s)-D_{o}(s)) + \dots $$

---

### **Diapositiva 12: Enfoque General de Funciones de Evaluación**

* **Resumen Estricto:**
    * Se presenta un enfoque general y moderno: definir `Eval` como una **función lineal ponderada de características (features)**.
    * La función extrae un vector de características $\phi(s)$ de un estado `s`, y calcula una puntuación como el producto punto con un vector de pesos `w`.

* **Ecuaciones de la Diapositiva:**
    * Función lineal de evaluación:
        $$ Eval(s;w)=w\cdot\phi(s)=\sum_{k=1}^{K}w_{k}\phi_{k}(s) $$
    * Ejemplo de vector de características $\phi(s)$ para ajedrez:
        $$ \phi(s) = (\phi_1(s), \phi_2(s), \dots) = (R_{a}-R_{o}, D_{a}-D_{o}, \dots) $$
    * Ejemplo de vector de pesos `w` y cálculo final:
        $$ w = (\aleph,9,5,3,1) \implies Eval(s)=w \cdot \phi(s) $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué es una función de evaluación basada en características?
        > **Respuesta:** Es una función que no evalúa el estado directamente, sino que extrae un conjunto de propiedades medibles (características o *features*) y las combina de forma ponderada para producir una puntuación.

---

### **Diapositiva 13, 14 y 15: Implementación**

* **Resumen Estricto:**
    * Se muestra pseudo-código para la implementación de un agente que juega usando Minimax.
    * **Diap. 13:** El bucle principal del juego, donde en cada turno se le pide a un jugador que elija una acción.
    * **Diap. 14:** La función `p.action`, que clona el estado actual del juego para no modificarlo y llama a la función `minimax` para planificar.
    * **Diapositiva 15:** La función recursiva `p.minimax`, que implementa la lógica de Minimax con límite de profundidad y función de evaluación.

* **Ecuaciones de la Diapositiva (Pseudo-código clave de la Diap. 15):**
    ```
    function minimax(G, p', d):
        if G.EsFinal(): return None, G.Utilidad(p)
        if d == 0: return None, G.Eval(p)
        
        // ... calcular valores V de los sucesores recursivamente ...
        
        if p == p': // Turno del Agente (MAX)
            return arg max V, max V
        else: // Turno del Oponente (MIN)
            return arg min V, min V
    ```

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuáles son los dos casos base (condiciones de parada) de la función recursiva de Minimax?
        > **Respuesta:** 1. Si el estado es terminal (fin del juego). 2. Si se ha alcanzado la profundidad máxima de búsqueda (`d=0`).

---

### **Diapositiva 16: Bibliografía**

* **Resumen Estricto:**
    * Se recomienda el Capítulo 5 del libro "Inteligencia Artificial: Un Enfoque Moderno" de Russell y Norvig como referencia principal.