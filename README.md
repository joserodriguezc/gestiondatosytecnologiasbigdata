# Gestión de datos y tecnologías Big Data (MCDI502)

Evaluaciones sumativas del curso:

| Sumativa | Contenido |
|---|---|
| `sumativa1` | Prueba de conocimientos de Big Data en Canvas (no se registra en el repositorio). |
| [`sumativa2/`](sumativa2/) | Taller 1: procesamiento de datos olímpicos con Apache Spark (RDDs, DataFrames y Spark SQL). |
| [`sumativa3/`](sumativa3/) | Taller II: ingesta de sensores con PySpark. |

## Entorno

El entorno se comparte entre todas las sumativas y se define en la raíz con [uv](https://docs.astral.sh/uv/) (`pyproject.toml`, `uv.lock` y `.python-version`).

```bash
uv sync --locked
uv run jupyter lab
```
