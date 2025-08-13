# 📚 Guía de Estudio Detallada: Fictitious Play 🤖

---

### **Diapositiva 1: Portada**

* **Resumen Estricto:**
    * **Curso:** Sistemas Multiagente.
    * **Tema:** Fictitious Play.
    * **Autor:** Sergio Yovine, Universidad ORT Uruguay.
    * **Fecha:** 25 de marzo de 2025.

---

### **Diapositiva 2: Concepto de Fictitious Play**

* **Resumen Estricto:**
    * Se introduce "Fictitious Play" (Juego Ficticio) como un proceso de aprendizaje donde el juego se repite en rondas.
    * Los agentes no conocen las funciones de utilidad de sus adversarios.
    * La suposición clave es que los oponentes tienen un **comportamiento estacionario**, es decir, que su estrategia, aunque desconocida, no cambia con el tiempo.
    * Históricamente fue propuesto como una técnica para calcular soluciones aproximadas de juegos.
    * El diagrama muestra a los agentes observando un estado $s_t$, eligiendo acciones $a_i$, y recibiendo recompensas $r_i$.

* **Ejemplo Didáctico:**
    * Imagina que juegas "Piedra, Papel o Tijera" ✊✋✌️ contra alguien por primera vez. No sabes cómo piensa, pero puedes ver lo que juega.
    * Fictitious Play es como si llevaras una cuenta mental: "Ok, ha jugado Piedra 5 veces y Papel 2 veces". Basado en esto, tú crees que tu oponente es un "jugador de Piedra" (su estrategia estimada es jugar Piedra $\approx$ 71% de las veces).
    * Tu siguiente jugada será tu **mejor respuesta** a esa creencia: jugarás Papel.
    * Después de la ronda, observas qué jugó, actualizas tu cuenta mental y repites el proceso. Estás jugando contra un oponente "ficticio" cuya estrategia es simplemente el historial de sus jugadas pasadas.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la idea central de Fictitious Play?
        > **Respuesta:** Es un algoritmo de aprendizaje donde cada agente asume que sus oponentes están jugando una estrategia mixta fija, la estima a partir de las acciones pasadas observadas, y juega su mejor respuesta a esa estimación.
    * **Pregunta:** ¿Qué suposición clave hace un agente sobre sus oponentes en Fictitious Play?
        > **Respuesta:** Asume que el comportamiento de sus oponentes es estacionario (que su estrategia no cambia con el tiempo).

---

### **Diapositiva 3: Algoritmo de Fictitious Play**

* **Resumen Estricto:**
    * Se formaliza el algoritmo. Cada jugador `p` mantiene un contador, $count_{p,q}^{t}(a_{q})$, para cada acción $a_q$ que ha observado del oponente `q`.
    * En cada iteración `t`, el jugador `p` estima la estrategia del oponente $\hat{\pi}_{q}^{t}$ normalizando esos contadores para crear una distribución de probabilidad.
    * Luego, `p` elige una acción $a_{p}^{t}$ que sea una Mejor Respuesta (BR) a la estrategia estimada de sus oponentes, $\hat{\pi}_{-p}^{t}$.

* **Ecuaciones de la Diapositiva:**
    * Contador de acciones observadas:
        $$ count_{p,q}^{t}(a_{q}) $$
    * Inicialización de los contadores (arbitraria):
        $$ count_{p,q}^{0}(a_{q}) $$
    * Actualización del contador:
        $$ count_{p,q}^{t+1}(a_{q})=count_{p,q}^{t}(a_{q})+1 \text{ (si q juega } a_{q} \text{ en } t+1) $$
    * Estimación de la estrategia del oponente:
        $$ \hat{\pi}_{q}^{t}(a_{q})=\frac{count_{p,q}^{t}(a_{q})}{\sum_{a_{q}^{\prime}\in A_{q}}count_{p,q}^{t}(a_{q}^{\prime})} $$
    * Selección de la acción del jugador:
        $$ a_{p}^{t}\in BR(\hat{\pi}_{-p}^{t}) $$

* **Flashcards 🧠:**
    * **Pregunta:** En Fictitious Play, ¿cómo estima un jugador la estrategia de su oponente?
        > **Respuesta:** Calculando la frecuencia relativa de cada acción que ha observado en el pasado. (Dividiendo el número de veces que vio cada acción por el total de observaciones).
    * **Pregunta:** Una vez que un agente estima la estrategia de su oponente, ¿qué hace?
        > **Respuesta:** Elige su propia acción que sea una "Mejor Respuesta" a esa estrategia estimada.

---

### **Diapositiva 4: Fictitious Play (Ejemplo, t=0)**

* **Resumen Estricto:**
    * Se inicia un ejemplo con una matriz de juego 2x2.
    * Se establecen **conteos iniciales arbitrarios (t=0)**. El Jugador 1 cree que J2 ha jugado L:3, R:0. El Jugador 2 cree que J1 ha jugado U:1, D:3.
    * Basado en estas creencias, J1 calcula que su mejor respuesta es D, y J2 calcula que su mejor respuesta es L.

* **Ecuaciones de la Diapositiva:**
    * Conteo inicial para J1 (sobre las acciones de J2): $count_{1}^{0}(L,R)=(3,0)$
    * Conteo inicial para J2 (sobre las acciones de J1): $count_{2}^{0}(U,D)=(1,3)$
    * Cálculo de la BR del Jugador 1 (asumiendo que J2 siempre juega L, $\pi_2=(1,0)$):
        $$ V_{1}^{0}(U,(1,0))=3\cdot1+0\cdot0=3 $$
        $$ V_{1}^{0}(D,(1,0))=4\cdot1+1\cdot0=4 $$
        $$ a_{1}^{0}=arg~max_{U,D}V_{1}(\pi_{1}^{0},(1,0))=D $$
    * Cálculo de la BR del Jugador 2 (asumiendo que J1 juega U 1/4 y D 3/4 de las veces, $\pi_1=(1/4, 3/4)$):
        $$ V_{2}^{0}(L,(\frac{1}{4},\frac{3}{4}))=3\frac{1}{4}+4\frac{3}{4}= 15/4 = 3.75 $$
        $$ V_{2}^{0}(R,(\frac{1}{4},\frac{3}{4}))=0\frac{1}{4}+1\frac{3}{4}= 3/4 = 0.75 $$
        $$ a_{2}^{0}=arg~max_{L,R}V_{2}^{0}(\pi_{2}^{0},(\frac{1}{4},\frac{3}{4}))=L $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué representan los "conteos iniciales" en Fictitious Play?
        > **Respuesta:** Representan una creencia inicial o "a priori" sobre cómo han jugado los oponentes antes de empezar el proceso de aprendizaje. Pueden ser arbitrarios.
    * **Pregunta:** En el ejemplo en t=0, ¿por qué el Jugador 1 elige la acción D?
        > **Respuesta:** Porque basado en su creencia de que J2 siempre juega L, la acción D le da una recompensa esperada (4) mayor que la acción U (3).

---

### **Diapositiva 5: Fictitious Play (Ejemplo, t=1)**

* **Resumen Estricto:**
    * La ronda $t=0$ resultó en la acción conjunta (D, L).
    * Los contadores se actualizan: el contador de L para J1 aumenta, y el contador de D para J2 aumenta.
    * Se repite el proceso: los jugadores re-estiman las estrategias y re-calculan sus mejores respuestas, que siguen siendo D y L.
    * Se afirma que a medida que el tiempo tiende a infinito ($t \rightarrow \infty$), las estrategias convergen a $((0,1),(1,0))$, que es la acción conjunta (D,L), y los valores convergen a (4,4).

* **Ecuaciones de la Diapositiva:**
    * Conteos actualizados en t=1:
        $$ count_{1}^{1}(L,R)=(4,0) $$
        $$ count_{2}^{1}(U,D)=(1,4) $$
    * Nueva BR de J1 (la estimación de la estrategia de J2 no cambia, sigue siendo (1,0)):
        $$ a_{1}^{1}=arg~max_{U,D}V_{1}^{1}(\pi_{1}^{1},(1,0))=D $$
    * Nueva BR de J2 (ahora con $\pi_1=(1/5, 4/5)$):
        $$ V_{2}^{1}(L,(\frac{1}{5},\frac{4}{5}))=3\frac{1}{5}+4\frac{4}{5}=\frac{19}{5} = 3.8 $$
        $$ V_{2}^{1}(R,(\frac{1}{5},\frac{4}{5}))=0\frac{1}{5}+1\frac{4}{5}=\frac{4}{5} = 0.8 $$
        $$ a_{2}^{1}=arg~max_{L,R}V_{2}^{1}(\pi_{2}^{1},(\frac{1}{5},\frac{4}{5}))=L $$
    * Convergencia:
        $$ \pi^{t}\rightarrow((0,1),(1,0))=(D,L) $$
        $$ V^{t}\rightarrow(4,4) $$

* **Flashcards 🧠:**
    * **Pregunta:** En el ejemplo, ¿por qué el proceso se "refuerza" a sí mismo en la jugada (D,L)?
        > **Respuesta:** Porque al jugar (D,L), los jugadores actualizan sus conteos de una manera que hace que sus estimaciones sobre el oponente se mantengan o se hagan más fuertes, llevando a que su mejor respuesta siga siendo la misma en la siguiente ronda.

---

### **Diapositiva 6: Convergencia de Fictitious Play**

* **Resumen Estricto:**
    * Se discuten las propiedades de convergencia de Fictitious Play para juegos de 2 jugadores.
    * **Propiedad clave:** Si el proceso de Fictitious Play converge a un perfil de estrategias $\pi$, entonces $\pi$ está garantizado de ser un Equilibrio de Nash.
    * Se sabe que FP converge en juegos de suma cero.
    * **Advertencia:** FP no siempre converge. Puede no converger en absoluto o puede quedar atrapado en ciclos de estrategias puras.

* **Ejemplo Didáctico (Ciclos):**
    * En algunos juegos, FP puede entrar en un bucle sin fin. Imagina un juego donde:
        1. Tu creencia sobre el oponente te hace jugar A.
        2. Al jugar A, actualizas tu creencia, y ahora tu mejor respuesta es B.
        3. Al jugar B, actualizas tu creencia, y ahora tu mejor respuesta es C.
        4. Al jugar C, actualizas tu creencia, y ahora tu mejor respuesta vuelve a ser A.
    * En este caso, el algoritmo nunca se asienta en un punto fijo (un EN) y se queda ciclando.

* **Flashcards 🧠:**
    * **Pregunta:** Si un amigo te dice que usó Fictitious Play en un juego y el algoritmo convergió a un punto, ¿qué sabes sobre ese punto?
        > **Respuesta:** Sabes que ese punto es un Equilibrio de Nash del juego.
    * **Pregunta:** ¿Fictitious Play es un método garantizado para encontrar un Equilibrio de Nash en cualquier juego?
        > **Respuesta:** No. Solo está garantizado que converja en ciertos tipos de juegos (como los de suma cero), pero puede fallar o ciclar en otros.

---

### **Diapositiva 7: Lectura Recomendada**

* **Resumen Estricto:**
    * Se recomienda leer la sección 3.1 del capítulo 6 del libro de Albrecht et al. para profundizar en el tema de Fictitious Play.