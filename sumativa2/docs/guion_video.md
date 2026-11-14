# Guion del video — Taller I

Video grupal `mcdi502s2_grupo1.mp4`, de 7 a 10 minutos. Cada integrante presenta su bloque mostrando las salidas del notebook ya ejecutado.

| Tramo | Integrante | Bloque | Secciones del notebook |
|---|---|---|---|
| 0:00–3:00 | Yerko Gallardo | Entorno, RDDs, unión, DataFrame `deportista` y transformaciones | 1 a 3 |
| 3:00–6:00 | Sebastián Rojas | DataFrames, integración, optimización, paralelismo e inspección | 4 a 6 |
| 6:00–9:00 | José Ignacio Rodríguez | Columnas calculadas, agregaciones con Spark SQL y conclusiones | 7, 8 y 10 |

En todos los bloques: no lean las consultas completas. Muestren sus salidas y expliquen qué se decidió y cómo se comprobó, con las cifras del notebook. No afirmen que algo es más rápido ni que una diferencia tiene una causa si el notebook no lo muestra.

## Bloque de Yerko Gallardo — secciones 1 a 3

**0:00–0:45 — entorno.** «Usamos uv para fijar Python 3.14 y PySpark 4.3.0. La celda 1.1 reutiliza el Java que ya esté instalado, siempre que sea 17 o superior; si no hay ninguno, lo instala. Luego define `JAVA_HOME` y las variables de PySpark. Creamos la SparkSession en modo clásico y obtenemos el SparkContext, porque el taller usa RDDs». Muestra las variables impresas, las versiones y la prueba mínima.

**0:45–1:45 — creación y unión de RDDs.** «Antes de crear los RDDs revisamos los archivos crudos: no tienen encabezado, y una línea trae una coma de más, que reparamos sin descartar el registro. El RDD `deportista` tiene seis particiones y 67.786 registros; `deportista2` tiene 67.785. Al unirlos, `deportistaTotal` queda con 135.571 registros, sin identificadores repetidos. Lo convertimos en el DataFrame `deportista` con un esquema explícito de siete columnas. Las edades, alturas y pesos iguales a cero se convierten a nulo, porque son datos no registrados». Muestra las particiones, la conciliación de la unión y el esquema.

**1:45–3:00 — transformaciones.** «`MayorEdad` filtra 123.244 deportistas de 18 años o más. Las edades no registradas no se cuentan como menores. `Deportistas_mujer` contiene los 33.981 registros con género 2. La conversión a mayúsculas se aplica al nombre, el único campo de texto, y comprobamos que ningún valor numérico cambió». Muestra las muestras y los conteos de cada transformación.

## Bloque de Sebastián Rojas — secciones 4 a 6

**3:00–4:00 — fuentes y calidad.** «Continúo con el DataFrame de deportistas construido por Yerko. Cargo eventos, resultados, equipos y juegos con esquemas explícitos. Resultados usa punto y coma, los otros CSV usan coma y Juegos es un arreglo JSON. En Juegos encontramos etiquetas desplazadas: la columna temporada tenía años y ciudad tenía Verano o Invierno. Verificamos esa relación en todas las filas, renombramos y conservamos los valores originales para trazabilidad».

**4:00–5:00 — integración.** «Usamos Resultado como eje porque cada fila representa una participación. Antes del join comprobamos que las claves de cada dimensión sean únicas. Usamos joins izquierdos para conservar las participaciones que no tienen equipo identificado. Los controles comparan el conteo de entrada con el final y verifican que cada resultado siga apareciendo una sola vez». Muestra los 271.116 registros conciliados y una fila sin equipo.

**5:00–6:00 — optimización, paralelismo e inspección.** «Las dimensiones pequeñas se distribuyen con broadcast, luego de verificar su tamaño. Persistimos la tabla integrada porque la inspección y las consultas de José la reutilizan. Reparticionamos a cinco: mostramos cuántas particiones había antes, que ahora son 5 y cuántas filas contiene cada una. Son particiones lógicas, no cinco computadores. La inspección muestra la cantidad de filas, los tipos y el esquema». Muestra el cambio de particiones y la salida de la sección 6.

## Bloque de José Ignacio Rodríguez — secciones 7, 8 y 10

**6:00–6:40 — columnas calculadas.** «Continúo desde `df_integrado`, con 271.116 participaciones. El IMC es el peso en kilos dividido por la altura en metros al cuadrado; como la altura viene en centímetros, primero la divido por 100. Solo lo calculo cuando la altura y el peso existen, son positivos y finitos; si no, queda nulo, sin imputar. Lo comprobé con un caso conocido: 180 cm y 80 kg dan 24,69. Hay IMC en el 76,3 % de las participaciones. `Descripción_sexo` traduce 1 a Hombre y 2 a Mujer». Muestra la muestra de 7.2 y los casos de control de 7.3.

**6:40–7:20 — medallas por equipo.** «Las cuatro agregaciones usan Spark SQL sobre una vista temporal. Para las medallas, cada fila suma 1 en la columna de oro, plata o bronce con `CASE WHEN`. Agrupo por `equipo_id` y nombre, para no mezclar equipos con el mismo nombre. Las 200 participaciones sin equipo quedan como "Equipo no identificado" en vez de perderse. La suma por equipos coincide con la entrada: 13.372 oros, 13.116 platas y 13.295 bronces». Muestra los primeros equipos y la conciliación.

**7:20–8:25 — edad, altura y sexo.** «Para cada grupo calculo la suma, el promedio, el máximo y el mínimo, y además cuento las participaciones y cuántas tienen el dato. Los nulos no se cuentan como cero. La edad promedio es casi igual en oro, plata, bronce y sin medalla: alrededor de 23,7 años. En altura, Verano promedia 175,5 cm e Invierno 174,6. Por sexo, las mujeres promedian 21,9 años y los hombres 24,4». Muestra `edad_medalla`, `temporada` y `sexo`.

**8:25–9:00 — decisiones y cierre.** «Todo se mide por participación, no por persona: quien compite varias veces cuenta varias veces. Por eso estos conteos no son el medallero oficial; en una prueba por equipos, cada integrante suma una medalla. Verificamos cada tabla contra la entrada, y al final liberamos la caché y cerramos Spark».
