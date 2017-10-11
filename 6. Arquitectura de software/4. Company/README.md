# IUGO Company Architecture

![company architecture][company-architecture]

Microservice that provides the domain logic for transportation companies.

It exposes the **Web API** component.

Uses the **IUGO Security** component that validates that a specific user is in a specific role using the **Yuxi security** component for any call to the exposed methods inside the **Web API** component.

It has an **Infrastructure** layer that provides common utilities functionality in the **Utils** component and data access layer in the **Data Access** component.

The **Data Access** component is implemented using Entity Framework Code First and saves data into an **Azure SQL Server Database**.

The domain core functionality is inside the **Domain** layer in is accessed only by the **Services** component that provide functionality that involves the domain.

In Azure there is a web job that triggers a service inside the **Services** layer when a dispatcher was rated. This service refreshes the company rating. 
