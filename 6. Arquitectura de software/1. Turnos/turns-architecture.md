# Arquitecture componente de turnos

## Flujos principales : 

1. **Enturnar:**

     ![Enturnar][enturnar-routes]

     1. Administrador de flota se loggea en la aplicación.
     2. Administrador de flota consulta los assets de la compañía a la cual pertenece y tiene acceso. ( debe validarse por medio del JWT que este usuario pertenezca a esa compañía y es el usuario que dice ser).
     3. Administrador de flota consulta los assets que ya se encuentran enturnados para su compañía y a los cuales tiene acceso.
     4. El administrador crea un turno con la siguiente información : 
        * Disponible desde : Fecha desde la cual el transportador se encontrará disponible para realizar un viaje.
        * Origen : Lugar desde el cual el transportador está dispuesto a realizar la carga.
        * Destinos : Lugar a los cuales el transportador puede viajar.
        * Vehiculo : Vehículo que transportará la carga.
        * Conductor : Conductor que transportará la carga.

     5. (Externo: Informativo) El componente de turnos emite un evento de integración, este contiene la información referente a el turno.
     6. (Externo: Informativo) El componente de shipping escucha el evento e inicia una busqueda de las solicitudes de servicio creadas que cumplen con los criterios del turno creado. En caso de encontrar un match, este utilizará el componente de notificaciones para notificar al administrador de flota para conocer su interés en esta solicitud de servicio.

2. **Seleccionar turno:**

    ![Seleccionar][seleccionar-turno]

    1. Despachador se loggea en la aplicación.
    2. Despachador seleccionar el turno que considera apto para realizar el viaje.
    3. El componente de turnos revisa que el turno no haya sido asignado y en caso que no lo esté emitirá un evento de integración indicando que un turno fue seleccionado.


    4. (Externo: Informativo) El componente de shipping escucha el evento y realiza las siguientes tareas de manera asíncrona:
        * Utiliza el componente de notificaciones para notificar el conductor y administrador de flota para informarles que fueron seleccionados para realizar el transporte de dicha solicitud de servicio.
        * Deberá actualizar la solicitud de carga para incluir la información relacionada con el conductor, vehículo, etc.
        * Deberá remover de las solicitudes de carga activas donde el turno se encontraba como posible candidato.

3. **Cancelar turno:**

    ![Cancelar][cancelar-turno]

    1. Administrador de flota se loggea en la aplicación.
    2. Administrador de flota selecciona el turno que desea cancelar.
    3. El componente de turnos revisa que ese turno no se encuentre asignado a ninguna solicitud de carga, es decir que haya un compromiso de transporte de una solicitud de carga, en caso que no se encuentre asignado deberá emitir un evento de integración indicando que el turno ha sido cancelado, en caso contrario deberá se deberá indicar que tendrá una penalidad y el usuario deberá confirmar al eliminación del turno.
    4. (Externo: Informativo) El componente de shipping escucha el evento, el componente debe remover el turno(candidato) de las solicitudes de carga activas.


## Arquitectura 

1. Alto nivel: 

    ![High Level][high-level]

    1. Services : Son los servicios encargados de realizar los flujos principales del componente.
        * Enturnar
        * Seleccionar turno
        * Cancelar turno
    2. Listeners : Son servicios encargados de realizar la integración con eventos externos a el componente.
        **TBD**


2. Detallado.
    1. Services : Seguirá una arquictura cebolla y este tendrá las siguientes capas:
        1. IUGO.Turns.API.
            * Rutas para acceder a los recursos :
            * Valida la seguridad con el componente *security* al realizar las operaciones.
        2. IUGO.Turns.Application.
            * Tiene los servicios con la lógica para realizar los flujos mencionados.
        3. IUGO.Turns.Domain.
            * Contiene el modelo de negocio de los turnos.
            * Define el repositorio para los turnos.
        4. IUGU.Turns.Infrastructure.  
            * Contiene las implementaciones de los repositorios definidos en Domain.
            * Contiene la implementación para la emisión de eventos de integración.
            * Contiene la referencias a cualquier paquete externo a el componente de turnos. (EntityFramework, ServiceBus)

[enturnar-routes]: ./assets/enturnar-routes.png "Enturnar"

[seleccionar-turno]: ./assets/seleccionar-turno.png "Seleccionar"

[cancelar-turno]: ./assets/cancelar-turno.png "Cancelar"

[high-level]: ./assets/turns-high-level.png "High Level"
    