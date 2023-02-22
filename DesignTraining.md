This is notes from design pattern traning by Krishna Mohan Koyya.  
Important Link:  
https://microservices.io/.  
https://12factor.net/.   
https://www.opensourceforu.com/2021/11/apache-kafka-asynchronous-messaging-for-seamless-systems/.  
https://bitbucket.org/glarimy/design/src/master/.  


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
- pre and post processing like proxy for example: protocol conversion- If service can only handle http and now required to handle https too. One sol: micros-service to convert https to http.
- client-facing(between client and micro-service)
- ex: istio gives several side car.

## Ambassador
if between micro-services
- upstream

# Patterns for Messaging

<img width="907" alt="image" src="https://user-images.githubusercontent.com/32810320/220291891-bc58274f-06aa-4be8-a482-d517dc2adb05.png">

<img width="863" alt="image" src="https://user-images.githubusercontent.com/32810320/220292284-5f7795b1-a164-48fa-b06d-0baf6adbaeda.png">

<img width="1062" alt="image" src="https://user-images.githubusercontent.com/32810320/220292593-a2fc0893-74d0-4400-8d49-0ce14b46e188.png">

<img width="811" alt="image" src="https://user-images.githubusercontent.com/32810320/220292846-da406a8a-ae3f-478e-9080-abd250f4c425.png">

Variable timing: To avoid sender blocked in case server is busy with request.  

<img width="853" alt="image" src="https://user-images.githubusercontent.com/32810320/220293329-7a94862d-d280-4353-b206-89c79ff859ac.png">

<img width="1102" alt="image" src="https://user-images.githubusercontent.com/32810320/220293472-fec42728-802f-4208-bcf6-bbd07066273c.png">

<img width="859" alt="image" src="https://user-images.githubusercontent.com/32810320/220294438-0937e04a-dc99-480f-9285-77a04c9b0526.png">

<img width="853" alt="image" src="https://user-images.githubusercontent.com/32810320/220295256-a0ec0116-79bd-479c-bbdf-c8239a655c1f.png">

<img width="1130" alt="image" src="https://user-images.githubusercontent.com/32810320/220295362-c2e92317-0b30-47df-b23c-9f8284f22fb7.png">

<img width="944" alt="image" src="https://user-images.githubusercontent.com/32810320/220296963-3b983cec-6db6-410d-95ce-a33fcf0f028f.png">

<img width="1071" alt="image" src="https://user-images.githubusercontent.com/32810320/220297002-0bc1d871-7268-4e4d-846b-796340e6b155.png">

<img width="1175" alt="image" src="https://user-images.githubusercontent.com/32810320/220297050-4c288ab8-3aea-4042-b7f2-671360a40cf9.png">

<img width="982" alt="image" src="https://user-images.githubusercontent.com/32810320/220297107-6aa267ac-4c31-412d-a2e5-185718999332.png">

Scatter gather 2 way, msg broker one way

<img width="1043" alt="image" src="https://user-images.githubusercontent.com/32810320/220297305-6d352b28-0fa2-4621-9375-86eaf78bf5e7.png">

<img width="1068" alt="image" src="https://user-images.githubusercontent.com/32810320/220298327-8c366d3d-44ee-4e4a-9224-1939b75a0331.png">

<img width="1121" alt="image" src="https://user-images.githubusercontent.com/32810320/220299658-2141334c-7383-4de9-abc7-71324ab146e7.png">

<img width="594" alt="image" src="https://user-images.githubusercontent.com/32810320/220300093-fbb83cd5-823b-4c96-bb71-4a007be87f19.png">

# Patterns for Distributed Systems

## Apache Kafka
https://www.opensourceforu.com/2021/11/apache-kafka-asynchronous-messaging-for-seamless-systems/

## Scatter-Gather
- For Map Reduce

## Leader-follower
- In Raft, Kafka
- Follower follow leader

<img width="394" alt="image" src="https://user-images.githubusercontent.com/32810320/220272454-09290f47-76cc-4839-9494-432675518207.png">

## Master-slave
- Master - accepts request and schedule on slave. 

<img width="382" alt="image" src="https://user-images.githubusercontent.com/32810320/220271447-c06d9ba2-e165-47dc-95e9-d687b03620a6.png">

## Heart-beat
- leader send heart-beat to follower.

## Generation clock
- every leader will have it, when new leader is elected, it will increment the clock from what was to previous leader. 
- when old guy come back, it will have older generation, as ack of heart-beat, it re-transfer the ownership or clock value to older leader(did not listen to the lecturer clearly)

## Zookeeper, Redis
- for metadata caching for distributed system.

## Quorum
- 2f + 1 where f is failover count. 3 node will handle 1 failover.

## Lease

## Segmented Log 
- Split log into multiple smaller files instead of a single large file for easier operations.

## Write-Ahead Log
- Provide durability guarantee without the storage data structures to be flushed to disk, by persisting every state change as a command to the append only log.

## Replicated Log
- Keep the state of multiple nodes synchronized by using a write-ahead log that is replicated to all the cluster nodes.
- https://martinfowler.com/articles/patterns-of-distributed-systems/replicated-log.html

## Sharded Services


# Patterns for Concurrent Systems

## Active Object

Provide Synchronous interface to client to accomplish task asynchronously.

**ACT**: Async Completion Token. Populated by thread which is executing the task. In node js: called promise, In other language: future

<img width="746" alt="image" src="https://user-images.githubusercontent.com/32810320/220316210-c1bc404c-f75f-40f3-af63-643c21b28eac.png">

## Thread Pool
- Multiple threads to accomplish task quickly.

## Lock
- Pessimistic, re-entrant lock etc

## Monitor Object
- Shuffles lock among different people, and lock allows and disallow multiple people to access critical section.
- In java, uses `synchronised` keyword for monitor.

## Proctor
- proactively do something and fire an event.
- ABC proactively monitor the file and upon reading content fires the event.  

<img width="499" alt="image" src="https://user-images.githubusercontent.com/32810320/220321253-1bf30882-b1be-4a5f-ab25-2bd3d3aa0133.png">


## Reactor
- to do something as reaction to event.  
- reading file as reaction to event(when data is in file).
 
<img width="448" alt="image" src="https://user-images.githubusercontent.com/32810320/220320067-e138ccf0-834b-43c2-a76a-f43cdd396cfa.png">

## Scheduler
- On which order task in executor is executed. ex: based on timestamp, priority.

## Thread-Specific Storage
- Thread local: global variable whose values are local to thread. 

## Acceptor-Connector
- to establish connection etc 
- The Acceptor-Connector design pattern decouples the connection and initialization of cooperating peer services in a networked system from the processing performed by the peer services after they are connected and initialized.

## Interceptor
- for pre and post processing.
 

## Lamport Clock(Logical Clock)
- related to distributed DB
- establishes partial order among node particular to a transaction agnostic to the local time.
- to determine the order of events occurring.
- N1, N2, N3 all will have its own clock, that starts with something say 0.
- Whenver you are sending a request, you increment the clock.
- Whenever you receive a request, you synchronise the clock by finding `max(its own clock, what it recieve)`.
 
## Hybrid Clock
- Clock value as well as machine time



