.. _doc_simple_2d_game:

Juego 2D Simple
==============

Pong
~~~~

En este sencillo tutorial, se creará juego básico de Pong. Hay un
montón de ejemplos más complejos que se pueden descargar desde el sitio
oficial de Godot, pero esto debería servir como introducción a la
funcionalidad básica para juegos 2D.

Assets (activos)
~~~~~~

Algunos assets son necesarios para este tutorial:
:download:`pong_assets.zip </files/pong_assets.zip>`.

Configuración de la escena
~~~~~~~~~~~~~~~~~~~~~~~~~~

Para recordar viejos tiempos, el juego tendrá una resolución de
640x400 pixels. Esto se puede configurar en Configuración de
Proyecto (ve :ref:`doc_scenes_and_nodes-configuring_the_project`).
El color de fondo debe ajustarse a negro.

.. image:: /img/clearcolor.png

Crea un nodo :ref:`class_Node2D`como raíz del proyecto. Node2D es el
tipo base para el motor 2D. Luego de esto, agrega algunos sprites
(:ref:`class_Sprite`node) y ajusta cada uno a su textura
correspondiente. El diseño de la escena final debe verse similar a
esto (nota: ¡la pelota está en el medio!):

.. image:: /img/pong_layout.png

El árbol de escena, entonces, luce similar a esto:

.. image:: /img/pong_nodes.png

Guarda la escena como "pong.scn" y ajusta la escena principal en las
propiedades del proyecto.

.. _doc_simple_2d_game-input_actions_setup:

Configuración de acciones de entrada
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Hay tantos métodos de entrada para videojuegos... Teclado, Pad,
Mouse, Pantalla táctil (Multitouch). Pero esto es pong. El único
control que importa es que los pads suban y bajen.

Manejar todos los posibles métodos de entrada puede ser muy
frustrante y tomar un montón de código. El hecho de que la mayoría
de los juegos permiten personalizar los controles hacen que este
problema empeore. Para esto, Godot creó las "Acciones de Entrada".
Una acción se define, luego se agregan métodos de entrada que
disparan esa acción.

Abre el diálogo de propiedades de proyecto nuevamente, pero esta
vez ve a la pestaña "Mapa de entradas".

Allí, agrega 4 acciones:
``izq_mover_arriba``, ``izq_mover_abajo``, ``der_mover_arriba``, ``der_mover_abajo``.
Asigna las teclas que deseas. A/Z (para el jugador de la izquierda)
y Flecha arriba/Flecha abajo (para el jugador de la derecha) como
teclas debería funcionar en la mayoría de los casos.

.. image:: /img/inputmap.png

Script
~~~~~~

Crea un script para el nodo raíz de la escena y ábrelo (como se explica
en :ref:`doc_scripting-adding_a_script`). El script heredara Node2D:

::

    extends Node2D

    func _ready():
        pass

En el constructor, se harán 2 cosas. Primero, habilitar el
procesamiento y, segundo, guardar algunos valores útiles. Esos
valores son las dimensiones de la pantalla y el pad:

::

    extends Node2D

    var pantalla_tamano
    var pad_tamano

    func _ready():
        pantalla_tamano = get_viewport_rect().size
        pad_tamano = get_node("izquierda").get_texture().get_size()
        set_process(true)

Luego, se agregarán algunas variables usadas para el procesamiento 
dentro del juego:

::

    #velocidad de la bola (en pixeles/segundo)
    var bola_velocidad = 80

    #dirección de la bola (vector normal)
    var direccion = Vector2(-1, 0)

    #constante para la velocidad de los pads (también en
    # pixeles/segundo)

    const PAD_VELOCIDAD = 150

Finalmente, la función de procesamiento:

::

    func _process(delta):

Toma algunos valores útiles para computar. La primera es la posición
de la bola (desde el nodo), la segunda es el rectángulo (``Rect2``) para
cada uno de los pads. Los sprites tienen sus texturas centradas por
defecto, por lo que un pequeño ajuste de ``pad_size / 2`` debe ser
agregado.

::

        var bola_posicion = get_node("bola").get_pos()
        var rect_izq = Rect2( get_node("izquierda").get_pos() - pad_tamano/2,pad_tamano)
        var rect_der = Rect2 ( get_node("derecha").get_pos() - pad_tamano/2,pad_tamano)

Debido a que se ha obtenido la posición de la bola, integrarla debería
ser simple:

::

        bola_posicion += direccion * bola_velocidad * delta

Luego, ahora que la bola tiene una nueva posición, debería ser probada
contra todo. Primero, el piso y el techo:

::

        if ( (bola_posicion.y < 0 and direccion.y < 0) or (bola_posicion.y > pantalla_tamano.y and direccion.y > 0)):
            direccion.y = -direccion.y

Si se tocó uno de los pads, se cambia la dirección y se incrementa la velocidad
un poco


::

        if ( (rect_izq.has_point(posicion_bola) and direccion.x < 0) or (rect_der.has_point(bola_posicion) and direccion.x > 0)):
            direccion.x = -direccion.x
            bola_velocidad *= 1.1
            direccion.y = randf() * 2.0 - 1
            direccion = direccion.normalized()

Si la bola sale de la pantalla, el juego termina. Luego se reinicia:

::

        if (bola_posicion.x < 0 or bola_posicion.x > pantalla_tamano.x):
            bola_posicion = pantalla_tamano * 0.5  # la bola va al centro de la pantalla
            bola_velocidad = 80
            direccion = Vector2 (-1, 0)

Una vez que todo fue hecho con la bola, el nodo es actualizado con la
nueva posición:

::

        get_node("bola").set_pos(bola_posicion)

Solo se actualizan los pads de acuerdo a la entrada del jugador. La clase
Input es muy útil aquí:

::

        #mover pad izquierdo
        var izq_posicion = get_node("izquierda").get_pos()

        if (izq_posicion.y > 0 and Input.is_action_pressed("izq_mover_arriba")):
            izq_posicion.y += -PAD_SPEED * delta
        if (izq_posicion.y < pantalla_tamano.y and Input.is_action_pressed("izq_mover_abajo")):
            izq_posicion.y += PAD_SPEED * delta

        get node("izquierda").set_pos(izq_posicion)

        #mover pad derecho
        var der_posicion = get_node("derecha").get_pos()

        if (der_posicion.y > 0 and Input.is_action_pressed("der_mover_arriba"))
            der_posicion.y += -PAD_SPEED * delta
        if (der_posicion.y < pantalla_tamano.y and Input.is_action_pressed("der_mover_abajo")):
            der_posicion.y += PAD_SPEED * delta

        get_node("derecha").set_pos(der_posicion)

¡Y eso es todo! Se escribió un simple Pong con unas pocas líneas de código.
