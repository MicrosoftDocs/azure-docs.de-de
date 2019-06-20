---
title: Überwachen von Workloads – Azure-Portal | Microsoft-Dokumentation
description: Überwachen von Azure SQL Data Warehouse mit dem Azure-Portal
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60748914"
---
# <a name="monitor-workload---azure-portal"></a>Überwachen von Workloads – Azure-Portal

In diesem Artikel wird beschrieben, wie Sie im Azure-Portal Ihre Workload überwachen. Dazu gehört auch das Einrichten von Azure Monitor-Protokollen, um Abfrageausführung und Workloadtrends mit Log Analytics für [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/) zu untersuchen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Azure SQL Data Warehouse: Sie sammeln Protokolle für ein SQL Data Warehouse. Wenn Sie noch kein SQL Data Warehouse bereitgestellt haben, finden Sie entsprechende Anweisungen unter [Erstellen eines SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs

Navigieren Sie im Suchfeld zu den Log Analytics-Arbeitsbereichen, und erstellen Sie einen Arbeitsbereich. 

![Log Analytics-Arbeitsbereiche](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Hinzufügen eines Log Analytics-Arbeitsbereichs](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Hinzufügen eines Log Analytics-Arbeitsbereichs](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Weitere Informationen zu Arbeitsbereichen finden Sie in dieser [Dokumentation](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Aktivieren von Diagnoseprotokollen 

Konfigurieren Sie die Diagnoseeinstellungen, um Protokolle von Ihrem SQL Data Warehouse auszugeben. Die Protokolle bestehen aus Ansichten von Telemetriedaten von Ihrem Data Warehouse, die den am häufigsten verwendeten DMVs zur Behandlung von Leistungsproblemen für SQL Data Warehouse entsprechen. Aktuell werden die folgenden Ansichten unterstützt:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Aktivieren von Diagnoseprotokollen](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Protokolle können an Azure Storage, Stream Analytics oder Log Analytics ausgegeben werden. Wählen Sie für dieses Tutorial „Log Analytics“ aus.

![Angeben der Protokolle](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Ausführen von Abfragen für Log Analytics

Navigieren Sie zu Ihrem Log Analytics-Arbeitsbereich, in dem Sie die folgenden Schritte ausführen können:

- Analysieren von Protokollen mithilfe von Protokollabfragen und Speichern der Abfragen zur Wiederverwendung
- Speichern von Abfragen zur Wiederverwendung
- Erstellen von Protokollwarnungen
- Anheften von Abfrageergebnissen an ein Dashboard

Weitere Informationen zu der Funktionalität von Protokollabfragen finden Sie in dieser [Dokumentation](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Log Analytics-Arbeitsbereich-Editor](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Abfragen von Log Analytics-Arbeitsbereichen](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Beispielprotokollabfragen



```Kusto
//List all queries 
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```
```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart 
```
```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits" 
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```
## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun Azure Monitor-Protokolle eingerichtet und konfiguriert haben, können Sie [Azure-Dashboards anpassen](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards), um die Protokolle mit Ihrem Team gemeinsam zu nutzen.
