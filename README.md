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
inversión      = medios + reserva de conversión + estructura
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

El presupuesto (51.400 €) es dinero **libre de estructura y salarios**, y la tarjeta *Encaje en el
presupuesto* invierte el cálculo: en vez de decirte cuánto costaría el mix, te dice qué conversión
estás **obligado** a alcanzar para que el mix entre en el presupuesto.

```
medios disponibles = presupuesto − reserva de conversión
MQLs que compra    = medios disponibles / coste por MQL
holgura            = MQLs que compra − MQLs que necesita el mix
conversión mínima  = clientes del mix / MQLs que compra
techo de clientes  = MQLs que compra × MQL→SQL × SQL→Opp × Opp→Won
```

La reserva de conversión (nurturing, SDR, cualificación, sales enablement) es el trade-off central:
cada euro que le pasás desde medios reduce los MQLs que podés comprar y por lo tanto **sube** la
tasa que tenés que alcanzar — pero es lo único que financia esa mejora de tasa.

Los mixes de bajo ticket y alto volumen quedan fuera de presupuesto automáticamente, y la banda se
pone en rojo indicando por cuánto se pasan.

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

Las métricas siguen la definición del área: **ROAS** = facturación ÷ inversión, **ROI** = (facturación − inversión) ÷ inversión, **CAC** = inversión ÷ clientes, cada una sobre inversión total y sin salario.

El planificador parte del objetivo de facturación. La calculadora va al revés: partís del **retorno
que querés sacarle a cada euro invertido** y sale cuánto habría que facturar.

```
facturación = ROAS × inversión
```

La inversión se puede medir de dos formas, y el mismo año da números muy distintos según cuál uses
— 2025 fue **1,04x** sobre inversión total y **2,15x** sobre medios:

| Base | Denominador | Para qué sirve |
| --- | --- | --- |
| Presupuesto variable | presupuesto (medios + reserva) | medir la eficacia de la pauta |
| Medios + estructura | presupuesto + estructura anual | ver si el área se paga sola |

Después baja de euros a MQLs con el ticket medio y las tasas del planificador, y compara contra lo
que el presupuesto puede pagar:

```
clientes necesarios = techo( facturación / ticket medio del mix )
MQLs necesarios     = clientes / (MQL→SQL × SQL→Opp × Opp→Won)
MQLs que compra     = (presupuesto − reserva) / coste por MQL
```

Si los MQLs necesarios entran en los que compra el presupuesto, el ROAS es alcanzable sin tocar
nada. Si no, el bloque *Qué tendría que pasar para llegar* muestra las tres salidas, cada una
resolviendo el hueco sola:

```
ticket necesario      = facturación / clientes que paga el presupuesto
conversión necesaria  = clientes necesarios / MQLs que compra
presupuesto necesario = MQLs necesarios × coste por MQL + reserva
```

La más barata casi siempre es el **ticket**: mover el mix hacia servicios caros no cuesta un euro
más de medios. El **ROAS máximo alcanzable** cierra el círculo — con el presupuesto, las tasas y el
ticket actuales, es el techo real, y hay un botón que lo carga directamente.

**Qué esperar con el ROAS que ponés.** El funnel no gasta todo el presupuesto: gasta lo que cuestan los
MQLs que hacen falta. Por eso la calculadora separa el ROAS que pedís (sobre lo disponible) del **ROAS
real** (sobre lo que se gasta), y muestra clientes, oportunidades, SQLs, MQLs, MQLs por mes, gasto real,
presupuesto sin usar y CAC. Un bloque aparte responde qué pasa si se gasta todo y la conversión sale
como la de 2025, a mitad de camino, o como la del plan.

La tabla de escenarios repite la cuenta de 1x a 10x con la inversión fija. Como la inversión no se
mueve, cada punto de ROAS suma siempre la misma facturación y pide MQLs en la misma proporción: en
este modelo no hay economía de escala, el único atajo es el ticket medio.

Las dos pestañas comparten el mismo estado: el presupuesto, las tasas y el ticket son los mismos de
un lado y del otro, y el botón *Usar esta facturación como objetivo* lleva la cifra al planificador
y regenera el mix para ella.

## Qué se puede editar

- Cantidad y **precio** de cada servicio
- Objetivo de facturación (campo o slider) y presupuesto a gastar (campo o slider)
- Reserva de conversión
- Las tres tasas de conversión: MQL→SQL, SQL→Opp y Opp→Cliente
- Coste por MQL y coste de estructura anual
- Ciclo de venta — recalcula la cadencia mensual de MQLs sobre los meses que realmente cierran
  dentro del año (los MQLs de los últimos meses no llegan a convertir)
- ROAS objetivo y base de cálculo, en la segunda pestaña

Cada bloque tiene un desplegable *Cómo se calcula* con las fórmulas y el criterio detrás de cada
número. Los cambios quedan guardados en `localStorage` (`funnel-planner-v3`, que recupera lo guardado en
`v2` menos la tasa vieja), así que la pestaña recuerda el último mix.

## Stack

Un solo `index.html`: HTML, CSS y JS sin dependencias ni build. Se abre con doble clic o se sirve
como estático.
