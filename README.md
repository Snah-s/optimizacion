#adsf

**1. En la sección de Optimización Estocástica, el profesor pregunta cómo se resuelve la función objetivo presentada.**

En la sección de **Optimización Estocástica**, presentamos la función objetivo:

$$
\min_x \{ c^T x + \mathbb{E}_\xi[Q(x, \xi)] \}
$$

**Resolución de la Función Objetivo:**

Para resolver esta función objetivo, seguimos un enfoque sistemático:

- **a) Discretización de la Incertidumbre:**
  La variable aleatoria $\xi$ representa la incertidumbre en los rendimientos futuros de los activos. Para manejar esta incertidumbre, discretizamos su distribución mediante la generación de un conjunto finito de escenarios posibles utilizando simulaciones Monte Carlo. Cada escenario $s$ tiene una probabilidad asociada $p_s$.

- **b) Reformulación Determinista Equivalente (RDE):**
  La esperanza matemática $\mathbb{E}_\xi[Q(x, \xi)]$ se reemplaza por una suma ponderada de los costos futuros en cada escenario:

  $$
  \mathbb{E}_\xi[Q(x, \xi)] = \sum_{s=1}^S p_s Q(x, \xi_s)
  $$

  Donde $S$ es el número total de escenarios generados.

- **c) Expansión de la Función Objetivo:**
  La función objetivo ahora se convierte en:

  $$
  \min_x \left\{ c^T x + \sum_{s=1}^S p_s Q(x, \xi_s) \right\}
  $$

  Donde $Q(x, \xi_s)$ es el costo futuro en el escenario $s$.

- **d) Incorporación de Restricciones:**
  El problema incluye restricciones que deben ser satisfechas en cada escenario y en general, como:

  - Restricción presupuestaria: $\sum_{i=1}^n x_i = 1$.
  - Restricciones de no negatividad: $x_i \geq 0$, para todo $i$.
  - Restricciones de retorno mínimo: $\sum_{i=1}^n r_i x_i \geq r_{\text{min}}$.

- **e) Formulación del Problema de Programación Lineal (o No Lineal):**
  Dependiendo de la naturaleza de $Q(x, \xi_s)$, el problema resultante puede ser lineal o no lineal. En nuestro caso, como el riesgo se mide mediante la varianza, que es una función cuadrática de $x$, obtenemos un problema de programación cuadrática.

- **f) Resolución Numérica:**
  Utilizamos algoritmos de optimización numérica para resolver el problema. En este trabajo, aplicamos el método **Sequential Least Squares Programming (SLSQP)**, que es adecuado para problemas no lineales con restricciones.

- **g) Implementación Computacional:**
  Implementamos el modelo en Python utilizando la función `minimize` de la librería `scipy.optimize`, especificando la función objetivo, las restricciones y los límites para las variables de decisión.

- **h) Interpretación de la Solución:**
  La solución óptima $x^*$ obtenida representa las proporciones de inversión en cada activo que minimizan el riesgo esperado del portafolio mientras cumplen con las restricciones impuestas.

---

**2. En la sección de Simulaciones Monte Carlo, el profesor pregunta cómo se obtienen las distribuciones probabilísticas estimadas para cada activo y cómo se halla la distribución de probabilidad.**

En la sección de **Simulaciones Monte Carlo**, mencionamos que se utilizan distribuciones probabilísticas estimadas para cada activo.

**Obtención de las Distribuciones Probabilísticas:**

- **a) Recopilación de Datos Históricos:**
  Obtenemos datos históricos de los precios de los activos financieros considerados en el portafolio. Estos datos suelen abarcar un período significativo para capturar diversas condiciones del mercado.

- **b) Cálculo de Rendimientos:**
  Calculamos los rendimientos diarios (o en otro intervalo de tiempo) de cada activo utilizando la fórmula:

  $$
  r_t = \frac{P_t - P_{t-1}}{P_{t-1}}
  $$

  Donde $P_t$ es el precio en el tiempo $t$.

- **c) Análisis Estadístico:**
  Analizamos los rendimientos históricos para estimar parámetros estadísticos clave:

  - **Media ($\mu$):** Representa el retorno promedio.
  - **Desviación Estándar ($\sigma$):** Mide la volatilidad de los rendimientos.
  - **Correlaciones ($\rho_{ij}$):** Calculamos la matriz de correlación entre los activos para considerar la dependencia entre ellos.

- **d) Selección de la Distribución:**
  Aunque los rendimientos financieros no siempre siguen una distribución normal, para simplificar y dado que suele ser una aproximación aceptable, asumimos que los rendimientos siguen una distribución normal multivariante.

- **e) Estimación de Parámetros:**
  Utilizamos los parámetros estimados ($\mu$, $\sigma$, $\rho_{ij}$) para definir completamente la distribución normal multivariante de los rendimientos.

- **f) Generación de Simulaciones:**
  Empleamos técnicas de simulación Monte Carlo para generar un gran número de escenarios de rendimientos futuros:

  - Generamos números aleatorios siguiendo una distribución normal estándar.
  - Aplicamos la transformación de Cholesky a la matriz de covarianza para introducir las correlaciones entre activos.
  - Obtenemos simulaciones de rendimientos que respetan tanto las medias y desviaciones estándar individuales como las correlaciones entre activos.

- **g) Validación de las Simulaciones:**
  Verificamos que las simulaciones generadas reproduzcan adecuadamente las características estadísticas observadas en los datos históricos.

**Cálculo de la Distribución de Probabilidad:**

- Las simulaciones generadas proporcionan una muestra de posibles rendimientos futuros.
- A partir de esta muestra, podemos estimar la distribución empírica de los rendimientos del portafolio.
- Esta distribución nos permite calcular métricas de riesgo como la varianza, el Valor en Riesgo (VaR), entre otras.

---

**3. En la sección de Metodología de Implementación, el profesor pregunta cómo se ajustan las decisiones iniciales para minimizar el riesgo y cómo se resuelve el modelo, incluyendo si existen restricciones y por qué aparece $x_i$ en la función objetivo.**

En la sección de **Modelo Matemático**, mencionamos que "se ajustan las decisiones iniciales para minimizar el riesgo".

**Ajuste de las Decisiones Iniciales:**

- **a) Decisiones Iniciales ($x$):**
  Las variables $x_i$ representan las proporciones iniciales de inversión en cada activo antes de conocer los rendimientos futuros.

- **b) Evaluación Bajo Escenarios Simulados:**
  Al generar múltiples escenarios de rendimientos futuros mediante simulaciones Monte Carlo, evaluamos cómo las decisiones iniciales $x$ afectan el desempeño del portafolio en términos de riesgo y retorno.

- **c) Proceso Iterativo de Optimización:**
  Utilizamos un algoritmo de optimización para ajustar las decisiones iniciales $x$ de manera que minimicen el riesgo esperado del portafolio. Este proceso considera las métricas de riesgo calculadas a partir de las simulaciones.

**Función Objetivo y Aparición de $x_i$:**

La función objetivo presentada es:

$$
\min_x \left\{ \sum_{i=1}^n x_i r_i + \mathbb{E} \left[ \sum_{i=1}^n x_i^2 \sigma_{ij} \right] \right\}
$$

- **Por qué aparece $x_i$:**
  - **Retorno Esperado:** El término $\sum_{i=1}^n x_i r_i$ calcula el retorno esperado del portafolio como una combinación lineal de los retornos esperados de los activos, ponderados por las proporciones de inversión $x_i$.
  - **Riesgo (Varianza):** El término $\mathbb{E} \left[ \sum_{i=1}^n x_i^2 \sigma_{ij} \right]$ representa el riesgo del portafolio, considerando la varianza y covarianza de los activos, y cómo las proporciones de inversión $x_i$ afectan el riesgo total.

**Restricciones del Modelo:**

- **Restricción de Presupuesto:**

  $$
  \sum_{i=1}^n x_i = 1
  $$

  Esto asegura que se invierta el 100\% del capital disponible.

- **Restricciones de No Negatividad:**

  $$
  x_i \geq 0, \quad \forall i
  $$

  Se asume que no se permite posiciones cortas (venta en corto).

- **Restricción de Retorno Mínimo:**

  $$
  \sum_{i=1}^n x_i r_i \geq r_{\text{min}}
  $$

  Garantiza que el portafolio alcance al menos un retorno esperado mínimo deseado.

**Resolución del Modelo:**

- **a) Formulación del Problema de Optimización:**
  El problema se formula como una **Programación Cuadrática** debido al término cuadrático en la función objetivo (varianza del portafolio).

- **b) Selección del Algoritmo de Optimización:**
  Utilizamos el método **SLSQP** (Sequential Least Squares Programming), que es adecuado para problemas no lineales con restricciones.

- **c) Implementación Computacional:**
  - Definimos la función objetivo y las restricciones en Python.
  - Utilizamos la función `minimize` de `scipy.optimize` especificando el método 'SLSQP'.
  - Establecemos los límites para $x_i$ y pasamos las restricciones al optimizador.

- **d) Resolución Numérica:**
  El algoritmo itera ajustando las variables $x_i$ para encontrar el mínimo de la función objetivo mientras cumple con todas las restricciones.

- **e) Obtención de la Solución Óptima:**
  El resultado es el conjunto de proporciones $x_i^*$ que minimizan el riesgo del portafolio dado el retorno mínimo y las restricciones establecidas.

**Conclusión:**

El ajuste de las decisiones iniciales se realiza mediante la optimización numérica de la función objetivo, que depende de las variables $x_i$. Estas variables aparecen en la función objetivo porque son las decisiones de inversión que afectan directamente el retorno y el riesgo del portafolio. El modelo incluye restricciones que garantizan la viabilidad y practicidad de la solución, y se resuelve utilizando métodos de optimización adecuados para problemas no lineales con restricciones.

---

Espero que estas respuestas aborden las preguntas del profesor y te ayuden a enriquecer tu documento. Puedes incorporar estas explicaciones en las secciones correspondientes para clarificar los puntos señalados.
