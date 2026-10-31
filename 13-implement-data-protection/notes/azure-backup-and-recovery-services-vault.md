# Azure Backup and Recovery Services Vault

## 1. Introduction

Azure Backup is a cloud-based data protection service that helps protect Azure resources and supported workloads from accidental deletion, data corruption, and other data loss scenarios.

It provides centralized backup management through Azure Backup and Recovery Services vaults.

## 2. Recovery Services Vault

A Recovery Services vault is an Azure storage management entity used to store and organize backup data and support disaster recovery operations for supported workloads.

### Main responsibilities

* Store backup recovery points.
* Manage backup policies.
* Track protected workloads.
* Monitor backup jobs.
* Configure backup-related security settings.
* Support Azure Site Recovery operations.

### Lab Configuration

| Setting               | Value               |
| --------------------- | ------------------- |
| Vault Name            | `az104-rsv-region1` |
| Resource Group        | `az104-rg-region1`  |
| Region                | Central US          |
| Storage Replication   | Geo-redundant       |
| Soft Delete Retention | 14 days             |

## 3. Backup Storage Replication

Storage replication determines how backup data is replicated for durability and availability.

### Geo-Redundant Storage (GRS)

Geo-redundant storage replicates data to a secondary Azure region.

Benefits:

* Additional protection against regional failures.
* Supports eligible cross-region restore capabilities.
* Provides geographically separated data copies.

### Important considerations

* Storage replication settings should be selected based on recovery requirements.
* Some vault configuration settings can only be changed before backup items are registered.
* Cross-region restore availability depends on the selected configuration and supported workload.

## 4. Soft Delete

Soft delete helps protect backup data from accidental or malicious deletion.

When backup data is deleted, soft delete can retain it for a configured period instead of immediately removing it permanently.

### Lab configuration

```text
Soft Delete Retention Period: 14 days
```

### Why it matters

* Helps protect against accidental deletion.
* Adds protection against malicious deletion attempts.
* Provides an opportunity to recover eligible deleted backup data.

## 5. Azure Backup Policies

A backup policy defines the schedule and retention settings used for protected workloads.

### Important policy components

| Component                           | Description                                      |
| ----------------------------------- | ------------------------------------------------ |
| Policy Name                         | Identifies the backup policy                     |
| Frequency                           | Determines how often backups run                 |
| Backup Time                         | Defines the scheduled backup time                |
| Time Zone                           | Determines the time reference                    |
| Retention                           | Controls how long recovery points are retained   |
| Instant Recovery Snapshot Retention | Controls retention of instant recovery snapshots |

### Lab Backup Policy

| Setting                             | Configuration   |
| ----------------------------------- | --------------- |
| Policy Name                         | `az104-backup`  |
| Policy Subtype                      | Standard        |
| Frequency                           | Daily           |
| Time                                | 12:00 AM        |
| Time Zone                           | Local time zone |
| Instant Recovery Snapshot Retention | 2 days          |

## 6. Backup Operations

### Scheduled Backup

A scheduled backup runs according to the configured backup policy.

### Backup Now

Backup Now initiates an on-demand backup without waiting for the next scheduled backup.

The lab uses this feature to initiate an additional backup operation.

### Backup Pre-Check

Backup pre-checks help identify conditions that may affect the backup operation.

### Backup Job

A backup job represents an operation performed by Azure Backup.

Examples include:

* Initial backup
* Scheduled backup
* On-demand backup
* Restore operations

Backup jobs can be reviewed through the Recovery Services vault.

## 7. Monitoring Azure Backup

Azure Backup monitoring provides visibility into backup activities and job status.

### Diagnostic Settings

Diagnostic settings can route supported logs and metrics to destinations such as:

* Azure Storage accounts
* Log Analytics workspaces
* Event Hubs

### Lab diagnostic categories

The lab selects the following categories:

1. Azure Backup Reporting Data
2. Addon Azure Backup Job Data
3. Addon Azure Backup Alert Data
4. Azure Site Recovery Jobs
5. Azure Site Recovery Events

### Storage Account Destination

The lab uses an Azure Storage account to archive the selected diagnostic data.

Example configuration:

```text
Resource Group: az104-rg-region1
Region: Central US
Destination: Archive to a storage account
Diagnostic Setting: Logs and Metrics to storage
```

## 8. Azure Backup vs Azure Site Recovery

| Area               | Azure Backup            | Azure Site Recovery            |
| ------------------ | ----------------------- | ------------------------------ |
| Purpose            | Backup and recovery     | Disaster recovery              |
| Data handling      | Creates recovery points | Replicates workloads           |
| Main configuration | Backup policy           | Replication settings           |
| Recovery approach  | Restore data or VM      | Failover to secondary location |
| Typical scenario   | Data loss               | Regional outage                |

## 9. Important AZ-104 Concepts

Remember these concepts:

* A Recovery Services vault is used to manage backup data and supported recovery operations.
* A backup policy defines backup frequency and retention.
* Backup Now starts an on-demand backup.
* Soft delete helps protect deleted backup data.
* Geo-redundant storage replicates data to a secondary region.
* Diagnostic settings route supported monitoring data to configured destinations.
* Backup jobs provide information about backup operations.

## 10. Practical Checklist

* [ ] Create a Recovery Services vault.
* [ ] Review storage replication configuration.
* [ ] Verify soft delete settings.
* [ ] Create a backup policy.
* [ ] Enable VM backup.
* [ ] Initiate Backup Now.
* [ ] Review backup details.
* [ ] Configure diagnostic settings.
* [ ] Review backup jobs.

## References

* [Azure Backup documentation](https://learn.microsoft.com/azure/backup/)
* [Recovery Services vault overview](https://learn.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)
* [Azure Backup monitoring](https://learn.microsoft.com/azure/backup/monitor-azure-backup)
* [Introduction to Azure Backup](https://learn.microsoft.com/training/modules/intro-to-azure-backup/)
