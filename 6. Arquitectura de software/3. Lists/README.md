#IUGO LISTS ARCHITECTURE
Microservice that provides general lists of application master data entities such as: 

1. Geographic Information.
2. Identification Types.
3. Branches.

It exposes the **Web API** component.
Uses the **IUGO Security** component that validates that a specific user is in a specific role using the **Yuxi security** component for any call to the exposed methods inside the **Web API** component.

It has a **Data Access** component that is implemented using Entity Framework Code First and saves data into an **Azure SQL Server Database**.

![list architecture][list-architecture]
