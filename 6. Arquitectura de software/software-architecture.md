# 6. Arquitectura de software

 ![IUGO Architecture][iugo-architecture]

1. **General:**
    Se utilizará azure service fabric como plataforma de desarrollo/despliegue y operación. Inicialmente se contará con dos aplicaciones corriendo bajo el mismo cluster : 
    
    * sf:IUGO 
    * sf:Yuxi

    La primera tendrá los microservicios encargados de realizar las operaciones de IUGO y la segunda tendrá los microservicios genéricos para todas las aplicaciones de Yuxi.

    La comunicación entre microservicios transaccionales deberá hacerse de manera asícrona, es decir utilizando el EventBus, y para comunicaciones con microservicios de maestros se tendrá la posibilidad de realizar un llamado sícrono (Http-rest).

    * Comunicaciones asícronas : 
        * Turn 
        * Shipping
        * Notifications
    * Comunicaciones sícronas : 
        * Company
        * Transportation Assets
        * Lists
        * Security

    Para las comunicaciones sícronas se deberá exponer una capa "Interfaces" con las interfaces de los servicios y modelos que el microservicio utiliza, está capa no deberá tener ningún tipo de lógica.

    Se podrá utilizar el siguiente proyecto a manera de ejemplo :

    [Service Fabric POC]( https://github.com/p1p3/azure-service-fabric-example)
   
2. **Yuxi Components:**
     Son los componentes genéricos que Yuxi posee para el desarrollo de sus aplicaciones.

     * Notifications : Servicio multitenant de notificaciones de email, sms y push notifications.
     * Security : Servicio multitenant de seguridad, permite almacenar usuarios y sus claims, además permite generar JWT.
     * Rating: Servicio multitenant de rating, permite calificar diferentes actores de una aplicación.
     * Assets: Servicio multitenant de assets, permite almacenar assets en azure y disco, es extensible para utilizar cualquier tipo de almacenamiento.

3. **Cross-Cutting Concerns:**
    Son todos los componentes utilizados transversalmente por la aplicación y no hacen parte del negocio.

    * Notifications : Proxy del servicio de notificaciones de Yuxi, su principal tarea es el registro de templates y envío de notificaciones de IUGO.

    * Security: Servicio de seguridad de Yuxi, su principal tarea es el registro de usuarios y creación de claims de IUGO:

    * Rating: Proxy del servicio de rating de Yuxi, su principal tarea es al creación de las encuestas y almacenamiento de las respuestas a ellos.

    * Service Bus: Permite la comunicación asíncrona entre microservicios de la aplicación.

4. **Administration:**
    Son los componentes utilizados para la creación de maestros de la aplicación.

    * [Transportation Assets] : Componente relacionado con los activos de una empresa de transporte  : vehículos, conductores, accesorios...
    * [Lists] : Componente con los maestros para listas comunes como :  regiones, departamentos, ciudades...
    
5. **Transactional:**
    Son los componentes transaccionales de la aplicación.

    * [Turnos] : Componente encargado del enturnamiento y desenturnamiento de los activos de transportes.

    * [Shipping] : Componente encargado de la creación de solicitudes de servicio, la asignación de los transportadores y cambio de estado de las solicitudes de servicio.

5. **Despliegue:**
    La aplicación cuenta con un flujo de integración y despliegue continuo, para realizar un despliegue en QA es necesario hacer un merge en la rama de **development** , en el caso de Producción es necesario realizar un merge a la rama **master**. 

    * [Deployment] : Diagrama del despliegue de la aplicación.

    [iugo-architecture]: ./assets/IUGO-architecture.png "IUGO Architecture"
    

    [Turnos]: ./1.%20Turnos/turns-architecture.md
	[Shipping]: ./2.%20Solicitudes%20de%20Servicio/README.md
	[Lists]: ./3.%20Lists/README.md
	[Company]: ./4.%20Company/README.md
    [Transportation Assets]: ./5.%20Transportation%20Assets/README.md
    [Deployment]: ./7.%20Deployment%20Diagrams/README.md
