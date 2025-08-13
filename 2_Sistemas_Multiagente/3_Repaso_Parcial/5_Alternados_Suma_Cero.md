# 📚 Guía de Estudio Detallada: Juegos Alternados y Minimax ♟️

---

### **Diapositiva 1: Portada**

* **Resumen Estricto:**
    * **Curso:** Sistemas Multiagente.
    * **Tema:** Juegos Alternados de Suma Cero para 2 Jugadores.
    * **Autor:** Sergio Yovine, Universidad ORT Uruguay.
    * **Fecha:** 30 de abril de 2025.

---

### **Diapositiva 2: Un Juego Alternado Sencillo**

* **Resumen Estricto:**
    * Se introduce un juego simple de 2 turnos para 2 jugadores.
        * **Turno 1 (Ustedes):** Eligen una de tres cajas (A, B, C). Su objetivo es maximizar el resultado final.
        * **Turno 2 (Yo):** Elijo un número de dentro de la caja que ustedes eligieron. Mi objetivo es minimizar el resultado final (implícito por ser un juego de suma cero).
    * Se muestran los números contenidos en cada caja.

* **Ejemplo Didáctico:**
    * Esto es la base de los juegos de estrategia por turnos, como el ajedrez. Un jugador hace un movimiento, y el otro responde. Tu elección inicial (qué caja elegir) depende de cómo crees que yo responderé. Si eliges la caja C que contiene {-5, 15}, ¿qué número crees que elegiré yo, sabiendo que quiero minimizar tu puntuación? Probablemente el -5. Debes anticipar mi jugada para hacer la tuya.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué significa que un juego sea "alternado"?
        > **Respuesta:** Que los jugadores no actúan simultáneamente, sino que toman turnos para hacer sus movimientos.

---

### **Diapositiva 3: Árbol de Juego**

* **Resumen Estricto:**
    * Se visualiza el "juego de las cajas" como un **árbol de juego**.
    * La idea es que cada nodo del árbol representa un punto de decisión para un jugador.
    * Los nodos hoja (al final del árbol) representan los resultados finales posibles del juego.

* **Ejemplo Didáctico:**
    * El nodo raíz (la cima del árbol) es el turno del Jugador 1 (el maximizador). Sus tres ramas son las acciones A, B y C.
    * Cada uno de esos tres nodos hijos es un punto de decisión para el Jugador 2 (el minimizador). Desde el nodo "A", el Jugador 2 tiene dos ramas que llevan a las hojas -50 y 50.
    * Este árbol representa todas las secuencias posibles del juego.

* **Flashcards 🧠:**
    * **Pregunta:** En un árbol de juego, ¿qué representan los nodos hoja?
        > **Respuesta:** Los estados terminales del juego y sus utilidades o resultados finales.

---

### **Diapositiva 4: Definición Formal de un Juego Alternado**

* **Resumen Estricto:**
    * Se definen formalmente los componentes de un juego alternado de suma cero para dos jugadores ({Agente, Oponente}).
    * Incluye un estado inicial, las acciones posibles en cada estado, una función sucesor (determinista), una función para saber si un estado es terminal, una función de utilidad (desde la perspectiva del Agente) y una función que indica a qué jugador le toca jugar en cada estado.

* **Ecuaciones de la Diapositiva (Definiciones de Componentes):**
    * Estado inicial: $s_{start}$
    * Acciones posibles: $Acciones(s)$
    * Estado siguiente: $Suc(s,a)$
    * Comprobación de estado final: $EsFinal(s)$
    * Utilidad del agente en un estado final: $Utilidad(s)$
    * Jugador activo en el estado `s`: $Jugador(s) \in \{\text{Agente, Oponente}\}$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la diferencia clave entre la función de transición `T` de un juego estocástico y la función `Suc(s,a)` de un juego alternado (determinista)?
        > **Respuesta:** `T` es probabilística (devuelve una distribución de probabilidad sobre los siguientes estados). `Suc(s,a)` es determinista (devuelve un único estado sucesor).

---

### **Diapositiva 5: Políticas**

* **Resumen Estricto:**
    * Se define qué es una política o estrategia en este contexto.
    * **Política pura o determinística:** Un mapeo de cada estado a una única acción. Te dice exactamente qué hacer en cada situación.
    * **Política mixta o estocástica:** Un mapeo de cada estado a una distribución de probabilidad sobre las acciones. Te dice con qué probabilidad jugar cada acción posible.

* **Ecuaciones de la Diapositiva:**
    * Política pura: $\pi_{p}(s)\in Acciones(s)$
    * Política mixta: $\pi_{p}(s,a)\in[0,1]$ (tal que $\sum_a \pi_p(s,a) = 1$)

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué es una política en el contexto de un árbol de juego?
        > **Respuesta:** Es un "libro de jugadas" completo que le dice a un agente qué acción tomar (o con qué probabilidad tomar cada acción) para cualquier estado posible del juego.

---

### **Diapositiva 6: Evaluando un Juego (Ejemplo)**

* **Resumen Estricto:**
    * Se muestra cómo calcular el valor de un juego si las políticas de ambos jugadores son fijas.
    * **Política del Agente:** Elegir siempre la caja A.
    * **Política del Oponente:** En cualquier caja, elegir cada número con un 50% de probabilidad (uniforme).
    * El valor esperado resultante para el agente al inicio del juego es 0.

* **Ecuaciones de la Diapositiva:**
    * $\pi_{agente}(s_{start})=A$
    * $\pi_{oponente}(s,a)=0,5$
    * $V_{\pi_{agente},\pi_{oponente}}(s_{start})=0$

* **Ejemplo Didáctico Extendido:**
    * El agente está obligado por su política a elegir la rama "A".
    * Ahora es el turno del oponente. Su política es estocástica: elige la rama "-50" con probabilidad 0.5 y la rama "50" con probabilidad 0.5.
    * El valor de este nodo de decisión del oponente es el valor esperado:
        $$ (0.5 \times -50) + (0.5 \times 50) = -25 + 25 = 0 $$
    * Como la primera acción del agente lleva a un nodo con valor 0, el valor total del juego bajo estas políticas es 0.

---

### **Diapositiva 7: Recurrencia de Evaluación de Juegos**

* **Resumen Estricto:**
    * Se presenta la fórmula recursiva general para calcular el valor de un estado `s` para cualquier par de políticas.
    * Si `s` es final, su valor es `Utilidad(s)`.
    * Si no es final, su valor depende de a quién le toque jugar:
        * Si juega el Agente, se promedian los valores de los estados sucesores según la política del Agente.
        * Si juega el Oponente, se promedian según la política del Oponente.

* **Ecuaciones de la Diapositiva:**
    * $V_{\pi_{agente},\pi_{oponente}}(s) = \begin{cases} Utilidad(s) & \text{si EsFinal(s)} \\ \sum_{a \in Acciones(s)} \pi_{agente}(s,a) V_{\pi_{agente},\pi_{oponente}}(Suc(s,a)) & \text{si Jugador(s)=Agente} \\ \sum_{a \in Acciones(s)} \pi_{oponente}(s,a) V_{\pi_{agente},\pi_{oponente}}(Suc(s,a)) & \text{si Jugador(s)=Oponente} \end{cases}$

---

### **Diapositiva 8 y 9: ExpectiMax**

* **Resumen Estricto:**
    * Se introduce el algoritmo **ExpectiMax**, que se usa cuando el Agente (MAX) es racional, pero el oponente no es un minimizador perfecto, sino que juega de forma estocástica (en este caso, al azar).
    * El Agente (nodo MAX) elige la acción que lleva al sucesor con el **máximo** valor.
    * El Oponente (nodo CHANCE) no elige, sino que sus nodos se evalúan calculando el **valor esperado (promedio)** de sus sucesores.
    * Para el juego de las cajas, el valor ExpectiMax es 5, y la acción óptima para el agente es elegir la caja C.

* **Ecuaciones de la Diapositiva 9 (Recurrencia de ExpectiMax):**
    * $V_{expmax}(s) = \begin{cases} Utilidad(s) & \text{si EsFinal(s)} \\ max_{a \in Acciones(s)} V_{expmax}(Suc(s,a)) & \text{si Jugador(s)=Agente} \\ \sum_{a \in Acciones(s)} \pi_{oponente}(s,a) V_{expmax}(Suc(s,a)) & \text{si Jugador(s)=Oponente} \end{cases}$
    * *(Nota: Se asume que $V_{expmax}$ es una abreviación de $V_{\pi_{expmax},\pi_{oponente}}$)*

* **Ejemplo Didáctico Extendido:**
    1.  **Evaluar Nodos del Oponente (Promedio):**
        * Valor de la caja A: $(-50 + 50) / 2 = 0$.
        * Valor de la caja B: $(1 + 3) / 2 = 2$.
        * Valor de la caja C: $(-5 + 15) / 2 = 5$.
    2.  **Evaluar Nodo del Agente (Máximo):**
        * El agente ve los posibles resultados: 0, 2, 5.
        * Elige el máximo: **5**. Por lo tanto, su acción óptima es elegir la caja C.

---

### **Diapositiva 10 y 11: MiniMax**

* **Resumen Estricto:**
    * Se introduce el algoritmo **MiniMax**, que se usa cuando el Agente (MAX) juega contra un Oponente perfecto y racional (MIN).
    * El Agente (nodo MAX) elige la acción que lleva al sucesor con el **máximo** valor.
    * El Oponente (nodo MIN) elige la acción que lleva al sucesor con el **mínimo** valor.
    * Para el juego de las cajas, el valor Minimax es 1, y la acción óptima para el agente es elegir la caja B.

* **Ecuaciones de la Diapositiva 11 (Recurrencia de MiniMax):**
    * $V_{minimax}(s) = \begin{cases} Utilidad(s) & \text{si EsFinal(s)} \\ max_{a \in Acciones(s)} V_{minimax}(Suc(s,a)) & \text{si Jugador(s)=Agente} \\ min_{a \in Acciones(s)} V_{minimax}(Suc(s,a)) & \text{si Jugador(s)=Oponente} \end{cases}$

* **Ejemplo Didáctico Extendido:**
    1.  **Evaluar Nodos del Oponente (Mínimo):**
        * Valor de la caja A: $min(-50, 50) = -50$.
        * Valor de la caja B: $min(1, 3) = 1$.
        * Valor de la caja C: $min(-5, 15) = -5$.
    2.  **Evaluar Nodo del Agente (Máximo):**
        * El agente ve los resultados garantizados que el oponente le dejará: -50, 1, -5.
        * Elige el máximo de estos peores casos: **1**. Por lo tanto, su acción óptima es elegir la caja B.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la diferencia fundamental en cómo se evalúan los nodos del oponente en ExpectiMax y en MiniMax?
        > **Respuesta:** En ExpectiMax, los nodos del oponente se evalúan con un **promedio** (valor esperado). En MiniMax, se evalúan con el **mínimo**.
    * **Pregunta:** ¿Cuándo deberías usar MiniMax y cuándo ExpectiMax?
        > **Respuesta:** Usa MiniMax si juegas contra un oponente perfectamente racional que intentará minimizar tu puntuación. Usa ExpectiMax si juegas contra un oponente que actúa de forma aleatoria o no óptima.

---

### **Diapositiva 12, 13 y 14: Propiedades de MiniMax**

* **Resumen Estricto:**
    * **(Diap. 12)** La política minimax del agente es una mejor respuesta contra la política minimizadora del oponente. No puedes mejorar tu resultado si el oponente juega perfecto.
    * **(Diapositiva 13)** El valor minimax es una **cota inferior**: jugando la estrategia minimax, tienes garantizado obtener *al menos* ese valor, sin importar lo que haga el oponente (incluso si juega mal y te beneficia).
    * **(Diapositiva 14)** En general, la política minimax **no es la mejor respuesta** contra un oponente no óptimo. Es demasiado pesimista. Contra un oponente aleatorio, la política Expectimax da un mejor resultado.

* **Ecuaciones Clave:**
    * $V_{\pi_{minimax},\pi_{min}}(s) \ge V_{\pi_{agente},\pi_{min}}(s)$ (No puedes mejorar contra un oponente perfecto).
    * $V_{\pi_{minimax},\pi_{min}}(s) \le V_{\pi_{minimax},\pi_{oponente}}(s)$ (Tu resultado será al menos el valor minimax, y posiblemente mejor si el oponente se equivoca).
    * En el ejemplo: $V_{\pi_{minimax},\pi_{oponente}}(s) = 2 < V_{\pi_{expmax},\pi_{oponente}}(s) = 5$ (Expectimax es mejor contra un oponente aleatorio).

---

### **Diapositiva 15, 16 y 17: Introduciendo Azar (ExpectiMiniMax)**

* **Resumen Estricto:**
    * Se introduce un elemento de azar en el juego: una moneda que puede cambiar la caja elegida.
    * Esto añade un tercer tipo de nodo al árbol: **Nodos de Azar (Chance)**.
    * El algoritmo para resolver estos juegos es **ExpectiMiniMax**.
    * **(Diapositiva 17)** La recurrencia es una combinación de los anteriores: los nodos del Agente maximizan, los del Oponente minimizan y los nodos de Azar calculan el valor esperado (promedio).

* **Ecuaciones de la Diapositiva 17 (Recurrencia ExpectiMiniMax):**
    * $V(s) = \begin{cases} Utilidad(s) & \text{si EsFinal(s)} \\ max_{a} V(Suc(s,a)) & \text{si Jugador(s)=Agente} \\ min_{a} V(Suc(s,a)) & \text{si Jugador(s)=Oponente} \\ \sum_{a} \pi_{azar}(s,a) V(Suc(s,a)) & \text{si Jugador(s)=Moneda} \end{cases}$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué tipo de nodo se añade al árbol de juego para modelar eventos aleatorios?
        > **Respuesta:** Un Nodo de Azar (Chance node).
    * **Pregunta:** ¿Cómo se calcula el valor de un Nodo de Azar en el algoritmo ExpectiMiniMax?
        > **Respuesta:** Se calcula el valor esperado, es decir, el promedio ponderado de los valores de sus nodos hijos, usando las probabilidades del evento aleatorio.

---

### **Diapositiva 18: Bibliografía**

* **Resumen Estricto:**
    * Se recomienda el Capítulo 5 del libro "Inteligencia Artificial: Un Enfoque Moderno" de Russell y Norvig como referencia principal para estos temas.