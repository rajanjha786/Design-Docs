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

## Sequence Diagram of Buying a Book Flow Happy Path

```mermaid
---
title: Buying Book Flow Happy Path
---

sequenceDiagram
    actor C as Customer
    participant ES as Edge Service
    participant OS as Order Service
    participant CS as Catalog Service
    participant DS as Dispatcher Service
    
    C ->> ES: POST /orders
    activate ES
    ES ->> OS: POST /orders
    activate OS
    OS ->> CS: Check if Book Exist GET /book/{isbn}
    activate CS
    CS -->> OS: Book Exist
    deactivate CS
    OS --)  DS: Order Accepted Event Published
    OS -->> ES: Order Accepted
    deactivate OS
    ES -->>C: Order Accepted
    deactivate ES

    activate DS
    DS --) DS: Order Packed and Dispatched
    DS --) OS: Order Dispatched Event Published
    deactivate DS
    activate OS
    OS --) OS: Order Status Updated To Dispatched
    deactivate OS
      
    
```

## Sequence Diagram of Buying a Book Flow When Ordered Book Does not exist in the Catalog

```mermaid
---
title: Buying Book Flow When Ordered Book Does not exist in the Catalog
---

sequenceDiagram
    actor C as Customer
    participant ES as Edge Service
    participant OS as Order Service
    participant CS as Catalog Service
    participant DS as Dispatcher Service
    
    C ->> ES: POST /orders
    activate ES
    ES ->> OS: POST /orders
    activate OS
    OS ->> CS: Check if Book Exist GET /book/{isbn}
    activate CS
    CS -->> OS: Book Does Not Exist
    deactivate CS
    OS -->> ES: Order Rejected
    deactivate OS
    ES -->>C: Order Rejected
    deactivate ES
      
    
```

## Sequence Diagram of Adding a Book in the catalog

```mermaid
---
title: Adding a Book in the catalog
---

sequenceDiagram
    actor E as Employee
    participant ES as Edge Service
    participant CS as Catalog Service
    
    E ->> ES: POST /books
    activate ES
    ES ->> CS: POST /books
    activate CS
    CS -->> ES: Book Created
    deactivate CS
    ES -->> E: Book Created
    deactivate ES
    
```

## Logging Architecture of Bookshop Application

```mermaid
---
title: Bookshop Logging Architecture
---

flowchart
    
App["Applications
[Containers]
"]

FB["Fluent Bit
[Container]
Collects, processes, and forwards logs"]

Loki["Loki
[Container]
Log aggregation system"]

GF["Grafana
[Container]
Platform for querying, visualizing, and alerting on metrics, logs, and traces"]

GF -- "Queries logs from" --> Loki
FB --"Forwards logs to" --> Loki
FB --"Fetches logs from" --> App
```

## Monitoring Architecture of Bookshop Application

```mermaid
---
title: Bookshop Monitoring Architecture
---
flowchart
    
GF["Grafana
[Container]
Platform for querying, visualizing, and alerting on metrics, logs, and traces"]

App["Applications
[Containers]
"]

P["Prometheus
[Containers]
Monitoring System
"]

P --"Scrapes metrics from" --> App
GF --"Queries metrics from" --> P

```

## Distributed Tracing Architecture

```mermaid
---
title: Bookshop Distributed Tracking Architecture
---

flowchart

GF["Grafana
[Container]
Platform for querying, visualizing, and alerting on metrics, logs, and traces"]

App["Applications
[Containers]
"]

T["Tempo
[Container]
Distributed Tracking System"]

GF-- "Queries traces from" --> T
App-- "Sends traces to" --> T
    
```

