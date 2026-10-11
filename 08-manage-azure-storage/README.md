# Lab 07 – Manage Azure Storage

[![Azure](https://img.shields.io/badge/Microsoft%20Azure-0089D6?logo=microsoftazure\&logoColor=white)](https://azure.microsoft.com/)
[![AZ-104](https://img.shields.io/badge/AZ--104-Azure%20Administrator-0078D4)](https://learn.microsoft.com/credentials/certifications/azure-administrator/)
[![Storage](https://img.shields.io/badge/Azure%20Storage-0078D4)](https://azure.microsoft.com/products/storage)

> **Microsoft AZ-104: Azure Administrator – Manage Azure Storage**

---

## 📌 Lab Overview

This lab focuses on creating, configuring, securing, and managing **Azure Storage** resources.

The lab demonstrates how Azure Storage can be used to store business data while applying appropriate controls for:

* Data redundancy
* Network access
* Authentication and authorization
* Blob lifecycle management
* Immutable storage
* Temporary data access
* Azure File shares
* Virtual network-based access restrictions

The original Microsoft lab instructions use **East US** and estimate approximately **50 minutes** for completion.

---

## 🎯 Learning Objectives

By completing this lab, I learned how to:

* Create and configure an Azure Storage account.
* Configure Standard storage performance.
* Configure Geo-Redundant Storage (GRS).
* Enable read access to the secondary region.
* Configure public network access restrictions.
* Configure Azure Storage lifecycle management.
* Create a private Blob container.
* Configure immutable Blob Storage.
* Configure time-based retention.
* Upload and manage Blob data.
* Assign Azure Storage RBAC roles.
* Generate a user delegation SAS.
* Create and configure Azure File shares.
* Use Azure Storage Browser.
* Configure a Microsoft.Storage service endpoint.
* Restrict storage access to a virtual network.
* Validate storage access restrictions.

---

# 🏗️ Architecture

![Azure Storage Lab Architecture](./screenshots/00-architecture-diagram.png)

### Lab Architecture Overview

```text
                         Azure Storage Account
                                  |
             +--------------------+--------------------+
             |                    |                    |
             v                    v                    v
        Blob Storage          Azure Files          Storage Config
             |                    |                    |
             v                    v                    |
       Container: data        Share: share1          |
             |                    |                    |
       +-----+------+             |                    |
       |            |             |                    |
      RBAC         SAS            |              Lifecycle
       |            |             |                    |
       |      Immutable           |                    |
       |       180 days           |                    |
       |                          |                    |
       +-------------+------------+--------------------+
                     |
                     v
              Network Security
                     |
                     v
                   vnet1
                     |
              Default Subnet
                     |
            Microsoft.Storage
             Service Endpoint
```

---

# 📚 Lab Tasks

The lab contains three main tasks:

| Task       | Description                                    |
| ---------- | ---------------------------------------------- |
| **Task 1** | Create and configure a storage account         |
| **Task 2** | Create and configure secure Blob Storage       |
| **Task 3** | Create and configure secure Azure File Storage |

---

# 🔹 Task 1 – Create and Configure a Storage Account

## 1. Create the Storage Account

The storage account is created using the following configuration:

| Setting                  | Value                                        |
| ------------------------ | -------------------------------------------- |
| Resource Group           | `az104-rg7-lod66087236`                      |
| Storage Account Name     | Globally unique name                         |
| Region                   | East US                                      |
| Performance              | Standard                                     |
| Preferred Storage Type   | Azure Blob Storage / Azure Data Lake Storage |
| Redundancy               | Geo-Redundant Storage (GRS)                  |
| Read Access to Secondary | Enabled                                      |

The lab specifically configures Standard performance and GRS, with read access to the secondary region.

![Storage Account Creation](./screenshots/01-storage-account-create.png)

---

## 2. Storage Account Basics

The Basics configuration was reviewed before deployment.

![Storage Account Basics](./screenshots/02-storage-account-basics.png)

### Standard vs Premium

The lab highlights that:

* **Standard** is suitable for most applications.
* **Premium** is intended for enterprise or high-performance applications.

For this lab, **Standard** performance was selected.

---

## 3. Configure Storage Redundancy

The storage account was configured with:

```text
Geo-Redundant Storage (GRS)
```

![Storage Account Redundancy](./screenshots/03-storage-account-redundancy.png)

### Why GRS?

GRS provides replication of storage data to a secondary region, helping protect data against regional failures.

The lab also enables read access to data in the secondary region.

---

## 4. Disable Public Network Access

During initial configuration, public network access was disabled.

![Storage Account Networking Disabled](./screenshots/04-storage-account-networking-disabled.png)

```text
Public Network Access
        ↓
     Disabled
```

This provides a restrictive starting configuration.

---

## 5. Storage Account Deployment

After completing the configuration, the storage account was deployed successfully.

![Storage Account Deployment Success](./screenshots/05-storage-account-deployment-success.png)

The storage account was then opened for further configuration.

---

## 6. Storage Account Overview

![Storage Account Overview](./screenshots/06-storage-account-overview.png)

An Azure Storage account can provide storage services including:

```text
Storage Account
├── Blob Containers
├── File Shares
├── Queues
└── Tables
```

The storage account acts as the main management and security boundary for these storage services.

---

## 7. Configure Selected Network Access

Public network access was changed to allow access from **selected networks**.

![Selected Networks](./screenshots/07-storage-account-network-selected-networks.png)

The client IPv4 address was initially added as an allowed source.

![Client IP Allowed](./screenshots/08-storage-account-client-ip-allowed.png)

This demonstrates that access can be restricted instead of allowing traffic from all networks.

---

## 8. Review Storage Redundancy

The configured redundancy was reviewed from the storage account.

![Storage Redundancy Overview](./screenshots/09-storage-account-redundancy-overview.png)

### Azure Storage Redundancy Models

```text
LRS
↓
Local redundancy

ZRS
↓
Zone redundancy

GRS
↓
Geo-redundancy
```

The lab uses **GRS**.

---

## 9. Configure Lifecycle Management

A lifecycle management rule named:

```text
Movetocool
```

was created.

![Lifecycle Management Rule](./screenshots/10-lifecycle-management-rule.png)

The rule was configured to move base blobs to the Cool tier when they have not been modified for more than **30 days**.

![Move to Cool Condition](./screenshots/11-lifecycle-movetocool-condition.png)

### Lifecycle Flow

```text
       Hot
        |
        | Last modified > 30 days
        v
       Cool
```

### Why Lifecycle Management?

Lifecycle management can automatically move data between storage tiers based on defined conditions.

This is useful for reducing storage costs when data becomes less frequently accessed.

---

# 🔹 Task 2 – Create and Configure Secure Blob Storage

## 10. Create a Blob Container

A Blob container named:

```text
data
```

was created.

![Blob Container Created](./screenshots/12-blob-container-created.png)

The container was configured with:

```text
Public Access:
Private
```

![Private Container Access](./screenshots/13-container-private-access.png)

The lab specifically uses a private container so anonymous access is not permitted.

---

## 11. Configure Immutable Blob Storage

A time-based immutable storage policy was configured.

![Blob Immutability Policy](./screenshots/14-blob-immutability-policy.png)

### Policy Configuration

| Setting          | Value                |
| ---------------- | -------------------- |
| Policy Type      | Time-based retention |
| Retention Period | 180 days             |

![180-Day Retention](./screenshots/15-blob-retention-180-days.png)

### Immutable Storage

Immutable storage provides a **Write Once, Read Many (WORM)** style of protection.

The configured retention period helps protect the stored data from modification or deletion during the specified period.

---

## 12. Configure Storage Account Key Access

Storage account key access was enabled for the lab configuration.

![Storage Account Key Access](./screenshots/16-storage-account-key-access.png)

The lab then configures role-based access to the storage data.

---

## 13. Configure Blob RBAC

The following role was assigned to the user:

```text
Storage Blob Data Contributor
```

![Storage Blob Data Contributor](./screenshots/17-blob-data-contributor-role.png)

A file-storage role was also configured:

```text
Storage File Data Privileged Contributor
```

![Storage File Data Privileged Contributor](./screenshots/18-storage-file-data-contributor-role.png)

### Why RBAC?

Azure RBAC provides identity-based authorization.

Instead of giving every user administrative permissions, users can be assigned appropriate roles based on their required access.

---

## 14. Upload a Blob

A file was uploaded into the `data` container.

The upload configuration used:

| Setting          | Value                            |
| ---------------- | -------------------------------- |
| Blob Type        | Block blob                       |
| Block Size       | 4 MiB                            |
| Access Tier      | Hot                              |
| Upload Folder    | `securitytest`                   |
| Encryption Scope | Existing default container scope |

![Blob Upload Configuration](./screenshots/19-blob-upload-configuration.png)

![Blob Upload Success](./screenshots/20-blob-upload-success.png)

The lab notes that a small file is recommended for the exercise.

---

## 15. Review Blob Properties

The uploaded file was selected to review its properties and available management operations.

![Blob Overview Properties](./screenshots/21-blob-overview-properties.png)

Available operations include:

* Download
* Delete
* Change tier
* Acquire lease
* Generate SAS

---

## 16. Test Private Blob Access

The blob URL was copied and opened in an InPrivate browser.

![Private Blob Access Denied](./screenshots/22-private-blob-access-denied.png)

The expected result is an error such as:

```text
ResourceNotFound
```

or:

```text
PublicAccessNotPermitted
```

This is expected because the container is configured as:

```text
Private (no anonymous access)
```

The lab explicitly identifies this behavior as expected.

---

## 17. Generate a Blob SAS

A Shared Access Signature was generated for the uploaded blob.

![Generate Blob SAS](./screenshots/23-generate-blob-sas.png)

The lab uses:

```text
Signing Method:
User delegation key
```

---

## 18. Configure User Delegation SAS

The SAS was configured with:

| Setting              | Value               |
| -------------------- | ------------------- |
| Signing Method       | User delegation key |
| Permission           | Read                |
| Start Date           | Previous day        |
| Start Time           | Current time        |
| Expiry Date          | Next day            |
| Expiry Time          | Current time        |
| Allowed IP Addresses | Blank               |

![User Delegation SAS Settings](./screenshots/24-user-delegation-sas-settings.png)

The lab specifically configures a read-only user delegation SAS with a limited validity period.

---

## 19. Test SAS Access

The generated Blob SAS URL was opened in an InPrivate browser.

![Blob SAS Access Success](./screenshots/25-blob-sas-access-success.png)

The blob content should now be accessible.

### Private Access vs SAS

```text
Private Blob
     |
     +---- Direct URL
     |       |
     |       X
     |     Denied
     |
     +---- SAS URL
             |
             v
       Temporary Access
```

This demonstrates how a private blob can be shared through controlled, time-limited access.

---

# 🔹 Task 3 – Create and Configure Azure File Storage

## 20. Create an Azure File Share

An Azure File share named:

```text
share1
```

was created.

The selected access tier was:

```text
Transaction optimized
```

Backup was disabled for this lab configuration.

![File Share Created](./screenshots/26-file-share-created.png)

![File Share Configuration](./screenshots/27-file-share-configuration.png)

The lab uses the file share to explore Azure Files and Storage Browser.

---

## 21. Explore Storage Browser

Azure Storage Browser was opened from the storage account.

![Storage Browser File Share](./screenshots/28-storage-browser-file-share.png)

The `share1` file share was verified.

Storage Browser can be used to:

* View storage resources.
* Navigate directories.
* Create directories.
* Upload files.
* Manage file-share content.

---

## 22. Upload a File to Azure Files

A file was uploaded to the `share1` file share.

![File Upload Success](./screenshots/29-file-upload-success.png)

This demonstrates how Storage Browser can be used to manage Azure File shares from the Azure portal.

---

## 23. Create the Virtual Network

A virtual network named:

```text
vnet1
```

was created in the lab resource group.

![Virtual Network Created](./screenshots/30-vnet1-created.png)

The virtual network will later be used to restrict access to the storage account.

---

## 24. Configure Microsoft.Storage Service Endpoint

A service endpoint for:

```text
Microsoft.Storage
```

was added to the default subnet.

![Microsoft.Storage Service Endpoint](./screenshots/31-storage-service-endpoint.png)

### Network Flow

```text
vnet1
  |
  v
Default Subnet
  |
  | Microsoft.Storage
  | Service Endpoint
  v
Azure Storage
```

The lab specifically configures the `Microsoft.Storage` service endpoint on the default subnet.

---

## 25. Restrict Storage Access to the VNet

The storage account networking configuration was modified to allow:

```text
Virtual Network:
vnet1

Subnet:
default
```

The previously configured client IPv4 address was removed.

![Storage VNet Access Restricted](./screenshots/32-storage-vnet-access-restricted.png)

### Final Network Access Model

```text
                    Azure Storage
                         |
                         |
                    Network Rules
                         |
                         v
                       vnet1
                         |
                    default subnet
                         |
                 Microsoft.Storage
                  Service Endpoint
```

The lab notes that the storage account should now only be accessed from the configured virtual network.

---

## 26. Validate Unauthorized Access

Storage Browser was refreshed after applying the network restriction.

![Storage Browser Unauthorized](./screenshots/33-storage-browser-unauthorized.png)

The expected result is an authorization error because the current client is not connecting from the allowed virtual network.

The restriction may take a few minutes to become effective.

---

## 27. Final Storage Networking State

![Final Storage Networking State](./screenshots/34-storage-networking-final-state.png)

The final configuration demonstrates that storage access can be restricted to a specific Azure virtual network.

---

# 🔐 Storage Security Controls

This lab demonstrates several Azure Storage security mechanisms:

| Security Control              | Purpose                                       |
| ----------------------------- | --------------------------------------------- |
| Private Blob container        | Prevent anonymous access                      |
| Azure RBAC                    | Control data access by identity               |
| User delegation SAS           | Provide temporary controlled access           |
| Immutable storage             | Protect data during retention                 |
| Network restrictions          | Control network sources                       |
| Service endpoint              | Restrict storage to a VNet subnet             |
| Storage account configuration | Control security and data-protection settings |

---

# 🌐 Storage Network Security

The lab demonstrates progressive network restriction.

### Initial State

```text
Public Network Access
        |
        v
     Disabled
```

### Selected Network State

```text
Public Network Access
        |
        v
Selected Networks
        |
        +---- Client IP
```

### Final State

```text
Public Network Access
        |
        v
Selected Network
        |
        v
      vnet1
        |
        v
Default Subnet
        |
        v
Microsoft.Storage
```

This demonstrates how Azure Storage network access can be narrowed from broad public access to a specific virtual network.

---

# 🗄️ Blob Storage Concepts

## Blob

A Blob is an object used to store unstructured data.

Examples include:

* Images
* Videos
* Documents
* Backups
* Multimedia

## Container

A container provides a logical organization boundary for blobs.

```text
Storage Account
      |
      v
Container
      |
      +---- Blob
      +---- Blob
      +---- Blob
```

## Access Tier

The lab demonstrates:

* Hot
* Cool

The lifecycle rule automatically moves older base blobs from Hot to Cool after the configured condition is met.

---

# 📁 Azure Files Concepts

Azure Files provides managed shared file storage.

```text
Storage Account
      |
      v
File Share
      |
      +---- Directory
      |       |
      |       +---- File
      |
      +---- Directory
              |
              +---- File
```

In this lab:

```text
File Share:
share1
```

was created and managed using Storage Browser.

---

# ⚖️ Blob Storage vs Azure Files

| Feature           | Azure Blob Storage          | Azure Files                  |
| ----------------- | --------------------------- | ---------------------------- |
| Storage Model     | Object storage              | Shared file storage          |
| Organization      | Containers                  | File shares                  |
| Data              | Unstructured objects        | Files and directories        |
| Lab Resource      | `data`                      | `share1`                     |
| Example           | Images / videos / documents | Shared files                 |
| Access Tier       | Hot / Cool demonstrated     | Transaction optimized        |
| Lifecycle         | Demonstrated                | Not demonstrated             |
| Immutable Storage | Demonstrated                | Not demonstrated             |
| SAS               | Demonstrated                | Not demonstrated in this lab |
| Storage Browser   | Supported                   | Used in this lab             |

### Simple Memory Rule

> **Blob = Objects inside containers**

> **Files = Files inside shared file shares**

---

# 🔄 Storage Access Flow

## Direct Blob Access

```text
Client
  |
  v
Blob URL
  |
  v
Private Container
  |
  X
Access Denied
```

## SAS Blob Access

```text
Client
  |
  v
SAS URL
  |
  v
Private Blob
  |
  v
Read Access
```

## VNet-Restricted Storage

```text
External Client
      |
      X
      |
 Azure Storage
      ^
      |
      |
    vnet1
      |
 Default Subnet
      |
Microsoft.Storage
Service Endpoint
```

---

# 🧠 Important AZ-104 Concepts

## 1. Storage Account

A storage account provides the namespace and configuration boundary for Azure Storage services.

## 2. GRS

**Geo-Redundant Storage** replicates data to a secondary region.

## 3. Lifecycle Management

Automatically performs storage actions based on configured conditions.

## 4. Immutable Storage

Provides WORM-style data protection using retention policies.

## 5. RBAC

Controls what an identity can do with Azure resources and storage data.

## 6. SAS

Provides controlled, limited access to storage resources.

## 7. Service Endpoint

Allows a virtual network subnet to access supported Azure services and can be used with storage network restrictions.

## 8. Azure Files

Provides managed shared file storage using file shares and directories.

---

# 📊 Lab Validation

| Validation                 | Expected Result       |
| -------------------------- | --------------------- |
| Storage account deployment | Successful            |
| Performance                | Standard              |
| Redundancy                 | GRS                   |
| Secondary read access      | Enabled               |
| Lifecycle rule             | `Movetocool`          |
| Blob container             | `data`                |
| Container access           | Private               |
| Immutable retention        | 180 days              |
| Blob upload                | Successful            |
| Direct blob URL            | Access denied         |
| User delegation SAS        | Generated             |
| SAS access                 | Successful            |
| File share                 | `share1`              |
| File share tier            | Transaction optimized |
| Storage Browser            | Successfully used     |
| VNet                       | `vnet1`               |
| Service endpoint           | Microsoft.Storage     |
| Final network restriction  | VNet-based            |
| External access test       | Unauthorized          |

---

# 🛠️ Troubleshooting Notes

### Blob URL returns an access error

This is expected when accessing a private container directly.

Check:

```text
Container
    ↓
Public Access Level
    ↓
Private
```

Use the generated SAS URL when temporary access is required.

---

### Storage Browser authorization error

Verify:

* Correct Azure account is selected.
* Required RBAC role is assigned.
* Storage networking configuration allows the current network.

---

### Microsoft.Storage service endpoint is unavailable

The lab provides an Azure CLI alternative:

```bash
az network vnet subnet update \
  --resource-group az104-rg7-lod66087236 \
  --vnet-name vnet1 \
  --name default \
  --service-endpoints Microsoft.Storage
```

The lab specifically provides this command as an alternative when the service endpoint cannot be found through the portal.

---

### Network restriction does not appear immediately

After changing storage networking rules, allow some time for the configuration to take effect.

The lab notes that this can take a couple of minutes.

---

# 💰 Cost Optimization

This lab also demonstrates storage cost optimization.

### Lifecycle Management

```text
Frequently Accessed
        |
        v
       Hot
        |
        | 30+ days
        v
      Cool
```

Moving infrequently accessed data to a more appropriate storage tier can help optimize storage costs.

The scenario specifically focuses on reducing the cost of storing infrequently accessed files.

---

# 🎓 Skills Demonstrated

* Azure Storage Administration
* Azure Blob Storage
* Azure Files
* Storage Redundancy
* Geo-Redundant Storage
* Storage Lifecycle Management
* Blob Access Tiers
* Immutable Storage
* Azure RBAC
* User Delegation SAS
* Azure Storage Security
* Azure Storage Networking
* Virtual Networks
* Service Endpoints
* Access Control
* Cloud Storage Architecture

---

# 📝 What I Learned

Through this lab, I gained practical experience with:

1. Creating Azure Storage accounts.
2. Configuring storage redundancy.
3. Configuring network access controls.
4. Creating private Blob containers.
5. Configuring immutable Blob retention.
6. Managing Blob access tiers.
7. Creating lifecycle management rules.
8. Assigning storage RBAC roles.
9. Generating user delegation SAS URLs.
10. Creating Azure File shares.
11. Using Azure Storage Browser.
12. Configuring Microsoft.Storage service endpoints.
13. Restricting storage access to a virtual network.
14. Testing storage authorization and network restrictions.

---

# 🧹 Cleanup

If using a personal Azure subscription, delete the lab resource group after completing the lab and capturing all required screenshots.

Resource group:

```text
az104-rg7-lod66087236
```

### Azure CLI

```bash
az group delete --name az104-rg7-lod66087236
```

### Azure PowerShell

```powershell
Remove-AzResourceGroup -Name az104-rg7-lod66087236
```

> ⚠️ **Warning:** Deleting the resource group permanently removes its dependent resources. Only perform this if the resources are dedicated to this lab.

The Microsoft lab also recommends deleting the lab resource group when working in a personal subscription to minimize unnecessary costs.

---

# 🏁 Lab Completion

**Status:** ✅ Completed

### Completed Tasks

* [x] Created Azure Storage account
* [x] Configured Standard performance
* [x] Configured GRS
* [x] Enabled secondary read access
* [x] Configured storage network access
* [x] Created Blob container
* [x] Configured private Blob access
* [x] Configured 180-day immutable retention
* [x] Uploaded Blob data
* [x] Configured Azure Storage RBAC
* [x] Generated user delegation SAS
* [x] Tested private Blob access
* [x] Tested SAS-based Blob access
* [x] Created Azure File share
* [x] Used Storage Browser
* [x] Created `vnet1`
* [x] Configured Microsoft.Storage service endpoint
* [x] Restricted storage access to the VNet
* [x] Validated unauthorized access

---

# 📂 Repository Structure

```text
08-manage-azure-storage/
│
├── README.md
│
├── screenshots/
│   ├── 00-architecture-diagram.png
│   │
│   ├── 01-storage-account-create.png
│   ├── 02-storage-account-basics.png
│   ├── 03-storage-account-redundancy.png
│   ├── 04-storage-account-networking-disabled.png
│   ├── 05-storage-account-deployment-success.png
│   ├── 06-storage-account-overview.png
│   ├── 07-storage-account-network-selected-networks.png
│   ├── 08-storage-account-client-ip-allowed.png
│   ├── 09-storage-account-redundancy-overview.png
│   ├── 10-lifecycle-management-rule.png
│   ├── 11-lifecycle-movetocool-condition.png
│   │
│   ├── 12-blob-container-created.png
│   ├── 13-container-private-access.png
│   ├── 14-blob-immutability-policy.png
│   ├── 15-blob-retention-180-days.png
│   ├── 16-storage-account-key-access.png
│   ├── 17-blob-data-contributor-role.png
│   ├── 18-storage-file-data-contributor-role.png
│   ├── 19-blob-upload-configuration.png
│   ├── 20-blob-upload-success.png
│   ├── 21-blob-overview-properties.png
│   ├── 22-private-blob-access-denied.png
│   ├── 23-generate-blob-sas.png
│   ├── 24-user-delegation-sas-settings.png
│   ├── 25-blob-sas-access-success.png
│   │
│   ├── 26-file-share-created.png
│   ├── 27-file-share-configuration.png
│   ├── 28-storage-browser-file-share.png
│   ├── 29-file-upload-success.png
│   ├── 30-vnet1-created.png
│   ├── 31-storage-service-endpoint.png
│   ├── 32-storage-vnet-access-restricted.png
│   ├── 33-storage-browser-unauthorized.png
│   └── 34-storage-networking-final-state.png
│
└── notes/
    ├── azure-storage-concepts.md
    └── blob-vs-file-storage.md
```

---

# 🚀 Portfolio Value

This lab demonstrates practical Azure Administrator skills beyond simply creating a storage account.

The implementation covers:

```text
Azure Storage
      |
      +── Availability
      |     └── GRS
      |
      +── Cost Optimization
      |     └── Lifecycle Management
      |
      +── Data Protection
      |     └── Immutable Storage
      |
      +── Identity & Authorization
      |     ├── RBAC
      |     └── SAS
      |
      +── Network Security
      |     ├── Selected Networks
      |     ├── VNet
      |     └── Service Endpoint
      |
      +── Storage Services
            ├── Blob Storage
            └── Azure Files
```

This provides hands-on evidence of Azure Storage administration, security, networking, data protection, and cost-optimization capabilities relevant to the **AZ-104 Azure Administrator** role.

---

## 📚 Key Takeaways

The main concepts from this lab are:

* An Azure Storage account provides the namespace for Azure Storage data services.
* Azure Storage supports multiple redundancy models such as LRS, ZRS, and GRS.
* Blob Storage is designed for large amounts of unstructured data.
* Azure Files provides shared file storage using file shares and directories.
* Lifecycle management can automatically transition Blob data between storage tiers.
* Immutable storage provides WORM-style protection.
* Private containers prevent anonymous Blob access.
* RBAC provides identity-based authorization.
* SAS provides controlled, time-limited access.
* Storage networking can restrict access to selected networks.
* Microsoft.Storage service endpoints can be used with virtual network-based storage restrictions.

These are the core storage administration concepts demonstrated by the lab.
