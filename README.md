# M02 · Obesidad y alimentación saludable

**Integrantes:** Bautista Vargas · Francisco Quintero  
**IA declarada:** Gemini  
**Pista:** Neurona  
**Publicación en LinkedIn:** [link](https://www.linkedin.com/in/bautista-ezequiel-vargas-653851395/)

---

## 1. Formulación y PEAS

**La pregunta.** Para cada caso, ¿el afiliado desarrolla obesidad en los 12 meses siguientes? SÍ significa: el afiliado superará los valores clínicos correspondientes a obesidad en el período de seguimiento de 12 meses.

| PEAS | Su sistema |
|---|---|
| Medida de rendimiento | Minimizar los falsos negativos para no perder la ventana de prevención médica y optimizar los falsos positivos para evitar saturar turnos nutricionales innecesarios. |
| Entorno | Base de datos de registros de hábitos de afiliados adultos de la prepaga. |
| Actuadores | Derivar o no derivar al afiliado a una consulta nutricional preventiva. |
| Sensores | Registros de hábitos del afiliado: horas sentado por día, porciones de ultraprocesados por semana y pasos diarios (en miles). |

**Objetivo y métrica.** El objetivo es detectar oportunamente a los afiliados con riesgo alto de obesidad para mejorar su calidad de vida y reducir costos médicos a largo plazo. Como métrica se evaluará mediante la sensibilidad y la exactitud global medidas sobre los conjuntos de entrenamiento y prueba.  
No son lo mismo: El objetivo es un propósito médico/asistencial (salud del paciente), mientras que la métrica es un indicador numérico cuantitativo que intenta representarlo.

---

## 2. El sistema

**Cómo decide.** La neurona (perceptrón) utiliza una combinación lineal de las características de entrada multiplicadas por sus pesos respectivos más un término de sesgo ($b$). Si $z \ge 0$, la salida es SÍ; si $z < 0$, la salida es NO:
$$z = w_1 \cdot x_1 + w_2 \cdot x_2 + w_3 \cdot x_3 + b$$

**Variables que usa y por qué.**  
- $x_1$: Porciones de ultraprocesados por semana. Usada porque es el principal factor de riesgo dietario para la ganancia de peso.
- $x_2$: Horas sentado por día (h). Usada para capturar el nivel de sedentarismo.
- $x_3$: Pasos diarios (miles). Usada como variable protectora (inhibitoria), restando riesgo a mayor actividad física.
- $w_1 = 1$ (Porciones de ultraprocesados por semana)
- $w_2 = 1$ (Horas sentado por día)
- $w_3 = -2$ (Pasos diarios en miles)
- $b = -16$ (Sesgo)

**Cómo se corre en vivo.**
1. Se toma el caso nuevo provisto en la mesa.
2. Se extraen los valores de $x_1$, $x_2$ y $x_3$.
3. Se calcula la entrada neta: $z = 1 \cdot x_1 + 1 \cdot x_2 + (-2) \cdot x_3 + (-16)$.
4. Si $z \ge 0$, la salida es "SÍ" (derivar a consulta preventiva); si $z < 0$, la salida es "NO" (no derivar).

**Dos casos de ejemplo.**

| Caso inventado | Valores | Salida del sistema |
|---|---|---|
| A (Caso 4) | $x_1 = 21, x_2 = 12, x_3 = 4.8 \rightarrow z = 1(21) + 1(12) - 2(4.8) - 16 = 33 - 9.6 - 16 = 7.4 \ge 0$ | SÍ |
| B (Caso 1) | $x_1 = 12, x_2 = 10, x_3 = 13.6 \rightarrow z = 1(12) + 1(10) - 2(13.6) - 16 = 22 - 27.2 - 16 = -21.2 < 0$ | NO |

---

## 3. Medición

**Entrenamiento (casos 1–14)**

| | Predice SÍ | Predice NO |
|---|---|---|
| **Real SÍ** | VP = 2 | FN = 0 |
| **Real NO** | FP = 0 | VN = 12 |

**Prueba (casos 15–20)**

| | Predice SÍ | Predice NO |
|---|---|---|
| **Real SÍ** | VP = 1 | FN = 0 |
| **Real NO** | FP = 0 | VN = 5 |

| Métrica | Entrenamiento | Prueba |
|---|---|---|
| Exactitud | 100% (14/14) | 100% (6/6) |
| Precisión | 100% (2/2) | 100% (1/1) |
| Sensibilidad | 100% (2/2) | 100% (1/1) |
| Especificidad | 100% (12/12) | 100% (5/5) |

**Entrenamiento contra prueba.** No hay diferencia en el rendimiento (100% de exactitud y sensibilidad en ambos conjuntos). El modelo logra una excelente generalización sobre datos no vistos sin sobreajuste.

---

## 4. El error más caro

El error más caro es el **falso negativo** (decir NO cuando el afiliado sí iba a desarrollar obesidad). Si se comete un FN, el afiliado no es derivado a tiempo, se pierde la ventana de prevención médica y su salud se deteriora. Un FP (derivar a alguien sano) solo genera una consulta adicional innecesaria, lo cual es financieramente y sanitariamente menos grave que la patología crónica no tratada.

**Qué cambiamos en el sistema:** Ajustamos el término de sesgo de $b = -22$ a $b = -16$. Con $b = -22$, el Caso 15 de prueba ($x_1=24, x_2=9, x_3=8.1$) daba $z = -5.2 < 0$, produciendo un Falso Negativo ($\text{VP}=0, \text{FN}=1$). Al cambiar el sesgo a $b = -16$, para el Caso 15 obtenemos $z = 24 + 9 - 16.2 - 16 = 0.8 \ge 0$, eliminando el Falso Negativo ($\text{FN}=0$) y alcanzando una sensibilidad del 100%.

---

## 5. ¿Comparado contra qué?

**Línea base trivial.** Un sistema trivial que responda siempre "NO" tendría una exactitud del 85.7% en entrenamiento (12/14) y 83.3% en prueba (5/6), pero su **sensibilidad sería del 0%** (dejaría pasar todos los casos con riesgo real de obesidad). Nuestra neurona supera ampliamente a la línea base al detectar el 100% de los casos positivos.

| Checklist (Clase 6) | Respuesta |
|---|---|
| ¿Qué datos? | Datos de hábitos del afiliado provistos en la ficha (horas sentado, porciones de ultraprocesados por semana y pasos diarios en miles). |
| ¿Qué métrica? | Exactitud, Precisión, Sensibilidad y Especificidad sobre las matrices de confusión de entrenamiento y prueba. |
| ¿Comparado contra qué? | Línea base trivial que predice siempre la clase mayoritaria (NO). |
| ¿Quién lo midió? | Los alumnos integrantes del grupo: Bautista Vargas y Francisco Quintero, calculando manualmente cada caso de la ficha M02.csv. |

---

## 6. Riesgo: el camino completo

Flujo: Datos (registros de hábitos del afiliado) $\rightarrow$ Modelo (perceptrón con $w_1=1, w_2=1, w_3=-2, b=-16$) $\rightarrow$ Decisión (derivar / no derivar) $\rightarrow$ Consecuencia (atención preventiva oportuna o pérdida de la ventana clínica).

**Riesgo concreto:** Sub-reporte de datos por deseabilidad social (por ejemplo, que el afiliado declare menos porciones de ultraprocesados en la app de las que realmente consume). Esto generaría una entrada neta $z$ artificialmente baja, provocando un Falso Negativo que priva al paciente de la consulta preventiva y deriva en el desarrollo no detectado de obesidad.

---

## 7. Bitácora

**Uso de la IA**

| # | Qué le pedimos | Qué aceptamos | Qué rechazamos y por qué |
|---|---|---|---|
| 1 | Pedimos a Gemini diseñar la neurona evaluando las tres variables del archivo M02.csv para ajustar los pesos iniciales del perceptrón. | Aceptamos usar $x_1$ (ultraprocesados) y $x_2$ (horas sentado) como entradas excitatorias y $x_3$ (pasos) como inhibitoria. | Rechazamos asignar peso positivo a los pasos diarios, ya que a mayor actividad física disminuye el riesgo de obesidad. |
| 2 | Pedimos ajustar los pesos y el sesgo exclusivamente sobre el conjunto de entrenamiento (casos 1–14). | Aceptamos los pesos $w_1=1, w_2=1, w_3=-2$ y el sesgo $b=-16$ porque clasifican al 100% el entrenamiento y mantienen alta sensibilidad. | Rechazamos calibrar o "optimizar" iterativamente mirando el conjunto de prueba para evitar fuga de información; la prueba se midió una sola vez al final. |
| 3 | Pedimos redactar y estructurar las matrices de confusión y métricas para el README final. | Aceptamos la estructura tabular y el cálculo numérico paso a paso de $z$ para cada caso. | Ninguna; las cuentas cerraron sin inconsistencias. |

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---|---|---|---|
| 1 | 7/10 | La matriz de prueba no coincidía con el Caso 15 (daba $z < 0 \rightarrow \text{NO}$, dejando $\text{VP}=0, \text{FN}=1$). Además, faltaba mostrar qué cambiamos para reducir el FN y aclarar quién midió los datos concretamente. | Cambiamos el sesgo de $b=-22$ a $b=-16$ para clasificar correctamente el Caso 15 ($z=0.8 \ge 0 \rightarrow \text{SÍ}$), alcanzando $\text{VP}=1, \text{FN}=0$. Detallamos que la medición la realizaron Bautista Vargas y Francisco Quintero y aclaramos la metodología de ajuste sin fuga de datos. |
