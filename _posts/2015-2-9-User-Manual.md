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


## Licencias

El sistema cuenta con 3 licencias estandar que definen la cantidad máxima de cierto tipo de entidades.

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

![Login]({{ site.url }}{{ site.baseurl }}/assets/user_manual/Login.png)


