# 📚 Guía de Estudio: Sistemas Multiagente y Teoría de Juegos 🎲

---

### **Diapositiva 1: Portada**

* **Resumen Estricto:**
    * **Curso:** Sistemas Multiagente.
    * **Tema:** Teoría de juegos - Juegos ordinales de una jugada.
    * **Autor:** Sergio Yovine, Universidad ORT Uruguay.
    * **Fecha:** 20 de marzo de 2025.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es el tema principal de esta presentación?
        > **Respuesta:** Teoría de juegos, específicamente los juegos ordinales de una jugada.

---

### **Diapositiva 2: Introducción al Juego "Split or Steal" 💰**

* **Resumen Estricto:**
    * Esta diapositiva introduce un tipo de juego matricial simultáneo de una jugada llamado "Split or Steal" (Repartir o Robar).
    * Los jugadores son Sarah y Steve.
    * Se presenta una matriz de pagos que muestra los resultados monetarios según la elección de cada jugador. Las opciones son "Split" (Repartir) y "Steal" (Robar).
        * 🤝 Si ambos eligen "Split", cada uno recibe $50,000.
        * ⚔️ Si uno elige "Steal" y el otro "Split", el que roba se lleva $100,000 y el otro nada.
        * 💀 Si ambos eligen "Steal", ninguno de los dos recibe nada.

* **Ejemplo Didáctico:**
    * Imagina que tú y un amigo se encuentran una billetera con 100€. Deben decidir qué hacer en secreto y al mismo tiempo. Cada uno escribe en un papel si quiere "Repartir" o "Quedársela".
        * Si ambos escriben "Repartir", se llevan 50€ cada uno.
        * Si tú escribes "Quedársela" y tu amigo "Repartir", tú te llevas los 100€.
        * Si ambos escriben "Quedársela", en la discusión se les cae por una alcantarilla y ambos se quedan sin nada.
    * Este es el dilema exacto que presenta el juego "Split or Steal". Es un juego "simultáneo" porque ninguno sabe qué elegirá el otro al momento de tomar su decisión.

* **Flashcards 🧠:**
    * **Pregunta:** En "Split or Steal", ¿qué ocurre si ambos jugadores eligen "Split"?
        > **Respuesta:** Se reparten el premio en partes iguales ($50,000 cada uno).
    * **Pregunta:** ¿Cuál es la combinación de acciones que resulta en el peor resultado para ambos jugadores?
        > **Respuesta:** Si ambos eligen "Steal", ya que ninguno se lleva nada.
    * **Pregunta:** ¿Cómo puede un jugador obtener el máximo premio posible ($100,000)?
        > **Respuesta:** Eligiendo "Steal" mientras el otro jugador elige "Split".

---

### **Diapositiva 3: 📐 Modelo de un Juego**

* **Resumen Estricto:**
    * Se define el marco formal para modelar un juego, que consta de:
        * **Jugadores (Agentes):** Un conjunto $P=\{1,...,n\}$.
        * **Acciones:** Un conjunto de acciones $A_p$ para cada jugador $p$.
        * **Acciones Conjuntas:** El conjunto de todas las combinaciones posibles de acciones, una por cada jugador: $A = \prod_{p\in P}A_{p}$.
        * **Utilidad (Recompensa):** Una función $R: P \times A \rightarrow \mathbb{R}$ que asigna un valor numérico a cada jugador para cada resultado.
    * La utilidad representa el orden de preferencias. Se introduce la notación:
        * $a >_p b$: $p$ prefiere estrictamente $a$ a $b$.
        * $a \ge_p b$: para $p$, $a$ es al menos tan bueno como $b$.
        * $a \sim_p b$: $p$ es indiferente entre $a$ y $b$.

* **Ejemplo Didáctico:**
    * Usemos el juego "Piedra, Papel o Tijera" ✊✋✌️:
        * **Jugadores (P):** {Jugador 1, Jugador 2}.
        * **Acciones (A_p):** {Piedra, Papel, Tijera}.
        * **Acciones Conjuntas (A):** Las 9 combinaciones posibles, como (Piedra, Tijera).
        * **Utilidad (R):** Podemos definir la utilidad como: Ganar = 1, Empatar = 0, Perder = -1.
        * **Preferencia:** Para el Jugador 1, se cumple que (Piedra, Tijera) $>_{J1}$ (Piedra, Papel), porque prefiere ganar a perder.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuáles son los cuatro componentes esenciales del modelo formal de un juego?
        > **Respuesta:** Jugadores, Acciones de cada jugador, Acciones conjuntas y una función de Utilidad.
    * **Pregunta:** ¿Qué representa la función de utilidad $R$?
        > **Respuesta:** El orden de preferencias de cada jugador sobre los posibles resultados del juego, asignando un valor numérico a cada uno.
    * **Pregunta:** ¿Qué significa la notación $a \sim_p b$?
        > **Respuesta:** Significa que el jugador $p$ es indiferente entre el resultado de la acción conjunta $a$ y el de la acción conjunta $b$.

---

### **Diapositiva 4: Ejemplo Aplicado "Sarah vs Steve" 🧐**

* **Resumen Estricto:**
    * Se aplica el modelo formal al juego "Sarah vs Steve".
        * **Jugadores:** $P=\{\text{Sarah, Steve}\}$.
        * **Acciones:** $A_{Sarah} = A_{Steve} = \{\text{Split, Steal}\}$.
    * Se muestra una matriz de utilidad que traduce los resultados a un ranking de preferencias (números del 1 al 4).
    * Se describen las preferencias:
        * **Sarah (Avara y Egoísta 😈):** Se enfoca solo en su ganancia.
        * **Steve (Equitativo y Benevolente 😇):** Prefiere repartir y que Sarah gane algo.

* **Ejemplo Didáctico:**
    * La matriz de utilidad es la clave. Para Sarah (el primer número de cada par), el mejor resultado es 4 (ella roba, Steve reparte). Para Steve (el segundo número), el mejor es 4 (ambos reparten).
    * Las descripciones verbales nos ayudan a entender *por qué* tienen esas preferencias. Sarah es "avara", busca el máximo para ella. Steve es "equitativo", su máxima utilidad es un reparto igualitario, aunque él no se lleve el máximo posible.

* **Flashcards 🧠:**
    * **Pregunta:** Según la matriz de utilidad, ¿cuál es el resultado que Sarah prefiere por encima de todos los demás?
        > **Respuesta:** La acción conjunta (Steal, Split), que le da una utilidad de 4.
    * **Pregunta:** ¿Qué resultado prefiere Steve por encima de todos? ¿Por qué se describe su comportamiento como "equitativo"?
        > **Respuesta:** La acción conjunta (Split, Split). Se le llama equitativo porque su máxima preferencia es un reparto igualitario.

---

### **Diapositiva 5: Actuar Racionalmente 🤖**

* **Resumen Estricto:**
    * **Racionalidad:** Un jugador actúa racionalmente si elige la acción que maximiza su beneficio esperado según sus preferencias.
    * **"Resolver el juego"**: Consiste en encontrar estas acciones racionales.
    * Para el caso de Sarah vs Steve, se afirma que la acción racional de Sarah es "Steal" y la de Steve es "Split".

* **Ejemplo Didáctico:**
    * Si juegas al ajedrez y eres racional, no harás un movimiento que te lleve a una derrota segura si tienes otra opción mejor. Siempre elegirás el movimiento que crees que maximiza tu utilidad (ganar).

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué significa que un agente "actúe racionalmente"?
        > **Respuesta:** Que elige la acción que maximiza su utilidad esperada de acuerdo a sus preferencias.
    * **Pregunta:** ¿En qué consiste "resolver un juego"?
        > **Respuesta:** En encontrar las acciones racionales que tomará cada jugador.

---

### **Diapositiva 6: Estrategias Puras 🎯**

* **Resumen Estricto:**
    * Una **estrategia pura** es una acción única y concreta que un jugador decide tomar.
    * Una **estrategia conjunta pura** ($\pi$) es una combinación de estrategias puras, una para cada jugador.
    * Notación importante:
        * $\pi_p$: la estrategia pura del jugador $p$.
        * $\pi_{-p}$: las estrategias de **todos los demás** jugadores.
        * $\pi = (\pi_p, \pi_{-p})$: la estrategia conjunta.
        * $R_p(\pi)$: la recompensa del jugador $p$.

* **Ejemplo Didáctico:**
    * En "Piedra, Papel o Tijera", una estrategia pura es decidir: "Voy a jugar Piedra, pase lo que pase".
    * La notación $\pi_{-p}$ es útil en juegos grandes. En póker contra 5 personas, tu estrategia es $\pi_p$ ("Apostar") y la de los otros 5 es $\pi_{-p}$.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué es una estrategia pura?
        > **Respuesta:** Una única acción que un jugador elige de forma determinística.
    * **Pregunta:** ¿Qué representa el término $\pi_{-p}$?
        > **Respuesta:** La estrategia conjunta de todos los jugadores a excepción del jugador $p$.

---

### **Diapositiva 7: Dominancia 💪**

* **Resumen Estricto:**
    * Se definen las relaciones de dominancia entre estrategias.
    * **Dominancia Estricta:** $\pi_p$ domina a $\pi'_p$ si siempre da una recompensa **mayor**, sin importar lo que hagan los demás.
    * **Dominancia Débil:** $\pi_p$ domina a $\pi'_p$ si siempre da una recompensa **mayor o igual**, y al menos una vez es estrictamente mayor.
    * **Equivalencia:** Dan siempre la misma recompensa.

* **Ejemplo Didáctico:**
    * Eres una empresa y eliges entre marketing "Agresivo" y "Normal".
        * Si "Agresivo" *siempre* te da más beneficios, **domina estrictamente** a "Normal".
        * Si "Agresivo" te da *al menos lo mismo* y a veces más, **domina débilmente** a "Normal".

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuál es la diferencia clave entre dominancia estricta y débil?
        > **Respuesta:** En la estricta, la estrategia es *siempre* mejor. En la débil, es *al menos igual* y *a veces mejor*.
    * **Pregunta:** Si una estrategia domina estrictamente a otra, ¿es racional jugar la estrategia dominada?
        > **Respuesta:** No, nunca. Un jugador racional la descartaría.

---

### **Diapositiva 8: 🏆 Estrategias Dominantes**

* **Resumen Estricto:**
    * Una estrategia es **estrictamente dominante** si domina estrictamente a *todas* las demás.
    * Se aplica al ejemplo:
        * Para Steve: "Split" es estrictamente dominante.
        * Para Sarah: "Steal" es (débilmente) dominante. En realidad, también es estrictamente dominante.

* **Ejemplo Didáctico (Verificación):**
    * **Steve:** Sin importar lo que Sarah haga, su utilidad es mayor si elige "Split". Por tanto, "Split" es estrictamente dominante para él.
    * **Sarah:** Sin importar lo que Steve haga, su utilidad es mayor si elige "Steal". Por tanto, "Steal" también es estrictamente dominante para ella.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué es una estrategia estrictamente dominante?
        > **Respuesta:** Una estrategia que es mejor que todas las demás opciones de ese jugador, sin importar lo que hagan los oponentes.
    * **Pregunta:** En Sarah vs Steve, ¿por qué "Split" es una estrategia dominante para Steve?
        > **Respuesta:** Porque le da una mayor utilidad que "Steal" tanto si Sarah elige "Split" como si elige "Steal".

---

### **Diapositiva 9: 📊 Dominancia (Ejemplo Visual)**

* **Resumen Estricto:**
    * Se presenta una matriz de pagos para un jugador (filas A,B,C,D) contra un oponente (columnas E,F,G).

* **Ejemplo Didáctico (Análisis de la Matriz):**
    * Vamos a analizar las estrategias del jugador de las filas:
    * **A vs. B:** La estrategia A siempre da un pago mayor que B. Por tanto, **A domina estrictamente a B**.
    * **A vs. C:** Dan pagos idénticos. Son **equivalentes**.
    * **A vs. D:** La estrategia A siempre da un pago mayor que D. Por tanto, **A domina estrictamente a D**.
    * **Conclusión:** Un jugador racional nunca jugaría B o D.

* **Flashcards 🧠:**
    * **Pregunta:** En la matriz del ejemplo, ¿cuál es la relación entre la estrategia A y la estrategia B?
        > **Respuesta:** A domina estrictamente a B.
    * **Pregunta:** ¿Qué estrategias de esta matriz descartaría un jugador racional y por qué?
        > **Respuesta:** Descartaría B y D, porque están estrictamente dominadas por la estrategia A.

---

### **Diapositiva 10: 🏅 Equilibrio Dominante**

* **Resumen Estricto:**
    * Una estrategia conjunta es un **equilibrio de estrategias dominantes** si la estrategia de cada jugador es dominante para él.
    * En Sarah vs Steve, **(Steal, Split) es un equilibrio dominante**.

* **Ejemplo Didáctico:**
    * Es la predicción más fuerte que se puede hacer. Si cada jugador tiene una "mejor jugada" que funciona siempre, podemos estar seguros de que la usarán. El resultado es (Sarah: Steal, Steve: Split).

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cómo se define un equilibrio de estrategias dominantes?
        > **Respuesta:** Es una estrategia conjunta donde la acción de cada jugador es una estrategia dominante para él.
    * **Pregunta:** ¿Por qué el equilibrio dominante es una solución tan estable?
        > **Respuesta:** Porque ningún jugador tiene incentivos para cambiar su estrategia, haga lo que haga el otro.

---

### **Diapositiva 11: 🔍 Equilibrio por Eliminación Iterada**

* **Resumen Estricto:**
    * Muchos juegos no tienen estrategias dominantes, pero sí **estrategias dominadas**.
    * La idea es **eliminar iterativamente** estas estrategias dominadas, basándose en el supuesto de **conocimiento común** (todos saben que todos son racionales).
    * El razonamiento es: "Yo sé que tú eres racional, así que no jugarás X. Sabiendo eso, yo no jugaré Y...".

* **Ejemplo Didáctico:**
    * Es como una deducción lógica. Cada jugador tacha las jugadas "tontas" del otro, y luego reevalúa sus propias opciones en el juego simplificado. Este proceso se repite hasta que no se puedan eliminar más estrategias.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué método de solución se puede usar cuando no hay estrategias dominantes?
        > **Respuesta:** La eliminación iterada de estrategias dominadas.
    * **Pregunta:** ¿Qué supuesto fundamental se requiere para que la eliminación iterada funcione?
        > **Respuesta:** El conocimiento común de la racionalidad de los jugadores.

---

### **Diapositiva 12: Proceso de Eliminación Iterada (IDSDS) ♻️**

* **Resumen Estricto:**
    * El proceso se llama **IDSDS**: Eliminación Iterada de Estrategias Estrictamente Dominadas.
    * El proceso visual muestra cómo se van tachando filas y columnas hasta llegar a una única solución.
    * La solución es **(D, C)**.
    * Se menciona la **IDWDS** (con estrategias débilmente dominadas) como tarea.

* **Dato Clave:**
    * Con estrategias **estrictamente** dominadas (IDSDS), el orden de eliminación no cambia el resultado.
    * Con estrategias **débilmente** dominadas (IDWDS), el orden SÍ puede cambiar el resultado.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué significa IDSDS?
        > **Respuesta:** Eliminación Iterada de Estrategias Estrictamente Dominadas.
    * **Pregunta:** ¿Cuál es el equilibrio que se encuentra en el ejemplo aplicando IDSDS?
        > **Respuesta:** (D, C).

---

### **Diapositiva 13: 💡 Equilibrio de Nash (Introducción)**

* **Resumen Estricto:**
    * Se introduce para juegos sin estrategias dominantes/dominadas.
    * Idea central: Es una estrategia conjunta en la que **ningún jugador tiene incentivo para cambiar su estrategia de forma unilateral**.
    * Se muestra un nuevo juego y se afirma que **(T, L) es el Equilibrio de Nash (EN)**.

* **Ejemplo Didáctico (No Arrepentimiento):**
    * En un EN, una vez que se revelan las jugadas, nadie piensa "¡rayos, ojalá hubiera jugado otra cosa!".
    * En (T, L), J1 gana 2. Si hubiera jugado M o B, habría ganado 1 o 0 (peor). J2 gana 2. Si hubiera jugado C o R, habría ganado 1 o 0 (peor). Como ninguno se arrepiente, es un estado estable.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuándo es útil el concepto de Equilibrio de Nash?
        > **Respuesta:** En juegos donde no hay estrategias dominantes ni dominadas.
    * **Pregunta:** ¿Cuál es la idea fundamental de un Equilibrio de Nash?
        > **Respuesta:** Es un estado estable donde ningún jugador puede mejorar su resultado cambiando su estrategia si los demás no cambian la suya.

---

### **Diapositiva 14: ✍️ Equilibrio de Nash (Definición Formal)**

* **Resumen Estricto:**
    * **Mejor Respuesta (Best Response, BR):** Es la estrategia que da la máxima recompensa a un jugador, dadas las estrategias de los demás.
    * **Equilibrio de Nash:** Una estrategia conjunta $\pi$ es un EN si la estrategia de **cada jugador es una mejor respuesta** a las de los demás. ($\pi_p \in BR_p(\pi_{-p})$).

* **Método para encontrar EN ("Subrayado"):**
    1.  Para cada **columna**, subraya el pago más alto para el Jugador de las **filas**.
    2.  Para cada **fila**, subraya el pago más alto para el Jugador de las **columnas**.
    3.  ✅ ¡Cualquier celda donde **ambos números** estén subrayados es un Equilibrio de Nash!

* **Flashcards 🧠:**
    * **Pregunta:** ¿Qué es la "mejor respuesta" de un jugador?
        > **Respuesta:** Es la estrategia que maximiza su recompensa, dada una elección específica de sus oponentes.
    * **Pregunta:** ¿Cómo se define un Equilibrio de Nash usando el concepto de "mejor respuesta"?
        > **Respuesta:** Es una estrategia conjunta donde la elección de cada jugador es una mejor respuesta a las elecciones de los demás.

---

### **Diapositiva 15: 🤔 Interpretaciones del Equilibrio de Nash**

* **Resumen Estricto:**
    * **No arrepentimiento:** Nadie se lamenta de su jugada.
    * **Acuerdo auto-ejecutable:** Un acuerdo verbal en un EN se cumpliría solo, por puro interés.
    * **Recomendación viable:** Una recomendación de un tercero solo se seguiría si es un EN.
    * **Razonamiento transparente:** Jugadores ultra-racionales llegarían a la misma conclusión.

* **Ejemplo Didáctico:**
    * Un acuerdo entre empresas para no bajar precios solo se mantendrá si es un EN. Si una empresa puede ganar mucho más rompiendo el pacto unilateralmente, el acuerdo no es un EN y probablemente no durará.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Por qué un Equilibrio de Nash se considera un "acuerdo auto-ejecutable"?
        > **Respuesta:** Porque una vez acordado, ningún jugador tiene un incentivo individual para desviarse.
    * **Pregunta:** Explica la interpretación de "no arrepentimiento".
        > **Respuesta:** Una vez conocidas las jugadas, ningún jugador puede decir "si hubiera elegido otra cosa, me habría ido mejor".

---

### **Diapositiva 16: 🤯 Múltiples Equilibrios de Nash**

* **Resumen Estricto:**
    * Un juego puede tener **más de un Equilibrio de Nash**.
    * El ejemplo muestra que **(D, C) y (M, R) son ambos Equilibrios de Nash**.

* **Ejemplo Didáctico (Juego de Coordinación):**
    * Dos amigos quieren ir juntos al cine o al parque. Ambos prefieren estar juntos a estar solos.
        * (Cine, Cine) es un EN.
        * (Parque, Parque) es otro EN.
    * El problema es: ¿cómo se coordinan para elegir el mismo? Esto se conoce como **problema de selección de equilibrios**.

* **Flashcards 🧠:**
    * **Pregunta:** ¿Es posible que un juego tenga más de un Equilibrio de Nash?
        > **Respuesta:** Sí.
    * **Pregunta:** ¿Qué nuevo problema surge cuando hay múltiples equilibrios?
        > **Respuesta:** El problema de la selección de equilibrios: los jugadores no saben en cuál coordinarse.

---

### **Diapositiva 17: 🚫 Inexistencia de Equilibrio de Nash**

* **Resumen Estricto:**
    * Un juego puede no tener **ningún Equilibrio de Nash en estrategias puras**.
    * El ejemplo clásico es **"Matching Pennies"** (juego de las monedas).

* **Ejemplo Didáctico:**
    * En "Matching Pennies", dos jugadores muestran una moneda. J1 gana si coinciden, J2 si no.
    * Busquemos un EN:
        * (Cara, Cara) ➡️ J2 quiere cambiar a Cruz.
        * (Cara, Cruz) ➡️ J1 quiere cambiar a Cruz.
        * ¡No hay ninguna casilla estable! Siempre uno se arrepiente y quiere cambiar. La solución requiere **estrategias mixtas** (jugar con probabilidades).

* **Flashcards 🧠:**
    * **Pregunta:** ¿Todos los juegos tienen al menos un Equilibrio de Nash en estrategias puras?
        > **Respuesta:** No.
    * **Pregunta:** ¿Cuál es un ejemplo clásico de un juego sin EN en estrategias puras?
        > **Respuesta:** "Matching Pennies".

---

### **Diapositiva 18: ✅ Resumen y Problemas Pendientes ❓**

* **Resumen Estricto:**
    * **Conceptos vistos:** Se repasan los supuestos de los juegos analizados.
    * **Solución principal:** El Equilibrio de Nash.
    * **Problemas subsistentes:**
        1.  Puede haber más de uno.
        2.  Puede no haber ninguno (en estrategias puras).
        3.  ¿Cómo aprenden los agentes a jugar un equilibrio?

* **Flashcards 🧠:**
    * **Pregunta:** ¿Cuáles son los tres problemas principales del Equilibrio de Nash que se mencionan en el resumen?
        > **Respuesta:** 1. Multiplicidad. 2. Inexistencia (en puras). 3. Aprendizaje.

---

### **Diapositiva 19: 📚 Lectura Recomendada**

* **Resumen Estricto:**
    * Se recomiendan tres fuentes para profundizar en el tema (libros sobre Aprendizaje por Refuerzo Multiagente, Teoría de Juegos y Microeconomía).