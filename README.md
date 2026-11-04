# Taller 1 — Apache Spark

Notebook grupal: `mcdi502_s2_grupo1.ipynb`. Ejecutarlo desde la raíz del repositorio, con los seis archivos de `data/` presentes.

## Preparación

Las dependencias están fijadas en `uv.lock` y Python en `.python-version`.

```bash
uv sync --locked --no-install-project
uv run --no-sync jupyter lab
```

`--no-install-project` es necesario porque el repositorio contiene un notebook, pero su configuración de empaquetado apunta a un módulo `src/gestiondatosytecnologiasbigdata` que no existe. No modifica el lockfile.

Seleccionar el kernel del entorno `.venv`. Java debe estar instalado y `JAVA_HOME` debe apuntar al JDK elegido. En el equipo Linux de Sebastián hay Java 17 y 27; para esta ejecución se selecciona explícitamente Java 17:

```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk
export PATH="$JAVA_HOME/bin:$PATH"
export SPARK_LOCAL_IP=127.0.0.1
uv run --no-sync jupyter lab
```

La ruta de Java es un ejemplo de este equipo; ajustar a la instalación local. El notebook muestra las versiones efectivas. Ejecutar todas las celdas desde un kernel reiniciado.

## Reparto y continuidad

- Yerko: entorno, RDDs, transformaciones y DataFrame `deportista` (secciones 1–5).
- Sebastián: cargas, controles, integración, cinco particiones e inspección (secciones 6–8).
- José: continuar desde `df_integrado` con IMC, `Descripción_sexo` y las cuatro agregaciones SQL; todavía pendientes en este notebook.

La tabla integrada conserva cada participación de `resultados.csv`, incluso cuando falta su equipo. En Juegos se corrigen etiquetas de columnas con verificación de los valores y se preservan los campos originales. Ver el notebook para los controles y el contrato de columnas.

Al finalizar las consultas de José, liberar la tabla con `df_integrado.unpersist()` y cerrar Spark con `spark.stop()`. El video grupal y el paquete de entrega siguen pendientes.

Guía para explicar el bloque de Sebastián: [docs/guia_sebastian.md](docs/guia_sebastian.md).
