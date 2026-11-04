# Informe técnico: diagnóstico de calidad de datos

| | |
| --- | --- |
| **Evidencia** | GA1-220501094-01-AA1-EV06 – Diagnosticar la calidad de datos: preparación, exploración y documentación |
| **Programa** | Tecnólogo en Análisis y Desarrollo de Software (228118) – SENA |
| **Aprendiz** | Jeferson Estiven Vargas |
| **Instructor** | Carlos Enrique Navia Torres |
| **Fecha** | 3 de noviembre de 2026 |
| **Notebook** | [`notebooks/diagnostico_calidad.ipynb`](../notebooks/diagnostico_calidad.ipynb) |
| **Tabla de hallazgos** | [`reports/tabla_hallazgos.csv`](tabla_hallazgos.csv) |

---

## 1. Nombre del proyecto y pregunta analítica

**Proyecto:** Géneros musicales en el Top 50 de Spotify Colombia 2024.

**Pregunta analítica:** ¿Qué géneros musicales aparecen con más frecuencia en el Top 50 diario de Spotify Colombia durante 2024?

**Propósito:** identificar qué estilos musicales dominan lo que más se escucha en Colombia, para que artistas, emisoras y organizadores de eventos tomen decisiones basadas en datos.

**Pregunta orientadora de este diagnóstico:** ¿Qué condiciones de calidad presentan los conjuntos de datos seleccionados y qué acciones de preparación o limpieza deben considerarse antes de iniciar el análisis?

---

## 2. Descripción de los datasets utilizados

| | Dataset 1 – Kaggle | Dataset 2 – Spotify Charts |
| --- | --- | --- |
| **Archivo** | `universal_top_spotify_songs.csv` | `regional-co-weekly-2024-01-11.csv` |
| **Fuente** | Kaggle – *Top Spotify Songs in 73 Countries* (recopilación de terceros) | Spotify Charts (fuente oficial) |
| **Contenido** | Top 50 diario de 72 países más la lista Global, con posición, artistas, fecha, popularidad y características de audio | Top 200 semanal de Colombia, semana del 11 de enero de 2024 |
| **Periodo** | 18 de octubre de 2023 a 11 de junio de 2025 | Una sola semana |
| **Papel en el proyecto** | Fuente principal: de aquí salen las canciones de Colombia en 2024 | Validación: comprobar que el Dataset 1 coincide con la lista oficial |
| **En el repositorio** | No (unos 498 MB). El README explica cómo descargarlo | Sí, en `data/raw/` |

---

## 3. Resultados de la inspección estructural

Sobre cada dataset se ejecutaron los comandos de Pandas que pide la guía: `head()`, `tail()`, `shape`, `columns`, `dtypes`, `info()`, `describe()` y `describe(include='object')`. En pandas 3 el texto es de tipo `str`, por eso se usó `include=["object", "str"]`. En el Dataset 1 se usó `info(show_counts=True)`, porque con más de 1,69 millones de filas pandas oculta el conteo de no nulos.

| Dataset | Filas | Columnas | Tipos de datos | Celdas nulas | Observaciones |
| --- | --- | --- | --- | --- | --- |
| Dataset 1 – Kaggle | 2.110.316 | 25 | 9 `float64`, 8 `int64`, 7 `str`, 1 `bool` | 30.448 | Fechas como texto; 583 fechas distintas; 24.983 canciones únicas; sin columna de género |
| Dataset 2 – Charts | 200 | 9 | 5 `int64`, 4 `str` | 0 | `previous_rank` usa -1; 86 disqueras distintas; sin columna de fecha ni de género |

**Características principales:**

- En el Dataset 1, `head()` muestra la fecha 2025-06-11 y `tail()` la fecha 2023-10-18: el archivo está ordenado de la fecha más reciente a la más antigua y **no es solo de 2024**.
- En el Dataset 1 hay 24.983 identificadores (`spotify_id`) pero solo 21.809 nombres de canción distintos. Hay canciones diferentes con el mismo nombre (versiones, remixes), así que el nombre no sirve como identificador.
- En el Dataset 2, `streams` va de 202.193 a 2.498.259 reproducciones, con mediana de 273.582. La canción número 1 tiene unas 9 veces las reproducciones de la mediana.
- En el Dataset 2, `artist_names` tiene solo 155 valores distintos en 200 canciones, y Feid aparece 13 veces como único artista.

---

## 4. Valores faltantes, tipos de datos y posibles inconsistencias

### 4.1 Valores faltantes

| Dataset | Columna | Nulos | % | Interpretación |
| --- | --- | --- | --- | --- |
| Dataset 1 | `country` | 28.908 | 1,37 | Corresponden a la **lista Global**, que no pertenece a ningún país. No son errores, pero hay que filtrarlos |
| Dataset 1 | `album_name` | 822 | 0,04 | Impacto bajo para la pregunta |
| Dataset 1 | `album_release_date` | 659 | 0,03 | Impacto bajo para la pregunta |
| Dataset 1 | `name` / `artists` | 30 / 29 | 0,00 | Pocas filas, pero sin artista no se puede asignar género |
| Dataset 2 | `previous_rank` | 0 visibles | — | **Faltante disfrazado:** 44 canciones tienen -1 (no estaban la semana anterior). El promedio da 71,14 con el -1 y 91,49 sin él |

Durante el diagnóstico se probó la hipótesis de que pandas convertía en nulo el código de país `"NA"` (Namibia). Al leer el archivo con `keep_default_na=False` los nulos siguieron siendo 28.908 y no apareció ninguna fila `"NA"`, así que **la hipótesis se descartó**.

### 4.2 Tipos de datos

- **Dataset 1:** `snapshot_date` y `album_release_date` son `str`, pero representan fechas. La prueba con `pd.to_datetime(errors="coerce")` dio **0 valores no convertibles**, así que la conversión es segura. `key`, `mode` y `time_signature` son enteros, pero en realidad son códigos de categoría y no tiene sentido promediarlos.
- **Dataset 2:** `previous_rank` debería aceptar nulos (`Int64`) en lugar de usar -1. `uri` tiene el formato `spotify:track:ID`, mientras que en el Dataset 1 el identificador es solo `ID`. Además, el Dataset 2 no tiene columna de fecha: la semana solo aparece en el nombre del archivo.

### 4.3 Posibles inconsistencias

- **Canciones distintas en la misma posición:** hay 3.686 casos con la misma fecha, país y posición en el Dataset 1. Al incluir `spotify_id` los duplicados bajan a 0, así que son **canciones distintas** en el mismo puesto. Ocurre en 6 fechas y en 358 listas.
- **Impacto en Colombia 2024:** hay 33 posiciones repetidas en tres días: 14 de marzo (54 canciones), 16 de marzo (55) y 18 de marzo (52). Sobran solo 11 canciones pero hay 33 posiciones repetidas, lo que indica que las listas están **mezcladas**. Estos días están pegados a dos días que faltan (15 y 17 de marzo).
- **Duración igual a 0:** 29 filas del Dataset 1 tienen `duration_ms` en 0, y `tempo` y `time_signature` también tienen mínimo 0. Probablemente son canciones sin características de audio calculadas.
- **Varios artistas en una celda:** 858.692 de 2.110.316 filas del Dataset 1 (40,7 %) y 100 de 200 del Dataset 2 (50 %).
- **Nombres escritos de formas distintas:** la disquera Bichota aparece de 2 formas en el Dataset 2.
- **Rangos válidos:** `daily_rank` (1–50), `popularity` (0–100), `rank` (1–200) y `streams` no tienen valores fuera de rango, y no hay filas completamente duplicadas.

---

## 5. Principales hallazgos de calidad por dataset

**Dataset 1 – Kaggle**

1. No existe columna de género, que es la variable central de la pregunta.
2. Cobertura de Colombia 2024: hay datos de 349 de 366 días. Faltan 17 días: el 15 y el 17 de marzo, del 20 al 24 de abril, y del 28 de noviembre al 8 de diciembre (con excepción del 6 de diciembre).
3. Hay 3 días de Colombia con listas mezcladas (14, 16 y 18 de marzo).
4. El 40,7 % de las filas tiene varios artistas en una sola celda.
5. Las fechas están guardadas como texto.

**Dataset 2 – Spotify Charts**

1. El -1 en `previous_rank` es un dato faltante que `isna()` no detecta.
2. Es un Top 200 de una sola semana: solo sirve para validar.
3. Pocos artistas concentran la lista (155 valores distintos de `artist_names` en 200 canciones).
4. El identificador `uri` tiene un formato distinto al del Dataset 1.

La tabla completa, con 12 hallazgos, evidencia, impacto y acción propuesta, está en [`tabla_hallazgos.csv`](tabla_hallazgos.csv) y en la sección 4.5 del notebook.

---

## 6. Acciones de limpieza y preparación propuestas

| # | Acción | Dataset | Problema que resuelve |
| --- | --- | --- | --- |
| 1 | Filtrar `country == "CO"` y el año 2024 | 1 | Lista Global y fechas fuera de 2024 |
| 2 | Convertir `snapshot_date` y `album_release_date` con `pd.to_datetime()` | 1 | Fechas como texto |
| 3 | Excluir los días 2024-03-14, 2024-03-16 y 2024-03-18 | 1 | Listas mezcladas |
| 4 | Reemplazar por `NaN` las características de audio de las filas con duración 0 | 1 | Valores imposibles |
| 5 | Separar los artistas con `str.split(", ")`, revisando nombres que llevan coma | 1 y 2 | Varios artistas en una celda |
| 6 | Construir una tabla de géneros por artista y unirla con `merge` | 1 | Falta la columna de género |
| 7 | Reemplazar -1 por `NaN` en `previous_rank` | 2 | Faltante disfrazado |
| 8 | Unificar nombres de disqueras (minúsculas y nombre único) | 2 | Conteos divididos |
| 9 | Extraer el ID de `uri` con `str.split(":").str[-1]` y comparar con `spotify_id` | 2 | Validar el Dataset 1 contra la fuente oficial |
| 10 | Contar géneros por proporción y decidir si se cuentan apariciones diarias o canciones únicas | 1 | Días faltantes y concentración de artistas |

En esta evidencia **no se ejecutó la limpieza**: la guía pide diagnosticar y documentar. Estas acciones se aplicarán en la siguiente etapa del proyecto.

---

## 7. Conclusiones

1. **Los datos se pueden usar, pero no tal como vienen.** La estructura es ordenada, casi no hay nulos reales y la mayoría de los tipos son correctos.
2. **El problema más grave es la falta de la columna de género.** Sin ella no se puede responder la pregunta, así que conseguir los géneros por artista es el primer paso del análisis.
3. **Los problemas más peligrosos no se ven con `isna()`:** el -1 en `previous_rank`, las canciones distintas en la misma posición y las filas con duración 0 solo aparecieron al revisar rangos, duplicados y contenido.
4. **La cobertura es suficiente para responder la pregunta:** después de excluir los días con listas mezcladas quedan 346 de 366 días confiables (94,5 %). Como faltan días, conviene comparar los géneros por proporción y no por totales.
5. **Probar hipótesis también es parte del diagnóstico:** la sospecha de que el código de Namibia se leía como nulo se comprobó con datos y se descartó, y así se confirmó que los nulos de `country` son la lista Global.

---

## Reproducibilidad

El diagnóstico se ejecutó con Python 3.12.10 y pandas 3.1.0 en un entorno virtual. Las versiones exactas están en `requirements.txt`, y las instrucciones para reproducirlo están en el `README.md`.