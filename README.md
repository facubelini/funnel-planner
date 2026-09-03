# Planificador de Funnel

Herramienta interactiva para dimensionar el funnel de marketing necesario para un objetivo de
facturación anual. Armás el mix de servicios y te devuelve cuántos MQLs, oportunidades y clientes
hacen falta, con la inversión, el ROI y el CAC asociados.

**En vivo:** https://facubelini.github.io/funnel-planner/

## Cómo funciona

Todo el modelo parte de la base real del año anterior:

| Métrica | Valor |
| --- | --- |
| MQLs | 182 |
| Oportunidades | 13 (7,1 % de los MQLs) |
| Clientes | 2 (15,4 % de las oportunidades) |
| Facturación | 28.200 € |
| Inversión | 27.000 € (13.127 € variable + 13.873 € estructura) |
| ROI | 1,04x |

De ahí salen los dos parámetros que mueven todo: **coste variable por MQL** (72 €) y las dos
**tasas de conversión**. El cálculo va hacia atrás desde el mix:

```
clientes       = suma de las cantidades del mix
oportunidades  = clientes / (Opp→Won)
MQLs           = oportunidades / (MQL→Opp)
inversión      = MQLs × coste por MQL + coste fijo
CAC variable   = coste por MQL / (MQL→Opp × Opp→Won)
```

El **CAC variable** es la métrica clave de la tabla de rentabilidad: si un servicio cuesta menos
que el CAC, adquirir ese cliente por marketing pago pierde dinero.

## Escenarios precargados

Los siete presets suman exactamente 200.000 €, así que se pueden comparar de forma directa:

| Escenario | Mix | Clientes | MQLs* |
| --- | --- | --- | --- |
| Recomendado · 3 DW | 3 DW, 4 Agentes, 3 Gestor, 2 Adopción | 12 | 320 |
| Un solo DW | 1 DW, 6 Agentes, 5 Gestor, 2 Adopción | 14 | 374 |
| Sin DW | 7 Agentes, 6 Gestor, 2 Adopción | 15 | 400 |
| Concentrado · 5 DW | 5 DW, 2 Agentes, 1 Gestor, 2 Adopción | 10 | 267 |
| Equilibrado | 2 DW, 4 Agentes, 3 Gestor, 6 Adopción, 10 Infra | 25 | 667 |
| Volumen | 4 Agentes, 4 Gestor, 16 Adopción, 20 Infra | 44 | 1.173 |
| Tasas 2025 (lineal) | mismo mix recomendado, sin mejorar conversión | 12 | 1.127 |

\* con tasas al benchmark (15 % / 25 %)

## Qué se puede editar

- Cantidad y **precio** de cada servicio
- Objetivo de facturación
- Las dos tasas de conversión
- Coste variable por MQL y coste fijo anual
- Ciclo de venta — recalcula la cadencia mensual de MQLs sobre los meses que realmente cierran
  dentro del año (los MQLs de los últimos meses no llegan a convertir)

Los cambios quedan guardados en `localStorage`, así que la pestaña recuerda el último mix.

## Stack

Un solo `index.html`: HTML, CSS y JS sin dependencias ni build. Se abre con doble clic o se
sirve como estático.
