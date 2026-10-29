# VM Scaling and Availability

## Overview

Azure virtual machine management includes selecting appropriate compute capacity, maintaining availability, and managing persistent storage.

This document summarizes the concepts explored in the **Manage Virtual Machines** lab.

## 1. Vertical Scaling

Vertical scaling means changing the size of an existing virtual machine to increase or decrease its compute capacity.

For example:

| VM Size           | vCPUs | Memory |
| ----------------- | ----: | -----: |
| `Standard_D2s_v5` |     2 |  8 GiB |
| `Standard_D4s_v5` |     4 | 16 GiB |

Increasing the VM size can provide more CPU and memory resources for workloads that need additional capacity.

### Important Points

* Vertical scaling changes the resources allocated to an existing VM.
* Some resize operations require the VM to be stopped and deallocated.
* The target size must be available for the VM's region and deployment configuration.
* Subscription quota and temporary capacity constraints can affect resizing.
* A larger VM may increase operating costs.

## 2. Horizontal Scaling

Horizontal scaling changes the number of VM instances rather than the size of an individual VM.

For example, a scale set may increase its instance count from two to four when workload demand increases.

### Vertical vs. Horizontal Scaling

| Feature          | Vertical Scaling                              | Horizontal Scaling                                         |
| ---------------- | --------------------------------------------- | ---------------------------------------------------------- |
| Main change      | VM size                                       | Number of instances                                        |
| Compute capacity | Adds resources to an existing VM              | Adds or removes VM instances                               |
| Common use       | A workload needs more CPU or memory per VM    | A workload can distribute demand across multiple instances |
| Azure example    | Resize `Standard_D2s_v5` to `Standard_D4s_v5` | Increase a VMSS from 2 to 4 instances                      |

Horizontal scaling works best when the application can distribute work across multiple instances and handle instance replacement or removal appropriately.

## 3. Availability Zones

Availability zones are separate physical locations within supported Azure regions.

Deploying resources across multiple zones can help reduce the impact of a failure affecting one zone.

### Example

The lab deploys two VMs in different zones:

* `az104-vm1` in Zone 1.
* `az104-vm2` in Zone 2.

The VM Scale Set is configured to use Zones 1, 2, and 3, where supported.

### Important Points

* Not every Azure region supports availability zones.
* Availability-zone support varies by service and resource type.
* VM sizes and disk SKUs may have zone-specific restrictions.
* A multi-zone deployment does not automatically guarantee application-level availability.
* The application, networking, storage, and failover design must support the desired resilience.
* SLA eligibility depends on the actual deployment architecture and the applicable Azure service terms.

## 4. VM Power States

The VM power state affects the availability of the VM and its billing behavior.

| State                 | General Meaning                                                   |
| --------------------- | ----------------------------------------------------------------- |
| Running               | The VM is operating and allocated compute resources.              |
| Stopped               | The VM is stopped but may still have compute resources allocated. |
| Stopped (deallocated) | The VM's allocated compute resources have been released.          |

### Deallocation

Deallocation is useful when a VM does not need to remain available and the goal is to stop its compute charges.

However, deallocation does not mean that all associated resources are deleted or free.

Retained resources such as managed disks and certain networking resources can continue to incur charges.

## 5. Azure Managed Disks

Azure managed disks provide persistent block storage for virtual machines.

A VM can use an OS disk and, where needed, one or more data disks.

### OS Disk

The OS disk stores the operating system and related system files.

### Data Disk

A data disk provides additional persistent storage for applications, files, and workload data.

### Disk SKU Availability

Azure managed disk options depend on the supported region, zone, configuration, and disk requirements.

The lab's intended data disk exercise uses:

| Parameter   | Intended configuration |
| ----------- | ---------------------- |
| Disk name   | `vm1-disk1`            |
| Initial SKU | Standard HDD           |
| Capacity    | 32 GiB                 |
| Target SKU  | Standard SSD           |

The required Standard HDD option was unavailable in the selected region/zone configuration. The exercise is recorded as blocked by the environment limitation.

### Important Points

* Detaching a data disk is different from deleting it.
* A detached disk can be retained for later use, subject to the relevant Azure requirements.
* Changing a disk SKU requires the target SKU to be supported.
* Managed disks can continue to incur storage charges when the VM is deallocated.
* Always verify disk state and SKU in Azure before considering a disk-management operation complete.

## 6. Troubleshooting VM Availability and Resizing

If a VM cannot be resized or deployed in a particular zone, check:

1. Whether the VM size is available in the selected region.
2. Whether the size supports the selected availability zone.
3. Whether the subscription has sufficient vCPU quota.
4. Whether the target capacity is temporarily unavailable.
5. Whether the VM must be stopped and deallocated before resizing.
6. Whether the target VM configuration supports the requested operation.

For managed disks, verify the disk SKU options available for the actual deployment configuration.

## 7. AZ-104 Revision Points

* Vertical scaling changes the size of a VM.
* Horizontal scaling changes the number of instances.
* Deallocation releases allocated compute resources.
* Managed disks provide persistent storage.
* VM and disk availability varies by region and configuration.
* Availability zones improve resilience when the overall architecture is designed appropriately.
* Deallocating a VM does not automatically delete its disks.
* VM sizes, disk types, and retained resources affect cost.

## 8. Practical Review Checklist

* [ ] Identify the region and availability zone used by each VM.
* [ ] Verify VM size and operating system configuration.
* [ ] Distinguish between a stopped VM and a deallocated VM.
* [ ] Explain the difference between vertical and horizontal scaling.
* [ ] Identify the OS disk and data disk roles.
* [ ] Explain why a disk SKU might not be available.
* [ ] Verify that required resources are retained or deleted as intended.
* [ ] Review the cost implications of VMs, disks, and networking resources.
