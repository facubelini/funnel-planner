# Planificador de Funnel

Herramienta interactiva para dimensionar el funnel de marketing necesario para un objetivo de
facturación anual. Ponés el objetivo y el presupuesto, y te devuelve el mix de servicios
recomendado más los MQLs, SQLs, oportunidades y clientes que hacen falta, con inversión, ROI y CAC.
Todo está en una sola página: abajo del planificador, la calculadora de ROAS hace la cuenta inversa: partís de un ROAS y te dice cuánto habría que
facturar y si el funnel da para tanto.

**En vivo:** https://facubelini.github.io/funnel-planner/

## Cómo funciona

Todo el modelo parte de la base real del año anterior:

| Métrica | Valor |
| --- | --- |
| MQLs | 182 |
| Oportunidades | 13 (7,1 % de los MQLs) |
| SQLs | sin medir |
| Clientes | 2 (15,4 % de las oportunidades) |
| Facturación | 28.200 € |
| Inversión | 27.000 € (13.127 € variable + 13.873 € estructura) |
| ROI | 1,04x |

De ahí salen los dos parámetros que mueven todo: **coste por MQL** (72 € = 13.127 € / 182) y las
**tasas de conversión**. El cálculo va hacia atrás desde el mix:

```
clientes       = suma de las cantidades del mix
oportunidades  = clientes / (Opp→Won)
SQLs           = oportunidades / (SQL→Opp)
MQLs           = SQLs / (MQL→SQL)
medios         = MQLs × coste por MQL
inversión      = presupuesto total (salarios incluidos)
CAC variable   = coste por MQL / (MQL→SQL × SQL→Opp × Opp→Won)
```

## El paso de SQL

El funnel tiene cuatro etapas: **MQL → SQL → Oportunidad → Cliente**. El SQL es el filtro entre lo
que marketing entrega y lo que ventas acepta trabajar.

De 2025 sabemos que de 182 MQLs salieron 13 oportunidades — un **7,1 % de punta a punta** — pero
**no en qué salto se perdió el resto**, porque ese año el paso de SQL no se midió. Por eso las dos
tasas vienen precargadas de modo que su producto dé exactamente esa cifra:

| | MQL→SQL | SQL→Opp | MQL→Opp efectivo |
| --- | --- | --- | --- |
| Tasas objetivo | 40 % | 37,5 % | 15 % |
| Tasas 2025 | 25,5 % | 28 % | 7,14 % |

El reparto entre las dos es un **supuesto editable**, no un dato: el modelo devuelve exactamente los
mismos MQLs, ROI y CAC que antes de separar el paso, sólo que ahora muestra la etapa intermedia. En
cuanto haya dato real de SQLs, se mueven los dos sliders y todo se recalcula.

Por qué conviene separarlo: un 7,1 % puede ser **mucho volumen mal cualificado** (MQL→SQL bajo) o
**buena cualificación que ventas no trabaja** (SQL→Opp bajo). Son dos problemas distintos, con
dueños distintos y arreglos distintos, y hasta separarlos no se sabe cuál es. También separa dónde
debería pegar la reserva de conversión: nurturing y cualificación mueven MQL→SQL, sales enablement
mueve SQL→Opp.

La etapa de SQL en el funnel muestra el **coste por SQL** (lo que cuesta poner un lead aceptado
sobre la mesa) y los **MQLs descartados**, que son el desperdicio del embudo: leads ya pagados que
ventas no toma. En el bloque *vs 2025* dice `sin dato 2025`, porque no hay contra qué compararla.

El **CAC variable** es la métrica clave de la tabla de rentabilidad: si un servicio cuesta menos
que el CAC, adquirir ese cliente por marketing pago pierde dinero.

## Mix recomendado automático

Cada escenario no define cantidades fijas sino **pesos de facturación**, así que se recalcula solo
para cualquier objetivo que pongas:

```
cantidad = piso( objetivo × peso / precio )
techo    = techo( objetivo × peso / precio )
```

Al redondear para abajo queda un resto. Ese resto se reparte dándole el proyecto al servicio que
está más lejos de su cuota, **sin pasar de su techo**. El techo importa: sin él el hueco se rellena
con proyectos de 2.000–2.500 € porque son los únicos que caben, y el mix termina lleno de justo los
servicios que no conviene captar por marketing. Si al final todavía queda hueco, se suma un único
proyecto más — el que deje el total más cerca del objetivo, por arriba o por abajo.

Pesos del escenario recomendado: Digital Workplace 45 %, Agentes IA 28 %, Gestor documental 24 %,
Adopción IA 3 %. El tope del 45 % en el flagship es deliberado: es el servicio más rentable pero
también el más difícil de cerrar, y no conviene que más de la mitad del año dependa de él.

Tocar los steppers a mano desactiva el escenario y el mix pasa a ser manual.

## La restricción de presupuesto

El presupuesto (**80.000 €**) es el **total, con salarios incluidos**, y es la única inversión del
modelo: ROAS, ROI y CAC se calculan sobre ese número. Los MQLs salen de ahí a su coste por MQL; lo que no
se va en MQLs queda para salarios y el resto. No hay estructura ni reserva aparte.

```
coste en MQLs  = MQLs que necesita el mix × coste por MQL
queda          = presupuesto total − coste en MQLs
conversión mín = clientes del mix / (presupuesto total / coste por MQL)
```

## Escenarios precargados

Calculados para un objetivo de 200.000 € y tasas objetivo del 40 % / 37,5 % / 25 % (15 % de
MQL→Opp efectivo). Al cambiar el objetivo se rearman solos:

| Escenario | Mix | Clientes | MQLs | Conversión mínima* |
| --- | --- | --- | --- | --- |
| Recomendado | 3 DW, 4 Agentes, 3 Gestor, 2 Adopción | 12 | 320 | 2,0 % |
| Un solo DW | 1 DW, 6 Agentes, 5 Gestor, 2 Adopción | 14 | 374 | 2,3 % |
| Sin DW | 7 Agentes, 6 Gestor, 2 Adopción | 15 | 400 | 2,5 % |
| Concentrado | 5 DW, 2 Agentes, 1 Gestor, 2 Adopción | 10 | 267 | 1,7 % |
| Equilibrado | 2 DW, 4 Agentes, 3 Gestor, 6 Adopción, 10 Infra | 25 | 667 | 4,1 % |
| Volumen | 4 Agentes, 4 Gestor, 16 Adopción, 20 Infra | 44 | 1.174 | 7,3 % |
| Tasas 2025 | mismo mix recomendado, sin mejorar conversión | 12 | 1.121 | 2,0 % |

\* con 51.400 € de presupuesto y 8.000 € de reserva. Los dos últimos escenarios se pasan de
presupuesto: piden más MQLs de los que compran 43.400 €.

## Sensibilidad

La tabla del final repite la cuenta del funnel para cada combinación de las dos tasas:

```
MQLs = clientes del mix / (MQL→Opp efectivo × Opp→Won)
```

Las filas son el **MQL→Opp efectivo** — las dos tasas de arriba multiplicadas — así que la tabla se
mantiene en dos dimensiones. Los colores comparan contra los 182 MQLs de 2025, que es el volumen que ya está demostrado: verde
hasta 2,2x, ámbar hasta 4x, rojo por encima. Sirve para ver que bajar una fila (mejor cualificación)
ahorra muchos más MQLs que correrse una columna (mejor cierre) — y arreglar la cualificación es más
barato y más rápido.

## Calculadora de ROAS (misma página, debajo del planificador)

Las métricas siguen la definición del área: **ROAS** = facturación ÷ inversión, **ROI** = (facturación − inversión) ÷ inversión, **CAC** = inversión ÷ clientes, con la inversión igual al presupuesto total.

```
facturación = ROAS × presupuesto total
clientes    = techo( facturación / ticket medio )
MQLs        = clientes / (MQL→SQL × SQL→Opp × Opp→Won)
queda       = presupuesto total − MQLs × coste por MQL
```

Muestra clientes, oportunidades, SQLs, MQLs, MQLs por mes, cuánto del presupuesto se va en MQLs y cuánto
queda para salarios. Hay un techo de ROAS = ticket ÷ coste en MQLs por cliente que ningún presupuesto
supera. Un bloque aparte repite el cálculo con la conversión de 2025, a mitad de camino y la del plan.

## Qué se puede editar

- Cantidad y **precio** de cada servicio
- Objetivo de facturación (campo o slider) y presupuesto total (campo o slider)
- Las tres tasas de conversión: MQL→SQL, SQL→Opp y Opp→Cliente
- Coste por MQL
- Ciclo de venta — recalcula la cadencia mensual de MQLs sobre los meses que realmente cierran
  dentro del año (los MQLs de los últimos meses no llegan a convertir)
- ROAS objetivo y base de cálculo, en la segunda pestaña

Cada bloque tiene un desplegable *Cómo se calcula* con las fórmulas y el criterio detrás de cada
número. Los cambios quedan guardados en `localStorage` (`funnel-planner-v5`), así que la pestaña recuerda el último mix.

## Stack

Un solo `index.html`: HTML, CSS y JS sin dependencias ni build. Se abre con doble clic o se sirve
como estático.
