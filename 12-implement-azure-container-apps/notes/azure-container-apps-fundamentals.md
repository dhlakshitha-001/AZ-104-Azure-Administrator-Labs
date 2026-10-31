# Azure Container Apps Fundamentals

## Overview

Azure Container Apps (ACA) is a serverless platform for deploying and managing containerized applications.

It provides managed container hosting and orchestration capabilities without requiring administrators to directly manage the underlying Kubernetes cluster infrastructure.

This document summarizes the fundamentals of Azure Container Apps covered in the Implement Azure Container Apps lab.

## 1. What Is Azure Container Apps?

Azure Container Apps is a managed Azure service designed to run containerized applications.

It provides an application hosting platform built on Kubernetes technology.

Unlike directly managing an Azure Kubernetes Service cluster, Azure Container Apps abstracts much of the underlying infrastructure and cluster management.

### Main Characteristics

* Serverless container hosting.
* Managed application environment.
* Container image deployment.
* Application ingress.
* Application scaling capabilities.
* Support for microservices and containerized applications.
* Reduced infrastructure management.

## 2. Container Apps Environment

A Container Apps Environment provides the managed environment in which container apps run.

It provides a shared boundary for applications and their supporting infrastructure.

The lab creates the following environment:

| Setting          | Value            |
| ---------------- | ---------------- |
| Environment name | `my-environment` |
| Resource group   | `az104-rg9`      |
| Region           | Central US       |

### Important Points

* A container app is deployed into a Container Apps Environment.
* An environment provides shared infrastructure and configuration boundaries.
* Multiple container apps can be deployed into an environment.
* Environment configuration affects networking and other shared capabilities.
* Environment location should be selected according to application and deployment requirements.

## 3. Container App

A Container App is an Azure resource used to run a containerized application.

The lab creates:

`my-app`

### Lab Configuration

| Setting          | Value                        |
| ---------------- | ---------------------------- |
| Container App    | `my-app`                     |
| Environment      | `my-environment`             |
| Region           | Central US                   |
| Image            | Simple Hello World container |
| Application port | 80                           |

A container app is different from the environment in which it runs:

* Environment: Provides the shared managed hosting environment.
* Container App: Runs the application workload.

## 4. Serverless Containers

Serverless container hosting allows administrators to run containerized workloads without directly provisioning and managing the underlying server infrastructure.

### Benefits

* Reduced infrastructure administration.
* Simplified container deployment.
* Managed hosting environment.
* Support for application scaling.
* Faster application deployment.
* Useful for microservices and web applications.

Serverless does not mean that no infrastructure exists. It means that Azure manages the underlying infrastructure on the customer's behalf.

## 5. Azure Container Apps vs. Azure Container Instances

Both services provide managed container execution, but they address different requirements.

| Feature             | Azure Container Instances                 | Azure Container Apps                                         |
| ------------------- | ----------------------------------------- | ------------------------------------------------------------ |
| Main purpose        | Run individual container workloads        | Host application-oriented container workloads                |
| Hosting model       | Managed container instances               | Managed serverless application platform                      |
| Environment         | Container instance configuration          | Container Apps Environment                                   |
| Application ingress | Configured on the container instance      | Managed application ingress                                  |
| Orchestration       | No Kubernetes cluster management required | Managed Kubernetes-based platform                            |
| Typical workload    | Individual or straightforward containers  | Web applications, microservices, and longer-running services |

## 6. Azure Container Apps vs. Azure Kubernetes Service

| Feature                | Azure Container Apps                  | Azure Kubernetes Service                             |
| ---------------------- | ------------------------------------- | ---------------------------------------------------- |
| Platform               | Managed serverless container platform | Managed Kubernetes service                           |
| Kubernetes management  | Abstracted from the customer          | Kubernetes administration and configuration required |
| Infrastructure control | Higher abstraction                    | Greater control                                      |
| Deployment complexity  | Generally simpler                     | More advanced                                        |
| Typical use            | Application-focused container hosting | Complex container orchestration                      |

### When to Use Azure Container Apps

* Containerized web applications.
* Microservices.
* Event-driven workloads.
* Applications requiring managed scaling.
* Workloads where reducing infrastructure management is important.

### When to Use AKS

* Advanced Kubernetes requirements.
* Greater control over Kubernetes configuration.
* Complex orchestration requirements.
* Workloads requiring Kubernetes-specific capabilities.

## 7. Container Apps Architecture

The basic architecture used in the lab is:

```text
              Internet
                 |
                 |
          Application URL
                 |
                 |
        Application Ingress
                 |
                 |
          Azure Container App
                 |
                 |
       Simple Hello World Image
                 |
                 |
       Container Apps Environment
```

The Container Apps Environment provides the managed environment for the deployed application.

## 8. AZ-104 Revision Points

* Azure Container Apps is a serverless container platform.
* ACA uses Kubernetes technology behind the managed platform.
* The underlying Kubernetes environment is managed by Azure.
* A Container Apps Environment hosts container apps.
* A Container App runs the application workload.
* Multiple container apps can share an environment.
* ACA supports containerized web applications and microservices.
* ACA reduces the need to manage infrastructure directly.
* ACA and AKS provide different levels of infrastructure control.

## 9. Practical Review Checklist

* [ ] Explain Azure Container Apps.
* [ ] Explain the purpose of a Container Apps Environment.
* [ ] Identify the difference between an environment and a container app.
* [ ] Explain serverless container hosting.
* [ ] Compare Azure Container Apps with ACI.
* [ ] Compare Azure Container Apps with AKS.
* [ ] Identify common ACA workloads.
* [ ] Explain the basic ACA architecture.
