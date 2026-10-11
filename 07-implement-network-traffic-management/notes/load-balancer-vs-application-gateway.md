# Azure Load Balancer vs Application Gateway

Azure Load Balancer and Azure Application Gateway are both Azure traffic-management services, but they operate at different layers and are designed for different types of traffic.

## 1. Azure Load Balancer

Azure Load Balancer operates at **Layer 4 (Transport Layer)** of the OSI model.

It distributes incoming network traffic across multiple backend resources based on IP addresses and TCP/UDP ports.

### Key Characteristics

* Operates at Layer 4.
* Supports TCP and UDP traffic.
* Can provide public or internal load balancing.
* Uses health probes to determine backend availability.
* Distributes connections across healthy backend instances.
* Suitable for applications that require network-level load balancing.
* Does not inspect HTTP URLs or application-layer content.

### Lab Configuration

In this lab, the public Load Balancer was configured with:

| Component           | Configuration |
| ------------------- | ------------- |
| Load Balancer       | `az104-lb`    |
| SKU                 | Standard      |
| Frontend            | `az104-fe`    |
| Public IP           | `az104-lbpip` |
| Backend Pool        | `az104-be`    |
| Protocol            | TCP           |
| Frontend Port       | 80            |
| Backend Port        | 80            |
| Health Probe        | `az104-hp`    |
| Session Persistence | None          |
| Idle Timeout        | 4 minutes     |

The Load Balancer distributes HTTP requests received on TCP port 80 between the backend virtual machines.

### Traffic Flow

```text
Internet
   |
   | TCP :80
   v
Public Load Balancer
   |
   +--------------------+
   |                    |
   v                    v
VM0                  VM1
TCP :80              TCP :80
```

When the browser sends a request to the Load Balancer public IP, the Load Balancer forwards the connection to one of the healthy backend VMs.

Refreshing the page can result in a response from a different VM.

---

## 2. Azure Application Gateway

Azure Application Gateway operates at **Layer 7 (Application Layer)**.

It is designed specifically for web application traffic and can make routing decisions based on HTTP/HTTPS information.

### Key Characteristics

* Operates at Layer 7.
* Designed for web traffic.
* Supports HTTP and HTTPS.
* Can perform URL path-based routing.
* Can use host-based routing.
* Provides frontend public or private IP configurations.
* Uses backend health monitoring.
* Supports Web Application Firewall capabilities in supported tiers.
* Can provide SSL/TLS termination.
* Can support end-to-end encryption.

### Lab Configuration

In this lab, an Application Gateway named `az104-appgw` was created.

A dedicated subnet was configured for the Application Gateway:

```text
Subnet Name: subnet-appgw
Address:     10.60.3.224/27
```

The lab notes that Application Gateway requires a dedicated subnet of `/27` or larger.

### Backend Pools

Three backend pools were configured:

| Backend Pool    | Backend Target | Purpose             |
| --------------- | -------------- | ------------------- |
| `az104-appgwbe` | NIC1 + NIC2    | General web traffic |
| `az104-imagebe` | NIC1           | Image requests      |
| `az104-videobe` | NIC2           | Video requests      |

### Path-Based Routing

The Application Gateway was configured to route requests according to the URL path.

```text
                   Internet
                       |
                       v
              Application Gateway
                       |
              +--------+--------+
              |                 |
        /image/*            /video/*
              |                 |
              v                 v
          Image VM          Video VM
          NIC1              NIC2
```

The routing configuration was:

| URL Path   | Target Backend Pool |
| ---------- | ------------------- |
| `/image/*` | `az104-imagebe`     |
| `/video/*` | `az104-videobe`     |

For example:

```text
http://<Application-Gateway-IP>/image/
```

is routed to the image backend pool.

Similarly:

```text
http://<Application-Gateway-IP>/video/
```

is routed to the video backend pool.

---

## 3. Main Differences

| Feature                        | Azure Load Balancer            | Application Gateway                |
| ------------------------------ | ------------------------------ | ---------------------------------- |
| OSI Layer                      | Layer 4                        | Layer 7                            |
| Main Purpose                   | Network traffic load balancing | Web application traffic management |
| Traffic                        | TCP / UDP                      | HTTP / HTTPS                       |
| URL Path Routing               | No                             | Yes                                |
| Host-Based Routing             | No                             | Yes                                |
| Health Probes                  | Yes                            | Yes                                |
| Public Frontend                | Yes                            | Yes                                |
| Private/Internal Configuration | Yes                            | Yes                                |
| SSL/TLS Termination            | No                             | Yes                                |
| Web Application Firewall       | No                             | Supported through WAF tier         |
| Application-Aware Routing      | No                             | Yes                                |
| Typical Use                    | Network-level load balancing   | Web application routing            |

---

## 4. When to Use Each

### Use Azure Load Balancer When:

* You need Layer 4 load balancing.
* The application uses TCP or UDP.
* You need to distribute network connections across multiple VMs.
* You need a public or internal load balancer.
* Application-level routing is not required.

### Use Application Gateway When:

* You are managing web applications.
* You need HTTP/HTTPS-aware routing.
* You need URL path-based routing.
* You need host-based routing.
* SSL/TLS termination is required.
* Web Application Firewall functionality is required.

---

## 5. Lab Comparison

This lab demonstrates why the two services serve different purposes.

The **Load Balancer** distributes incoming TCP traffic between backend virtual machines:

```text
Client
  |
  v
Azure Load Balancer
  |
  +----> VM0
  |
  +----> VM1
```

The **Application Gateway** examines HTTP requests and routes them according to their URL path:

```text
Client
  |
  v
Application Gateway
  |
  +---- /image/* ---> Image Backend
  |
  +---- /video/* ---> Video Backend
```

Therefore, the Load Balancer provides **network-level traffic distribution**, while Application Gateway provides **application-aware web traffic routing**.

## 6. Key Takeaway

> **Azure Load Balancer = Layer 4 network traffic distribution**

> **Azure Application Gateway = Layer 7 web application traffic management**

Understanding this distinction is important when designing Azure network architectures and selecting the appropriate traffic-management service.
