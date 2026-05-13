# TPO Grupo 4 — Análisis de datos Ecobici BA (2022-2024)

Pipeline de datos completo sobre el sistema de bicicletas compartidas de Buenos Aires:
ingesta, limpieza, análisis exploratorio y modelo de machine learning para predecir
la duración de los recorridos.

## Arquitectura del pipeline (Medallion)

```
Datasets/ (CSV)
     │
     ▼
data/raw/       ← Notebook 01: CSV → Parquet sin transformaciones
     │
     ▼
data/stg/       ← Notebook 02: Limpieza (sklearn Pipeline, 8 pasos) + EDA + métricas de calidad
     │
     ├──► data/mart_bi/   ← Notebook 03: Modelo estrella (fact + 4 dimensiones)
     │
     └──► data/mart_ml/   ← Notebook 03: Features para ML + predicciones
```

## Estructura del repositorio

```
TPO_G4_PYTHON/
├── Datasets/                          ← CSVs originales (no incluidos en el repo)
├── data/                              ← Generado al correr los notebooks
│   ├── raw/                           ← Parquet crudos por año
│   ├── stg/                           ← Parquet limpios y unificados
│   ├── mart_bi/                       ← Modelo estrella (dim_* + fact_*)
│   └── mart_ml/                       ← Features y predicciones
├── notebooks/
│   ├── 01_ETL_RAW.ipynb               ← Ingesta CSV → Parquet
│   ├── 02_ETL_STG_EDA.ipynb           ← Limpieza, EDA y calidad de datos
│   ├── 03_MART_ML.ipynb               ← Feature engineering, BI y modelo ML
│   └── viz*.png                       ← Visualizaciones generadas
├── requirements.txt
└── .gitignore
```

## Requisitos previos

- Python 3.10 o superior
- pip

## Instalación

```bash
# 1. Clonar el repositorio
git clone https://github.com/GianMontemarani/TPO_G4_PYTHON.git
cd TPO_G4_PYTHON

# 2. Crear y activar entorno virtual (recomendado)
python -m venv venv

# Windows
venv\Scripts\activate

# macOS / Linux
source venv/bin/activate

# 3. Instalar dependencias
pip install -r requirements.txt
```

## Datasets

Los archivos CSV **no están incluidos en el repositorio** por su tamaño (≈2.3 GB).
Descargalos desde el portal de datos abiertos de Buenos Aires y colocalos en la carpeta `Datasets/`:

| Archivo esperado | Descripción |
|---|---|
| `badata_ecobici_recorridos_realizados_2024.csv` | Recorridos 2024 |
| `trips_2022.csv` | Recorridos 2022 |
| `trips_2023.csv` | Recorridos 2023 |
| `usuarios_ecobici_2022.csv` | Usuarios 2022 |
| `usuarios_ecobici_2023.csv` | Usuarios 2023 |
| `usuarios_ecobici_2024.csv` | Usuarios 2024 |

> Fuente: [data.buenosaires.gob.ar](https://data.buenosaires.gob.ar/dataset/bicicletas-publicas)

## Cómo correr el proyecto

Los notebooks deben ejecutarse **en orden**. Cada uno genera los datos que usa el siguiente.

### Paso 1 — ETL RAW

```bash
jupyter notebook notebooks/01_ETL_RAW.ipynb
```

Lee los 6 CSV y los convierte a Parquet en `data/raw/`. No aplica transformaciones de negocio.
Tiempo estimado: 5-15 min (depende del hardware).

### Paso 2 — STG + EDA

```bash
jupyter notebook notebooks/02_ETL_STG_EDA.ipynb
```

Aplica un pipeline de limpieza de 8 pasos con scikit-learn, realiza el análisis exploratorio
(4 visualizaciones) y calcula métricas de calidad de datos. Guarda el resultado en `data/stg/`.

### Paso 3 — MART BI + ML

```bash
jupyter notebook notebooks/03_MART_ML.ipynb
```

Construye el modelo estrella para BI (`data/mart_bi/`) y entrena dos modelos de regresión
(Regresión Lineal y Random Forest) para predecir la duración de los recorridos.

## Visualizaciones generadas

| Archivo | Descripción |
|---|---|
| `viz1_evolucion_mensual.png` | Recorridos por mes y año (2022-2024) |
| `viz2_distribucion_horaria.png` | Uso por hora: día hábil vs fin de semana |
| `viz3_duracion_por_modelo.png` | Duración de recorrido por modelo de bicicleta |
| `viz4_top_estaciones.png` | Top 15 estaciones de origen más utilizadas |
| `viz5_feature_importance.png` | Importancia de variables — Random Forest |
| `viz6_real_vs_predicho.png` | Duración real vs predicha |

## Modelo de Machine Learning

**Problema:** regresión para predecir la duración de un recorrido en segundos.

**Features utilizadas:** hora, día de semana, mes, trimestre, es_fin_de_semana,
es_hora_pico, año, modelo de bicicleta, género, distancia entre estaciones (km).

**Modelos entrenados:**

| Modelo | MAE | R² |
|---|---|---|
| Regresión Lineal (baseline) | ~X min | ~X |
| Random Forest (50 árboles, max_depth=8) | ~X min | ~X |

> Los valores exactos se imprimen al correr el notebook 03.

## Dependencias

| Librería | Versión mínima |
|---|---|
| pandas | 2.0 |
| pyarrow | 14.0 |
| scikit-learn | 1.4 |
| matplotlib | 3.7 |
| seaborn | 0.13 |
| numpy | 1.26 |
| jupyter | — |
| ipykernel | — |
