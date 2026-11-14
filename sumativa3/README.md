# Taller II — Integración de fuentes heterogéneas con PySpark

Sumativa 3 del curso **Gestión de datos y tecnologías Big Data** (MCDI502). Grupo 1: Yerko Gallardo, Sebastián Rojas y José Ignacio Rodríguez.

Notebook de entrega: [`notebooks/mcdi502_s3_grupo1.ipynb`](notebooks/mcdi502_s3_grupo1.ipynb).

## Estructura

```text
sumativa3/
├── README.md
├── .gitignore
├── notebooks/
│   └── mcdi502_s3_grupo1.ipynb   notebook maestro, sin datos de acceso
├── entrega/                      copia de entrega con los datos de acceso (no versionada)
├── .local/                       datos de acceso para ejecutar en local (no versionada)
├── notebooks_ejemplos/           notebooks base del docente (no versionada)
└── planificacion/                planificación del equipo (no versionada)
```

## Secciones del notebook

| Sección | Contenido |
|---|---|
| 1 | Instalación de Spark, Java y librerías; SparkSession y SparkContext; datos de acceso a las fuentes |
| 2 (ID3.1) | OpenMeteo → `df_openmeteo`; AQICN → `df_aqicn`; MongoDB → `df_mongo_bpm`; MySQL/REST → `df_mysql_dht` |
| 3 (RA4, ID4.2) | `altura_metros`; `condicion_clima` |
| 4 (RA3) | `df_unificado` |
| 5 (ID3.1, ID4.1) | Vistas temporales y consultas C1–C7 |
| 6 | Fuentes y atribuciones |

## Entrega

Un ZIP con el notebook ejecutado de `entrega/` (`mcdi502_s3_grupo1.ipynb`, con los datos de acceso escritos en la celda 1.3) y el video de 7 a 10 minutos con los tres integrantes (`mcdi502_s3_grupo1_video.mp4`). No incluir `.local/`, `notebooks_ejemplos/` ni `planificacion/`.

## Ejecutar en VS Code

1. Instalar uv y las extensiones Python y Jupyter de VS Code. Si no hay un JDK 17 o superior, la celda 1.1 lo instala.
2. Desde la raíz del repositorio, ejecutar `uv sync --locked`.
3. Seleccionar como kernel el `.venv` de la raíz. En Windows el ejecutable es `.venv\Scripts\python.exe`; en Linux y macOS, `.venv/bin/python`.
4. Aprovisionar los datos de acceso (ver abajo).
5. Reiniciar el kernel y ejecutar todas las celdas. No copiar `.venv` entre equipos.

En Google Colab basta con subir el notebook y ejecutar todo: la celda 1.1 instala Java 17, PySpark 4.3.0 y PyMongo. Hay que subir la copia de `entrega/` o completar la celda 1.3.

## Datos de acceso

La URI de MongoDB entregada por el docente y el token gratuito de AQICN no se versionan, porque el repositorio es público. En el notebook del repositorio, las constantes `MONGODB_URI` y `AQICN_TOKEN` de la celda 1.3 están vacías, y la celda toma los valores en este orden: variable de entorno, constante de la celda y archivo en `.local/`.

| Dato | Variable | Archivo |
|---|---|---|
| URI de MongoDB del docente | `MONGODB_URI` | `.local/mongodb.json` → `{"uri": "..."}` |
| Token de AQICN ([solicitar](https://aqicn.org/data-platform/token)) | `AQICN_TOKEN` | `.local/aqicn.json` → `{"token": "..."}` |

La copia de `entrega/` es la misma, con los dos valores escritos en la celda 1.3, para que el docente la ejecute sin configurar nada. Se genera a partir del notebook del repositorio y de `.local/`. `entrega/`, `.local/`, `notebooks_ejemplos/` y `planificacion/` están excluidos de Git.

## Decisiones de las fuentes

- **OpenMeteo:** serie horaria de los últimos 7 días completos de Santiago (`past_days=7`, `forecast_days=0`). Se toman 100 horas repartidas de forma determinista entre las 168. La serie completa se conserva en `df_openmeteo_serie`.
- **AQICN:** el token público `demo` siempre devuelve datos de Shanghái. Con un token propio se buscan las estaciones de la región (`map/bounds`, ampliando la búsqueda hasta Chile) y se consulta el feed de cada una, de la más cercana a Santiago a la más lejana. AQICN publica solo unas 50 estaciones chilenas, así que las 100 filas se completan con una segunda ronda de consultas, identificada en la columna `ronda`. Al 14/11 la red seguía sin publicar lecturas nuevas desde el 09/11, por lo que la segunda ronda repite valores; el notebook informa la antigüedad de las lecturas. Los contaminantes son sub-índices AQI (escala US EPA), no µg/m³.
- **MongoDB:** se usa `telemetria.lecturas` con PyMongo. El conector de Spark para MongoDB 3.x es para Spark 3 (Scala 2.12) y no funciona con Spark 4 (Scala 2.13). Se leen las 100 lecturas más recientes por `fecha` e `_id`. Campos: `altitudFt` → `alturaft`, `presionAtm` → `presion_atmosferica`, `ppmCO` → `monoxido_carbono`, `presionPa` → `presion_pa`.
- **MySQL:** se usa el endpoint PHP `http://mgtt.comunidadingenieria.cl/obtener_datos.php`. `temperatura` se renombra a `temperatura_interior`. Se conservan el ID, la humedad, la fecha original y la fecha tipada sin zona horaria.

## Integración y consultas

- **Estrategia:** las fuentes no comparten período ni granularidad, así que no se unen por fecha. Cada una recibe un índice ordinal `idx = 1…100` con `row_number()` sobre su orden documentado (OpenMeteo por hora ascendente; AQICN por distancia, estación y ronda; MongoDB y MySQL por fecha e ID descendentes). Las cuatro se unen por `idx`, con prefijos `om_`, `aq_`, `mg_` y `my_`, y se controla la cardinalidad en cada unión. Se conservan las fechas y claves originales.
- **Límite:** el emparejamiento solo presenta los datos juntos. La excepción es el par MongoDB–MySQL: ambos registran los mismos eventos del ESP32 con 5 horas de diferencia (MySQL no declara zona horaria), y la sección 4.5 lo comprueba par a par.
- **Vistas temporales:** `openmeteo`, `aqicn`, `mongo_bpm`, `mysql_dht` y, como apoyo para C3, `openmeteo_serie` (168 horas).
- **Consultas:** C2 usa el último día presente en la muestra, porque la muestra cambia en cada ejecución. C3 informa la muestra de 100 horas y la serie completa. C4 y C5 trabajan en la escala de sub-índices AQI e informan las 100 capturas y las observaciones distintas. C5 excluye NO2 = 4.

## Referencias

Notebooks base del docente (Semana 3): `-BaseEjemploOpenMeteo.ipynb`, `-BaseEjemploAQICN.ipynb`, `-BaseEjemploMongoDB.ipynb` y `-BaseEjemploMysqlPHP_APIRest.ipynb`. Documentación: [OpenMeteo](https://open-meteo.com/en/docs), [AQICN JSON API](https://aqicn.org/json-api/doc/) y [PyMongo](https://www.mongodb.com/docs/languages/python/pymongo-driver/current/crud/query/specify-documents-to-return/).
