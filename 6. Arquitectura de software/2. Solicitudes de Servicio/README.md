# Arquitectura componente Solicitudes de Servicio / Shipping Requests

A continuación se describen los flujos de la aplicación y la arquitectura a nivel general que tendrá el módulo de envíos

## Flujos principales

### 1. Crear Solicitud de servicio
![secuencia solicitud de servicio][sequence-shipping-request]

#### Prerequisitos: 
* El usuario ha ingresado a la aplicación como despachador
* El usuario ha ingresado al formulario de creación de Solicitud de Servicio

#### Descripción:
1. **Despachador** ingresa la información de la solicitud de servicio, con las condiciones del camión(es) que se requieren para el transporte.
2. **Despachador** selecciona la opción "Publicar" (Buscar candidatos/Asignar) lo cual hace la petición a el Web Api de *Shipping*.
3. **Shipping API** emite un evento del dominio que estará escuchando el *Web Job de enturnamiento* para saber si alguno de los activos enturnados corresponden con las características de la solicitud entrante.
4. **Turns.Integration** utiliza el Notification Proxy para enviar una notificación de *Oferta* a lo administradores de los activos que se seleccionaron en el paso anterior.
5. **Administrador de flota** recibe la notificación de *Oferta* con la información del envío a realizar y el flete y la acepta en caso de esta interesado, lo cual desencadena un llamado a la **Shipping API** con el candidato interesado.
6. **Shipping API** emite un evento del dominio con de *Canditado ha aceptado* lo cual va a permitir que un Web Socket que se encuentra publicado en el módulo de shipping emita un nuevo mensaje con el nuevo candidato.
7. **FrontEnd** va a estar subscrito a todos los eventos lanzados por el **WebSocket** mostrando la información de los candidatos que llegan de acuerdo a la respuesta de **Turning Service**
8. *Alternativa* Si en algún momento un **Administrador de Flota** enturna un *activo* entonces el WebJob de enturnamiento debe estar suscrito al evento y si el *activo* que está llegando tiene las condiciones necesarias para ser candidato de una *solicitud de servicio* que se encuentre publicada y pendiente de asignar entonces se le envía la notificación al administrador de carga de este activo y correrían los mismos pasos desde el **numeral 5**.

# 2. Cambiar estado carga

![secuencia cambiar estado carga][sequence-change-status]

#### Prerequisitos: 
* El usuario ha ingresado a la aplicación como conductor o como despachador (el ejemplo va a ser tomado como conductor)
* El usuario ha ingresado tiene asignado un 'Paquete' o 'Servicio'

#### Descripción:
1. **Conductor** Está en la aplicación móvil y como se encuentra en *proceso de transporte de paquete* se le mostrarán algunos botones para cambiar los estados de la carga, el primero, que es antes de recoger la carga mostrará `RECOGIDA`.
2. **App Mobil** enviará una petición al Shipping API notificando el nuevo estado, en este caso sería `cargaRecogida`.
3. Para todos los estados se seguirá el mismo proceso de los pasos 1 y 2.
4. En caso de que el **Conductor** notifique que el estado de la carga es `Entregado` el *Shipping API* emitirá un evento `Package delivered` que será escuchado por el WebJob de Rating y generará las encuentras asociadas a este proceso para los actores involucrados.

________________________________


## Software architecture
 ![Arquitectura del modulo][shipping-general-architecture]

[sequence-shipping-request]: ./assets/sequence-shipping-request.png "Secuencia solicitud de servicio"

[sequence-change-status]: ./assets/sequence-change-status.png "Secuencia cambiar estado carga"

[shipping-general-architecture]: ./assets/shipping-general-architecture.png "Arquitectura del modulo"
