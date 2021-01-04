---
title: Synapse SQL-Ressourcennutzung
description: Hier finden Sie Informationen zu Synapse SQL-Verbrauchsmodellen in Azure Synapse Analytics.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 0e3bbb2532340664d0fc54b29bea3ef3af75f9b6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459160"
---
# <a name="synapse-sql-resource-consumption"></a>Synapse SQL-Ressourcennutzung

In diesem Artikel werden die Modelle zum Ressourcenverbrauch von Synapse SQL beschrieben.

## <a name="serverless-sql-pool"></a>Serverloser SQL-Pool

Ein serverloser SQL-Pool ist ein Dienst mit abfragebasierter Bezahlung, bei dem Sie nicht die richtige Größe auswählen müssen. Das System passt sich automatisch an Ihre Anforderungen an, sodass Sie weder Ihre Infrastruktur verwalten und noch die richtige Größe für Ihre Lösung auswählen müssen.

## <a name="dedicated-sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Dedizierter SQL-Pool: Data Warehouse-Einheiten (DWUs) und Compute Data Warehouse-Einheiten (cDWUs)

Empfehlungen zum Auswählen der idealen Anzahl von Data Warehouse-Einheiten (Data Warehouse Units, DWUs) sowie zum Ändern der Anzahl der Einheiten.

### <a name="data-warehouse-units"></a>Data Warehouse-Einheiten

Ein Synapse SQL-Pool ist eine Sammlung von Analyseressourcen, die bereitgestellt werden. Analyseressourcen werden als eine Kombination aus CPU, Arbeitsspeicher und E/A definiert. Diese drei Ressourcen werden in Computeskalierungseinheiten gebündelt, die als „Data Warehouse-Einheiten“ (Data Warehouse Units, DWUs) bezeichnet werden. Eine DWU stellt ein abstraktes, normalisiertes Maß für Computeressourcen und -leistung dar. Durch eine Änderung der Dienstebene wird die Anzahl von DWUs geändert, die für das System verfügbar sind. Hiermit werden dann die Leistung und die Kosten Ihres Systems angepasst.

Um die Leistung zu steigern, können Sie die Anzahl von Data Warehouse-Einheiten erhöhen. Verringern Sie die Anzahl von Data Warehouse-Einheiten, um die Leistung zu reduzieren. Speicher- und Computekosten werden separat in Rechnung gestellt. Das Ändern der Data Warehouse-Einheiten wirkt sich daher nicht auf die Speicherkosten aus.

Die Leistung für Data Warehouse-Einheiten basiert auf den folgenden Data Warehouse-Workloadmetriken:

- Wie schnell eine Data Warehousing-Standardabfrage eine große Anzahl von Zeilen überprüfen und anschließend eine komplexe Aggregation ausführen kann. Dies ist ein Vorgang mit hohem E/A- und CPU-Aufwand.
- Wie schnell das Data Warehouse Daten aus Azure Storage Blob oder Azure Data Lake aufnehmen kann. Dies ist ein Vorgang mit hohem Netzwerk- und CPU-Aufwand.
- Wie schnell mit dem T-SQL-Befehl [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) eine Tabelle kopiert werden kann. Dieser Vorgang umfasst das Lesen von Daten aus dem Speicher, das Verteilen auf die Knoten der Anwendung und das erneute Schreiben in den Speicher. Dies ist ein Vorgang mit hohem CPU-, E/A- und Netzwerkaufwand.

Erhöhen der Anzahl der DWUs:

- Lineare Änderung der Systemleistung bei Scans, Aggregationen und CTAS-Anweisungen
- Erhöhen der Anzahl von Readern und Writern für PolyBase-Ladevorgänge
- Erhöht die maximale Anzahl von gleichzeitigen Abfragen und Parallelitätsslots.

### <a name="service-level-objective"></a>Servicelevelziel

Das Servicelevelziel (Service Level Objective, SLO) ist die Skalierbarkeitseinstellung, die die Kosten und Leistungsstufe Ihres Data Warehouse festlegt. Die Servicelevel für Gen2 werden in cDWU (Compute-Data Warehouse-Einheiten) gemessen, z.B. DW2000c. Gen1-Servicelevel werden in DWUs gemessen, z.B. DW2000.

Das Servicelevelziel (Service Level Objective, SLO) ist die Skalierbarkeitseinstellung, die die Kosten und Leistungsstufe Ihres Data Warehouse festlegt. Die Servicelevel für einen dedizierten Gen2-SQL-Pool werden in DWU (Data Warehouse-Einheiten) gemessen, z. B. DW2000c.

> [!NOTE]
> Azure Synapse Analytics Gen2 wurden kürzlich zusätzliche Skalierungsfunktionen zur Unterstützung von Computeebenen bis zu 100 cDWU hinzugefügt. Vorhandene Data Warehouses mit derzeit Gen1, die die niedrigeren Computeebenen erfordern, können jetzt in den Regionen, die derzeit ohne zusätzliche Kosten zur Verfügung stehen, auf Gen2 upgraden.  Wenn Ihre Region noch nicht unterstützt wird, können Sie weiterhin auf eine unterstützte Region upgraden. Weitere Informationen finden Sie unter [Optimieren der Leistung durch ein Upgrade von SQL Data Warehouse](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

In T-SQL bestimmt die Einstellung SERVICE_OBJECTIVE den Servicelevel und die Leistungsstufe für Ihren dedizierten SQL-Pool.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Leistungsstufen und Data Warehouse-Einheiten

Jede Leistungsstufe verwendet eine etwas andere Maßeinheit für ihre Data Warehouse-Einheiten. Dieser Unterschied wird in der Rechnung berücksichtigt, weil die Skalierungseinheit direkt in eine Abrechnung übersetzt wird.

- Gen1-Data Warehouses werden in DWUs (Data Warehouse-Einheiten) gemessen.
- Gen2-Data Warehouses werden in cDWUs (Compute-Data Warehouse-Einheiten) gemessen.

Sowohl DWUs als auch cDWUs unterstützen das zentrale Herunterskalieren und Hochskalieren sowie Computepausen, wenn Sie das Data Warehouse nicht nutzen müssen. Diese Vorgänge werden alle bei Bedarf ausgeführt. Gen2 verwendet einen lokalen datenträgerbasierten Cache auf den Computeknoten, um die Leistung zu verbessern. Wenn Sie das System skalieren oder anhalten, wird der Cache für ungültig erklärt. Daher ist eine „Aufwärmphase“ des Caches erforderlich, bevor eine optimale Leistung erzielt wird.  

Wenn Sie Data Warehouse-Einheiten erhöhen, erhöhen Sie die Computingressourcen linear. Gen2 bietet die beste Abfrageleistung und die höchste Skalierung. Gen2-Systeme nutzen den Cache außerdem am meisten.

#### <a name="capacity-limits"></a>Kapazitätsgrenzen

Jeder SQL-Server (z.B. myserver.database.windows.net) weist ein Kontingent für [DTUs (Database Transaction Unit, Datenübertragungseinheiten)](../../azure-sql/database/service-tiers-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) auf, das eine bestimmte Anzahl von Data Warehouse-Einheiten zulässt. Weitere Informationen finden Sie in den [Kapazitätsgrenzen für die Workloadverwaltung](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

### <a name="assess-the-number-of-data-warehouse-units-you-need"></a>Ermitteln der Anzahl der benötigten Data Warehouse-Einheiten

Die optimale Anzahl der Data Warehouse-Einheiten hängt in hohem Maß von Ihrer Workload und der Datenmenge ab, die Sie in das System geladen haben.

Schritte zum Ermitteln der besten DWU-Anzahl für Ihre Arbeitsauslastung:

1. Beginnen Sie, indem Sie eine kleinere DWU auswählen.
2. Überwachen Sie die Anwendungsleistung, wenn Sie Datenmengen im System testen, und beobachten Sie dabei die Anzahl der ausgewählten DWUs im Vergleich zur beobachteten Leistung.
3. Identifizieren Sie zusätzliche Anforderungen für periodische Zeiten mit Spitzenauslastung. Workloads, bei denen die Aktivität erhebliche Spitzen und Täler aufweist, müssen unter Umständen häufig skaliert werden.

Ein SQL-Pool ist ein horizontal hochskalierbares System, das große Mengen von Compute- und Abfragedaten bereitstellen kann. Wenn Sie seine wahren Skalierungsfunktionen (insbesondere bei größeren DWUs) in Aktion sehen möchten, wird empfohlen, bei der Skalierung das Dataset zu skalieren, um sicherzustellen, dass genügend Daten zum Übertragen an die CPUs verfügbar sind. Es wird empfohlen, für die Skalierungstests mindestens 1 TB zu verwenden.

> [!NOTE]
>
> Die Abfrageleistung steigt bei höherem Parallelisierungsgrad nur, wenn die Verarbeitung auf mehrere Computeknoten aufgeteilt werden kann. Wenn Sie feststellen, dass die Skalierung Ihre Leistung nicht ändert, müssen Sie möglicherweise Ihr Tabellendesign und/oder Ihre Abfragen anpassen. Eine Anleitung zur Abfrageoptimierung finden Sie unter [Spickzettel für Azure SQL Data Warehouse](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="permissions"></a>Berechtigungen

Zum Ändern der Data Warehouse-Einheiten sind die unter [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) beschriebenen Berechtigungen erforderlich.

Integrierte Azure-Rollen wie „Mitwirkender von SQL DB“ und „SQL Server-Mitwirkender“ können die DWU-Einstellungen ändern.

#### <a name="view-current-dwu-settings"></a>Anzeigen der aktuellen DWU-Einstellungen

So zeigen Sie die aktuellen DWU-Einstellungen an:

1. Öffnen Sie den Objekt-Explorer von SQL Server in Visual Studio.
2. Stellen Sie eine Verbindung mit der Masterdatenbank mit dem logischen SQL-Server her.
3. Treffen Sie eine Auswahl aus der dynamischen Verwaltungssicht „sys.database_service_objectives“. Beispiel:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>Ändern von Data Warehouse-Einheiten

#### <a name="azure-portal"></a>Azure-Portal

So ändern Sie DWUs:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), öffnen Sie Ihre Datenbank, und wählen Sie dann **Skalieren** aus.

2. Verschieben Sie unter **Skalieren** den Schieberegler nach links oder rechts, um die DWU-Einstellung zu ändern.

3. Wählen Sie **Speichern** aus. Eine Bestätigungsmeldung wird angezeigt. Wählen Sie zur Bestätigung **Ja** oder zum Abbrechen **Nein** aus.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Zum Ändern der DWUs verwenden Sie das PowerShell-Cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Im folgenden Beispiel wird das Servicelevelziel für die Datenbank „MySQLDW“, die auf dem Server „MyServer“ gehostet wird, auf „DW1000“ festgelegt.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Weitere Informationen finden Sie unter [PowerShell-Cmdlets für Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

#### <a name="t-sql"></a>T-SQL

Mit T-SQL können Sie die aktuellen DWU-Einstellungen anzeigen, die Einstellungen ändern und den Fortschritt überprüfen.

So ändern Sie die DWUs

1. Stellen Sie eine Verbindung mit der Masterdatenbank mit Ihrem Server her.
2. Verwenden Sie die T-SQL-Anweisung [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Im folgenden Beispiel wird das Servicelevelziel für die Datenbank „MySQLDW“ auf „DW1000c“ gesetzt.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>REST-APIs

Um die DWUs zu ändern, verwenden Sie die REST-API zum [Erstellen oder Aktualisieren einer Datenbank](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Im folgenden Beispiel wird der Servicelevel-Zielpunkt für die Datenbank „MySQLDW“, die auf dem Server „MyServer“ gehostet wird, auf „DW1000c“ gesetzt. Der Server befindet sich in einer Azure-Ressourcengruppe namens „ResourceGroup1“.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Weitere REST-API-Beispiele finden Sie unter [REST-APIs für Azure Synapse Analytics](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="check-status-of-dwu-changes"></a>Überprüfen des Status von DWU-Änderungen

Es kann mehrere Minuten in Anspruch nehmen, bis DWU-Änderungen abgeschlossen werden. Wenn Sie die Skalierung automatisch ausführen, empfiehlt es sich, eine Logik zu implementieren, die sicherstellt, dass bestimmte Vorgänge abgeschlossen wurden, bevor mit einer andern Aktion fortgefahren wird.

Überprüfen Sie den Datenbankzustand über verschiedene Endpunkte, um sicherzugehen, dass die Automatisierung ordnungsgemäß implementiert wird. Über das Portal erhalten Sie bei Abschluss eines Vorgangs eine Benachrichtigung sowie Informationen zum aktuellen Zustand der Datenbanken. Das Portal ermöglicht aber keine programmgesteuerte Überprüfung des Zustands.

Der Datenbankzustand für horizontale Hochskalierungsvorgänge kann nicht mit dem Azure-Portal überprüft werden.

So überprüfen Sie den Zustand von DWU-Änderungen:

1. Stellen Sie eine Verbindung mit der Masterdatenbank mit Ihrem Server her.
2. Übermitteln Sie die folgende Abfrage, um den Datenbankzustand zu überprüfen.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Übermitteln der folgenden Abfrage zum Überprüfen des Betriebszustands

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Diese DMV gibt Informationen zu verschiedenen Verwaltungsvorgängen in Ihrem dedizierten SQL-Pool zurück. Dazu gehören etwa der Vorgang und der Status des Vorgangs, der IN_PROGRESS oder COMPLETED lauten kann.

### <a name="the-scaling-workflow"></a>Der Skalierungsworkflow

Wenn Sie einen Skalierungsvorgang starten, beendet das System zuerst alle geöffneten Sitzungen und führt ein Rollback aller offenen Transaktionen aus, um einen konsistenten Zustand zu gewährleisten. Bei Skalierungsvorgängen erfolgt die Skalierung nur ein Mal, nachdem das Rollback der Transaktionen abgeschlossen ist.  

- Für einen Vorgang zum zentralen Hochskalieren trennt das System die Verbindung aller Computeknoten, stellt die zusätzlichen Computeknoten bereit und führt dann erneut das Anfügen an die Speicherebene aus.
- Für einen Vorgang zum zentralen Herunterskalieren trennt das System die Verbindung aller Computeknoten und fügt dann nur die benötigten Knoten wieder an die Speicherebene an.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten der Leistung finden Sie unter [Ressourcenklassen für die Workloadverwaltung](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) und [Grenzwerte für Arbeitsspeicher und Parallelität](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
