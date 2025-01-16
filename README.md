# holbertonschool-system_engineering-devops

# Simple Web Infrastructure Schema

To design a simple and clear schema for hosting a website on a single server using a LAMP (Linux, Apache, MySQL, PHP) stack, follow these steps:

Components in the Schema:
1. User/Client: A device with a browser making a request to access www.foobar.com.
2. Internet: The medium through which the request travels.
3. Domain Name System (DNS)**: Resolves the domain name (www.foobar.com) to the server’s IP address (e.g., 8.8.8.8).
4. Server: Hosts all components of the web stack.
   - Web Server (Apache): Handles HTTP requests and serves static files.
   - Application Server (PHP): Executes the application’s dynamic content.
   - Database Server (MySQL): Stores and retrieves data for the application.

Flow:
1. User Request: The user types "www.foobar.com" in the browser and presses Enter.
2. DNS Resolution: The domain name is translated to the server’s IP address.
3. Server Communication**: The request reaches the web server (Apache) on the server (IP 8.8.8.8).
4. dynamic Content Handling**:
   - The web server forwards the request to the application server (PHP).
   - The application server queries the database server (MySQL) if needed.
   - The application server generates a response (HTML).
5. Response to User: The web server sends the response back to the user’s browser.

---

 Diagram Layout:
User** ➔ Internet ➔ DNS ➔ Web Server (Apache) ➔ Application Server (PHP) ➔ Database Server (MySQL)➔ Response Back to User

# 1. Distributed web infrastructure

General diagram:
User (Client)
The user types the address www .foobar .com in his browser to the site.

Load balancer (HAProxy)

The role of the Load Balancer is to distribute traffic between several servers to avoid that a single one is overloaded.
For example, it can use a system like round-robin (sending requests in turn to each server).
Web server (Nginx)

This server manages static content (like HTML, CSS, JavaScript).
If a request asks for something more complex, it is sent to the application server.
Application server

This is where the site code is executed (PHP, Node.js, etc.).
It manages the logic of the site and interactions with the database.
Database (MySQL)

We use a main database (to write data) and responses (to read). This allows for better performance and ensures that data is always available in the event of a problem.

# 2. Secured and monitored web infrastructure
1. Infrastructure Components
Load Balancer (HAProxy):

SSL Certificate: The load balancer handles encrypted traffic with HTTPS.
Distributes traffic to web servers based on an algorithm like round-robin.
Configured with a firewall to control incoming requests.
Web Server (Nginx):

Hosts static content (HTML, CSS, JS) and proxies dynamic requests to the application server.
Protected by its own firewall to block unwanted traffic.
Application Server:

Executes the application code (e.g., PHP, Python, Node.js).
Protected by a firewall to secure data between servers.
Database Server (MySQL):

Stores and retrieves data for the application.
Uses a Primary-Replica setup to ensure scalability and reliability.
Monitoring Clients:

Installed on all servers to send logs and metrics to a central monitoring service (e.g., Sumologic, Prometheus).
Collects data on server health, QPS (queries per second), and response times.
2. Why These Components?
Firewalls:

Restrict unauthorized access by filtering incoming and outgoing traffic.
Enhance security for the load balancer, web server, and database.
HTTPS Traffic:

Ensures data encryption, protecting user information and maintaining privacy.
Prevents man-in-the-middle attacks.
Monitoring Clients:

Allow real-time tracking of server performance.
Help detect and resolve issues proactively (e.g., CPU spikes, memory usage).
Primary-Replica Database Setup:

Primary Node: Handles all writes and updates.
Replica Node: Handles read operations to improve performance and redundancy.
3. Issues with This Setup
SSL Termination at Load Balancer:

If SSL is terminated at the load balancer, traffic between the load balancer and servers is unencrypted. This creates a security risk.
Single Write-Only MySQL Server:

The database becomes a bottleneck. If the primary server fails, write operations stop unless failover is configured.
Same Components on All Servers:

Mixing web, application, and database services on the same server complicates scaling and maintenance.

# 3 scale up 
Components and Additions
New Additions

Additional Server:
A new App Server (S2) is added to offload application processing from the Web Server (S1). This separation enhances scalability and optimizes resource allocation.
HAProxy Cluster:
Configures a Primary Load Balancer (LB1) with a Backup Load Balancer (LB2) to ensure high availability. If the primary fails, the backup can take over seamlessly.
Split Components

Web Server (S1): Handles static assets and incoming HTTP(S) requests. Uses Nginx or Apache.
App Server (S2): Processes dynamic content and application logic.
Database Server: Stores and manages data using MySQL.
Purpose of Each Element
Load Balancers (HAProxy):

Why Added? Provides fault tolerance and distributes traffic evenly.
Role: Prevents overloading any single server, ensuring smooth user experience.
Dedicated App Server:

Why Added? Separates application logic from web serving, improving response times.
Role: Executes backend logic and communicates with the database.
Database Server:

Why Split? Reduces contention and allows focused optimization for queries and data handling.
Benefits of Scaling Up
Reliability: Redundant load balancers ensure continued operation during failures.
Performance: Splitting components reduces resource contention.
Scalability: Each layer can be scaled independently as demand grows.
Potential Issues
HAProxy Single Points of Failure:

Issue: If both primary and backup load balancers fail, the entire system is down.
Solution: Use a third load balancer or DNS-based failover.
Database Bottleneck:

Issue: A single database server might not handle growing write operations.
Solution: Introduce database replication or sharding.
Resource Isolation:

Issue: Mismanagement of resource limits on app or web servers can lead to performance issues.
Solution: Use containerization (Docker) or virtual machines to enforce boundaries.
Monitoring Strategy
Purpose of Monitoring:

Track resource usage (CPU, memory, disk I/O).
Alert administrators for anomalies or downtime.
Monitoring Web Server QPS:

Enable access logs and parse them using tools like Prometheus or Datadog.
Visualize metrics to identify traffic patterns and scaling needs.
