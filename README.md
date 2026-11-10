# Gestión de datos y tecnologías Big Data (MCDI502)

Trabajos del curso, uno por carpeta:

| Carpeta | Contenido |
|---|---|
| [`sumativa1/`](sumativa1/) | Taller 1: procesamiento de datos olímpicos con Apache Spark (RDDs, DataFrames y Spark SQL). |
| [`sumativa2/`](sumativa2/) | Sumativa 2 (en preparación). |

## Entorno

El entorno se comparte entre todas las sumativas y se define en la raíz con [uv](https://docs.astral.sh/uv/) (`pyproject.toml`, `uv.lock` y `.python-version`).

```bash
uv sync --locked
uv run jupyter lab
```
