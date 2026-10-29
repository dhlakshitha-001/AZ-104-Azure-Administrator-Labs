# VM Scale Sets and Autoscaling Concepts

## Overview

Azure Virtual Machine Scale Sets (VMSS) allow administrators to deploy and manage a group of virtual machine instances.

Autoscaling can increase or decrease the number of instances based on metrics and configured rules.

This document summarizes the concepts covered in the **Manage Virtual Machines** lab.

## 1. Virtual Machine Scale Sets

A VM Scale Set supports centralized management of a group of VMs.

The lab uses the following configuration:

| Setting                | Value                   |
| ---------------------- | ----------------------- |
| Scale set name         | `vmss1`                 |
| Resource group         | `az104-rg8-lod66120625` |
| Region                 | West US 2               |
| Availability zones     | Zones 1, 2, and 3       |
| Orchestration mode     | Uniform                 |
| VM size                | `Standard_D2s_v5`       |
| Initial instance count | 2                       |
| Minimum instance count | 2                       |
| Maximum instance count | 10                      |

### Benefits

* Centralized instance management.
* Support for horizontal scaling.
* Integration with Azure monitoring and autoscaling.
* Integration with network and load-balancing resources.
* Support for availability-zone deployment where available.

The actual capabilities depend on the selected orchestration mode and resource configuration.

## 2. VMSS Networking

The lab uses the following networking resources:

| Resource                      | Configuration  |
| ----------------------------- | -------------- |
| Virtual network               | `vmss-vnet`    |
| Virtual network address space | `10.82.0.0/20` |
| Subnet                        | `subnet0`      |
| Subnet address range          | `10.82.0.0/24` |
| Network security group        | `vmss1-nsg`    |
| Load balancer                 | `vmss-lb`      |

### Virtual Network

An Azure virtual network provides private network connectivity for supported Azure resources.

### Subnet

A subnet divides a virtual network address space into smaller network segments.

The lab configures `subnet0` with the address range `10.82.0.0/24`.

### Network Security Group

A network security group (NSG) contains security rules that allow or deny network traffic.

The lab includes an inbound HTTP rule with these intended settings:

| Setting          | Value        |
| ---------------- | ------------ |
| Rule name        | `allow-http` |
| Protocol         | TCP          |
| Destination port | 80           |
| Source           | Any          |
| Action           | Allow        |
| Priority         | 1010         |

The rule should be reviewed carefully before use outside the lab. Allowing HTTP from any source exposes port 80 to the relevant network sources permitted by the surrounding configuration.

An NSG rule alone does not guarantee that the application is reachable. Routing, load-balancer rules, backend configuration, and the application listener must also be configured correctly.

### Load Balancer

Azure Load Balancer distributes supported network traffic across configured backend instances.

A load balancer can help distribute traffic, but it does not automatically ensure that the application is correctly configured or healthy.

## 3. Autoscaling Fundamentals

Autoscaling adjusts the number of VMSS instances in response to configured conditions.

Two main scaling actions are:

### Scale-Out

Scale-out increases the number of instances.

A scale-out rule may be triggered when CPU utilization remains above a configured threshold.

### Scale-In

Scale-in decreases the number of instances.

A scale-in rule may be triggered when CPU utilization remains below a configured threshold.

### Example

A scale set may use CPU utilization as the scaling metric:

* CPU above 70% for 10 minutes: increase capacity.
* CPU below 30%: reduce capacity according to the configured scale-in rule.

The configured rule, metric evaluation, cooldown, and instance limits determine whether a scaling action occurs.

## 4. Lab Autoscaling Configuration

The intended scale-out configuration is:

| Parameter           | Value                 |
| ------------------- | --------------------- |
| Metric source       | Current VMSS resource |
| Metric namespace    | Virtual Machine Host  |
| Metric              | Percentage CPU        |
| Condition           | Greater than          |
| Threshold           | 70%                   |
| Evaluation duration | 10 minutes            |
| Operation           | Increase percent by   |
| Percentage          | 50%                   |
| Cooldown            | 5 minutes             |

### Scale-Out Operation: Count vs. Percentage

Azure autoscaling distinguishes between fixed-count and percentage-based operations.

**Increase count by**

Adds a specified number of instances.

**Increase percent by**

Increases the instance count proportionally.

The lab specifically calls for selecting **Increase percent by** for the scale-out rule.

The actual resulting instance count depends on the existing count, scaling behavior, and configured limits.

## 5. Scale-In Configuration

The intended scale-in condition is:

| Parameter | Value          |
| --------- | -------------- |
| Metric    | Percentage CPU |
| Condition | Less than      |
| Threshold | 30%            |

The source lab instructions contain conflicting percentage values for the scale-in operation:

* The introductory explanation refers to decreasing the instance count by 20%.
* The configuration table specifies a 50% decrease.

Because these values conflict, the exact scale-in percentage must be verified against the saved Azure configuration. The documentation should reflect the actual configured value rather than assuming one value was used.

## 6. Cooldown Period

A cooldown period is the time configured between scaling actions before another scaling action can be initiated under the applicable autoscaling behavior.

The lab specifies a five-minute cooldown for the scale-out rule.

Cooldown settings help reduce rapid repeated scaling actions, but they do not guarantee that the instance count will immediately match workload demand.

## 7. Instance Limits

The lab uses these instance limits:

| Parameter         | Value |
| ----------------- | ----: |
| Minimum instances |     2 |
| Maximum instances |    10 |
| Default instances |     2 |

### Minimum Instance Count

The scale set should not scale below the configured minimum through the autoscaling policy.

### Maximum Instance Count

The scale set should not scale above the configured maximum through the autoscaling policy.

### Default Instance Count

The default count is used by the autoscaling configuration as the baseline or fallback value in applicable situations.

These settings help control capacity and costs.

## 8. Why Autoscaling May Not Trigger

A configured autoscaling rule does not guarantee that the scale set will immediately change its instance count.

Potential causes include:

* CPU utilization has not crossed the threshold.
* The configured evaluation duration has not elapsed.
* The selected metric source is incorrect.
* The cooldown period has not elapsed.
* The scale set has reached its minimum or maximum instance count.
* The rule was not saved or enabled correctly.
* Metric availability or evaluation timing affects the result.
* The subscription has insufficient quota or the required VM capacity is unavailable.

When troubleshooting, inspect the saved autoscale configuration, metric values, activity logs, and scale-set instance count.

## 9. Availability and Scaling

Availability-zone deployment and autoscaling solve different problems.

| Capability         | Purpose                                                       |
| ------------------ | ------------------------------------------------------------- |
| Availability zones | Improve resilience against certain zone-level failures        |
| Load balancing     | Distribute supported network traffic across backend instances |
| Autoscaling        | Adjust instance count based on configured metrics and rules   |
| NSGs               | Control network traffic using security rules                  |
| VMSS               | Manage a group of VM instances                                |

These capabilities can work together, but they are not interchangeable.

For example, autoscaling does not automatically distribute instances across zones unless the scale-set configuration and applicable Azure behavior support that distribution.

## 10. Cost Considerations

Autoscaling can help align compute capacity with workload demand.

However:

* Increasing the instance count generally increases compute costs.
* Larger VM sizes generally cost more than smaller sizes.
* Managed disks may continue to incur storage charges after VM deallocation.
* Load balancers, public IPs, and other associated resources may have separate charges.
* Autoscaling limits should reflect actual capacity requirements and budget constraints.

Always review the resources deployed in the lab before leaving them running.

## 11. AZ-104 Revision Points

* VMSS manages groups of virtual machine instances.
* Scale-out adds instances; scale-in removes instances.
* Percentage CPU can be used as an autoscaling metric.
* Evaluation duration and cooldown affect scaling behavior.
* Fixed-count and percentage-based operations are different.
* Minimum and maximum instance counts constrain scaling.
* Load balancing and autoscaling serve different purposes.
* NSGs filter network traffic.
* Availability zones improve resilience when supported by the overall architecture.
* Actual autoscaling behavior should be verified from the saved configuration and observed metrics.

## 12. Practical Review Checklist

* [ ] Identify the VMSS name and orchestration mode.
* [ ] Verify the configured region and availability zones.
* [ ] Review the virtual network and subnet address ranges.
* [ ] Inspect the NSG and HTTP rule.
* [ ] Verify load-balancer configuration.
* [ ] Identify the autoscaling metric and threshold.
* [ ] Confirm the scale-out operation uses the intended percentage setting.
* [ ] Verify the scale-in rule's actual saved percentage.
* [ ] Confirm minimum, maximum, and default instance counts.
* [ ] Review scaling metrics and activity logs when troubleshooting.
* [ ] Delete unused lab resources to avoid unnecessary charges.
