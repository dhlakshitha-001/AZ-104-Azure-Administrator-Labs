# Web App Scaling and Load Testing

## Overview

Azure App Service supports scaling to accommodate changes in application demand.

Scaling helps maintain application availability and performance when workload requirements change.

Azure Load Testing can simulate application traffic and provide metrics that help evaluate application performance.

## 1. App Service Scaling

Scaling changes the capacity available to an application.

There are two common scaling approaches:

### Scale Up

Scale up changes the App Service Plan pricing tier or instance size to provide additional resources.

This is vertical scaling.

### Scale Out

Scale out increases the number of instances available to host an application.

This is horizontal scaling.

### Comparison

| Feature        | Scale Up                                 | Scale Out                       |
| -------------- | ---------------------------------------- | ------------------------------- |
| Scaling method | Vertical                                 | Horizontal                      |
| Main change    | Hosting resources or tier                | Instance count                  |
| Purpose        | Increase individual instance capacity    | Increase total hosting capacity |
| Example        | Change to a larger App Service Plan tier | Add additional instances        |

## 2. Automatic Scaling

Automatic scaling adjusts application hosting capacity according to configured scaling behavior and workload demand.

The lab uses the Automatic scaling option.

### Lab Configuration

| Setting           | Value     |
| ----------------- | --------- |
| Scaling method    | Automatic |
| Maximum burst     | 2         |
| Minimum instances | 1         |

### Maximum Burst

Maximum burst defines the configured upper burst capacity for the automatic scaling configuration.

### Minimum Instances

Minimum instances define the configured minimum number of instances.

These settings help control the capacity available to the application.

> The exact scaling behavior and available configuration options depend on the App Service Plan tier and the selected scaling mode.

## 3. Automatic vs. Rules-Based Scaling

The original lab presents Automatic and Rules Based as scaling options.

### Automatic Scaling

The platform manages scaling according to the supported automatic scaling behavior and configuration.

### Rules-Based Scaling

Rules-based scaling uses configured conditions and metrics to determine when the application hosting capacity should change.

Depending on the supported configuration, metrics can include CPU utilization, memory, or bandwidth.

## 4. Staging Slot Elastic Instance Configuration

The lab includes an Azure CLI command to set the staging slot minimum elastic instance count.

```bash
az webapp update \
  --resource-group az104-rg9-lod66837092 \
  --name WebApp66837092 \
  --slot staging \
  --minimum-elastic-instance-count 1
```

### Command Explanation

| Parameter                          | Description                                  |
| ---------------------------------- | -------------------------------------------- |
| `az webapp update`                 | Updates an Azure Web App                     |
| `--resource-group`                 | Specifies the resource group                 |
| `--name`                           | Specifies the Web App name                   |
| `--slot`                           | Identifies the deployment slot               |
| `--minimum-elastic-instance-count` | Specifies the minimum elastic instance count |

The command should be executed using Azure Cloud Shell or an appropriately configured Azure CLI environment.

## 5. Azure Load Testing

Azure Load Testing is a service for simulating application traffic and evaluating application performance.

It can help identify performance issues and understand application behavior under load.

### Lab Workflow

1. Open the production Web App.
2. Navigate to Diagnose and solve problems.
3. Select Load Test your App.
4. Create an Azure Load Testing resource.
5. Configure an HTTP request.
6. Enter the production Web App URL.
7. Create the test.
8. Run the test.
9. Review the live metrics.
10. Stop the test.

## 6. HTTP Request Configuration

The lab configures an HTTP request using the production Web App default domain.

The URL must begin with:

`https://`

The production URL is used to direct simulated requests to the deployed application.

The request should be checked before creating the test.

## 7. Important Load Testing Metrics

### Virtual Users

Virtual users represent simulated users participating in the load test.

Increasing virtual users can increase the traffic generated against an application.

### Response Time

Response time measures how long the application takes to respond to requests.

Higher response times can indicate increased workload or application performance problems.

### Requests Per Second

Requests per second (RPS) measures the number of requests processed per second.

It provides an indication of application throughput.

### Metric Summary

| Metric        | Meaning                           |
| ------------- | --------------------------------- |
| Virtual users | Simulated concurrent users        |
| Response time | Time taken to respond to requests |
| Requests/sec  | Request throughput per second     |

## 8. Understanding Load Test Results

Load testing metrics help administrators evaluate application performance under simulated demand.

For example:

* Increasing virtual users can increase application workload.
* Response time can increase when the application becomes overloaded.
* Requests per second indicates the amount of traffic being processed.
* Scaling behavior should be reviewed alongside the application performance metrics.

A load test does not automatically prove that autoscaling is working correctly. To verify autoscaling, also inspect the App Service Plan scaling configuration and relevant instance metrics.

## 9. Scaling and Cost Considerations

Scaling can improve application capacity but may increase Azure costs.

Important considerations include:

* App Service Plan pricing tier.
* Number of hosting instances.
* Minimum instance configuration.
* Maximum burst capacity.
* Application workload.
* Load testing resource usage.
* Test duration.

Load testing should be performed within the intended subscription and budget limits.

## 10. Troubleshooting

### Scaling Settings Cannot Be Selected

Possible causes:

* The selected App Service Plan tier does not support the required feature.
* The wrong deployment slot is selected.
* The available scaling mode differs from the lab instructions.
* Subscription or regional restrictions apply.

### Staging Slot Command Fails

Check:

* Azure CLI is available.
* You are signed in to the correct subscription.
* The resource group exists.
* The Web App name is correct.
* The staging slot exists.
* The command parameters are supported by the selected environment.

### Load Test Creation Fails

Check:

* Azure Load Testing resource deployment.
* Subscription permissions.
* Resource naming requirements.
* Regional availability.
* Resource provider registration if requested by Azure.

### Load Test Does Not Reach the Application

Check:

* The production URL.
* HTTPS formatting.
* Application availability.
* HTTP request configuration.
* Test execution status.

### Performance Metrics Are Unexpected

Check:

* Number of virtual users.
* Response time.
* Requests per second.
* Application availability.
* Scaling configuration.
* Instance metrics and resource utilization.

## 11. AZ-104 Revision Points

* Scale up increases individual hosting capacity.
* Scale out increases the number of instances.
* Automatic scaling adjusts capacity according to supported automatic scaling behavior.
* Rules-based scaling uses configured conditions and metrics.
* Minimum instances establish a minimum capacity.
* Maximum burst controls the configured upper burst capacity.
* Deployment slots can have elastic instance settings.
* Azure Load Testing simulates application traffic.
* Virtual users represent simulated users.
* Response time measures application responsiveness.
* Requests per second measures throughput.
* Load testing metrics should be reviewed together with scaling and application metrics.

## 12. Practical Review Checklist

* [ ] Explain scale up and scale out.
* [ ] Identify the automatic scaling option.
* [ ] Configure maximum burst.
* [ ] Configure minimum instances.
* [ ] Understand the staging slot elastic instance setting.
* [ ] Execute the Azure CLI update command.
* [ ] Create an Azure Load Testing resource.
* [ ] Configure an HTTP request.
* [ ] Run a load test.
* [ ] Review virtual users.
* [ ] Review response time.
* [ ] Review requests per second.
* [ ] Verify scaling behavior independently from load test metrics.
* [ ] Stop the test and clean up unnecessary resources.
