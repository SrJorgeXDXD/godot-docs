.. _doc_gui_tutorial:

Tutorial GUI (Interfaz Gráfica de Usuario)
============

Introducción
~~~~~~~~~~~~

Si hay algo que la mayoría de los programadores realmente odian, es
programar interfaces gráficas de usuario (GUIs). Es aburrido, tedioso
y no ofrece retos. Varios aspectos hacen este problema peor como:

-  La alineación de los elementos de la UI (interfaz de usuario) es
   difícil (para que se vea justo como el diseñador quiere).
-  Las UIs deben cambiarse constantemente debido a los problemas de
   apariencia y usabilidad que aparecen durante el testing(prueba).
-  Manejar apropiadamente el cambio de tamaño de pantalla para
   diferentes resoluciones de pantallas.
-  Animar varios componentes de pantalla para hacerlo parecer menos
   estático.

La programación GUI es una de las principales causas de frustración
de los programadores. Durante el desarrollo de Godot (y previas
iteraciones del motor) se pusieron en práctiva varias técnicas y filosofías 
para el desarrollo UI, como un modo inmediato, contenedores, anclas, scripting, etc.
Esto fue hecho con el objetivo principal de reducir el estrés que los programadores
tienen que enfrentar cuando crean interfaces de usuario.

Al final, el sistema UI resultante en Godot es una solución eficiente
para este problema, y funciona mezclando juntos algunos enfoques.
Mientras que la curva de aprendizaje es un poco más pronunciada que
en otros conjuntos de herramientas, los desarrolladores pueden crear
interfaces de usuario complejas en muy poco tiempo, al compartir las
mismas herramientas con diseñadores y animadores.


Control
~~~~~~~

El nodo básico para elementos UI es :ref:`Control <class_Control>`
(a veces llamados "Widget" o "Caja" en otras herramientas). Cada
nodo que provee funcionalidad de interfaz de usuario desciende de
el.

Cuando los controles se ponen en el árbol de escena como hijos
de otro control, sus coordenadas (posición, tamaño) son siempre
relativas a sus padres. Esto aporta la base para editar interfaces
de usuario complejas rápido y de manera visual.

Entrada y dibujado
~~~~~~~~~~~~~~~~~~

Los controles reciben eventos de entrada a traves de la llamada
de retorno :ref:`Control._input_event() <class_Control__input_event>`.
Solo un control, el que tiene el foco, va a recibir los eventos
de teclado/joypad (ve :ref:`Control.set_focus_mode() <class_Control_set_focus_mode>`
y  :ref:`Control.grab_focus() <class_Control_grab_focus>`.)

Los eventos de movimiento de mouse son recibidos por el control que
esta directamente debajo del puntero de mouse. Cuando un control
recibe el evento de que se presiono un boton de mouse, todas los
eventos siguientes de movimiento son recibidos por el control
presionado hasta que el boton se suelta, aún si el puntero se mueve
fuera de los límites del control.

Como cualquier clase que hereda de :ref:`CanvasItem <class_CanvasItem>`
(Control lo hace), una llamada de retorno :ref:`CanvasItem._draw() <class_CanvasItem__draw>`
se recibe al principio y cada vez que el control deba ser
redibujado (los programadores deben llamar :ref:`CanvasItem.update() <class_CanvasItem_update>`
para poner en cola el CanvasItem para redibujar). Si el control no
está visible (otra propiedad CanvasItem), el control no recibe
ninguna entrada.

Sin embargo, el programador no necesita lidiar con el
dibujado y los eventos de entrada directamente cuando se construyen
UIs, (es mas útil cuando se crean controles personalizados). En su
lugar, los controles emiten diferente tipos de señales con información
contextual para cuando ocurre la acción. Por ejemplo, una :ref:`Button <class_Button>`
emite una señal "pressed", una :ref:`Slider <class_Slider>`emitirá un
"value_changed" cuando se arrastra, etc.

Mini tutorial de controles personalizados
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Antes de ir mas profundo, se creará un control personalizado 
para entender cómo funcionan los controles, ya que no son
tan complejos como parecen.

Aunque Godot viene con docenas de controles para
diferentes propósitos, a menudo sucede que es simplemente más
sencillo obtener la funcionalidad específica creando uno nuevo.

Para comenzar, crea una escena con un solo nodo. El nodo es del tipo
"Control" y tiene cierta área de la pantalla en el editor 2D, como
esto:

.. image:: /img/singlecontrol.png

Agregale un script a ese nodo, con el siguiente codigo:

::

    extends Control

    var pulsado=false

    func _draw():

        var r = Rect2( Vector2(), get_size() )
        if (pulsado):
            draw_rect(r, Color(1,0,0) )
        else:
            draw_rect(r, Color(0,0,1) )

    func _input_event(ev):

        if (ev.type==InputEvent.MOUSE_BUTTON and ev.pressed):
            pulsado=true
            update()

Luego corre la escena. Cuando el rectángulo es clickeado/pulsado, cambiará
de azul a rojo. Esa sinergia entre los eventos y el dibujo es
básicamente como funcionan internamente la mayoria de los controles.

.. image:: /img/ctrl_normal.png

.. image:: /img/ctrl_tapped.png

Complejidad de la UI
~~~~~~~~~~~~~

Como mencionamos antes, Godot incluye docenas de controles listos para
usarse en una interfaz. Esos controles estan divididos en dos
categorías. La primera es un pequeño grupo de controles que funcionan
bien para crear la mayoria de las interfaces de usuario. La segunda
(y la mayoria de los controles son de este tipo) estan destinadas a
interfaces de usuario complejas y el skinning(aplicar un forro) a
traves de estilos. A continuación se presentará una descripción para
ayudar a entender cuál debe ser usada en qué caso.

Controles UI simplificados
~~~~~~~~~~~~~~~~~~~~~~~~~~

Este conjunto de controles es suficiente para la mayoría de los
juegos, donde interacciones complejas o formas de presentar la
información no son necesarias. Pueden ser "skineados" fácilmente
con texturas regulares.

-  :ref:`Label <class_Label>`: Nodo usado para mostrar texto
-  :ref:`TextureFrame <class_TextureFrame>`: Muestra una sola
   textura, que puede ser escalada o mantenida fija.
-  :ref:`TextureButton <class_TextureButton>`: Muestra un
   simple botón con textura para los estados como pressed, hover,
   disabled, etc.
-  :ref:`TextureProgress <class_TextureProgress>`: Muestra una
   sola barra de progreso con textura.

Adicionalmente, el reposicionado de controles es más eficiente
hecho con anclas en este caso (ve el tutorial :ref:`doc_size_and_anchors`
para mas información)

De cualquier forma, sucederá seguido que, aún para juegos simples,
se requieren comportamientos de UI más complejos. Un ejemplo de
esto una lista de elementot con scrolling (desplazamiento) (por ejemplo
para una tabla de puntuaciones altas), la cual necesita un
:ref:`ScrollContainer <class_ScrollContainer>` y un :ref:`VBoxContainer <class_VBoxContainer>`.
Este tipo de controles mas avanzados puede ser mezclado con los
regulares sin problema (todos son controles de todas formas).

Controles de UI complejos
~~~~~~~~~~~~~~~~~~~~~~~~~

El resto de los controles (¡y hay docenas de ellos!) estan destinados
para otro tipo de escenario, los más comunes:

-  Juegos que requieren UIs complejas, como RPGs (juegos de rol),
   MMOs (juegos online masivos), strategy (estrategia), sims
   (simulación), etc.
-  Crear herramientas de desarrollo personalizadas para acelerar
   la creación de contenido.
-  Crear Plugins de Editor de Godot, para extender la funcionalidad
   del motor.

Es más común reposicionar controles con contenedores para este tipo
de casos (ve el tutorial :ref:`doc_size_and_anchors` para
más información).
