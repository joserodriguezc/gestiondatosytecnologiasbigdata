# Taller III — Limpieza, transformación y validación de ventas en Databricks

Sumativa 4 del curso **Gestión de datos y tecnologías Big Data** (MCDI502). Grupo 1: Yerko Gallardo, Sebastián Rojas y José Ignacio Rodríguez.

El taller se desarrolla y ejecuta íntegramente en un notebook de **Databricks**. Esta carpeta solo respalda ese notebook, exportado con sus salidas: no forma parte del entorno uv del repositorio y no se ejecuta en local.

Notebook: [`notebooks/mcdi502_s4_grupo1.ipynb`](notebooks/mcdi502_s4_grupo1.ipynb).

## Caso

Consolidar las ventas del último trimestre de las sucursales de Viña del Mar, Santiago y Concepción, entregadas en tres CSV con problemas de calidad. Hay que integrarlas, depurarlas y validarlas para dejar un dataset limpio, y luego analizarlo con Spark SQL.

## Estructura

```text
sumativa4/
├── README.md
└── notebooks/
    └── mcdi502_s4_grupo1.ipynb   respaldo del notebook de Databricks
```

Los CSV de Canvas (`ventas_vina_del_mar.csv`, `ventas_santiago.csv` y `ventas_concepcion.csv`) no se versionan.

## Secciones del notebook

| Sección | Contenido |
|---|---|
| 1. Carga e integración (ID3.1) | `df_vina`, `df_santiago` y `df_concepcion`; esquema y primeras 5 filas; unión en `df_ventas_unificado` con verificación del conteo |
| 2. Limpieza y depuración (ID4.2) | Nulos en `categoria`, duplicados completos, `producto_nombre` estandarizado, `fecha` como `Date` y `precio_unitario` numérico |
| 3. Transformación y análisis con DataFrame (ID4.1) | Filtro de cantidades no positivas, `precio_total`, `dia_semana`, sucursales y categorías únicas con `distinct()` |
| 4. Validación y control de calidad (ID4.3) | Regla de `precio_total` no negativo, atípicos de `precio_unitario` por IQR, informe de calidad antes y después |
| 5. Análisis con Spark SQL (ID3.3) | Vista `ventas_limpias` y consultas 1 a 3, con su interpretación |
| 6. Conclusiones | Hallazgos de calidad, ventas por sucursal e impacto de los atípicos |

## Ejecutar en Databricks

1. Importar el notebook en el workspace (*Workspace → Import*).
2. Subir los tres CSV a un volumen de Unity Catalog. El notebook usa `/Volumes/workspace/data/data`; para otra ubicación, cambiar `RUTA_BASE` en la celda 1.1. Databricks Free Edition no permite usar la raíz de DBFS.
3. Conectar el notebook a un cómputo (*serverless* en Free Edition) y ejecutar todas las celdas (*Run all*).

## Resultados principales

- Se integraron 315 registros. Tras eliminar 15 duplicados completos y 26 filas con cantidad no positiva, quedaron 274 transacciones válidas.
- Las categorías faltantes se marcan como `Sin categoría`, para conservar las ventas sin asignarlas a una familia de productos sin respaldo.
- Santiago lidera en importe y en número de transacciones.
- Los 7 precios atípicos detectados por IQR son el 2,55 % de las transacciones y concentran el 60,33 % del importe. Se conservan marcados para revisión, porque de ellos depende el orden entre Viña del Mar y Concepción.

## Entrega

Un ZIP con el notebook exportado desde Databricks, con el nombre `mcdi502_s4_grupo1` (17 caracteres, sin tildes ni símbolos).
