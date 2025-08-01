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
Comienzo la transformación y limpieza de datos atendiendo en cada columna a los valores vacíos y únicos si estos son releavntes para poder identificar posibles anomalías o necesidad de transformación.

En primer lugar observo que las 2 últimas filas no contienen ningún dato, así que las elimino.

Además, me percato de que no hay ninguna columna con un identificador único para cada videojuego. La que en un primer lugar pudiera parecer algo similar es la del nombre de estos, sin embargo, este no cuenta con el mismo número de valores únicos que de filas (identificado gracias a las estadísticas de columna que ofrece google sheets). Me aseguro de que no se tratara de un error, comprobando que las frecuencias máximas eran de 9, y filtrando por este criterio observo que esto se debe a que hay videojuegos en diferentes consolas que pueden tener un mismo nombre, no se trata de un error. 

Decido generar una nueva columna llamda _ID_juego_ para tener un identificador único decada fila por si fuera necesario hacer uso de ello. Para comprobar también mediante esta columna que no haya datos duplicados, sin caer en el error que puede suponer buscar simplemente juegos con el mismo nombre, creo una columna que una valores que no puedan coincidir entre sí. Es decir, uno mediante la siguiente fórmula el nombre del juego, la plataforma o consola, el desarrollador y año de publicación.

```
=ARRAYFORMULA(SI(A2:A=""; ""; A2:A & "_" & B2:B & "_" & D2:D & "_" & C2:C)) 
```
ARRAYFORMULA: permite que la fórmula se aplique a toda la columna al ponerla sólo en la primera celda

SI: Evita generar identificadores si hay algún valor vacío en los seleccionados, que resultan imprescindibles (traducción de "IF" al usar la versión en español de sheets).

Gracias esto resulta una columna sin celdas vacías, con 16.719 filas y 16.717 valores únicos. Es decir, 2 valores repetidos. Gracias a las estadísticas de columna veo un valor con frecuencia 2, el cual filtro y al tener las mismas características mencionadas en la fórmula pero diferentes valoraciones y ventas, decido eliminar. 

```
**PONER EL RATING DE CRÍTICA Y USUARIOS EN EL MISMO FORMATO PARA PODER COMPARARLO !!!!!! **  
```

### X.2 Análisis descriptivo de los datos 

### X.3 Dashboard

### X.4 Informe explicativo del análisis 
