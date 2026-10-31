# Azure App Service and Deployment Slots

## Overview

Azure App Service is a Platform as a Service (PaaS) offering that enables organizations to host web applications without managing the underlying infrastructure.

Azure Web Apps supports different programming languages and runtime environments, including PHP, Java, .NET, and Python.

This document summarizes the App Service and deployment slot concepts explored in the Implement Web Apps lab.

## 1. Azure App Service

Azure App Service provides a managed hosting platform for web applications.

### Main Features

* Managed application hosting.
* Support for multiple runtime environments.
* Integration with deployment tools.
* Scaling capabilities.
* Deployment slots.
* Monitoring and diagnostics.
* Integration with Azure services.

### PaaS Responsibility Model

With PaaS, Azure manages much of the underlying infrastructure, while the customer manages application code and application-level configuration.

| Responsibility                      | Customer                      | Azure                                     |
| ----------------------------------- | ----------------------------- | ----------------------------------------- |
| Application code                    | Yes                           | No                                        |
| Application configuration           | Yes                           | Platform support                          |
| Physical infrastructure             | No                            | Yes                                       |
| Host operating system maintenance   | Generally managed by platform | Yes                                       |
| Application deployment              | Yes                           | Platform provides deployment capabilities |
| Hosting infrastructure availability | Shared service responsibility | Yes                                       |

The exact division of responsibilities depends on the selected service and configuration.

## 2. App Service Plan

An App Service Plan defines the hosting resources used by an App Service application.

It determines:

* Compute resources.
* Pricing tier.
* Available features.
* Scaling capabilities.
* Deployment slot availability.

### Lab Configuration

| Setting          | Value      |
| ---------------- | ---------- |
| Operating system | Linux      |
| Runtime          | PHP 8.2    |
| Pricing tier     | Premium V3 |
| Plan size        | P1V3       |

### Important Points

* Web Apps are hosted within an App Service Plan.
* Applications in the same plan share the plan's allocated compute resources.
* Scaling an App Service Plan affects the hosting capacity available to its applications.
* Pricing tier determines the available App Service features.

## 3. Runtime Stack

A runtime stack provides the environment required to execute an application.

The lab uses:

| Setting           | Value   |
| ----------------- | ------- |
| Runtime           | PHP 8.2 |
| Operating system  | Linux   |
| Publishing method | Code    |

The selected runtime and operating system should match the application's requirements.

## 4. Deployment Slots

Deployment slots are separate application environments associated with an App Service application.

They allow developers and administrators to deploy and validate application updates before promoting them to production.

### Common Deployment Environments

| Slot        | Purpose                         |
| ----------- | ------------------------------- |
| Development | Initial application development |
| Testing     | Application testing             |
| QA          | Quality assurance               |
| Staging     | Pre-production validation       |
| Production  | Application serving end users   |

### Lab Configuration

The lab creates one staging slot:

`staging`

The production slot is the primary Web App environment.

### Benefits

* Pre-production testing.
* Reduced risk during application updates.
* Easier deployment promotion.
* Application version validation.
* Support for multiple deployment environments.

## 5. Deployment Slot URLs

Each deployment slot has its own default domain.

This allows administrators to access the staging and production applications separately.

The URLs should be recorded during the lab so that the correct application environment can be verified.

## 6. External Git Deployment

External Git deployment connects an App Service application to a repository hosted outside the App Service platform.

The lab uses the following repository:

`https://github.com/Azure-Samples/php-docs-hello-world`

### Lab Deployment Settings

| Setting           | Value                              |
| ----------------- | ---------------------------------- |
| Source            | External Git                       |
| Repository        | Azure-Samples/php-docs-hello-world |
| Branch            | master                             |
| Deployment target | Staging slot                       |

### Deployment Process

1. Open the staging slot.
2. Configure the required publishing credentials.
3. Open Deployment Center.
4. Select External Git.
5. Enter the repository URL.
6. Enter the branch name.
7. Save the deployment configuration.
8. Wait for deployment.
9. Open the staging default domain.
10. Verify the Hello World application.

## 7. SCM Basic Authentication

The original lab requires enabling SCM Basic Authentication for its deployment workflow.

SCM publishing credentials are associated with App Service deployment operations.

### Security Considerations

* Basic authentication is a security-sensitive authentication method.
* Avoid enabling it unnecessarily in production environments.
* Prefer supported modern authentication methods where possible.
* Restrict access to publishing credentials.
* Never commit deployment credentials to GitHub.
* Review whether basic authentication can be disabled after completing the lab.

## 8. Deployment Slot Swapping

A slot swap promotes application content between deployment slots.

In the lab, the staging application is swapped with production.

### Swap Process

1. Deploy the application to staging.
2. Verify the staging application.
3. Open Deployment slots.
4. Select Swap.
5. Review the source and target.
6. Start the swap.
7. Wait for completion.
8. Verify the production application.

### Benefits

* Application validation before production deployment.
* Reduced deployment risk.
* Faster application promotion.
* Support for controlled release workflows.

### Important Considerations

Some application settings are associated with individual slots, while others may move during a swap.

Administrators should understand which settings are slot-specific and verify application behavior after the swap.

## 9. Deployment Troubleshooting

### Deployment Failure

Check:

* Repository URL.
* Branch name.
* Publishing credentials.
* Deployment Center status.
* Runtime compatibility.
* Application deployment logs.

### Staging Application Not Updated

Check:

* Whether deployment has finished.
* Whether the correct branch is configured.
* Whether the staging slot URL is correct.
* Whether the browser needs refreshing.

### Slot Swap Failure

Check:

* Source and target slot.
* Application configuration.
* Deployment status.
* Slot-specific settings.
* Azure Activity Log.

## 10. AZ-104 Revision Points

* Azure App Service is a PaaS service.
* App Service Plans define hosting resources and features.
* Runtime stacks determine the application execution environment.
* Deployment slots provide separate application environments.
* Each deployment slot has its own URL.
* External Git can be used as an application deployment source.
* Staging supports pre-production validation.
* Slot swapping promotes application content between environments.
* Application settings may have slot-specific behavior.
* Publishing credentials must be protected.

## 11. Practical Review Checklist

* [ ] Explain Azure App Service.
* [ ] Identify the purpose of an App Service Plan.
* [ ] Identify the runtime stack and operating system.
* [ ] Explain the purpose of deployment slots.
* [ ] Create a staging slot.
* [ ] Identify the staging and production URLs.
* [ ] Configure an external Git repository.
* [ ] Verify application deployment.
* [ ] Perform a slot swap.
* [ ] Verify the production application.
* [ ] Review publishing credential security.
