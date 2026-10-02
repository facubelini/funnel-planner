# Calculadora de ROAS

Herramienta de una sola página: ponés el **ROAS** que querés sobre el presupuesto total (salarios
incluidos) y te dice qué esperar: facturación, clientes, oportunidades, SQLs (reuniones) y MQLs, cuánto
del presupuesto se va en MQLs y cuánto queda para salarios y el resto.

**En vivo:** https://facubelini.github.io/funnel-planner/

## Base 2025

| Métrica | Valor |
| --- | --- |
| MQLs (el viejo SQL) | 182 |
| SQLs (reunión) | sin medir |
| Oportunidades | 13 |
| Clientes | 2 (28.200 €, ticket 14.100 €) |
| Inversión total | 27.000 € (ROAS 1,04x, ROI 4,44 %, CAC 13.500 €) |
| Inversión sin salario | 13.127 € (ROAS 2,15x, ROI 114,82 %, CAC 6.564 €) |

## Cómo se calcula

```
facturación   = ROAS × presupuesto total
clientes      = techo( facturación / ticket medio )
oportunidades = clientes / (Opp→Cliente)
SQLs          = oportunidades / (SQL→Opp)
MQLs          = SQLs / (MQL→SQL)
coste en MQLs = MQLs × coste por MQL
queda         = presupuesto total − coste en MQLs
```

La inversión es una sola cifra (80.000 € por defecto). ROAS = facturación ÷ inversión, ROI =
(facturación − inversión) ÷ inversión, CAC = inversión ÷ clientes. Hay un **techo** de ROAS = ticket ÷
(coste por MQL ÷ conversión de punta a punta) que ningún presupuesto supera.

Clientes, oportunidades, SQLs y MQLs son **editables**: si escribís una cifra, las demás se recalculan con
las tasas y el ROAS pasa a ser el resultado.

Un bloque aparte repite el cálculo con la conversión de 2025, a mitad de camino y la que tengas puesta:
la conversión de 2025 sale de 2 cierres, así que es un piso, no un pronóstico.

## Qué se puede editar

ROAS, presupuesto total, ticket medio, las tres tasas (MQL→SQL, SQL→Opp, Opp→Cliente), coste por MQL y
ciclo de venta. Todo queda guardado en `localStorage` (`funnel-planner-v6`).

## Stack

Un solo `index.html`: HTML, CSS y JS sin dependencias ni build.
