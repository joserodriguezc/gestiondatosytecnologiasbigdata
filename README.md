# Gestión de datos y tecnologías Big Data (MCDI502)

Evaluaciones sumativas del curso:

| Sumativa | Contenido |
|---|---|
| `sumativa1` | Prueba de conocimientos de Big Data en Canvas (no se registra en el repositorio). |
| [`sumativa2/`](sumativa2/) | Taller I: procesamiento de datos olímpicos con Apache Spark (RDDs, DataFrames y Spark SQL). |
| [`sumativa3/`](sumativa3/) | Taller II: integración de fuentes heterogéneas con PySpark (APIs OpenMeteo y AQICN, sensores ESP32 en MongoDB y MySQL) y consultas con Spark SQL. |
| [`sumativa4/`](sumativa4/) | Taller III: limpieza, transformación y validación de ventas de retail con PySpark y Spark SQL en Databricks (respaldo del notebook). |

## Entorno

Las sumativas 2 y 3 comparten un entorno local definido en la raíz con [uv](https://docs.astral.sh/uv/) (`pyproject.toml`, `uv.lock` y `.python-version`). La sumativa 4 se ejecuta en Databricks; en el repositorio solo se respalda su notebook.

```bash
uv sync --locked
uv run jupyter lab
```
