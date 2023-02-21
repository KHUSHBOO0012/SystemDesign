This is notes from design pattern traning by Krishna Mohan Koyya.  
Important Link:  
https://microservices.io/.  
https://12factor.net/

## Story of Architecture
#### Monoliths
-	Developed and deployed as one piece.
#### Modular
-	Module with well-defined interfaces
-	Developed independently but one image for deployment.
#### Client/Server 
-	Thick client: Client required some installation that knows proprietary protocol to talk to server, language and platform specific.
#### Web 
-	Thin client: Browser
-	HTTP protocol for communication.
#### Distributed:
-	On server, we can always add additional capabilities without disturbing client. Bulky server, So divide it in multiple server, But for outsiders, it looks like single unit.
-	Each server known as node.
-	Talk to each other through network/RPC.
-	Nodes can act as client or server
#### SOA:
-	Node as endpoint.
-	Operation centric
-	XML over HTTP for communication.
-	WSDL(service definition language) required to know the request and response format of any service before communication.
-	These endpoints can be developed by different vendors.
#### REST: 
-	Resource centric
-	Synchronous by default
-	Can become distributed monolith if not handled properly with event broker etc.
#### Event Driven: 
-	Event sent to broker and broker send this to services.
-	Make request asynchronous.
-	More Throughput
#### Microservices: 
-	Services are smaller
-	developed independently
-	deployed independently.
-	Can be scaled independently.
#### Cloud: 
-	Servers in cloud, infrastructure as service, No longer need to maintain your own infrastructure, DB etc.
#### Serverless: 
-	Just give your event handler i.e. function and tell how much requests to handle. Example: Lambda

## Prime Factors for Modern Architecture
Scalability: You should be able to handle more request with increase in hardware. 
Vertical- enhance power of machine, Horizontal Scaling- distirbute load on multiple machine

Resilience: What happens if service go down. Somehow it should identify that something is wrong and repair itself. 
Like multiple instances of the service.

Stability
Ability to grow  
Service Integration. 

## E-commerce Application

<img width="442" alt="image" src="https://user-images.githubusercontent.com/32810320/220064843-d14d8311-8379-440a-85d3-39f51888b3df.png">
Fig1: Monolith 

<img width="441" alt="image" src="https://user-images.githubusercontent.com/32810320/220064929-ae60abf6-7792-4a3f-b7d4-a0cd5ed2365c.png">

Fig2: Synchronous Micro-services

Wallet Service: PUT for debit and that would handle insufficient balance etc.
Order Service: POST, PUT (in case order failed to update status) and GET for order
Product Service: PUT for reserve and release. 

# Data Management Patterns 

**DB per service**: 
DB number increase and Management cost increases

**Shared DB**:
Management easy

**2-Phase Commit** in case of multiple DB, operation as part of single transaction, 
Start transaction, do operation and commit transaction

In memory transaction, followed by commit or rollback.
Not all DB support transaction.

## SAGA

- Series of local transaction, typically done in micro-services (in case could not implement as 2 phase commit)
- Update db, Reserve inventory, Wallet DB update, confirm order
- Compensatory transaction: Upon orchestration failure, add reserved product back, second transaction happened as different commit. 
- Eventual consistency

2 ways of implementing SAGA pattern when DB is per service:  

**Orchestration**: for large number of micro-services and is preferred one.
- Orchestrator talks to different services. 
- In case of failure, does compensatory transaction and fail order.
<img width="344" alt="image" src="https://user-images.githubusercontent.com/32810320/220068841-38902c11-6b0a-4644-a7d3-a279e5df7611.png">


**Choreography**: for 2-3 micros-services only as it is complex to debug,

<img width="363" alt="image" src="https://user-images.githubusercontent.com/32810320/220068787-722a04f3-d7ce-47b3-bf58-8f23c038ea4f.png">

## Event Driven Approach(Asynchronous)
- Calls not directly using HTTP, rather dealing with event.  
- Requester fires event and responser too fires event when done with request
- Saga command channel for order and orchestrator. 
- In starting order in pending state, and system work on that order with help of orchestrator.
- Depending on saga.results, order status is updated.
- Saga DB used to recover orchestrator instance in case it is down
- Ex: Axon
<img width="1199" alt="image" src="https://user-images.githubusercontent.com/32810320/220069327-30fabfc3-8bf1-4783-b510-0a05c8f647e5.png">

## Domain Event
- Message: command(to do something, debit money, release product)
- Event: something already happened that no body can change(debited money)

## CQRS
- Command and query responsibility segregation over DB. 

## Transactional Outbox 
- It uses application server database to add another table outbox where every record is indication that this event need to be published. 
- As single transaction, you update DB as actual record and outbox table to fire an event. 
- Sending duplicate event not a problem as channel would de-dup.

## Event Sourcing 
- When event occurs, as handler you will receive event.
- Keep on piling the event like to-do list. 
- Event Sourcing is a pattern for storing data as events in an append-only log.

## API Composition
- opposite of join in case of DB per service,
- When data need to be fetched from multiple services. 
- make query to one service and the other, do composition and return


# Patterns for Concurrent Systems

These clocks are related to Distributed DB.  
to trace back the event.  

## Lamport Clock(Logical Clock)
- establishes partial order among node particular to a transaction agnostic to the local time.
- to determine the order of events occurring.
- N1, N2, N3 all will have its own clock, that starts with something say 0.
- Whenver you are sending a request, you increment the clock.
- Whenever you receive a request, you synchronise the clock by finding `max(its own clock, what it recieve)`.
 
## Hybrid Clock
- Clock value as well as machine time


# Patterns for Microservices

## Twelve Factor: https://12factor.net/.   
I. Codebase: One codebase tracked in revision control, many deploys.  
II. Dependencies: Explicitly declare and isolate dependencies.  
III. Config: Store config in the environment.   
IV. Backing services: Treat backing services as attached resources.  
V. Build, release, run: Strictly separate build and run stages.  
VI. Processes: Execute the app as one or more stateless processes(one request should not depend on last request).  
VII. Port binding: Export services via port binding.  
VIII. Concurrency: Scale out via the process model.  
IX. Disposability: Maximize robustness with fast startup and graceful shutdown.  
X. Dev/prod parity: Keep development, staging, and production as similar as possible.  
XI. Logs: Treat logs as event streams.  
XII. Admin processes: Run admin/management tasks as one-off processes.  

## Service per Host 
<img width="159" alt="image" src="https://user-images.githubusercontent.com/32810320/220248841-6205c3a2-55a1-4dd4-a40e-8aa093a3d1c9.png">

## Service per VM 
<img width="287" alt="image" src="https://user-images.githubusercontent.com/32810320/220248929-004e7a71-f3e1-4777-9384-2b6d3d5887e1.png">

## Service per Container 
<img width="290" alt="image" src="https://user-images.githubusercontent.com/32810320/220249789-c5cc14dc-f2e9-44e5-b7e1-dcbdf9afa5ea.png">

## Gateway
<img width="621" alt="image" src="https://user-images.githubusercontent.com/32810320/220250750-cbb73f6c-60c8-4407-aa3b-1967b811b8c1.png">

## Registry
<img width="575" alt="image" src="https://user-images.githubusercontent.com/32810320/220253430-a61e7c60-7d6f-4cac-9aff-3f0ed96d043a.png">
Here locator helps gateway with registry info.

## Chassis
Infrastructure which consists of some pattern like below. 

<img width="329" alt="image" src="https://user-images.githubusercontent.com/32810320/220255459-2a6c41e4-8dab-4f05-8649-9ab56916b08a.png">

Same Correlatin id for a request for all services.

Chassis also equipped with log aggregator, listen to logs from each services, log sent as event and aggregated by aggregator, ex: Splunk, log stash,   

## Strangler 

It is gateway only. Seat before services, required while migrating to micro-services from monolith to direct calls by choosing between monolith and newly created micro-services. once migrated, no longer required.

<img width="309" alt="image" src="https://user-images.githubusercontent.com/32810320/220258698-80df0ea2-0c35-4b55-b641-426b772f7dd9.png">

## Kubernetes

Node have Pod, pod contains container. Pod is like machine, they have their address.  

## Sidecar
If service can only handle http and now required to handle https too. One sol: micros-service to convert https to http.
