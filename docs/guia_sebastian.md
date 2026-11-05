# Guía del bloque de Sebastián

Tu responsabilidad según el plan cubre creación e integración de DataFrames (18 puntos), paralelismo (9) e inspección (6): 33 puntos, además de tu participación en el video.

## Cómo revisar tu trabajo

1. Abre el notebook y ejecuta todo desde cero según el README. Las secciones 1–3 son la entrada de Yerko; tu bloque abarca las secciones 4 a 6.
2. Revisa los cuatro esquemas y la muestra cruda de Juegos. Explica por qué el año y la temporada se renombraron y por qué no podemos conocer la ciudad sede con ese archivo.
3. Revisa los controles de unicidad y los joins `left_anti`. Identifican claves sin correspondencia sin modificar los datos.
4. Comprueba que las filas de Resultado y `df_integrado` coinciden, que `resultado_id` no se repite y que el número de particiones pasa de 8 a 5.
5. Entrega a José el contrato de columnas de la sección 6. Su entrada es `df_integrado`, dentro de la misma sesión.

## Guion orientativo de tres minutos

**Primer minuto — fuentes y calidad.** «Continúo con el DataFrame de deportistas construido por Yerko. Cargo eventos, resultados, equipos y juegos con esquemas explícitos. Resultados usa punto y coma, los otros CSV usan coma y Juegos es un arreglo JSON. En Juegos encontramos etiquetas desplazadas: la columna temporada tenía años y ciudad tenía Verano o Invierno. Verificamos esa relación en todas las filas, renombramos y conservamos los valores originales para trazabilidad».

**Segundo minuto — integración.** «Usamos Resultado como eje porque cada fila representa una participación. Antes del join comprobamos que las claves de cada dimensión sean únicas. Usamos joins izquierdos para conservar las participaciones que no tienen equipo identificado. Los controles comparan el conteo de entrada con el final y verifican que cada resultado siga apareciendo una sola vez». Muestra los conteos efectivos del notebook y una fila sin equipo.

**Tercer minuto — optimización y salida.** «Las dimensiones pequeñas se distribuyen con broadcast, luego de verificar su tamaño. Persistimos la tabla integrada porque la inspección y las consultas de José la reutilizan. Reparticionamos a cinco: mostramos que se pasa de 8 a 5 particiones y cuántas filas contiene cada una. Son particiones lógicas, no cinco computadores. La salida incluye tipos, esquema, nulos y muestras. José recibe esta tabla para calcular IMC y agregar por medalla, temporada y sexo».

Adapta el guion a tu forma de hablar y a las salidas de tu ejecución. No afirmes que broadcast o cinco particiones son más rápidos: no se hizo una comparación de tiempos. El conteo representa participaciones, no personas únicas ni necesariamente medallas colectivas oficiales.

## Pendientes grupales

El bloque de José (secciones 7, 8 y 10) ya está en el notebook y el flujo completo se ejecutó sin errores. Falta que Yerko lo revise, grabar el video de 7–10 minutos y preparar el ZIP de entrega (ver la lista de verificación del README). El grupo 1 está confirmado.
