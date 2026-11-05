# Taller 1 — Apache Spark

Notebook grupal: `mcdi502_s2_grupo1.ipynb`. Ejecutarlo desde la raíz del repositorio, con los seis archivos de `data/` presentes.

## Preparación

Las dependencias están fijadas en `uv.lock` y Python en `.python-version`.

```bash
uv sync --locked
uv run jupyter lab
```

Seleccionar el kernel del entorno `.venv`. La celda 1.1 del notebook prepara Spark y Java sin intervención manual:

- Reutiliza un JDK 17 o superior si ya existe: `JAVA_HOME`, `java` del PATH o las rutas habituales de Windows, macOS y Linux.
- Si no hay ninguno, lo instala: OpenJDK 17 con `apt-get` en Colab o Linux con root, o Temurin 17 con `install-jdk` en `~/.jdk` (sin permisos de administrador).
- Define `JAVA_HOME`, `PYSPARK_PYTHON`, `PYSPARK_DRIVER_PYTHON` y `SPARK_LOCAL_IP`.

Para elegir un JDK concreto, basta con definir `JAVA_HOME` antes de abrir Jupyter; por ejemplo, en Linux con varias versiones:

```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk
uv run jupyter lab
```

El notebook muestra las versiones efectivas. Ejecutar todas las celdas desde un kernel reiniciado.

**Windows:** ejecutar el notebook completo en Linux, macOS o WSL. En Windows nativo, PySpark 4.3 falla de forma intermitente en `take` sobre RDDs grandes (*Python worker exited unexpectedly*, sección 2.2). Además, si la ruta de la carpeta contiene tildes, Spark no arranca (*ClassNotFoundException: SparkSubmit*).

## Reparto

Las secciones del notebook siguen los puntos de la consigna.

- Yerko Gallardo: entorno, RDDs, unión, DataFrame `deportista` y transformaciones (secciones 1–3).
- Sebastián Rojas: cargas, controles, integración, cinco particiones e inspección (secciones 4–6).
- José Ignacio Rodríguez: columnas `IMC` y `Descripción_sexo` en `df_analisis`, las cuatro agregaciones con Spark SQL (`medallas_equipo`, `edad_medalla`, `temporada`, `sexo`), conclusiones, fuentes y cierre de recursos (secciones 7, 8 y 10).

La tabla integrada conserva cada participación de `resultados.csv`, incluso cuando falta su equipo. En Juegos se corrigen etiquetas de columnas con verificación de los valores y se preservan los campos originales. Ver el notebook para los controles y el contrato de columnas.

Guion del video, con el bloque de cada integrante: [docs/guion_video.md](docs/guion_video.md).
