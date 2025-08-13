# 📚 Guía de Estudio Detallada: Juegos Estocásticos 🕹️

---

### **Diapositiva 1: Portada**

* **Resumen Estricto:**
    * **Curso:** Sistemas Multiagente.
    * **Tema:** Juegos Estocásticos.
    * **Autor:** Sergio Yovine, Universidad ORT Uruguay.
    * **Fecha:** 9 de abril de 2025.

---

### **Diapositiva 2: Procesos de Decisión de Markov con Múltiples Jugadores**

* **Resumen Estricto:**
    * Se define formalmente un **Juego Estocástico** (también conocido como Juego de Markov) como una generalización de los Procesos de Decisión de Markov (MDP) para múltiples jugadores.
    * Los componentes clave son: un conjunto de agentes, un conjunto de estados del entorno, una función que define los estados terminales, una distribución de probabilidad de los estados iniciales, un conjunto de acciones conjuntas (una por cada agente), una función de transición de estados (probabilística) y una función de recompensa que asigna un vector de recompensas (una para cada agente) por cada transición.

* **Ecuaciones de la Diapositiva:**
    * Conjunto de agentes: $P=\{1,...,n\}$
    * Conjunto de estados: $S$
    * Función de estado terminal: $EsFinal: S\rightarrow\mathbb{B}$
    * Distribución de estados iniciales: $\mu:S\rightarrow[0,1]$ con la condición $\sum_{s}\mu(s)=1$ y $\mu(s)=0$ si $EsFinal(s)$.
    * Acciones conjuntas: $A=A_{1}\times...\times A_{n}$
    * Función de transición: $T:S\times A\times S\rightarrow[0,1]$ con la condición $\sum_{s^{\prime}}T(s^{\prime}|s,a)=1$.
    * Función de recompensa (vectorial): $R:S\times A\times S\rightarrow\mathbb{R}^{n}$

* **Ejemplo Didáctico:**
    * Imagina un juego de fútbol 2 vs 2.
        * **Estados (S):** La posición del balón y de los 4 jugadores en el campo.
        * **Agentes (P):** Los 4 jugadores.
        * **Acciones (A):** Para cada jugador, {correr, pasar, chutar, esperar}. Una acción conjunta es lo que los 4 hacen a la vez.
        * **Transición (T):** Si un jugador chuta desde una posición (estado `s`), la función `T` da la probabilidad de que el balón termine en otra posición (nuevo estado `s'`). Es probabilística porque el tiro puede ser impreciso.
        * **Recompensa (R):** Si el equipo A marca un gol, el vector de recompensa podría ser `(10, 10, -10, -10)`, donde los dos primeros jugadores son del equipo A y los otros dos del B.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la diferencia fundamental entre un Proceso de Decisión de Markov (MDP) y un Juego Estocástico?
        > **Respuesta:** Un MDP tiene un solo agente. Un Juego Estocástico es la generalización para múltiples agentes. La transición y la recompensa dependen de las acciones conjuntas de *todos* los agentes.
    * **Pregunta:** ¿Por qué la función de recompensa $R$ produce un vector en $\mathbb{R}^n$?
        > **Respuesta:** Porque en un juego con `n` agentes, cada uno puede recibir una recompensa diferente por la misma transición.

---

### **Diapositiva 3: Historias y Episodios**

* **Resumen Estricto:**
    * Una **historia** ($h^t$) es una secuencia de estados y acciones que han ocurrido desde el inicio del juego hasta el tiempo `t`.
    * Un **episodio** es una historia completa, que termina cuando se alcanza un estado terminal.
    * La progresión de la historia depende de 3 cosas: dónde empieza (distribución $\mu$), cómo juegan los agentes (estrategias $\pi_i$) y cómo responde el entorno (transiciones $T$).

* **Ecuaciones de la Diapositiva:**
    * Secuencia de una historia:
        $$ h^{t}=s^{0},a^{0},...,s^{t-1},a^{t-1},s^{t} $$
    * Origen de cada elemento:
        $$ s^{0}\sim\mu $$
        $$ a_{i}^{t}\sim\pi_{i}(\cdot|h^{t}) $$
        $$ s^{t+1}\sim T(\cdot|s^{t},a^{t}) $$
    * Condición de fin de un episodio:
        $$ EsFinal(s^{t}) $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la diferencia entre una historia y un episodio?
        > **Respuesta:** Una historia es una secuencia de eventos hasta un momento `t` cualquiera. Un episodio es una historia completa, desde el principio hasta el fin del juego.

---

### **Diapositiva 4: Retorno Esperado**

* **Resumen Estricto:**
    * El **retorno** ($u_i$) es la suma de recompensas que un agente recibe a lo largo de una historia. Las recompensas futuras se "descuentan" con un factor gamma ($\gamma$).
    * El **retorno esperado** ($U_i$) es el objetivo del agente: maximizar el retorno promedio que obtendría si jugara infinitas veces con la misma estrategia. Se calcula promediando los retornos de todas las posibles historias.

* **Ecuaciones de la Diapositiva:**
    * Retorno (descontado) de una historia:
        $$ u_{i}(h^{t})=\sum_{k=0}^{t-1}\gamma^{k}R_{i}(s^{k},a^{k},s^{k+1}), \quad \text{con } \gamma\in(0,1) $$
    * Retorno esperado de una estrategia $\pi$:
        $$ U_{i}(\pi)=lim_{t\rightarrow\infty}\mathbb{E}_{h^{t}\sim(\pi,\mu,T)}[u_{i}(h^{t})] $$
    * Probabilidad de una historia específica:
        $$ Pr(h^{t}|\pi,T,\mu)=\mu(s^{0})\prod_{k=0}^{t-1}\pi(a^{k}|h^{k})T(s^{k+1}|s^{k},a^{k}) $$

* **Ejemplo Didáctico (Factor de Descuento $\gamma$):**
    * Imagina que puedes recibir 10€ ahora o 10€ mañana. Prefieres ahora. El factor $\gamma$ modela esta impaciencia. Si $\gamma=0.9$, recibir 10€ en el siguiente paso de tiempo solo vale $0.9 \times 10 = 9€$ para ti hoy. Esto hace que los agentes prefieran recompensas más tempranas que tardías, y es matemáticamente crucial para que las sumas infinitas no diverjan.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Por qué se utiliza un factor de descuento $\gamma < 1$?
        > **Respuesta:** Por dos razones: 1) Refleja la preferencia por recompensas inmediatas sobre las futuras. 2) Asegura que la suma infinita de recompensas converja a un valor finito.

---

### **Diapositiva 5: Retorno Esperado (Recursivo)**

* **Resumen Estricto:**
    * Se presenta la famosa relación recursiva (o de Bellman) para los juegos estocásticos.
    * Introduce el **valor de estado V** (qué tan bueno es estar en un estado) y el **valor de acción Q** (qué tan bueno es tomar una acción en un estado).
    * El valor de un estado ($V_i^\pi$) es el promedio ponderado de los valores Q de las acciones que se pueden tomar desde allí.
    * El valor de una acción conjunta ($Q_i^\pi$) es la recompensa inmediata más el valor descontado del estado al que se transita.

* **Ecuaciones de la Diapositiva:**
    * Valor de estado (o historia) V:
        $$ V_{i}^{\pi}(h^{t})=\sum_{a^{t}\in A}\pi(a^{t}|h^{t})Q_{i}^{\pi}(h^{t},a^{t}) $$
    * Valor de acción Q:
        $$ Q_{i}^{\pi}(h^{t},a^{t})=\sum_{s^{t+1}\in S}T(s^{t+1}|s^{t},a^{t})[R_{i}(s^{t},a^{t},s^{t+1})+\gamma V_{i}^{\pi}(h^{t},a^{t},s^{t+1})] $$
    * Retorno esperado total:
        $$ U_{i}(\pi)=\mathbb{E}_{s^{0}\sim\mu}[V_{i}^{\pi}(s^{0})] $$

* **Flashcards 🧠:**
    * **Pregunta:** En tus propias palabras, ¿cuál es la relación entre $V_i^\pi$ y $Q_i^\pi$?
        > **Respuesta:** $V_i^\pi(s)$ es el valor de *estar* en el estado `s`. $Q_i^\pi(s,a)$ es el valor de *hacer* la acción conjunta `a` desde el estado `s`. El valor de estar en un sitio es el valor promedio de las cosas que puedes hacer desde allí.

---

### **Diapositiva 6: Independent Q-learning (IQL)**

* **Resumen Estricto:**
    * **Idea:** Cada agente aprende de forma completamente independiente, como si fuera el único agente en el mundo y los demás fueran parte del entorno.
    * **Algoritmo:** Cada agente `i` aprende su propia función de valor $Q_i(s, a_i)$, que solo depende de su propia acción $a_i$. Utiliza la regla de actualización estándar de Q-learning.
    * **Ventaja:** Muy simple de implementar y escalar.
    * **Desventaja:** La suposición de que el entorno es estacionario se rompe (porque los otros agentes también están aprendiendo y cambiando), por lo que el algoritmo puede no converger a un Equilibrio de Nash.

* **Ecuaciones de la Diapositiva:**
    * Inicialización: $Q_{i}(s,a_{i})=0$
    * Selección de acción ($\epsilon$-greedy): $a_{i}=arg~max_{a_{i}^{\prime}}Q_{i}(s,a_{i}^{\prime})$
    * Regla de actualización:
        $$ Q_{i}(s,a_{i})\leftarrow Q_{i}(s,a_{i})+\alpha[r_{i}+\gamma~max_{a_{i}^{\prime}}Q_{i}(s^{\prime},a_{i}^{\prime})-Q_{i}(s,a_{i})] $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la principal simplificación (y problema) de Independent Q-learning?
        > **Respuesta:** Simplifica el problema al hacer que cada agente ignore las acciones de los demás, aprendiendo una Q-función $Q_i(s, a_i)$. El problema es que al ignorarlos, el entorno se vuelve no estacionario y la convergencia no está garantizada.

---

### **Diapositiva 7: Joint-Action Learning con Teoría de Juegos (JAL-GT)**

* **Resumen Estricto:**
    * **Idea:** Los agentes aprenden los valores de las **acciones conjuntas** ($Q_j(s,a)$) y en cada estado, tratan esos valores como una matriz de juego de un solo paso.
    * **Algoritmo:** En un estado `s`, resuelven el juego definido por $Q(s, \cdot)$ para encontrar un Equilibrio de Nash $\pi$, y luego juegan una acción según esa estrategia de equilibrio.
    * La actualización del valor Q utiliza el **valor del equilibrio** en el estado siguiente, $Value_j(\Gamma_{s'})$, en lugar del simple `max`.

* **Ecuaciones de la Diapositiva:**
    * Selección de acción: $a_{i}\sim\pi_{i}$ tal que $\pi=Solve(\Gamma_{s})$
    * Actualización:
        $$ Q_{j}(s,a)\leftarrow Q_{j}(s,a)+\alpha[r_{j}+\gamma Value_{j}(\Gamma_{s^{\prime}})-Q_{j}(s,a)] $$
    * Valor del equilibrio:
        $$ Value_{i}(\Gamma_{s})=\sum_{a\in A}\pi_{s}(a)\Gamma_{s,i}(a) $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cómo elige su acción un agente JAL-GT en un estado `s`?
        > **Respuesta:** Modela la situación como un juego matricial usando los valores Q para ese estado, calcula un Equilibrio de Nash para ese juego, y elige una acción según la estrategia de equilibrio.
    * **Pregunta:** ¿Qué valor se usa en la actualización de JAL-GT para estimar el retorno futuro?
        > **Respuesta:** El valor esperado del Equilibrio de Nash en el siguiente estado ($Value_j(\Gamma_{s'})$).

---

### **Diapositiva 8: JAL-GT: Inconvenientes**

* **Resumen Estricto:**
    * Se señalan las desventajas de JAL-GT.
    * En general, no se puede garantizar que los agentes aprendan las estrategias de equilibrio correctas.
    * El método es "prescriptivo": asume que los agentes se comportarán de una manera específica (p. ej., que usarán minimax para resolver el juego), lo cual puede ser una suposición fuerte.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuáles son los dos principales inconvenientes de JAL-GT?
        > **Respuesta:** 1) La convergencia no está garantizada para todos los casos. 2) Requiere que los agentes se pongan de acuerdo en cómo resolver el juego en cada estado (p. ej., qué equilibrio elegir si hay múltiples).

---

### **Diapositiva 9: Joint-Action Learning con Agent Modeling (JAL-AM)**

* **Resumen Estricto:**
    * **Idea:** Un punto intermedio. Cada agente `i` aprende un valor Q sobre acciones conjuntas $Q_i(s,a)$ y, al mismo tiempo, mantiene un **modelo de la estrategia** de los otros agentes, $\hat{\pi}_j$.
    * **Algoritmo:** Para elegir una acción, el agente `i` calcula el valor esperado de cada una de sus propias acciones, promediando sobre lo que cree que harán los demás (según su modelo $\hat{\pi}_{-i}$). Luego, actualiza tanto su valor Q como su modelo del oponente.

* **Ecuaciones de la Diapositiva:**
    * Inicialización del modelo del oponente: $\hat{\pi}_{j}(\cdot|s)=\mathcal{U}(A_{j})$ (uniforme)
    * Selección de acción: $a_{i}=arg~max_{a_{i}^{\prime}}AV_{i}(s,a_{i}^{\prime})$
    * Valor esperado promediado (Average Value):
        $$ AV_{i}(s,a_{i})=\sum_{a_{-i}}Q_i(s,(a_{i},a_{-i}))\hat{\pi}_{-i}(a_{-i}|s) $$
    * Actualización de Q-values:
        $$ Q_{i}(s,a)\leftarrow Q_{i}(s,a)+\alpha[r_{i}+\gamma~max_{a_{i}^{\prime}}AV_{i}(s^{\prime},a_{i}^{\prime})-Q_{i}(s,a)] $$

* **Ejemplo Didáctico:**
    * Este agente es como un jugador de Fictitious Play (porque modela al oponente) que usa Q-Learning. Piensa: "Basado en lo que he visto, creo que mi oponente jugará A con 70% de prob. y B con 30%. Ahora, usando mis valores Q, ¿cuál es mi mejor jugada en promedio contra ese comportamiento?". Después del turno, observa qué hizo realmente el oponente y actualiza su creencia.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué dos componentes clave aprende y mantiene un agente JAL-AM?
        > **Respuesta:** 1) Un valor Q sobre las acciones conjuntas. 2) Un modelo de las estrategias de los otros agentes.

---

### **Diapositiva 10 y 11: Bibliografía**

* **Resumen Estricto:**
    * Se lista la bibliografía recomendada y las referencias utilizadas en la presentación.