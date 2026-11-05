# Guía del bloque de José

Tu responsabilidad cubre las columnas calculadas (12 puntos) y las agregaciones requeridas (21): 33 puntos en total, además de tu participación en el video.

## Cómo revisar tu trabajo

1. Abre el notebook y ejecútalo desde cero según el README. Las secciones 1 a 6 son la entrada de Yerko y Sebastián; tu bloque abarca las secciones 7, 8 y 10.
2. En 7.1, comprueba que la entrada tiene 271.116 participaciones, que `resultado_id` es único y que `medalla` solo contiene `Gold`, `Silver`, `Bronze` y `NA`.
3. En 7.2 y 7.3, revisa la fórmula del IMC: la altura se convierte de centímetros a metros. Revisa también el caso conocido (180 cm y 80 kg dan 24,69) y que los casos inválidos queden nulos.
4. En 8.1, revisa la conciliación: las medallas sumadas por equipo deben coincidir con los totales de la entrada.
5. En 8.2 a 8.4, comprueba que cada tabla suma 271.116 participaciones y que el promedio queda entre el mínimo y el máximo.

## Guion orientativo de tres minutos

**0:00–0:40 — columnas calculadas.** «Continúo desde `df_integrado`, la tabla de Sebastián, con 271.116 participaciones. El IMC es el peso en kilos dividido por la altura en metros al cuadrado. Como la altura viene en centímetros, primero la divido por 100. Solo lo calculo cuando la altura y el peso existen, son positivos y son finitos; si no, queda nulo, sin imputar nada. Lo comprobé con un caso conocido: 180 cm y 80 kg dan 24,69. Hay IMC en el 76,3 % de las participaciones; en el resto falta la altura o el peso. `Descripción_sexo` traduce 1 a Hombre y 2 a Mujer, y cualquier otro valor a No informado». Muestra la muestra de 7.2 y los casos de control de 7.3.

**0:40–1:20 — medallas por equipo.** «Las cuatro agregaciones usan Spark SQL sobre una vista temporal. Para las medallas sumo 1 con `CASE WHEN` en la columna de oro, plata o bronce, según corresponda. Agrupo por `equipo_id` y nombre, para no mezclar equipos con el mismo nombre. Las 200 participaciones sin equipo quedan como "Equipo no identificado" en vez de perderse. La suma por equipos coincide con la entrada: 13.372 oros, 13.116 platas y 13.295 bronces». Muestra la tabla de los primeros equipos y la conciliación.

**1:20–2:25 — edad, altura y sexo.** «Para cada grupo calculo la suma, el promedio, el máximo y el mínimo, y además dos conteos: el total de participaciones y cuántas tienen el dato. Los promedios ignoran los nulos, no los cuentan como cero. La edad promedio es casi igual en oro, plata, bronce y sin medalla: alrededor de 23,7 años. En altura, Verano tiene 175,5 cm de promedio e Invierno 174,6, menos de un centímetro de diferencia. Por sexo, las mujeres promedian 21,9 años y los hombres 24,4». Muestra las tablas `edad_medalla`, `temporada` y `sexo`.

**2:25–3:00 — decisiones y conclusión.** «Todo se mide por participación, no por persona: quien compite varias veces cuenta varias veces. Por eso estos conteos no son el medallero oficial; en una prueba por equipos, cada integrante suma una medalla. Verificamos cada tabla contra la entrada, y la sección final libera la caché y cierra Spark».

Adapta el guion a tu forma de hablar. No leas las consultas completas: muestra sus salidas y explica qué se decidió y cómo se comprobó. No afirmes que las diferencias tienen una causa (por ejemplo, el deporte) si el notebook no lo muestra.
