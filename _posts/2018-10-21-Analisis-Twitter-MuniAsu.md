---
layout: post
title: Utilizando Twitter para Monitorear los Reclamos de la Ciudadanía (1)
categories: data visualization
tags: 'data_visualization, seaborn, matplotlib, paraguay, asuncion, nlp'
published: true
---

Desde hace ya unos años, las **redes sociales se volvieron el medio de comunicación
preferido de los ciudadanos para realizar reclamos** relacionados a la provisión de servicios públicos e infraestructura (electricidad, agua potable, recolección de basura, reportes de baches, etc.)

![bache.jpeg]({{site.baseurl}}/images/2018-10-21-Analisis-Twitter-MuniAsu/bache.jpeg){: .center-image }
*([Fuente](http://www.lavozdigital.com.py/noticia.php?id=10658&id_categoria=9){:target="_blank"})*

Si bien esto produjo un avance importante en la comunicación ciudadanía-autoridades, el
exceso y la velocidad de generación de la información impide tener un análisis certero de los reclamos como para reaccionar de manera eficaz, entender la causa raíz y prevenir futuros eventos.

Entender esto nos ayudaría por ejemplo a responder las siguientes preguntas:
- ¿Cuáles son los _reclamos más frecuentes_ realizados por los ciudadanos?
- ¿Cómo varía la _intensidad de estos reclamos en el tiempo_?
- ¿Cuál es el _sentimiento de las publicaciones_ realizadas por los ciudadanos hacia las autoridades?

### Alcance

Con el objetivo de lograr un entendimiento más profundo que nos permita responder a
estas y otras preguntas, utilizamos los tweets o publicaciones realizadas en Twitter donde se mencionan a **la Municipalidad de Asunción** ([@AsuncionMuni](https://twitter.com/AsuncionMuni){:target="_blank"}) y al **Intendente** ([@FerreiroMario1](https://twitter.com/Ferreiromario1){:target="_blank"}).  

Twitter provee una interfaz de aplicación (API) que permite extraer publicaciones en tiempo real o de los últimos 7 días. Si bien existen ciertas limitaciones en cuanto a la cantidad de tweets que se pueden obtener y la historia en la versión gratuita, hay alternativas para obtener publicaciones más antiguas, como [Get Old Tweets (GOT)](https://bit.ly/2pm3LlI){:target="_blank"}. En el caso de este análisis, utilizamos la librería GOT para **recolectar 70.000 tweets desde Octubre, 2017 hasta Octubre, 2018** con menciones a las autoridades.

En esta primera parte no entraré en detalles técnicos de la implementación, con el objetivo de hacer amena la lectura a quienes no tengan conocimientos de programación. La idea es incluir un paso a paso en la segunda parte.

### Nube de Palabras (_Word Cloud_)

![Nube de Palabras]({{site.baseurl}}/images/2018-10-21-Analisis-Twitter-MuniAsu/word_cloud_2.png){: .center-image }

Las nubes de palabras o _word clouds_ permiten visualizar las palabras más frecuentes de un texto utilizando el tamaño para representar la frecuencia o importancia. En este caso, las palabras se extraen de los últimos 2000 tweets publicados hasta la fecha donde se mencionan a la Intendencia de Asunción.

Podemos observar que palabras como **calle**, **zona** y **bache** son las más mencionadas en las publicaciones. También aparecen con menor destaque **favor**, **basura**, **gente** y **gracias**. Esto puede indicar interacciones frecuentes entre los ciudadanos y las autoridades, donde los ciudadanos realizan reclamos (por _favor_) y las autoridades agradecen (gracias) las informaciones. 

#### favor

> _Urgente por **favor** agente de tránsito desde las 06:00 horas frente al Botánico_

#### gracias

> _Buenas tardes, **gracias** por indicarnos en la brevedad posible los compañeros estarán por el lugar. Saludos!_

### Evolución de Reclamos por Categoría

Con el objetivo de entender la evolución de reclamos de los temas más críticos, procedemos a la clasificación de los tweets en las siguientes categorias:

- **Bache**
- **Basura**
- **Inundaciones**
- **Dengue**

Para cada categoría se define una lista de palabras relacionadas utilizando [expresiones regulares](https://platzi.com/blog/expresiones-regulares-python/){:target="_blank"} para identificar patrones. Por ejemplo, en el caso de basura se incluye _basura*, vertedero*, recicla*, desecho*, tóxico*, escombro*_. Los asteriscos indican que luego de la palabra pueden seguir 0 o más caracteres (ej. **recicla**do, **recicla**je, **recicla**mos). 

Existen  otras formas más avanzadas de clasificar tweets realizando anotaciones manuales y utilizando esto para entrenar modelos supervisados de Machine Learning. Sin embargo, para esta primera parte del análisis las expresiones regulares hacen un muy buen trabajo.

![Temas Criticos por Dia]({{site.baseurl}}/images/2018-10-21-Analisis-Twitter-MuniAsu/historico_menciones_new.png){: .center-image }

Observando el gráfico de arriba podemos argumentar lo siguiente:

- El pico de menciones de **dengue** ocurre en Marzo de 2018 que coincide con la [epidemia de la enfermedad](http://www.abc.com.py/nacionales/aprueban-emergencia-por-dengue-1681564.html){:target="_blank"}. 
- En el mes de la epidemia, existe un alta correlación de menciones de **basura** y **dengue**.
- El pico de menciones de **inundaciones** ocurre en Octubre de 2018 y coincide con las [lluvias intensas que dejaron raudales](http://www.abc.com.py/nacionales/raudales-causan-estragos-durante-tormenta-1747540.html){:target="_blank"} y ocasionaron destrozos.
- Entre Julio y Agosto de 2018 se observan picos de menciones de **baches**, probablemente relacionados a la aparición de baches en zonas muy concurridas.

### Análisis de Sentimiento de las Publicaciones

El análisis de sentimiento es una técnica que permite asignar un valor numérico o _score_ a un texto de acuerdo al contenido. Un score alto indica un contenido con sentimiento positivo y un score bajo, con sentimiento negativo. 

Abajo vemos dos ejemplos:

#### positivo (score: 0.87)
> _Visita del Presidente @ MaritoAbdo al intendente @ Ferreiromario1 . Me llama gratamente la atención, la cercanía y sencillez del presidente._ 

#### negativo (score: 0.002)
> _inútil, ahora recapaste la bicisenda jajaja, un asco es asuncion, una vergüenza zurdito, hace algo haragán_

En este análisis se utiliza un modelo desarrollado por [Elliot Hofman](https://github.com/aylliote/senti-py){:target="_blank"} que asigna _scores_ de sentimiento a los textos de acuerdo al contenido. 

![Historico de Sentimiento de Tweets]({{site.baseurl}}/images/2018-10-21-Analisis-Twitter-MuniAsu/historico_sentimiento.png){: .center-image }

La gráfica muestra el _score promedio del sentimiento_ de los tweets en el último año. Vemos que los promedios diarios oscilan entre 0.25 y 0.50, lo cual indica que la mayoría de las publicaciones contienen mensajes principalmente negativos. 

Si bien el score es una estimación numérica que se obtiene como resultado de un modelo estadístico y por esta razón no es siempre exacto (teniendo en cuenta, por ejemplo, las ironías), es una herramienta muy útil para medir tendencias y en este caso para evaluar el sentimiento general de los ciudadanos en el tiempo.

### Similitudes entre Categorías

En la gráfica de evolución de reclamos por día habíamos encontrado que _existe cierta correlación entre las publicaciones que hablan de basura y de dengue, sobre todo en época de epidemias_.

Esto puede indicar que las publicaciones que hablan de dengue, también mencionan en muchos casos a la basura. Podemos validar esta hipótesis con una gráfica denominada [Scatter Text](https://github.com/JasonKessler/scattertext){:target="_blank"}. 

![scatter_text_new.png]({{site.baseurl}}/images/2018-10-21-Analisis-Twitter-MuniAsu/scatter_text_new.png){: .center-image }

El eje horizontal indica la frecuencia de ocurrencia de palabras en publicaciones de dengue (más frecuentes a la derecha). Por otro lado, el eje vertical hace lo mismo publicaciones de basura (más frecuentes arriba). 

En el extremo superior derecho vemos que se encuentra la palabra basura. Es decir, **basura es una palabra frecuentemente utilizada tanto en publicaciones de dengue como de basura**. Sin embargo, la palabra criadero aparece frecuentemente en publicaciones de dengue pero muy poco en publicaciones de basura. 

### Conclusión y Uso

En esta primera parte realizamos un **análisis exploratorio de las publicaciones
realizadas en Twitter con menciones a la Municipalidad de Asunción y a su Intendente**. Encontramos que los temas que más aquejan a los ciudadanos están relacionados al estado de las calles, los servicios de recolección de basura, las inundaciones en épocas de lluvia y las epidemas de enfermedades como el dengue.

Si bien estos son temas conocidos, el análisis puede servir de herramienta tanto a la ciudadanía como a las autoridades para: 
- Medir la importancia que se da a estas cuestiones en el tiempo
- Evaluar la gestión de la Municipalidad en las diferentes áreas
- Priorizar acciones de acuerdo a la intensidad de las publicaciones

En la próxima entrega, nos enfocaremos en los **detalles técnicos de la implementación** de tal forma a compartir las técnicas utilizadas para aquellos interesados en aplicar las mismas a diferentes problemas de análisis textuales.
