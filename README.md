# SystemDesign
System Design concepts

# Introduction to Load Balancing

1. Distribute incoming requests and traffic evenly across multiple servers.
2. Main goal is to ensure high availabilty, reliability and performance by avoiding overloading a single server and avoiding downtime.
![img.png](img.png)
3. To utilise full scalability and redundancy, we can try to balance the load at each layer of the system.
We can add load balancers(LBs) at three places.

   1. Between user and the web server. 
   2. Between web server  and an internal platform layer, like application servers or cache servers. 
   3. Between internal platform layer and database.

![img_1.png](img_1.png)

# Key terminology and concepts.
1. Load balancing algo:- The method used by the load balancer to determine how to distribute incoming traffic among the backend servers.
2. Health checks: Periodic tests performed by the load balancer to determine the avaialbilty and performance of backend servers. Unhealthy servers are removed from the server pool until they recover.
3. Session persistence: A technique used to ensure that subsequent requests from the same client are directed to the same backend server, maintaining session state and providing a consistent user experience.

# how load balancer works?

Steps
1. LB receives a request from a client.
2. LB evaluates incoming request and determines which server or resource should handle the request. This is done on a predefines load-balancing algorithm that takesinto account factros such as server capacity, server response time, number of active connections, and geographic localtion.
3. LB forwards incomning traffic to selected server or resource.
4. Server processes the requests and sends a response back to the load balancer.
5. LB receives the response from the server and sends it to the client who made the requests.

# LB Algorithms.

1. **Round Robin** 

   1. Algo distributes requests to servers in cyclic order. It assign a requests to the first server, then moves to the second, third, and so on, after reaching the last server, it starts again at the first.
   
   **PROS**
    1. Ensures equal distribution of requests among the servers, as each server gets a turn in fixed order.
    2. Easy to implement.
    3. Works well when servers have similar capacities.

   **CONS**
    1. Does not know about the current load of capacity of each server. All servers are treated equally regardless of their current state.
    2. Subsequent requests from same client may be directed to different servers. which can be problematic for stateful applications.
    3. Its is predictable in its requests distribution pattern, which could potentially be exploied by attackers who can observe traffic patterns and might find vulnerabilties in specific servers by predicting which server will handle their requests. 

    **USE CASES**
    1. **Homegeneous environments**  Suitable for environments where all servers have similar capacity and performcance.
    2. **Stateless applications** Useful for stateless applications where each requests can be handled independently.
   ![img_2.png](img_2.png)

2. **Least connections**

  1. Assigns requests to the server with the fewest active connections at the time of the request. This method ensures more balanced distribution of the load across servers, especially in environments where traffic patterns are un predictable and requests processing times vary.
 
   **PROS**
   1. Takes into account the current load on each server by considering the number of active connections, leading to better utilization of server resources.
   2. Dynamic distributin: Adapts to changing traffic patterns and server loads, ensuring no single server becomes overloaded.
   3. Efficiency in hetreogeneous environments: Performs well when servers have varying capacities and workloads, as it dynamically allocates requests to less busy servers.
   
   **CONS**
  1. High complexity: More complex to implements compared to simpler algorithms like round robin, as it requires real-time monitoring of active connections.
  2. State maintanance: Requires the load balancer to maintain the state of active connections, which can increase overhead.
 
   **USE CASES**
  1. Heterogeneous environments: Suitable for environments where servers have different capacities and workloads, and the loads need to be dynamically distributed.
  2. Variable traffic patterns: Works well for applications with unpredicatable or highly variable traffic patterns, ensuring that no single server if overloaded.

3. Weighted Round Robin:

  Weighted Round robin is enhanced version of round robin load balancing algorithm.
  It assigns weights to each server based on their capacity or performance, distributing incoming requests proportinally according to their weights, This ensures that more powerful server handles a large share of the load while less
 powerful server handles smaller share.

   **PROS**
 1. Load distribution according to capacity: Servers with higher capcities handle more requests leading to better utilization of resources.
 2. Flexibilty: Easily adjustable to accomodate changes in server capacities or additions of new servers.

   **CONS**
 1. Complexity in Weight assignment: Determining appropriate weights for each server can be challenging and requires accurate performance metrics.
 2. Increased overhead: Managing and updating weights can introduce additonal overhead, espceially in dynamic environments where server performance fluctuates.

  **USE CASES**
 1. Heterogeneous Environments: Ideal for environments where servers have different processing capabilties, ensuring efficient use of resources.
 2. Scalable web applications: Suitable for web applications where different servers may have varying performance characterstics.
 3. Database clusters: Useful in database clusters where nodes have higher processing power and can handle more queries.

4. Weighted Least connections

  Advanced load balancing algorithm which combines principles of Least connections and Weighted Round Robin algorithms. It takes into account both the current load(number of active connections) on each server and the relative capacilty of each server(Weight).
 This approach ensures more powerful servers handles a proportionally larger share of the loadm whilw also dynamically adjusting to the real-time load on each server.

   **PROS**
1. Adjusts to real time load on each server, ensuring a more balanced distribution of requests.
2. Capacity awareness: Takes into account the relative capacity of each server, leading to better utilizattion of resources.
3. Can handle environments with heterogeneous servers and varaible load patterns effectively.

   **CONS**
1. Complexity: More complex to implement.
2. State maintenance: Requires load balancer to keep traks of both active connections and server weights.
3. Weight Assignment: Determing appropriate weights for each server can be challenging and requires accurate performance metrics.

   **USE CASES**
1. Heterogeneous environments: Good for environments wher servers have different processing capacities and workloads.

5. IP Hash
 Assigns requests based on the clients IP address. The load balancer uses a hash function to convert the clients IP address into a hash value, which is then used to determine which server should handle the request. This method ensures that requests from the same client IP address are consistently routed to the same server. providing session persistence.

Example.
Suppose you have 3 servers(Server A, Server B, Server C) and a client with the IP address 192.168.1.10. The load balancer applies a hash functions to this IP address, resulting in a hash value. If the hash value is 2. The load balancer routes the requests to server C( 2 %3 ==2).
  

   **PROS**
1. Session persistence: Ensures requests from the same client Ip address are routed to the same server. Which is beneficial for stateful applications.
2. Simplicity: Easy to implement and does not require the load balancer to maintain state of connections.

   **CONS**
1. Uneven distributions: if client Ip address are not evenly distributed some servers may receive more requests than others.
2. Dynamic changes: Adding or removing servers can disrupt the hash mapping, causing some clients to be routed to different servers.
3. Limited flexibility: Does not take into account the current load or capacity of servers, which can lead to inefficiencies.

   **USE CASES**
1. Stateful application: Ideal for applications where maintaining session persistence is important, such as online shopping carts or user sessions.
2. Geographically distributes clients: useful when clients are distributed across different regions and consistent routing is required.
![img_3.png](img_3.png)

6. Random 

   Its distributes incoming requests to servers randomly. This method can be effective in scenarios where the load is relatively uniform and server have same capacities.
   Over time, if the randomness is uniform, each server should receive approximately the same number of requests.
   
   **PROS**
   1. Simplicity: Very easy to implement and understand, requiring minimal configuration.
   2. No state maintenance: LB does not need to track state or performance of servers, reducing overhead.
   3. uniform distribution over time: If random selection is uniform, the load will be evenly distributed across servers over a long period.

   **CONS**
  1. No load awareness: Does not consider the current load or capacity of servers, which can lead to uneven didtribution if server performance varies.
  2. Requests from the same client can be directed to different servers, which can be problematic for stateful applications.

   **USE CASES**
  1. Homegeneous environments: Suitable for environments where servers have similar capacity and performance.
  2. Stateless application: works well for stateless applications where each request can be handles independently.
  3. Simple deployments: ideal for simple deployments where the complexity of other load balancing algorithms is not justified.

8. Least Bandwidth:
 
    This distributes incoming requests to servers based on the current bandwidth usage. It routes each new requests to the server that is consuming the least amount of bandwidth at the time. This approach helps in balancing the network load more effectively.
9. Custom load:
 It is a configurable approach that allows you to define your own metrics and rules for distributing incoming traffic across a pool of servers.
 
 How it works?
   1. Determine the metrics that best represent the load or performance characterstics relevant to  your application like CPu usage, memory usage, IO application.
   2. Implement monitoring on each server in pool.
   3. Use the collected data and rules to dynamically adjust the distribution of incoming requests, ensuring that the traffic is balanced according to custom load criteria.


  **PROS**
  1. Adabtability: Easily adaptable to changing conditions and requirements, making it suitable for complex and dynamic environments.
  2. Optimised resource utilization: Can lead to more efficient use of server resources by considering a comprehensive set of metrics.

  **CONS**
  1. More complex to implement.
  2. Requires continous monitoring which can introduce additional overhead.  

  **USE CASES**
  1. Ideal for applications with complex performance.
  2. Suitable for enivornments where workloads and server performance can change quickly.



    
