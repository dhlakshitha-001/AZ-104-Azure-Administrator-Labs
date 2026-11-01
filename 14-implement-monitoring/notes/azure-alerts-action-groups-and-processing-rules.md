# 🚨 Azure Alerts and Action Groups

## 1. Introduction

Azure Monitor alerting helps administrators detect important infrastructure events and respond to operational changes.

Alerts can monitor metrics, logs, and Activity Log events.

This lab focuses on Activity Log alerts, action groups, and alert processing rules.

---

## 2. Azure Monitor Alerts

Azure Monitor alerts notify administrators when configured conditions are met.

### Main Alert Types

| Alert Type          | Purpose                        | Example            |
| ------------------- | ------------------------------ | ------------------ |
| Metric Alerts       | Monitor numerical measurements | CPU utilization    |
| Log Search Alerts   | Evaluate log query results     | Application errors |
| Activity Log Alerts | Detect administrative events   | VM deletion        |

---

## 3. Activity Log Alerts

Activity Log alerts monitor supported events recorded in the Azure Activity Log.

They help administrators identify important resource operations and administrative changes.

### Lab Example

Operation:

```text
Microsoft.Compute/virtualMachines/delete
```

Alert name:

```text
VM was deleted
```

Description:

```text
A VM in the subscription was deleted
```

**Important:** VM deletion is an administrative Activity Log operation, not a VM performance metric.

---

## 4. Action Groups

Action groups define the actions and recipients associated with Azure Monitor alerts.

When an alert is triggered, its associated action group can execute the configured notification or automation actions.

### Supported Action Categories

Depending on service availability and configuration:

* Email
* SMS
* Push notifications
* Voice notifications
* Webhooks
* Azure Functions
* Logic Apps
* Automation-related actions

### Lab Configuration

| Setting           | Value                       |
| ----------------- | --------------------------- |
| Action Group      | `Alert the operations team` |
| Display Name      | `AlertOpsTeam`              |
| Region            | Global                      |
| Notification      | Email                       |
| Notification Name | `VM was deleted`            |

---

## 5. Alert Rule Configuration

An alert rule defines the conditions that trigger an alert.

### Main Configuration Areas

| Area      | Purpose                            |
| --------- | ---------------------------------- |
| Scope     | Defines monitored resources        |
| Condition | Defines the event or condition     |
| Actions   | Associates action groups           |
| Details   | Defines alert name and description |

### Lab Alert

```text
Alert Rule: VM was deleted
Resource Group: az104-rg11
Region: Global
Operation: Microsoft.Compute/virtualMachines/delete
```

---

## 6. Alert Processing Rules

Alert processing rules control how alerts are processed and when notifications are delivered.

They can suppress notifications during planned maintenance periods.

### Main Capabilities

* Suppress notifications.
* Configure schedules.
* Apply rules to selected scopes.
* Control notification delivery.

### Lab Configuration

| Setting        | Value                  |
| -------------- | ---------------------- |
| Rule Name      | `Planned Maintenance`  |
| Resource Group | `az104-rg11`           |
| Action         | Suppress notifications |
| Start          | 10:00 PM               |
| End            | 7:00 AM                |
| Time Zone      | Local time zone        |

---

## 7. Action Groups vs Alert Processing Rules

| Feature      | Action Groups                                    | Alert Processing Rules                    |
| ------------ | ------------------------------------------------ | ----------------------------------------- |
| Main purpose | Define notification recipients and actions       | Control alert processing                  |
| Function     | Send notifications or perform configured actions | Suppress or manage notifications          |
| Example      | Send email when a VM is deleted                  | Suppress notifications during maintenance |
| Relationship | Associated with alert rules                      | Applied to matching alerts                |

---

## 8. Alert Triggering Workflow

```text
Virtual Machine
      |
      v
VM Deletion Operation
      |
      v
Azure Activity Log
      |
      v
Activity Log Alert Rule
      |
      v
Associated Action Group
      |
      v
Email Notification
```

An applicable alert processing rule can control notification delivery according to its configured scope and schedule.

---

## 9. Azure CLI – Activity Log Alert

The lab provides a Cloud Shell fallback for situations where the portal cannot load the Delete Virtual Machine signal.

```bash
subscriptionId=$(az account show --query id --output tsv)

actionGroupId=$(az monitor action-group show \
  --resource-group az104-rg11 \
  --name "Alert the operations team" \
  --query id \
  --output tsv)

az monitor activity-log alert create \
  --name "VM was deleted" \
  --resource-group az104-rg11 \
  --scope "/subscriptions/$subscriptionId" \
  --condition "category=Administrative and operationName=Microsoft.Compute/virtualMachines/delete" \
  --action-group "$actionGroupId" \
  --description "A VM in the subscription was deleted"
```

---

## 10. Important AZ-104 Revision Points

* Activity Log alerts monitor supported administrative events.
* Metric alerts monitor numerical measurements.
* Action groups define notification recipients and actions.
* Alert rules define the conditions that trigger alerts.
* Alert processing rules control alert processing and notification delivery.
* Notification suppression can be configured for planned maintenance.
* Azure Activity Log records administrative operations.
* VM deletion can be monitored using its operation name.

---

## 11. Practical Checklist

* [ ] Create an action group.
* [ ] Configure email notification.
* [ ] Create an Activity Log alert.
* [ ] Select the Delete Virtual Machine signal.
* [ ] Associate the action group.
* [ ] Enable the alert rule.
* [ ] Create an alert processing rule.
* [ ] Configure notification suppression.
* [ ] Verify the triggered alert.
* [ ] Review the notification and alert history.

---

## 📚 References

* [Azure Monitor Alerts](https://learn.microsoft.com/azure/azure-monitor/alerts/alerts-overview)
* [Action Groups](https://learn.microsoft.com/azure/azure-monitor/alerts/action-groups)
* [Activity Log Alerts](https://learn.microsoft.com/azure/azure-monitor/alerts/alerts-create-activity-log-alert-rule)
* [Alert Processing Rules](https://learn.microsoft.com/azure/azure-monitor/alerts/alerts-processing-rules)
* [Azure Activity Log](https://learn.microsoft.com/azure/azure-monitor/platform/activity-log)
