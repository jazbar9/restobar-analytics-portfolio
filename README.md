# Restobar Analytics Portfolio
restobar-analytics-portfolio/
├── README.md                      ← case study: problema → datos → enfoque → resultado
├── requirements.txt
├── .gitignore                     ← incluye /data (dato crudo nunca sube)
├── data_prep/
│   └── 01_data_cleaning.ipynb     ← tu notebook de limpieza (fecha de negocio, meseros, nulos)
├── analysis/
│   └── 02_market_basket_analysis.ipynb   ← reglas de asociación (Apriori/FP-Growth)
└── img/
    ├── evolucion_ingresos.png
    └── reglas_asociacion.png       ← visualización de las combinaciones más frecuentes