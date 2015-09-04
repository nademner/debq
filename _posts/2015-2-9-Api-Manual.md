---
layout: post
title: Manual de uso de la api
---

Este documento pretende explicar tanto el funcionamiento como el uso correcto de la API del sistema de turnos de debMedia.

---

# Documentación API debQ


## Detalles previos

El sistema se maneja con listas enlazadas para mantener los turnos organizados. A medida que se agregan turnos a las listas estos se agregan de manera que el primero en la lista siempre sea el que mas tiempo de espera tiene hasta el momento. Notar lo siguiente  

* Las listas enlazadas son de **turnos**.
* Existen las entidades **tramites** y **grupo de tramites**. Los grupos de tramites sirven como contenedor para conglomerar muchos tramites.
* En el documento las palabras tramites y **filas** se usan como sinónimos.
* Existen trámites denominados **filas de entrada**, que permite ingresar manualmente el turno o al tocar llamar crea automáticamente el próximo turno. No se pueden derivar a ese trámite, y además al cancelar el llamado se borra el turno.
* Las listas enlazadas de turnos pertenecen a un grupo de tramites para así atender dentro del grupo a la persona que más tiempo esperó.
* Existen las entidades **sala de espera** y **puesto de trabajo**. Los puestos de trabajo tienen grupos de trámites a los cuales pueden llamar y además pertenecen a una sala de espera.
* Es importante que todas las request tengan el header ***Accept*** configurado en ***application/json***, esto permite saber el formato de respuesta a utilizar en caso de que ocurra algún error.
* El sistema soporta el esquema de multi-sucursal.

## Proceso de autenticación.

El primer paso para el uso de la Api, es la autenticación del usuario. Para esto el sistema trabaja basado en un sistema de tokens. Los pasos son los siguientes

1. El cliente se identifica contra el servidor enviando un email y una contraseña, las cuales deben pertenecer a un usuario creado en el sistema.
2. Si el cliente pertenece a mas de una sucursal es necesario especificar el id de sucursal a la cual se esta conectando. Además, si el usuario utilizará un puesto de atención es necesario especificarlo con el id.  Si no se especificó la sucursal, se presentará una lista de ellas para poder asignarla con una nueva request; lo mismo con el puesto, aunque no es necesario asignar el puesto para ingresar al sistema (salvo para usar la API del puesto de atención).
4. El servidor responde con un token guardado en el header ***Set-Cookie*** de la respuesta de la request realizada en el paso (1), notar que el servidor solo responderá con un token valido en el caso que el email y la contraseña se hayan validado, en caso contrario se recibirá una respuesta de no autorizado.
4. El cliente debe guardar el token enviado por el servidor y en cada request que haga deberá guardar dicho token en el header ***Cookie***.
5. En algunas request puede que el servidor cambie el token por lo que luego de cada request sucesiva a la autenticacion, chequear que el header ***Set-Cookie*** este vacío, en el caso de no estarlo pisar el token viejo y guardar el nuevo.

## Puesto de atención.

### Funcionamiento básico.

El puesto de atención tiene principalmente dos procesos importantes. El primero es el proceso mediante el cual se llama un turno y consecuentemente el segundo proceso es el de finalizado; este ultimo es de vital importancia ya que en dicha instancia es donde el sistema computa las estadísticas de dicho turno.

El primer proceso mencionado consta de distintas opciones:

* ***Llamar el siguiente turno en un grupo***: Esta acción permite llamar el siguiente turno en la cola de un grupo de tramites. Si se llama en una fila de entrada se crea un nuevo turno siguiendo la numeración.
* ***Llamar un turno especifico en un grupo***: Esta acción permite llamar cualquier turno que se encuentre en la cola de un grupo de tramites. Con este método se puede crear un número no predefinido en un nuevo turno de una fila de entrada.

El segundo proceso mencionado consta de las siguientes opciones:

* ***Finalizar el turno***: Esta acción permite finalizar un turno.
* ***Ignorar el turno***: Esta acción permite finalizar un turno, pero a diferencia de la anterior, esta acción no computa estadísticas.
* ***Derivar el turno***: Esta acción permite derivar un turno a otro tramite. Esta acción computa estadísticas. No se puede derivar a una fila de entrada
* ***Colocar al final***: Esta acción permite derivar un turno a la misma fila y a diferencia del método anterior, no computa estadística. Si se coloca al final en una fila de entrada se elimina el turno.

Hay un instancia intermedia, luego de llamar y antes de finalizar, en la cual se permite al usuario volver a llamar el turno que ya llamó.

Por ultimo el puesto posee alguna acciones que permite volver a atrás en operaciones como el llamado y la derivación, que corresponde cancelar llamado y cancelar derivación. Si se cancela el llamado en una fila de entrada se borra el turno creado.

### API

#### Proceso de autenticación
A continuación se detalla el método que se debe ejecutar para poder autenticarse

```
Request Method: POST

Request URL: http://debserver/api/authenticate

Request Headers:
    Accept: application/json
    Content-Type : application/json
  
Request Body:
{
    'email' : 'Email del usuario',
    'password' : 'Contraseña',
    'branch.id' : 'Id de sucursal'      //Parámetro condicional
    'worker.id' : 'Id de puesto'        //Parámetro condicional
}

Response Headers:
    Set-Cookie: token

Response Body:

Status code: 200 OK

ó

Status code: 303 SEE OTHER -> http://debserver/api/authenticate/workplaces  //Para setear el Sitio de trabajo
```

Una vez autenticado el usuario, todas las además acciones a realizar, como es el llamado de turnos y su finalización se harán usando dicho usuario.
El parámetro ***branch.id*** es condicional en el sentido en que es necesario especificar cuando el usuario con el que se esta conectando pertenece a más de una sucursal. En caso contrario el usuario se estará conectando automáticamente a la única sucursal que tiene asignada.
El parámetro ***worker.id*** es condicional en el sentido en que un administrador para conectarse no necesita especificar un puesto de atención.
Si eventualmenten no se ingresó la sucursal y el usuario está asignada a más de uno, debe ejecutarse la siguiente request, con los datos del usuario que se obtuvieron al autenticarse. Lo mismo si tiene varios puestos, pero no es necesario mandarlo para ingresar al sistema, pero si para ejecutar la API del puesto de atención.

```
Request Method: POST

Request URL: http://debserver/api/authenticate/workplace

Request Headers:
    Accept: application/json
    Content-Type : application/json
  
Request Body:
{
    'branchId' : 2, //ID de sucursal
    'workerId' : 1  //ID de puesto
}

Response Headers:
    Set-Cookie: token

Response Body:

Status code: 200 OK
```


#### Obtención de parámetros del usuario
Desde el panel de control se configura los grupos de tramites que tiene cada puesto de trabajo habilitados para llamar y para derivar, entre otras configuraciones. Una vez autenticado el usuario es necesario obtener la configuración de dicho usuario, para así saber su configuración. Para obtener dicha configuración utilizar el siguiente servicio

```
Request Method: GET

Request URL: http://debserver/api/loggedUser

Request Headers:
    Accept: application/json
    Cookie: Token

Request Body:

Response Headers:
    Content-Type : application/json

Response Body:
{  
    "id":"Id de usuario.",
    "worker":                                                               //Puesto de trabajo del usuario.
    {  
        "id":"Id de puesto.",
        "actualTurn":                                                       //Turno que el usuario esta atendiendo actualmente.
        {  
            "id":"Id de turno.",          
            "queue":                                                        //Tramite al cual pertenece el turno.
            {  
                "id":"Id de fila.",
                "name":"Nombre de fila."
            },
            "waitingRoom": 
            {
                "id": "Id de sala",
                "name": "Nombre de sala"
            },
            "letter":"Letra.",                                              //Letra identificadora del turno.
            "number":"Numero.",                                             //Numero identificador del turno.
            "initTimeStamp" : "Inicio",                                     //Hora en la que fue sacado el turno
            "callTimeStamp" : "Llamado"                                     //Hora en la que fue llamado el turno
        },
        "callableQueueGroups":                                              //Grupos habilitados, en el puesto, para llamar.   
        [  
            {  
                "id":"Id de grupo.",
                "name":"Nombre de grupo.",
                "standBy":"Cantidad de personas en espera.",                //Cantidad de personas esperando actualmente en el grupo.
                "standByAlert":"Máxima cantidad de personas en espera.",    //Máxima cantidad de personas en el grupo.
                "standAlone":"Grupo de única fila",                         //Boleano indicando si es un grupo de una única fila.
                "maxWaitTimeAlert" : "MaxAlert",                            //Alerta de tiempo maximo de espera excedido
                "maxWaitTime" : "Max",                                      //Tiempo de espera mas grande entre los turnos del grupo. 
                "queues":                                                   //Tramites que pertenecen al grupo    
                [  
                    {  
                        "id":"Id de fila",
                        "name":"Nombre de fila",
                        "entryQueue": "false"                               //Fila de entrada
                    }
                ],
                "actualTurn":                                               //Ultimo turno llamado en el grupo  
                {  
                    "id":"Id de turno",
                    "letter":"Letra",
                    "number":"Numero",
                },
                "turns":                                                    //Lista de turnos esperando para ser atendidos en el grupo.   
                [  
                    {  
                        "id":"Id de turno",
                        "queue":
                        {  
                            "id":"Id de fila",
                            "name":"Nombre de fila"
                        },
                        "waitingRoom": 
                        {
                            "id": "Id de sala",
                            "name": "Nombre de sala"
                        },
                        "letter":"Letra",
                        "number":"Numero",
                        "initTimeStamp" : "Inicio",                         //Hora en la que fue sacado el turno
                    },
                ]
            },
        ],
        "derivableQueueGroups":                                             //Grupos habilitados, en el puesto, para derivar.   
        [  
            {  
                "id":"Id de grupo.",
                "name":"Nombre de grupo."
                "possibleWaitingRooms":                                     //Sala de espera donde se puede derivar.
                [
                    {
                        "id": "Id de sala",
                        "name": "Nombre de sala"
                    }
                ]
            },
        ],
        "waitingRoom":                                                      //Sala de espera a la que pertenece el puesto.  
        {
            "id": "Id de sala",
            "name": "Nombre de sala"
        },
        "possibleStates":                                                   //Posibles estados que puede tener el puesto de trabajo.
        [  
            {  
                "id":"Id de estado.",
                "name":"Nombre de estado."
            }
        ],
        "state":                                                            //Estado actual del puesto de trabajo.
        {  
            "id":"Id de estado.",
            "name":"Nombre de estado."
        }
        "buttons":
        [
            {
                "id" : "Id de boton"
                "name" : "Nombre del boton"
                "value" : "Estado del boton"                                //Activated o Deactivated
            }
        ]
    },
    "branches":
    [
        {
            "id" : "Id de branch",
            "name" : "Nombre de la branch"
        }
    ]
}

Status code: 200 OK
```

Es importante notar que los puestos de atención pertenecen a una sala de espera, ya que esta relación permite saber a que salas de espera se puede derivar un turno. La lista de posibles salas de espera se construye automáticamente a partir de la relación que tienen los puestos con los grupos llamables. Por ejemplo si el "Puesto A" atiende al "Grupo 1" y pertenece a la sala "Sala de espera PB"  entonces turnos se podrán derivar al "Grupo 1" solo a la sala "Sala de espera PB".
Notar que la lista de turnos en los grupos es dependiente del puesto, ya que un puesto solo puede atender turnos que pertenecen a su sala de espera. Por lo tanto la lista de turnos solo contendrá turnos de su sala de espera, otra forma de verlo es que la lista de turnos total esta filtrada por la sala de espera a la que pertenece el puesto.
Otra observación necesaria es que tanto el parámetro "standBy" como el parámetro "maxWaitTime" son los correspondientes a la lista de turnos filtrada a la sala de espera a la que pertenece el puesto de atención.

A continuación se detallan los nombres de los botones que aparecerán en la lista de botones del puesto.

 * DerivateBtn
 * CallBtn
 * NextBtn
 * FinishBtn
 * RevokeBtn
 * AssociateBtn
 * DetachBtn
 * ReCallBtn
 * ReQueueBtn
 * CancelCallBtn
 * CancelDerivateBtn

Los posibles valores son

 * Activated
 * Deactivated

#### Proceso de llamado

Para poder realizar las acciones correspondientes al llamado de turno se cuenta con un servicio, a continuación se lo detalla:

```
Request Method: POST

Request URL: http://debserver/api/workstation/call

Request Headers:
    Accept: application/json
    Content-Type : application/json
    Cookie: Token

Request Body:
{
    'queueGroup.id' : 'Id de grupo',            //Campo obligatorio
    'turn.id' : 'Id de turno'                   //Campo opcional  
}

Response Headers:
    Content-Type : application/json

Response Body:
{
    "id": "Id de turno",
    "queue": {
        "id": "Id de la fila a donde pertenece el turno"
    },
    "letter": "Letra del turno",
    "number": "Numero del turno"
}

Status code: 200 OK
```

Como se observa en el request body el parámetro 'qTurn.id' es opcional. En caso de no completar dicho campo el servidor llamará automáticamente al siguiente. En caso contrario tratará de llamar el turno especificado.

#### Proceso de finalizado

A diferencia del proceso de llamado, este contiene mas de un servicio, a continuación se los detalla

***Finalización***

```
Http action: POST

Url: http://debserver/api/workstation/finish

Request Headers:
    Accept: application/json
    Cookie: Token

Request Body:

Response Headers:

Response Body:

Status code: 200 OK
```

***Descarte***  

```
Request Method: POST

Request URL: http://debserver/api/workstation/revoke

Request Headers:
    Accept: application/json
    Cookie: Token

Request Body:

Response Headers:

Response Body:

Status code: 200 OK
```

***Derivación***  

```
Request Method: POST

Request URL: http://debserver/api/workstation/derivate

Request Headers:
    Accept: application/json
    Content-Type : application/json
    Cookie: Token

Request Body:
{
    'destinationQueueGroup.id' : 'Id de grupo'
    'waitingRoom.id' : 'Id de puesto de atención'
}

Response Headers:

Response Body:

Status code: 200 OK
```

Tanto el grupo de destino como la sala de espera son parámetros que tienen que ser especificados por el cliente en el momento que se deriva el turno, y se obtienen a partir de la configuración del usuario obtenida en el servicio ***loggedUser***.

***Colocar al final***  

```
Request Method: POST

Request URL: http://debserver/api/workstation/requeue

Request Headers:
    Accept: application/json
    Cookie: Token

Request Body:

Response Headers:

Response Body:

Status code: 200 OK
```

#### Re-llamado
Como se mencionó anteriormente, una vez llamado un turno este se puede volver a llamar para enviar nuevamente el evento de llamado al televisor. A continuación se detalla el método para realizar dicha acción

```
Request Method: POST

Request URL: http://debserver/api/workstation/recall

Request Headers:
    Accept: application/json
    Cookie: Token

Request Body:

Response Headers:

Response Body:

Status code: 200 OK
```

#### Proceso de cancelación
Para contemplar los errores que puede generar una persona utilizando un puesto de atención, se tienen dos métodos de cancelación. Uno te permite cancelar el llamado realizado por el usuario y el otro permite cancelar una derivación. 

***Cancelar Llamado***  
Este método lo que hace es cancelar el último llamado realizado por el usuario, al turno se lo encola primero en el tramite al cual pertenecía.

```
Request Method: POST

Request URL: http://debserver/api/workstation/cancelcall

Request Headers:
    Accept: application/json
    Cookie: Token

Request Body:

Response Headers:

Response Body:

Status code: 200 OK
```

***Cancelar Derivación***  
Este método lo que hace es cancelar la ultima derivación realizada, al turno se lo remueve del tramite al cual fue derivado y se lo coloca en atención al usuario.

```
Request Method: POST

Request URL: http://debserver/api/workstation/cancelderivate

Request Headers:
    Accept: application/json
    Content-Type : application/json
    Cookie: Token

Request Body:
{
  "turn.id" : "Id de turno",                            //Id de turno
  "originalQueue.id" : "ID de tramite"                  //Id de tramite al cual pertenecía el turno
}

Response Headers:
    Content-Type : application/json

Response Body:
{
    "id": "Id de turno",
    "queue": {
        "id": "Id de la fila a donde pertenece el turno"
    },
    "letter": "Letra del turno",
    "number": "Numero del turno"
}

Status code: 200 OK
```

#### Cambiar de estado
Para cambiar el estado de un puesto de atención, ejecutar el siguiente servicio

```
Request Method: POST

Request URL: http://debserver/api/workstation/updateState

Request Headers:
    Accept: application/json
    Content-Type : application/json
    Cookie: Token

Request Body:
{
    'id' : 'Id de estado'     //El id de estado se puede obtener de los posibles estados obtenidos en el loggedUser.
}

Response Headers:

Response Body:

Status code: 200 OK
```


#### WebSocket 
El servidor posee un sistema de notificaciones que permiten mantener a los puestos de atención actualizados. Por ejemplo cuando se agrega un nuevo turno a un tramite, todos los puestos que atienden este tramite deben enterarse sobre este evento para por ejemplo actualizar la lista de turnos. Otro caso útil es para el sistema de alarmas, actualmente se poseen dos tipos de alarmas, la alarma por máxima cantidad de gente en espera excedida y la alarma por tiempo máximo de espera excedido.

En el caso de la primer alarma se notifica , en el caso que se haya excedido el máximo, de manera puntual cuando en el servidor suceden los siguientes eventos: 

* Cuando se agrega un turno a un tramite.
* Cuando se deriva un turno.
* Cuando se cancela un llamado.

Para la alarma de máximo tiempo de espera excedido, el servidor tiene una tarea programada para que cada 2 minutos verifique en todos los tramite si se excedió el limite. En el caso de que se haya excedido lo notifica.

Para conectarse al canal de comunicación es necesario usar un cliente web socket y estar logeado al sistema. 

***Url de conexión :*** ws://debserver/api/workstation/wsSync

El formato con el que se notifica es el siguiente

```

{
    'action' : 'Acción'
}

```

La siguiente lista muestra las acciones que el servidor puede llegar a notificar

 * MAXWAITINGTIME_ALERT             //Alerta de máximo tiempo de espera excedido
 * MAXSTANDBY_ALERT                 //Alerta de máxima cantidad en espera excedida
 * QUEUEGROUP_DATA                  //Actualización de grupo, para actualización de lista de turnos
 * KEEP_ALIVE                       //Permite saber si la conexión sigue activa
 * REFRESH                          //Notifica un cambio en la configuración del puesto

Como se mencionó en el caso del KEEP_ALIVE este tiene como finalidad saber si la conexión sigue activa, el servidor manda un mensaje de KEEP_ALIVE cada 40 segundos.

### Api General

#### Encolar turnos
Esta función generalmente la cumple la terminal tactil, sin embargo por cuestiones de comodidad a veces se requiere poder agregar turnos ejecutando un servicio web, a continuación se describe dicho servicio.

```
Request Method: POST

Request URL: http://debserver/api/terminal/queueUp

Request Headers:
    Accept: application/json
    Content-Type : application/json
    Cookie: Token

Request Body:
{
    'queue.id' : 'Id de tramite'      
}

Response Headers:
    Content-Type : application/json

Response Body:
{
    "id": "Id de turno",
    "queue": {
        "id": "Id de la fila a donde pertenece el turno"
    },
    "letter": "Letra del turno",
    "number": "Numero del turno"
}

Status code: 200 OK
```

#### Obtención de los tramites
En el servicio anterior se vió como es necesario mandar el id de tramite para encolar un turno. A continuación se describe un servicio que permite encontrar todos los tramites disponibles a los cuales se les puede agregar turnos.

```
Request Method: POST

Request URL: http://debserver/api/queue/queues

Request Headers:
    Accept: application/json
    Content-Type : application/json
    Cookie: Token

Request Body:

Response Headers:
    Content-Type : application/json

Response Body:
[
    {
        "id": "Id de fila",
        "name": "Nombre de fila",
        "queueGroups": [
            {
                "id": "Id de grupo",
                "name": "Nombre de grupo",
                "actualTurn": null
            }
        ]
    },
    {
        "id": "Id de fila",
        "name": "Nombre de fila",
        "queueGroups": [
            {
                "id": "Id de grupo",
                "name": "Nombre de grupo",
                "actualTurn": null
            }
        ]
    },
]

Status code: 200 OK
```

#### Obtención de parámetros de la compañía

Al crear una compañía en el sistema se crea un usuario administrador con permiso para administrar todas las sucursales que posee dicha compañía. Este usuario tiene permisos para ver toda la información disponible para una compañía.

El siguiente servicio web permite obtener todos los grupos de una compañía y solo puede ser ejecutada si se ha autenticado con el usuario administrador de compañía.

```
Request Method: GET

Request URL: http://debserver/api/queueGroup/companyQueueGroups

Request Headers:
    Accept: application/json
  
Request Body:

Response Body:
{
    "queueGroups" : 
    [  
        {  
            "id":"Id de grupo.",
            "name":"Nombre de grupo.",
            "standBy":"Cantidad de personas en espera.",                        //Cantidad de personas esperando actualmente en el grupo.     
            "standByAlert":"Máxima cantidad de personas en espera.",            //Máxima cantidad de personas en el grupo.
            "standAlone":"Grupo de única fila",                                 //Boleano indicando si es un grupo de una única fila.
            "maxWaitTimeAlert" : "MaxAlert",                                    //Alerta de tiempo máximo de espera excedido
            "maxWaitTime" : "Max",                                              //Tiempo de espera mas grande entre los turnos del grupo. 
            "queues":                                                           //Tramites que pertenecen al grupo                                                     
            [   
                {  
                    "id":"Id de fila",
                    "name":"Nombre de fila"
                }
            ],
            "actualTurn":                                                       //Ultimo turno llamado en el grupo        
            {  
                "id":"Id de turno",
                "letter":"Letra",
                "number":"Numero"
            },
            "turns":                                                            //Lista de turnos esperando para ser atendidos en el grupo.             
            [  
                {  
                    "id":"Id de turno",
                    "queue":
                    {  
                        "id":"Id de fila",
                        "name":"Nombre de fila"
                    },
                    "waitingRoom": 
                    {
                        "id": "Id de sala",
                        "name": "Nombre de sala"
                    },
                    "letter":"Letra",
                    "number":"Numero",
                    "initTimeStamp" : "Inicio"                                  //Hora en la que fue sacado el turno
                }
            ],
            "branch":
            {
                "id" : "Id de branch"
            }
        }
    ],
    "actualTime" : "Hora actual"                                                //Hora del servidor
}   

Status code: 200 OK
```

El siguiente servicio web permite obtener todos los puestos de una compañía y solo puede ser ejecutada si se ha autenticado con el usuario administrador de compañía.

```
Request Method: GET

Request URL: http://debserver/api/worker/companyWorkers

Request Headers:
    Accept: application/json
  
Request Body:

Response Body:
[
  {
    "id": "ID DE PUESTO",
    "name": "NOMBRE DE PUESTO",
    "branch":
    {
      "id": "ID DE SUCURSAL",
      "district": "NOMBRE DE SUCURSAL"
    }
  }
]


Status code: 200 OK
```


El siguiente servicio web permite obtener todos las sucursales de una compañía y solo puede ser ejecutada si se ha autenticado con el usuario administrador de compañía.

```
Request Method: GET

Request URL: http://debserver/api/branch/companyBranches

Request Headers:
    Accept: application/json
  
Request Body:

Response Body:
[
  {
    "id": "D DE SUCURSAL",
    "district": "NOMBRE DE SUCURSAL"
  }
]

Status code: 200 OK
```


El siguiente servicio web permite obtener todos los usuarios de una compañía y solo puede ser ejecutada si se ha autenticado con el usuario administrador de compañía.

```
Request Method: GET

Request URL: http://debserver/api/user/companyUsers

Request Headers:
    Accept: application/json
  
Request Body:

Response Body:
[
  {
    "id": "ID DE USUARIO",
    "workers": 
    [
     {
        "id": "ID DE PUESTO",
        "name": "NOMBRE DE PUESTO",
        "branch": 
        {
          "id": "ID DE SUCURSAL",
          "district": "NOMBRE DE SUCURSAL"
        }
      }
    ],
    "branches": 
    [
      {
        "id": "ID DE SUCURSAL",
        "district": "NOMBRE DE SUCURSAL"
      }
    ],
    "name": "NOMBRE DE USUARIO",
    "email": "EMAIL DE USUARIO"
  }
]
Status code: 200 OK
```

#### Actualización de turnos
Existe la posibilidad de conectarse al servidor para unirse a un canal que permite el envío de eventos desde el servidor hacia el cliente. Este canal es útil para comunicar nuevos turnos.

Para conectarse al canal de comunicación es necesario usar un cliente web socket y estar autenticado.

***Url de conexión :*** ws://debserver/api/appSync

El servidor actualmente no soporta recibir mensajes, al recibir un mensaje contesta con un eco. Es importante que si se desea testear el eco enviar el mensaje en formato json, de lo contrario no responderá.

La siguiente lista muestra las acciones que el servidor puede llegar a notificar

```

 * NEW_TURN             //Alerta de máximo tiempo de espera excedido

```

El formato con el que se notifica es el siguiente

Por ejemplo para la acción de ***Nuevo turno***

```

{
    'action' : 'NEW_TURN',
    'turn' :
    {  
        "id":"Id de turno",
        "queue":
        {  
            "id":"Id de fila",
            "name":"Nombre de fila"
        },
        "waitingRoom": 
        {
            "id": "Id de sala",
            "name": "Nombre de sala"
        },
        "letter":"Letra",
        "number":"Numero",
        "initTimeStamp" : "Inicio"                                  //Hora en la que fue sacado el turno
    }
}

```

#### Errores
El servidor se maneja con cuatro ***status codes***

***2xx Success***

* ***200 OK*** : Este código se devuelve solo cuando la request fue ejecutada correctamente.

***4xx Client Error***

* ***400 Bad Request*** : Este código se devuelve siempre que el cliente ejecuto erróneamente una request, esto se puede deber por ejemplo a que faltan especificar parámetros o a una falta de un request header.
* ***401 Unauthorized*** : Este código se devuelve cuando el token no es valido.
* ***404 Not Found*** : Este código se devuelve cuando se trata de acceder a una ruta invalida.

***5xx Server Error***

* ***500 Internal Server Error*** : Este código se devuelve cuando ocurrió un error inesperado en el servidor, generalmente se deben a que el servidor no puede completar la operación requerida debido a que hay una transacción en curso en la base de datos.

Siempre que haya un error, ya sea por ***client error*** o por ***server error*** el servidor responde en el body de la response un objecto en formato json siempre con la siguiente estructura

```
Request Method: 

Request URL: 

Request Headers:
    Accept: application/json
    Cookie: Token

Request Body:

Response Headers:
    Content-Type : application/json

Response Body:
{
   "key" : "Nombre identificador del error." ,
   "message" : "Mensaje explicativo del error."
}

Status code:


Keys:
    UnexpectedError
    CreationError
    InvalidJson
    BadParameters
    ValidationError
    InvalidBody
    InvalidRoute
    Unauthorized
    BadContext
    ActiveTransaction
```
