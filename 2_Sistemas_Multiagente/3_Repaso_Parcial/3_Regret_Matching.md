# 📚 Guía de Estudio Detallada: Regret Matching 🤔

---

### **Diapositiva 1: Portada**

* **Resumen Estricto:**
    * **Curso:** Sistemas Multiagente.
    * **Tema:** Regret Matching.
    * **Autor:** Sergio Yovine, Universidad ORT Uruguay.
    * **Fecha:** 2 de abril de 2025.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es el algoritmo de aprendizaje que se presenta en esta sección?
        > **Respuesta:** Regret Matching (Ajuste por Arrepentimiento).

---

### **Diapositiva 2: Ejemplo Introductorio: RPS**

* **Resumen Estricto:**
    * Se introduce el concepto de "arrepentimiento" (regret) con un ejemplo de Piedra-Papel-Tijeras (RPS).
    * Si el Jugador 1 (P1) elige Roca y el Jugador 2 (P2) elige Papel, la recompensa para P1 es -1.
    * Se plantea la pregunta "¿Qué hubiera pasado si P1 hubiera elegido otra acción?".
        * Si hubiera elegido Papel, su recompensa habría sido 0 (una mejora de +1).
        * Si hubiera elegido Tijeras, su recompensa habría sido 1 (una mejora de +2).
    * Esta "mejora perdida" es la esencia del arrepentimiento.

* **Ejemplo Didáctico:**
    * Estás jugando RPS. Juegas **Roca** y tu amigo juega **Papel**. Pierdes. Inmediatamente piensas:
        1.  "Uff, si hubiera jugado **Papel**, habríamos empatado. Hubiera ganado 1 punto más de lo que gané (0 en vez de -1)". Tu arrepentimiento por no jugar Papel es de 1.
        2.  "¡Rayos! Si hubiera jugado **Tijeras**, le habría ganado. Hubiera obtenido 2 puntos más (1 en vez de -1)". Tu arrepentimiento por no jugar Tijeras es de 2.
    * El arrepentimiento es esa ganancia que dejaste pasar por no haber tomado otra decisión.

* **Flashcards 🧠:**
    * **Pregunta:** En el ejemplo inicial, si P1 juega Roca y P2 Papel, ¿cuál es el arrepentimiento de P1 por no haber jugado Tijeras?
        > **Respuesta:** El arrepentimiento es de 2, porque la recompensa de Tijeras (1) es 2 puntos mayor que la que obtuvo con Roca (-1).

---

### **Diapositiva 3: Concepto de Arrepentimiento (Regret)**

* **Resumen Estricto:**
    * Se define formalmente el arrepentimiento (regret) del jugador `p` por no haber jugado la acción $a_p'$ cuando la acción conjunta que ocurrió fue $a$.
    * Es la diferencia entre la recompensa que se *hubiera obtenido* con $a_p'$ y la recompensa que se *obtuvo* con la acción actual $a_p$.

* **Ecuaciones de la Diapositiva:**
    * Fórmula del Arrepentimiento Instantáneo:
        $$ g_{p}(a_{p}^{\prime},a)=R_{p}(a_{p}^{\prime},a_{-p})-R_{p}(a) $$
    * Ejemplo de cálculo para P1, cuando la acción fue (Roca, Papel):
        $$ g_{1}(\text{Paper},(\text{Rock,Paper}))=R_{1}(\text{Paper,Paper})-R_{1}(\text{Rock,Paper}))=0 - (-1) = 1 $$
        $$ g_{1}(\text{Scissors},(\text{Rock,Paper}))=R_{1}(\text{Scissors,Paper})-R_{1}(\text{Rock,Paper}))=1 - (-1) = 2 $$
        *(Nota: La diapositiva tiene un typo y usa $\gamma$ en la segunda línea, pero debería ser $g$)*.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cómo se define formalmente el arrepentimiento de una acción no elegida?
        > **Respuesta:** Es la recompensa que se hubiera obtenido con esa acción MENOS la recompensa que se obtuvo realmente.
    * **Pregunta:** ¿Cuál es el arrepentimiento de la acción que SÍ jugaste?
        > **Respuesta:** Cero, porque la diferencia de la recompensa contigo misma es cero.

---

### **Diapositiva 4: Regret Matching (Idea y Ejemplo)**

* **Resumen Estricto:**
    * **Idea Central:** Jugar más a menudo aquellas acciones de las que más te has arrepentido en el pasado.
    * Para no ser predecible, se usa una estrategia mixta donde la probabilidad de cada acción es proporcional a su **arrepentimiento positivo acumulado**.
    * Se muestra una tabla con un ejemplo de 2 rondas, donde se calculan los arrepentimientos, se acumulan y se deriva la nueva estrategia.

* **Ejemplo Didáctico Extendido (Análisis de la Tabla):**
    * **Ronda 1:**
        * **Suceso:** La jugada fue (Roca, Papel). P1 recibió -1.
        * **Arrepentimientos de P1 (`g_1`):** Se calcula el "qué hubiera pasado" para P1:
            * Por no jugar Roca: $R_1(R,P) - R_1(R,P) = -1 - (-1) = 0$.
            * Por no jugar Papel: $R_1(P,P) - R_1(R,P) = 0 - (-1) = 1$.
            * Por no jugar Tijeras: $R_1(S,P) - R_1(R,P) = 1 - (-1) = 2$.
            * El vector de arrepentimientos de la ronda es **(0, 1, 2)** para (R, P, S).
        * **Arrepentimiento Acumulado (`G_1`):** Como es la primera ronda, es el mismo: **(0, 1, 2)**.
        * **Nueva Estrategia de P1:** Se basa en los arrepentimientos positivos. La suma es $0+1+2=3$.
            * Prob(Roca) = 0/3 = 0%
            * Prob(Papel) = 1/3 $\approx$ 33%
            * Prob(Tijeras) = 2/3 $\approx$ 67%
            * La nueva estrategia es jugar Papel o Tijeras, con más probabilidad de jugar Tijeras porque es de lo que más se arrepiente.

    * **Ronda 2:**
        * **Suceso:** La jugada fue (Tijeras, Roca). P1 ganó +1. (Nota: La tabla tiene valores que no corresponden a RPS estándar, pero seguimos su lógica).
        * **Arrepentimientos de P1 (`g_1`):**
            * Por no jugar Roca: $R_1(R,R) - R_1(S,R) = 0 - 1 = -1$.
            * Por no jugar Papel: $R_1(P,R) - R_1(S,R) = -1 - 1 = -2$.
            * Por no jugar Tijeras: $R_1(S,R) - R_1(S,R) = 1 - 1 = 0$.
            * El vector de arrepentimientos de la ronda es **(-1, -2, 0)**.
        * **Arrepentimiento Acumulado (`G_1`):** Se suma el acumulado anterior con el nuevo:
            * `(0, 1, 2) + (-1, -2, 0) = (-1, -1, 2)` (Aquí la tabla de la diapositiva parece seguir una lógica distinta, pero el proceso es este).

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la idea principal del algoritmo de Regret Matching?
        > **Respuesta:** Jugar acciones con una probabilidad proporcional al arrepentimiento positivo acumulado que se tiene por no haberlas jugado en el pasado.

---

### **Diapositiva 5: Algoritmo Formal de Regret Matching**

* **Resumen Estricto:**
    * Se formaliza el algoritmo completo.
    1.  **Inicializar:** Los arrepentimientos acumulados ($G_p$) empiezan en cero. La primera estrategia ($\hat{\pi}_p^1$) es uniforme (todas las acciones igual de probables).
    2.  **Iterar:** En cada paso $t+1$:
        * Se calcula el vector de arrepentimiento instantáneo $g_p^{t+1}$ basado en la jugada que acaba de ocurrir.
        * Se actualiza el arrepentimiento acumulado: $G_p^{t+1} = G_p^{t} + g_p^{t+1}$.
        * Se deriva la nueva estrategia $\hat{\pi}_p^{t+1}$ normalizando **solo los arrepentimientos acumulados positivos**.
        * Se elige la siguiente acción $a_p^{t+1}$ muestreando de esa nueva distribución.

* **Ecuaciones de la Diapositiva:**
    * Inicialización: $\hat{\pi}_{p}^{1}$ (uniforme) y $G_{p}^{1}=0$
    * Cálculo del regret instantáneo para cada acción $a_p'$:
        $$ g_{p}^{t+1}(a_{p}^{\prime})=R_{p}(a_{p}^{\prime},a_{-p}^{t})-R_{p}(a^{t}) $$
    * Acumulación del regret:
        $$ G_{p}^{t+1}=G_{p}^{t}+g_{p}^{t+1} $$
    * Obtención de la nueva estrategia (versión corregida):
        $$ \hat{\pi}_{p}^{t+1}(a'_p)=\frac{\max\{G_{p}^{t+1}(a'_p),0\}}{\sum_{a''_p \in A_p} \max\{G_{p}^{t+1}(a''_p),0\}} $$
    * Nota: Si el denominador es cero (no hay regrets positivos), se usa la distribución uniforme.
    * Elección de la siguiente acción:
        $$ a_{p}^{t+1}\sim\hat{\pi}_{p}^{t+1} $$

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la diferencia entre el arrepentimiento instantáneo (`g`) y el acumulado (`G`)?
        > **Respuesta:** `g` es el arrepentimiento de la última ronda. `G` es la suma de todos los arrepentimientos de todas las rondas hasta ahora.
    * **Pregunta:** ¿Por qué en la fórmula de la estrategia solo se consideran los arrepentimientos positivos?
        > **Respuesta:** Porque un arrepentimiento negativo significa que la acción que tomaste fue *mejor* que la alternativa. No quieres jugar más a menudo una acción que te habría dado un peor resultado. Solo las oportunidades perdidas (regrets positivos) te motivan a cambiar.

---

### **Diapositiva 6: FP vs RM (Fictitious Play vs Regret Matching)**

* **Resumen Estricto:**
    * Se compara la información que necesita cada algoritmo.
    * **Agente Fictitious Play (FP):** Necesita observar las **estrategias de todos** (el historial completo de acciones de cada uno) y conocer su **matriz de recompensas completa** para calcular el valor esperado.
    * **Agente Regret Matching (RM):** Solo necesita saber qué **acción jugaron los demás en la última ronda** para poder calcular su "qué hubiera pasado". No necesita el historial completo, solo el resultado del último turno.
    * **Agente Ingenuo:** Es el más simple, solo observa su recompensa y no sabe nada más.

* **Ejemplo Didáctico:**
    * **FP es como un estadístico:** Necesita toda la base de datos de jugadas para construir un modelo del oponente.
    * **RM es como un jugador impulsivo:** Solo se fija en la última jugada y piensa "¡Debería haber hecho otra cosa!". Su memoria se basa en la suma de estos impulsos de arrepentimiento.
    * Por esto, RM a menudo se considera más plausible y requiere menos información que FP.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la principal diferencia en la información que necesita un agente de Fictitious Play versus uno de Regret Matching?
        > **Respuesta:** FP necesita el historial completo de acciones de los oponentes para estimar su estrategia. RM solo necesita saber la acción del oponente en la última ronda para calcular su arrepentimiento.

---

### **Diapositiva 7: Bibliografía Recomendada**

* **Resumen Estricto:**
    * Se recomiendan dos fuentes para profundizar en el tema de Regret Matching y su variante más famosa, Counterfactual Regret Minimization (CFR).