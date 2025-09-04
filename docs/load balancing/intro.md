---
sidebar_position: 3
---

# Load balancing
Helps distribute incoming requests and traffic evenly across multiple servers.

The main goal of load balancing is to ensure high availability, reliability, and performance by avoiding overloading a single server and avoiding downtime.

## How it works
1. The load balancer receives a request from the client.
2. It selects a backend server using a load-balancing algorithm.
3. The request is forwarded to the chosen server.
4. The server processes the request and sends the response back to the load balancer.
5. The load balancer returns the response to the client.

```mermaid
sequenceDiagram
  participant Client
  participant LoadBalancer
  participant Server

  Client->>LoadBalancer: Sends request
  LoadBalancer->>LoadBalancer: Evaluate request (algorithm, health, etc.)
  LoadBalancer->>Server: Forward request
  Server->>Server: Process request
  Server->>LoadBalancer: Send response
  LoadBalancer->>Client: Return response
```
## Each Layer
Between the user and the web servers.

Between web servers and an internal platform layer, like application servers or cache servers.

Between internal platform layer and database.
```mermaid
graph LR
    User1["User1"]
    User2["User2"]
    User3["User3"]
    LB1{"Edge Load Balancer"}
    Web1["Web Server 1"]
    Web2["Web Server 2"]
    LB2{"Platform Load Balancer"}
    App1["App/Cache Server 1"]
    App2["App/Cache Server 2"]
    LB3{"Data Load Balancer"}
    DB1[("Database 1")]
    DB2[("Database 2")]

    User1 --> LB1
    User2 --> LB1
    User3 --> LB1
    LB1 --> Web1
    LB1 --> Web2
    Web1 --> LB2
    Web2 --> LB2
    LB2 --> App1
    LB2 --> App2
    App1 --> LB3
    App2 --> LB3
    LB3 --> DB1
    LB3 --> DB2
```

## Key terminology
- **Load balancer**: A device or software that distributes incoming network traffic across multiple servers.
- **Backend server**: The servers that receive and process requests forwarded by the load balancer. Also referred to as the server pool or server farm.
  - Server pool/server farm: This means there are multiple backend servers working together.
  - Using a pool of backend servers allows for horizontal scaling (adding more servers as needed), better fault tolerance (if one server fails, others can continue).
- **Load Balancing Algorithm**: A method used by the load balancer to determine how to distribute incoming requests among the backend servers.
- **Health Check**: Periodic tests performed by the load balancer to determine the availability and performance of backend servers. Unhealthy servers are removed from the server pool until they recover.
- **Session Persistence (Sticky Sessions)**: A technique used to ensure that a user's requests are consistently directed to the same backend server for the duration of their session. This is important for applications that maintain state information on the server side.
- **SSL/TLS Termination**: The process of decrypting SSL/TLS traffic at the load balancer level, allowing backend servers to handle unencrypted traffic. This offloads the computationally intensive task of encryption/decryption from the backend servers.
  - SSL nowadays is mostly replaced by TLS, but the term SSL is still commonly used to refer to both protocols.
    ```mermaid
    sequenceDiagram
        participant Client
        participant Server

        Client->>Server: ClientHello (TLS version, ciphers, random)
        Server->>Client: ServerHello (chosen cipher, random, certificate)
        Client->>Client: Verify certificate (CA, domain, validity)
        Client->>Server: Key exchange (RSA or ECDHE)
        Server->>Client: Key exchange confirmation
        Client->>Server: Finished (encrypted)
        Server->>Client: Finished (encrypted)
        Note over Client,Server: Secure session established 🔒
    ```
