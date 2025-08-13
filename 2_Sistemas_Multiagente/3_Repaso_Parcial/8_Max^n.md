# 📚 Guía de Estudio Detallada: Maxⁿ y Juegos Multijugador ♟️

---

### **Diapositiva 1: Portada**

* **Resumen Estricto:**
    * **Curso:** Sistemas Multiagente.
    * **Tema:** Maxⁿ (Max-ene).
    * **Autor:** Sergio Yovine, Universidad ORT Uruguay.
    * **Fecha:** 14 de mayo de 2025.

---

### **Diapositiva 2: Juegos con n>2 jugadores: Maxⁿ**

* **Resumen Estricto:**
    * Se introduce el algoritmo **Maxⁿ**, que es una generalización de Minimax para juegos con `n` jugadores (donde `n > 2`).
    * La diferencia fundamental es que ya no hay un solo oponente "minimizador". En Maxⁿ, se asume que **todos los jugadores son agentes racionales que intentan maximizar su propia utilidad individual**.
    * La utilidad en un estado final es un **vector** $u \in \mathbb{R}^n$, con un valor para cada jugador.
    * Cuando le toca jugar al jugador `p`, este elegirá la acción `a` que lo lleve a un estado sucesor cuyo vector de utilidad tenga el valor más alto en la `p`-ésima componente.

* **Ecuaciones de la Diapositiva:**
    * Utilidad en un estado final es un vector:
        $$ Utilidad(s)\in\mathbb{R}^{n} $$
    * Recurrencia de Maxⁿ:
        $$ V_{max^{n}}(s)= \begin{cases} Utilidad(s) & \text{si EsFinal(s)} \\ max_{p}\{V_{max^{n}}(Suc(s,a))|a\in A(s)\} & \text{si Jugador(s)=p} \end{cases} $$
        *(Nota: $max_p$ significa que el jugador `p` elige el vector que maximiza su propia componente)*.
    * Comparación de vectores para el jugador `p`:
        $$ u \ge_{p} v \iff u_{p} \ge v_{p} $$

* **Ejemplo Didáctico (Análisis del árbol):**
    * En el nodo `C`, es el turno del jugador `C` (el tercer jugador). Tiene dos opciones que llevan a los resultados $(1,5,2)$ y $(7,7,1)$.
    * El jugador `C` mira solo su propia utilidad (la tercera componente): ¿prefiere 2 o 1? Elige 2. Por lo tanto, el valor del nodo `C` se convierte en $(1,5,2)$.
    * En el nodo `B`, es el turno del jugador `B` (el segundo). Sus opciones dan utilidades de $(6,1,2)$ y $(5,1,1)$. Mira su componente: ¿prefiere 1 o 1? Es indiferente. En este caso, podría elegir cualquiera.
    * En el nodo raíz `A`, es el turno del jugador `A` (el primero). Compara los valores de los nodos hijos: $(1,2,6)$, $(6,1,2)$ y $(1,5,2)$ (este último es el valor propagado de C). `A` mira su utilidad: ¿prefiere 1, 6 o 1? Elige 6. Por lo tanto, la jugada óptima para `A` es la del medio.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la suposición fundamental del algoritmo Maxⁿ sobre el comportamiento de los jugadores?
        > **Respuesta:** Asume que todos los jugadores son "egoístas" y racionales, y cada uno siempre tomará la decisión que maximice su propia utilidad personal, sin importar cómo afecte a los demás.
    * **Pregunta:** ¿En qué se diferencia Maxⁿ de Minimax de 2 jugadores?
        > **Respuesta:** En Minimax hay un jugador MAX y un MIN. En Maxⁿ, todos los jugadores son MAX (maximizadores de su propia utilidad).

---

### **Diapositiva 3: Maxⁿ: Juegos de Suma Constante**

* **Resumen Estricto:**
    * Se introduce un tipo especial de juego multijugador: los **juegos de suma constante**.
    * En estos juegos, la suma de las utilidades de todos los jugadores en cualquier estado final es siempre igual a una constante `C`.
    * Esto crea una relación más competitiva, porque la ganancia de un jugador implica necesariamente una pérdida para el conjunto de los demás.
    * Se introduce una regla de **poda inmediata**: si una acción `a` le da al jugador actual el valor máximo posible `C`, puede tomar esa acción sin evaluar las demás (ya que es imposible mejorar).

* **Ecuaciones de la Diapositiva:**
    * Condición de suma constante:
        $$ \sum_{p}Utilidad_{p}(s)=C $$
    * Regla de poda inmediata:
        > Si $V_{max^{n}}(Suc(s,a))=C$ para alguna acción `a`, entonces se podan el resto de acciones.

* **Ejemplo Didáctico (Análisis del árbol de suma 10):**
    * En el nodo hoja más a la izquierda, el resultado es (5,3,2). La suma es $5+3+2=10$.
    * En el nodo de más abajo a la izquierda, es el turno del jugador 3. Compara (5,3,2) con (7,2,1). Mira su utilidad (la 3ª): prefiere 2 a 1. Así que el valor que se propaga hacia arriba es (5,3,2).
    * En el nodo superior, es el turno del jugador 1. Compara los valores que le llegan de sus tres ramas: (5,3,2), (3,5,2) y (6,4,0). Mira su utilidad (la 1ª): prefiere 5, 3 o 6. Elige 6. Su jugada óptima es la de la derecha.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué es un juego de suma constante?
        > **Respuesta:** Un juego donde la suma de las utilidades de todos los jugadores es siempre la misma en cualquier resultado final. Lo que uno gana, los demás lo pierden en conjunto.

---

### **Diapositiva 4: Maxⁿ: Poda Superficial**

* **Resumen Estricto:**
    * En juegos de suma constante, es posible realizar una **poda superficial (shallow pruning)**.
    * El principio es que si el jugador `p` encuentra una jugada que le garantiza un valor de `v`, entonces la suma de los valores para el resto de jugadores no puede superar `C - v`.
    * Esta información puede usarse para podar ramas. En el ejemplo, se muestra cómo una cota inferior para un jugador implica una cota superior para los demás, y si estas cotas demuestran que una rama no es interesante para el jugador actual, se puede podar.

* **Ecuaciones de la Diapositiva (Lógica de cotas para C=9):**
    * En el nodo `1a`, tras evaluar la primera rama, P1 sabe que puede obtener al menos 3. Su cota es $(\ge3, \le6, \le6)$.
    * En el nodo `2f`, P2 encuentra una jugada que le da 7. Sabe que puede obtener al menos 7. Esto implica que para P1 y P3, la utilidad será como máximo $9-7=2$. La cota es $(\le2, \ge7, \le2)$.
    * El jugador en `1a` compara su opción garantizada de $\ge3$ con una nueva rama `2f` que le dará como máximo $\le2$. Nunca elegirá esa rama, por lo que se puede podar.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Por qué es posible la poda superficial en juegos de suma constante?
        > **Respuesta:** Porque el valor de un jugador está directamente ligado al de los demás. Saber que un jugador puede garantizarse una puntuación `v` nos da información (una cota superior `C-v`) sobre lo que pueden obtener los otros, lo que puede permitir podas.

---

### **Diapositiva 5: Maxⁿ: Imposibilidad de Poda Profunda**

* **Resumen Estricto:**
    * Se demuestra por qué la poda profunda de estilo Alfa-Beta **no funciona** en juegos de más de 2 jugadores, incluso si son de suma constante.
    * El problema es que una elección de un jugador en el fondo del árbol (jugador 3 en `d`) no solo afecta al jugador en la raíz (jugador 1 en `a`), sino también a los jugadores intermedios (jugador 2 en `c`).
    * Una rama no se puede podar solo porque es mala para el jugador de la raíz, porque un jugador intermedio podría elegirla si es buena para él, cambiando así el resultado final de una forma impredecible para un simple par de cotas $\alpha-\beta$.
    * **Conclusión:** El valor de un nodo `f` en el fondo del árbol puede afectar el valor final en la raíz `a`, por lo que no se puede podar `f` de forma segura.

* **Ejemplo Didáctico:**
    * La lógica de Alfa-Beta es: "Yo (MAX) ya tengo una opción que me da 5. Ahora exploro una rama donde mi oponente (MIN) puede forzar que yo obtenga como máximo 4. La podo".
    * En Maxⁿ esto falla. "Yo (P1) ya tengo una opción que me da 5. Ahora exploro una rama donde P3 puede forzar que yo obtenga como máximo 4. ¡Pero no puedo podar! Porque quizás esa misma jugada de P3 es tan mala para P2, que P2 se verá forzado a hacer otra jugada que indirectamente me beneficie y me termine dando un valor de 6". Las alianzas y perjuicios indirectos rompen la poda profunda.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Por qué la poda Alfa-Beta tradicional no funciona en juegos con más de 2 jugadores?
        > **Respuesta:** Porque las cotas de dos jugadores (el mejor valor para MAX y el mejor para MIN) no son suficientes para capturar las complejas interacciones y posibles "alianzas" o perjuicios indirectos entre múltiples jugadores maximizadores.

---

### **Diapositiva 6: Bibliografía**

* **Resumen Estricto:**
    * Se lista la bibliografía de referencia, incluyendo el libro de Russell y Norvig y los papers de investigación de Korf y Luckhardt sobre el tema.