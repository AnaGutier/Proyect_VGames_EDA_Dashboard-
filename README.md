# :chart_with_upwards_trend: Proyecto EDA - Dashboard en Google Sheets

 :dart: Descripción del proyecto 

- En qué consiste y cuál es el objetivo - mencionar brevemente el contexto del análisis y los problemas que he resuelto - técnicas y enfoques usados. Un parrafito.
- Enlace al sheets!!!!!!!!

IMPORTANTE REALIZAR Y SEÑALAR QUE REALIZO EL EDA CON UN OBJETIVO PROPIO: VOY A HACER UN ESTUDIO DE LAS VENTAS!!!! 
 VENTAS de cada juego en realición a diferentes parámetros (localización, año,)
Aun así es importante analizar todas las variables entre sípara captiar todas sus relaciones.

## :postbox: 1. La fuente de los datos, enlace a Kaggle 
Y EL ACCESO AL GOOGLE SHEETS URL DE ACCESO PÚBLICO EN MODO LECTURA

:space_invader:

API: kaggle kernels pull artemsolomko/video-games-sales-with-score-eda-and-stat-test 

```bash
URL: https://www.kaggle.com/code/artemsolomko/video-games-sales-with-score-eda-and-stat-test/input 
```

## :computer: 2. Estructura del repositorio
```bash
|--> Data #Datos en crudo
|-----> video_Games_Sales_as_at22_Dec_2016.csv
|--> Google Sheets .extensión? #Enlace al archivo de Google Sheets??
|--> README.md #Descripción del proyecto 

```

## :hourglass: 3. Fases de realización del proyecto 

### 3.1 Carga y comprensión general de los datos
En primer lugar, realizo la carga de los datos a u nuevo archivo de  Google Sheets, espacio donde trabajaré con ellos. A continuación inspecciono de forma genérica filas y columnas para comprender las principales características de las mismas.

Definicion de las columnas en orden de aparición según elarchivo original:

- **Name:** Nombre del videojuego. No es un valor único porque hay juegos con un mismo nombre que se juegan en diferentes consolas sin ser exactamente el mismo (pues cada consola ofrece diferentes prestaciones y experiencia de juego). Valor nominal.
- **Platform:** Plataforma o consola donde se juega el videojuego en cuestión. Valor nominal.
- **Year_of_Release:** Año en que el videojuego es publicado. Valor numérico y entero.
- **Genre:** Género o tipo de videojuego. Valor nominal (por ejemplo, hay juegos de lucha, plataformas, deportes...). 
- **Publisher:** Editor del videojuego, la empresa que lo publica. Valor nominal.
- **NA_sales:** Ventas en Norteamérica. Valores numéricos cuya unidad representa el millón de copias vendidas. 
- **EU_sales:** Ventas en Europa. Valores numéricos cuya unidad representa el millón de copias vendidas. 
- **JP_sales:** Ventas en Japón. Valores numéricos cuya unidad representa el millón de copias vendidas. 
- **Other_sales:** Ventas en otras regiones. Valores numéricos cuya unidad representa el millón de copias vendidas. 
- **Global_sales:** Ventas globales. Valores numéricos cuya unidad representa el millón de copias vendidas. 
- **Critic_Score:** Valoración de los críticos. Valor numérico de 0 a 100.
- **Critic_count:** - The number of critics used in coming up with the Criticscore
- **User_Score:** Valoración de los usuarios. Valor nominal entre 0 y 10.
- **Rating:** Clasificación de la organización ESRB (English Entertainment Software Rating Board), basada en la edad apropiada del público al que se orienta el videojuego en cuestión. Valor nominal (categorías que agupan edades con códigos como E, EC,E10+ T, M...). 
- **Developer:** Desarrollador del videojuego. Valor nominal.

Como consideraciones preliminares puedo observar que el set de datos se orienta a las ventas de la empresa, que varían en función de las diferentes características de los videojuegos.

### 3.2. Trasnformación y limpieza de datos
#### 3.2.1 Valores duplicados
Comienzo la transformación y limpieza de datos atendiendo en cada columna a los valores vacíos y únicos si estos son releavntes para poder identificar posibles anomalías o necesidad de transformación. En primer lugar, observo que las 2 últimas filas no contienen ningún dato, así que las elimino.

Además, me percato de que no hay ninguna columna con un identificador único para cada videojuego. La que en un primer lugar pudiera parecer algo similar es la del nombre de estos, sin embargo, este no cuenta con el mismo número de valores únicos que de filas (identificado gracias a las estadísticas de columna que ofrece google sheets). Me aseguro de que no se tratara de un error, comprobando que las frecuencias máximas eran de 9, y filtrando por este criterio observo que esto se debe a que hay videojuegos en diferentes consolas que pueden tener un mismo nombre, no se trata de un error. 

Decido generar una nueva columna llamda _ID_juego_ para tener un identificador único decada fila por si fuera necesario hacer uso de ello. Para comprobar también mediante esta columna que no haya datos duplicados, sin caer en el error que puede suponer buscar simplemente juegos con el mismo nombre, creo una columna que una valores que no puedan coincidir entre sí. Es decir, uno mediante la siguiente fórmula el nombre del juego, la plataforma o consola, el desarrollador y año de publicación.

```
=ARRAYFORMULA(SI(A2:A=""; ""; A2:A & "_" & B2:B & "_" & D2:D & "_" & C2:C)) 
```
ARRAYFORMULA: permite que la fórmula se aplique a toda la columna al ponerla sólo en la primera celda

SI: Evita generar identificadores si hay algún valor vacío en los seleccionados, que resultan imprescindibles (traducción de "IF" al usar la versión en español de sheets).

Gracias esto resulta una columna sin celdas vacías, con 16.719 filas y 16.717 valores únicos. Es decir, 2 valores repetidos. Gracias a las estadísticas de columna veo un valor con frecuencia 2, el cual filtro y al tener ambas filas las mismas características mencionadas en la fórmula pero diferentes ventas, decido eliminarlas.  

#### 3.2.2 Correciones varias en transformación de datos
En la columna de plataforma, encuentro 31 valores únicos, pero muchos de ellos con frecuencias muy escasas (1, 2, 3, 6, 12, 29 y 52), por lo que no resultan relevantes frente a las 16.717 filas totales. Para una comprensión y visualización más clara de los datos, elimino estas filas al no suponer datos representativos.

Hay varias columnas con valores numéricos (correpondientes a año de publicación, ventas y valoraciones) que incialmente están en un formato de texto. Los transformo a formato numérico. Para que sheets reconozca los puntos como separación para los decimales decido poner la configuración de EEUU en lugar de la de España que era de la que partía este archivo.

En concreto con la columna de año de publicación, al tener sólo el dato del año, no se reconoce como fecha al cambiar el formato. Por ello genero una nueva conluma _"Year_Date"_ con la siguiente fórmula para poder aplicar el formato fecha y luego personalizarlo para sólo visualizar el año (el dato real) pero trabajar con el formato que corresponde.
```
=ARRAYFORMULA(FECHA(C2,1,1))
```
Al realizar esto me doy cuenta de que en algunos casos la fórmula no funciona debido a que hay 150 filas no numéricas, que contienen "N/A". Al tratarse de menos de un 1% de los datos totales decido eliminarlas al no resultar representativas.

Por último, observo que las valoraciones de la crítica están en una escala de 0 a 100 y las de los usuarios de 0 a 10 (usando decimales en esta última columna). Decido uniformar el formato de ambas para una mejor comprensión, genrando una nueva columna _"User_Score_0/100"_ donde las valoraciones de los usuarios se encontrarán en un formato de 0 a 100 gracias a la siguiente fórmula:
```
=ARRAYFORMULA(SI(ESNUMERO(M3), M3 * 10, ""))
```

#### 3.2.3 Columnas con valores faltantes destacables
Las columnas con considerables valores faltantes son las de valoración (Critic_Score, Critic_Count, User_Score y User_Count),la de la clasificación (Rating) y la de desarrollador (Developer). Estos valores faltantes llegan a suponer en algunos casos un tercio u incluso más del total, por lo que es necesario tener esto en cuenta a la hora de realizar el análisis de los mismos. Estos datos pueden ser útiles pero a su vez pueden generar sesgos si no tenemos esto en cuenta, e ignorar los juegos que no cuentan con estos datos supone reducir demasiado la muestra. Por ello se realizará el análisis principal enfocado en las ventas y las columnas completas (asegurando una base sólida para este EDA), dejando las otras como un indicador secundario del que no podemos extrapolar al conjunto total, remarcando su carater parcial. Además, se tratará de comprobar si esta ausencia de datos sigue un patrón reconocible. 
 

### 3.3 Análisis descriptivo de los datos 

Genero una nueva hoja _"Tablas Din 1"_ donde comienzo a generar tablas dinámicas donde relaciono las diferentes variables del set de datos. Junto a estas genero gráficos que me permiten comenzar a comprender de una forma visual las tendencias de los datos. Uso formatos condicionales para mejor visualización de las tablas. Clasifico esas tablas de la siguiente forma:

#### 3.3.1 Características de los juegos
Atiendo en primer lugar al  año, género, plataforma, clasificación, desarrollador y empresa que publica los juegos en función del conteo de estos. Así observo que:
-  La mayoría de juegos se publican en los años 2002 - 2011, existiendo cifras considerables desde finales de los 90 hasta 2016. Resulta extraño que desde 2009 el numero de videojuegos vaya en descenso, y especialmente que en 2017 y 2020 sólo se registres 3 y 1 videojuego respectivamente (sin datos de 2018 y 2019) ya que elmercado de videojuegos continuaba muy activo y creciente en esos años. Esto puede deberse a la recogida de datos. 

- Los géneros más populares son acción (con una gran diferencia), deportes, misc (juegos que engloban varios géneros), role-playing, shooters, aventuras y carreras, en ese orden. Los menos populares son rompecabezas y estrategia.

- En las plataformas predominan PS2 y DS con gran ventaja, seguidas de PS3, X360, Wii, PSP y PS. Las minoritarias son GB y NES. 

- La clasificación más numerosa es con amplia diferecia E () seguida de T (), siendo residuales los casos de juegos clasificados como AO (Adults Only), RP (Rating Pending), K-A (Kids to Adults) y EC (Early Childhood). Estos últimos serán desestimados por su escaso número en indagaciones postreiores. 

- En el caso de las catogrías de desarrollador y empresa publicadora del juego, encontramos tal diversidad que no resultan esclarecientes las tablas y gráficos. Para considerar estas variables atenderemos a la frecuencia de las mismas o a su realción con otras variables si estas brindaran resultados relevantes.

_Inciso: para la visualización de tablas dinámicas con datos faltantes, he añadido un filtro en la propia tabla para ocultar esos datos vacíos y poder observar únicamente la información relevante (siendo consciente de estas ausencias como he explicado antes y explicito en el informe final)._

A continuación realciono todas estas variables entre sí, usando los filtros necesarios para los valores poco relevantes que sólo oscueren la visibilidad de las tendencias de los datos, dando lugar a las siguientes observaciones:

- Año y plataforma: A lo largo del tiempo las plataformas más usadas varían, destacan PS a finales de la década de 1990, hasta que aparece PS2 que rápidamente la sustitye, dejando paso para un enorme auge de DS a final de la década de los años 2000. Este momento también será el que cuenta con mayor variedad de plataformas, con valores relevantes de Wii, X360 o PS3 y dejando obsoletas algunas como SNES, o la inicialmente mencionada PS. 

- Año y género: Entre géneros, a lo largo del tiempo se observa la predominancia de los juegos de acción,superada únicamente por los deportivos hasta inicios de la década de los 2000. Pocos años después aumentan notablemente los juegos Misc y de aventuras. Poca relevancia.

- Año y rating: Las principales clasificaciones de videojuegos no presentan destacables cambios conel paso de los años. Tras su incorposación E10+ si experimenta gran crecimiento al rededor de 2005, siendo E siempre dominante a pesar de una curva e desceso a mediados de la década de los 2000.

- Plataforma y género: Aunque no hay una correlación relevante entre estas categorías, destaca la inusual popularidad de juegos de simulación en DS frente a otras consolas, la de estrategia en PC. Suelen predominar los juegos de deporte (PS2, Wii, x360) o los de Role-playing y shooters (PSP o 3DS).

- rating x plataforma: No brinda información muy destacable, en la mayoría predominan juegos para todos los públicos, aumentando las restricciones de edad en PS2, PS3, PC, XB y X36. Estas son también algunas de las más populares, así que tienen por lo general más variedad de juegos.

- rating x género: No presentan tendencias demasiado destacables,al margen de que en la mayoría de géneros predomina la categoría E (para todos los públicos),excepto en los de lucha y acción que sobre todo son T (Adolescentes, a partir de 13 años aprox), y los shootes M (Maduro, a partir de unos 17 años aprox). 

- Publicador y desarrollador: Tanta diversidad de publicadores y desarrolladores no permite extraer conclusiones que determinen tendencias a priori. Es por esto que al no resultar tan relevantes no aparecerán como una variable destacable postreiormente.

- Valoraciones: Al tratar las valoraciones observo que es a partir de los años 2000 que comienzan a efectuarse (motivo por el cual encontramos muchos datos faltantes), posiblemente al abrise los canales correspondientes para que críticos y usuarios valores los juegos. Hay un declive a medidados de la década de 2010. Las puntuaciones más altas coinciden por lo general tanto en ususarios como en crítcos, siendo según género acción, deportes y shooters los favoritos; por plataforma PS2 y x360, y en clasificación T y E. 


#### 3.3.2 Análisis de las ventas
En otra hoja del archivo genero las tablas dinámimcas en relación a los datos de las ventas.

- En términos de ventas totales casi la mitad de las ventas corresponden a norteamérica, un poco más de un cuarto a Europa, seguidas de las ventas en Japón y el resto quedando para "otras regiones".

- Las ventas por plataforma las lideran PS2, X360, PS3, Wii , DS y PS. En las ventas en Japón destacan sobre otros países consolas como SAT, SNES, NES, GB (más antiguas) o 3DS y PSP; siendo muy inferior la familia XB, XOne y X360 y también el PC.

- El género más vendido es acción, seguido aun con gran diferencia por deportes. Japón presenta excepciones como la preeminancia del Role-PLaying y Misc, que son poco populares en otras regiones.

- En términos globales la clasificación principal (40,2%) es E, seguida de T y M con un cuarto de las ventas aproximadamente en cada una de ellas. En las diferentes regiones la dinámica es similar a excepción de que en Japón E y T tienen un nivel de ventas similar dejando el resto de categorías como minoritarias. 

- Para realizar un estudio de las valoraciones genero columnas extra sobre las iniales con estos datos agrupados para poder generar tablas dinámicas y gráficos. Lo hago agregando a las nuevas columnas la siguiente fórmula en relación a su columna de referencia. Gracias  a esto puedo comprobar que según aumentan las puntuaciones en las valoraciones tanto de críticos como de usuarios, las ventas son mayores. Es importante diferenciar entre la suma de ventas y el promedio de las mismas, puesto que son mucho menores los juegos con valoraciones del último rango pero tienen un promedio de venta superior (es decir, se venden mejor).


```
=ARRAYFORMULA( SI(K2:K="", "", ENTERO(K2:K/10)*10 & "-" & (ENTERO(K2:K/10)*10+9) ))
```

-  En los ámbitos de empresa y desarrollador es tanta la variedad que no hay resultados reseñables más allá de los qu consiguen más ventas en las diferentes regiones, lo cual descubro gracias a hacer rangos de ventas (al igual que los rangos previamente generados para las valoraciones). En el caso de los publicadores destaca Nintendo en todos los espacios a excepción de la clasificación de otras regiones donde sobresale Take-Two Interactive. En el caso de los desarrolladores sucede lo mismo con Nintendo y en otras regiones predomina Rockstar North. 

### 3.4 Dashboard

Descarto la opción de incluir un mapa sobre las ventas porque al existir una categoría sobre "otros países" no tiene sentido asociarla a ningun espacio concreto y puede resultar impreciso.

He priorizado las cifras enteras frente a los porcentajes porque estas brindan infomración relevante sobre la magnitud de las ventas de la empresa. Además, al haber mucha variedad en ciertas categorías los porcentajes pueden generar una compresión menos clara de los datos.

_Big numbers:_  Destaco las ventas tanto globales como seccionadas por las diferentes regiones como indicadores principales.

_Gráficos centrales:_ Incluyo aquí indicadores de las principales variables asociados a las ventas, el eje central de este análisis. En primer lugar las ventas en relación al año en el que se publicaron los videojuegos, su clasificación, género, plataforma, y rangos de valoración tanto de usuarios como críticos. En los gráficos respectivos al género y plataforma de los juegos, al contar con muchas categorías y complicar su visualización, he filtrado las tablas dinámicas descartando de los gráficos aquellas categorías que a penas tenían datos y no resultaban relevantes (los géneros puzzle y strategy; y las plataformas 2006, GB, N64, NES, PS4, PSV, SAT, SNES, WiiU y XOne).

_Interactividad:_ He añadido filtros para acceder a información más concreta que interactuan con los diferentes gráficos. En la parte izquieda del dashboard sobre las variables de los gráficos centrales. Bajo los Big Numbers añado también filtros con los rangos de ventas de las diferentes regiones, incluyendo las ventas globales. Estos son más limitados ya que al modificar ciertos rangos desaparecen la gran parte de datos con los que se trata.

### 3.5 Informe explicativo del análisis 
Durante cada paso realizado genero anotaciones destacables a tener en cuenta a la hora de generar el informe, a lo que sumo un repaso del análisis realizado y el dashboard para llevarlo a cabo.

## :bulb: 4. Informe final y conclusiones  
### 4.1 Advertencias iniciales
El set de datos analizado presenta una proporción significativa de valores faltantes en variables de valoraciones (crítica y usuarios), clasificación ESRB y desarrollador. Esto limita el alcance de algunas interpretaciones, ya que los análisis basados en dichas columnas reflejan únicamente una parte de la muestra y no el total. Por ello, las conclusiones principales se centran en las ventas y en las variables con mayor completitud, utilizando las otras como indicadores secundarios de carácter parcial

### 4.2 Principales hallazgos 
- Evolución temporal: 
La mayor parte de los videojuegos del set fueron publicados entre finales de los 1990 y 2016, alcanzando un pico de publicaciones y ventas entre 2005 y 2010. Tras 2010 se observa un descenso en el número de lanzamientos registrados. Dado que el mercado mundial continuó creciendo en esos años, esta disminución probablemente obedezca a limitaciones en la recogida de datos o de la propia empresa, más que a la realidad del sector.

- Géneros: El género acción domina de forma clara en número de juegos y en ventas globales, seguido por deportes, role-playing y shooters. Géneros como puzzle y estrategia tienen presencia testimonial en ventas globales, aunque mantienen nichos concretos (el caso de los juegos de estrategia en PC).

- Clasificaciones: En la clasificación ESRB, los juegos E (Everyone) concentran más del 40% de las ventas, seguidos por T (Teen) y M (Mature), lo que refleja un mercado muy diversificado en públicos. Japón muestra un perfil distinto, con mayor equilibrio entre categorías.

- Plataformas: A lo largo de las décadas se observa un reemplazo cíclico de consolas dominantes. La PS2 y la DS aparecen como líderes absolutas en volumen de juegos y ventas, mientras que consolas más recientes como PS3, X360 y Wii también registran cifras relevantes. 
En Japón destacan plataformas propias o históricas como SNES, NES, GB, 3DS y PSP, confirmando una fuerte preferencia regional por plataformas de origen japonés.

- Ventas por región: Norteamérica concentra casi la mitad de las ventas globales, seguido de Europa (algo más de un cuarto), Japón y el resto del mundo. Japón presenta patrones diferenciados: fuerte preferencia por juegos de role-playing y misc, y ventas relativamente bajas de títulos de Xbox y PC. 

- Relación con valoraciones: Existe una correlación positiva entre la valoración (críticos y usuarios) y las ventas: Juegos mejor valorados tienden a vender más en promedio. No obstante, el número de juegos con valoraciones altas es reducido, lo que sugiere que un producto de gran calidad puede tener un impacto comercial muy superior aunque represente un pequeño porcentaje del catálogo.

- Publicadores y desarrolladores: La variedad de empresas es altísima, lo que fragmenta los datos. Sin embargo, cuando se agrupan las ventas, Nintendo aparece como el actor más fuerte en todas las regiones excepto en "otros", donde sobresale Take-Two Interactive. 
Entre desarrolladores sucede lo mismo: Nintendo lidera ampliamente, seguido en algunos casos por Rockstar North en regiones fuera de Japón.

### 4.3 Posibles hipótesis

- El descenso de lanzamientos en años recientes no refleja la realidad del mercado, sino carencias en la fuente de datos o de la empresa misma.

- La predominancia de géneros como acción y deportes podría estar relacionada con la accesibilidad y la preferencia por experiencias rápidas y competitivas en mercados como Norteamérica y Europa.

- Las diferencias en plataformas y géneros vendidos en Japón muestran que las preferencias culturales son determinantes y que las estrategias de localización deben ser específicas por región.

- El impacto de las valoraciones sugiere que la calidad percibida del producto (tanto por crítica como por los usuarios) es un factor comercial clave, incluso más allá del volumen de tjuegos ofertados.

### 4.4 Recomendaciones posibles para la empresa

- Concentrar esfuerzos en géneros dominantes (acción, deportes, role-playing, shooters), sin descuidar nichos regionales que pueden ofrecer rentabilidad en mercados específicos como es el caso japonés.

- Diseñar estrategias diferenciadas por región

- Invertir en calidad y en reseñas críticas/usuarios: los datos muestran que los juegos mejor valorados venden proporcionalmente más, lo cual sugiere que la inversión en innovación, jugabilidad y narrativa puede generar beneficios más altos.

- Fortalecer franquicias con historial de alto rendimiento (ejemplo: sagas de Nintendo), ya que concentran ventas de manera desproporcionada.

- Diversificación de plataformas: aunque las consolas dominantes cambian con los años, mantener presencia en varias reduce el riesgo de obsolescencia de las más populares actualmente. 

- Atender a la clasificación por edades: la categoría E sigue siendo la más amplia y segura comercialmente, pero T y M representan un público adolescente/adulto que puede aportar estabilidad y fidelización a largo plazo.

## :page_with_curl: X. Autores 

