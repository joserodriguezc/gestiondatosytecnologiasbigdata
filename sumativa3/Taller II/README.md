# Taller II — Bloque de Sebastián

Notebook: `notebooks/sebastian_entorno_sensores.ipynb`.

## Ejecutar en VS Code

1. Instalar uv, JDK 17 y las extensiones Python y Jupyter de VS Code.
2. Desde la raíz del repositorio, ejecutar `uv sync --locked`.
3. Seleccionar el kernel del entorno `.venv` de la raíz. En Windows el ejecutable es `.venv\Scripts\python.exe`; en Linux/macOS, `.venv/bin/python`.
4. Configurar `JAVA_HOME` con la ruta del JDK de cada equipo. El notebook usa la instalación Linux conocida solo si no existe esa variable.
5. Aprovisionar la URI del docente en `MONGODB_URI` o introducirla en la entrada oculta. En este equipo está en `.local/mongodb.json`, excluido de Git. No compartir ese archivo en el ZIP ni en capturas.
6. Reiniciar el kernel y ejecutar todas las celdas. No copiar `.venv` entre equipos.

## Fuentes

Los ejemplos del docente proporcionan MongoDB `telemetria.lecturas` y el endpoint PHP `http://mgtt.comunidadingenieria.cl/obtener_datos.php` para MySQL.

MongoDB: `altitudFt` se renombra a `alturaft`; se calcula `altura_metros`. `presionAtm`, `presionPa` y `ppmCO` se conservan con nombres descriptivos. Confirmar unidades con el docente.

MySQL: `temperatura` se renombra a `temperatura_interior`. Se conservan ID, humedad, fecha original y fecha tipada sin zona horaria; confirmar la zona de origen antes de hacer un join temporal.

Se eligen 100 registros recientes ordenados por fecha descendente e ID descendente. La selección puede cambiar si las fuentes reciben nuevas filas. Los controles se ejecutan sobre los registros extraídos en cada sesión.

## Conexión actualizada

Se reemplazó la conexión anterior por la URI del nuevo ejemplo del docente y la colección `telemetria.lecturas`. La conexión en modo lectura devolvió 100 registros. El esquema conserva `altitudFt`, `presionAtm`, `presionPa`, `ppmCO` y `fecha`.

La ejecución local completa terminó sin errores: ambas fuentes tienen 100 filas, 100 IDs únicos y cero nulos en la muestra; la conversión de altura pasó sus controles. El notebook se ejecuta desde una sesión limpia y exige que ambas fuentes pasen sus controles; ya no omite el error de MongoDB para continuar con MySQL.

## Traspaso

Acordar ciudad, esquema y orden con José y Yerko. Entregar los dos DataFrames con 100 filas y controles ejecutados tras ejecutar los controles. El jueves, revisar integración/SQL y grabar el segmento asignado.

## Referencias

Notebooks base del docente: `-BaseEjemploMongoDB.ipynb` y `-BaseEjemploMysqlPHP_APIRest.ipynb`. Se adaptó la lectura MongoDB a PyMongo y se mantuvo el acceso MySQL mediante PHP.

https://www.mongodb.com/docs/languages/python/pymongo-driver/current/crud/query/specify-documents-to-return/

## Observación para integrar

En la ejecución verificada, MongoDB mostró fechas alrededor de las 22:02 en America/Santiago y MySQL alrededor de las 17:02, con aproximadamente cinco horas de diferencia. La zona de MySQL no está declarada: no aplicar una corrección fija ni hacer un join por fecha hasta confirmarla. Compartir esta observación con Yerko.
