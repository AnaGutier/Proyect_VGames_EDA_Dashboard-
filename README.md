# Análisis de  nosequé, título.
Primer Proyecto EDA - Dashboard en Google Sheets
Primera línea para subir un primer cambio en el repo 


IMPORTANTE REALIZAR Y SEÑALAR QUE REALIZO EL EDA CON UN OBJETIVO PROPIO: VOY A HACER UN ESTUDIO DE LAS VENTAS!!!! VEN-TAS
Pinta que puede ser ver las VENTAS de cada juego en realición a diferentes parámetros (localización, año,)
Aun así es importante analizar todas las variables entre sípara captiar todas sus relaciones.

## 0. La fuente de los datos, enlace a Kaggle 
Y EL ACCESO AL GOOGLE SHEETS URL DE ACCESO PÚBLICO EN MODO LECTURA

API: kaggle kernels pull artemsolomko/video-games-sales-with-score-eda-and-stat-test 

URL: https://www.kaggle.com/code/artemsolomko/video-games-sales-with-score-eda-and-stat-test/input 

## 1. Ver cómo hacer un buen readme
Con emojis, una foto de header, escribiendo bien en markdown, etc. Hay que describir la base de datos y demás (a parte de la estructura del repo que va ahora).

## 2. Estructura del repositorio
```bash
|--> Data
|-----> video_Games_Sales_as_at22_Dec_2016.csv
|--> Google Sheets

```


## Recap de sesiones ESTO ESLO QUE EVALUAN 
Ir poniendo lo que voy haciendo cada día, o mejor poner paso a paso o fases del proyecto. También me sirve a mi de recordatoriode lo que llevo y demás, para saber qué voy haciendo y en que la puedo cagar. 

### X.0 Carga y comprensión general de los datos
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

Las consideraciones preliminales de los datos: LO DEL OBJETIVO DEL PROPIO DATASET !!!!!!

### X.1 Trasnformación y limpieza de datos
#### X.1.1 Valores duplicados
Comienzo la transformación y limpieza de datos atendiendo en cada columna a los valores vacíos y únicos si estos son releavntes para poder identificar posibles anomalías o necesidad de transformación. En primer lugar, observo que las 2 últimas filas no contienen ningún dato, así que las elimino.

Además, me percato de que no hay ninguna columna con un identificador único para cada videojuego. La que en un primer lugar pudiera parecer algo similar es la del nombre de estos, sin embargo, este no cuenta con el mismo número de valores únicos que de filas (identificado gracias a las estadísticas de columna que ofrece google sheets). Me aseguro de que no se tratara de un error, comprobando que las frecuencias máximas eran de 9, y filtrando por este criterio observo que esto se debe a que hay videojuegos en diferentes consolas que pueden tener un mismo nombre, no se trata de un error. 

Decido generar una nueva columna llamda _ID_juego_ para tener un identificador único decada fila por si fuera necesario hacer uso de ello. Para comprobar también mediante esta columna que no haya datos duplicados, sin caer en el error que puede suponer buscar simplemente juegos con el mismo nombre, creo una columna que una valores que no puedan coincidir entre sí. Es decir, uno mediante la siguiente fórmula el nombre del juego, la plataforma o consola, el desarrollador y año de publicación.

```
=ARRAYFORMULA(SI(A2:A=""; ""; A2:A & "_" & B2:B & "_" & D2:D & "_" & C2:C)) 
```
ARRAYFORMULA: permite que la fórmula se aplique a toda la columna al ponerla sólo en la primera celda

SI: Evita generar identificadores si hay algún valor vacío en los seleccionados, que resultan imprescindibles (traducción de "IF" al usar la versión en español de sheets).

Gracias esto resulta una columna sin celdas vacías, con 16.719 filas y 16.717 valores únicos. Es decir, 2 valores repetidos. Gracias a las estadísticas de columna veo un valor con frecuencia 2, el cual filtro y al tener ambas filas las mismas características mencionadas en la fórmula pero diferentes ventas, decido eliminarlas.  

#### X.1.3 Correciones varias en transformación de datos
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

#### X.1.3 Columnas con valores faltantes destacables
Las columnas con considerables valores faltantes son las de valoración (Critic_Score, Critic_Count, User_Score y User_Count),la de la clasificación (Rating) y la de desarrollador (Developer). Estos valores faltantes llegan a suponer en algunos casos un tercio u incluso más del total, por lo que es necesario tener esto en cuenta a la hora de realizar el análisis de los mismos. Estos datos pueden ser útiles pero a su vez pueden generar sesgos si no tenemos esto en cuenta, e ignorar los juegos que no cuentan con estos datos supone reducir demasiado la muestra. Por ello se realizará el análisis principal enfocado en las ventas y las columnas completas (asegurando una base sólida para este EDA), dejando las otras como un indicador secundario del que no podemos extrapolar al conjunto total, remarcando su carater parcial. Además, se tratará de comprobar si esta ausencia de datos sigue un patrón reconocible. 
 

### X.2 Análisis descriptivo de los datos 

Genero una nueva hoja _"Tablas Din 1"_ donde comienzo a generar tablas dinámicas donde relaciono las diferentes variables del set de datos. Junto a estas genero gráficos que me permiten comenzar a comprender de una forma visual las tendencias de los datos. Uso formatos condicionales para mejor visualización de las tablas. Clasifico esas tablas de la siguiente forma:

#### X.2.1 Características de los juegos
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

- Publicador y desarrollador: Tanta diversidad de publicadores y desarrolladores no permite extraer conclusiones que determinen tendencias a priori.

- Valoraciones: Al tratar las valoraciones observo que es a partir de los años 2000 que comienzan a efectuarse (motivo por el cual encontramos muchos datos faltantes), posiblemente al abrise los canales correspondientes para que críticos y usuarios valores los juegos. Hay un declive a medidados de la década de 2010. Las puntuaciones más altas coinciden por lo general tanto en ususarios como en crítcos, siendo según género acción, deportes y shooters los favoritos; por plataforma PS2 y x360, y en clasificación T y E. 


#### X.2.2 Análisis de las ventas
En otra hoja del archivo genero las tablas dinámimcas en relación a los datos de las ventas.

- En términos de ventas totales casi la mitad de las ventas corresponden a norteamérica, un poco más de un cuarto a Europa, seguidas de las ventas en Japón y el resto quedando para "otras regiones".

- Las ventas por plataforma las lideran PS2, X360, PS3, Wii , DS y PS. En las ventas en Japón destacan sobre otros países consolas como SAT, SNES, NES, GB (más antiguas) o 3DS y PSP; siendo muy inferior la familia XB, XOne y X360 y también el PC.

- El género más vendido es acción, seguido aun con gran diferencia por deportes. Japón presenta excepciones como la preeminancia del Role-PLaying y Misc, que son poco populares en otras regiones.

- En términos globales la clasificación principal (40,2%) es E, seguida de T y M con un cuarto de las ventas aproximadamente en cada una de ellas. En las diferentes regiones la dinámica es similar a excepción de que en Japón E y T tienen un nivel de ventas similar dejando el resto de categorías como minoritarias. 

- "" x Empresa
- "" x Desarrollador

- Para realizar un estudio de las valoraciones genero columnas extra sobre las iniales con estos datos agrupados para poder generar tablas dinámicas y gráficos.   

#### X.2.X Análisis de las valoraciones
LOS DATOS FALTANTES TIENEN UN PATRÓN: EL AÑO DE PUBLICACIÓN DE LOS VIDEOUJEGOS (Incapacidad de feedback de valoraciones, menos desarrollo interno de los videoguegos y no había clasificación por edades)

### X.3 Dashboard

Pondría un mapa pero como hay una categoría que es 'otros países' no tiene sentido asociarla a ningun espacio concreto y puede generar confusiones. 

Decir que no he usado % porque creo que es mejor verlo en cifras tal cual

_Big numbers: Juego más vendido, Publicador más vendido, plataforma más vendida...  - elementos más importantes - interactividad confiltros y macros._

### X.4 Informe explicativo del análisis 
Lo voy generando paso a paso pero le voy una vuelta al final sobre las conclusiones y eso

## Informe final - Conclusiones  
Advertencia inicial sobre los datos faltantes 

