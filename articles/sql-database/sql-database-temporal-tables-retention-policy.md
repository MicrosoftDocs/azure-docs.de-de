---
title: Verwalten von Verlaufsdaten in temporalen Tabellen mit einer Aufbewahrungsrichtlinie | Microsoft Docs
description: Erfahren Sie, wie Sie eine temporale Aufbewahrungsrichtlinie verwenden, um die Kontrolle über Verlaufsdaten zu behalten.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 62e88d912c55015f87cc00f21527010ad01ee00c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615661"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Verwalten von Verlaufsdaten in temporalen Tabellen mit einer Aufbewahrungsrichtlinie

Temporale Tabellen können die Größe einer Datenbank mehr erhöhen als reguläre Tabellen, insbesondere, wenn Sie Verlaufsdaten für einen längeren Zeitraum beibehalten. Daher stellt eine Aufbewahrungsrichtlinie für Verlaufsdaten einen wichtigen Aspekt der Planung und Verwaltung des Lebenszyklus jeder temporalen Tabelle dar. Temporale Tabellen in Azure SQL-Datenbank verfügen über einen benutzerfreundlichen Aufbewahrungsmechanismus, der Sie bei dieser Aufgabe unterstützt.

Die temporale Verlaufsbeibehaltung kann auf der Ebene der einzelnen Tabelle konfiguriert werden, wodurch Benutzer flexible Alterungsrichtlinien erstellen können. Das Anwenden der temporalen Aufbewahrung ist einfach: Hierzu muss lediglich beim Erstellen der Tabelle bzw. beim Ändern des Schemas ein entsprechender Parameter gesetzt werden.

Nachdem Sie die Aufbewahrungsrichtlinie definiert haben, prüft Azure SQL-Datenbank regelmäßig, ob Zeilen mit Verlaufsdaten vorhanden sind, die für eine automatische Datenbereinigung in Frage kommen. Die Ermittlung passender Zeilen und deren Löschung aus der Verlaufstabelle erfolgen transparent in der Hintergrundaufgabe, die vom System geplant und ausgeführt wird. Die Altersbedingung für die Verlaufstabellenzeilen wird anhand der Spalte geprüft, die das Ende des SYSTEM_TIME-Zeitraums markiert. Wenn als Aufbewahrungszeitraum beispielsweise sechs Monate festgelegt sind, erfüllen für die Bereinigung in Frage kommende Tabellenzeilen die folgende Bedingung:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

Im vorherigen Beispiel wird angenommen, dass die Spalte **ValidTo** dem Ende des SYSTEM_TIME-Zeitraums entspricht.

## <a name="how-to-configure-retention-policy"></a>Konfigurieren der Aufbewahrungsrichtlinie

Bevor Sie die Aufbewahrungsrichtlinie für eine temporale Tabelle konfigurieren, überprüfen Sie zunächst, ob die temporale Verlaufsdatenaufbewahrung *auf Datenbankebene* aktiviert ist.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Das Datenbank-Flag **is_temporal_history_retention_enabled** ist standardmäßig auf ON gesetzt, Benutzer können es jedoch mit der ALTER DATABASE-Anweisung ändern. Nach dem Vorgang [Point-in-Time-Wiederherstellung](sql-database-recovery-using-backups.md) wird es außerdem automatisch auf OFF gesetzt. Um die Bereinigung der temporalen Verlaufsdatenaufbewahrung für Ihre Datenbank zu aktivieren, führen Sie die folgende Anweisung aus:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Sie können die Aufbewahrungsdauer für temporale Tabellen selbst dann konfigurieren, wenn **is_temporal_history_retention_enabled** auf „OFF“ festgelegt ist. In diesem Fall wird jedoch keine automatische Bereinigung veralteter Zeilen ausgelöst.

Die Aufbewahrungsrichtlinie wird beim Erstellen der Tabelle durch Angeben eines Werts für den Parameter HISTORY_RETENTION_PERIOD konfiguriert:

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Mit Azure SQL-Datenbank können Sie den Aufbewahrungszeitraum mithilfe verschiedener Zeiteinheiten festlegen: TAGE, WOCHEN, MONATE und JAHRE. Wenn HISTORY_RETENTION_PERIOD weggelassen wird, wird von einer unbegrenzten Aufbewahrungsdauer (INFINITE) ausgegangen. Sie können auch explizit das Schlüsselwort INFINITE verwenden.

In einigen Szenarios möchten Sie möglicherweise die Aufbewahrung nach dem Erstellen der Tabelle konfigurieren oder einen zuvor konfigurierten Wert ändern. Verwenden Sie in diesem Fall die Anweisung ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Wenn Sie SYSTEM_VERSIONING auf OFF setzen, bleibt der Wert für die Beibehaltungsdauer *nicht* erhalten. Wenn Sie SYSTEM_VERSIONING auf ON setzen, ohne explizit HISTORY_RETENTION_PERIOD anzugeben, führt dies zu einer unbegrenzten Beibehaltungsdauer (INFINITE).

Um den aktuellen Status der Aufbewahrungsrichtlinie zu überprüfen, verwenden Sie die folgende Abfrage, die das Flag für die Aktivierung der temporalen Beibehaltungsdauer auf Datenbankebene mit den Beibehaltungsdauern für einzelne Tabellen verknüpft:

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```


## <a name="how-sql-database-deletes-aged-rows"></a>Wie löscht SQL-Datenbank veraltete Zeilen?

Der Bereinigungsprozess hängt vom Indexlayout der Verlaufstabelle ab. Zu beachten ist, dass *nur Verlaufstabellen mit einem gruppierten Index (B-Struktur oder Columnstore) mit einer unendlichen Aufbewahrungsrichtlinie konfiguriert werden können*. Es wird ein Hintergrundtask erstellt, um veraltete Daten für alle temporalen Tabellen mit begrenzter Beibehaltungsdauer zu bereinigen.
Die Bereinigungslogik für den gruppierten Index für Rowstore (B-Struktur) löscht veraltete Zeilen in kleineren Blöcken (max. 10.000), was die Belastung des Datenbankprotokolls und des E/A-Subsystems minimiert. Zwar verwendet die Bereinigungslogik den benötigten B-Strukturindex, die Löschreihenfolge der Zeilen, deren Alter die Beibehaltungsdauer übersteigt, kann jedoch nicht sicher garantiert werden. *Verwenden Sie in Ihren Anwendungen daher keine Abhängigkeit von der Bereinigungsreihenfolge*.

Der Bereinigungstask für den gruppierten Columnstore entfernt ganze [Zeilengruppen](https://msdn.microsoft.com/library/gg492088.aspx) gleichzeitig (die üblicherweise jeweils 1 Mio. Zeilen enthalten). Dies ist sehr effizient, vor allem dann, wenn mit einer hohen Geschwindigkeit Verlaufsdaten generiert werden.

![Gruppierte Columnstore-Aufbewahrung](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Die hervorragende Datenkomprimierung und eine effiziente Aufbewahrungsbereinigung machen den gruppierten Columnstore-Index zur perfekten Lösung für Szenarios, bei denen Ihre Workload in kürzester Zeit eine große Menge an Verlaufsdaten generiert. Dieses Muster ist typisch für intensive [Transaktionsverarbeitungs-Workloads, die temporale Tabellen verwenden](https://msdn.microsoft.com/library/mt631669.aspx), um die Änderungsnachverfolgung und -überwachung, Trendanalysen oder die Erfassung von IoT-Daten durchzuführen.

## <a name="index-considerations"></a>Überlegungen bezüglich des Index

Beim Bereinigungstask für Tabellen mit gruppiertem Rowstore-Index muss der Index mit der Spalte beginnen, die dem Ende des SYSTEM_TIME-Zeitraums entspricht. Wenn kein solcher Index vorhanden ist, können Sie keine begrenzte Beibehaltungsdauer konfigurieren:

*Meldg. 13765, Ebene 16, Status 1 <br></br> Setting finite retention period failed on system-versioned temporal table 'temporalstagetestdb.dbo.WebsiteUserInfo' because the history table 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' does not contain required clustered index. (Festlegen einer unbegrenzten Beibehaltungsdauer bei der temporalen Tabelle mit Systemversionsverwaltung 'temporalstagetestdb.dbo.WebsiteUserInfo' fehlgeschlagen, da die Verlaufstabelle 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' nicht den erforderlichen gruppierten Index enthält.) Sie könnten nun für die Verlaufstabelle einen gruppierten Columnstore- oder B-Strukturindex erstellen, der mit der Spalte beginnt, die mit dem Ende des SYSTEM_TIME-Zeitraums entspricht.*

Hierbei ist zu beachten, dass die von Azure SQL-Datenbank erstellte Standardverlaufstabelle bereits einen gruppierten Index enthält, der mit der Aufbewahrungsrichtlinie kompatibel ist. Wenn Sie versuchen, diesen Index für eine Tabelle mit begrenzte Beibehaltungsdauer zu entfernen, schlägt der Vorgang mit folgender Fehlermeldung fehl:

*Meldg. 13766, Ebene 16, Status 1 <br></br> Cannot drop the clustered index 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' because it is being used for automatic cleanup of aged data. Consider setting HISTORY_RETENTION_PERIOD to INFINITE on the corresponding system-versioned temporal table if you need to drop this index.* (Der gruppierte Index "WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory" kann nicht entfernt werden, da er für die automatische Bereinigung veralteter Daten verwendet wird. Legen Sie ggf. "HISTORY_RETENTION_PERIOD" für die zugehörige temporale Tabelle mit Systemversionsverwaltung auf "INFINITE" fest, wenn Sie diesen Index entfernen müssen.)

Die Bereinigung des gruppierten Columnstore-Index funktioniert optimal, wenn Zeilen mit Verlaufsdaten in aufsteigender Reihenfolge (geordnet nach der Periodenende-Spalte) eingefügt werden, was immer dann der Fall ist, wenn die Verlaufstabelle ausschließlich mithilfe des Mechanismus SYSTEM_VERSIONING gefüllt wird. Wenn Zeilen in der Verlaufstabelle nicht nach der Periodenende-Spalte sortiert sind (was beim Migrieren vorhandener Verlaufsdaten der Fall sein kann), sollten Sie den gruppierten Columnstore-Index auf Basis des ordnungsgemäß sortierten B-Struktur-Rowstore-Index neu erstellen, um eine optimale Leistung zu erzielen.

Vermeiden Sie eine Neuerstellung des gruppierten Columnstore-Index für die Verlaufstabelle mit begrenzter Beibehaltungsdauer, da hierdurch die Reihenfolge der Zeilengruppen verändert werden kann, die durch die Systemversionsverwaltung automatisch auferlegt wurde. Wenn Sie den gruppierten Columnstore-Index für die Verlaufstabelle neu erstellen müssen, erstellen Sie ihn hierzu auf Basis des kompatiblen B-Strukturindex neu. So behalten Sie die Reihenfolge der Zeilengruppen bei, die für eine regelmäßige Datenbereinigung erforderlich ist. Derselbe Ansatz sollte gewählt werden, wenn Sie eine temporale Tabelle mit einer vorhandenen Verlaufstabelle erstellen, die über einen gruppierten Spaltenindex ohne garantierte Datenreihenfolge verfügt:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Wenn eine begrenzte Beibehaltungsdauer für die Verlaufstabelle mit dem gruppierten Columnstore-Index konfiguriert ist, können Sie keine zusätzlichen ungruppierten B-Strukturindizes für diese Tabelle erstellen:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Der Versuch, die obige Anweisung auszuführen, ist nicht erfolgreich, und es wird folgende Fehlermeldung ausgegeben:

*Msg 13772, Level 16, State 1 <br></br> Cannot create non-clustered index on a temporal history table 'WebsiteUserInfoHistory' since it has finite retention period and clustered columnstore index defined. (Nicht-gruppierter Index kann für temporale Verlaufstabelle 'WebsiteUserInfoHistory' nicht erstellt werden, da sie eine begrenzte Beibehaltungsdauer hat und ein gruppierter Columnstore-Index definiert wurde.)*

## <a name="querying-tables-with-retention-policy"></a>Abfragen von Tabellen mit Aufbewahrungsrichtlinie

Alle Abfragen für die temporale Tabelle filtern automatisch Zeilen mit Verlaufsdaten aus, die der begrenzten Aufbewahrungsrichtlinie entsprechen, um unvorhersehbare und inkonsistente Ergebnisse zu vermeiden, da veraltete Zeilen *jederzeit und in beliebiger Reihenfolge* durch den Bereinigungstask gelöscht werden können.

Das folgende Bild zeigt den Abfrageplan für eine einfache Abfrage:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Der Abfrageplan enthält einen zusätzlichen Filter, der auf die Spalte mit dem Zeitraumende (ValidTo) im Operator „Clustered Index Scan“ für die Verlaufstabelle angewendet wird (siehe Hervorhebung). In diesem Beispiel wird davon ausgegangen, dass in der Tabelle „WebsiteUserInfo“ eine Beibehaltungsdauer von einem MONAT festgelegt wurde.

![Aufbewahrungsabfragefilter](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Wenn Sie allerdings die Verlaufstabelle direkt abfragen, sehen Sie möglicherweise Zeilen, die älter sind als die angegebene Beibehaltungsdauer – allerdings ohne Garantie für wiederholbare Abfrageergebnisse. Das folgende Bild zeigt den Abfrageausführungsplan für die Abfrage der Verlaufstabelle, ohne dass zusätzliche Filter angewandt werden:

![Abfragen des Verlaufs ohne Beibehaltungsfilter](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Basieren Sie Ihre Geschäftslogik nicht auf das Lesen der Verlaufstabelle nach der Beibehaltungsdauer, da Sie inkonsistente oder unerwartete Ergebnisse erhalten könnten. Es empfiehlt sich, temporale Abfragen mit der Klausel „FOR SYSTEM_TIME“ zu verwenden, um Daten in temporalen Tabellen zu analysieren.

## <a name="point-in-time-restore-considerations"></a>Überlegungen zur Point-in-Time-Wiederherstellung

Wenn Sie eine neue Datenbank erstellen, indem Sie eine [vorhandene Datenbank zu einem bestimmten Zeitpunkt wiederherstellen](sql-database-recovery-using-backups.md), ist die temporale Beibehaltungsdauer auf Datenbankebene deaktiviert. (Das Flag **is_temporal_history_retention_enabled** ist auf OFF gesetzt.) Mit dieser Funktion können Sie bei der Wiederherstellung alle Zeilen mit Verlaufsdaten untersuchen, ohne dass veraltete Zeilen entfernt werden, bevor Sie diese abfragen können. Sie können diese Funktion verwenden, um *Verlaufsdaten außerhalb der konfigurierten Beibehaltungsdauer zu überprüfen*.

Ein Beispiel: Für eine temporale Tabelle wurde eine Beibehaltungsdauer von einem MONAT angegeben. Wenn Ihre Datenbank im Premium-Tarif erstellt wurde, könnten Sie eine Datenbankkopie mit dem Status der Datenbank vor 35 Tagen erstellen. So könnten Sie effektiv Zeilen mit Verlaufsdaten analysieren, die bis zu 65 Tage alt sind, indem Sie die Verlaufstabelle direkt abfragen.

Wenn Sie die temporale Aufbewahrungsbereinigung aktivieren möchten, führen Sie die folgende Transact-SQL-Anweisung nach der Point-in-Time-Wiederherstellung aus:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Verwendung temporaler Tabellen in Ihren Anwendungen finden Sie unter [Erste Schritte mit temporalen Tabellen in der Azure SQL-Datenbank](sql-database-temporal-tables.md).

Auf Channel 9 können Sie sich einen [Kundenerfahrungsbericht zur Temporal-Implementierung](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) anhören und eine [Temporal-Live-Demo](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016) ansehen.

Ausführliche Informationen zu temporalen Tabellen finden Sie in der [MSDN-Dokumentation](https://msdn.microsoft.com/library/dn935015.aspx).
