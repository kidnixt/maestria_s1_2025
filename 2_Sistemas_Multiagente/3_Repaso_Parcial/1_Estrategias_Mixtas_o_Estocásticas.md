# 📚 Guía de Estudio Definitiva (Corregida): Estrategias Mixtas 🎲

---

### **Diapositiva 1: Portada**

* **Resumen Estricto:**
    * **Curso:** Sistemas Multiagente.
    * **Tema:** Teoría de juegos - Estrategias mixtas o estocásticas.
    * **Autor:** Sergio Yovine, Universidad ORT Uruguay.
    * **Fecha:** 25 de marzo de 2025.

---

### **Diapositiva 2: Recordatorio del Problema: Matching Pennies**

* **Resumen Estricto:**
    * Se vuelve a presentar el juego "Matching Pennies" para recordar que no tiene un Equilibrio de Nash (EN) en estrategias puras, lo que nos obliga a buscar una solución diferente.
    * Se muestra la matriz de pagos 2x2.

* **Ejemplo Didáctico:**
    * La inestabilidad es la clave. Para cualquier combinación de Caras y Cruces (estrategias puras), uno de los dos jugadores siempre se arrepentirá y querrá cambiar su jugada. No hay un resultado estable.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Por qué las estrategias puras no son suficientes para resolver "Matching Pennies"?
        > **Respuesta:** Porque para cualquier combinación de estrategias puras, siempre hay un jugador con un incentivo para desviarse unilateralmente, impidiendo un equilibrio.

---

### **Diapositiva 3: Definición de Estrategias Mixtas**

* **Resumen Estricto:**
    * Una **estrategia mixta** se define como una distribución de probabilidad sobre las acciones de un jugador.
    * Se define el **soporte** de la estrategia como el conjunto de acciones que tienen una probabilidad > 0.

* **Ecuaciones de la Diapositiva:**
    * Definición del conjunto de estrategias mixtas para el jugador p:
        $$ \Delta(A_{p})\triangleq\{\pi_{p}:A_{p}\rightarrow[0,1] \ | \ \sum_{a_{p}\in A_{p}}\pi_{p}(a_{p})=1\} $$
    * Condición para una estrategia pura:
        $$ \exists a_{p} \text{ tal que } \pi_{p}(a_{p})=1, \text{ y para toda } a_{p}^{\prime}\ne a_{p}, \pi_{p}(a_{p}^{\prime})=0 $$
    * Estrategia conjunta mixta:
        $$ \pi=(\pi_{1},...,\pi_{n})\in\prod_{p\in P}\Delta(A_{p}) $$
    * Probabilidad de una acción conjunta (asumiendo independencia):
        $$ \pi(a)=\prod_{p\in P}\pi_{p}(a_{p}) $$
    * Soporte de una estrategia:
        $$ \{a_{p} \ | \ \pi_{p}(a_{p})>0\} $$

* **Ejemplo Didáctico:**
    * Una estrategia mixta es simplemente "jugar al azar con un plan". Por ejemplo, en Piedra-Papel-Tijera, una estrategia podría ser lanzar un dado: si sale 1-3 (50% prob), juegas Piedra; si sale 4-5 (33% prob), juegas Papel; si sale 6 (17% prob), juegas Tijera.

* **Flashcards 🧠:**
    * **Pregunta:** En tus propias palabras, ¿qué es una estrategia mixta?
        > **Respuesta:** Es cuando un jugador no elige una acción fija, sino que decide jugarlas al azar según unas probabilidades que él mismo define.
    * **Pregunta:** ¿Qué es el "soporte" de una estrategia?
        > **Respuesta:** El conjunto de acciones que tienen una probabilidad real (mayor a cero) de ser jugadas.

---

### **Diapositiva 4: Ejemplo de Estrategia Mixta**

* **Resumen Estricto:**
    * Se muestra un ejemplo para Matching Pennies donde ambos jugadores usan la estrategia mixta de jugar Cara (H) y Cruz (T) con probabilidad 1/2.
    * Esto resulta en que cada uno de los 4 resultados posibles tiene una probabilidad de 1/4.

* **Ecuaciones de la Diapositiva:**
    * Estrategia mixta de cada jugador:
        $$ \pi_{p}(H)=\pi_{p}(T)=\frac{1}{2}, \text{ para } p=\{1,2\} $$
    * Probabilidad de cada resultado:
        $$ \frac{1}{4} $$

* **Ejemplo Didáctico Extendido:**
    * Si el Jugador 1 elige su acción lanzando una moneda (50% H, 50% T) y el Jugador 2 hace lo mismo, las probabilidades de los resultados se calculan multiplicando las probabilidades individuales:
        * **P(H,H)** = $P(J1=H) \times P(J2=H) = \frac{1}{2} \times \frac{1}{2} = \frac{1}{4}$
        * **P(H,T)** = $P(J1=H) \times P(J2=T) = \frac{1}{2} \times \frac{1}{2} = \frac{1}{4}$
        * **P(T,H)** = $P(J1=T) \times P(J2=H) = \frac{1}{2} \times \frac{1}{2} = \frac{1}{4}$
        * **P(T,T)** = $P(J1=T) \times P(J2=T) = \frac{1}{2} \times \frac{1}{2} = \frac{1}{4}$

---

### **Diapositiva 5: Valor de una Estrategia Mixta**

* **Resumen Estricto:**
    * Se define el **Valor ($V_p(\pi)$)** como la recompensa esperada (el promedio de ganancias) para un jugador $p$ dada una estrategia conjunta mixta $\pi$.
    * Se calcula para J1 en el ejemplo 50/50, obteniendo un valor de 1/2.

* **Ecuaciones de la Diapositiva:**
    * Fórmula del Valor Esperado:
        $$ V_{p}(\pi)=\mathbb{E}_{a\sim\pi}[R_{p}(a)]=\sum_{a\in A}\pi(a)R_{p}(a) $$
    * Cálculo para el ejemplo:
        $$ V_{1}=\frac{1}{4} \cdot 1+\frac{1}{4} \cdot 0+\frac{1}{4} \cdot 0+\frac{1}{4} \cdot 1 = \frac{1}{2} $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué representa el "Valor" de una estrategia mixta?
        > **Respuesta:** La ganancia promedio que un jugador espera obtener si el juego se repitiera muchas veces con esa misma estrategia.

---

### **Diapositiva 6: Mejor Respuesta (en Estrategias Mixtas)**

* **Resumen Estricto:**
    * Una estrategia $\pi_p$ es una **Mejor Respuesta (Best Response)** a las estrategias de los demás ($\pi_{-p}$) si maximiza el valor esperado del jugador $p$.

* **Ecuaciones de la Diapositiva:**
    * Definición formal de Mejor Respuesta:
        $$ \pi_{p} = \text{argmax}_{\pi_{p}^{\prime}\in\Delta(A_{p})}V_{p}(\pi_{p}^{\prime},\pi_{-p}) $$
    * Notación para el conjunto de mejores respuestas:
        $$ BR(\pi_{-p}) $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué busca un jugador al elegir una "Mejor Respuesta"?
        > **Respuesta:** La estrategia que le da el mayor valor esperado (ganancia promedio) posible, dadas las estrategias de sus oponentes.

---

### **Diapositiva 7: Equilibrio de Nash en Estrategias Mixtas**

* **Resumen Estricto:**
    * Un **Equilibrio de Nash (EN)** en estrategias mixtas es un perfil de estrategias donde la estrategia de cada jugador es una mejor respuesta a las de los demás.
    * Se enuncia el **Teorema de Nash (1951):** todo juego finito tiene al menos un EN en estrategias mixtas.
    * Se afirma que la estrategia 50/50 es el EN para Matching Pennies.

* **Ecuaciones de la Diapositiva:**
    * Condición para un Equilibrio de Nash:
        $$ \pi_{p}\in BR(\pi_{-p}), \text{ para todo } p\in P $$
    * Estrategia de Equilibrio para Matching Pennies:
        $$ \pi^{*}=(\pi_{1}^{*},\pi_{2}^{*}) \text{ tal que } \forall p\in\{1,2\}, \pi_{p}^{*}(H)=\pi_{p}^{*}(T)=\frac{1}{2} $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué garantiza el famoso teorema de Nash de 1951?
        > **Respuesta:** Que todo juego con un número finito de jugadores y acciones tiene al menos un punto de equilibrio estable (un EN), si permitimos que los jugadores usen estrategias mixtas.

---

### **Diapositiva 8: Matching Pennies como Juego de Suma Cero**

* **Resumen Estricto:**
    * Se formaliza Matching Pennies como un **juego de suma cero**, donde la ganancia de uno es la pérdida del otro.

* **Ecuaciones de la Diapositiva:**
    * Condición de suma cero:
        $$ \forall a\in A, \ R_{1}(a)=-R_{2}(a) $$
    * Relación entre los valores esperados:
        $$ V_{1}(\pi)=\sum_{a\in A}\pi(a)R_{1}(a) = -\sum_{a\in A}\pi(a)R_{2}(a) = -V_{2}(\pi) $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué define a un juego de "suma cero"?
        > **Respuesta:** Que la suma de las ganancias y pérdidas de todos los jugadores es siempre cero. Lo que uno gana, otro lo pierde.

---

### **Diapositiva 9: Cálculo de Mejor Respuesta (Ejemplo)**

* **Resumen Estricto:**
    * Se calcula la BR de J1 a la estrategia de J2, $\pi_2 = (1/3, 2/3)$, demostrando que es la estrategia pura de jugar siempre T.

* **Ecuaciones de la Diapositiva:**
    * Estrategia de J2: $\pi_{2}=(h_{2},t_{2})=(\frac{1}{3},\frac{2}{3})$
    * Valor de J1 en función de su probabilidad de jugar H ($h_1$):
        $$ V_{1}(\pi_{1},\pi_{2})=h_{2}(1h_{1}-1~t_{1})+t_{2}(-1h_{1}+1t_{1}) = -\frac{2}{3}h_{1}+\frac{1}{3} $$
    * Maximización y resultado:
        $$ \text{máx } V_{1}(\pi_{1},\pi_{2})=\frac{1}{3} \text{ en } \pi_{1}=(0,1) \implies BR(\pi_{2})=\{(0,1)\} $$

* **Flashcards 🧠:**
    * **Pregunta:** Al calcular la BR de J1 a la estrategia mixta de J2, ¿por qué el resultado es una estrategia pura?
        > **Respuesta:** Porque la función de valor esperado de J1 resultó ser una línea recta. El máximo de una línea recta siempre está en uno de sus extremos (en este caso, jugar H con probabilidad 0 o 1).

---

### **Diapositiva 10: Propiedad de Mejor Respuesta**

* **Resumen Estricto:**
    * Se enuncia la propiedad de que siempre existe una estrategia **pura** que es una mejor respuesta a una estrategia mixta.

* **Ecuaciones de la Diapositiva:**
    $$ \pi_{p}^{\prime}\in BR(\pi_{-\rho}) $$

* **Flashcards 🧠:**
    * **Pregunta:** Verdadero o Falso: Para encontrar la mejor respuesta a una estrategia mixta, debo probar todas las posibles estrategias mixtas.
        > **Respuesta:** Falso. Gracias a esta propiedad, solo necesito comprobar el valor de cada una de mis estrategias puras y elegir la mejor.

---

### **Diapositiva 11: ¿Es un Equilibrio de Nash?**

* **Resumen Estricto:**
    * Se concluye que el perfil $\pi = ((0,1), (1/3, 2/3))$ **no** es un EN porque la estrategia de J2 no es una BR a la de J1.

* **Ecuaciones de la Diapositiva:**
    * Perfil a analizar: $\pi=(\pi_{1},\pi_{2})=((0,1),(\frac{1}{3},\frac{2}{3}))$
    * Condición que falla: $BR(\pi_{1})\ne\pi_{2}$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué condición debe cumplirse para que un perfil de estrategias sea un EN?
        > **Respuesta:** La estrategia de *cada* jugador debe ser una mejor respuesta a las estrategias de los demás. Si un solo jugador tiene incentivo a cambiar, ya no es un EN.

---

### **Diapositiva 12: Verificación del Equilibrio de Nash (50/50)**

* **Resumen Estricto:**
    * Se verifica que la estrategia 50/50 ($\pi^*$) sí es un EN, porque si J2 juega 50/50, el valor para J1 es siempre 0, sin importar lo que J1 haga.

* **Ecuaciones de la Diapositiva:**
    * Valor en el EN (versión suma cero):
        $$ V_{1}(\pi^{*})=\sum_{a\in A}\pi^{*}(a)R_{1}(a)=\sum_{a\in A}\frac{1}{4}R_{1}(a)=\frac{1}{4}\sum_{a\in A}R_{1}(a)=0 $$
    * Comprobación de no incentivo a la desviación:
        $$ V_{1}(\pi_{1},\pi_{2}^{*})=\frac{1}{2}(1h_{1}-1t_{1}-1h_{1}+1t_{1})=0 $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué es el "Principio de Indiferencia" y cómo ayuda a encontrar un EN mixto?
        > **Respuesta:** Dice que un jugador solo mezclará estrategias si todas le dan el mismo valor esperado. Para encontrar el EN, buscamos la estrategia del oponente que nos haga indiferentes entre nuestras propias acciones.

---

### **Diapositiva 13: Visualización 3D**

* **Resumen Estricto:**
    * Muestra la utilidad de J1 como una superficie en forma de "silla de montar", donde el punto de silla (el centro) es el Equilibrio de Nash.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué representa el "punto de silla" en la visualización 3D de la utilidad?
        > **Respuesta:** Representa el Equilibrio de Nash, un punto estable donde ningún jugador puede mejorar su utilidad cambiando unilateralmente su estrategia.

---

### **Diapositiva 14 y 15: Maxmin y Minmax**

* **Resumen Estricto:**
    * **Maxmin (Diap. 14):** Estrategia de seguridad del agente para maximizar su ganancia mínima garantizada.
    * **Minmax (Diap. 15):** Estrategia del oponente para minimizar la ganancia máxima del agente.

* **Ecuaciones de la Diapositiva 14 (Maxmin):**
    * $V(\pi_{a},L) = 5\lambda-3$
    * $V(\pi_{a},R) = -7\lambda+4$
    * $V_{mix,min}=max_{\lambda\in[0,1]}min\{5\lambda-3,-7\lambda+4\}=-\frac{1}{12}$

* **Ecuaciones de la Diapositiva 15 (Minmax):**
    * $V(A,\pi_{o})=5\alpha-3$
    * $V(B,\pi_{o})=-7\alpha+4$
    * $V_{min,mix}=min_{\alpha\in[0,1]}max\{5\alpha-3,-7\alpha+4\}=-\frac{1}{12}$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la diferencia de mentalidad entre un jugador Maxmin y un jugador Minmax?
        > **Respuesta:** El jugador Maxmin es un pesimista que busca la mejor de las peores situaciones. El jugador Minmax busca activamente limitar las opciones de su oponente.

---

### **Diapositiva 16: Teorema Minimax**

* **Resumen Estricto:**
    * Se enuncia el **Teorema Minimax (von Neumann, 1928)**: para juegos de suma cero de 2 jugadores, el valor maxmin es igual al valor minmax. Este es el valor del juego, $V^*$.
    * La solución minimax es un Equilibrio de Nash.

* **Ecuaciones de la Diapositiva:**
    * $V_{mix,min}=V_{min,max}=V^{*}$
    * $\pi_{a}^{*}\in arg~max_{\pi_{a}}min_{\pi_{o}}V(\pi_{a},\pi_{o})$
    * $\pi_{o}^{*}\in arg~min_{\pi_{o}}max_{\pi_{a}}V(\pi_{a},\pi_{o})$
    * $\pi_{a}^{*}\in BR(\pi_{o}^{*})$
    * $\pi_{o}^{*}\in BR(\pi_{a}^{*})$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Para qué tipo de juegos se aplica el Teorema Minimax?
        > **Respuesta:** Para juegos de dos jugadores y suma cero.

---

### **Diapositiva 17 y 18: Solución como Programación Lineal**

* **Resumen Estricto:**
    * El problema de encontrar la estrategia minimax se puede formular y resolver como un problema de **Programación Lineal**.

* **Ecuaciones de la Diapositiva 18 (General):**
    * `minimize V`
    * `subject to`
        * $\sum_{a\in Acc(p)}\pi_{p}(a)R_{p}(a,b)\le V, \quad \forall b\in Acc(q)$
        * $\sum_{a\in Acc(p)}\pi_{p}(a)=1$
        * $\pi_{p}(a)\ge0, \quad \forall a\in Acc(p)$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es el objetivo principal al formular un juego como un problema de programación lineal?
        > **Respuesta:** El objetivo es encontrar el valor `V` más bajo posible (el valor del juego) que un jugador puede garantizarse, sujeto a que sus probabilidades sumen 1 y sean positivas.

---

### **Diapositiva 19: Lectura Recomendada**

* **Resumen Estricto:**
    * Se recomienda leer las secciones 2-4 del capítulo 4 del libro de Albrecht et al. para profundizar en el tema.