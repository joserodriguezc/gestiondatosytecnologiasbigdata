# Planificación del bloque de José en PySpark

**Proyecto:** Taller I de Gestión de datos y tecnologías Big Data.  
**Responsable:** José Ignacio Rodríguez.  
**Alcance:** columnas calculadas y cuatro agregaciones mediante Spark SQL.  
**Cobertura:** 33 puntos de la rúbrica: columnas calculadas, 12; agregaciones, 21.  
**Punto de partida:** DataFrame `df_integrado`, generado por el bloque de Sebastián en la misma sesión de PySpark.  
**Base de esta planificación:** revisión del repositorio en el commit `d0a53476e6a536767f107e50cd7950d701193591`. Los conteos aquí indicados provienen de sus salidas guardadas; deben volver a comprobarse al ejecutar.

Repositorio: https://github.com/joserodriguezc/gestiondatosytecnologiasbigdata

## 1. Resultado que debes entregar

Extender `mcdi502_s2_grupo1.ipynb` después del cierre del bloque de Sebastián, conservando el trabajo anterior. Tu sección debe ejecutarse consecutivamente y producir:

1. `df_analisis`, con todas las columnas originales más `IMC` y `Descripción_sexo`.
2. `medallas_equipo`, con cantidades de oro, plata y bronce por equipo.
3. `edad_medalla`, con suma, promedio, máximo y mínimo de edad por tipo de medalla.
4. `temporada`, con suma, promedio, máximo y mínimo de altura por temporada.
5. `sexo`, con suma, promedio, máximo y mínimo de edad por sexo.
6. Salidas visibles, controles y explicaciones breves de cada resultado.
7. Conclusiones sobre resultados reales, fuentes y liberación de recursos al final del notebook.

El notebook completo debe seguir funcionando desde un kernel reiniciado. Tu bloque no debe reconstruir los RDDs ni sustituir la integración de Sebastián.

## 2. Entrada disponible

Las salidas revisadas contienen **271.116 participaciones**, correspondientes a **135.571 deportistas** en la tabla de deportistas. La tabla integrada mantiene cinco particiones antes de tus consultas. Una persona puede aparecer varias veces por participar en distintos resultados.

| Columna disponible | Tipo observado | Significado y uso |
|---|---|---|
| `resultado_id` | int | Identificador único de participación; sirve para controlar que los cálculos no cambien la cantidad de registros |
| `deportista_id` | int | Identificador de persona; no es único en la tabla integrada |
| `equipo_id` | int | Identificador del equipo; conservarlo en las agrupaciones |
| `equipo` | string | Nombre del equipo; existen valores nulos |
| `sigla` | string | Sigla disponible del equipo |
| `genero` | int | 1 = hombre; 2 = mujer |
| `edad` | int | Edad en años; los ceros se convirtieron a nulo |
| `altura` | double | Altura en centímetros; los ceros se convirtieron a nulo |
| `peso` | double | Peso en kilogramos; los ceros se convirtieron a nulo |
| `medalla` | string | `Gold`, `Silver`, `Bronze` y `NA`; este último significa sin medalla |
| `temporada` | string | `Verano` o `Invierno`, ya corregidos por Sebastián |
| `anio` | int | Año de los juegos, ya normalizado |

Conservar también las demás columnas de `df_integrado`, incluidas las de trazabilidad de Juegos. No volver a interpretar `ciudad_fuente` como una ciudad: el archivo fuente contiene etiquetas desplazadas y la corrección ya está documentada.

**Anomalías que debes contemplar:**

- 200 participaciones sin nombre de equipo identificado. La revisión muestra casos con `equipo_id = 0`.
- 9 participaciones sin evento identificado. No afectan por sí solas las agregaciones solicitadas; no eliminarlas por ese motivo.
- En la tabla integrada se observaron 9.474 edades nulas, 60.171 alturas nulas y 62.875 pesos nulos. Son conteos por participación, diferentes de los nulos de la tabla de personas.

## 3. Secuencia de trabajo

Las tareas J1 a J7 forman un único bloque consecutivo. El esfuerzo estimado es de **5,25 horas**, sin incluir el video ni la revisión grupal. Reservar una hora adicional si surgen problemas de entorno o resultados.

| Paso | Actividad | Producto | Tiempo |
|---|---|---|---|
| J1 | Preparar entorno y comprobar entrada | Sesión completa hasta `df_integrado` y diagnóstico de columnas | 45 min |
| J2 | Crear y validar columnas calculadas | `df_analisis` con IMC y descripción de sexo | 45 min |
| J3 | Consultar medallas por equipo | `medallas_equipo` y conciliación de medallas | 45 min |
| J4 | Consultar edad por medalla | `edad_medalla` con cuatro medidas y cobertura | 45 min |
| J5 | Consultar altura por temporada | `temporada` con cuatro medidas y cobertura | 45 min |
| J6 | Consultar edad por sexo | `sexo` con cuatro medidas y cobertura | 45 min |
| J7 | Ejecutar, revisar y documentar | Notebook integrado, conclusiones y controles aprobados | 45 min |

### J1 Preparar entorno y comprobar la entrada

1. Actualizar tu copia del repositorio y crear una rama propia, por ejemplo `bloque-jose-sql`.
2. Reproducir el entorno actual siguiendo el README:

```bash
uv sync --locked --no-install-project
uv run --no-sync jupyter lab
```

El parámetro `--no-install-project` evita el problema ya documentado del módulo de empaquetado ausente. Resolver esa configuración es una mejora grupal separada; no es requisito para comenzar tu bloque usando el procedimiento del README.

3. Seleccionar el kernel de `.venv` y comprobar que Java esté disponible. El proyecto actual fija Python 3.14 y PySpark 4.3.0. El notebook revisado contiene una ejecución Linux con Java 17 y una preparación original macOS con Java 21; documentar la combinación efectiva de tu equipo.
4. Reiniciar el kernel y ejecutar todas las secciones existentes hasta obtener `df_integrado`.
5. Comprobar columnas requeridas, tipos, filas, unicidad de `resultado_id`, valores distintos de `medalla` y `genero`, y nulos relevantes.
6. Registrar el conteo inicial en una variable de control para compararlo después de crear las columnas.

**Aceptación:** dispones de la tabla real de entrada y puedes ejecutar todas tus celdas sin depender de variables creadas manualmente fuera del notebook.

### J2 Crear IMC y descripción de sexo

1. Crear `df_analisis` a partir de `df_integrado`, conservando sus columnas.
2. Calcular `IMC` como `peso / ((altura / 100.0) * (altura / 100.0))`, porque la altura está en centímetros y el peso en kilogramos.
3. Calcularlo únicamente cuando altura y peso sean numéricos válidos, presentes y positivos. Si aparecen NaN o infinitos, tratarlos explícitamente como inválidos. Dejar IMC nulo cuando no se cumpla la condición; no imputar valores para completar la columna.
4. Crear la columna exacta `Descripción_sexo`: 1 = `Hombre`; 2 = `Mujer`; cualquier otro código o nulo = `No informado`.
5. Mantener precisión suficiente del IMC; redondear solo para mostrarlo.
6. Mostrar una muestra con identificador, altura, peso, IMC, género y descripción. Mostrar también cantidades de IMC válidos y nulos y la distribución de las descripciones.
7. Verificar que se conserva el número de filas y la unicidad de `resultado_id`.
8. Validar un caso conocido: 80 kg y 180 cm producen aproximadamente **24,69**. Mostrar además casos de control de datos ausentes o no positivos. Si se crean filas artificiales, separarlas de las salidas analíticas sobre datos oficiales.

**Aceptación:** ambas columnas están visibles, los valores son coherentes y los casos inválidos no generan divisiones por cero ni IMC engañosos.

### J3 Medallas por equipo mediante Spark SQL

1. Registrar `df_analisis` como vista temporal, por ejemplo `participaciones_olimpicas`.
2. Ejecutar la consulta con `spark.sql`, utilizando agregación condicional para contar `Gold`, `Silver` y `Bronze`.
3. Agrupar por identificador y nombre de equipo. Conservar `equipo_id` para evitar unir equipos distintos únicamente porque comparten nombre.
4. Mostrar un nombre explícito como `Equipo no identificado` para los nombres nulos, conservando el código original.
5. Crear las columnas `oro`, `plata`, `bronce` y, como ayuda para revisión, `total_medallas`.
6. Incluir ceros para los tipos de medalla no obtenidos. Una agrupación condicional sobre todas las participaciones conserva también equipos participantes sin medallas. Si se desea incluir todo el catálogo Equipos, aun sin participaciones, añadirlo mediante una unión explícita; explicar ese alcance.
7. `NA` no cuenta como medalla. Comprobar si existen otros valores antes de clasificarlos; no asumir que un código desconocido significa ausencia de medalla.
8. Ordenar para mostrar los primeros equipos y mostrar separadamente el grupo sin equipo identificado.
9. Conciliar la suma de oro, plata y bronce por equipos con los conteos globales de esos tres códigos en la entrada.

**Aceptación:** `medallas_equipo` contiene los tres conteos y conserva todas las medallas de la entrada, incluidas las que correspondan a equipos no identificados.

**Interpretación:** se cuentan registros de medallas en participaciones individuales. No presentar este resultado como un medallero oficial de pruebas colectivas.

### J4 Edad por tipo de medalla mediante Spark SQL

1. Crear `edad_medalla` con suma, promedio, máximo y mínimo de `edad` por `medalla`.
2. Para mantener trazabilidad, incluir `NA` como grupo separado etiquetado `Sin medalla`, además de Oro, Plata y Bronce. Explicar que es un grupo complementario; no lo mezclar con las tres medallas.
3. Añadir `n_participaciones` y `n_edad_valida` como controles de cobertura; no sustituyen las cuatro medidas exigidas.
4. Dejar que los agregados numéricos omitan las edades nulas y explicar cuántas observaciones se utilizaron en cada grupo. No reemplazar esas edades por cero.
5. Mostrar la tabla completa y comprobar que, cuando hay valores válidos, el promedio está entre el mínimo y el máximo.

**Aceptación:** las cuatro medidas están presentes, los grupos se interpretan correctamente y la cantidad de edades utilizadas es verificable.

### J5 Altura por temporada mediante Spark SQL

1. Crear el DataFrame exacto `temporada` usando `spark.sql`.
2. Agrupar por la columna normalizada `temporada` y calcular suma, promedio, máximo y mínimo de `altura`.
3. Conservar centímetros como unidad de estas estadísticas y usar nombres de columnas que lo indiquen, por ejemplo `promedio_altura_cm`.
4. Añadir número de participaciones y número de alturas válidas para documentar cobertura.
5. Mostrar las filas de Verano e Invierno y cualquier grupo inesperado, si aparece.
6. Comprobar que los conteos de grupos concilian con la entrada y que el promedio válido está entre los extremos.

**Aceptación:** `temporada` contiene las cuatro estadísticas de altura, con unidades y tratamiento de nulos explícitos.

### J6 Edad por sexo mediante Spark SQL

1. Crear el DataFrame exacto `sexo` usando `spark.sql`.
2. Agrupar por `genero` y su descripción; en SQL referenciar `Descripción_sexo` entre comillas invertidas cuando corresponda.
3. Obtener suma, promedio, máximo y mínimo de `edad`.
4. Añadir número de participaciones y número de edades válidas por grupo.
5. Mantener un grupo de no informados si existe; no borrar esos registros antes de la consulta.
6. Mostrar la tabla completa y conciliar la suma de participaciones con el total de entrada.

**Aceptación:** `sexo` muestra las cuatro medidas, los códigos son interpretables y el tratamiento de edades nulas está explicado.

### J7 Validar y cerrar tu bloque

1. Revisar que las cuatro consultas se ejecuten explícitamente con Spark SQL; no dejar únicamente soluciones con `groupBy` de la API de DataFrames.
2. Añadir una breve explicación antes de cada consulta y una interpretación sustentada en los resultados después de su salida.
3. Añadir conclusiones generales y fuentes efectivamente utilizadas. No anticipar hallazgos que no aparezcan en las tablas.
4. Reiniciar el kernel y ejecutar todo el notebook de principio a fin. Guardar las salidas finales sin errores.
5. Comprobar que los controles existentes de seis particiones iniciales y cinco de la tabla integrada siguen visibles. Las agregaciones pueden tener otra distribución de particiones; eso no invalida la evidencia anterior.
6. Liberar al final `df_integrado` con `unpersist()` y cerrar la sesión con `spark.stop()`. Si se persisten otros objetos, liberarlos también después de su último uso. Revisar con Yerko las cachés RDD que sigan activas.
7. Revisar el diff antes de enviar tu rama: no eliminar ni alterar innecesariamente las secciones de tus compañeros. Si se ejecutó el notebook completo, es esperable que se actualicen sus salidas y metadatos.
8. Solicitar a Yerko la revisión de tu bloque, especialmente IMC, consultas, conteos y unidad de análisis.

**Aceptación:** el notebook funciona completo y el código, las salidas y el texto explicativo son coherentes entre sí.

## 4. Orden sugerido dentro del notebook

Mantener las secciones 1 a 8 existentes y agregar:

9. Columnas calculadas — José.
10. Agregaciones con Spark SQL — José.
    - Medallas por equipo.
    - Edad por tipo de medalla.
    - Altura por temporada.
    - Edad por sexo.
11. Controles finales y conclusiones.
12. Fuentes y cierre de recursos.

Los nombres de DataFrames `temporada` y `sexo`, y de las columnas `IMC` y `Descripción_sexo`, deben respetar los solicitados, aunque la numeración de las secciones difiera de la consigna.

## 5. Matriz de evidencia de tus 33 puntos

| Criterio | Producto exigido | Evidencia visible |
|---|---|---|
| Columnas calculadas — 12 puntos | IMC y Descripción_sexo | Muestra de columnas, caso de IMC comprobado, distribución de descripciones y tratamiento de inválidos |
| Agregaciones — parte de 21 puntos | Oro, plata y bronce por equipo | Tres conteos por equipo y conciliación global |
| Agregaciones — parte de 21 puntos | Edad por medalla | Suma, promedio, máximo y mínimo visibles |
| Agregaciones — parte de 21 puntos | DataFrame temporada | Cuatro estadísticas de altura por temporada |
| Agregaciones — parte de 21 puntos | DataFrame sexo | Cuatro estadísticas de edad por sexo |

La rúbrica asigna 21 puntos al conjunto de las cuatro agregaciones; no especifica un puntaje individual para cada producto.

## 6. Decisiones metodológicas que debes explicar

- **Participaciones y personas:** las agregaciones se realizan sobre participaciones de la tabla integrada. Una misma persona puede pesar varias veces. No deduplicar sin cambiar y justificar la pregunta analítica.
- **Edad histórica:** el modelo vincula una edad de la tabla Deportista con múltiples resultados. Sin información adicional, no afirmar que esa edad corresponde exactamente a la edad de la persona en cada edición.
- **Medallas colectivas:** varios deportistas de un mismo equipo pueden representar registros asociados a una misma prueba colectiva. El conteo solicitado no equivale necesariamente a medallas oficiales por evento.
- **Nulos:** `COUNT(*)` mide participaciones; `COUNT(edad)` o `COUNT(altura)` mide observaciones utilizables para cada estadística. Distinguirlos al interpretar los promedios.
- **Equipos desconocidos:** conservarlos y mostrarlos evita perder medallas y participaciones en los totales.
- **Unidades:** IMC usa metros; las estadísticas de altura pueden conservar centímetros, siempre que se indique.

## 7. Tu intervención en el video

Reservar aproximadamente tres minutos, dentro de un video grupal de 7 a 10 minutos.

| Tramo propio | Contenido |
|---|---|
| 0:00 a 0:40 | Entrada df_integrado, IMC, conversión de unidades y descripción de sexo |
| 0:40 a 1:20 | Conteos de oro, plata y bronce por equipo; conciliación y equipos desconocidos |
| 1:20 a 2:25 | Edad por medalla, altura por temporada y edad por sexo; resultados reales y cobertura |
| 2:25 a 3:00 | Participaciones frente a personas, nulos y conclusión principal |

No consumir el tiempo leyendo todas las consultas. Mostrar sus salidas y explicar qué decisión tomó el equipo y cómo comprobó que era correcta.

## 8. Lista de verificación antes de entregar tu bloque

- [ ] Se ejecuta con el entorno uv del repositorio y el kernel correcto.
- [ ] df_analisis conserva todas las filas y columnas de entrada.
- [ ] IMC convierte centímetros a metros y controla valores inválidos.
- [ ] Descripción_sexo conserva su nombre exacto y los códigos 1 y 2.
- [ ] Las cuatro agregaciones usan spark.sql y tienen salidas guardadas.
- [ ] medallas_equipo contiene oro, plata y bronce y concilia los totales.
- [ ] Los equipos desconocidos se muestran explícitamente.
- [ ] edad_medalla muestra las cuatro medidas y documenta el grupo sin medalla.
- [ ] temporada muestra cuatro medidas de altura en unidades explícitas.
- [ ] sexo muestra cuatro medidas de edad y los grupos son interpretables.
- [ ] Los nulos se cuantifican y no se sustituyen por cero para calcular promedios.
- [ ] Las conclusiones usan resultados reales y distinguen participaciones de personas.
- [ ] El notebook completo se ejecutó desde un kernel reiniciado.
- [ ] Yerko revisó los resultados y sus observaciones quedaron resueltas.
- [ ] La intervención de tres minutos se ensayó con las salidas definitivas.

**Cierre de tu bloque:** código, evidencia y explicación de los criterios que suman 33 puntos listos para revisión. El video, el formato final, el ZIP y la carga al buzón se cierran con el equipo.
