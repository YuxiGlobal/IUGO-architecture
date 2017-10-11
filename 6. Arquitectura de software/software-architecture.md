# 6. Arquitectura de software

 ![IUGO Architecture][iugo-architecture]
 
1. **Yuxi Components :**
     Son los componentes genéricos que Yuxi posee para el desarrollo de sus aplicaciones.

     * Notifications : Servicio multitenant de notificaciones de email, sms y push notifications.
     * Security : Servicio multitenant de seguridad, permite almacenar usuarios y sus claims, además permite generar JWT.
     * Rating: Servicio multitenant de rating, permite calificar diferentes actores de una aplicación.
     * Assets: Servicio multitenant de assets, permite almacenar assets en azure y disco, es extensible para utilizar cualquier tipo de almacenamiento.

2. **Cross-Cutting Concerns:**
    Son todos los componentes utilizados transversalmente por la aplicación y no hacen parte del negocio.

    * Notifications : Proxy del servicio de notificaciones de Yuxi, su principal tarea es el registro de templates y envío de notificaciones de IUGO.

    * Security: Proxy del servicio de seguridad de Yuxi, su principal tarea es el registro de usuarios y claims de IUGO:

    * Rating: Proxy del servicio de rating de Yuxi, su principal tarea es al creación de las encuestas y almacenamiento de las respuestas a ellos.

    * Service Bus: Permite la comunicación asíncrona entre microservicios de la aplicación.

3. **Administration:**
    Son los componentes utilizados para la creación de maestros de la aplicación.

    * [Company] : Componente relacionado con las empresas de transporte.
    * Transportation Assets : Componente relacionado con los activos de una empresa de transporte  : vehículos, conductores, accesorios...
    * [Lists] : Componente con los maestros para listas comunes como :  regiones, departamentos, ciudades...
    
4. **Transactional:**
    Son los componentes transaccionales de la aplicación.

    * [Turnos] : Componente encargado del enturnamiento y desenturnamiento de los activos de transportes.

    * [Shipping] : Componente encargado de la creación de solicitudes de servicio, la asignación de los transportadores y cambio de estado de las solicitudes de servicio.

    [iugo-architecture]: ./assets/IUGO-architecture.png "IUGO Architecture"
    

    [Turnos]: ./1.%20Turnos/turns-architecture.md
	[Shipping]: ./2.%20Solicitudes%20de%20Servicio/README.md
	[Lists]: ./2.%20Lists/README.md
	[Company]: ./2.%20Company/README.md
