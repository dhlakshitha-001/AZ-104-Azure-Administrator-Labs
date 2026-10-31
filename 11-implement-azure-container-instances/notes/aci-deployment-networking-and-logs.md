# Azure Container Instances – Deployment, Networking and Logs

## Overview

Deploying an Azure Container Instance involves selecting a container image, configuring the container environment, defining networking settings, and verifying the application after deployment.

This document summarizes the practical deployment, networking, and monitoring concepts covered in the lab.

## 1. Container Instance Deployment

The lab deploys a Linux container using the Azure Quickstart image.

### Lab Configuration

| Setting          | Value                                               |
| ---------------- | --------------------------------------------------- |
| Resource group   | `az104-rg9-lod66839366`                             |
| Container name   | `az104-c1`                                          |
| Region           | East US                                             |
| Image source     | Quickstart images                                   |
| Image            | `mcr.microsoft.com/azuredocs/aci-helloworld:latest` |
| Operating system | Linux                                               |

### Deployment Workflow

1. Open Azure Container Instances.
2. Select Create.
3. Configure the subscription and resource group.
4. Enter the container name.
5. Select the deployment region.
6. Select the container image.
7. Configure networking.
8. Review monitoring settings.
9. Review the deployment.
10. Create the container instance.
11. Verify the deployment.

## 2. Container Networking

ACI supports networking configurations that allow applications to communicate with other services and clients.

The lab configures public access to the sample web application.

### DNS Name Label

The DNS name label identifies the public hostname assigned to the container instance.

The label must be globally unique within the applicable Azure DNS namespace.

### FQDN

FQDN stands for Fully Qualified Domain Name.

It identifies the full domain name assigned to the container endpoint.

The general format used in the lab is:

```text
<dns-name-label>.<region>.azurecontainer.io
```

Example:

```text
aci-demo.eastus.azurecontainer.io
```

The actual hostname depends on the selected region and configured DNS name label.

### Public Access

The sample application is accessed using:

```text
http://<container-fqdn>
```

The application uses port 80 by default.

## 3. DNS Name Label Availability

Azure may return a DNS name label availability error if the selected label is already in use.

### Troubleshooting

1. Return to the Networking configuration.
2. Choose a different DNS name label.
3. Ensure the label follows the supported naming requirements.
4. Retry the configuration.
5. Verify the assigned FQDN after deployment.

## 4. Application Verification

After deployment, the container instance should be checked through the Azure portal.

### Verification Steps

1. Open the container instance.
2. Navigate to Overview.
3. Check the Status field.
4. Confirm that the container is Running.
5. Locate the FQDN.
6. Open the FQDN in a browser.
7. Verify that the sample application loads.

### Expected Application Message

**Welcome to Azure Container Instance**

The application should be accessible through the configured public endpoint.

## 5. Container Logs

Container logs help administrators inspect application output and activity.

The lab uses the Logs section under the container instance's Containers settings.

### Log Verification Workflow

1. Access the web application.
2. Refresh the browser page several times.
3. Return to the Azure portal.
4. Open the container instance.
5. Navigate to Containers.
6. Select the container.
7. Open Logs.
8. Review the available entries.

### Expected Log Activity

Accessing the sample application generates HTTP GET requests.

The log output can be used to verify that the application received requests.

### Important Distinction

Container application logs and diagnostic logging integrations are not necessarily the same thing.

The original lab asks to uncheck Enable container instance logs during creation and subsequently inspect the container Logs page.

If the Logs page does not display the expected entries, record the actual behavior and investigate the monitoring configuration rather than assuming that all logging is enabled.

## 6. HTTP GET Requests

HTTP GET is a request method used by clients to retrieve information from a web server.

When a browser opens the containerized application, it sends HTTP requests to the application endpoint.

Refreshing the page generates additional requests.

These requests may appear in the container's application logs.

## 7. Security Considerations

Public container endpoints should be configured carefully.

Important considerations:

* Expose only the required application ports.
* Avoid publishing administrative interfaces unnecessarily.
* Use HTTPS for production applications.
* Review access requirements.
* Use supported authentication mechanisms when applications contain sensitive information.
* Avoid placing secrets directly in container images.
* Review container image sources and update images regularly.
* Remove unused container instances.

The lab uses HTTP port 80 for a simple demonstration application. Production applications should use an appropriate secure transport configuration.

## 8. Troubleshooting

### Container Status Is Not Running

Check:

* Deployment status.
* Image availability.
* Container events.
* Container configuration.
* Application startup output.

### FQDN Does Not Resolve

Check:

* DNS name label.
* Selected region.
* FQDN copied from the actual resource.
* DNS propagation and availability.

### Web Application Is Not Accessible

Check:

* Container status.
* Public networking configuration.
* Correct FQDN.
* Application port.
* Application startup status.
* Whether the application is listening on the expected port.

### Logs Are Empty

Check:

* Correct container selection.
* Whether the application has generated requests.
* Container Logs page.
* Creation-time monitoring configuration.
* Application output and availability.

## 9. AZ-104 Revision Points

* ACI container instances can be deployed from supported container images.
* A container image is different from a running container.
* Public networking allows access through a configured endpoint.
* A DNS name label must be unique.
* FQDN identifies the full container hostname.
* The lab application uses HTTP port 80.
* Container status should be checked after deployment.
* Container logs can help identify application activity.
* HTTP GET requests can be generated by accessing the application.
* Public container endpoints should be secured appropriately.

## 10. Practical Review Checklist

* [ ] Create an Azure Container Instance.
* [ ] Select the Linux container image.
* [ ] Configure the DNS name label.
* [ ] Review the monitoring settings.
* [ ] Deploy the container.
* [ ] Verify Running status.
* [ ] Retrieve the FQDN.
* [ ] Access the application.
* [ ] Generate HTTP GET requests.
* [ ] Inspect container logs.
* [ ] Review public endpoint security.
* [ ] Delete unused resources after completing the lab.
