---
tags: WebBasics
---
# **Health Check API**

You have applied the [Microservice architecture pattern](https://medium.com/@madhukaudantha/microservice-architecture-and-design-patterns-for-microservices-e0e5013fd58a). Sometimes a service instance can be incapable of handling requests yet still be running. For example, it might have run out of database connections. When this occurs, the monitoring system should generate an alert.

#### **How to detect that a running service instance or HTTP server is unable to handle requests?**


---


In a microservice MYSQL, Redis, and RabbitMq is used, then how to make sure MYSQL, Redis, and RabbitMQ are alive.

Create an API for the health check of the server. In which we are checking the connection of MYSQL, Redis, and RabbitMQ.


## **How to check MySQL Health?**




MySQL Health check code in NodeJs with TypeScript.
```
function mySQLHealthStatus(): Promise<boolean> {
    return new  Promise((resolve, reject) => {
        try {
            mysqlconnection.query('SELECT 1', function (error, results) {
                if (error)
                    throw error;
                resolve(results[0]["1"] == 1);
            });
        } catch (error) {
            reject(error)
        }
    });
```
> mySQLHealthStatus() Method check the health of MySQL by run  **"SELECT 1"** query.
> **if** OUTPUT is **1(ONE)** return **true**  
> **else** return **false**.



## **How to check Redis Health?**



Redis Health check code in NodeJs with TypeScript.

```
function redisHealthStatus(): Promise<boolean> {
    return new Promise((resolve, reject) => {
        try {
            redisClient.ping((error,res)=>{
                if(error)
                throw error;
                resolve(res=="PONG");
            });
        } catch (error) {
            reject(error)
        }
    }
}
```
> redisHealthStatus() Method check the health of Redis by run  Redis **"PING"** CMD.
>**if** OUTPUT is **"PONG"** return **true** 
>**else** return **false**.

## **How to check RabbitMQ Health?**

RabbitMQ Health check code in NodeJs with TypeScript

```
function rabbitMQHealthStatus(): Promise<boolean> {
    return new Promise((resolve, reject) => {
        try {
            amqp.connect({ port, hostname, vhost, username, password }, (error, connection) => {
                if (error)
                    throw error;
                resolve(true);
                connection.close();
            });
        } catch (error) {
            reject(error)
        }
    }
}
```
> rabbitMQHealthStatus() Method check the health of RabbitMQ by make a **connection** with RabbitMQ server, because there are many way to check health of RabbitMQ but these is simplest way to check health of RabbitMQ
>**if** there is no error in connection making return **true** 
>**else** return **false**.

## **Now create an API for a health check of server in ExpressJs.**


```
import * as express from 'express';
 
let router = express.Router();
 
router.post('/healthCheck', async function (req, res) {
    try {
        const rabbitMQHealth = await rabbitMQHealthStatus();
        const redisHealth = await redisHealthStatus();
        const mySQLHealth = await mySQLHealthStatus();
        res.send(rabbitMQHealth && redisHealth && mySQLHealth);
        return;
    }
    catch (error) {
        res.statusCode = 502;
        res.send(false);
        return;
    }
});
```
