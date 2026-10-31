# Lab 13 – Implement Data Protection

## Overview

This lab focuses on implementing data protection and disaster recovery solutions in Microsoft Azure.

The main objective is to understand how Azure Backup protects virtual machines from accidental or malicious data loss and how Azure Site Recovery provides disaster recovery capabilities through virtual machine replication.

During this lab, a virtual machine infrastructure is deployed using an ARM template. A Recovery Services vault is created and configured to protect the virtual machine. A backup policy is assigned, backup operations are initiated, and diagnostic settings are configured to monitor backup activities.

Finally, Azure Site Recovery is configured to replicate the virtual machine to a secondary Azure region.

## Learning Objectives

By completing this lab, the following skills are practiced:

* Deploy Azure infrastructure using a custom ARM template.
* Create and configure a Recovery Services vault.
* Understand Azure Backup storage replication settings.
* Configure soft delete settings.
* Create a virtual machine backup policy.
* Enable Azure VM-level backup.
* Initiate an on-demand backup.
* Monitor Azure Backup jobs.
* Configure diagnostic settings for backup monitoring.
* Create a secondary Recovery Services vault.
* Configure Azure Site Recovery.
* Enable virtual machine replication to another Azure region.
* Review replication health and protection status.

## Lab Environment

| Component                           | Configuration                    |
| ----------------------------------- | -------------------------------- |
| Cloud Platform                      | Microsoft Azure                  |
| Azure Portal                        | https://portal.azure.com         |
| Primary Region                      | Central US                       |
| Secondary Region                    | West US                          |
| Primary Resource Group              | `az104-rg-region1`               |
| Primary Recovery Services Vault     | `az104-rsv-region1`              |
| Backup Policy                       | `az104-backup`                   |
| Secondary Resource Group            | `az104-rg-region2`               |
| Secondary Recovery Services Vault   | `az104-rsv-region2`              |
| VM Username                         | `localadmin`                     |
| VM Size                             | `Standard_D2s_v5` (if available) |
| Storage Replication                 | Geo-redundant                    |
| Soft Delete Retention               | 14 days                          |
| Backup Frequency                    | Daily                            |
| Backup Time                         | 12:00 AM                         |
| Instant Recovery Snapshot Retention | 2 days                           |

> **Note:** Azure subscription availability, regional capacity, and portal changes may affect the available configuration options. Follow the lab instructions and document any environment-specific changes.

---

# Task 1 – Use a Template to Provision an Infrastructure

## Objective

Deploy a virtual machine and virtual network using a custom ARM template. This infrastructure will be used to demonstrate Azure Backup and Azure Site Recovery.

## Deployment Files

The lab provides the following files:

* `az104-10-vms-edge-template.json`
* `az104-10-vms-edge-parameters.json`

These files are available in the lab environment under:

```text
F:\Allfiles\Labs\Lab10\
```

## Deployment Procedure

1. Sign in to the Azure Portal.
2. Search for **Deploy a custom template**.
3. Select **Build your own template in the editor**.
4. Select **Load file**.
5. Upload `az104-10-vms-edge-template.json`.
6. Review the ARM template.
7. Save the template.
8. Select **Edit parameters**.
9. Load `az104-10-vms-edge-parameters.json`.
10. Save the parameter configuration.
11. Configure the deployment:

| Setting        | Value                          |
| -------------- | ------------------------------ |
| Subscription   | Your Azure subscription        |
| Resource Group | `az104-rg-region1`             |
| Region         | Central US                     |
| VM Size        | `Standard_D2s_v5` if available |
| Username       | `localadmin`                   |
| Password       | A complex password             |

12. Select **Review + Create**.
13. Review the configuration and select **Create**.
14. Wait for the deployment to complete.
15. Select **Go to resource** and verify the deployed virtual machine.

### VM Size Availability

The lab provides alternative VM sizes if the initial deployment fails because of regional capacity or availability:

1. `Standard_D2s_v5`
2. `Standard_D2s_v6`
3. `Standard_D2s_v7`

If a deployment retry fails because of partially deployed resources or resource conflicts, the lab instructions recommend deleting `az104-rg-region1` and restarting Task 1.

> **Important:** Deleting the resource group permanently deletes its resources. Only do this when appropriate for the lab environment.

### Verification

Confirm that:

* The ARM template deployment completed successfully.
* The virtual network was created.
* The virtual machine was created.
* The VM is deployed in Central US.
* The resources are available in `az104-rg-region1`.

### Screenshots

| Screenshot                           | Description             |
| ------------------------------------ | ----------------------- |
| `01-template-deployment-editor.png`  | Custom template editor  |
| `02-template-json-loaded.png`        | Loaded ARM template     |
| `03-template-parameters-loaded.png`  | Loaded parameters       |
| `04-vm-deployment-configuration.png` | Deployment settings     |
| `05-vm-deployment-completed.png`     | Deployment success      |
| `06-deployed-vm-and-vnet.png`        | Deployed infrastructure |

---

# Task 2 – Create and Configure a Recovery Services Vault

## Objective

Create a Recovery Services vault to store and manage backup data for Azure virtual machines.

## Configuration

| Setting               | Value                   |
| --------------------- | ----------------------- |
| Subscription          | Your Azure subscription |
| Resource Group        | `az104-rg-region1`      |
| Vault Name            | `az104-rsv-region1`     |
| Region                | Central US              |
| Storage Replication   | Geo-redundant           |
| Soft Delete Retention | 14 days                 |

## Deployment Procedure

1. Open the Azure Portal.
2. Search for **Recovery Services vaults**.
3. Select **Create**.
4. Configure the vault using the values above.
5. Select **Review + Create**.
6. Verify that validation passes.
7. Select **Create**.
8. Wait for the deployment to complete.
9. Select **Go to Resource**.

## Configure Backup Storage Replication

1. Open the Recovery Services vault.
2. Navigate to **Settings > Properties**.
3. Locate **Backup Configuration**.
4. Select **Update**.
5. Review the available storage replication types.
6. Keep the default **Geo-redundant** configuration.
7. Close the configuration blade.

### Geo-Redundant Storage (GRS)

Geo-redundant storage maintains replicated backup data in a secondary Azure region.

This provides additional protection against regional failures.

The lab also introduces Cross Region Restore, which allows eligible backup data to be restored in a secondary Azure paired region.

## Configure Soft Delete

1. Navigate to the vault's **Properties**.
2. Locate **Security Settings > Soft Delete Settings**.
3. Select **Update**.
4. Verify that the soft delete retention period is **14 days**.
5. Close the configuration blade.

### Verification

Confirm that:

* The Recovery Services vault exists.
* The vault is located in Central US.
* Storage replication is Geo-redundant.
* Soft delete retention is 14 days.

### Screenshots

| Screenshot                                | Description                |
| ----------------------------------------- | -------------------------- |
| `07-recovery-services-vault-creation.png` | Vault creation settings    |
| `08-recovery-services-vault-overview.png` | Vault overview             |
| `09-vault-backup-configuration.png`       | Backup storage replication |
| `10-vault-soft-delete-settings.png`       | Soft delete configuration  |

---

# Task 3 – Configure Azure Virtual Machine-Level Backup

## Objective

Configure Azure Backup for the deployed virtual machine using a daily backup policy.

## Backup Goal Configuration

1. Open the Recovery Services vault.
2. Select **Overview**.
3. Select **+ Backup**.
4. Configure the backup goal:

| Setting                         | Value           |
| ------------------------------- | --------------- |
| Where is your workload running? | Azure           |
| What do you want to back up?    | Virtual machine |

5. Select **Backup**.
6. Review the available policy subtypes.
7. Select **Standard**.

## Create a Backup Policy

Select **Create a new policy** and configure:

| Setting                               | Value                |
| ------------------------------------- | -------------------- |
| Policy Name                           | `az104-backup`       |
| Frequency                             | Daily                |
| Time                                  | 12:00 AM             |
| Time Zone                             | Your local time zone |
| Retain instant recovery snapshots for | 2 days               |

Leave the remaining settings at their defaults unless the lab environment requires otherwise.

Select **OK** to create the backup policy.

## Select the Virtual Machine

1. In the Virtual Machines section, select **Add**.
2. Locate the virtual machine deployed in Task 1.
3. Select the VM.
4. Confirm the selection.
5. Select **Enable backup**.
6. Wait for the backup configuration to complete.
7. Select **Go to resource**.

> **VM name verification:** The source instructions use both `az-104-10-vm0` and `az104-10-vm0` in different steps. Check the actual VM name created by the ARM template and use that exact name throughout the remaining tasks.

## Review Backup Details

1. Open the Recovery Services vault.
2. Navigate to **Protected items > Backup items**.
3. Select **Azure virtual machine**.
4. Locate the protected VM.
5. Select **View details**.
6. Review:

   * Backup Pre-Check
   * Last Backup Status

The lab notes that the backup status may initially be pending.

## Initiate an On-Demand Backup

1. Open the protected VM's backup details.
2. Select **Backup now**.
3. Accept the default value in **Retain Backup Till**.
4. Select **OK**.
5. Proceed to Task 4 without waiting for the backup operation to finish.

### Verification

Confirm that:

* The backup policy was created.
* The VM was selected for protection.
* Backup was enabled.
* Backup details are accessible.
* An on-demand backup operation was initiated.

### Screenshots

| Screenshot                           | Description           |
| ------------------------------------ | --------------------- |
| `11-backup-goal-configuration.png`   | Backup goal selection |
| `12-backup-policy-configuration.png` | Backup policy         |
| `13-backup-vm-selection.png`         | Selected VM           |
| `14-backup-enabled.png`              | Backup enablement     |
| `15-protected-vm-backup-details.png` | Backup details        |
| `16-backup-now-operation.png`        | On-demand backup      |

---

# Task 4 – Monitor Azure Backup

## Objective

Configure diagnostic settings to send Recovery Services vault logs and metrics to an Azure Storage account.

This allows backup monitoring data to be retained and used by Log Analytics or third-party monitoring solutions.

## Create a Storage Account

1. Search for **Storage accounts** in the Azure Portal.
2. Select **Create**.
3. Configure the following settings:

| Setting              | Value                   |
| -------------------- | ----------------------- |
| Subscription         | Your Azure subscription |
| Resource Group       | `az104-rg-region1`      |
| Storage Account Name | A globally unique name  |
| Region               | Central US              |

4. Select **Review + Create**.
5. Complete the deployment.
6. Open the created storage account.

## Configure Diagnostic Settings

1. Open `az104-rsv-region1`.
2. Navigate to **Monitoring > Diagnostic Settings**.
3. Select **Add diagnostic setting**.
4. Enter the setting name:

```text
Logs and Metrics to storage
```

5. Select the following categories:

* Azure Backup Reporting Data
* Addon Azure Backup Job Data
* Addon Azure Backup Alert Data
* Azure Site Recovery Jobs
* Azure Site Recovery Events

6. Under Destination details, select:

**Archive to a storage account**

7. Select the storage account created earlier.
8. Select **Save**.

## Review Backup Jobs

1. Return to the Recovery Services vault.
2. Navigate to **Monitoring > Backup jobs**.
3. Locate the backup operation for the protected VM.
4. Select **View details**.
5. Review the job status and available details.

### Verification

Confirm that:

* The storage account was deployed.
* Diagnostic settings were configured.
* The required diagnostic categories were selected.
* The storage account was configured as the destination.
* Backup jobs are visible in the vault.

> **Note:** The availability and visibility of diagnostic categories and job details may depend on the Azure portal experience and the activities performed so far.

### Screenshots

| Screenshot                                 | Description               |
| ------------------------------------------ | ------------------------- |
| `17-storage-account-deployment.png`        | Storage account           |
| `18-diagnostic-settings-configuration.png` | Diagnostic configuration  |
| `19-diagnostic-settings-saved.png`         | Saved diagnostic settings |
| `20-backup-jobs-overview.png`              | Backup jobs               |
| `21-backup-job-details.png`                | Backup job details        |

---

# Task 5 – Enable Virtual Machine Replication

## Objective

Configure Azure Site Recovery to replicate the protected virtual machine to a secondary Azure region.

This provides disaster recovery capabilities in case the primary region becomes unavailable.

## Create a Secondary Recovery Services Vault

1. Search for **Recovery Services vaults**.
2. Select **Create**.
3. Configure the secondary vault:

| Setting        | Value                   |
| -------------- | ----------------------- |
| Subscription   | Your Azure subscription |
| Resource Group | `az104-rg-region2`      |
| Vault Name     | `az104-rsv-region2`     |
| Region         | West US                 |

4. Select **Review + Create**.
5. Verify the configuration.
6. Select **Create**.
7. Wait for deployment to complete.

> **Important:** The secondary vault must be in a different region from the source VM. The lab uses West US as the secondary region.

## Configure Disaster Recovery

1. Search for and open the source virtual machine.
2. Navigate to **Backup + Disaster Recovery**.
3. Select **Disaster recovery**.
4. Review the **Basics** tab.
5. Check the target region.
6. Select **Next: Advanced settings**.
7. Review the pre-populated resource selections.
8. Scroll down and create the automation account as instructed.

> **Important:** The lab warns that the settings must be populated; otherwise, validation may fail.

## Start Replication

1. Select **Review + Start replication**.
2. Review the configuration.
3. Select **Start replication**.
4. Monitor the Azure Portal notifications.
5. Allow approximately 10–15 minutes for the initial replication process, as estimated by the lab.

## Verify Replication Status

1. Search for the secondary Recovery Services vault:

```text
az104-rsv-region2
```

2. Open the vault.
3. Navigate to **Protected items > Replicated items**.
4. Locate the replicated VM.
5. Review the replication health and synchronization status.
6. Select the VM to review additional details.

### Expected Replication States

| Status                      | Description                                                 |
| --------------------------- | ----------------------------------------------------------- |
| Synchronization in progress | Initial replication is being processed                      |
| Synchronization percentage  | Indicates replication progress                              |
| Protected                   | Initial synchronization is complete and the VM is protected |

The lab expects the VM to eventually display a healthy replication state and **Protected** status after initial synchronization completes.

### Verification

Confirm that:

* The secondary Recovery Services vault was created.
* The target region is different from the source VM region.
* Disaster recovery configuration was reviewed.
* The automation account was created as required.
* Replication was started.
* The replicated VM is visible in the secondary vault.
* Replication health and protection status were reviewed.

### Screenshots

| Screenshot                               | Description              |
| ---------------------------------------- | ------------------------ |
| `22-secondary-recovery-vault.png`        | Secondary vault          |
| `23-site-recovery-basics.png`            | Disaster recovery Basics |
| `24-site-recovery-advanced-settings.png` | Advanced settings        |
| `25-replication-started.png`             | Replication started      |
| `26-replicated-items-overview.png`       | Replicated items         |
| `27-replicated-vm-details.png`           | Replicated VM details    |

---

# Key Concepts Learned

## Azure Backup

Azure Backup is an Azure service used to protect data and workloads by creating recovery points.

Key concepts:

* Backup policies
* Backup frequency
* Retention periods
* On-demand backups
* Recovery Services vaults
* Backup jobs
* Backup monitoring

## Recovery Services Vault

A Recovery Services vault stores and manages backup data and supports disaster recovery operations for eligible workloads.

Important configuration areas include:

* Storage replication
* Soft delete
* Backup policies
* Protected items
* Backup jobs
* Diagnostic settings

## Backup Policy

A backup policy controls when backups occur and how long recovery points are retained.

In this lab, the policy uses:

* Daily backup frequency
* 12:00 AM backup time
* Local time zone
* Two-day instant recovery snapshot retention

## Soft Delete

Soft delete helps protect backup data from accidental or malicious deletion by retaining deleted backup data for a configured period.

The lab verifies a 14-day retention period.

## Azure Site Recovery

Azure Site Recovery is an Azure disaster recovery service that replicates workloads to a secondary location.

It supports disaster recovery planning by allowing protected workloads to be failed over to a secondary region when necessary.

## Azure Backup vs Azure Site Recovery

| Feature           | Azure Backup                     | Azure Site Recovery                    |
| ----------------- | -------------------------------- | -------------------------------------- |
| Primary purpose   | Data protection and recovery     | Disaster recovery                      |
| Main capability   | Creates backup recovery points   | Replicates workloads                   |
| Recovery approach | Restore from backup              | Failover to secondary location         |
| Typical use case  | Accidental deletion or data loss | Regional outage or major disruption    |
| Configuration     | Backup policy and vault          | Replication and recovery configuration |

---

# Troubleshooting and Lab Notes

| Issue                                     | Recommended action                                                |
| ----------------------------------------- | ----------------------------------------------------------------- |
| VM size unavailable                       | Try the alternative VM sizes specified in the lab                 |
| ARM deployment conflict                   | Review partially deployed resources before retrying               |
| Recovery Services vault deployment fails  | Verify subscription, resource group and region                    |
| Backup policy not visible                 | Refresh the vault and review the backup configuration             |
| VM does not appear in backup selection    | Confirm the VM deployment completed successfully                  |
| Backup status is pending                  | Review the backup pre-check and allow the operation to progress   |
| Diagnostic categories unavailable         | Check the available categories in the current vault configuration |
| Replication validation fails              | Review the advanced settings and required resource selections     |
| Replication is still synchronizing        | Allow the initial synchronization to complete                     |
| VM does not appear under replicated items | Refresh the secondary vault and review replication status         |

## Important Lab Observations

1. The Recovery Services vault and the protected VM are deployed in the same primary region for this lab.
2. Geo-redundant storage provides an additional level of backup data protection.
3. Soft delete helps protect backup data against accidental deletion.
4. Backup policies define the backup schedule and retention configuration.
5. Diagnostic settings can route supported backup and Site Recovery data to a storage account.
6. Azure Site Recovery replicates the VM to a secondary region.
7. Replication health should be reviewed before considering the VM protected.

---

# Resource Cleanup

If you are using your own Azure subscription, delete the lab resources after completing the required verification.

The lab uses the following resource groups:

```text
az104-rg-region1
az104-rg-region2
```

Before deleting resources:

1. Review the resources deployed in both resource groups.
2. Review backup and replication protection dependencies.
3. Remove protected items and other dependencies where required.
4. Disable applicable security features if required for vault deletion.
5. Delete the resource groups when the resources are ready for cleanup.

> **Important:** A Recovery Services vault cannot necessarily be deleted while protected items, backup servers, or other dependencies remain. Soft delete and other security features may also affect the deletion process. Follow the Azure vault deletion requirements before attempting to remove the vault.

For a disposable lab environment, resource-group deletion can be used after the required dependencies have been addressed.

---

# Lab Completion Summary

This lab introduced Azure data protection and disaster recovery capabilities.

The following areas were covered:

* [ ] Deployed VM infrastructure using a custom ARM template.
* [ ] Created a Recovery Services vault.
* [ ] Reviewed backup storage replication settings.
* [ ] Verified soft delete configuration.
* [ ] Created an Azure VM backup policy.
* [ ] Enabled VM backup.
* [ ] Initiated an on-demand backup.
* [ ] Created a storage account.
* [ ] Configured diagnostic settings.
* [ ] Reviewed Azure Backup jobs.
* [ ] Created a secondary Recovery Services vault.
* [ ] Configured Azure Site Recovery.
* [ ] Started VM replication.
* [ ] Reviewed replication health and protection status.

**Lab Status:** In Progress / Completed

> Update the lab status and checkboxes only after completing and verifying the corresponding activities.

---

# Additional Learning Resources

* [Introduction to Azure Backup](https://learn.microsoft.com/training/modules/intro-to-azure-backup/)
* [Protect your virtual machines by using Azure Backup](https://learn.microsoft.com/training/modules/protect-virtual-machines-with-azure-backup/)
* [Create a Recovery Services vault](https://learn.microsoft.com/azure/backup/backup-create-recovery-services-vault)
* [Cross Region Restore](https://learn.microsoft.com/azure/backup/backup-create-recovery-services-vault#set-cross-region-restore)
* [Run a test failover for a single VM](https://learn.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure#run-a-test-failover-for-a-single-vm)

---

**AZ-104 Microsoft Azure Administrator – Lab 13**

*Practical Azure administration, backup management, and disaster recovery.*
