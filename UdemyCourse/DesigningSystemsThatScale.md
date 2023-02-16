Separating out the DB from web server

## Scaling Web Servers

### Horizontal vs Vertical Scaling:
**Vertical Scaling**  
Pros:  
- Number of Host to maintain is less. 
 
Cons:   
- Single point of failure    

**Horizontal Scaling**  

Pros:
- High traffic can be handled.  
- if one is down then traffic re-directed to another one with help of Load balancer. 
 
Cons:  
- Number of Host to maintain increases.  

Easier if web server is stateless. Means Subsequent request 
should not depend on something being stored on that server from previous request.

Stateless means any web server can handle any request at any time.

Where are Servers located?
- Provisioned within you own compnay's data centers
- Cloud Services eg: EC2, Googe Cloud Engine, Azure VM's
- Fully managed serverless service 
eg: Lambda(you give snippets of code to run)
Kinesis (streaming data)
Athena (Data Warehouse product: allows to query data warehouse or data lake)

## Scaling Database  

Cold Standby
- Periodic backup

Warm Standby
- Always got a copy of data from DB.

Hot Standby
- Instead of relying on replication, write data simultaneously from our web server to every DB instance.
- It can be used to read data(closer to horizontal scaling)

### Horizontal Scaling of Databases: Sharding

<img width="911" alt="image" src="https://user-images.githubusercontent.com/32810320/219420207-e4785e56-5f7b-48ae-b4ba-78b8b5c8bf95.png">

Client direct request to one of the shard(it is horizontal partition of DB).  
Scalable and Resilient  

#### MongoDB:  

We have application server processes running on each server.
Each Application server has mongos process whose job is to distribute data among  
multiple replica sets(Shard) withing MongoDB. Every shard has a primary server and   
multiple secondary servers as well. So Primary server acting as router within that shard.

3 config server to know which is primary, and partitioning mechanism. Mongos talk to primary Config server. But if primary goes down, then hosts can elect a new primary server.

#### Cassandra:

It has ring system. Data is split up into all these different nodes.  
Any node can act as primary so each node needs to know about every data and it is achieved through eventual consistency.  

Giving Availability but trading off with consistency. 

## NoSQL

Sharded databased are sometimes called NoSQL.  
- Tough to do joins across shards.
- Resharding when addition of new shards to re-distribute data.
- Hotspots(Celebrity problem)
- Most NoSQL DB actually do support most SQL operations and use SQL as their API.
- Works best with simple key/value lookups.
- A formal schema may not be needed.
- Examples- Mongodb, DynamoDB, Cassandra, HBase











