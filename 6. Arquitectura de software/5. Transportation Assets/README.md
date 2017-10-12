## Arquitectura del contexto Transportation Assets

En este contexto se encapsula la funcionalidad de almacenar todos los activos de transporte, como por ejemplo los Administradores, los Conductores, los Vehículos y sus relaciones con las Empresas en donde están aprobados operar (que ya hayan sido filtrados previamente).

El objetivo es que, inicialmente nosotros no realizaremos validaciones, sino que, por medio de cargas de ETL, tomaremos como verdad lo que vengan en dichos archivos.

Se ha pensado que este contexto se comporte inicialmente como un CRUD, puesto que no tendría entidades de negocio. De esta manera, la siguiente gráfica muestra la arquitectura de este contexto:

![imagen-arquitectura-contexto]


## Capas de la arquitectura:
* **IUGO.TransportationAssets.API**
  * Provee RESTFull services
  * Es el punto de entrada de interacción desde el mundo exterior hacia este contexto
  * Provee funcionalidades de Health Check (para propósitos de monitoreo y balanceo de carga)
  * Esta capa está hecha en **.Net Core 2.0**
* **IUGO.TransportationAssets.CRUD**
  * Este facade provee funcionalidades a la API para carga de información a partir de un archivo plano
  * Estos métodos también proveen funcionalidades de CRUD
  * Accede a la capa de infraestructura, específicamente a la persistencia por medio del componente de Data Access que encapsula los Repositories
  * Esta capa también es llamada desde los Azure WebJobs/Functions, para reaccionar a los eventos de integración expuestos por los otros contextos del aplicativo
  * Esta capa está hecha en **.Net Standard 2.0**
* **IUGO.TransportationAssets.Infrastructure**
  * Utilidades en común para todo el contexto
  * Código de los Repositorios, hechos con Entity Framework con Code First approach
  * SDK para conectarse con el Yuxi Assets REST service, para almacenar imágenes, videos y assets en general
  * Esta capa está hecha en **.Net Standard 2.0**
  
  
* **Componentes externos**
  * Yuxi Assets: Servicio RESTfull que almacena información sobre assets, tales como imágenes, videos y archivos en general
  * Azure WebJobs/Functions: Se prefiere el uso de Azure Functions. Reaccionan a los eventos de integración que están en los Topics del Azure Service Bus
  * Yuxi Security: Es el **Identity Server** que validará cada petición, en forma síncrona, que se hace en la capa de API

[imagen-arquitectura-contexto]: ./assets/Transport Assets Architecture.jpg "Arquitectura de este contexto"