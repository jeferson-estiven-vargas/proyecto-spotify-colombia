# Géneros musicales en el Top 50 de Spotify Colombia 2024

Diagnóstico inicial de calidad de los datos para un proyecto de ciencia de datos con Python y pandas.

**Evidencia:** GA1-220501094-01-AA1-EV06 – Diagnosticar la calidad de datos: preparación, exploración y documentación
**Programa:** Tecnólogo en Análisis y Desarrollo de Software (228118) – SENA
**Aprendiz:** Jeferson Estiven Vargas
**Instructor:** Carlos Enrique Navia Torres

---

## Descripción

El proyecto busca identificar qué géneros musicales dominan el Top 50 diario de Spotify en Colombia durante 2024.

## Propósito

Que artistas, emisoras y organizadores de eventos puedan tomar decisiones basadas en datos sobre los estilos musicales que más se escuchan en Colombia.

## Pregunta analítica

> ¿Qué géneros musicales aparecen con más frecuencia en el Top 50 diario de Spotify Colombia durante 2024?

## Datasets

| Dataset | Archivo | Fuente | Contenido | ¿Está en el repositorio? |
| --- | --- | --- | --- | --- |
| 1 – Kaggle | `universal_top_spotify_songs.csv` | [Top Spotify Songs in 73 Countries](https://www.kaggle.com/datasets/asaniczka/top-spotify-songs-in-73-countries-daily-updated) | Top 50 diario de 72 países y la lista Global (2.110.316 filas, 25 columnas) | **No** (unos 498 MB) |
| 2 – Spotify Charts | `regional-co-weekly-2024-01-11.csv` | [Spotify Charts – Colombia semanal](https://charts.spotify.com/charts/view/regional-co-weekly/2024-01-11) | Top 200 de Colombia, semana del 11 de enero de 2024 (200 filas, 9 columnas) | Sí |

### Cómo obtener el Dataset 1

El archivo de Kaggle no se sube a GitHub por su tamaño (está en `.gitignore`).

1. Entra al enlace de Kaggle de la tabla e inicia sesión.
2. Descarga el dataset con el botón **Download** y descomprime el `.zip`.
3. Copia `universal_top_spotify_songs.csv` en la carpeta `data/raw/`.

> El dataset de Kaggle se actualiza con frecuencia. Este diagnóstico se hizo con una versión que va del **2023-10-18 al 2025-06-11**. Si descargas una versión más nueva, algunos números pueden cambiar.

## Estructura del repositorio

```
proyecto-spotify-colombia/
├── README.md                     
├── .gitignore                    
├── requirements.txt              
├── data/
│   └── raw/                      
│       └── regional-co-weekly-2024-01-11.csv
├── notebooks/
│   └── diagnostico_calidad.ipynb 
└── reports/
    ├── informe_diagnostico.md    
    └── tabla_hallazgos.csv       
```

## Requisitos

- Python 3.12 o superior (pandas 3 necesita Python 3.11 o superior).
- Git.
- Unos 2 GB de memoria RAM libres para cargar el Dataset 1.

## Instrucciones para reproducir el diagnóstico

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

En Jupyter abre `notebooks/diagnostico_calidad.ipynb` y ejecuta **Kernel → Restart Kernel and Run All Cells**. Si el navegador muestra `ERR_FILE_NOT_FOUND`, copia en el navegador el enlace `http://localhost:8888/lab?token=...` que aparece en la terminal. También puedes abrir el notebook en VS Code y seleccionar el kernel `venv`.

> **Nota de compatibilidad:** `requirements.txt` se generó con `pip freeze` en Windows. Si lo instalas en macOS o Linux y falla algún paquete exclusivo de Windows (por ejemplo `pywinpty`), quita esa línea e instala de nuevo.

## Resultados principales

- Los datos **se pueden usar, pero no tal como vienen**.
- **Ningún dataset tiene columna de género**: hay que unir una tabla de géneros por artista.
- Colombia 2024 tiene datos de **349 de 366 días**; quitando 3 días con listas mezcladas quedan **346 días confiables (94,5 %)**.
- Se encontraron problemas que `isna()` no detecta: el `-1` en `previous_rank`, canciones distintas en la misma posición, filas con duración 0 y varios artistas en una sola celda.

El detalle completo está en el [informe técnico](reports/informe_diagnostico.md) y en la [tabla de hallazgos](reports/tabla_hallazgos.csv).

## Herramientas

Python 3.12.10 · pandas 3.1.0 · JupyterLab 4.6.5 · Git y GitHub
