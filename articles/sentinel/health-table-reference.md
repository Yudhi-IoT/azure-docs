---
title: SentinelHealth tables reference 
description: Learn about the fields in the SentinelHealth tables, used for health monitoring and analysis.
author: limwainstein
ms.author: lwainstein
ms.topic: reference
ms.date: 11/08/2022
ms.service: microsoft-sentinel
---

# SentinelHealth tables reference 

This article describes the fields in the *SentinelHealth* table used for monitoring the health of Microsoft Sentinel resources. With the Microsoft Sentinel [health monitoring feature](health-audit.md), you can keep tabs on the proper functioning of your SIEM and get information on any health drifts in your environment. 

Learn how to query and use the health table for deeper monitoring and visibility of actions in your environment:
- For [data connectors](monitor-data-connector-health.md)
- For [automation rules and playbooks](monitor-automation-health.md)

> [!IMPORTANT]
>
> The *SentinelHealth* data table is currently in **PREVIEW**. See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
>

Microsoft Sentinel's health monitoring feature covers different kinds of resources, such as [data connectors](monitor-data-connector-health.md) and [automation rules](monitor-automation-health.md). Many of the data fields in the following tables apply across resource types, but some have specific applications for each type. The descriptions below will indicate one way or the other.

## SentinelHealth table columns schema

The following table describes the columns and data generated in the SentinelHealth data table:

| ColumnName               | ColumnType     | Description                                                   |
| ------------------------ | -------------- | ------------------------------------------------------------- |
| **TenantId**             | String         | The tenant ID for your Microsoft Sentinel workspace.          |
| **TimeGenerated**        | Datetime       | The time at which the health event occurred.                  |
| <a name="operationname_health"></a>**OperationName** | String      | The health operation. Possible values depend on the resource type.<br>See [Operation names for different resource types](#operation-names-for-different-resource-types) for details. |
| <a name="sentinelresourceid_health"></a>**SentinelResourceId**     | String         | The unique identifier of the resource on which the health event occurred, and its associated Microsoft Sentinel workspace.  |
| **SentinelResourceName** | String         | The resource name.                                            |
| <a name="status_health"></a>**Status**    | String         | Indicates the overall result of the operation. Possible values depend on the operation name.<br>See [Operation names for different resource types](#operation-names-for-different-resource-types) for details.                  |
| **Description**          | String         | Describes the operation, including extended data as needed. For failures, this can include details of the failure reason. |
| **Reason**               | Enum           | Shows a basic reason or error code for the failure of the resource. Possible values depend on the resource type. More detailed reasons can be found in the **Description** field.     |
| **WorkspaceId**          | String         | The workspace GUID on which the health issue occurred. The full Azure Resource Identifier is available in the [SentinelResourceID](#sentinelresourceid_health) column. |
| **SentinelResourceType** | String         | The Microsoft Sentinel resource type being monitored.<br>Possible values: `Data connector`, `Automation rule`, `Playbook` |
| **SentinelResourceKind** | String         | A resource classification within the resource type.<br>- For data connectors, this is the type of connected data source. |
| **RecordId**             | String         | A unique identifier for the record that can be shared with the support team for better correlation as needed.  |
| **ExtendedProperties**   | Dynamic (json) | A JSON bag that varies by the [OperationName](#operationname_health) value and the [Status](#status_health) of the event.<br>See [Extended properties](#extended-properties) for details.                                                      |
| **Type**                 | String         | `SentinelHealth`                                              |

## Operation names for different resource types

| Resource types       | Operation names | Statuses |
| -------------------- | --------------- | -------- |
| **[Data collectors](monitor-data-connector-health.md)**  | Data fetch status change<br><br>__________________<br>Data fetch failure summary | Success<br>Failure<br>_____________<br>Informational |
| **[Automation rules](monitor-automation-health.md)** | Automation rule run | Success<br>Partial success<br>Failure |
| **[Playbooks](monitor-automation-health.md)**        | Playbook was triggered | Success<br>Failure |


## Extended properties

### Data connectors

For `Data fetch status change` events with a success indicator, the bag contains a ‘DestinationTable’ property to indicate where data from this resource is expected to land. For failures, the contents vary depending on the failure type. 

### Automation rules

| ColumnName               | ColumnType     | Description                                                   |
| ------------------------ | -------------- | ------------------------------------------------------------- |
| **ActionsTriggeredSuccessfully** | Integer | Number of actions the automation rule successfully triggered. |
| **IncidentName**         | String         | The resource ID of the Microsoft Sentinel incident on which the rule was triggered.  |
| **IncidentNumber**       | String         | The sequential number of the Microsoft Sentinel incident as shown in the portal. |
| **TotalActions**         | Integer        | Number of actions configured in this automation rule.         |
| **TriggeredOn**          | String         | `Alert` or `Incident`. The object on which the rule was triggered. |
| **TriggeredPlaybooks**  | Dynamic (json) | A list of playbooks this automation rule triggered successfully.<br><br>Each playbook record in the list contains:<br>- **RunId:** The run ID for this triggering of the Logic Apps workflow<br>- **WorkflowId:** The unique identifier (full ARM resource ID) of the Logic Apps workflow resource. |
| **TriggeredWhen**        | String         | `Created` or `Updated`. Indicates whether the rule was triggered due to the creation or updating of an incident or alert. |

### Playbooks

| ColumnName               | ColumnType     | Description                                                   |
| ------------------------ | -------------- | ------------------------------------------------------------- |
| **IncidentName**         | String         | The resource ID of the Microsoft Sentinel incident on which the rule was triggered.  |
| **IncidentNumber**       | String         | The sequential number of the Microsoft Sentinel incident as shown in the portal. |
| **RunId**                | String         | The run ID for this triggering of the Logic Apps workflow.    |
| **TriggeredByName**      | Dynamic (json) | Information on the identity (user or application) that triggered the playbook. |
| **TriggeredOn**          | String         | `Incident`. The object on which the playbook was triggered.<br>(Playbooks using the alert trigger are logged only if they're called by automation rules, so those playbook runs will appear in the **TriggeredPlaybooks** extended property under automation rule events.) |


## Next steps

- Learn about [health monitoring in Microsoft Sentinel](health-audit.md).
- [Turn on health monitoring](enable-monitoring.md) in Microsoft Sentinel.
- Monitor the [health of your data connectors](monitor-data-connector-health.md).
- Monitor the [health of your automation rules and playbooks](monitor-automation-health.md).
