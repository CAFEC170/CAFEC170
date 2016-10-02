---
layout: post
title:  "Como crear un blog en Jekyll en 5 minutos (despues de muchos problemas)"
date:   2016-10-02 16:55 -0300
categories: jekyll blog
---

# Introducción

Inauguro este blog comentando los problemas que tuve para configurarlo. 

Como todo en la informatica, y en especial en estos tiempos donde hay mil utilidades para automatizar y simplificar los procesos, todo es "muy facil" hasta que surgen los pequeños e inevitables problemas, los cuales al mismo tiempo son parte de workflow normal del dia a dia de un informatico.  

Este blog está hosteado en [github](https://github.com/), usando [github pages](https://pages.github.com/), que es un servicio que github ofrece para poder publicar páginas de proyectos, páginas personales o blogs.  
Ademas, usa [Jekyll](http://jekyllrb.com/) sobre el cual dejo la descripción tomada de su sitio:

> Jekyll is a simple, blog-aware, static site generator. It takes a template  directory containing raw text files in various formats, runs it through a converter (like Markdown) and our Liquid renderer, and spits out a complete, ready-to-publish static website suitable for serving with your favorite web server. Jekyll also happens to be the engine behind GitHub Pages, which means you can use Jekyll to host your project’s page, blog, or website from GitHub’s servers for free.

Respecto a explicar los pasos para instalar, configurar y usar jekyll y tener el blog publicado en internet, no tiene sentido que repita lo mismo que está explicado en la [Documentación de Jekyll](http://jekyllrb.com/docs/home) y Github pages, por lo que en esta entrada lo que voy a hacer es comentar las soluciones a los problemas que me surgieron cuando seguí la documentación y el aprendizaje que obtuve de la experiencia.  
  
  
# Crear un blog en jekyll

Es "tan facil" como instalar todo lo necesario: ruby, gema jekyll, etc (explicado en la documentación) y luego ejecutar esta serie de comandos:

{% highlight bash %}
jekyll new blog 
cd myblog
bundle install
bundle exec jekyll serve
{% endhighlight %}

Con esto no solo creamos el directorio con todos los archivos listos para el blog, sinó que además el comando `jekyll serve` levanta un servidor web local con el blog para poder previsualizarlo, algo muy práctico al momento de escribir una nueva entrada y ver como se va a ver antes de pushearla al repositorio en github.

# Problemas y Soluciones #

### Problemas de rutas absolutas y relativas, dependiendo de la url base ###

Gracias a seguir las prácticas que menciona este articulo: <http://downtothewire.io/2015/08/15/configuring-jekyll-for-user-and-project-github-pages/> logré arreglar varios de los problemas que comento abajo.  

### Modificar layouts por defecto ###
Para hacer lo que el articulo mencionado arriba sugiere, y para corregir otros problemas que comento mas abajo, necesitaba modificar archivos de `layouts` e `includes` que vienen con el theme `minima` por defecto.  

Como en la estructura que viene se usan el layout `default` el cual hace uso de includes como `header`, mi idea para cambiar las rutas era copiar el contenido del los layouts e includes que necesitaba modificar, a un nuevo lugar en mi directorio y ahí realizarle las modificaciones necesarias.  

El problema es que yo no tenía idea en donde estaban guardados estos archivo, entonces lo primero que se me ocurrió fue ir al repositorio de Jekyll y buscar `_layouts` para ver si en algun lugar se hacía referencia a esta carpeta, no lo encontré exactamente ahí, pero si las [direcciones para llegar a el](https://github.com/jekyll/jekyll/blob/152fa877f6775dc6502e8020e4384cd24f0a0c8c/site/_docs/themes.md#overriding-theme-defaults). Además leyendo este link entendí el comportamiento de Jekyll: Si se usa un layout, primero lo busca en el directorio local y sino lo encuentra, hace uso de los por defecto, lo cual me viene perfecto para hacer lo que yo quiero.  

Entonces usando el comando `bundle show minima` (que menciona el link) obtuve la ubicación de donde estaba el theme `minima` y en ese directorio (en mi caso `/Library/Ruby/Gems/2.0.0/gems/minima-1.2.0`) encontré la carpeta `_layouts` y `_includes` y copié los archivos que necesitaba cambiar a mi directorio.  


### No carga los CSS ###
Inicialmente al levantar el blog con el comando `bundle exec jekyll serve` pude verlo en la url `localhost:4000`, pero por alguna extraña razón no cargaba los estilos CSS.  

Inspeccionando el archivo `index.html` que Jekyll genera, vi que los estilos estaban referenciandos hacia la url:`cafec170.github.io/css/main.css` y en el consola de Firefox me indicaba que no se encontró el recurso. Esto se debía a que en el archivo de configuración `_config.yml` yo setié la variable `url: "cafec170.github.io"`, lo cual está correcto! y es necesario para que sea publicado correctamente online en github pages.  
Pero... localmente la url donde levanta el blog no es `cafec170.github.io` sinó que es `localhost:4000`.  
Esto se resuelve automagicamente si hacemos uso de los consejos mencionados arriba sobre **rutas absolutas y relativas**.  
En particular, en el archivo `header.html` cambié la ruta del CSS.
Este archivo estaba dentro de la carpeta `_includes`, el cual es usado por el archivo `default.html` dentro de la carpeta `_templates`, un template que usa `index.html`.  
Como dije, es "todo muy facil" hasta que algo no funciona y hay que remangarse las manos. Lo bueno es que se aprende un montón.


### Las fechas están por defecto en inglés ###
Al menos por el momento Jekyll no ofrece alguna configuración especifica para que las fechas se muestren en otro idioma que no sea inglés.  
Si queremos las fechas en español podemos usar el siguiente código que saqué [de acá](https://www.snip2code.com/Snippet/366556/Mostrar-la-fecha-en-castellano-en-Jekyll):

{% highlight ruby %}{% raw %}
{% assign m = post.date | date: "%-m" %}
{{ post.date | date: "%-d de" }}
  {% case m %}
    {% when '1' %}enero
    {% when '2' %}febrero
    {% when '3' %}marzo
    {% when '4' %}abril
    {% when '5' %}mayo
    {% when '6' %}junio
    {% when '7' %}julio
    {% when '8' %}agosto
    {% when '9' %}septiembre
    {% when '10' %}octubre
    {% when '11' %}noviembre
    {% when '12' %}diciembre
  {% endcase %}
{{ post.date | date: "de %Y" }}
{{ post.date | date: " - %H:%M"}}
{% endraw %}{% endhighlight %}

este código lo podemos sustituir en todos los lugares en donde se usa: `{%raw%}{{ post.date | date: "%b %-d, %Y" }}{%endraw%}`, por ejemplo en el archivo `index.html` y tambien dentro de los archivos de template que me copié de los por defecto, como `post.html`.

> De paso les comento que para poder "escapar" tags de de **liquid template** en esta entrada, es decir código de la forma `{%raw%}{% tag %}{%endraw%}` o `{%raw%}{{ variable }}{%endraw%}` que normalmente es preprocesado, puesto que es parte del lenguaje de que jekyll usa, usé los tags especiales (que descubrí [gracias nuevamente a stackoverflow](http://stackoverflow.com/a/5866429/1710845)) `{%raw%}{%raw%}{%endraw%}` y `{{"{%endraw"}}%}` que si son colocados encerrando cualquier codigo de liquid template, se pueden omitir su procesamiento y permite mostrarlos.


### Muchos otros problemas ###
En realidad acá estoy contando la experiencia bastante resumida.  

Inicialmente intenté crear el blog basandome en templates de jekyll más avanzandos y completos con varias funcionalidades como: [hackcss](https://github.com/wemake-services/jekyll-theme-hackcss) y [lanyon](https://github.com/poole/lanyon).  
Hice un fork de ambos y de algunos otros, y siempre algún problema me surgía que no podia lograr tener el blog funcionando como yo quería (dependencias de gemas de ruby con distintas versiones a las que tenía instaladas, otros asuntos de archivos de configuraciones, el template no era tan orientado a blog y mas orientado a páginas web, etc).

# Resumen
La lección aprendida es que muchas veces es mejor arrancar por lo básico (que como se pudo ver por mi experiencia, ni siquiera es algo tan trivial) y despues de que uno domina eso y lo tiene configurado como uno quiere, construir y agregar encima.  

Y eso es lo que planeo hacer con este blog, por ahora tiene *casi* todo por defecto y a medida vaya agarrandole la mano lo voy a empezar a personalizar y agregarle más funcionalidades, gracias a la modularización que jekyll ofrece.  