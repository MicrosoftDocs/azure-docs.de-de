---
title: Überwachen von Workloads – Azure-Portal
description: Überwachen von Synapse SQL im Azure-Portal
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 53c68f44273b14658bd4d95ce7cf3cf847c077d0
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93025951"
---
# <a name="monitor-workload---azure-portal"></a>Überwachen von Workloads – Azure-Portal

In diesem Artikel wird beschrieben, wie Sie im Azure-Portal Ihre Workload überwachen. Dazu gehört auch das Einrichten von Azure Monitor-Protokollen, um Abfrageausführung und Workloadtrends mit Log Analytics für [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/) zu untersuchen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- SQL-Pool: Sie sammeln Protokolle für einen SQL-Pool. Wenn Sie keinen SQL-Pool bereitgestellt haben, lesen Sie die Anleitungen unter [Erstellen eines SQL-Pools](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="create-a-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs

Navigieren Sie im Suchfeld zu den Log Analytics-Arbeitsbereichen, und erstellen Sie einen Arbeitsbereich.

![Log Analytics-Arbeitsbereiche](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Der Screenshot zeigt die Log Analytics-Arbeitsbereiche, in denen Sie „Hinzufügen“ auswählen können.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Der Screenshot zeigt den Log Analytics-Arbeitsbereich, in dem Sie Werte eingeben können.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Weitere Informationen zu Arbeitsbereichen finden Sie in dieser [Dokumentation](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-resource-logs"></a>Aktivieren von Ressourcenprotokollen

Konfigurieren Sie die Diagnoseeinstellungen, um Protokolle aus Ihrem SQL-Pool auszugeben. Protokolle bestehen aus Telemetriesichten, die den am häufigsten verwendeten Leistungsproblemen bei dynamischen Verwaltungssichten (DMVs) entsprechen. Aktuell werden die folgenden Ansichten unterstützt:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Aktivieren von Ressourcenprotokollen](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Protokolle können an Azure Storage, Stream Analytics oder Log Analytics ausgegeben werden. Wählen Sie für dieses Tutorial „Log Analytics“ aus.

![Angeben der Protokolle](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Ausführen von Abfragen für Log Analytics

Navigieren Sie zu Ihrem Log Analytics-Arbeitsbereich, in dem Sie die folgenden Schritte ausführen können:

- Analysieren von Protokollen mithilfe von Protokollabfragen und Speichern der Abfragen zur Wiederverwendung
- Speichern von Abfragen zur Wiederverwendung
- Erstellen von Protokollwarnungen
- Anheften von Abfrageergebnissen an ein Dashboard

Weitere Informationen zu der Funktionalität von Protokollabfragen finden Sie in dieser [Dokumentation](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

![Log Analytics-Arbeitsbereich-Editor](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Abfragen von Log Analytics-Arbeitsbereichen](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

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

Nachdem Sie nun Azure Monitor-Protokolle eingerichtet und konfiguriert haben, können Sie [Azure-Dashboards anpassen](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), um die Protokolle mit Ihrem Team gemeinsam zu nutzen.
