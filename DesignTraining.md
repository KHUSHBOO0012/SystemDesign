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
Vertical, Horizontal Scaling

Resilience: What happens in service go down. Somehow it should repair itself, 
Like multiple instances of the service.

Stability:

Ability to grow:

Service Integration:

## E-commerce Application

<img width="442" alt="image" src="https://user-images.githubusercontent.com/32810320/220064843-d14d8311-8379-440a-85d3-39f51888b3df.png">
Fig1: Monolith 

<img width="441" alt="image" src="https://user-images.githubusercontent.com/32810320/220064929-ae60abf6-7792-4a3f-b7d4-a0cd5ed2365c.png">

Fig2: Synchronous Micro-services

Wallet Service: PUT for debit and that would handle insufficient balance etc.
Order Service: POST, PUT (in case order failed to update status) and GET for order
Product Service: PUT for reserve and release. 

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
- In case of failure, does compensatory transaction and fail order
<img width="344" alt="image" src="https://user-images.githubusercontent.com/32810320/220068841-38902c11-6b0a-4644-a7d3-a279e5df7611.png">


**Choreography**: for 2-3 micros-services

<img width="363" alt="image" src="https://user-images.githubusercontent.com/32810320/220068787-722a04f3-d7ce-47b3-bf58-8f23c038ea4f.png">

## Event Driven Approach(Asynchronous)
- Calls not directly using HTTP, rather dealing with event.  
- Requester fires event and responser too fires event when done with request
- Saga command channel for order and orchestrator. 
- In starting order in pending state, and system work on that order with help of orchestrator.
- Depending on saga.results, order status is updated.
<img width="1199" alt="image" src="https://user-images.githubusercontent.com/32810320/220069327-30fabfc3-8bf1-4783-b510-0a05c8f647e5.png">








 
