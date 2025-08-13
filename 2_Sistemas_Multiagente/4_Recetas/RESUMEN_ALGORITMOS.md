# Resumen Final: La Caja de Herramientas del Agente Racional (Versión Completa)

*Aquí tienes un resumen de cada "arma" en nuestro arsenal de algoritmos. Cada uno es una herramienta para un tipo de problema específico.*

---
---

## **Familia 1: Algoritmos de Solución (Para Juegos Matriciales)**

*Estos algoritmos se usan para "resolver" juegos de un solo turno, como "Split or Steal" o el Dilema del Prisionero.*

### **Eliminación Iterada de Estrategias Estrictamente Dominadas (IDSDS)**
* **La Idea:** Simplificar el juego tachando las jugadas que un jugador racional nunca haría porque son objetivamente peores que otra opción, sin importar nada.
* **Cuándo se Usa:** Es el primer método a intentar para reducir la complejidad de una matriz y, a veces, encontrar un único resultado lógico.
* **Su Límite:** No todos los juegos tienen estrategias dominadas para eliminar.

### **Equilibrio de Nash (EN)**
* **La Idea:** Encontrar un "punto de estabilidad" o de "no arrepentimiento", donde ningún jugador puede mejorar su resultado cambiando su jugada si los demás no lo hacen.
* **Cuándo se Usa:** Es el concepto de solución más fundamental y general para juegos de un solo turno, tanto con estrategias puras como mixtas.
* **Su Límite:** Puede haber múltiples equilibrios (problema de coordinación) o, en estrategias puras, puede no haber ninguno, lo que obliga a usar estrategias mixtas.

---
---

## **Familia 2: Algoritmos de Búsqueda y Planificación (Para Juegos por Turnos)**

*Estos algoritmos exploran un "árbol de juego" para anticipar jugadas futuras en juegos como el ajedrez o el Ta-Te-Ti.*

### **MiniMax**
* **La Idea:** Explorar el árbol de juego asumiendo que juegas contra un oponente perfecto (un "Minimizador") que siempre tratará de minimizar tu puntuación. Tú, como "Maximizador", eliges la jugada que te da el mejor resultado en el peor de los casos.
* **Cuándo se Usa:** Es el algoritmo base para cualquier juego por turnos, determinista, de 2 jugadores, suma cero y con información perfecta.
* **Su Límite:** Su complejidad computacional ($O(b^d)$) lo hace inviable para juegos complejos sin optimizaciones.

### **Poda Alfa-Beta ($\alpha-\beta$)**
* **La Idea:** Una optimización inteligente de Minimax que "poda" o ignora ramas del árbol que no afectarán la decisión final, obteniendo el mismo resultado pero mucho más rápido.
* **Cuándo se Usa:** Siempre que se usa Minimax. En la práctica, son inseparables.
* **Su Límite:** Solo funciona de forma óptima en juegos de 2 jugadores.

### **ExpectiMiniMax**
* **La Idea:** Una versión de Minimax que puede manejar la incertidumbre. Tiene nodos MAX (para ti), MIN (para el oponente) y **CHANCE** (para eventos aleatorios). En los nodos de azar, calcula un **valor esperado**.
* **Cuándo se Usa:** Para juegos por turnos que combinan estrategia y suerte, como el Backgammon.

### **Maxⁿ**
* **La Idea:** La generalización de Minimax para juegos con más de 2 jugadores, donde todos son "Maximizadores" egoístas.
* **Cuándo se Usa:** Para juegos por turnos con 3 o más competidores.
* **Su Límite:** La poda Alfa-Beta no funciona, por lo que su complejidad es aún mayor.

### **Monte Carlo Tree Search (MCTS)**
* **La Idea:** En lugar de explorar el árbol de forma exhaustiva, simula miles de partidas rápidas y aleatorias (rollouts) para estimar qué jugadas son más prometedoras, construyendo un árbol de búsqueda de forma asimétrica.
* **Cuándo se Usa:** Es el estado del arte para juegos de información perfecta con árboles de búsqueda enormes (Go, Ajedrez moderno). Se usa como una función de evaluación muy potente.

---
---

## **Familia 3: Algoritmos de Aprendizaje (Para Agentes Adaptativos)**

*Estos algoritmos se usan cuando un agente no conoce las reglas o las estrategias de sus oponentes, y debe aprender a jugar bien a través de la experiencia.*

### **Fictitious Play (FP)**
* **La Idea:** Aprender observando el **historial** del oponente. Asume que la estrategia del oponente es la frecuencia con la que ha jugado cada acción en el pasado, y juega su mejor respuesta a esa creencia.
* **Cuándo se Usa:** Como un modelo simple de aprendizaje basado en creencias sobre un oponente estable.
* **Su Límite:** Asume que el oponente es estacionario y puede quedar atrapado en ciclos.

### **Regret Matching (RM)**
* **La Idea:** Aprender basándose en el **arrepentimiento**. La probabilidad de jugar una acción aumenta en proporción a cuánto te has arrepentido de no haberla jugado en rondas anteriores.
* **Cuándo se Usa:** Es un algoritmo de aprendizaje muy robusto que no necesita modelar explícitamente al oponente. Es la base de CFR.
* **Su Límite:** Por sí solo, se aplica a juegos repetidos de un solo paso.

### **Independent Q-Learning (IQL)**
* **La Idea:** Cada agente aprende con Q-Learning por su cuenta, **ignorando por completo** que los otros agentes también aprenden y cambian (los trata como parte del entorno).
* **Cuándo se Usa:** Como una línea base simple en problemas de Aprendizaje por Refuerzo Multiagente (MARL).
* **Su Límite:** El entorno se vuelve no estacionario, por lo que la convergencia a una buena solución no está garantizada.

### **Joint-Action Learning con Teoría de Juegos (JAL-GT)**
* **La Idea:** Un enfoque más inteligente. Cada agente aprende el valor de las **acciones conjuntas**. En cada estado, usa esos valores para construir una matriz de juego y la "resuelve" con teoría de juegos (ej. buscando un EN) para decidir qué hacer.
* **Cuándo se Usa:** En entornos multiagente donde se desea una coordinación explícita basada en la racionalidad del juego.
* **Su Límite:** Asume que todos los agentes pueden coordinarse perfectamente en cada paso para elegir y jugar el mismo equilibrio, lo cual es una suposición muy fuerte y poco realista.

### **Joint-Action Learning con Modelado de Agentes (JAL-AM)**
* **La Idea:** Un punto intermedio. Cada agente aprende los Q-valores de las acciones conjuntas, pero también mantiene un **modelo estadístico** de sus oponentes (como en Fictitious Play). Para decidir, calcula el valor esperado de sus acciones contra lo que *cree* que harán los demás.
* **Cuándo se Usa:** En entornos multiagente donde no se puede asumir una coordinación perfecta. Es un enfoque más robusto y práctico que JAL-GT.
* **Su Límite:** La calidad de su decisión depende de la precisión de su modelo sobre los oponentes.

### **Counterfactual Regret Minimization (CFR)**
* **La Idea:** El algoritmo maestro para juegos de información imperfecta. Itera millones de veces, recorriendo el árbol de juego y usando **Regret Matching** en cada **infoset** para minimizar el "arrepentimiento contrafáctico".
* **Cuándo se Usa:** Es el estado del arte para resolver juegos de suma cero, de 2 jugadores y de **información imperfecta**, como el Póker Heads-Up.
* **Su Límite:** Computacionalmente muy intensivo.