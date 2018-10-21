---
published: false
---
## Utilizando Twitter para Monitorear los Reclamos de la Ciudadanía (1)

### Problemática 

Desde hace ya unos años, las **redes sociales se volvieron el medio de comunicación
preferido de los ciudadanos para realizar reclamos** relacionados a la provisión de servicios públicos e infraestructura (electricidad, agua potable, recolección de basura, reportes de baches, etc.)

Si bien esto produjo un avance importante en la comunicación ciudadanía-autoridades, el
exceso y la velocidad de generación de la información impide tener un análisis certero de los reclamos como para reaccionar de manera eficaz, entender la causa raíz y prevenir futuros eventos.

Entender esto nos ayudaría por ejemplo a responder las siguientes preguntas:
• ¿Cuáles son los _reclamos más frecuentes_ realizados por los ciudadanos?
• ¿Cómo varía la _intensidad de estos reclamos en el tiempo_?
• ¿Cuál es el _sentimiento de las publicaciones_ realizadas por los ciudadanos hacia las autoridades?

### Alcance

Con el objetivo de lograr un entendimiento más profundo que nos permita responder a
estas y otras preguntas, utilizamos los tweets o publicaciones realizadas en Twitter donde se mencionan a **la Municipalidad de Asunción** ([@AsuncionMuni](https://twitter.com/AsuncionMuni)) y al **Intendente** ([@FerreiroMario1](https://twitter.com/Ferreiromario1)).  

Twitter provee una interfaz de aplicación (API) que permite extraer publicaciones en tiempo real o de los últimos 7 días. Si bien existen ciertas limitaciones en cuanto a la cantidad de tweets que se pueden obtener y la historia en la versión gratuita, hay alternativas para obtener publicaciones más antiguas, como [Get Old Tweets (GOT)](https://bit.ly/2pm3LlI). En el caso de este análisis utilizamos la librería GOT para **recolectar 70.000 tweets desde Octubre, 2017 hasta Octubre, 2018** con menciones a las autoridades, excluyendo las respuestas de los usuarios ([@AsuncionMuni](https://twitter.com/AsuncionMuni)) y ([@FerreiroMario1](https://twitter.com/Ferreiromario1)).

En esta primera parte no voy a entrar en detalles técnicos de la implementación, con el objetivo de hacer amena la lectura a quienes no tengan conocimientos de programación. La idea es incluir un paso a paso en la segunda parte.

### Nube de Palabras (_Word Cloud_)

![Nube de Palabras]({{site.baseurl}}/images/2018-10-21-Analisis-Twitter-MuniAsu/word_cloud_2.png)

Las nubes de palabras o _word clouds_ permiten visualizar las palabras más frecuentes de un texto utilizando el tamaño para representar la frecuencia o importancia. En este caso, las palabras se extraen de los últimos 2000 tweets publicados donde se mencionan a la Intendencia de Asunción.

Podemos observar que palabras como **calle**, **zona** y **bache** son las más mencionadas en las publicaciones. También aparecen con menor destaque **favor**, **basura**, **gente** y **gracias**. Esto puede indicar interacciones frecuentes entre los ciudadanos y las autoridades donde se realizan reclamos (favor) y se agradecen (gracias) las respuestas. 

### Evolución de Reclamos por Categoría

![Temas Criticos por Dia]({{site.baseurl}}/images/2018-10-21-Analisis-Twitter-MuniAsu/historico_menciones_new.png)




