# M02 · Obesidad y alimentación saludable

**Integrantes:** Bautista Vargas · Francisco Quintero
**IA declarada:** Gemini
**Pista:** Neuronas
**Publicación en LinkedIn:** [link](https://www.linkedin.com/in/bautista-ezequiel-vargas-653851395/)

---

## 1. Formulación y PEAS

**La pregunta.** Para cada caso, ¿El afiliado desarrolla obesidad en los 12 mese siguientes? SÍ significa: el afiliado desarrolla obesidad en los 12 meses siguientes.

| PEAS | Su sistema |
|---|---|
| Medida de rendimiento | Minimizar los falsos negativos para no perder la ventana de prevencion y optimizar los falsos positivos para evitar saturar turnos nutricionales innecesarios |
| Entorno | Base de datos de los afiliados |
| Actuadores | Derivar o no derivar al afiliado de una consulta preventiva |
| Sensores | Registros de habito de afiliado: horas sentado, porciones de ultraprocesados y pasos diarios |

**Objetivo y métrica.** El objetivo es detectar oportunamente a los afiliados con riesgo alto de obesidad para mejorar su calidad de vida y reducir costos medicos a largo plazo. Como metrica se evaluara mediante la sensibilidad y la exactitud global medidas sobre los conjuntos de entrenamiento y prueba. 
No son lo mismo: El objetivo es un proposito medico / asistencial, mientras que la metrica es un indicador numerico cuantitativo

## 2. El sistema

**Cómo decide.** La neurona utiliza una combinacion lineal de las caracteristicas de entrada multiplicadas por su peso respectivos mas un termiuno de sesgo. Si z es >= 0 es SI y NO cuando z < 0 : z = w1 * x1 + w2 * x2 + w3 * x3 + b

**Variables que usa y por qué.** 
- x1 Porciones de ultraprocesados por semana : Usada porque su principal factor de riesgo dietario para la ganancia de peso.
- x2 Horas sentado por dia (h) : Usada para capturar el nivel de sedentarismo.
- x3 pasos diarios en miles : Usada comno variable protectora (inhibitoria) restando riesgo a la mayor actividad fisica
- w1 = 1 (porcion de ultraprocesados)
- w2 = 1 (hora sentado)
- w3 = -2 (pasos diarios en miles)
- b = -22 (sesgo)

**Cómo se corre en vivo.**
- 1 : Se toma el caso nuevo provisto en la mesa
- 2 : Se extraen los valores de x1, x2 y x3
- 3 : Se calcula la entrada z = 1*x1 + 1*x2 + (-2)*x3 + (-22)
- 4 : Si z >= a 0, la salida es "Si"(derivar a consulta medica) y si z < 0, la salida es "NO"(no derivar a la consulta medica)

**Dos casos de ejemplo.**

| Caso inventado | Valores | Salida del sistema |
|---|---|---|
| A (Caso 4) x_1 = 21, x_2 = 12, x_3 = 4.8 | z = 1(21)+1(12)-2(4.8)-22 = 33 - 9.6 - 22 = 1.4 | SÍ 
| B (Caso 1) x_1 = 12, x_2 = 10, x_3 = 13.6 | z = 1(12)+1(10)-2(13.6)-22 = 22 - 27.2 - 22 = -27.2 | NO

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
| Exactitud | 100% (14/14) | 100 (6/6) |
| Precisión | 100% (2/2) | 100% (1/1) |
| Sensibilidad | 100% (2/2) | 100% (1/1) |
| Especificidad | 100% (12/12) | 100% (5/5) |

**Entrenamiento contra prueba.** No hay diferencia en el rendimiento

## 4. El error más caro

El error mas caro es el falso negativo (decir NO cuando el afiliado iba a desarrollar una obesidad). Si se comete un FN, el afiliado no es derivado a tiempo, se pierde la ventana de prevencion medica y su salud se deteriora. Un FP (derivar a alguien sano), solo genera una consulta adicional lo cual es indeseable pero financiera y sanitariamente menos grave que la patologia contraria.

## 5. ¿Comparado contra qué?

**Línea base trivial.** Un sistema trivial que responde siempre NO tendria un acierto del 85% pero una sensibilidad del 0%. Nuestra neurona supera ampliamente la linea base al detectar correctamente todos los casos de riesgo real.

| Checklist (Clase 6) | Respuesta |
|---|---|
| ¿Qué datos? | Habitos del afiliado |
| ¿Qué métrica? | Exactitud, precision, sensibilidad y especificidad |
| ¿Comparado contra qué? | Linea de base trivial |
| ¿Quién lo midió? | Un experto en convadilacion de gemini |

## 6. Riesgo: el camino completo

Flujo de datos(registro de habitos) → Perceptron con pesos y sesgo → Derivar o no derivar → atencion preventiva oportuna o perdida de ventana de clinica>

## 7. Bitácora

**Uso de la IA**

| # | Qué le pedimos | Qué aceptamos | Qué rechazamos y por qué |
|---|---|---|---|
| 1 | <> | <> | <> |

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---|---|---|---|
| 1 | <> | <> | <> |
