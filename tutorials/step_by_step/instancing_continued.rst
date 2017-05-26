.. _doc_instancing_continued:

Instanciar (continuación)
=========================

Recapitulación
--------------

Instanciar tiene muchos usos. A simple vista, al instanciar tienes:

-  La habilidad de subdividir las escenas y hacerlas mas fáciles de
   administrar.
-  Una alternativa flexible a los prefabricados (y mucho mas poderosa
   dado que las instancias trabajan en varios niveles)
-  Una forma de diseñar flujos de juegos mas complejos o incluso UIs
   (interfaces de usuario) (Los elementos de UIs son nodos en Godot
   también)

Lenguaje de diseño
---------------

Pero el verdadero punto fuerte de instanciar escenas es que trabaja 
como un excelente lenguaje de diseño. Esto es básicamente lo que hace
especial a Godot y diferente a cualquier otro motor en existencia.
Todo el motor fue diseñado desde cero en torno a este concepto.

Cuando se hacen juegos con Godot, el enfoque recomendado es dejar a
un costado otros patrones de diseño como MVC o diagramas de
entidad-relación y empezar a pensar en juegos de una forma mas
natural. Comienza imaginando los elementos visibles en un juego, los
que pueden ser nombrados no solo por un programador sino por
cualquiera.

Por ejemplo, aquí esta como puede imaginarse un juego de disparo
simple:

.. image:: /img/shooter_instancing.png

Es bastante sencillo llegar a un diagrama como este para casi
cualquier tipo de juego. Solo anota los elementos que te vienen a la
cabeza, y luego las flechas que representan pertenencia.

Una vez que este diagrama existe, hacer el juego se trata de crear
una escena para cada uno de esos nodos, y usar instancias (ya sea por
código o desde el editor) para representar la pertenencia.

La mayoría del tiempo programando juegos (o software en general) se
usa para diseñar una arquitectura y adecuando los componentes del
juego a dicha arquitectura. Diseñar basado en escenas reemplaza esto
y vuelve el desarrollo mucho mas rápido y directo, permitiendo
concentrarse en el juego. El diseño basado en Escenas/Instancias es
extremadamente eficiente para ahorrar una gran parte de ese trabajo,
ya que la mayoría de los componentes diseñados se mapean directamente
a una escena. De esta forma, se precisa poco o nada de código de
arquitectura.

El siguiente es un ejemplo mas complejo, un juego de mundo abierto
con un montón de assets(activos) y partes que interactúan.

.. image:: /img/openworld_instancing.png

Crea algunas habitaciones con muebles, luego conectalos. Crea una
casa mas tarde, y usa esas habitaciones como su interior.

La casa puede ser parte de la ciudadela, que tiene muchas casas.
Finalmente la ciudadela puede ser puesta en el terreno del mapa
del mundo. También agrega guardias y otros NPCs(personajes no jugador)
a la ciudadela, creando previamente sus escenas.

Con Godot, los juegos pueden crecer tan rápido como se desee, ya que
se trata de crear mas escenas e instanciarlas. El editor UI(interfaz
de usuario) también esta diseñado para ser operado por personas que
no son programadores, por lo que un equipo usual de desarrollo
consiste de artistas 2D o 3D, diseñadores de niveles, diseñadores de
juegos, animadores, etc todos trabajando en la interfaz del editor.

¡Sobrecarga de información!
--------------------------

No te preocupes demasiado, la parte importante de este tutorial es
crear la conciencia de como las escenas e instanciar son usados en
la vida real. La mejor forma de entender todo esto es hacer algunos
juegos.

Todo se volverá muy obvio cuando se pone en practica, entonces, ¡por
favor no te rasques la cabeza y ve al siguiente tutorial!
