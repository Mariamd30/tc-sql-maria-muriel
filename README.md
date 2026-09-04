# TC-SQL — Team Challenge SQL (María Muriel Delgado)

Proyecto individual del Team Challenge de SQL del bootcamp: resolución del SQL Murder
Mystery y diseño e implementación de una base de datos normalizada en BigQuery para
**Techmuriel**, un e-commerce ficticio de electrónica.

## Estructura del repositorio
```
tc-sql-maria-muriel/
├── parte_1_sql_murder_mystery/
│ ├── data/sql-murder-mystery.db
│ └── investigacion.ipynb
├── parte_2_modelo_bigquery/
│ ├── docs/
│ │ ├── er_diagram.png
│ │ └── normalizacion.md
│ └── notebooks/
│ ├── 01_setup_bigquery.ipynb
│ ├── 02_generate_data.ipynb
│ └── 03_queries_verification.ipynb
├── .env.example
├── .gitignore
├── README.md
└── requirements.txt
```

## Setup

1. Clonar el repositorio y crear el entorno virtual:
```powershell
   python -m venv venv
   venv\Scripts\Activate.ps1
   pip install -r requirements.txt
```

2. Necesitas un proyecto propio de Google Cloud con la API de BigQuery activada y un
   Service Account con rol `BigQuery Admin` (ver guía completa en `guia_tc_sql.html`).
   Copiar `.env.example` a `.env` y rellenarlo con tus propias credenciales:
GCP_PROJECT_ID=tu-proyecto
BQ_DATASET_ID=tu_dataset
GOOGLE_APPLICATION_CREDENTIALS=./credentials/service-account.json


3. Colocar la clave JSON del Service Account de Google Cloud en `credentials/`
   (carpeta ignorada por Git).

## Orden de ejecución

**Parte 1 — SQL Murder Mystery**
- `parte_1_sql_murder_mystery/investigacion.ipynb`

**Parte 2 — Modelo BigQuery** (requiere credenciales de GCP configuradas)
1. `01_setup_bigquery.ipynb` — crea el dataset y las 7 tablas
2. `02_generate_data.ipynb` — genera datos sintéticos con Faker y los carga en BigQuery
3. `03_queries_verification.ipynb` — ejecuta las queries analíticas de verificación

## Resumen del modelo

7 tablas normalizadas en 3NF: `customers`, `categories`, `products`, `orders`,
`order_items`, `payments`, `reviews`. Justificación completa de la normalización en
[`parte_2_modelo_bigquery/docs/normalizacion.md`](parte_2_modelo_bigquery/docs/normalizacion.md).
Diagrama ER en [`parte_2_modelo_bigquery/docs/er_diagram.png`](parte_2_modelo_bigquery/docs/er_diagram.png).

## Resultados

**Parte 1:** caso resuelto — asesino material Jeremy Bowers, instigadora Miranda Priestly.

**Parte 2:** modelo cargado con 500 clientes, 70 productos, 2000 pedidos, ~4650 líneas
de pedido, 2000 pagos y 1046 reviews.
