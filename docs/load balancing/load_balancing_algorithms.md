---
sidebar_position: 4
---

# Load Balancing Algorithms

## Round Robin 
Distributes requests sequentially to each server in the pool. Simple

![Round Robin](/img/round_robin.gif)


### Pros:
- Ensures an equal distribution of requests among the servers, as each server gets a turn in a fixed order.
- Easy to implement and understand.
- Works well when servers have similar capacities.

### Cons:
- Ignores server load and capacity.
- No session affinity; clients may hit different servers that each request from the same client can be sent to a different backend server. The load balancer does not keep track of which server handled previous requests for that client. This can be a problem for applications that store user-specific data (session state) on the server.
- Not optimal if servers have different performance.
- Predictable pattern can be exploited by attackers.

### Use Cases
- **Homogeneous Environments**: Suitable for environments where all servers have similar capacity and performance.
- **Stateless Applications**: Works well for stateless applications where each request can be handled independently.

## Least Connections
Load balancing technique that assigns incoming requests to the server with the fewest active connections at the time of the request.

### Pros:
- **Load Awareness**: Takes into account the current load on each server by considering the number of active connections, leading to better utilization of server resources.
- **Dynamic Distribution**: Adapts to changing traffic patterns and server loads, ensuring no single server becomes a bottleneck.
- **Efficiency in Heterogeneous Environments**: Performs well when servers have varying capacities and workloads, as it dynamically allocates requests to less busy servers.

### Cons:
- **Higher Complexity**: More complex to implement compared to simpler algorithms like Round Robin, as it requires real-time monitoring of active connections.
- **State Maintenance**: Requires the load balancer to maintain the state of active connections, which can increase overhead.
- **Potential for Connection Spikes**: In scenarios where connection duration is short, servers can experience rapid spikes in connection counts, leading to frequent rebalancing.

### Use Cases
- **Heterogeneous servers**: Good when servers have different capacities.
- **Variable traffic**: Handles unpredictable or changing loads well.
- **Stateful apps**: Distributes active sessions more evenly.

### Comparison to Round Robin
- **Round Robin**: Distributes requests in a fixed, cyclic order without considering the current load on each server.
- **Least Connections**: Distributes requests based on the current load, directing new requests to the server with the fewest active connections.

![Least Connections](/img/least_connections.gif)

## Weighted Round Robin
WRR is an enhanced version of the Round Robin load balancing algorithm. It assigns weights to each server based on their capacity or performance.

### Pros:
- Distributes requests based on server capacity.
- Flexible—easy to adjust weights as servers change.
- Improves performance by avoiding overload on weaker servers.

### Cons:
- Complexity in Weight Assignment: Determining appropriate weights for each server can be challenging and requires accurate performance metrics.
- Increased Overhead: Managing and updating weights can introduce additional overhead, especially in dynamic environments where server performance fluctuates.
- Not Ideal for Highly Variable Loads: In environments with highly variable load patterns, WRR may not always provide optimal load balancing as it doesn't consider real-time server load (its fixed weights).


### Use Cases
- **Heterogeneous Server Environments**: Ideal for environments where servers have different processing capabilities, ensuring efficient use of resources.
- **Scalable Web Applications**: Suitable for web applications where different servers may have varying performance characteristics.
- **Database Clusters**: Useful in database clusters where some nodes have higher processing power and can handle more queries.

![Weighted Round Robin](/img/weighted_round_robin.gif)

## Weighted Least Connections
An advanced load balancing algorithm that combines the principles of the Least Connections and Weighted Round Robin algorithms. It assigns weights to servers based on their capacity and directs incoming requests to the server with the fewest active connections, adjusted by these weights.

![Weighted Least Connections](/img/weighted_least_connections.gif)


## IP Hash
A load balancing algorithm that uses a hash function to map a client's IP address to a specific server

### Example
Suppose you have three servers (Server A, Server B, and Server C) and a client with the IP address 192.168.1.10. The load balancer applies a hash function to this IP address, resulting in a hash value. If the hash value is 2 and there are three servers, the load balancer routes the request to Server C (2 % 3 = 2).

### Pros
- Keeps client sessions on the same server.
- Simple and easy to implement.
- Predictable routing based on IP.

### Cons
- Can cause uneven load if IPs aren’t distributed evenly.
- Adding/removing servers disrupts client-server mapping.
- Limited Flexibility ignores server load and capacity.

### Use Cases
- **Stateful Applications**: Ideal for applications where maintaining session persistence is important, such as online shopping carts or user sessions.
- **Geographically Distributed Clients**: Useful when clients are distributed across different regions and consistent routing is required.

![IP Hash](/img/ip_hash.gif)

## Least Response Time

A load balancing algorithm that directs incoming requests to the server with the lowest average response time.

### How it works
1. The load balancer monitors server response times.
2. It assigns each new request to the server with the lowest average response time.
3. Assignment is adjusted dynamically based on real-time performance.

### Pros
 - Directs requests to the fastest server.
 - Dynamic Load Balancing, adapts to changing server performance.
 - Effective Resource Utilization, helps in better utilization of server resources by directing traffic to servers that can respond quickly.

### Cons
 - More complex to implement.
 - Adds monitoring overhead.
 - Can rebalance too often if response times fluctuate.


### Use Cases
- **Real-Time Applications:** Ideal for applications where low latency and fast response times are critical, such as online gaming, video streaming, or financial trading platforms.
- **Web Services:** Useful for web services and APIs that need to provide quick responses to user requests.
- **Dynamic Environments:** Suitable for environments with varying loads and server performance.

![Least Response Time](/img/least_response_time.gif)

## Random
Is a simple algorithm that distributes incoming requests to servers randomly. Instead of following a fixed sequence or using performance metrics, the load balancer selects a server at random to handle each request. This method can be effective in scenarios where the load is relatively uniform and servers have similar capacities.

Suppose we have three servers: Server A, Server B, and Server C. When a new request arrives, the load balancer randomly chooses one of these servers to handle the request. Over time, if the randomness is uniform, each server should receive approximately the same number of requests.

### Pros
- **Simplicity**: Very easy to implement and understand, requiring minimal configuration.
- **No State Maintenance**: The load balancer does not need to track the state or performance of servers, reducing overhead.
- **Uniform Distribution Over Time**: If the random selection is uniform, the load will be evenly distributed across servers over a long period.

### Cons
- **No Load Awareness**: Does not consider the current load or capacity of servers, which can lead to uneven distribution if server performance varies.
- **Potential for Imbalance**: In the short term, random selection can lead to an uneven distribution of requests.
- **No Session Affinity**: Requests from the same client may be directed to different servers, which can be problematic for stateful applications.
- Security systems that rely on detecting anomalies (e.g., to mitigate DDoS attacks) might find it slightly more challenging to identify malicious patterns if a Random algorithm is used, due to the inherent unpredictability in request distribution. This could potentially dilute the visibility of attack patterns.

### Use Cases
- **Homogeneous Environments**: Suitable for environments where servers have similar capacity and performance.
- **Stateless Applications**: Works well for stateless applications where each request can be handled independently.
- **Simple Deployments**: Ideal for simple deployments where the complexity of other load balancing algorithms is not justified.

![Random](/img/random.gif)

## Least Bandwidth
A load balancing algorithm that directs incoming requests to the server currently handling the least amount of network traffic.

## Pros
- **Dynamic Load Balancing**: Continuously adjusts to the current network load, ensuring optimal distribution of traffic.
- **Prevents Overloading**: Helps in preventing any single server from being overwhelmed with too much data traffic, leading to better performance and stability.
- **Efficient Resource Utilization**: Ensures that all servers are utilized more effectively by balancing the bandwidth usage.

## Cons
- **Complexity**: More complex to implement compared to simpler algorithms like Round Robin, as it requires continuous monitoring of bandwidth usage.
- **Overhead**: Monitoring bandwidth and dynamically adjusting the load can introduce additional overhead.
- **Short-Term Variability**: Bandwidth usage can fluctuate in the short term, potentially causing frequent rebalancing.

### Use Cases
- **High Bandwidth Applications**: Ideal for applications with high bandwidth usage, such as video streaming, file downloads, and large data transfers.
- **Content Delivery Networks (CDNs)**: Useful for CDNs that need to balance traffic efficiently to deliver content quickly.
- **Real-Time Applications**: Suitable for real-time applications where maintaining low latency is critical.

![Least Bandwidth](/img/least_bandwidth.gif)

## Custom Load
A load balancing algorithm that allows for custom logic to determine how requests are distributed among servers. This can be based on specific business rules, application requirements, or other criteria defined by the user.


## How it works
1. **Define Custom Metrics**: Determine the metrics that best represent the load or performance characteristics relevant to your application. These metrics can include CPU usage, memory usage, disk I/O, application-specific metrics, or a combination of several metrics.

1. **Implement Monitoring**: Continuously monitor the defined metrics on each server in the pool. This may involve integrating with monitoring tools or custom scripts that collect and report the necessary data.

1. **Create Load Balancing Rules**: Establish rules and algorithms that use the monitored metrics to make load balancing decisions. This can be a simple weighted sum of metrics or more complex logic that prioritizes certain metrics over others.

Dynamic Adjustment: Use the collected data and rules to dynamically adjust the distribution of incoming requests, ensuring that the traffic is balanced according to the custom load criteria.