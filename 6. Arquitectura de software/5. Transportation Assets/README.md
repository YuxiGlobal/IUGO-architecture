## Arquitectura del contexto Transportation Assets
En este contexto se encapsula la funcionalidad de almacenar todos los activos de transporte, como por ejemplo los Administradores, los Conductores, los Vehículos y sus relaciones con las Empresas en donde están aprobados operar (que ya hayan sido filtrados previamente).

Este contexto tendrá múltiples etapas de desarrollo, cada una de ellas se describe a continuación : 
1. Carga Manual : Se creará un excel que generará un script de T-SQL para el cargado de base de datos. Este script deberá correr de manera manual.
2. ETL :  No realizaremos validaciones, sino que, por medio de cargas de ETL, tomaremos como verdad lo que vengan en dichos archivos y se realizará de manera automática.
3. Las entidades podrán ser administrables desde la aplicación.


## Capas de la arquitectura:
* **IUGO.TransportationAssets.API**
  * Provee RESTFull services
  * Es el punto de entrada de interacción desde el mundo exterior hacia este contexto
  * Provee funcionalidades de Health Check (para propósitos de monitoreo y balanceo de carga)
  * Esta capa está hecha en **.Net Core 2.0**
* **IUGO.TransportationAssets.Services**
  * Este facade provee funcionalidades a la API para carga de información a partir de un archivo plano
  * Estos métodos también proveen funcionalidades de CRUD
  * Accede a la capa de infraestructura, específicamente a la persistencia por medio del componente de Data Access que encapsula los Repositories
  * Esta capa está hecha en **.Net Standard 2.0**
* **IUGO.TransportationAssets.Infrastructure**
  * Utilidades en común para todo el contexto
  * Código de los Repositorios, hechos con Entity Framework con Code First approach
  * SDK para conectarse con el Yuxi Assets REST service, para almacenar imágenes, videos y assets en general
  * Esta capa está hecha en **.Net Standard 2.0***
* **IUGO.TransportationAssets.Database**
  * Proyecto usado para el modelamiento de la base de datos.
  * Utilizado para la generación del .dacpac para el despliegue de la base de datos.

    
* **Componentes externos**
  * Yuxi Assets: Servicio RESTfull que almacena información sobre assets, tales como imágenes, videos y archivos en general
  * Yuxi Security: Es el **Identity Server** que validará cada petición, en forma síncrona, que se hace en la capa de API


[imagen-arquitectura-contexto]: ./assets/Transport%20Assets%20Architecture.jpg "Arquitectura de este contexto"