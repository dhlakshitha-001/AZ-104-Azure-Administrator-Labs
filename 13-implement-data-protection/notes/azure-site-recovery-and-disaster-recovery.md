# Azure Site Recovery and Disaster Recovery

## 1. Introduction

Azure Site Recovery (ASR) is a disaster recovery service that helps organizations maintain business continuity by replicating workloads to a secondary location.

It helps protect workloads against major disruptions, including regional outages.

In this lab, Azure Site Recovery is used to replicate an Azure virtual machine from Central US to West US.

## 2. What is Disaster Recovery?

Disaster Recovery (DR) is the process of restoring IT services and workloads after an unexpected disruption.

Examples include:

* Azure regional outages.
* Infrastructure failures.
* Major service disruptions.
* Other incidents that affect workload availability.

A disaster recovery solution should help organizations restore critical services within acceptable recovery time and recovery point objectives.

## 3. Azure Site Recovery

Azure Site Recovery provides replication and recovery capabilities for supported workloads.

### Main capabilities

* Replicate supported workloads.
* Maintain a secondary recovery location.
* Monitor replication health.
* Support planned and unplanned failover scenarios.
* Support disaster recovery testing.

## 4. Recovery Services Vault for Replication

The lab creates a second Recovery Services vault in a different Azure region.

### Primary and Secondary Configuration

| Component               | Primary                     | Secondary                |
| ----------------------- | --------------------------- | ------------------------ |
| Resource Group          | `az104-rg-region1`          | `az104-rg-region2`       |
| Recovery Services Vault | `az104-rsv-region1`         | `az104-rsv-region2`      |
| Region                  | Central US                  | West US                  |
| Purpose                 | Primary workload protection | Disaster recovery target |

The secondary vault must be deployed in a different region from the source VM.

## 5. Enabling Virtual Machine Replication

The lab uses the Azure Portal to configure disaster recovery for the VM.

### Configuration process

1. Open the source virtual machine.
2. Navigate to Backup + Disaster Recovery.
3. Select Disaster recovery.
4. Review the Basics tab.
5. Check the target region.
6. Open Advanced settings.
7. Review the selected resources.
8. Create the automation account as instructed.
9. Select Review + Start replication.
10. Start replication.

### Important consideration

The lab instructions emphasize that the advanced settings must be populated. Missing required settings can cause validation to fail.

## 6. Replication Synchronization

When replication is first enabled, Azure Site Recovery performs initial synchronization.

During this process, the source VM's data is replicated to the secondary location.

The synchronization process may take several minutes or longer, depending on the workload and environment.

### Replication states

| State                       | Meaning                                                     |
| --------------------------- | ----------------------------------------------------------- |
| Synchronization in progress | Initial replication is being processed                      |
| Synchronization percentage  | Shows the reported synchronization progress                 |
| Protected                   | Initial synchronization is complete and the VM is protected |

The exact status labels can vary depending on the Azure Portal experience.

## 7. Replication Health

Replication health provides information about the condition of the replicated workload.

After replication is configured:

1. Open the secondary Recovery Services vault.
2. Navigate to Protected items.
3. Select Replicated items.
4. Locate the VM.
5. Review replication health.
6. Open the VM to inspect additional details.

A healthy replication state indicates that the replication process is operating as expected.

## 8. Failover Concepts

Failover is the process of switching a protected workload from its primary location to a secondary recovery location.

### Test Failover

A test failover allows administrators to validate disaster recovery readiness without performing an actual production recovery.

It is useful for checking whether the recovery environment can operate as expected.

### Planned Failover

A planned failover is generally used when the primary environment is still available and a controlled transition is possible.

### Unplanned Failover

An unplanned failover is used when an unexpected disruption makes the primary environment unavailable.

> **Note:** This lab configures replication and verifies protection. It does not perform an actual failover.

## 9. Important Disaster Recovery Concepts

### Recovery Point Objective (RPO)

RPO represents the maximum acceptable amount of data loss measured in time.

For example, an organization with a 15-minute RPO aims to limit data loss to approximately 15 minutes of changes.

### Recovery Time Objective (RTO)

RTO represents the target time required to restore a service after a disruption.

For example, a four-hour RTO means the organization aims to restore the service within four hours.

### Backup vs Replication

Backup and replication address different recovery requirements.

| Feature            | Backup                           | Replication                           |
| ------------------ | -------------------------------- | ------------------------------------- |
| Main objective     | Recover data and workloads       | Recover workloads in another location |
| Recovery point     | Backup recovery point            | Replicated workload state             |
| Typical use        | Accidental deletion or data loss | Disaster recovery                     |
| Main Azure service | Azure Backup                     | Azure Site Recovery                   |

## 10. Important AZ-104 Concepts

* Azure Site Recovery is designed for disaster recovery.
* Replication maintains workload copies in a secondary location.
* A secondary region provides geographic separation.
* Initial synchronization occurs after replication is enabled.
* Replication health should be monitored.
* A protected VM can be considered for disaster recovery operations.
* Test failover helps validate recovery readiness.
* RPO measures acceptable data loss.
* RTO measures acceptable recovery time.

## 11. Practical Checklist

* [ ] Create a secondary Recovery Services vault.
* [ ] Use a different region from the source VM.
* [ ] Open the source VM's disaster recovery configuration.
* [ ] Review the target region.
* [ ] Review advanced settings.
* [ ] Create the automation account as required.
* [ ] Start replication.
* [ ] Review synchronization progress.
* [ ] Verify replication health.
* [ ] Confirm the VM reaches Protected status.

## References

* [Azure Site Recovery documentation](https://learn.microsoft.com/azure/site-recovery/)
* [Run a test failover for a single VM](https://learn.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure#run-a-test-failover-for-a-single-vm)
* [Azure Backup documentation](https://learn.microsoft.com/azure/backup/)
* [Protect your virtual machines by using Azure Backup](https://learn.microsoft.com/training/modules/protect-virtual-machines-with-azure-backup/)
