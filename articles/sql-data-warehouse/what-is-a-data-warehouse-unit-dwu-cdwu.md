---
title: Data Warehouse-Einheiten (Data Warehouse Units, DWUs, cDWUs) in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Empfehlungen zum Auswählen der idealen Anzahl von Data Warehouse-Einheiten (Data Warehouse Units, DWUs, cDWUs) sowie zum Ändern der Anzahl der Einheiten.
services: sql-data-warehouse
author: happynicolle
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: nicw
ms.reviewer: igorstan
ms.openlocfilehash: d71594e745fdd57ba383b98b547cceb0be1b61bc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65797836"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Data Warehouse-Einheiten (DWUs) und Compute Data Warehouse-Einheiten (cDWUs)
Empfehlungen zum Auswählen der idealen Anzahl von Data Warehouse-Einheiten (Data Warehouse Units, DWUs, cDWUs) sowie zum Ändern der Anzahl der Einheiten. 

## <a name="what-are-data-warehouse-units"></a>Was sind Data Warehouse-Einheiten?
Durch SQL Data Warehouse werden CPU, Arbeitsspeicher und E/A in Einheiten von Computeskalierungen gebündelt, die als Data Warehouse-Einheiten (DWUs) bezeichnet werden. Eine DWU stellt ein abstraktes, normalisiertes Maß für Computeressourcen und -leistung dar. Durch Ändern der Dienstebene ändern Sie die Anzahl der DWUs, die dem System zugewiesen werden. Dies wiederum passt die Leistung und die Kosten Ihres Systems an. 

Wenn Sie für höhere Leistung bezahlen möchten, können Sie die Anzahl der Data Warehouse-Einheiten erhöhen. Um für weniger Leistung zu bezahlen, verringern Sie die Anzahl der Data Warehouse-Einheiten. Speicher- und Computekosten werden separat in Rechnung gestellt. Das Ändern der Data Warehouse-Einheiten wirkt sich daher nicht auf die Speicherkosten aus.

Die Leistung für Data Warehouse-Einheiten basiert auf den folgenden Data Warehouse-Workloadmetriken:

- Wie schnell kann eine Data Warehousing-Standardabfrage eine große Anzahl von Zeilen überprüfen und anschließend eine komplexe Aggregation ausführen? Dies ist ein Vorgang mit hohem E/A- und CPU-Aufwand.
- Wie schnell kann das Data Warehouse Daten aus Azure Storage Blob oder Azure Data Lake aufnehmen? Dies ist ein Vorgang mit hohem Netzwerk- und CPU-Aufwand. 
- Wie schnell kann der T-SQL-Befehl [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) eine Tabelle kopieren? Dieser Vorgang umfasst das Lesen von Daten aus dem Speicher, das Verteilen auf die Knoten der Anwendung und das erneute Schreiben in den Speicher. Dies ist ein Vorgang mit hohem CPU-, E/A- und Netzwerkaufwand.

Erhöhen der Anzahl der DWUs:
- Lineare Änderung der Systemleistung bei Scans, Aggregationen und CTAS-Anweisungen
- Erhöhen der Anzahl von Readern und Writern für PolyBase-Ladevorgänge
- Erhöht die maximale Anzahl von gleichzeitigen Abfragen und Parallelitätsslots.

## <a name="service-level-objective"></a>Servicelevelziel
Das Servicelevelziel (Service Level Objective, SLO) ist die Skalierbarkeitseinstellung, die die Kosten und Leistungsstufe Ihres Data Warehouse festlegt. Die Servicelevel für Gen2 werden in cDWU (Compute-Data Warehouse-Einheiten) gemessen, z.B. DW2000c. Gen1-Servicelevel werden in DWUs gemessen, z.B. DW2000.
  > [!NOTE]
  > Azure SQL Data Warehouse Gen2 hat kürzlich zusätzliche Skalierungsfunktionen zur Unterstützung von Computeebenen bis zu 100 cDWU hinzugefügt. Vorhandene Data Warehouses mit derzeit Gen1, die die niedrigeren Computeebenen erfordern, können jetzt in den Regionen, die derzeit ohne zusätzliche Kosten zur Verfügung stehen, auf Gen2 upgraden.  Wenn Ihre Region noch nicht unterstützt wird, können Sie weiterhin auf eine unterstützte Region upgraden. Weitere Informationen finden Sie unter [Upgrade auf Gen2](upgrade-to-latest-generation.md).

In T-SQL bestimmt die Einstellung SERVICE_OBJECTIVE den Servicelevel und die Leistungsstufe für das Data Warehouse.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Leistungsstufen und Data Warehouse-Einheiten

Jede Leistungsstufe verwendet eine etwas andere Maßeinheit für ihre Data Warehouse-Einheiten. Dieser Unterschied wird in der Rechnung berücksichtigt, weil die Skalierungseinheit direkt in eine Abrechnung übersetzt wird.

- Gen1-Data Warehouses werden in DWUs (Data Warehouse-Einheiten) gemessen.
- Gen2-Data Warehouses werden in cDWUs (Compute-Data Warehouse-Einheiten) gemessen. 

Sowohl DWUs als auch cDWUs unterstützen das zentrale Herunterskalieren und Hochskalieren sowie Computepausen, wenn Sie das Data Warehouse nicht nutzen müssen. Diese Vorgänge werden alle bei Bedarf ausgeführt. Gen2 verwendet einen lokalen datenträgerbasierten Cache auf den Computeknoten, um die Leistung zu verbessern. Wenn Sie das System skalieren oder anhalten, wird der Cache für ungültig erklärt. Daher ist eine „Aufwärmphase“ des Caches erforderlich, bevor eine optimale Leistung erzielt wird.  

Wenn Sie Data Warehouse-Einheiten erhöhen, erhöhen Sie die Computingressourcen linear. Gen2 bietet die beste Abfrageleistung und die höchste Skalierung. Diese Systeme nutzen den Cache am meisten.

### <a name="capacity-limits"></a>Kapazitätsgrenzen
Jeder SQL-Server (z.B. myserver.database.windows.net) weist ein Kontingent für [DTUs (Database Transaction Unit, Datenübertragungseinheiten)](../sql-database/sql-database-what-is-a-dtu.md) auf, das eine bestimmte Anzahl von Data Warehouse-Einheiten zulässt. Weitere Informationen finden Sie in den [Kapazitätsgrenzen für die Workloadverwaltung](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Wie viele Data Warehouse-Einheiten benötige ich?
Die optimale Anzahl der Data Warehouse-Einheiten hängt in hohem Maß von Ihrer Arbeitsauslastung und der Datenmenge ab, die Sie in das System geladen haben.

Schritte zum Ermitteln der besten DWU-Anzahl für Ihre Arbeitsauslastung:

1. Beginnen Sie, indem Sie eine kleinere DWU auswählen. 
2. Überwachen Sie die Anwendungsleistung, wenn Sie Datenmengen im System testen, und beobachten Sie dabei die Anzahl der ausgewählten DWUs im Vergleich zur beobachteten Leistung.
3. Identifizieren Sie zusätzliche Anforderungen für periodische Zeiten mit Spitzenauslastung. Wenn die Arbeitsauslastung starke Aktivitätsschwankungen zeigt und es einen guten Grund gibt, häufige Skalierungen auszuführen.

SQL Data Warehouse ist ein horizontal hochskalierbares System, das große Mengen von Compute- und Abfragedaten bereitstellen kann. Wenn Sie seine wahren Skalierungsfunktionen (insbesondere bei größeren DWUs) in Aktion sehen möchten, wird empfohlen, bei der Skalierung das Dataset zu skalieren, um sicherzustellen, dass genügend Daten zum Übertragen an die CPUs verfügbar sind. Es wird empfohlen, für die Skalierungstests mindestens 1 TB zu verwenden.

> [!NOTE]
>
> Die Abfrageleistung steigt bei höherem Parallelisierungsgrad nur, wenn die Verarbeitung auf mehrere Computeknoten aufgeteilt werden kann. Wenn Sie feststellen, dass die Skalierung Ihre Leistung nicht ändert, müssen Sie möglicherweise Ihr Tabellendesign und/oder Ihre Abfragen anpassen. Eine Anleitung zur Abfrageoptimierung finden Sie unter [Spickzettel für Azure SQL Data Warehouse](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Berechtigungen

Zum Ändern der Data Warehouse-Einheiten sind die unter [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) beschriebenen Berechtigungen erforderlich. 

Integrierte Rollen für Azure-Ressourcen wie „Mitwirkender von SQL DB“ und „SQL Server-Mitwirkender“ können die DWU-Einstellungen ändern. 

## <a name="view-current-dwu-settings"></a>Anzeigen der aktuellen DWU-Einstellungen

So zeigen Sie die aktuellen DWU-Einstellungen an:

1. Öffnen Sie den Objekt-Explorer von SQL Server in Visual Studio.
2. Stellen Sie eine Verbindung mit der Masterdatenbank mit dem logischen SQL-Datenbank-Server her.
3. Treffen Sie eine Auswahl aus der dynamischen Verwaltungssicht „sys.database_service_objectives“. Beispiel:  

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Ändern von Data Warehouse-Einheiten

### <a name="azure-portal"></a>Azure-Portal
So ändern Sie DWUs oder cDWUs:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), öffnen Sie Ihre Datenbank, und klicken Sie dann auf **Skalieren**.

2. Verschieben Sie unter **Skalieren** den Schieberegler nach links oder rechts, um die DWU-Einstellung zu ändern.

3. Klicken Sie auf **Speichern**. Eine Bestätigungsmeldung wird angezeigt. Klicken Sie zur Bestätigung auf **Ja** oder zum Abbrechen auf **Nein**.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Um die DWUs oder cDWUs zu ändern, verwenden Sie das PowerShell-Cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase). Im folgenden Beispiel wird das Servicelevelziel für die Datenbank „MySQLDW“, die auf dem Server „MyServer“ gehostet wird, auf „DW1000“ festgelegt.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Weitere Informationen finden Sie unter [PowerShell-Cmdlets und REST-APIs für SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md).

### <a name="t-sql"></a>T-SQL
Mit T-SQL können Sie die aktuellen DWU- oder cDWU-Einstellungen anzeigen, die Einstellungen ändern und den Fortschritt überprüfen. 

So ändern Sie DWUs oder cDWUs:

1. Stellen Sie eine Verbindung mit der Masterdatenbank her, die Ihrem logischen SQL-Datenbank-Server zugeordnet ist.
2. Verwenden Sie die TSQL-Anweisung [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) . Im folgenden Beispiel wird das Servicelevelziel für die Datenbank „MySQLDW“ auf „DW1000“ gesetzt. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST-APIs

Um die DWUs zu ändern, verwenden Sie die REST-API zum [Erstellen oder Aktualisieren einer Datenbank](/rest/api/sql/databases/createorupdate). Im folgenden Beispiel wird der Servicelevel-Zielpunkt für die Datenbank „MySQLDW“, die auf dem Server „MyServer“ gehostet wird, auf „DW1000“ gesetzt. Der Server befindet sich in einer Azure-Ressourcengruppe namens „ResourceGroup1“.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Weitere REST-API-Beispiele finden Sie unter [REST-APIs für SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Überprüfen des Status von DWU-Änderungen

Es kann mehrere Minuten in Anspruch nehmen, bis DWU-Änderungen abgeschlossen werden. Wenn Sie die Skalierung automatisch ausführen, empfiehlt es sich, eine Logik zu implementieren, die sicherstellt, dass bestimmte Vorgänge abgeschlossen wurden, bevor mit einer andern Aktion fortgefahren wird. 

Überprüfen Sie den Datenbankzustand über verschiedene Endpunkte, um sicherzugehen, dass die Automatisierung ordnungsgemäß implementiert wird. Über das Portal erhalten Sie bei Abschluss eines Vorgangs eine Benachrichtigung sowie Informationen zum aktuellen Zustand der Datenbanken. Das Portal ermöglicht aber keine programmgesteuerte Überprüfung des Zustands. 

Der Zustand der Datenbank für horizontale Hochskalierungsvorgänge kann nicht mit dem Azure-Portal überprüft werden.

So überprüfen Sie den Zustand von DWU-Änderungen:

1. Stellen Sie eine Verbindung mit der Masterdatenbank her, die Ihrem logischen SQL-Datenbank-Server zugeordnet ist.
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

Diese DMV gibt Informationen zu verschiedenen Verwaltungsvorgängen in Ihrer SQL Data Warehouse-Instanz zurück. Dazu gehören etwa der Vorgang und der Status des Vorgangs, der IN_PROGRESS oder COMPLETED lauten kann.

## <a name="the-scaling-workflow"></a>Der Skalierungsworkflow

Wenn Sie einen Skalierungsvorgang einleiten, beendet das System zuerst alle geöffneten Sitzungen und führt ein Rollback aller offenen Transaktionen aus, um einen konsistenten Zustand zu gewährleisten. Bei Skalierungsvorgängen erfolgt die Skalierung nur ein Mal, nachdem das Rollback der Transaktionen abgeschlossen ist.  

- Für eine zentrale Hochskalierung stellt das System die zusätzliche Computeressourcen bereit und führt dann erneut eine Anfügung an die Speicherebene aus. 
- Für eine zentrale Herunterskalierung werden die nicht benötigten Knoten vom Speicher getrennt und an die verbleibenden Knoten erneut angefügt.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Verwalten der Leistung finden Sie unter [Ressourcenklassen für die Workloadverwaltung](resource-classes-for-workload-management.md) und [Grenzwerte für Arbeitsspeicher und Parallelität](memory-and-concurrency-limits.md).



