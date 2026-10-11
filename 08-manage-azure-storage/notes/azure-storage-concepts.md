# Azure Storage Concepts

This note summarizes the main Azure Storage concepts demonstrated in the **Manage Azure Storage** lab.

---

## 1. Azure Storage Account

An Azure Storage account provides a unique namespace for Azure Storage data.

A single storage account can contain different Azure Storage services:

```text
Storage Account
│
├── Blob Containers
│
├── File Shares
│
├── Queues
│
└── Tables
```

The storage account provides access to storage data through Azure-supported network protocols and services.

---

## 2. Storage Account Performance

Azure Storage accounts provide different performance options.

The lab uses:

```text
Performance:
Standard
```

### Standard

Standard performance is suitable for most general-purpose applications.

### Premium

Premium performance is intended for enterprise or high-performance applications.

> **Lab Note:** The lab specifically recommends Standard performance for most applications and highlights Premium for high-performance scenarios.

---

## 3. Storage Redundancy

Azure Storage provides redundancy options to protect data from hardware, zone, or regional failures.

The lab configures:

```text
Redundancy:
Geo-redundant storage (GRS)
```

The lab also enables:

```text
Read access to data
in the secondary region
```

### Main Redundancy Models

The lab's key takeaways identify:

* **LRS** – Locally Redundant Storage
* **ZRS** – Zone-Redundant Storage
* **GRS** – Geo-Redundant Storage

Conceptually:

```text
                    Azure Storage
                         |
              +----------+----------+
              |          |          |
              v          v          v
             LRS        ZRS        GRS
              |          |          |
            Local       Zone      Secondary
          redundancy  redundancy    region
```

### GRS

Geo-redundant storage provides replication to a secondary region.

This provides additional protection if the primary region becomes unavailable.

---

## 4. Public Network Access

Storage accounts can be configured to control network access.

In the lab, public network access is initially disabled.

```text
Public Network Access:
Disabled
```

The lab later changes the configuration to:

```text
Public Network Access:
Enabled

Access:
Selected Networks
```

The client's IPv4 address is initially added as an allowed source.

Later, the client IP is removed and access is restricted to a virtual network.

---

## 5. Selected Network Access

Instead of allowing access from all public networks, a storage account can be configured to allow traffic only from selected sources.

The lab demonstrates two stages:

```text
Stage 1
Storage Account
      |
      +---- Client IPv4 Address
```

Then:

```text
Stage 2
Storage Account
      |
      +---- vnet1
              |
              +---- Default Subnet
```

This demonstrates how storage network access can be progressively restricted.

---

## 6. Data Protection

The lab reviews the **Data protection** settings of the storage account.

The lab notes that:

```text
Blob soft delete retention:
7 days
```

is the default retention value shown during the exercise.

The lab also highlights blob versioning as another available data-protection capability.

---

## 7. Storage Encryption

The lab reviews the **Encryption** configuration of the storage account.

Azure Storage provides encryption-related configuration options to protect stored data.

The lab accepts the default encryption settings.

---

# 8. Azure Blob Storage

Azure Blob Storage is designed for storing large amounts of unstructured data.

Examples of blob data include:

* Images
* Videos
* Documents
* Backups
* Other files and objects

The term **Blob** stands for **Binary Large Object**.

---

## 9. Blob Containers

A blob container is a directory-like structure used to organize blobs.

The lab creates:

```text
Container:
data
```

The container uses:

```text
Public Access:
Private
```

This means anonymous public access is not permitted.

Conceptually:

```text
Storage Account
      |
      v
    data
      |
      +---- file1
      +---- file2
      +---- securitytest/
                |
                +---- uploaded-file
```

---

## 10. Private Blob Access

The lab verifies that directly accessing the uploaded blob URL from an InPrivate browser does not provide public access.

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

---

## 11. Blob Access Tiers

Azure Blob Storage supports different access tiers.

The lab specifically demonstrates the:

```text
Hot
```

tier during blob upload and explores the available tier choices.

The lab also demonstrates lifecycle management that moves older blobs to:

```text
Cool
```

### Conceptual Flow

```text
Frequently accessed
        |
        v
       Hot
        |
        | 30+ days without modification
        v
      Cool
```

Using lifecycle management can help reduce storage costs for data that is accessed less frequently.

---

# 12. Lifecycle Management

Lifecycle management allows storage administrators to define rules for automatically changing blob storage behavior based on conditions.

The lab creates:

```text
Rule:
Movetocool
```

Condition:

```text
If base blobs were last modified
more than 30 days ago
```

Action:

```text
Move to Cool storage
```

Conceptually:

```text
Blob
 |
 | Last modified > 30 days
 v
Cool Storage
```

Lifecycle management is useful when data access patterns change over time.

---

# 13. Immutable Blob Storage

The lab configures immutable blob storage using a:

```text
Time-based retention policy
```

Retention period:

```text
180 days
```

Immutable storage provides a **Write Once, Read Many (WORM)** capability.

The objective is to protect stored data from modification or deletion during the configured retention period.

---

## 14. Time-Based Retention

The lab uses:

```text
Policy Type:
Time-based retention

Retention:
180 days
```

Conceptually:

```text
Blob
 |
 | Immutable protection
 |
 +----------------------+
 |                      |
 v                      v
Read                  Protected
                         |
                         |
                  180-day period
```

This is useful when stored information must remain protected for a defined period.

---

# 15. Azure RBAC for Storage

The lab demonstrates assigning Azure roles to the user account.

The following roles are used:

```text
Storage Blob Data Contributor
```

and:

```text
Storage File Data Privileged Contributor
```

Azure RBAC allows permissions to be assigned to users based on their required access.

---

## 16. Storage Blob Data Contributor

The **Storage Blob Data Contributor** role is used in the lab to provide the required permissions for working with Blob Storage data.

The role is assigned through:

```text
Access Control (IAM)
        |
        v
Add Role Assignment
        |
        v
Storage Blob Data Contributor
```

---

# 17. User Delegation SAS

The lab demonstrates generating a SAS for the uploaded blob.

The selected signing method is:

```text
User delegation key
```

The configured permission is:

```text
Read
```

The SAS has a defined start and expiry time.

Conceptually:

```text
Private Blob
     |
     v
User Delegation SAS
     |
     +---- Read permission
     |
     +---- Start time
     |
     +---- Expiry time
     |
     v
Temporary controlled access
```

This allows controlled access without making the entire container publicly accessible.

---

# 18. Azure Files

Azure Files provides shared file storage.

Unlike Blob Storage, which stores objects inside containers, Azure Files provides file shares with directories and files.

The lab creates:

```text
File Share:
share1
```

The selected access tier is:

```text
Transaction optimized
```

---

# 19. Storage Browser

Azure Storage Browser is a portal-based tool used in the lab to manage storage resources.

The lab uses Storage Browser to:

* View the file share.
* Navigate the `share1` directory.
* Create directories.
* Upload files.
* Review storage content.

Conceptually:

```text
Azure Portal
     |
     v
Storage Browser
     |
     +---- Blob containers
     |
     +---- File shares
```

---

# 20. Virtual Network Service Endpoint

The lab creates a virtual network:

```text
vnet1
```

A Microsoft.Storage service endpoint is then configured on the default subnet.

```text
vnet1
 |
 +---- Default Subnet
          |
          +---- Microsoft.Storage
                    |
                    v
              Azure Storage
```

The service endpoint allows the storage account to recognize traffic from the selected virtual network subnet.

---

# 21. Restrict Storage to a Virtual Network

After configuring the service endpoint, the storage account networking configuration is changed.

The lab:

1. Adds `vnet1`.
2. Selects the `default` subnet.
3. Removes the client's public IPv4 address.
4. Saves the configuration.

The resulting access model is:

```text
Allowed
   |
   v
vnet1
 |
 +---- default subnet
          |
          v
     Azure Storage
```

External access is no longer allowed according to the configured network restriction.

---

# 22. Unauthorized Access Testing

After the network restriction is applied, Storage Browser is refreshed.

The expected result is:

```text
Not authorized to perform this operation
```

This occurs because the client is not connecting from the configured virtual network.

The lab also notes that the change may take a few minutes to take effect.

---

# 23. Storage Security Layers

The lab demonstrates multiple security mechanisms.

```text
                 Azure Storage Security
                         |
       +-----------------+------------------+
       |                 |                  |
       v                 v                  v
   Network             Identity          Data
   Security            Security         Protection
       |                 |                  |
       |                 |                  |
       v                 v                  v
Selected Networks       RBAC          Immutable Storage
VNet Restrictions       SAS           Soft Delete
Service Endpoint
```

These controls address different aspects of storage security.

---

# 24. Network Access vs Authorization

It is important to distinguish between **network access** and **authorization**.

### Network Access

Determines whether traffic can reach the storage account.

Example:

```text
Is this client/network allowed?
```

The lab demonstrates:

* Public network access
* Selected networks
* Client IP filtering
* Virtual network restrictions
* Microsoft.Storage service endpoint

### Authorization

Determines what an authenticated identity can do.

The lab demonstrates:

* Azure RBAC
* Storage Blob Data Contributor
* Storage File Data Privileged Contributor
* User delegation SAS

Conceptually:

```text
Client
  |
  v
Network Access Check
  |
  +---- Denied ---> Stop
  |
  v
Authentication / Authorization
  |
  +---- Denied ---> Stop
  |
  v
Storage Resource
```

---

# 25. Storage Data Protection

The lab demonstrates several data protection mechanisms:

| Mechanism            | Purpose                                                   |
| -------------------- | --------------------------------------------------------- |
| Soft Delete          | Helps protect deleted data                                |
| Blob Versioning      | Provides versions of blob data                            |
| Immutable Storage    | Protects data from modification/deletion during retention |
| Lifecycle Management | Automates storage-tier transitions                        |
| GRS                  | Provides geo-redundant replication                        |

---

# 26. Important AZ-104 Takeaways

### Storage Account

> A storage account provides the namespace and configuration boundary for Azure Storage services.

### Blob Storage

> Blob Storage is designed for large amounts of unstructured data.

### Azure Files

> Azure Files provides shared file storage organized using files and directories.

### GRS

> Geo-redundant storage provides replication to a secondary region.

### Lifecycle Management

> Lifecycle rules can automatically move blobs to another storage tier based on conditions.

### Immutable Storage

> Immutable storage provides WORM-style protection using retention policies.

### RBAC

> Azure RBAC controls what an identity can do with Azure resources and data.

### SAS

> A SAS provides controlled, time-limited access to storage resources.

### Service Endpoint

> A Microsoft.Storage service endpoint can be used to restrict storage access to a virtual network subnet.

---

# 27. Lab Security Flow

The complete security approach demonstrated in this lab can be represented as:

```text
                     Client
                       |
                       v
              Network Restrictions
                       |
             +---------+---------+
             |                   |
          Allowed              Denied
             |                   |
             v                   X
       Authentication
             |
             v
       Azure RBAC / SAS
             |
             v
        Storage Resource
             |
      +------+------+
      |             |
    Blob          Files
```

---

# 28. Final Summary

The **Manage Azure Storage** lab demonstrates how Azure Storage can be configured for availability, cost optimization, secure access, and data protection.

The main concepts covered are:

* Storage accounts
* Blob Storage
* Azure Files
* Storage redundancy
* Geo-redundant storage
* Storage access tiers
* Lifecycle management
* Immutable storage
* RBAC
* User delegation SAS
* Storage Browser
* Virtual network restrictions
* Microsoft.Storage service endpoints

The most important AZ-104 concept is that Azure Storage security is not based on a single control. Network restrictions, identity-based authorization, temporary access mechanisms, and data-protection features can work together to provide a more controlled storage environment.
