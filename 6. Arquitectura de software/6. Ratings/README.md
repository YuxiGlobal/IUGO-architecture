## Arquitectura del contexto Ratings

En este contexto se encapsula la funcionalidad de almacenar las Encuestas que se hacen entre todos los Actores del sistema

Se puede ver como un Proxy entre IUGO y el servicio compartido de Yuxi Rating, que es donde finalmente se almacenarán el detalle de todas las calificaciones de cada entidad a ser calificada en el sistema

Se debe tener en cuenta que, el servicio externo llamado Yuxi Ratings se encarga de almacenar el detalle de las calificaciones; de esta manera, este contexto almacenará las preferencias de calificaciones de cada empresa (Tenant)

Se ha pensado que este contexto se comporte como un full DDD

![imagen-arquitectura-contexto]


## Capas de la arquitectura:
* **IUGO.Rating.API**
  * Provee RESTFull services
  * Es el punto de entrada de interacción desde el mundo exterior hacia este contexto
  * Provee funcionalidades de Health Check (para propósitos de monitoreo y balanceo de carga)
* **IUGO.Rating.Services**
  * Provee servicios de lógica de negocio
  * Orquestra llamadas entre las entidades de negocio y los repositorios que la representan
  * Aquí es donde se inicia la Unidad de Trabajo (transacciones)
  * Esta capa también es llamada desde los Azure WebJobs/Functions
* **IUGO.Rating.Domain**
  * Entidades de Negocio, al estilo DDD
* **IUGO.Rating.Infrastructure**
  * Utilidades en común para todo el contexto
  * Código de los Repositorios, hechos con Entity Framework con Code First approach
  
  
* **Componentes externos**
  * Yuxi Rating: Servicio RESTfull que almacena información sobre el detalle de calificaciones de cada actor del sistema
  * Azure WebJobs/Functions: Se prefiere el uso de Azure Functions. Reaccionan a los eventos de integración que están en los Topics del Azure Service Bus
  * Yuxi Security: Es el **Identity Server** que validará cada petición, en forma síncrona, que se hace en la capa de API

[imagen-arquitectura-contexto]: ./assets/Rating Architecture.jpg "Arquitectura de este contexto"