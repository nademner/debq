---
layout: post
title: Manual de usuario v3.0.0
---

Manual de usuario para el sistema de turnos de debMedia.

---

# Introducción

El sistema de turnos cuenta fundamentalmente con cinco módulos fundamentales:

 * Player: Este módulo es el encargado de visualizar los turnos que son llamados por los puestos de atención.
 * Terminal de auto-gestión: Este módulo es por el cual se podrá ingresar turnos ( modulo opcional )
 * Panel de control: Este módulo permite cambiar la configuración del sistema.
 * Estadísticas: Este módulo permite el calculo de estadísticas a partir de los movimientos generados por los turnos.
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


# Puesta en marcha

## Configuraciones

Para poder adaptar el sistema a su ambiente de atención es necesario conocer las configuraciones fundamentales del mismo, el sistema solo puede ser configurado por los usuarios administradores desde el modulo **Panel de Control**.

El sistema se configura por sucursal, cada sistema viene con un usuario administrador de compañía y un usuario administrador de sucursal por cada sucursal que se haya adquirido.

Cada sucursal tiene sus propias configuraciones, para poder modificar las configuraciones de cada sucursal debe autenticarse con el usuario administrador de la sucursal que desea configurar o con el usuario administrador de compañía.

Una vez autenticado verá una pagina similar a la siguiente : 

![Selección de sucursal]({{ site.url }}{{ site.baseurl }}/assets/user_manual/seleccion_sucursal.png)

en dicha pagina podrá seleccionar la sucursal a la cual desea ingresar, una vez seleccionada vera una pagina similar a la siguiente :

![Página principal]({{ site.url }}{{ site.baseurl }}/assets/user_manual/home.png)

en dicha pagina podrá seleccionar si desea ingresar al modulo de atención o al modulo del panel de control, ingrese a este último y diríjase al menú **Configuraciones** , donde vera una pagina similar a la siguiente

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







