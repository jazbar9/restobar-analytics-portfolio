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


Información extra:
data_prep/ → limpieza y preparación de datos (01_data_cleaning.ipynb)
analysis/ → análisis de negocio, Fase 1 (02_business_analysis.ipynb)
market_basket/ → reglas de asociación de productos (03_market_basket_analysis.ipynb)
img/ → gráficos generados

