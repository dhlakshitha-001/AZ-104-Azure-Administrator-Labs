# Azure Blob Storage vs Azure Files

Azure Blob Storage and Azure Files are two Azure Storage services used for different storage scenarios.

This note compares the two services using the concepts demonstrated in the **Manage Azure Storage** lab.

---

# 1. Azure Blob Storage

Azure Blob Storage is designed for storing large amounts of **unstructured data**.

Examples include:

* Images
* Videos
* Documents
* Multimedia
* Backup files
* Other objects

The lab describes Blob Storage as a service for storing large amounts of unstructured data.

---

## 2. Blob Storage Structure

Blob Storage uses containers to organize blobs.

```text
Storage Account
      |
      v
Blob Container
      |
      +---- Blob
      |
      +---- Blob
      |
      +---- Blob
```

In this lab:

```text
Container:
data
```

A folder-like path was also used:

```text
securitytest
```

Conceptually:

```text
data
└── securitytest
    └── uploaded-file
```

---

# 3. Blob Access Model

The container created in the lab uses:

```text
Public Access:
Private
```

Therefore, anonymous public access is not permitted.

A direct request to the blob URL is expected to return an error such as:

```text
ResourceNotFound
```

or:

```text
PublicAccessNotPermitted
```

---

# 4. Blob Storage Access Tiers

The lab demonstrates the **Hot** access tier during blob upload.

It also configures lifecycle management to move blobs to the **Cool** tier when the configured condition is met.

```text
Hot
 |
 | Base blob not modified for > 30 days
 v
Cool
```

### Why Use Different Tiers?

Storage access patterns can change over time.

Frequently accessed data can remain in a more suitable access tier, while less frequently accessed data can be moved to another tier to help optimize storage costs.

---

# 5. Blob Lifecycle Management

The lab creates a lifecycle rule:

```text
Rule:
Movetocool
```

Condition:

```text
Base blobs last modified
more than 30 days ago
```

Action:

```text
Move to Cool storage
```

This demonstrates automated storage lifecycle management.

---

# 6. Blob Immutable Storage

The lab creates an immutable storage policy:

```text
Policy Type:
Time-based retention
```

Retention period:

```text
180 days
```

Immutable storage provides **Write Once, Read Many (WORM)** protection.

The purpose is to protect stored data from modification or deletion during the configured retention period.

---

# 7. Blob Access Through SAS

Although the container is private, the lab demonstrates controlled access using a **user delegation SAS**.

Configuration:

```text
Signing Method:
User delegation key

Permission:
Read
```

The SAS includes a start time and expiry time.

Conceptually:

```text
Private Blob
     |
     v
User Delegation SAS
     |
     +---- Read
     |
     +---- Start Time
     |
     +---- Expiry Time
     |
     v
Temporary Access
```

This demonstrates that a private blob can be accessed through a controlled authorization mechanism without making the container publicly accessible.

---

# 8. Azure Files

Azure Files provides shared file storage.

The lab creates:

```text
File Share:
share1
```

The default access tier selected in the lab is:

```text
Transaction optimized
```

Azure Files organizes data using files and directories.

---

# 9. Azure Files Structure

The basic structure can be represented as:

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

In the lab:

```text
share1
```

was created and managed through Azure Storage Browser.

---

# 10. Storage Browser

The lab uses Azure Storage Browser to manage the file share.

Storage Browser allows the administrator to:

* View the file share.
* Navigate directories.
* Create directories.
* Upload files.
* Manage storage content.

Conceptually:

```text
Azure Portal
      |
      v
Storage Browser
      |
      v
Classic File Shares
      |
      v
share1
      |
      +---- Directories
      +---- Files
```

---

# 11. Blob Storage vs Azure Files

| Feature              | Azure Blob Storage             | Azure Files                              |
| -------------------- | ------------------------------ | ---------------------------------------- |
| Primary Purpose      | Object storage                 | Shared file storage                      |
| Data Type            | Unstructured objects           | Files                                    |
| Organization         | Containers                     | File shares                              |
| Folder Structure     | Virtual directory-like paths   | Directories                              |
| Lab Resource         | `data`                         | `share1`                                 |
| Lab Tool             | Azure Portal / Storage Browser | Storage Browser                          |
| Example Data         | Images, videos, documents      | Shared files and folders                 |
| Lab Access Control   | Private container, RBAC, SAS   | Network restrictions and access controls |
| Lab Access Tier      | Hot / Cool demonstrated        | Transaction optimized                    |
| Lifecycle Management | Demonstrated                   | Not demonstrated in this lab             |
| Immutable Storage    | Demonstrated                   | Not demonstrated in this lab             |

---

# 12. Main Architectural Difference

The simplest way to understand the difference is:

```text
Azure Blob Storage
        |
        v
      Objects
        |
        v
    Containers
```

while:

```text
Azure Files
        |
        v
       Files
        |
        v
    File Shares
        |
        v
   Directories
```

---

# 13. Blob Storage Use Case

Blob Storage is suitable when the application needs to store large amounts of unstructured data.

For example:

```text
Application
     |
     +---- Images
     +---- Videos
     +---- Documents
     +---- Backups
             |
             v
       Blob Storage
```

The lab specifically identifies examples such as images and multimedia as Blob Storage data.

---

# 14. Azure Files Use Case

Azure Files is suitable when data needs to be organized as shared files and directories.

Conceptually:

```text
Users / Applications
        |
        v
    Azure Files
        |
        v
     File Share
        |
   +----+----+
   |         |
Folder A   Folder B
   |         |
 Files     Files
```

The lab specifically explores Azure Files as a possible solution for hosting on-premises file shares.

---

# 15. Network Security Comparison

The lab demonstrates network restrictions for the storage account.

### Blob Storage

The lab demonstrates:

```text
Private Container
       |
       v
No Anonymous Access
```

It also demonstrates SAS-based controlled access.

### Azure Files

The lab demonstrates:

```text
Virtual Network
      |
      v
Microsoft.Storage
Service Endpoint
      |
      v
Storage Account
      |
      v
Azure File Share
```

The storage account is eventually restricted so that allowed traffic must originate from the configured virtual network.

---

# 16. Access Control Comparison

Both storage services can be protected through Azure identity and network controls, but this lab demonstrates different access scenarios.

### Blob Storage

The lab uses:

* Private container
* Azure RBAC
* User delegation SAS
* Storage account network restrictions

### Azure Files

The lab uses:

* Storage Browser
* Azure Storage RBAC
* Storage account network restrictions
* Virtual network service endpoint

---

# 17. Security Architecture

The lab's storage security model can be summarized as:

```text
                     Azure Storage
                           |
             +-------------+-------------+
             |                           |
             v                           v
        Blob Storage                Azure Files
             |                           |
             v                           v
      Private Container              share1
             |                           |
       +-----+------+                    |
       |            |                    |
      RBAC         SAS                   |
       |            |                    |
       +-----+------+                    |
             |                           |
             +-------------+-------------+
                           |
                    Network Controls
                           |
                         vnet1
                           |
                    Default Subnet
                           |
                 Microsoft.Storage
                  Service Endpoint
```

---

# 18. When to Think "Blob"

Think about **Blob Storage** when the requirement involves:

* Unstructured data
* Images
* Videos
* Documents
* Multimedia
* Object storage
* Storage tiers
* Lifecycle management
* Immutable blob data
* Temporary controlled access through SAS

A useful memory phrase is:

> **Blob = Object Storage**

---

# 19. When to Think "Files"

Think about **Azure Files** when the requirement involves:

* Shared files
* Directories
* File shares
* Shared storage
* On-premises file-share scenarios

A useful memory phrase is:

> **Files = Shared File Storage**

---

# 20. Lab-Based Comparison

The lab provides a practical demonstration of both services.

### Blob Workflow

```text
Storage Account
      |
      v
Container: data
      |
      v
Private Access
      |
      v
Upload Blob
      |
      +---- Hot Tier
      |
      +---- Lifecycle → Cool
      |
      +---- Immutable Retention
      |
      +---- SAS Access
```

### Azure Files Workflow

```text
Storage Account
      |
      v
File Share: share1
      |
      v
Storage Browser
      |
      v
Upload File
      |
      v
vnet1
      |
      v
Microsoft.Storage Service Endpoint
      |
      v
Restricted Storage Access
```

---

# 21. Quick AZ-104 Exam Comparison

| Question                                    | Think About               |
| ------------------------------------------- | ------------------------- |
| Store images or videos                      | Blob Storage              |
| Store unstructured objects                  | Blob Storage              |
| Store data in containers                    | Blob Storage              |
| Move old blob data to Cool                  | Blob lifecycle management |
| Protect blobs from modification             | Immutable Blob Storage    |
| Temporary controlled blob access            | SAS                       |
| Shared folders and files                    | Azure Files               |
| Host an on-premises file-share scenario     | Azure Files               |
| Organize data into directories              | Azure Files               |
| Manage a file share through Storage Browser | Azure Files               |

---

# 22. Key Differences to Remember

```text
                    Azure Storage
                         |
              +----------+----------+
              |                     |
              v                     v
            Blob                  Files
              |                     |
          Objects               File Shares
              |                     |
        Containers             Directories
              |                     |
     Images / Videos          Shared Files
     Documents / Data
```

### Blob Storage

> **Object-based storage for unstructured data.**

### Azure Files

> **Managed shared file storage using files and directories.**

---

# 23. Final Takeaway

The most important distinction from this lab is:

```text
Blob Storage
    ↓
Unstructured object data
    ↓
Containers
    ↓
Images / Videos / Documents
```

versus:

```text
Azure Files
    ↓
Shared file storage
    ↓
File shares
    ↓
Directories / Files
```

For AZ-104, remember:

> **Blob = objects in containers**

> **Files = files in shared file shares**

Both services are part of Azure Storage and can be secured using appropriate network, identity, authorization, and data-protection mechanisms.
