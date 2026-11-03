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
| Medida de rendimiento | Minimizar los falsos negativos para no perder la ventana de prevención médica y mantener bajo control la tasa de falsos positivos para no saturar la agenda de turnos nutricionales. |
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
- $b = -22$ (Sesgo, fijado exclusivamente observando los casos de entrenamiento).

**Cómo se corre en vivo.**
1. Se toma el caso nuevo no visto provisto en la mesa de evaluación.
2. Se extraen los valores de $x_1$, $x_2$ y $x_3$.
3. Se calcula la entrada neta: $z = 1 \cdot x_1 + 1 \cdot x_2 + (-2) \cdot x_3 + (-22)$.
4. Si $z \ge 0$, la salida es "SÍ" (derivar a consulta preventiva); si $z < 0$, la salida es "NO" (no derivar).

**Dos casos de ejemplo.**

| Caso de la ficha | Valores | Salida del sistema |
|---|---|---|
| Caso 4 | $x_1 = 21, x_2 = 12, x_3 = 4.8 \rightarrow z = 1(21) + 1(12) - 2(4.8) - 22 = 33 - 9.6 - 22 = 1.4 \ge 0$ | SÍ |
| Caso 1 | $x_1 = 12, x_2 = 10, x_3 = 13.6 \rightarrow z = 1(12) + 1(10) - 2(13.6) - 22 = 22 - 27.2 - 22 = -27.2 < 0$ | NO |

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
| **Real SÍ** | VP = 0 | FN = 1 |
| **Real NO** | FP = 0 | VN = 5 |

| Métrica | Entrenamiento | Prueba |
|---|---|---|
| Exactitud | 100% (14/14) | 83.3% (5/6) |
| Precisión | 100% (2/2) | N/A (0/0) |
| Sensibilidad | 100% (2/2) | 0% (0/1) |
| Especificidad | 100% (12/12) | 100% (5/5) |

**Entrenamiento contra prueba.** Observamos una caída en el rendimiento al pasar al conjunto de prueba. Mientras que en entrenamiento la sensibilidad es del 100%, en prueba cae al 0% al fallar en detectar el único caso positivo (Caso 15). Esto demuestra que los pesos y el sesgo ($b=-22$) ajustados con los 14 primeros casos no lograron generalizar correctamente el umbral para un caso positivo limítrofe en datos nuevos.

---

## 4. El error más caro

El error más caro es el **falso negativo** (decir NO cuando el afiliado sí iba a desarrollar obesidad). Si se comete un FN, el afiliado no es derivado a tiempo, se pierde la ventana de prevención médica y su salud se deteriora, lo cual es financieramente y sanitariamente más grave que un FP (derivar a alguien sano a una consulta extra).

**Qué cambiamos en el sistema:** El Caso 15 produjo un Falso Negativo evidente. Alterar el sesgo mirando este caso sería cometer "fuga de datos" (data leakage), invalidando la evaluación. Por lo tanto, el cambio sistémico que proponemos es **incorporar una nueva variable al modelo** (por ejemplo, $x_4$: "Antecedentes familiares de obesidad", con peso excitatorio), o recolectar un volumen mucho mayor de casos positivos en la fase de entrenamiento para que el algoritmo calibre un hiperplano de decisión más robusto que no dependa exclusivamente de tres variables de hábitos.

---

## 5. ¿Comparado contra qué?

**Línea base trivial.** Un sistema trivial que responda siempre "NO" tendría una exactitud del 85.7% en entrenamiento (12/14) y 83.3% en prueba (5/6), pero su **sensibilidad sería del 0%** (dejaría pasar todos los casos con riesgo real de obesidad). 

| Checklist (Clase 6) | Respuesta |
|---|---|
| ¿Qué datos? | Datos de hábitos del afiliado provistos en la ficha (horas sentado, porciones de ultraprocesados por semana y pasos diarios en miles). |
| ¿Qué métrica? | Exactitud, Precisión, Sensibilidad y Especificidad sobre las matrices de confusión de entrenamiento y prueba. |
| ¿Comparado contra qué? | Línea base trivial que predice siempre la clase mayoritaria (NO). |
| ¿Quién lo midió? | Los alumnos integrantes del grupo: Bautista Vargas y Francisco Quintero, calculando manualmente cada caso de la ficha M02.csv. |

---

## 6. Riesgo: el camino completo

Flujo: Datos (registros de hábitos del afiliado) $\rightarrow$ Modelo (perceptrón ponderado) $\rightarrow$ Decisión (derivar / no derivar) $\rightarrow$ Consecuencia (atención preventiva oportuna o pérdida de la ventana clínica).

**Riesgo concreto:** Sub-reporte de datos por sesgo de deseabilidad social (por ejemplo, que el afiliado declare menos porciones de ultraprocesados en la app de las que realmente consume). Esto generaría una entrada neta $z$ artificialmente baja, provocando un Falso Negativo que priva al paciente de la consulta preventiva y deriva en el desarrollo no detectado de obesidad a largo plazo.

---

## 7. Bitácora

**Uso de la IA**

| # | Qué le pedimos | Qué aceptamos | Qué rechazamos y por qué |
|---|---|---|---|
| 1 | Pedimos a Gemini diseñar la neurona evaluando las tres variables del archivo M02.csv para ajustar los pesos iniciales del perceptrón. | Aceptamos usar $x_1$ (ultraprocesados) y $x_2$ (horas sentado) como entradas excitatorias y $x_3$ (pasos) como inhibitoria. | Rechazamos asignar peso positivo a los pasos diarios, ya que a mayor actividad física disminuye el riesgo de obesidad. |
| 2 | Pedimos redactar y estructurar las matrices de confusión y métricas para el README final, calculando de forma honesta con $b=-22$. | Aceptamos la estructura tabular, asumiendo el falso negativo real en prueba. | Rechazamos la sugerencia de la IA de modificar iterativamente el sesgo para "arreglar" el Caso 15, ya que comprendimos que eso es fuga de datos. |

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---|---|---|---|
| 1 | 7/10 | Matriz de prueba inconsistente; faltaba aclarar qué cambiamos para reducir el FN y quién midió. | Alteramos incorrectamente el sesgo mirando el caso de prueba para forzar el VP, cayendo en data leakage. |
| 2 | 7.5/10 | Detectó la fuga de datos: el sesgo se fijó mirando un caso de prueba. Falso negativo arreglado de forma artificial validando con el mismo caso. Contradicción en la bitácora. | Revertimos el sesgo a $b=-22$ (fijado solo con entrenamiento). Asumimos la caída a 0% de sensibilidad en prueba y reemplazamos el parche manual por una propuesta sistémica válida (agregar variables/datos) para el punto 4. Corregimos las contradicciones de la bitácora. |
