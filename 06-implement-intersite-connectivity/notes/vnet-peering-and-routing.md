# VNet Peering and Routing

## Lab 05 – Implement Intersite Connectivity

This document summarizes the key networking concepts demonstrated in the lab.

---

# 1. Virtual Network Isolation

Azure virtual networks provide logical network isolation.

In this lab, two separate VNets were created:

```text
CoreServicesVnet
10.0.0.0/16

ManufacturingVnet
172.16.0.0/16
```

Initially, the two VNets did not have connectivity between them.

```text
CoreServicesVnet
      │
      X
      │
ManufacturingVnet
```

The initial Network Watcher connection test therefore reported the destination as unreachable.

---

# 2. Virtual Network Peering

VNet peering was configured between:

```text
CoreServicesVnet
        │
        │ VNet Peering
        │
        ▼
ManufacturingVnet
```

Two peering links were configured:

* `CoreServicesVnet-to-ManufacturingVnet`
* `ManufacturingVnet-to-CoreServicesVnet`

Both peering connections were verified as **Connected**.

---

# 3. Connectivity After Peering

After peering was configured, the virtual machines could communicate using private IP addresses.

```text
ManufacturingVM
      │
      │ Private IP connectivity
      ▼
VNet Peering
      │
      ▼
CoreServicesVM
```

The connectivity was verified using:

```powershell
Test-NetConnection <CoreServicesVM-private-IP> -Port 3389
```

A successful result confirmed that the private network path was working.

---

# 4. Perimeter Subnet

A perimeter subnet was created inside `CoreServicesVnet`:

```text
Subnet:
perimeter

Address:
10.0.1.0/24
```

The perimeter subnet represents a network area where a future Network Virtual Appliance (NVA) can be placed.

---

# 5. Route Table

A route table named:

```text
rt-CoreServices
```

was created.

The route table contains a custom route:

```text
PerimetertoCore
```

---

# 6. User Defined Route

The custom route was configured as follows:

| Setting          | Value             |
| ---------------- | ----------------- |
| Route Name       | `PerimetertoCore` |
| Destination      | `10.0.0.0/16`     |
| Next Hop Type    | Virtual Appliance |
| Next Hop Address | `10.0.1.7`        |

---

# 7. Understanding the UDR

The route tells Azure that traffic destined for the Core Services network should use the virtual appliance as the next hop.

```text
Perimeter / DMZ
10.0.1.0/24
       │
       │ User Defined Route
       │
       ▼
Virtual Appliance
10.0.1.7
       │
       ▼
CoreServicesVnet
10.0.0.0/16
```

The lab does not deploy an actual NVA at `10.0.1.7`. The address represents the future NVA next hop used to demonstrate custom routing.

---

# 8. System Routes vs User Defined Routes

Azure automatically creates system-defined routes for network traffic.

User Defined Routes can be added to provide custom routing behavior.

In this lab, the UDR provides a custom next hop:

```text
Destination: 10.0.0.0/16
        ↓
Next Hop: Virtual Appliance
        ↓
10.0.1.7
```

This allows network administrators to control how traffic is forwarded through the network.

---

# 9. Final Network Concept

The completed lab demonstrates three important networking concepts:

```text
1. VNet Isolation
        ↓
2. VNet Peering
        ↓
3. Custom Traffic Routing
```

### Connectivity

```text
CoreServicesVnet
       │
       │ VNet Peering
       │
       ▼
ManufacturingVnet
```

### Custom Routing

```text
Perimeter
   │
   ▼
UDR
   │
   ▼
Future NVA
10.0.1.7
   │
   ▼
CoreServicesVnet
```

---

## Key Takeaways

* Separate Azure VNets are isolated by default.
* VNet peering provides private connectivity between VNets.
* Network Watcher can be used to diagnose connectivity.
* `Test-NetConnection` can verify TCP connectivity.
* Route tables can contain custom routes.
* User Defined Routes provide additional control over traffic flow.
* A virtual appliance can be configured as a custom route next hop.
* The lab demonstrates the routing configuration for an NVA without deploying the actual NVA.
