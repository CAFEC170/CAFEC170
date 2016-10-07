---
layout: post
title:  "Python, pip y sus versiones (Un dolor de cabeza)"
categories: pip python solucion
---

Tanto en MacOS como en Ubuntu o cualquier distro Unix-based, tener varias versiones de Python te da dolor de cabeza, cuando no sabes como administrarlas. Y como si no fuera suficiente, además MacOS tiene su propia versión de Python con la cual supuestamente no te podes meter, sino se rompe todo.  

Dejo dos soluciones a dos problemas que tuve, que estan relacionados entre si.

## Mi situación ##
Necesitaba importar la libreria de python [**hyper**](http://python-hyper.org/projects/h2/en/stable/index.html) para poder hacer un servidor y cliente usando HTTP/2. Es de las pocas, por no decir la única, que provee esa funcionalidad en Python.  

Empecé a seguir el tutorial que en la documentación de la librería sobre como crear un propio servidor HTTP/2.  
El problema surgió cuando copié el código del tutorial que hay en la documentación. El mismo explican como crear un servidor básico. Yo intenté ejecutarlo y me salieron errores que supuestamente no debería salir.  

## Problema \#1 ##
**ProtocolError: Received pseudo-header field out of sequence: b':status'**  
No tengo idea exactamente porque salta el error, pero despues de unos dias de frustración y a las cansadas, me enteré de que no me funcionaba porque estaba usando python2.7 para levantar el servidor. Y como yo instalé **el cli de hyper** (que se usa como cliente en el tutorial) usando pip (packet manager de python) de python2.7, me daba problemas.

### Solución ###

Tanto en Ubuntu como en Mac OS la solución es la misma, desinstalar el cli de **hyper** usando pip2 (de python 2) y volver a instalar hyper usando pip3 (de python 3).

#### Ubuntu ####
Haciendo `sudo pip2 uninstall hyper` ya basta para quitarlo del sistema y a continuacion lo volvemos a instalar pero con **pip3** haciendo `sudo pip3 install hyper`.  
Si no llegan a tener pip3 lo pueden instalar con `sudo apt-get install python3-pip`.


#### MacOS ####
Acá es un poco mas complicada la cosa, y la verdad me llevó pila de tiempo lograr resolverlo.  
Desinstalar e instalar hyper usando pip2 y pip3 respectivamente, es identico a Ubuntu, el problema surge cuando **no tenemos pip3 instalado y usamos macports como gestor de paquetes**. Instalar pip3 usando homebrew (otro gestor de paquetes muy usado para MacOS) es mucho mas simple, porque tiene una version de python3 que viene con pip3 incluido, no es el caso de macports, o al menos no fue mi caso.  

Pero yo no puedo usar homebrew, porque tengo macports desde hace mucho tiempo y he instalado muchas cosas con el. Y al mismo tiempo no pueden convivir en el sistema homebrew con macports.   

Así que  desinstalar macports e instalar homebrew no es una alternativa, entoncesme las tengo que arreglar sin usar homebrew.

La manera que encontré de instalar pip3, fue descargando el script [`get-pip.py](https://pip.pypa.io/en/stable/installing/) que supuestamente te instala pip como sea.  
Se instala facilmente haciendo `sudo python3 get-pip.py`.  

Todo muy lindo hasta acá pero ahora aparece el Problema \#2.

## Problema \#2 ##
**Bueno, y ahora donde está pip3? No lo acabo de instalar? Por qué no aparece?**   
No se si fue mi caso particular o pasa siempre, pero despues de instalar pip3, aparentemente aún no podia usar pip3 desde la terminal.  
En una de las varias cosas que hice para ver que estaba pasando, fue volver a ejecutar el script `get-pip.py` y me respondió con esto:

>Requirement already up-to-date: pip in /opt/local/Library/Frameworks/Python.framework/Versions/3.5/lib/python3.5/site-packages

Y al entrar a `/opt/local/Library/Frameworks/Python.framework/Versions/3.5/bin/` me encuentro con el desaparecido `pip3`.  
Lo que hago para poder invocarlo desde la terminal, es crear un soft link en el directorio `/usr/local/bin/` que apunte a donde está pip3 de la siguiente manera:
    
    ln -s /usr/local/bin/pip3 /opt/local/Library/Frameworks/Python.framework/Versions/3.5/bin/pip3

Y ahora si, ya puedo finalmente tener pip3 instalado y accesible desde la terminal.