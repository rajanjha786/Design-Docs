## Architecture of BookShop Application

```mermaid
---
title: "Architecture of Bookshop Application" 
---

flowchart

Customer["Customer
    [Person]

A customer of the Book Shop Application"]

Employee["Employee
    [Person]
    An Employee of the Book Shop Application"]

subgraph BS [Book Shop]

ES["Edge Service
[Container: Spring Boot]

Provides API Gateway and cross-cutting concerns"]

CS["Catalog Service
[Container: Spring Boot]

Provides functionality for managing the books in the catalog"]

OS["Order Service
[Container: Spring Boot]

Provides functionality for purchasing the books"]

DS["Dispatcher Service
[Container: Spring Boot]
Provides functionality for dispatching orders"]

SPA["SPA
[Container: Angular]
Provides Book Shop functionality to users"]

CnS["Config Service
[Container: Spring Boot]

Provides centralized Configuration"] 

CnRepo[("Config Repo
[Container: Git]

Stores Configuration Data
")]

OD[("Order Database
[Container: PostgreSQL]

Stores order data")]

CD[("Catalog Database
[Container: PostgreSQL]

Stores book data")]

EB["Event Broker
[Container: RabbitMQ]
Provides event processing functionality"]

SS[("Session Store
[Container: Redis]
Stores web session data and cache")]
end

Customer-- "Uses\n[REST/HTTP]" -->ES
Employee-- "Uses\n[REST/HTTP]" -->ES

ES-- "Delivers to the user's web browser" -->SPA
ES-- "Makes API calls to\n[REST/HTTP]" -->CS
ES-- "Makes API calls to\n[REST/HTTP]" -->OS
ES-- "Reads from and writes to\n[RESP]" --> SS

CS-- "Reads from and writes to\n[JDBC]" -->CD
CS-- "Gets configurations from\n[REST\HTTP]" -->CnS

CnS-- "Reads config data from[REST/HTTP]" --> CnRepo

OS-- "Reads from and writes to\n[R2DBC]" -->OD
OS-- "Reads from and writes to\n[AMQP]" -->EB
OS-- "Makes API calls to\n[REST\HTTP]" -->CS

DS-- "Gets configurations from\n[REST\HTTP]" -->CnS
DS-- "Reads from and writes to\n[AMQP]" -->EB


classDef person fill:#08427b,stroke:#052e56,color:#ffffff
class Customer,Employee person


```