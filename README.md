# Crear una nube de palabras en R
## Minería de textos y fundamentos de las nubes de palabras en R en 5 simples pasos

Como ya sabrán, una **nube de palabras** (o **word cloud** _nube de text_ o **word tag** _nube de etiquetas_) es una representación visual de datos de texto basada en la minería de textos para encontrar las palabras más utilizadas en un texto. El procedimiento para generar una nube de palabras utiliza el software R. 

El procedimiento de creación de nubes de palabras es muy sencillo en R si se conocen los diferentes pasos a seguir. El paquete de minería de textos (tm _text mining_) y el paquete generador de nubes de palabras (wordcloud) están disponibles en R para ayudarnos a analizar los textos y visualizar rápidamente las palabras clave como una nube de palabras.

## 3 razones por las que debería usar nubes de palabras para presentar sus datos de texto
1. Las nubes de palabras añaden **simplicidad y claridad**. Las palabras clave más utilizadas destacan mejor en una nube de palabras
2. Las nubes de palabras son una **potente herramienta de comunicación**. Son fáciles de entender, de compartir y tienen un impacto
3. Las nubes de palabras son **más atractivas visualmente que los datos** de una tabla. 

## ¿Quién usa las nubes de palabras?
- Investigadores: para la presentación de datos cualitativos.
- Comercializadores (Marketing): para destacar las necesidades de los clientes.
- Educadores: para apoyar las cuestiones esenciales.
- Políticos y periodistas.
- Social Media: para recoger, analizar y compartir los sentimientos de los usuarios.


## Los 5 pasos principales para crear nubes de palabras en R

### Paso 1: Crear un archivo de texto
En los siguientes ejemplos, procesaré el discruso **"I have a dream"** de Martin Luther King, pero puedes usar el texto que quieras:

- Copia y pega el texto en un archivo de texto simple (por ejemplo : ml.txt)
- Guarda el archivo

> :warning: Ten en cuenta que el texto debe guardarse en un formato de archivo de texto simple (.txt) usando tu editor de texto favorito.

### Paso 2: Instalar y cargar los paquetes necesarios
Escriba el código R a continuación, para instalar y cargar los paquetes necesarios:

~~~r
# Install
install.packages("tm")  # for text mining"
install.packages("SnowballC") # for text stemming
install.packages("wordcloud") # word-cloud generator 
install.packages("RColorBrewer") # color palettes
# Load
library("tm")
library("SnowballC")
library("wordcloud")
library("RColorBrewer")
~~~

### Paso 3 : Minería de textos
#### Cargar el texto
El texto se carga usando la función **Corpus()** del paquete de minería de textos (tm). Corpus es una lista de un documento (en nuestro caso, sólo tenemos un documento).

1. **Comenzamos importando el archivo de texto creado en el paso 1**
Para importar el archivo guardado localmente en su computadora, escriba el siguiente código R. Se le pedirá que elija el archivo de texto de forma interactiva.

`text <- readLines(file.choose())`

Aparecerá una ventana de tu buscador para que indiques dónde está el texto. Selecciona el documento. 

También puede importarlo de una página web. Para ello, deberás transformarlo en formato texto. Entonces cambia el código: 

```r
# Read the text file from internet
filePath <- "http://www.sthda.com/sthda/RDoc/example-files/martin-luther-king-i-have-a-dream-speech.txt"
text <- readLines(filePath)
```
2. **Carga los datos como un cuerpo de texto** (corpus)

```r
# Load the data as a corpus
docs <- Corpus(VectorSource(text))
````

> :warning: La función VectorSource() crea un corpus de vectores de carácter

3. **Inspeccionar el contenido del documento.**

`inspect(docs)`


#### Transformación del texto

Si queremos que la nueb de palabras tengo un solo color, en función de la persona que ha realizado el discurso. Vamos a determinar el código de color en hexadecimal: 

```r
#poner el codigo de color en hexadecimal

PSOE <- '#a31313'
ciudadanos <- '#b56525'
podemos <- '#7d4d7d'
pp <- '#4484b4'
vox <- '#6e9e4e'
```

Marcamos el que queremos: 

`colors <- ciudadanos`

También seleccionaremos la funete que queramos usar: 

```r
#tipo de fuente a usar
font <- "Arial"
```
La transformación se realiza mediante la función **tm_map()** para sustituir, por ejemplo, los caracteres especiales del texto.

Reemplazando "/", "@" y "|" por un espacio:

```r
toSpace <- content_transformer(function (x , pattern ) gsub(pattern, " ", x))
docs <- tm_map(docs, toSpace, "/")
docs <- tm_map(docs, toSpace, "@")
docs <- tm_map(docs, toSpace, "\\|")
```

#### Limpiando el texto
la **función tm_map()* se utiliza para eliminar los espacios en blanco innecesarios, para convertir el texto en minúsculas, para eliminar las palabras comunes como "el", "nosotros".

El valor de información de las "palabras clave" está cerca de cero debido al hecho de que son tan comunes en un idioma. La eliminación de este tipo de palabras es útil antes de realizar más análisis. Para las 'palabras clave', los idiomas soportados son el danés, el holandés, el inglés, el finlandés, el francés, el alemán, el húngaro, el italiano, el noruego, el portugués, el ruso, el español y el sueco. Los nombres de los idiomas son sensibles a las mayúsculas y minúsculas.

:white_check_mark: También os mostraré cómo hacer una lista de palabras clave propia para eliminar del texto.

También podrías eliminar los números y la puntuación con los argumentos **removeNumbers** y **removePunctuation**.

Otro importante paso de preprocesamiento es hacer un texto **text stemming** que reduzca las palabras a su forma raíz. En otras palabras, este proceso elimina los sufijos de las palabras para simplificar y obtener el origen común. Por ejemplo, un proceso de stemming reduce las palabras "moving", "moved" y "movement" a la palabra raíz, "move".

:warning: Tengan en cuenta que, el texto que se deriva requiere el paquete 'SnowballC’'.

The R code below can be used to clean your text :

```r
# Convert the text to lower case
docs <- tm_map(docs, content_transformer(tolower))

# Remove numbers
docs <- tm_map(docs, removeNumbers)

# Remove english common stopwords for spanish "english" -> "spanish"
docs <- tm_map(docs, removeWords, stopwords("spanish"))

# Remove your own stop word specify your stopwords as a character vector desired words
docs <- tm_map(docs, removeWords, c("parece", "solo", "une", "significa", "hablando", "quien", "sino", "pagan", "limitar", "gracias", "viendo", "general", "oiga", "hacía", "fin", "incluso", "quiera", "quieren", "mismo", "didiendo", "planteado", "hizo", "dejó", "decía", "dijo", "final", "gustaría", "pagó", "viene", "sabe", "lleva", "habla", "nivel", "lado", "digo", "pueden", "miren", "altura", "así", "ponga", "volver", "mejor", "luego", "cosa", "gusta", "pido", "igual", "cómo", "mirar", "cada", "ahora", "voy", "pasa", "dicho", "vivir", "nunca", "toda", "mire", "poquito", "suprimir", "último", "dicho", "meses", "saben", "media", "saber", "acabar", "seguir", "luchar", "haciendo", "primera", "poder", "año", "cosas", "bueno", "león", "ver", "gran", "¿", "hoy", "precisamente", "manera", "vez", "creo", "sólo", "vez", "momento", "van", "dar", "ser", "bien", "día", "hace", "hacerlo", "tipo", "entrar","hablar", "sé", "además", "tampoco", "queremos", "quiero", "quiere", "puede", "lugar", "puede", "quiere", "señor","aquí", "decir", "unas", "unos", "nada", "pues", "también", "ante", "con", "contra", "de", "desde", "para", "por", "según", "sin", "trans", "y", "la", "el", "lo", "mi", "tu", "su", "vuestro", "nuestro", "ellos", "aquél", "aquella","aquellos", "aquellas", "señor", "hecho", "hacer", "ustedes", "usted","claro","uno", "dos","tres","cuatro", "cinco", "seis","iba", "iban", "dice", "dicen", "tener", "quiero", "haber", "poner", "más", "menos", "mayor", "menor", "", "¿", "años", "crear", "ejemplo", "nuevo", "crear", "todas", "todos", "nuevo", "mínimo", "máximo", "mayor", "menor", "primero", "segundo", "tercero", "cuarto", "quinto", "partir", "vamos", "muchas")) 

# Remove punctuations
docs <- tm_map(docs, removePunctuation)

# Eliminate extra white spaces
docs <- tm_map(docs, stripWhitespace)
```
### Paso 4: Construir una matriz de términos y documentos
La matriz de documentos es una tabla que contiene la frecuencia de las palabras. Los nombres de las columnas son palabras y los nombres de las filas son documentos. La función _TermDocumentMatrix()_ del paquete de **minería de textos** puede utilizarse de la siguiente manera :

```
# Text stemming
#docs <- tm_map(docs, stemDocument)

dtm <- TermDocumentMatrix(docs)

m <- as.matrix(dtm)
v <- sort(rowSums(m),decreasing=TRUE)
d <- data.frame(word = names(v),freq=v)
head(d, 10)
```

Grabamos el archivo para asegurarnos que estamos visualizando la nueb de palabras desde el texto limpio. 
 `write_csv(cleaned, 'barplot.csv') #Grabamos el archivo`
 
### Paso 5: Generar la nube de palabras
Antes de nada, tenemos que decidir algunos argumentos importantes, por ejemplo:

- La **frecuencia de repetición** de las palabras que se visualicen `min.freq=x`
- El número de palabras máximo que queremos que aparezca en nuestra nube de palabras con `max.words=X``

La importancia de las palabras se puede ilustrar como una **nube de palabras**:


```r
#set.seed(1234)

#with colour palete 
#wordcloud(words = d$word, freq = d$freq, min.freq = 1,
 #         max.words=200, random.order=FALSE, rot.per=0.35, 
 #         colors=brewer.pal(8, "Dark2"))
 #         colors=brewer.pal(8, "Dark2"))
 ```
 Si lo preferimos con la paleta de color gris:
 
 ```r
 #with gray palete
#png("Descargas/cloud_grey.png", width=750, height=500)
#wordcloud(words = d$word, freq = d$freq, min.freq = 1,
 #         max.words=200, random.order=FALSE, rot.per=0.35, 
 #         colors= gray.colors(10, start = 0.3, end = 0.9, gamma = 2.2, alpha = NULL))
 #         colors=brewer.pal(8, "Dark2"))
  ```
    
 Si queremos la tabla definida por los colores hexagesimales que hemos deifnido al principio, podemos usar este código:

```r
#with defined hex colour
#png("Desktop/cloud_hex_color.png", width=750, height=500)
wordcloud(words = d$word, freq = d$freq, min.freq = 1,
          max.words=50, random.order=FALSE, rot.per=0.35, 
          colors= colors, family = font, font = 1)
          colors= colors, family = font, font = 1)
```

## Vamos más allá
### Explorar los términos frecuentes y sus asociaciones
Puede echar un vistazo a los términos frecuentes en la matriz de términos-documentos como sigue. En el ejemplo siguiente queremos encontrar palabras que se producen al menos cuatro veces :
`findFreqTerms(dtm, lowfreq = 4)`

Puede analizar la asociación entre términos frecuentes (es decir, términos que se correlacionan) usando la función **findAssocs()**. El código R de abajo identifica qué palabras están asociadas con "libertad" en el discurso:

`findAssocs(dtm, terms = "freedom", corlimit = 0.3)`

### La tabla de frecuencias de las palabras 
`head(d, 10)`


Además, podemos crear un gráfico de barras que contenga las palabras más usadas por orden. Deberemos cambiar el nombre del .png. El código será: 

```r
#barplot hex color
png("Descargas/bar_hex_color.png", width=750, height=500)
barplot(d[1:10,]$freq, las = 2, names.arg = d[1:10,]$word,
        col = c(colors), main ="Palabras más repetidas del candidato durante el debate",
        ylab = "Número de veces", family = font, font = 1, font.lab = 2, font.axis = 2, ylim = c(0 , 50))

dev.off()
```
:heart_eyes: ¿Te ha gustado este artículo? Te agradecería mucho que ayudaras a difundirlo enviándolo por correo electrónico a un amigo, o compartiéndolo en Twitter, Facebook o Linked In.

## ¡¡Gracias y por favor no olvides compartir y comentar abajo!!
