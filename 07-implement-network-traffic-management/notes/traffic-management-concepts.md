# Azure Traffic Management Concepts

This note summarizes the key concepts demonstrated in the **Implement Network Traffic Management** lab.

---

## 1. Network Traffic Management

Network traffic management controls how incoming network requests are distributed to application resources.

In this lab, Azure services were used to:

1. Deploy multiple backend virtual machines.
2. Distribute incoming public traffic using Azure Load Balancer.
3. Monitor backend availability using health probes.
4. Route web requests using Azure Application Gateway.
5. Direct different URL paths to different backend servers.

The overall objective is to improve availability and provide controlled traffic distribution.

---

## 2. Layer 4 Load Balancing

Azure Load Balancer works at **Layer 4 – Transport Layer**.

It makes traffic-management decisions using network information such as:

* IP addresses
* TCP/UDP protocols
* Ports

For example:

```text
Client
   |
   | TCP :80
   v
Load Balancer
   |
   +------> VM0 :80
   |
   +------> VM1 :80
```

The Load Balancer does not need to understand the content of an HTTP request to distribute the connection.

---

## 3. Public Load Balancer

A **public Load Balancer** provides a public frontend through which clients on the internet can access backend resources.

The lab used:

```text
Public IP
    |
    v
Azure Load Balancer
    |
    v
Backend Pool
    |
    +---- VM0
    |
    +---- VM1
```

The public IP is associated with the Load Balancer frontend.

Clients connect to the public IP rather than directly connecting to individual backend VMs.

---

## 4. Standard Load Balancer

The lab uses the **Standard** Load Balancer SKU.

The Standard SKU was selected for the lab's public load-balancing configuration, including a static public IP configuration.

The frontend configuration included:

```text
Frontend IP Configuration:
az104-fe

Public IP:
az104-lbpip

IP Assignment:
Static
```

Using a static public IP provides a predictable frontend address for clients.

---

## 5. Backend Pool

A backend pool contains the resources that receive traffic from the Load Balancer.

In this lab:

```text
Backend Pool:
az104-be
```

The backend pool included the network interfaces of:

```text
az104-06-vm0
az104-06-vm1
```

Traffic received by the Load Balancer can therefore be distributed between the backend virtual machines.

---

## 6. Health Probes

Health probes allow the Load Balancer to determine whether a backend resource is available.

The lab configured:

```text
Health Probe:
az104-hp

Protocol:
TCP

Port:
80

Interval:
5 seconds
```

Conceptually:

```text
Load Balancer
      |
      | Health Probe
      v
    VM0 :80
      |
      | Healthy
      v
Available for traffic
```

If a backend resource is not responding according to the configured health probe, the Load Balancer can stop sending new traffic to that unhealthy backend.

### Why Health Probes Matter

Without health monitoring, traffic could continue to be sent to an unavailable server.

Health probes help the Load Balancer maintain traffic distribution across available backend resources.

---

## 7. Load-Balancing Rule

The Load Balancing Rule defines how traffic arriving at the frontend is forwarded to the backend pool.

The lab used:

```text
Rule:
az104-lbrule

Protocol:
TCP

Frontend Port:
80

Backend Port:
80

Backend Pool:
az104-be

Health Probe:
az104-hp
```

Traffic flow:

```text
Client
  |
  | TCP :80
  v
Frontend :80
  |
  v
Load-Balancing Rule
  |
  v
Backend Pool
  |
  +----> VM0 :80
  |
  +----> VM1 :80
```

---

## 8. Layer 7 Application Gateway

Application Gateway works at **Layer 7 – Application Layer**.

Unlike a Layer 4 Load Balancer, Application Gateway understands web traffic and can make routing decisions using HTTP information.

It can support capabilities such as:

* URL path-based routing
* Host-based routing
* SSL/TLS termination
* Backend health monitoring
* Web Application Firewall capabilities through the appropriate tier

---

## 9. Dedicated Application Gateway Subnet

Application Gateway requires a dedicated subnet.

The lab created:

```text
Subnet:
subnet-appgw

Address:
10.60.3.224/27
```

The Application Gateway was deployed into this subnet.

### Important

Do not place other application resources in the dedicated Application Gateway subnet.

The lab specifically demonstrates creating a separate subnet for the gateway.

---

## 10. Application Gateway Frontend

The Application Gateway was configured with a public frontend.

The lab used:

```text
Application Gateway:
az104-appgw

Frontend Public IP:
az104-gwpip
```

The public IP provides the entry point for web clients.

Traffic flow:

```text
Internet
   |
   v
Application Gateway Public IP
   |
   v
Application Gateway
   |
   v
Backend Pool
```

---

## 11. Application Gateway Backend Pools

The lab configured multiple backend pools for different types of web content.

### General Backend Pool

```text
az104-appgwbe
```

Targets:

```text
NIC1
NIC2
```

### Image Backend Pool

```text
az104-imagebe
```

Target:

```text
NIC1
```

### Video Backend Pool

```text
az104-videobe
```

Target:

```text
NIC2
```

This configuration allows the Application Gateway to direct different types of requests to different backend resources.

---

## 12. Path-Based Routing

Path-based routing allows Application Gateway to route requests according to the URL path.

The lab configured:

```text
/image/*  ---> az104-imagebe
/video/*  ---> az104-videobe
```

Example:

```text
Client
  |
  | /image/
  v
Application Gateway
  |
  v
Image Backend
```

And:

```text
Client
  |
  | /video/
  v
Application Gateway
  |
  v
Video Backend
```

This is an important difference between Layer 4 and Layer 7 traffic management.

---

## 13. Basic Routing vs Path-Based Routing

### Basic Routing

A basic Application Gateway routing rule forwards traffic to a backend using a listener and backend configuration.

```text
Client
  |
  v
Listener
  |
  v
Backend Pool
```

### Path-Based Routing

Path-based routing uses URL paths to select the backend.

```text
Client
  |
  v
Listener
  |
  v
Path Rule
  |
  +---- /image/* ---> Image Backend
  |
  +---- /video/* ---> Video Backend
```

Path-based routing is useful when different parts of a web application are hosted on different backend resources.

---

## 14. Backend Health

Application Gateway provides backend health monitoring.

The lab verified that the backend servers were shown as **Healthy** after deployment.

Conceptually:

```text
Application Gateway
       |
       +---- Backend VM 1 → Healthy
       |
       +---- Backend VM 2 → Healthy
```

Healthy backend resources can receive traffic according to the configured routing rules.

---

## 15. Session Persistence

The Load Balancer configuration in the lab used:

```text
Session Persistence:
None
```

This means the configuration does not intentionally maintain client-session affinity to one backend through the selected persistence setting.

Session persistence is an important consideration for applications that maintain state locally on a backend server.

---

## 16. Idle Timeout

The Load Balancer rule used an idle timeout of:

```text
4 minutes
```

An idle timeout determines how long an inactive connection can remain established before being closed.

The correct timeout value depends on the application's connection requirements.

---

## 17. TCP Reset and Floating IP

The Load Balancer rule in the lab used:

```text
TCP Reset:
Disabled

Floating IP:
Disabled
```

These are additional Load Balancer configuration options that control specific connection and frontend behaviors.

For this lab scenario, the default-style configuration was sufficient to demonstrate basic Layer 4 load balancing.

---

## 18. Load Balancer vs Application Gateway

The main conceptual distinction is:

```text
                 Traffic Management
                        |
            +-----------+-----------+
            |                       |
            v                       v
       Load Balancer          Application Gateway
          Layer 4                 Layer 7
            |                       |
       TCP / UDP               HTTP / HTTPS
            |                       |
      Network-aware           Application-aware
       distribution              routing
```

### Load Balancer

Focuses on:

```text
IP + Protocol + Port
```

### Application Gateway

Can use:

```text
HTTP/HTTPS + URL Path + Host Information
```

---

## 19. Typical Traffic Flow in This Lab

The lab demonstrates two traffic-management approaches.

### Load Balancer Flow

```text
Internet
   |
   v
Public Load Balancer
   |
   v
Backend Pool
   |
   +------> VM0
   |
   +------> VM1
```

The Load Balancer distributes TCP port 80 traffic between healthy backend VMs.

### Application Gateway Flow

```text
Internet
   |
   v
Application Gateway
   |
   +---- /image/* ---> Image Backend
   |
   +---- /video/* ---> Video Backend
```

Application Gateway makes the routing decision based on the web request path.

---

## 20. Why Use Traffic Management?

Traffic management can provide several architectural benefits:

* Distribution of incoming traffic
* Better availability
* Health-based backend selection
* Separation of application workloads
* Application-aware routing
* Improved scalability
* Controlled public access to backend services

The exact benefits depend on the service and architecture being used.

---

## 21. Key AZ-104 Concepts

The main concepts demonstrated by this lab are:

### Layer 4

> Azure Load Balancer provides Layer 4 traffic distribution for TCP/UDP traffic.

### Layer 7

> Azure Application Gateway provides Layer 7 web traffic management.

### Health Probes

> Health probes help identify backend resources that are available to receive traffic.

### Backend Pools

> Backend pools define the resources that can receive forwarded traffic.

### Path-Based Routing

> Application Gateway can route HTTP requests to different backend pools according to URL paths.

### Public vs Internal Load Balancing

> Azure Load Balancer can provide public or internal load-balancing capabilities depending on the frontend configuration.

### WAF

> Application Gateway can provide Web Application Firewall functionality when using the appropriate WAF tier.

---

## 22. Final Takeaway

Remember the simplest distinction:

```text
Azure Load Balancer
        ↓
Layer 4
        ↓
TCP / UDP
        ↓
Network-level distribution
```

```text
Azure Application Gateway
        ↓
Layer 7
        ↓
HTTP / HTTPS
        ↓
Application-aware routing
```

For AZ-104, understanding **when to use Load Balancer versus Application Gateway** is more important than simply memorizing the configuration steps.

