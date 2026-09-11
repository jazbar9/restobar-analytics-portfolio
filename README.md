# Análisis de Ventas — Restobar (Data Pipeline + Market Basket Analysis)

## Resumen del proyecto
Pipeline de datos y análisis de negocio sobre transacciones reales de un
restaurante bar (nombre omitido por confidencialidad), desde la exportación cruda
del sistema de punto de venta hasta hallazgos accionables que ya generaron
decisiones reales en el negocio. Incluye un componente de machine learning
(market basket analysis) para identificar qué productos se piden juntos.

## Estructura del repositorio

```
restobar-analytics-portfolio/
├── README.md
├── requirements.txt
├── .gitignore                          
├── data_prep/
│   └── 01_data_cleaning.ipynb          
├── analysis/
│   └── 02_business_analysis.ipynb      ← básicamente Fin de Fase 1 
│                                           
│                                           
├── market_basket/
│   └── 03_market_basket_analysis.ipynb 
└── img/
    ├── ingresototal_vs_ingresonocheoperada.png
    └── pedidos_vs_ticket.png
```

```
Información extra:
├─data_prep/ → limpieza y preparación de datos (01_data_cleaning.ipynb)
├─analysis/ → análisis de negocio, Fase 1 (02_business_analysis.ipynb)
├─market_basket/ → reglas de asociación de productos (03_market_basket_analysis.ipynb)
└─img/ → gráficos generados
```

## Contexto del proyecto
Análisis de datos transaccionales reales de un restaurant bar (nombre omitido por
confidencialidad del negocio), operando viernes, sábado y domingo desde
2022. Los datos provienen del sistema de punto de venta interno (WePOS), exportados como historial de pedidos ítem por ítem.

**Nota sobre privacidad:** el dataset original no se incluye en este
repositorio por decisión del negocio. Este README documenta el proceso
completo de limpieza, análisis y hallazgos, con outputs y gráficos
incluidos. Los nombres del personal fueron anonimizados.

## Preguntas de negocio
- ¿Cuáles son los productos más vendidos, en ingreso y en volumen?
- ¿Cómo ha evolucionado el ingreso mes a mes desde 2022?
- ¿La caída de ingresos observada desde marzo 2026 es real o un efecto de
  menos días operados?
- Si es real, ¿viene de menos clientes o de menor gasto por cliente?
- ¿Qué productos suelen pedirse juntos, y eso puede orientar una estrategia
  de combos?

## Proceso de limpieza y decisiones clave

**Estructura de los datos:** cada pedido genera una fila "cabecera" con el
nombre del mesero/a (Precio = 0, sin variación de producto) seguida de una
fila por cada producto vendido. Se separaron ambos niveles para no
contaminar el conteo de productos vendidos con los registros de personal.

**Corrección de "fecha de negocio":** el local opera hasta la madrugada
(viernes/domingo hasta 2-3am, sábado hasta 4-5am). Los pedidos hechos antes
de las 6am se reasignaron a la fecha de la noche anterior, para que un
pedido del sábado 1am se cuente como parte de la noche del viernes, tal
como opera el negocio realmente.

**Consolidación y anonimización de personal:** variantes como 'NN', 'NN2',
'NNN', 'Nn', 'Mary-Barra' correspondían todas a pedidos hechos en la barra
(confirmado con el negocio) y se unificaron bajo 'Barra'. Los nombres
reales del personal se anonimizaron como Mesero_1, Mesero_2, etc. desde el
inicio del pipeline, antes de cualquier cálculo de negocio.

**Valores nulos:**
- `Variación del Producto` y `Descuento`: nulos normales (no todos los
  productos tienen variación ni descuento), se rellenaron con "Sin
  variación" y 0 respectivamente.
- `Mesero` (1,023 filas, ~1%): vacíos reales por fallas de registro del
  personal, especialmente al inicio del sistema. Se categorizaron como
  "Sin registrar" en vez de eliminarse o inferirse.
- Una fila totalmente vacía (fila fantasma del export) se eliminó.

## Hallazgos principales

**1. Estacionalidad:** el negocio muestra un patrón estacional consistente
año a año, con picos a mediados de año (jul-oct) y valles a inicio de año
(dic-feb).

**2. Caída de ingresos desde marzo 2026 — confirmada, no es un artefacto
de calendario:** el ingreso promedio por noche operada (ajustado por
número real de noches trabajadas, no solo el total mensual) cayó ~14.5%
entre marzo-junio 2026 frente al promedio de abril 2025-febrero 2026.

Al cruzar los ingresos netos con las noches realmente operadas, se
corrigieron sesgos del análisis mensual tradicional (que solo mira el
total facturado):

- *Pico de eficiencia (febrero 2026):* el mes más eficiente de la serie
  reciente. Con un ingreso total moderado (S/ 35,263) mostró el mayor
  ingreso promedio por noche (S/ 2,938.58) operando solo 12 noches — la
  mejor relación consumo/noche del periodo analizado.
- *Abril y junio 2026 — la caída es real, no solo un efecto de
  calendario:* ambos meses tuvieron menos noches operadas por feriados y
  elecciones (10 noches cada uno, frente a un promedio de ~13). Sin
  embargo, el ingreso promedio por noche (S/ 2,324 en abril, S/ 2,239 en
  junio) también quedó por debajo del promedio histórico (~S/ 2,668),
  indicando que la caída no se explica solo por menos noches trabajadas.
- *Mayo 2026 — más noches, menor rendimiento por noche:* con 15 noches
  operadas (más que el promedio), el ingreso total fue de S/ 33,256, pero
  el ingreso promedio por noche (S/ 2,217.07) fue el más bajo del periodo
  analizado. El dataset no incluye información de costos, por lo que no
  es posible atribuir esto a gastos operativos.

**3. La caída viene de menor afluencia, no de menor gasto por cliente:**
el ticket promedio por pedido se mantuvo estable (~S/ 40) durante todo el
periodo. En cambio, los pedidos por noche cayeron ~16% en el mismo corte
(de ~66.7 a ~55.8 pedidos/noche). Julio 2026 muestra recuperación parcial
hacia los niveles históricos.

**4. Factores de contexto identificados junto con el negocio:** parte de
la caída coincide con días no operados por feriados/elecciones (Viernes
Santo el 5 de abril, primera vuelta electoral 11-12 de abril, segunda
vuelta 6-7 de junio de 2026 — no laborales por disposición legal). Sin
embargo, el ajuste por noche operada confirma que la caída persiste más
allá de estos días puntuales. El negocio también reportó una reducción en
su inversión de publicidad en redes sociales desde inicios de 2026,
coincidiendo con el periodo de menor afluencia. Meses con publicidad
reforzada (febrero y julio 2026) muestran los mejores desempeños del
periodo reciente. Sin datos de inversión y alcance de campañas, esto se
presenta como hipótesis cualitativa, no como relación causal comprobada.

**5. Top 5 productos por ingreso neto:**
```
| Producto | Ingreso Neto (S/.) |
|---|---|
| Pilsen | 113,730 |
| Medellín | 91,712 |
| Cuzqueña Trigo | 83,340 |
| Cartavio Black | 65,631 |
| Jarra Pomalca Rubio | 51,660 |
```

**6. Desempeño por mesero (top 10 por ticket promedio, mín. 10 pedidos):**
```
| Mesero | Pedidos Únicos | Ticket Promedio (S/.) |
|---|---|---|
| Mesero_1 | 11 | 56.00 |
| Mesero_2 | 64 | 55.23 |
| Mesero_3 | 34 | 52.44 |
| Mesero_4 | 65 | 48.60 |
| Sin registrar | 745 | 47.97 |
| Mesero_5 | 223 | 47.39 |
| Mesero_6 | 45 | 46.76 |
| Mesero_7 | 62 | 46.55 |
| Mesero_8 | 49 | 44.49 |
| Mesero_9 | 177 | 43.69 |
```
**7. Pedidos sin registrar:** los 745 pedidos categorizados como "Sin
registrar" arrojaron un ticket promedio de S/ 47.96, dentro del rango de
ticket promedio observado en el resto del personal (S/ 43.69-56.00). Esto
sugiere que estos pedidos huérfanos no corresponden a un patrón de consumo
atípico y no distorsionan las métricas generales. El negocio ya tenía
conocimiento de que el personal a veces omitía registrar su nombre en el
sistema, pero al dimensionar la magnitud del problema (745 pedidos),
decidió implementar una política más estricta de registro obligatorio —
un ejemplo concreto de cómo este análisis generó una acción operativa
real.

## Market Basket Analysis (Machine Learning)
**Nota metodológica importante:** el sistema POS genera un "pedido" (ticket)
por cada ronda de consumo, no por mesa ni por visita completa. Una misma
mesa puede generar varios pedidos independientes a lo largo de una noche
(ej. una ronda de bebidas, y horas después otra). Esto significa que el
análisis identifica productos que se piden juntos *en un mismo momento*,
no el patrón de consumo completo de un cliente durante toda su visita.

Esto se confirma en los datos: el 87% de los pedidos (39,209 de 45,068)
contienen un solo producto, con un promedio de 1.15 productos por pedido.
Bajo esta estructura, es esperable encontrar pocas combinaciones
frecuentes de 2+ productos — no es una limitación del algoritmo, sino de
la unidad de análisis disponible.

Aun así, se encontraron dos asociaciones con soporte estadístico (Apriori,
min_support=0.003, lift ≥ 1.0):

- **Inca Kola → Pilsen** (confianza 26.9%, lift 2.14): los pedidos que
  incluyen Inca Kola tienen más del doble de probabilidad de incluir
  también una Pilsen, comparado con el promedio general — es un posible patrón
  de consumo mixto alcohol/no-alcohol dentro de un mismo grupo.
- **Cuzqueña Trigo ↔ Cuzqueña Malta** (lift 1.38): asociación moderada,
  consistente con clientes explorando variantes de una misma marca.

**Recomendación para el negocio:** un análisis de combos más preciso
orientado a "qué se consume por mesa en una visita completa" requeriría
que el sistema POS capture un identificador de mesa que agrupe los
pedidos de una misma sesión de consumo — la cual es una mejora concreta derivada de
este análisis.

## Limitaciones del dataset
- No hay identificador de cliente, por lo que no es posible analizar
  clientes recurrentes ni frecuencia de visita individual.
- La relación entre publicidad y ventas es una correlación temporal
  observada, no una relación causal comprobada con datos de marketing.
- El ticket promedio por mesero puede reflejar el tipo de mesa/turno
  asignado, no necesariamente habilidad individual de venta.
- El dataset no incluye información de costos operativos, por lo que no
  es posible relacionar ingresos con rentabilidad real.

## Impacto
Este análisis generó las siguientes acciones y recomendaciones concretas:

- **Registro de personal:** al dimensionar que 745 pedidos (~0.8% del
  total) carecían de mesero registrado por fallas de captura, el negocio
  implementó una política de registro obligatorio en el sistema POS.
- **Revisión de percepción sobre el desempeño del personal:** el dueño
  percibía a un par de meseros como los más destacados del equipo; los
  datos mostraron que, si bien su ticket promedio por pedido era alto, su
  volumen de pedidos atendidos era menor que el del resto del personal —
  evidenciando que la percepción y el desempeño medido en datos no
  siempre coinciden.
- **Estrategia de combos — hipótesis refutada con evidencia:** la
  hipótesis inicial de impulsar combos entre productos top y de menor
  venta no se sostuvo con los datos: el 87% de los pedidos contiene un
  solo producto, indicando que el consumo real ocurre en rondas
  sucesivas por mesa, no en pedidos combinados. Esto derivó en una
  recomendación de mejora al sistema POS: capturar un identificador de
  mesa para poder analizar el consumo completo de una visita, no solo de
  cada ronda individual.
  
## Próximos pasos (Fase 2)
Cruzar el gasto y alcance semanal de campañas de Meta Ads con los pedidos
por noche, para evaluar si la correlación observada entre publicidad y
afluencia se sostiene con datos de marketing reales (fuera del alcance de
este repositorio) en la Fase 2 de este análisis.

## Herramientas
Python (pandas, matplotlib, seaborn, mlxtend), Google Colab.