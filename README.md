# Taller 1 — Apache Spark

Notebook grupal: `mcdi502_s2_grupo1.ipynb`. Ejecutarlo desde la raíz del repositorio, con los seis archivos de `data/` presentes.

## Preparación

Las dependencias están fijadas en `uv.lock` y Python en `.python-version`.

```bash
uv sync --locked --no-install-project
uv run --no-sync jupyter lab
```

`--no-install-project` es necesario porque el repositorio contiene un notebook, pero su configuración de empaquetado apunta a un módulo `src/gestiondatosytecnologiasbigdata` que no existe. No modifica el lockfile.

Seleccionar el kernel del entorno `.venv`. La celda 1.1 del notebook prepara Spark y Java sin intervención manual:

- Reutiliza un JDK 17 o superior si ya existe: `JAVA_HOME`, `java` del PATH o las rutas habituales de Windows, macOS y Linux.
- Si no hay ninguno, lo instala: OpenJDK 17 con `apt-get` en Colab o Linux con root, o Temurin 17 con `install-jdk` en `~/.jdk` (sin permisos de administrador).
- Define `JAVA_HOME`, `PYSPARK_PYTHON`, `PYSPARK_DRIVER_PYTHON` y `SPARK_LOCAL_IP`.

Para elegir un JDK concreto, basta con definir `JAVA_HOME` antes de abrir Jupyter; por ejemplo, en Linux con varias versiones:

```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk
uv run --no-sync jupyter lab
```

El notebook muestra las versiones efectivas. Ejecutar todas las celdas desde un kernel reiniciado.

**Windows:** ejecutar el notebook completo en Linux, macOS o WSL. En Windows nativo, PySpark 4.3 falla de forma intermitente en `take` sobre RDDs grandes (*Python worker exited unexpectedly*, sección 2.2). Además, si la ruta de la carpeta contiene tildes, Spark no arranca (*ClassNotFoundException: SparkSubmit*).

## Reparto y continuidad

Las secciones del notebook siguen los puntos de la consigna.

- Yerko Gallardo: entorno, RDDs, unión, DataFrame `deportista` y transformaciones (secciones 1–3).
- Sebastián Rojas: cargas, controles, integración, cinco particiones e inspección (secciones 4–6).
- José Ignacio Rodríguez: columnas `IMC` y `Descripción_sexo` en `df_analisis`, las cuatro agregaciones con Spark SQL (`medallas_equipo`, `edad_medalla`, `temporada`, `sexo`), conclusiones, fuentes y cierre de recursos (secciones 7, 8 y 10).

La tabla integrada conserva cada participación de `resultados.csv`, incluso cuando falta su equipo. En Juegos se corrigen etiquetas de columnas con verificación de los valores y se preservan los campos originales. Ver el notebook para los controles y el contrato de columnas.

La sección 10 libera `df_integrado` con `unpersist()` y cierra Spark con `spark.stop()`.

Guías para explicar cada bloque en el video: [docs/guia_sebastian.md](docs/guia_sebastian.md) y [docs/guia_jose.md](docs/guia_jose.md).

## Entrega

- [ ] Yerko revisó el bloque de José (IMC, consultas, conteos y unidad de análisis).
- [ ] Notebook ejecutado completo desde un kernel reiniciado, con las salidas guardadas: `mcdi502_s2_grupo1.ipynb`.
- [ ] Video de 7 a 10 minutos en que participan los tres integrantes: `mcdi502_s2_grupo1_video.mp4`.
- [ ] Ambos archivos en un único `.zip`, subido al buzón por un integrante antes del domingo a las 23:59.

El nombre de video que pide la consigna (`mcdi502_s2_grupo1_video`) tiene 23 caracteres, más que el límite de 20. Se usa el formato prescrito; conviene confirmarlo en el foro «Consultas al docente».
