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
| A (Caso 4) x_1 = 21, x_2 = 12, x_3 = 4.8 --> z = 1(21)+1(12)-2(4.8)-22 = 33 - 9.6 - 22 = 1.4 >= 0   SÍ |
| B (Caso 1) x_1 = 12, x_2 = 10, x_3 = 13.6 --> z = 1(12)+1(10)-2(13.6)-22 = 22 - 27.2 - 22 = -27.2 < 0   NO |

## 3. Medición
Entrenamiento (casos 1–14):Predice SÍ / Predice NO
Real SÍ: VP = 2, FN = 0
Real NO: FP = 0, VN = 12
Prueba (casos 15–20):
Predice SÍ / Predice NO
Real SÍ: VP = 1, FN = 0
Real NO: FP = 0, VN = 5

**Entrenamiento (casos 1–14)**

| | Predice SÍ | Predice NO |
|---|---|---|
| **Real SÍ** | VP = <> | FN = <> |
| **Real NO** | FP = <> | VN = <> |

**Prueba (casos 15–20)**

| | Predice SÍ | Predice NO |
|---|---|---|
| **Real SÍ** | VP = <> | FN = <> |
| **Real NO** | FP = <> | VN = <> |

| Métrica | Entrenamiento | Prueba |
|---|---|---|
| Exactitud | <> | <> |
| Precisión | <> | <> |
| Sensibilidad | <> | <> |
| Especificidad | <> | <> |

**Entrenamiento contra prueba.** <¿Hay diferencia? ¿Por qué? ¿Sobreajuste o generalización?>

## 4. El error más caro

<¿Es peor el falso positivo o el falso negativo en su problema? ¿Por qué, según la ficha? ¿Qué cambiaron en el sistema para cometer menos ese error?>

## 5. ¿Comparado contra qué?

**Línea base trivial.** <Qué resultado saca un sistema que responde siempre lo mismo, y cómo se compara el suyo.>

| Checklist (Clase 6) | Respuesta |
|---|---|
| ¿Qué datos? | <> |
| ¿Qué métrica? | <> |
| ¿Comparado contra qué? | <> |
| ¿Quién lo midió? | <> |

## 6. Riesgo: el camino completo

<Datos → modelo → decisión → consecuencia aplicado a su caso. Al menos un riesgo concreto.>

## 7. Bitácora

**Uso de la IA**

| # | Qué le pedimos | Qué aceptamos | Qué rechazamos y por qué |
|---|---|---|---|
| 1 | <> | <> | <> |

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---|---|---|---|
| 1 | <> | <> | <> |
