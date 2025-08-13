# 📚 Guía de Estudio Detallada: Información Imperfecta y CFR 🃏

---

### **Diapositiva 1: Portada**

* **Resumen Estricto:**
    * **Curso:** Sistemas Multiagente.
    * **Tema:** Juegos Alternados de Información Imperfecta y Counterfactual Regret Minimization (CFR).
    * **Autor:** Sergio Yovine, Universidad ORT Uruguay.
    * **Fecha:** 6 de junio de 2025.

---

### **Diapositiva 2: Ejemplo Visual: Kuhn Poker**

* **Resumen Estricto:**
    * Se presenta el **Kuhn Poker**, una versión simplificada del póker con solo 3 cartas (J, Q, K), como el ejemplo principal para ilustrar los conceptos.
    * El diagrama muestra el árbol del juego. Los nodos `1` y `2` son los turnos de los jugadores. El nodo `C` es un movimiento de "azar" (el reparto de cartas).
    * Las líneas curvas que agrupan nodos son **Information Sets (infosets)**, la característica clave de los juegos con información imperfecta.

* **Ejemplo Didáctico (¿Qué es un Infoset?):**
    * Observa los dos nodos superiores del Jugador 1, que están dentro de un gran infoset.
        * En el nodo de la izquierda, J1 tiene la K y J2 tiene la J.
        * En el nodo de la derecha, J1 tiene la Q y J2 tiene la K.
    * El infoset significa que **el Jugador 1 no sabe en cuál de esos dos nodos se encuentra realmente**. Él conoce su propia carta (ej. la K), pero no la de su oponente. Como no puede distinguir estos dos estados del juego, está obligado a usar la misma estrategia (la misma probabilidad de `bet` o `pass`) en ambos casos. Esto es la información imperfecta.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué representa un "information set" (infoset) en un árbol de juego?
        > **Respuesta:** Un conjunto de nodos de decisión para un jugador donde este no puede distinguir en cuál de ellos se encuentra, porque no tiene toda la información del juego (ej. no puede ver las cartas del oponente).

---

### **Diapositiva 3: Historias**

* **Resumen Estricto:**
    * Se define la notación formal para las **historias**.
    * Una historia `h` es una secuencia de acciones desde el inicio.
    * `Z` es el conjunto de historias terminales (donde termina la partida).
    * $A(h)$ es el conjunto de acciones válidas que se pueden tomar después de la historia `h`.

* **Ecuaciones de la Diapositiva (Notación):**
    * Historia vacía (inicio del juego): $\lambda$
    * Conjunto de todas las historias: $H$
    * `h` es prefijo de `h'`: $h \subset h'$
    * `i`-ésima acción de la historia `h`: $h_i$
    * Prefijo de `h` hasta antes de la acción `i`: $h_{<i}$
    * Conjunto de historias terminales: $Z \subseteq H$
    * Acciones disponibles después de la historia `h`: $A(h)=\{a|ha\in H\}$

---

### **Diapositiva 4: Infosets**

* **Resumen Estricto:**
    * Se define formalmente un **Information Set** `I` para un jugador `p`.
    * Es un subconjunto de todos los puntos de decisión de `p`.
    * **Condición clave:** Para dos historias `h` y `h'` que pertenecen al mismo infoset `I`, el jugador `p` no puede distinguirlas.
    * **Propiedad clave:** El conjunto de acciones disponibles debe ser el mismo para todas las historias dentro de un mismo infoset ($A(h) = A(h')$).

* **Ecuaciones de la Diapositiva:**
    * Partición del juego en infosets: $H=\bigcup_{p}\mathcal{I}_{p}$
    * Conjunto de puntos de decisión de un jugador: $\mathcal{I}_{p}=\{h|Agente(h)=p\}$
    * Propiedades de un infoset `I`:
        * $Agente(I) = p$ (a qué jugador pertenece).
        * Para toda $h, h' \in I$, $A(h)=A(h^{\prime})$.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué dos condiciones debe cumplir un conjunto de nodos para ser un infoset válido?
        > **Respuesta:** 1) Todos los nodos deben pertenecer al mismo jugador. 2) El conjunto de acciones disponibles debe ser idéntico en todos los nodos del conjunto.

---

### **Diapositiva 5: Estrategias en Juegos de Información Imperfecta**

* **Resumen Estricto:**
    * Una **estrategia** $\pi$ en estos juegos es una función que mapea cada **infoset** `I` a una distribución de probabilidad sobre las acciones disponibles en ese infoset.
    * Esto es diferente a los juegos de información perfecta, donde la estrategia mapeaba *estados* a acciones. Aquí, se mapea la *información que tiene el jugador*.
    * Se define $\pi_{I \to a}$ como una estrategia idéntica a $\pi$ en todas partes, excepto en el infoset `I`, donde se juega la acción pura `a`.

* **Ecuaciones de la Diapositiva:**
    * Estrategia para un infoset `I`: $\pi(I)$
    * Estrategia del jugador `p` en el infoset `I`: $\pi_p(I)$
    * Estrategia "what if" (jugar `a` en `I`): $\pi_{I\rightarrow a}$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la diferencia fundamental entre una estrategia en un juego de información perfecta y una en uno de información imperfecta?
        > **Respuesta:** En información perfecta, la estrategia decide qué hacer en cada *estado* del juego. En información imperfecta, la estrategia decide qué hacer en cada *infoset* (en cada situación de información disponible para el jugador).

---

### **Diapositiva 6 y 7: Probabilidades y Probabilidad Contrafáctica**

* **Resumen Estricto:**
    * Se definen las probabilidades de llegar a una historia o a un infoset.
    * $P_p^\pi(h)$ es la probabilidad de que las **propias acciones** del jugador `p` contribuyan a llegar a `h`.
    * $P^\pi(h)$ es la probabilidad total de que ocurra `h`, multiplicando las contribuciones de todos los jugadores y del azar.
    * Se introduce el concepto crucial de **probabilidad contrafáctica** ($P_{-p}^\pi(h)$). Esta es la probabilidad de llegar a `h` asumiendo que el jugador `p` siempre toma las acciones necesarias para llegar a `h`. Mide la probabilidad con la que **el azar y los oponentes** "permiten" que se llegue a esa historia.

* **Ecuaciones Clave de las Diapositivas:**
    * Probabilidad de la contribución del jugador p a la historia h:
        $$ P_{p}^{\pi}(h)=\prod_{i,p_{i}=p}\pi_{p_{i}}(h_{<i})(h_{i}) $$
    * Probabilidad total de la historia h:
        $$ P^{\pi}(h)=\prod_{p}P_{p}^{\pi}(h) $$
    * Probabilidad de llegar al infoset I:
        $$ P^{\pi}(I)=\sum_{h\in I}P^{\pi}(h) $$
    * Probabilidad contrafáctica (sin la contribución de p):
        $$ P_{-p}^{\pi}(h)=\prod_{q \ne p}P_{q}^{\pi}(h) $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué mide la probabilidad contrafáctica $P_{-p}^\pi(h)$?
        > **Respuesta:** Mide la probabilidad de que la historia `h` ocurra debido a las acciones del azar y de todos los oponentes, asumiendo que el jugador `p` coopera y juega su parte del camino hacia `h`.

---

### **Diapositiva 8: Utilidades**

* **Resumen Estricto:**
    * Se definen las utilidades en este marco.
    * $u_p(z)$: Es la recompensa final que obtiene el jugador `p` si el juego termina con la historia `z`.
    * $u_p^\pi$: Es la utilidad esperada total para el jugador `p` si todos juegan según la estrategia conjunta $\pi$.
    * $v_p^\pi(h)$: Es el valor esperado para `p` desde la historia `h` en adelante.

* **Ecuaciones de la Diapositiva:**
    * Utilidad de una historia terminal `z`: $u_{p}(z)$
    * Utilidad esperada de una estrategia $\pi$: $u_{p}^{\pi}=\sum_{z\in Z}P^{\pi}(z)u_{p}(z)$
    * Valor de una historia no terminal `h`: $v_{p}^{\pi}(h)=\sum_{z\in Z,h\subset z}P^{\pi}(h,z)u_{p}(z)$

---

### **Diapositiva 9: Regrets Contrafácticos**

* **Resumen Estricto:**
    * Se define el **regret contrafáctico**, la idea central de CFR.
    * El arrepentimiento por no haber jugado la acción `a` en una historia `h` es la ganancia de utilidad que se hubiera obtenido, pero ponderado por la **probabilidad contrafáctica** de que los otros jugadores y el azar te hubieran llevado a esa situación.
    * El regret total de una acción `a` en un infoset `I` es la suma de estos regrets ponderados sobre todas las historias `h` que componen `I`.

* **Ecuaciones de la Diapositiva:**
    * Regret contrafáctico en una historia `h` por no jugar `a`:
        $$ r_{h}^{\pi}(a)=P_{-p}^{\pi}(h)(v_{p}^{\pi_{I\rightarrow a}}(h)-v_{p}^{\pi}(h)) $$
    * Regret contrafáctico en un infoset `I` por no jugar `a`:
        $$ r_{I}^{\pi}(a)=\sum_{h\in I}r_{h}^{\pi}(a) $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Por qué se multiplica el arrepentimiento por la probabilidad contrafáctica?
        > **Respuesta:** Para darle más importancia al arrepentimiento en situaciones a las que es más probable llegar. Si te arrepientes de algo en una situación muy improbable (porque los oponentes casi nunca te dejarían llegar ahí), ese arrepentimiento importa menos.

---

### **Diapositiva 10, 11 y 12: Counterfactual Regret Minimization (CFR)**

* **Resumen Estricto:**
    * Se presenta el algoritmo **CFR**, que busca minimizar el arrepentimiento contrafáctico a lo largo de muchas iteraciones.
    * **(Diap. 10)** El bucle principal `Train()` itera muchas veces, y en cada iteración, recorre el árbol para cada jugador, actualizando sus estrategias.
    * **(Diap. 11)** Se muestra el pseudo-código de la función recursiva `CFR(h, p, P)`. Esta función atraviesa el árbol y, a la vuelta de la recursión, si el turno es del jugador que se está entrenando (`q=p`), llama a la función `update`.
    * **(Diap. 12)** La función `update` es el núcleo del aprendizaje: calcula el regret contrafáctico para cada acción del infoset actual y lo suma al regret acumulado. Luego, usa **regret matching** (visto en la clase anterior) para actualizar la estrategia de ese infoset.

* **Ecuaciones Clave:**
    * Llamada principal recursiva: `CFR(h, p, P)`
    * Cálculo de la utilidad esperada `v` en un nodo:
        $$ v=\sum_{a\in A(I)}(\pi(I)[a] \cdot u[a]) $$
    * Actualización del regret acumulado $r_I$:
        $$ r_{I} = r_{I}+\prod_{q\ne p}P_{q} \cdot (u-v) $$
    * Dónde `u` es el vector de utilidades de cada acción y `v` es el valor esperado del nodo.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué dos algoritmos combina CFR?
        > **Respuesta:** Combina una traversa recursiva del árbol de juego con el algoritmo de **Regret Matching** en cada infoset.
    * **Pregunta:** ¿En qué momento del algoritmo CFR se actualiza la estrategia de un jugador?
        > **Respuesta:** Se actualiza en la fase de "vuelta" o "backup" de la recursión. Después de calcular los valores de las acciones hijas, la función `update` usa esos valores para calcular el regret y ajustar la estrategia del infoset actual.

---
