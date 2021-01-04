---
title: Workloadklassifizierung für dedizierte SQL-Pools
description: Leitfaden zur Verwendung der Klassifizierung, um Abfrageparallelität, Priorität und Computeressourcen für dedizierte SQL-Pools in Azure Synapse Analytics zu verwalten
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: bf19e2d1674d0a0c2102280b28b5549505c1dfab
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447765"
---
# <a name="workload-classification-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Workloadklassifizierung für dedizierte SQL-Pools in Azure Synapse Analytics

In diesem Artikel wird der Workloadklassifizierungsprozess beim Zuweisen einer Workloadgruppe und Priorität für eingehende Anforderungen mit dedizierten SQL-Pools in Azure Synapse erläutert.

## <a name="classification"></a>Klassifizierung

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Durch die Klassifizierung der Workloadverwaltung können Richtlinien auf Anforderungen angewendet werden, wobei [Ressourcenklassen](resource-classes-for-workload-management.md#what-are-resource-classes) und [Prioritäten](sql-data-warehouse-workload-importance.md) zugewiesen werden.

Data Warehousing-Workloads lassen sich auf viele Weisen klassifizieren. Die einfachste und gängigste Klassifizierung besteht allerdings im Laden und Abfragen von Daten. INSERT-, UPDATE- und DELETE-Anweisungen sind „Lasten“ für Daten.  Mit SELECT-Anweisungen werden sie abgefragt. Für eine Data Warehousing-Lösung wird häufig eine Workloadrichtlinie für Ladeaktivitäten eingesetzt, wodurch z. B. eine höhere Ressourcenklasse und mehr Ressourcen zugewiesen werden können. Eine unterschiedliche Workloadrichtlinie könnte auf Abfragen angewendet werden, wodurch beispielsweise im Gegensatz zu Ladeaktivitäten eine niedrigere Priorität festgelegt wird.

Für Lade- und Abfrageworkloads können Sie auch Unterklassifizierungen erstellen. Durch diese erhalten Sie mehr Kontrolle über Ihre Workloads. Abfrageworkloads können beispielsweise aus Cubeaktualisierungen, Dashboardabfragen oder Ad-hoc-Abfragen bestehen. Sie können jede dieser Abfrageworkloads mit unterschiedlichen Ressourcenklassen oder Prioritätseinstellungen klassifizieren. Auch Ladevorgänge können von der Unterklassifizierung profitieren. Große Transformationen können größeren Ressourcenklassen zugewiesen werden. Eine höhere Priorität kann festgelegt werden, um sicherzustellen, dass wichtige Verkaufsdaten vor Wetterdaten oder vor den Daten eines Social Media-Datenfeeds geladen werden.

Nicht alle Anweisungen sind klassifiziert, da sie keine Ressourcen oder Bedeutung erfordern, um die Ausführung zu beeinflussen.  DBCC-Befehle, BEGIN-, COMMIT- und ROLLBACK TRANSACTION-Anweisungen werden nicht klassifiziert.

## <a name="classification-process"></a>Klassifizierungsprozess

Die Klassifizierung für dedizierte SQL-Pools erfolgt zurzeit, indem Benutzern mithilfe von [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) einer Rolle zugewiesen werden, der wiederum eine entsprechende Ressourcenklasse zugewiesen wurde. Die Möglichkeit, andere Anforderungen als eine Anmeldung bei einer Ressourcenklasse zu charakterisieren, ist bei dieser Funktion beschränkt. Mit der [CREATE WORKLOAD CLASSIFIER-Syntax](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) steht nun eine leistungsfähigere Klassifizierungsmethode zur Verfügung.  Mit dieser Syntax können Benutzer dedizierter SQL-Pools Priorität zuweisen und angeben, wie viele Systemressourcen einer Anforderung über den Parameter `workload_group` zugewiesen werden.

> [!NOTE]
> Die Klassifizierung wird für jede Anforderung einzeln ausgewertet. Mehrere Anforderungen in einer einzelnen Sitzung können separat klassifiziert werden.

## <a name="classification-weighting"></a>Klassifizierungsgewichtung

Im Rahmen des Klassifizierungsprozesses wird mittels Gewichtung bestimmt, welche Arbeitsauslastungsgruppe zugewiesen wird.  Die Gewichtung funktioniert wie folgt:

|Klassifizierungsparameter |Weight   |
|---------------------|---------|
|MEMBERNAME:USER      |64       |
|MEMBERNAME:ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

Der Parameter `membername` ist obligatorisch.  Handelt es sich bei dem angegebenen Mitgliedsnamen jedoch um einen Datenbankbenutzer und nicht um eine Datenbankrolle, ist die Gewichtung für den Benutzer höher, sodass dieser Klassifizierer ausgewählt wird.

Wenn ein Benutzer Mitglied mehrerer Rollen mit verschiedenen zugewiesenen Ressourcenklassen oder Übereinstimmungen in mehreren Klassifizierern ist, wird dem Benutzer die höchste Ressourcenklasse zugewiesen.  Dies entspricht dem Verhalten für bereits vorhandene Ressourcenklassenzuweisungen.

## <a name="system-classifiers"></a>Systemklassifizierungen

Workloadklassifizierungen verfügen über Systemworkloadklassifizierungen. Letztere ordnen die Ressourcenklassen-Rollenmitgliedschaften den Ressourcenzuordnungen der Ressourcenklassen mit normaler Priorität zu. Systemklassifizierungen können nicht gelöscht werden. Sie können die folgende Abfrage ausführen, um Systemklassifizierungen anzuzeigen:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Kombinieren von Ressourcenklassenzuweisungen mit Klassifizierungen

Systemklassifizierungen, die in Ihrem Auftrag erstellt werden, stellen eine einfache Möglichkeit zum Migrieren einer Workloadklassifizierung bereit. Wenn Sie Ressourcenklassen-Rollenzuordnungen mit einer Klassifizierungsrangfolge verwenden, kann dies beim Erstellen neuer priorisierter Klassifizierungen zu Fehlklassifizierungen führen.

Nehmen Sie das folgende Szenario als Beispiel:

- Ein vorhandenes Data Warehouse verfügt über den Datenbankbenutzer DBAUser, der der Ressourcenklassenrolle „largerc“ zugewiesen ist. Die Ressourcenklassenzuweisung wurde mit „sp_addrolemember“ durchgeführt.
- Das Data Warehouse wird nun mit der Workloadverwaltung aktualisiert.
- Zum Testen der neuen Klassifizierungssyntax wird über die Datenbankrolle „DBARole“ (von der DBAUser ein Mitglied ist) eine Klassifizierung für beide erstellt. Dabei wird eine Zuordnung zu „mediumrc“ mit hoher Priorität vorgenommen.
- Wenn DBAUser sich anmeldet und eine Abfrage ausführt, wird diese „largerc“ zugewiesen, da ein Benutzer Vorrang vor einer Rollenmitgliedschaft hat.

Beim Erstellen von Workloadklassifizierungen wird empfohlen, Ressourcenklassen-Rollenzuordnungen zu entfernen, da Sie auf diese Weise Probleme mit Fehlklassifizierungen leichter beheben können.  Der folgende Code gibt Ressourcenklassen-Rollenmitgliedschaften zurück.  Führen Sie [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) für alle Mitgliedsnamen aus, die von der entsprechenden Ressourcenklasse zurückgegeben werden.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen einer Klassifizierung finden Sie unter [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  
- Beachten Sie für die Erstellung eines Workloadklassifizierers die folgende Schnellstartanleitung: [Erstellen eines Workloadklassifizierers](quickstart-create-a-workload-classifier-tsql.md).
- Lesen Sie die Anleitungsartikel zum [Konfigurieren der Workloadpriorität](sql-data-warehouse-how-to-configure-workload-importance.md) und zum [Verwalten und Überwachen der Workloadpriorität](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Sie können die Abfragen und die zugewiesene Wichtigkeit unter [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) anzeigen.
