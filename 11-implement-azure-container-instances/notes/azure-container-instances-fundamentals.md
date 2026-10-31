# Azure Container Instances Fundamentals

## Overview

Azure Container Instances (ACI) is a Microsoft Azure service that enables administrators to run containers without provisioning or managing the underlying virtual machine infrastructure.

It provides a simple way to deploy containerized applications directly into Azure.

This document summarizes the container fundamentals and Azure Container Instances concepts covered in the Implement Azure Container Instances lab.

## 1. What Is a Container?

A container is an isolated environment used to package and run an application with its required dependencies.

Containers allow applications to run consistently across supported environments.

A container typically includes:

* Application code.
* Runtime dependencies.
* Required libraries.
* Configuration.
* Application execution environment.

Containers share the host operating system kernel rather than requiring a complete guest operating system for each container.

## 2. What Is Docker?

Docker is a platform used to package, distribute, and run applications in containers.

It provides tools for building and managing container images and running containers.

### Important Docker Concepts

| Concept            | Description                                           |
| ------------------ | ----------------------------------------------------- |
| Docker image       | Template containing application code and dependencies |
| Container          | Running instance of a container image                 |
| Dockerfile         | Instructions for building a container image           |
| Container registry | Repository used to store and distribute images        |
| Docker Engine      | Technology used to build and run containers           |

## 3. What Is Azure Container Instances?

Azure Container Instances is a managed Azure service for running containers.

Administrators can deploy containers without creating or managing virtual machines.

### Main Characteristics

* Managed container execution.
* No direct VM infrastructure management.
* Support for Linux containers.
* Support for Windows containers.
* Container image deployment.
* Public networking support.
* Container logs and status monitoring.

## 4. ACI vs. Virtual Machines

| Feature                   | Azure Virtual Machines            | Azure Container Instances                  |
| ------------------------- | --------------------------------- | ------------------------------------------ |
| Deployment unit           | Virtual machine                   | Container                                  |
| Infrastructure management | VM administration required        | Underlying infrastructure managed by Azure |
| Operating system          | Full guest OS                     | Container environment                      |
| Application packaging     | Installed or configured inside VM | Packaged into container image              |
| Startup                   | Generally slower                  | Generally faster                           |
| Common use                | Persistent server workloads       | Containerized application workloads        |

The appropriate service depends on application requirements, infrastructure control, networking, storage, and workload lifecycle.

## 5. ACI vs. Azure Kubernetes Service

Azure Container Instances and Azure Kubernetes Service serve different container hosting requirements.

| Feature                   | Azure Container Instances                         | Azure Kubernetes Service                                                       |
| ------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Main purpose              | Run containers directly                           | Orchestrate containerized applications                                         |
| Orchestration             | No Kubernetes cluster required                    | Kubernetes                                                                     |
| Infrastructure management | Managed container infrastructure                  | Managed Kubernetes control plane; node infrastructure depends on configuration |
| Complexity                | Relatively simple                                 | More advanced                                                                  |
| Typical use               | Individual or straightforward container workloads | Complex multi-container applications and orchestration                         |

ACI is useful when an organization wants to run containers without deploying a Kubernetes cluster.

## 6. Container Images

A container image is a packaged template used to create containers.

The lab uses the following image:

`mcr.microsoft.com/azuredocs/aci-helloworld:latest`

### Image Components

A container image may include:

* Base operating system image.
* Application files.
* Runtime dependencies.
* Application configuration.
* Startup instructions.

### Image Naming Structure

A container image reference commonly contains:

```text
registry/repository:tag
```

Example:

```text
mcr.microsoft.com/azuredocs/aci-helloworld:latest
```

| Component  | Value                      |
| ---------- | -------------------------- |
| Registry   | `mcr.microsoft.com`        |
| Repository | `azuredocs/aci-helloworld` |
| Tag        | `latest`                   |

The `latest` tag is a mutable tag and does not guarantee that the same image content will be retrieved every time.

For production deployments, consider using a specific version tag or image digest.

## 7. Container Instance Lifecycle

A container instance follows a lifecycle that includes deployment, execution, monitoring, and termination.

Typical stages include:

1. Select or provide a container image.
2. Configure the container instance.
3. Deploy the container.
4. Start the container.
5. Verify its running state.
6. Monitor application activity.
7. Stop or delete the container when no longer required.

## 8. Common Container Workloads

ACI can be used for:

* Simple web applications.
* Short-lived application workloads.
* Background processing.
* Automated tasks.
* Testing and development workloads.
* Containerized applications that do not require full orchestration.

## 9. AZ-104 Revision Points

* ACI is a managed Azure service for running containers.
* Docker images package applications and dependencies.
* A container is a running instance of an image.
* ACI does not require administrators to manage the underlying VM infrastructure.
* ACI supports Linux and Windows containers.
* Container images can be deployed from supported registries.
* Container instances can expose applications through public networking.
* Container lifecycle and logs are important administration concepts.

## 10. Practical Review Checklist

* [ ] Explain containerization.
* [ ] Explain Docker images and containers.
* [ ] Identify the components of a Docker image reference.
* [ ] Explain Azure Container Instances.
* [ ] Compare ACI with Azure Virtual Machines.
* [ ] Compare ACI with Azure Kubernetes Service.
* [ ] Identify common ACI workloads.
* [ ] Explain the basic container lifecycle.
