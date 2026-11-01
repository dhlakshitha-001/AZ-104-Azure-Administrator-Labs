# 📊 Azure Monitor and Log Analytics

## 1. Introduction

Azure Monitor is Microsoft's centralized monitoring platform for collecting, analyzing, and responding to monitoring data from Azure resources and applications.

It provides visibility into infrastructure performance, application health, and operational activities.

### Core Capabilities

* Metrics monitoring
* Log collection and analysis
* Alerting
* Data collection
* Performance investigation
* Monitoring visualization

---

## 2. Azure Monitor Architecture

The monitoring workflow demonstrated in this lab:

```text
Azure Virtual Machine
        |
        v
Azure Monitor Agent
        |
        v
Data Collection Rule
        |
        v
Log Analytics Workspace
        |
        v
Azure Monitor Logs
        |
        v
KQL Queries and Analysis
```

---

## 3. Log Analytics Workspace

A Log Analytics workspace provides a centralized environment for storing and analyzing log data.

### Main Capabilities

* Centralized log storage.
* Query-based investigation.
* Monitoring analysis.
* Performance troubleshooting.
* Integration with Azure Monitor.

### Lab Configuration

| Setting        | Value           |
| -------------- | --------------- |
| Resource Group | `az104-rg11`    |
| Workspace      | `az104-law11-*` |
| Region         | East US         |

---

## 4. Azure Monitor Agent (AMA)

Azure Monitor Agent collects monitoring data from supported machines and sends it to configured destinations.

### Key Responsibilities

* Collect guest monitoring data.
* Process data collection configuration.
* Send data to configured destinations.
* Support centralized monitoring.

### Lab Verification

Extension:

```text
AzureMonitorWindowsAgent
```

Expected provisioning status:

```text
Provisioning succeeded
```

---

## 5. Data Collection Rules (DCR)

A Data Collection Rule defines what monitoring data is collected and where it is sent.

### Main Components

| Component    | Description                           |
| ------------ | ------------------------------------- |
| Data Sources | Defines the data to collect           |
| Destinations | Defines where data is sent            |
| Data Flows   | Connects sources and destinations     |
| Associations | Connects rules to monitored resources |

### Lab Configuration

```text
Data Collection Rule: az104-dcr11
Virtual Machine: az104-vm0
Workspace: az104-law11-*
```

A valid DCR association is required for the configured data collection workflow.

---

## 6. Kusto Query Language (KQL)

KQL is used to query and analyze data stored in Azure Monitor Logs.

### Important Operators

| Operator    | Purpose                      |
| ----------- | ---------------------------- |
| `where`     | Filters records              |
| `project`   | Selects columns              |
| `summarize` | Aggregates records           |
| `count()`   | Counts records               |
| `avg()`     | Calculates average           |
| `max()`     | Returns maximum value        |
| `bin()`     | Groups values into intervals |
| `render`    | Visualizes query results     |

### Example – Heartbeat Query

```kusto
Heartbeat
| where TimeGenerated > ago(30m)
| where Computer =~ "az104-vm0"
| summarize HeartbeatCount = count(),
            LastHeartbeat = max(TimeGenerated)
            by Computer, Category
```

**Purpose:** Verify that the monitored VM is sending heartbeat records.

### Example – Performance Query

```kusto
InsightsMetrics
| where TimeGenerated > ago(30m)
| where Computer =~ "az104-vm0"
| where Name == "UtilizationPercentage"
| summarize AverageUtilization = avg(Val)
    by bin(TimeGenerated, 5m), Computer
| render timechart
```

**Purpose:** Analyze VM utilization records and visualize the results.

---

## 7. Metrics vs Logs vs Activity Logs

| Feature      | Metrics                | Logs             | Activity Logs             |
| ------------ | ---------------------- | ---------------- | ------------------------- |
| Data         | Numerical measurements | Detailed records | Administrative events     |
| Main purpose | Performance monitoring | Investigation    | Track resource operations |
| Example      | CPU utilization        | Heartbeat        | VM deletion               |

---

## 8. Important AZ-104 Revision Points

* Azure Monitor is the central monitoring platform.
* Log Analytics workspaces store and analyze log data.
* Azure Monitor Agent collects supported monitoring data.
* Data Collection Rules define what data is collected and its destination.
* Monitored resources need appropriate DCR associations.
* KQL is used to query monitoring data.
* Heartbeat records help verify monitoring communication.
* Monitoring data should be verified before deleting the monitored resource.

---

## 9. Practical Checklist

* [ ] Deploy a monitoring-enabled VM.
* [ ] Verify Azure Monitor Agent.
* [ ] Verify DCR association.
* [ ] Open the Log Analytics workspace.
* [ ] Run the Heartbeat query.
* [ ] Run the performance query.
* [ ] Verify query results.

---

## 📚 References

* [Azure Monitor Documentation](https://learn.microsoft.com/azure/azure-monitor/)
* [Azure Monitor Agent](https://learn.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview)
* [Data Collection Rules](https://learn.microsoft.com/azure/azure-monitor/data-collection/data-collection-rule-overview)
* [Log Analytics Workspace](https://learn.microsoft.com/azure/azure-monitor/logs/log-analytics-workspace-overview)
* [Kusto Query Language](https://learn.microsoft.com/kusto/query/)
