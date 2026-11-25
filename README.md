# Géneros musicales en el Top 50 de Spotify Colombia 2024

Proyecto de ciencia de datos con Python y pandas: diagnóstico de calidad, limpieza, análisis y estructuración de los datos del Top 50 diario de Spotify en Colombia.

**Programa:** Tecnólogo en Análisis y Desarrollo de Software (228118) – SENA
**Aprendiz:** Jeferson Estiven Vargas
**Instructor:** Carlos Enrique Navia Torres

| Evidencia | Contenido | Notebook |
| --- | --- | --- |
| GA1-220501094-01-AA1-EV06 – Diagnosticar la calidad de datos | Inspección y diagnóstico de calidad | `notebooks/data_quality.ipynb` |
| GA1-220501094-01-AA1-EV07 – Preparación, limpieza y estructuración de datos con Pandas | Limpieza, indicadores, combinación y exportación | `notebooks/data_preparation.ipynb` |

---

## Descripción

El proyecto identifica qué géneros musicales dominan el Top 50 diario de Spotify en Colombia durante 2024.

## Propósito

Que artistas, emisoras y organizadores de eventos puedan tomar decisiones basadas en datos sobre los estilos musicales que más se escuchan en Colombia.

## Pregunta analítica

> ¿Qué géneros musicales aparecen con más frecuencia en el Top 50 diario de Spotify Colombia durante 2024?

## Respuesta (EV07)

El **urbano latino** (reguetón, trap y estilos afines) domina el Top 50 de Colombia en 2024:

| Indicador | Resultado |
| --- | --- |
| Participación en las apariciones del Top 50 | **79,82 %** (13.808 de 17.300) |
| Participación en el Top 10 | **86,18 %** |
| Días en el número 1 | **338 de 346 (97,69 %)**; *LUNA* de Feid fue número 1 durante 149 días |
| Siguientes géneros | Regional mexicano (6,05 %), pop (4,55 %) y música popular colombiana (2,86 %) |
| Validación con Spotify Charts (semana del 5 al 11 de enero) | Urbano latino: 80 % en Kaggle y 80 % en el Top 50 oficial |

Los demás géneros cambian por temporadas: el regional mexicano baja del 14,8 % en enero al 1,9 % en diciembre, el pop llega al 9,5 % en noviembre y el tropical aparece casi solo en diciembre y enero (música decembrina).

**Limitaciones:** se analizaron 346 de los 366 días (17 sin datos y 3 excluidos por listas mezcladas); cada canción recibe un solo género, el de su artista principal; y el 2,26 % de las apariciones quedó sin clasificar.

## Datasets

### Originales (`data/raw/`, no se modifican)

| Dataset | Archivo | Fuente | Contenido | ¿Está en el repositorio? |
| --- | --- | --- | --- | --- |
| 1 – Kaggle | `universal_top_spotify_songs.csv` | [Top Spotify Songs in 73 Countries](https://www.kaggle.com/datasets/asaniczka/top-spotify-songs-in-73-countries-daily-updated) | Top 50 diario de 72 países y la lista Global (2.110.316 filas, 25 columnas) | **No** (unos 498 MB) |
| 2 – Spotify Charts | `regional-co-weekly-2024-01-11.csv` | [Spotify Charts – Colombia semanal](https://charts.spotify.com/charts/view/regional-co-weekly/2024-01-11) | Top 200 de Colombia, semana que termina el 11 de enero de 2024 (200 filas, 9 columnas) | Sí |
| 3 – Géneros por artista | `artist_genres.csv` | Tabla construida para el proyecto a partir de `reports/main_artists.csv` | Género y nivel de confianza de los 186 artistas principales | Sí |

> **Sobre el Dataset 3:**La tabla se elaboró con apoyo de un asistente de IA; su revisión detallada está pendiente (40 artistas sin clasificar y 31 con confianza media o baja).

### Procesados (`data/processed/`, generados por `data_preparation.ipynb`)

| Archivo | Filas × columnas | Contenido |
| --- | --- | --- |
| `top50_co_2024_clean.csv` | 17.300 × 30 | **Dataset consolidado**: una fila por aparición diaria en el Top 50 de Colombia 2024, con su género |
| `charts_co_weekly_2024-01-11_clean.csv` | 200 × 12 | Spotify Charts limpio, con `spotify_id` y artista principal |
| `genre_summary.csv` | 10 × 8 | Apariciones, canciones, artistas y participación por género |
| `genre_monthly_share.csv` | 120 × 3 | Participación mensual de cada género (formato largo) |
| `artist_summary.csv` | 186 × 10 | Apariciones de cada artista principal con su género |

### Cómo obtener el Dataset 1

El archivo de Kaggle no se sube a GitHub por su tamaño (está en `.gitignore`).

1. Entra al enlace de Kaggle de la tabla e inicia sesión.
2. Descarga el dataset con el botón **Download** y descomprime el `.zip`.
3. Copia `universal_top_spotify_songs.csv` en la carpeta `data/raw/`.

> El dataset de Kaggle se actualiza con frecuencia. Este trabajo se hizo con una versión que va del **2023-10-18 al 2025-06-11**. Si descargas una versión más nueva, algunos números pueden cambiar y algunas validaciones con `assert` podrían avisarlo.

## Transformaciones realizadas (EV07)

### Lección 2 – Limpieza y estandarización

Pipeline de funciones encadenadas con `pipe()`, con un informe de cambios paso a paso:

| Paso | Transformación | Resultado |
| --- | --- | --- |
| 1 | Convertir fechas con `pd.to_datetime()` y filtrar Colombia 2024 | 2.110.316 → 17.461 filas; los 30.448 nulos (lista Global y otros países) quedan fuera |
| 2 | Excluir las 3 fechas con posiciones repetidas (14, 16 y 18 de marzo) | 17.461 → 17.300 filas (346 días × 50) |
| 3 | Quitar duplicados exactos | Sin cambios (regla de protección) |
| 4 | Optimizar tipos (`int8`, `int16`, `Int8`, `Int64`) | Memoria de 8,4 a 7,7 MB |
| 5 | Pasar a nulo el audio de canciones con duración 0 | Sin cambios en Colombia 2024 (regla de protección) |
| 6 | Estandarizar textos (`\xa0`, espacios dobles y en los extremos) | Sin cambios en Colombia 2024 (regla de protección) |
| 7 | Crear `main_artist` y `artist_count` | 25 → 27 columnas |
| Charts | `previous_rank = -1` a nulo; `spotify_id` extraído de `uri`; artista principal | 44 faltantes disfrazados corregidos |

Validación final con 15 reglas `assert` y comparación de la calidad antes y después.

### Lección 3 – Filtrado, ordenamiento y agrupación

- Categorización de cada aparición por género (`map()`) y por franja de posiciones (`pd.cut()`: Top 10, 11-25, 26-50).
- Filtros con máscaras booleanas, `between()`, `isin()` y `query()`.
- Cinco indicadores con `groupby()`, `agg()` y `pivot_table()`: participación por género, canciones y artistas por género, participación por franja, días en el número 1 y participación mensual.

### Lección 4 – Combinación y estructuración

- `merge()` con `validate` e `indicator=True` para comparar Kaggle con Spotify Charts y para unir los géneros.
- No se usa `concat()`: no hay tablas con las mismas columnas que apilar, y apilar datos diarios con semanales sería una combinación artificial.
- Dataset consolidado validado (clave única fecha + posición, sin nulos en las claves, géneros válidos y mismo resultado que la categorización de la Lección 3).
- Exportación a CSV con verificación de ida y vuelta. Se usa CSV y no Parquet para no agregar `pyarrow` como dependencia.

## Estructura del repositorio

```
proyecto-spotify-colombia/
├── README.md
├── .gitignore
├── requirements.txt
├── data/
│   ├── raw/
│   │   ├── universal_top_spotify_songs.csv      (no se sube: ver "Cómo obtener el Dataset 1")
│   │   ├── regional-co-weekly-2024-01-11.csv
│   │   └── artist_genres.csv
│   └── processed/
│       ├── top50_co_2024_clean.csv
│       ├── charts_co_weekly_2024-01-11_clean.csv
│       ├── genre_summary.csv
│       ├── genre_monthly_share.csv
│       └── artist_summary.csv
├── notebooks/
│   ├── data_quality.ipynb        (EV06)
│   └── data_preparation.ipynb    (EV07)
└── reports/
    ├── data_quality.md
    ├── data_quality_findings.csv
    └── main_artists.csv
```

## Requisitos

- Python 3.12 o superior (pandas 3 necesita Python 3.11 o superior).
- Git.
- Unos 2 GB de memoria RAM libres para cargar el Dataset 1.

## Instrucciones de ejecución

Los comandos son para **Git Bash** en Windows.

```bash
# 1. Clonar el repositorio
git clone https://github.com/jeferson-estiven-vargas/proyecto-spotify-colombia.git
cd proyecto-spotify-colombia

# 2. Crear y activar el entorno virtual
python -m venv venv
source venv/Scripts/activate      # macOS y Linux: source venv/bin/activate

# 3. Instalar las dependencias
python -m pip install -r requirements.txt

# 4. Copiar el Dataset 1 en data/raw/ (ver "Cómo obtener el Dataset 1")

# 5. Abrir Jupyter desde la raíz del proyecto
jupyter lab
```

Ejecuta los notebooks en orden, con **Kernel → Restart Kernel and Run All Cells**:

1. `notebooks/data_quality.ipynb`: diagnóstico de calidad (EV06).
2. `notebooks/data_preparation.ipynb`: limpieza, indicadores, combinación y exportación (EV07). Genera `reports/main_artists.csv` y los archivos de `data/processed/`.

Si el navegador muestra `ERR_FILE_NOT_FOUND`, copia en el navegador el enlace `http://localhost:8888/lab?token=...` que aparece en la terminal. También puedes abrir los notebooks en VS Code y seleccionar el kernel `venv`.

> **Nota de compatibilidad:** `requirements.txt` se generó con `pip freeze` en Windows. Si lo instalas en macOS o Linux y falla algún paquete exclusivo de Windows (por ejemplo `pywinpty`), quita esa línea e instala de nuevo.

## Documentación del diagnóstico (EV06)

El detalle del diagnóstico de calidad está en el [informe técnico](reports/data_quality.md) y en la [tabla de hallazgos](reports/data_quality_findings.csv).

## Herramientas

Python 3.12.10 · pandas 3.1.0 · JupyterLab · VS Code · Git y GitHub
