---
layout: post
title: Manual de usuario v3.0.0
---

Manual de usuario para el sistema de turnos de debMedia.

---

# Introducción

El sistema de turnos cuenta fundamentalmente con siete módulos:

 * Player: Este módulo es el encargado de visualizar los turnos que son llamados por los puestos de atención.
 * Terminal de auto-gestión: Este módulo es por el cual se podrá ingresar turnos ( modulo opcional )
 * Panel de control: Este módulo permite cambiar la configuración del sistema.
 * Estadísticas: Este módulo permite el calculo de estadísticas a partir de los movimientos generados por los turnos.
 * Movimientos: Este modulo permite visualizar todos los movimientos realizados por un turno, desde que ingreso al sistema hasta que fue finalizado.
 * Monitoreo: Este modulo permite visualizar en tiempo real el estado del sistema.
 * Puesto de atención: Este módulo permite llamar los turnos sacados desde la terminal de auto-gestión.

El sistema cuenta con seis entidades fundamentales las cuales permiten reproducir cualquier tipo de ambiente de atención.

 * Sucursales: El sistema cuenta con la posibilidad de brindar el servicio de turnos a más de una sucursal al mismo tiempo. 
 * Usuarios: El sistema cuenta con la entidad usuarios, la cual permite definir los distintos usuarios que se conectarán al sistema.
 * Filas: El sistema cuenta con la entidad filas, esta permite definir los trámites que habrá en cada ambiente de atención.
 * Pantallas: El sistema cuenta con la entidad pantallas, esta permite definir los distintos players que estarán disponibles para visualizar los distintos trámites definidos en el sistema.
 * Puestos: El sistema cuenta con la entidad puestos, esta permite definir los distintos puestos de trabajo donde se atenderán los tramites. 
 * Salas de espera: El sistema cuenta con la entidad salas de espera, esta permite definir el lugar físico donde atiende cada puesto de trabajo.

Cada entidad cumple su función y es fundamental entender las relaciones que existen entre ellas para lograr una configuración adecuada para su ambiente de atención.

![Relaciones]({{ site.url }}{{ site.baseurl }}/assets/user_manual/relaciones.png)

## Licencias

El sistema cuenta con 3 licencias que definen la cantidad máxima de cierto tipo de entidades.

 * Básica: Esta licencia define las siguientes cantidades máximas:

    * 5 Usuarios
    * 5 Filas
    * 1 Pantallas
   
 * Intermedia: Esta licencia define las siguientes cantidades máximas:

    * 10 Usuarios
    * 10 Filas
    * 3 Pantallas

 * Avanzada: Esta licencia define las siguientes cantidades máximas:

    * 25 Usuarios
    * 25 Filas
    * 6 Pantallas

Los usuarios que define cada licencia son usuarios de atención, es decir en estos no están incluidos los usuarios administradores.
Las licencias se definen por sucursal, es decir en el caso de que haya mas de una , cada una tendrá su propia licencia.

## Instalación

El sistema de turnos estará instalado en una computadora la cual funcionará como servidor, a la cual se conectaran los distintos módulos. Una vez colocado el servidor en la red en la cual se usará se podrá acceder al mismo a través de un navegador web ( Chrome o Firefox ). Para lograr esto ingresar http://debServer en el navegador y verá la siguiente pantalla :

![Login]({{ site.url }}{{ site.baseurl }}/assets/user_manual/login.png)

El sistema vendrá inicializado según la licencia adquirida, es decir si adquirió una licencia Básica , el sistema vendrá inicializado con 5 usuarios , 5 filas y 1 pantalla. Por lo tanto una vez conectado al servidor a través del navegador se podrán autenticar con los siguientes usuarios :

Usuarios administradores: 

 * Compañia : admin.debtech@debtech.com ; admin
 * Sucursal : admin.devoto@debtech.com ; admin

Usuarios de atención

 * atencion1.devoto@debtech.com ; atención1
 * atencion2.devoto@debtech.com ; atención2
 * atencionN.devoto@debtech.com ; atencionN

donde N identifica la cantidad máxima de usuarios para la licencia adquirida.

NOTA: Los usuarios mostrados son un ejemplo, en este ejemplo el sistema esta inicializado con la compañía **Debtech** y con una sucursal **Devoto** , si su sistema esta inicializado con otro nombre de compañía y sucursal , los usuarios cambian según los nombres usados.

# Puesta en marcha

## Configuraciones

Para poder adaptar el sistema a su ambiente de atención es necesario conocer las configuraciones fundamentales del mismo, el sistema solo puede ser configurado por los usuarios administradores desde el modulo **Panel de Control**.

El sistema se configura por sucursal, cada sistema viene con un usuario administrador de compañía y un usuario administrador de sucursal por cada sucursal que se haya adquirido.

Cada sucursal tiene sus propias configuraciones, para poder modificar las configuraciones de cada sucursal debe autenticarse con el usuario administrador de la sucursal que desea configurar o con el usuario administrador de compañía.

Una vez autenticado verá una página similar a la siguiente : 

![Selección de sucursal]({{ site.url }}{{ site.baseurl }}/assets/user_manual/seleccion_sucursal.png)

en dicha página podrá seleccionar la sucursal a la cual desea ingresar, una vez seleccionada vera una página similar a la siguiente :

![Página principal]({{ site.url }}{{ site.baseurl }}/assets/user_manual/home.png)

en dicha página podrá seleccionar si desea ingresar al modulo de atención o al modulo del panel de control, ingrese a este último y diríjase al menú **Configuraciones** , donde vera una página similar a la siguiente

![Panel de control > Configuraciones]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_configuraciones.png)

A continuación se explica que función cumple cada configuración.

 * Derivación : Cuando una persona debe pasar por dos tramites distintos, es necesario activar la derivación, por ejemplo si una persona ingresa al sistema a través del tramite **Fila 1** con el turno **A1** y luego debe dirigirse a la **Fila 2** , esta persona en el momento de la atención deberá ser derivada del trámite **Fila 1** al trámite **Fila 2**.
 * Tipo de Sincronismo : El modulo del puesto de atención debe mantenerse sincronizado con el servidor y para hacerlo se pueden utilizar distintas tecnologías, el sistema viene por defecto con la configuración en WebSocket, en cierto ambientes puede que esta configuración no funcione , si no esta seguro que opción elegir deje la configuración por defecto.
 * Reinicio de filas: Es posible configurar un horario donde las filas se reinician al turno inicial, esta configuración generalmente cumple la función de reiniciar las filas a la noche para comenzar un nuevo día con las filas en los turnos iniciales.
 * Estadísticas : Esta configuración permite definir un tiempo máximo a partir del cual dicha estadística no se computará, por ejemplo si usted no tiene activado el reinicio automático de filas , y un turno que fue sacado en el día 1 , se llama en el día 2, este tendrá un tiempo de espera muy grande, por lo que no es razonable utilizarlo para computar estadísticas, el umbral que permite decidir si computar o no es el que se configura en esta configuración.
 * Seguridad de pantallas activa: Como se mencionó anteriormente , los turnos avanzados en cada fila se visualizan en las pantallas, si por ejemplo el trámite **Fila 1** se atiende en la **Pantalla 1** y esta se encuentra apagada, entonces no se podrán visualizar los turnos del trámite **Fila 1** , por lo tanto esta configuración permite decidir si se desea permitir llamar cuando la pantalla se encuentra apagada.
 * Comportamiento de turnos en pantalla: Esta configuración permite decidir como se desplazan los turnos en la pantalla

    * En orden de llamado: En esta configuración los nuevos turnos llamados, aparecerán arriba de todo en la lista de turnos que se visualiza en la pantalla y el ultimo desaparecerá.
    * Mantener ultimo por fila: En esta configuración se mantendrá el ultimo turno de cada fila que se visualiza en cada pantalla siempre que se pueda.

 * Estado de puesto: Los puestos de atención tienen estados, por defecto vienen los estados **Online** y **Offline** , esta configuración permite decidir si se desea que el estado se pueda cambiar desde el modulo puesto de atención, independientemente si se configura para que sea editable desde el puesto, los estados de cada puesto se configuran automáticamente a **Online** cuando un usuario ingresa al puesto de atención y a **Offline** cuando se retira o cuando tuvo una inactividad mayor a un tiempo x el cual es configurable.


Si todavía no esta seguro de algunas configuraciones deje las que vinieron por defecto.


## Configuración de usuarios

El siguiente paso es configurar los usuarios, diríjase al menú **Usuarios** , vera una página similar a la siguiente:

![Panel de control > Usuarios]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_usuarios.png)

En dicho menú podrá configurar toda la información relacionada con los usuarios , como es  el nombre, el apellido, etc.

También podrá configurar el **Puesto** en el cual trabajará. El sistema viene de fabrica con tantos **Puestos** como usuarios de atención tenga, por lo que por defecto cada usuario de atención tiene asignado un **Puesto**.


## Configuración de filas

Luego de configurar los usuarios es necesario configurar las filas, diríjase al menú **Filas** , vera una página similar la siguiente: 

![Panel de control > Filas]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_filas.png)

En este menú se podrá configurar todo lo relacionado con los tramites que posee en su ambiente de atención. A continuación se detalla cada una de las configuraciones:

 * Información general
    * Nombre : El nombre configurado del tramite será la propiedad que se utilizará en todo el sistema para identificar al tramite, por ejemplo en el puesto de atención.
 * Sala de espera: Los tramites se pueden llegar a atender en mas de una sala de espera, pero deben tener configurada una por defecto, cuando se agregue un nuevo turno al tramite, este sera dirigido a la sala de espera por defecto.
 * Numeración : Es posible configurar la numeración que usará el trámite , este debe ser adecuado con el template pre-establecido para las pantallas, por ejemplo si usted adquirió un template que utiliza numeración con letra, no le debe configurar , al trámite, una numeración sin letra. Si no esta seguro del template adquirido deje la configuración por defecto.
 * Configuración: Es posible configurar un tramite para que sea una **fila de entrada**, generalmente hay dos flujos posibles :
    * Flujo 1 : Una persona ingresa al establecimiento, se dirige al terminal táctil saca el turno y espera a ser llamado.
    * Flujo 2 : Una persona ingresa al establecimiento, se dirige al caracol de turnos , toma un turno y espera a ser llamada.
   en el segundo flujo el turno no es ingresado por una terminal táctil por lo que tiene que ser llamado si o si por un tramite que este configurado para que sea **fila de entrada**.
 * Alertas : Es posible definir dos alertas, una por cantidad máxima de personas en espera excedida y otra por tiempo máximo de espera excedido, una vez que se hayan sobre-pasado dicho limites se le notificará a todo puesto de atención que atienda dicho tramite.

## Configuración de Pantallas

Una vez ingresado a dicho menú verá una pantalla similar a la siguiente : 

![Panel de control > Pantallas]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_pantallas.png)

La configuración de la pantalla le servirá para ajustar lo que desea ver en cada uno de los player que haya adquirido.
 
 * Filas mostradas en Tv: Con esta configuración deberá seleccionar los tramites que desea visualizar la pantalla que está configurando, por ejemplo si usted configura para que en la pantalla se visualicen los tramites **Fila 1** y **Fila 3** , cuando desde el puesto de atención se avancen turnos sobre los tramites **Fila 1** y **Fila 3** estos se verán en esta pantalla y no otros, es decir si avanza turnos sobre **Fila 2** estos no los verá en dicha pantalla.
 * Videos: Con esta configuración podrá seleccionar los videos que desea que se visualicen en la pantalla seleccionada ( al comienzo la lista le aparecerá vacía, en la siguiente sección se vera como subir al servidor un video )
 * Textos: Con esta configuración podrá seleccionar los textos que desea que se visualicen en la pantalla seleccionada ( al comienzo la lista le aparecerá vacía, en la siguiente sección se vera como subir al servidor un texto )
 * Template: 
    * Template : Si usted adquirió mas de un template, puede configurar que template debe tener cada pantalla.
    * Numero de entradas: Debe configurar cuantas filas tiene el turnero del template, si no esta seguro de que valor colocar, diríjase al player y cuentelas.


## Configuración de medias

En esta página podrá subir videos, textos e imágenes, cuando ingrese vera una pantalla similar a la siguiente:

![Panel de control > Medias]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_medias.png)

Al presionar el botón agregar le aparecerá la siguiente pantalla

![Panel de control > Medias > Subir]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_medias_upload.png)

Si desea subir un video seleccione la solapa subir video, busque el archivo en su computadora y presione **subir archivo**.
Si desea subir un texto seleccione la solapa subir texto, complete en el recuadro el texto que desee y presione **subir texto**.


Una vez que haya subido los videos y textos que desea diríjase nuevamente al menú **Pantallas** y seleccione que media desea ver en cada player.

## Configuración de puestos

Una de las configuraciones mas importante es la de los puestos, aquí sera donde configurará que trámites atiende cada puesto, cuando entre verá una pantalla similar a la siguiente:

![Panel de control > Puestos]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_puestos.png)

A continuación se detalla cada propiedad que puede configurar

 * Propiedades generales
    * Nombre: Esta propiedad le servirá para identificar al puesto a lo largo del sistema
    * Nombre en tv: Este nombre es el que se verá en los players cuando dicho puesto llame a un turno.
 * Sala de espera: Con esta propiedad esta configurando la ubicación física del puesto.
 * Salas de espera atendibles: Esta propiedad permite configurar las salas de espera sobre las cuales podrá atender turnos el puesto.
 * Filas de atención: Con esta propiedad lo que hará es configurar que trámites atiende cada puesto.
 * Filas de derivación: Con esta propiedad lo que hará es configurar a que trámites puede derivar turnos cada puesto.
 * Inactividad : Con esta propiedad lo que hará es configurar el tiempo por el cual el puesto puede permanecer en estado **Online** sin realizar acciones.
 * Estados disponibles: Aquí podrá configurar los estados que podrá configurar cada puesto desde el modulo **puesto de atención**
 * Botones habilitados: Aquí podrá configurar los botones que tendrá visible cada puesto en el modulo **puesto de atención**

Es posible configurar el orden en el cual aparecen los tramites en el puesto de atención, esta funcionalidad en general se usa para que los usuarios que utilizan el puesto de atención sepan la importancia de cada tramite. Para poder elegir el orden , arrastre los tramites en la opción **filas de atención** de la siguiente manera:

![Panel de control > Puestos > Orden]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_puestos_orden.png)


Si usted posee mas de una sala de espera, donde las personas esperan para ser atendidas deberá leer la siguiente sección, en caso contrario puede saltearla :

Cada puesto pertenece a una **Sala de espera** y atiende **Filas** , supongamos el siguiente caso

 * El puesto **Box 01** esta ubicado en la sala de espera **Sala de espera 1** y atiende los tramites **Fila 1** y **Fila 2** 
 * El puesto **Box 02** esta ubicado en la sala de espera **Sala de espera 2** y atiende los tramites **Fila 1** y **Fila 3** 

Como tanto el **Box 01** como el **Box 02** atienden la **Fila 1** en distintas salas de espera , los turnos pertenecientes al tramite **Fila 1** se podrán atender tanto en la sala **Sala de espera 1** como en la sala **Sala de espera 2** , por esto es que es importante configurar una sala de espera por defecto , ya que cuando ingresa un turno por la terminal táctil , en dicho momento no es posible seleccionar la sala de espera a la cual debe ir a esperar el turno. Luego en una derivación de algún turno del tramite **Fila 1** se podrá seleccionar la sala de espera a la cual será enviado el turno.

Por otro lado la configuración de salas de espera atendibles permite a un puesto poder atender turnos de una sala de espera a la cual no pertenece, en general esta acción no es recomendable por el hecho de que produce movimiento de personas entre salas de esperas que físicamente están ubicadas en lugares distintos.

## Configuración de salas de espera

Al entrar a este menú verá una pantalla similar a la siguiente:

![Panel de control > Salas de espera]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_sala_espera.png)

En esta pantalla podrá crear salas de espera y configurarles un nombre, el cual se utilizará para identificarla a lo largo del sistema.

## Configuración terminal táctil

Diríjase al menú **Autoservicio** , al entrar vera una pagina como la siguiente : 

![Panel de control > Autoservicio]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_terminales.png)

En la opción **Filas habilitadas** podrá seleccionar que filas desea que se muestren en cada terminal táctil.

Si desea tener tramites agrupados por sector por ejemplo tenerlos agrupados por aquellos que se atienden en primer piso y en planta baja póngase en contacto con un administrador para que le habilite la opción de edición de niveles.


## Configuración de grupos de tramites

Los puestos de atención atiende tramites, los turnos los atiende en el orden en el cual llegaron, es decir cuando llama a un turno llama al que hace mas tiempo esta esperando. A veces se requiere poder llamar al que mas tiempo esta esperando dentro de un grupo de tramites, para lograr esto se debe crear grupos, para hacerlo entrar al menú **Grupos de fila**, verá una pantalla como la siguiente:

![Panel de control > Grupos]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_grupos.png)

Para crear un nuevo grupo presione en el botón **Agregar nuevo grupo** , con esto se habrá creado un nuevo grupo y verá una pantalla similar a la siguiente:

![Panel de control > Nuevo Grupo]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_grupos_2.png)

Las propiedades

 * Nombre
 * Sala de espera por defecto
 * Alertas

cumplen la misma función que en el caso de las **Filas** 

La propiedad **Filas agrupadas** le permite seleccionar que filas desea agrupar en el grupo creado. Una vez hecho esto puede dirigirse al menú **Puestos** en la opción **filas de atención** para asignarle el nuevo grupo a los puestos que desee.



# Puesto de atención

El puesto de atención será el modulo que utilizarán las personas que atenderán los distintos trámites configurados, en esta sección se explicará como utilizarlo. Para ingresar al puesto de atención se puede o autenticar con un usuario de atención, por ejemplo atencion1.devoto@debtech.com o puede ingresar con el usuario adminitrador teniendo en cuenta que esté por defecto no tiene asignado ningún puesto, por lo tanto debe dirigirse al **Panel de control** solapa **Usuarios** y asignarle un puesto al usuario administrador. Al ingresar al puesto de atención verá la siguiente página: 


![Puesto de atención]({{ site.url }}{{ site.baseurl }}/assets/user_manual/ws_descripcion.png)


A continuación se detalla la funcionalidad de cada sección:

 1.  Estado del puesto de atención.  
 2.  Lista de filas habilitadas desde el panel de control para el puesto.
 3.  Botón para llamar al siguiente en la fila seleccionada en la lista 2.
 4.  Esta opción permite cambiar la sala de espera para así poder visualizar los turnos de otra sala de espera y poder llamarlos.
 5.  Lista de turnos de la fila seleccionada en la lista 2.
 6.  Botón para llamar al turno seleccionado en la lista 4.
 7.  Campo para ingresar numero a llamar cuando la fila es **fila de entrada**.
 8.  Campo donde se visualiza el turno en atención.
 9.  Botón para volver a llamar al turno llamado.
 10.  Campo que indica la cantidad de personas en espera para la fila seleccionada en la lista 2.
 11. Último turno llamado para la fila seleccionada en la lista 2.
 12. Botón para finalizar el turno llamado.
 13. Botón para finalizar el turno llamado, para el caso donde la persona no se presentó, este botón a diferencia del anterior, finaliza el turno pero no computa estadística.
 14. Este selector permite elegir la fila a donde se derivará el turno en atención.
 15. Botón que deriva el turno en atención.
 16. Este botón manda al turno en atención, al final de la fila, es similar al botón anterior con la diferencia que no computa estadística y es útil para el caso donde la persona no se presento, pero no se desea perder el turno para contemplar la eventual aparición de la persona.
 17. Este botón cancela la ultima derivación realizada por el puesto y le coloca el turno al puesto como turno en atención, este botón es útil para el caso donde la persona que atiende el puesto realizo una derivación errónea.
 18. Este botón cancela el llamado realizado, es útil para el caso donde la persona realizo un llamado erróneo.
 19. Este botón lo dirige a la pagina anterior.
 20. Este botón cierra la sesión y lo dirige a la página de autenticación.

Es importante notar que todas las alertas configuradas para los grupos y/o filas son para la sala de espera a la cual pertenece el puesto, incluso si se cambia la sala de espera desde la opción 4.

# Monitoreo

Este módulo permite visualizar en tiempo real lo que esta sucediendo con las distintas filas , puestos , pantallas y terminales táctiles configuradas. También muestra mensajes relevantes al sistema, por ejemplo, el reinicio automático de filas. Para entrar a este modulo dirigirse al **Panel de Control** y entrar en la opción **Monitoreo** , al entrar vera una página similar a la siguiente : 


![Panel de control > Monitoreo]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_monitoreo.png)

La página esta dividida en secciones, cada una indica parámetros relevantes a su funcionamiento.

 * Filas y Grupos: 
  - Turno actual: Último turno llamado en la fila/grupo.
  - En espera: Cantidad de personas esperando para ser atendidas
  - Máximo tiempo de espera: Muestra el tiempo mas grande de espera de entre las personas que están esperando para ser atendidas.
  - Tiempo de espera promedio: Muestra el promedio que esperaron las personas para ser atendidas durante la ultima hora.

 * Pantallas:
  - Pantalla: Muestra el número de la pantalla.
  - Estado: Muestra el estado actual de la pantalla.
  - Volumen: Muestra el volumen de la pantalla.

 * Puestos:
  - Puesto de trabajo: Muestra el nombre del puesto.
  - Turno actual: Muestra el turno que esta atendiendo el puesto.
  - Usuario: Muestra el usuario que esta utilizando el puesto.
  - Sala de espera: Muestra en que sala de espera esta ubicado el puesto.
  - Estado: Muestra el estado del puesto.

 * Terminales:
  - Terminal: Muestra el numero de la terminal táctil.
  - Estado: Muestra el estado de la terminal táctil.

 * Mensajes: Mensajes relevantes al funcionamiento del sistema.


# Movimientos

Este modulo permite visualizar todos los movimientos realizados por un turno. Para acceder dirigirse al **Panel de Control** e ingresar en la opción **Movimientos** , al ingresar verá una página similar a la siguiente:

![Panel de control > Movimientos]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_movimientos.png)

Los movimientos se almacenan por mes, por lo tanto solo podrá ver los movimientos realizados en el mes actual. Si desea ver de meses anteriores deberá exportarlos utilizando el botón **Exportar** junto con el calendario que tiene alado el cual permite seleccionar el mes que desea exportar.

El módulo permite seleccionar un día del mes utilizando el calendario. Una vez seleccionado el día se le mostraran en la ventana de la derecha todos los movimientos que hubo en el día seleccionado.

A continuación se describe que indica cada columna:

 * Id de turno: Es un número único que permite identificar al turno univocamente.
 * Turno: Letra y numero del turno.
 * Acción: Muestra la acción realizada sobre el turno, como por ejemplo INGRESO,LLAMADA,FINALIZACIÓN,etc.
 * Fila: Fila sobre la cual se realizó la acción, por ejemplo en el caso que la acción sea INGRESO, este campo muestra la fila donde ingreso el turno.
 * Puesto: Este campo aplica cuando la acción es realizada por algún puesto, por ejemplo la acción LLAMADA la realiza un puesto y este se indica en esta columna.
 * Usuario: Este campo indica el usuario que esta utilizando el puesto que realizo la acción y aplica a los mismos casos que la columna anterior.
 * Tiempo de espera: Muestra el tiempo que espero el turno en la ultima espera.
 * Tiempo de atención: Muestra el tiempo que estuvo siendo atendido durante la ultima llamada.
 * Tiempo total de espera: Muestra el tiempo de espera total que viene acumulando el turno, se diferencia del tiempo de espera por las posibles derivaciones del turno.
 * Tiempo total de atención: Muestra el tiempo de atención total que viene acumulando el turno, se diferencia del tiempo de atención por las posibles derivaciones del turno.

# Estadísticas

Este modulo permite visualizar la estadísticas calculadas por el sistema. Para ingresar , dirigirse al **Panel de control** opción **Estadísticas**, al entrar verá una página como la siguiente:


![Panel de control > Estadísticas]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_estadisticas.png)


El sistema calcula 6 estadísticas:

 * Cantidad: Cantidad de personas ingresadas, esta estadística se puede discriminar por usuario , por fila y por ambas.
 * Tiempo de atención: Tiempo de atención promedio, esta estadística se puede discriminar por usuario , por fila y por ambas.
 * Tiempo de espera: Tiempo de espera promedio, esta estadística se puede discriminar solo por fila.
 * Tiempo de espera del cliente: Indica el tiempo de espera promedio total, es decir tiene en cuenta las esperas que tuvo en cada fila por la que pasó, esta estadística no se discrimina ni por fila ni por usuario.
 * Tiempo de atención del cliente: Indica el tiempo de atención promedio total, es decir tiene en cuenta el tiempo de atención que tuvo en cada fila por la que pasó, esta estadística no se discrimina ni por fila ni por usuario. 
 * Tiempo total del cliente: Indica el tiempo promedio total, tiene en cuenta tanto atención como espera, esta estadística no se discrimina ni por fila ni por usuario.

Las estadísticas se pueden consultar en distintos periodos.

 * Horas: Al elegir por horas podrá elegir desde que hora y hasta que hora desea ver las estadísticas así como el día de inicio y el día de fin.
 * Días: Al elegir días solo le dejará elegir el día de inicio y el día de fin.
 * Meses: Al elegir meses, lo dejará elegir un día de inicio y un día de fin, y a partir de dichas fechas se obtendrá el mes de inicio y el mes de fin.
 * Años: Al elegir años, lo dejará elegir un día de inicio y un día de fin, y a partir de dichas fechas se obtendrá el año de inicio y el año de fin.

El modulo permite graficar estadísticas o exportarlas a Excel, el graficador grafica un máximo de sesenta puntos.

Para poder graficar primero expandir la opción **Parámetros de estadísticas** tocando el botón **Editar** al hacerlo verá lo siguiente: 


![Panel de control > Estadísticas > Parámetros]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_estadisticas_parametros.png)


Aquí podrá elegir el periodo y las fechas para el periodo elegido.


Luego expanda la opción **Graficación** y verá lo siguiente: 

![Panel de control > Estadísticas > Graficación]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_estadisticas_graficacion.png)


Aquí podrá seleccionar, dependiendo de la estadística seleccionada los discriminadores, puede elegir mas de uno y para las estadísticas que tienen dos discriminadores puede elegir ambos o uno a la vez.

Por ejemplo si desea graficar la cantidad de personas que fueron atendidas en el tramite **Fila 1** en el día 28/09/2015 en horas deberá configurar los parámetros de la siguiente manera:

 * Periodo: Horas
 * Fecha inicial: 28/09/2015
 * Fecha final: 28/09/2015
 * Estadística: Cantidad
 * Filas: Fila 1

Luego tocar el botón , graficar.

Si desea graficar la cantidad de personas que fueron atendidas en el tramite **Fila 1** en el día 28/09/2015, en horas, discriminado por los usuarios **Atención 1** y **Atención 2** deberá configurar los parámetros de la siguiente manera:

 * Periodo: Horas
 * Fecha inicial: 28/09/2015
 * Fecha final: 28/09/2015
 * Estadística: Cantidad
 * Filas: Fila 1
 * Usuarios: Atención 1, Atención 2.

Luego tocar el botón , graficar.


Si desea exportar las estadísticas a Excel, primero configure los parámetros en la opción **Parámetros de estadísticas** y luego expanda la opción **Exportación**, al hacerlo verá lo siguiente:


![Panel de control > Estadísticas > Exportación]({{ site.url }}{{ site.baseurl }}/assets/user_manual/pc_estadisticas_exportacion.png)


Los parámetros que debe configurar son los mismos que configura para la graficación. Una vez configurado esto presionar el botón **Exportar**.



