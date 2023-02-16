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









