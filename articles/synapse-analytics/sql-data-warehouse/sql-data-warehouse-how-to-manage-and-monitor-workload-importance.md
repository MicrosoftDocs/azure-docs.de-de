---
title: Verwalten und Überwachen der Workloadpriorität in dedizierten SQL-Pools
description: Hier erfahren Sie, wie Sie die Priorität für die Anforderungsebene in dedizierten SQL-Pools für Azure Synapse Analytics verwalten und überwachen können.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fb0a5fbf33b48521882646bf8fb5eb3fe5dacca6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459201"
---
# <a name="manage-and-monitor-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Verwalten und Überwachen der Workloadpriorität in dedizierten SQL-Pools für Azure Synapse Analytics

Verwalten und überwachen Sie die Priorität für die Anforderungsebene in dedizierten SQL-Pools für Azure Synapse mithilfe von dynamischen Verwaltungssichten (Dynamic Management Views, DMVs) und Katalogsichten.

## <a name="monitor-importance"></a>Überwachen der Priorität

Überwachen Sie die Priorität in der dynamischen Verwaltungssicht [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) über die neue Spalte für Priorität.
Die nachstehende Überwachungsabfrage zeigt die Übermittlungszeit und Startzeit für Abfragen. Überprüfen Sie die Übermittlungszeit und Startzeit zusammen mit der Priorität, um zu erfahren, wie Planung die Priorität beeinflusst.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Wenn Sie genauer sehen möchten, wie Abfragen geplant werden, verwenden Sie die Katalogsichten.

## <a name="manage-importance-with-catalog-views"></a>Verwalten der Priorität mit Katalogsichten

Die Katalogsicht „sys.workload_management_workload_classifiers“ enthält Informationen zu Klassifizierern. Zum Ausschließen der systemdefinierten Klassifizierer, die Ressourcenklassen zugeordnet werden, führen Sie den folgenden Code aus:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Die Katalogsicht, [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), enthält Informationen zu den Parametern, die bei der Erstellung des Klassifizierers verwendet werden.  Die nachstehende Abfrage zeigt, dass „ExecReportsClassifier“ beim Parameter ```membername``` für Werte mit „ExecutiveReports“ erstellt wurde:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Abfrageergebnisse](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Beim Erstellen von Workloadklassifizierungen wird empfohlen, Ressourcenklassen-Rollenzuordnungen zu entfernen, da Sie auf diese Weise Probleme mit Fehlklassifizierungen leichter beheben können. Der folgende Code gibt Ressourcenklassen-Rollenmitgliedschaften zurück. Führen Sie „sp_droprolemember“ für jeden ```membername``` aus, der von der entsprechenden Ressourcenklasse zurückgegeben wird.
Hier ist ein Beispiel für Überprüfung auf Existenz, bevor ein Workloadklassifizierer gelöscht wird:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Klassifizierung finden Sie unter [Workloadklassifizierung](sql-data-warehouse-workload-classification.md).
- Weitere Informationen zu Priorität finden Sie unter [Workloadpriorität](sql-data-warehouse-workload-importance.md).

> [!div class="nextstepaction"]
> [Wechseln zu „Konfigurieren der Workloadpriorität“](sql-data-warehouse-how-to-configure-workload-importance.md)
