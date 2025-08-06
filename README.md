# Análisis de  nosequé, título.
Primer Proyecto EDA - Dashboard en Google Sheets
Primera línea para subir un primer cambio en el repo 


IMPORTANTE REALIZAR Y SEÑALAR QUE REALIZO EL EDA CON UN OBJETIVO PROPIO
Pinta que puede ser ver la valoracion de cada juego en realición a diferentes parámetros
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

Genero una nueva hoja _"Tablas Din 1"_ donde comienzo a generar tablas dinámicas donde relaciono las diferentes variables del set de datos. Junto a estas genero gráficos que me permiten comenzar a comprender de una forma visual las tendencias de los datos. Clasifico esas tablas de la siguiente forma:

#### X.2.1 Características de los juegos
Atiendo en primer lugar al  género, plataforma, año, clasificación, desarrollador y empresa que publica los juegos en función del conteo de estos. Así observo que:
-  la mayoría de juegos se publican en los años ____________. (rarogno esto eeeeee)


LOS DATOS FALTANTES TIENEN UN PATRÓN: EL AÑO DE PUBLICACIÓN DE LOS VIDEOUJEGOS (Incapacidad de feedback de valoraciones, menos desarrollo interno de los videoguegos y no había clasificación por edades)

### X.3 Dashboard

_Big numbers - elementos más importantes - interactividad confiltros y macros._

### X.4 Informe explicativo del análisis 
Lo voy generando paso a paso pero le voy una vuelta al final sobre las conclusiones y eso

## Informe final - Conclusiones  
Advertencia inicial sobre 