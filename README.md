# Planificador de Funnel

Herramienta interactiva para dimensionar el funnel de marketing necesario para un objetivo de
facturación anual. Ponés el objetivo y el presupuesto, y te devuelve el mix de servicios
recomendado más los MQLs, oportunidades y clientes que hacen falta, con inversión, ROI y CAC.

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

De ahí salen los dos parámetros que mueven todo: **coste por MQL** (72 € = 13.127 € / 182) y las
dos **tasas de conversión**. El cálculo va hacia atrás desde el mix:

```
clientes       = suma de las cantidades del mix
oportunidades  = clientes / (Opp→Won)
MQLs           = oportunidades / (MQL→Opp)
medios         = MQLs × coste por MQL
inversión      = medios + estructura
CAC variable   = coste por MQL / (MQL→Opp × Opp→Won)
```

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

El presupuesto (51.400 €) es dinero **libre de estructura y salarios**, y la tarjeta *Encaje en el
presupuesto* invierte el cálculo: en vez de decirte cuánto costaría el mix, te dice qué conversión
estás **obligado** a alcanzar para que el mix entre en el presupuesto.

```
medios disponibles = presupuesto − reserva de conversión
MQLs que compra    = medios disponibles / coste por MQL
holgura            = MQLs que compra − MQLs que necesita el mix
conversión mínima  = clientes del mix / MQLs que compra
techo de clientes  = MQLs que compra × MQL→Opp × Opp→Won
```

La reserva de conversión (nurturing, SDR, cualificación, sales enablement) es el trade-off central:
cada euro que le pasás desde medios reduce los MQLs que podés comprar y por lo tanto **sube** la
tasa que tenés que alcanzar — pero es lo único que financia esa mejora de tasa.

Los mixes de bajo ticket y alto volumen quedan fuera de presupuesto automáticamente, y la banda se
pone en rojo indicando por cuánto se pasan.

## Escenarios precargados

Calculados para un objetivo de 200.000 € y tasas objetivo del 15 % / 25 %. Al cambiar el objetivo
se rearman solos:

| Escenario | Mix | Clientes | MQLs | Conversión mínima* |
| --- | --- | --- | --- | --- |
| Recomendado | 3 DW, 4 Agentes, 3 Gestor, 2 Adopción | 12 | 320 | 2,0 % |
| Un solo DW | 1 DW, 6 Agentes, 5 Gestor, 2 Adopción | 14 | 374 | 2,3 % |
| Sin DW | 7 Agentes, 6 Gestor, 2 Adopción | 15 | 400 | 2,5 % |
| Concentrado | 5 DW, 2 Agentes, 1 Gestor, 2 Adopción | 10 | 267 | 1,7 % |
| Equilibrado | 2 DW, 4 Agentes, 3 Gestor, 6 Adopción, 10 Infra | 25 | 667 | 4,1 % |
| Volumen | 4 Agentes, 4 Gestor, 16 Adopción, 20 Infra | 44 | 1.174 | 7,3 % |
| Tasas 2025 | mismo mix recomendado, sin mejorar conversión | 12 | 1.127 | 2,0 % |

\* con 51.400 € de presupuesto y 8.000 € de reserva. Los dos últimos escenarios se pasan de
presupuesto: piden más MQLs de los que compran 43.400 €.

## Sensibilidad

La tabla del final repite la cuenta del funnel para cada combinación de las dos tasas:

```
MQLs = clientes del mix / (MQL→Opp × Opp→Won)
```

Los colores comparan contra los 182 MQLs de 2025, que es el volumen que ya está demostrado: verde
hasta 2,2x, ámbar hasta 4x, rojo por encima. Sirve para ver que bajar una fila (mejor cualificación)
ahorra muchos más MQLs que correrse una columna (mejor cierre) — y arreglar la cualificación es más
barato y más rápido.

## Qué se puede editar

- Cantidad y **precio** de cada servicio
- Objetivo de facturación (campo o slider) y presupuesto a gastar (campo o slider)
- Reserva de conversión
- Las dos tasas de conversión
- Coste por MQL y coste de estructura anual
- Ciclo de venta — recalcula la cadencia mensual de MQLs sobre los meses que realmente cierran
  dentro del año (los MQLs de los últimos meses no llegan a convertir)

Cada bloque tiene un desplegable *Cómo se calcula* con las fórmulas y el criterio detrás de cada
número. Los cambios quedan guardados en `localStorage`, así que la pestaña recuerda el último mix.

## Stack

Un solo `index.html`: HTML, CSS y JS sin dependencias ni build. Se abre con doble clic o se sirve
como estático.
