---
title: Automatisieren der Replikation von Schemaänderungen in der SQL-Datensynchronisierung
description: Erfahren Sie, wie Sie die Replikation von Schemaänderungen in der Azure SQL-Datensynchronisierung automatisieren.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/14/2018
ms.openlocfilehash: f9997ea737b96185b31a7f51996a396fb5fc46c4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790184"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatisieren der Replikation von Schemaänderungen in der Azure SQL-Datensynchronisierung
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Mit der SQL-Datensynchronisierung können Benutzer Daten zwischen Datenbanken in Azure SQL-Datenbank und SQL Server-Instanzen in eine Richtung oder in beide Richtungen synchronisieren. Eine der aktuellen Einschränkungen der SQL-Datensynchronisierung ist die fehlende Unterstützung für die Replikation von Schemaänderungen. Jedes Mal, wenn Sie das Tabellenschema ändern, müssen Sie die Änderungen manuell auf alle Endpunkte, einschließlich des Hubs und aller Mitglieder, anwenden und dann das Synchronisierungsschema aktualisieren.

Dieser Artikel stellt eine Lösung zur automatisierten Replikation von Schemaänderungen auf alle Endpunkte der SQL-Datensynchronisierung vor.

1. Diese Lösung nutzt einen DDL-Trigger, um Schemaänderungen nachzuverfolgen.
1. Durch den Trigger werden die Befehle für die Schemaänderung in eine Nachverfolgungstabelle eingefügt.
1. Diese Nachverfolgungstabelle wird über den Datensynchronisierungsdienst mit allen Endpunkten synchronisiert.
1. DML-Trigger nach dem Einfügen werden verwendet, um die Schemaänderungen auf die anderen Endpunkte anzuwenden.

In diesem Artikel wird ALTER TABLE als Beispiel für eine Schemaänderung verwendet, aber diese Lösung funktioniert auch für andere Arten von Schemaänderungen.

> [!IMPORTANT]
> Es wird empfohlen, diesen Artikel sorgfältig zu lesen, insbesondere die Abschnitte über [Problembehandlung](#troubleshoot) und [Weitere Überlegungen](#other), bevor Sie mit der Implementierung der automatisierten Replikation von Schemaänderungen in Ihrer Synchronisierungsumgebung beginnen. Darüber hinaus ist es empfehlenswert, den Artikel [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](sql-data-sync-data-sql-server-sql-database.md) zu lesen. Es besteht die Möglichkeit, dass einige Datenbankvorgänge die in diesem Artikel beschriebene Lösung unterbrechen. Daher sind ggf. zusätzliche Domänenkenntnisse von SQL Server und Transact-SQL erforderlich, um derartige Probleme zu beheben.

![Automatisieren der Replikation von Schemaänderungen](./media/sql-data-sync-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Einrichten der automatisierten Replikation von Schemaänderungen

### <a name="create-a-table-to-track-schema-changes"></a>Erstellen einer Tabelle zum Nachverfolgen von Schemaänderungen

Erstellen Sie eine Tabelle zum Nachverfolgen von Schemaänderungen in allen Datenbanken der Synchronisierungsgruppe:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Diese Tabelle weist eine Identitätsspalte auf, damit Sie die Reihenfolge der Schemaänderungen nachvollziehen können. Fügen Sie bei Bedarf zusätzliche Felder hinzu, um weitere Informationen zu protokollieren.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Erstellen einer Tabelle zum Nachverfolgen des Verlaufs der Schemaänderungen

Erstellen Sie für alle Endpunkte eine Tabelle, um die ID des zuletzt verwendeten Schemaänderungsbefehls zu verfolgen.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Erstellen eines ALTER TABLE-DDL-Triggers in der Datenbank, in der Schemaänderungen vorgenommen werden

Erstellen Sie einen DDL-Trigger für ALTER TABLE-Vorgänge. Sie müssen diesen Trigger nur in der Datenbank erstellen, in der Schemaänderungen vorgenommen werden. Um Konflikte zu vermeiden, lassen Sie nur Schemaänderungen in einer Datenbank einer Synchronisierungsgruppe zu.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

Für jeden ALTER TABLE-Befehl fügt der Trigger einen Datensatz in die Tabelle der Schemaänderungsnachverfolgung ein. In diesem Beispiel wird ein Filter hinzugefügt, um zu vermeiden, dass Schemaänderungen unter dem Schema **DataSync** repliziert werden, da diese höchstwahrscheinlich vom Datensynchronisierungsdienst vorgenommen werden. Fügen Sie weitere Filter hinzu, wenn Sie nur bestimmte Typen von Schemaänderungen replizieren möchten.

Außerdem haben Sie auch die Möglichkeit, weitere Trigger hinzuzufügen, um andere Typen von Schemaänderungen zu replizieren. Erstellen Sie beispielsweise CREATE_PROCEDURE-, ALTER_PROCEDURE- und DROP_PROCEDURE-Trigger, um Änderungen auf gespeicherte Prozeduren zu replizieren.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Erstellen eines Triggers an anderen Endpunkten zum Anwenden von Schemaänderungen während des Einfügens

Dieser Trigger führt den Befehl für die Schemaänderung aus, wenn er mit anderen Endpunkten synchronisiert wird. Sie müssen diesen Trigger an allen Endpunkten erstellen, mit Ausnahme desjenigen, an dem Schemaänderungen vorgenommen werden (d.h. in der Datenbank, in der im vorherigen Schritt der DDL-Trigger `AlterTableDDLTrigger` erstellt wurde).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Dieser Trigger wird nach dem Einfügen ausgelöst und prüft, ob der aktuelle Befehl als Nächstes ausgeführt werden soll. Durch die Codelogik wird sichergestellt, dass keine Schemaänderungsanweisung übersprungen wird, und alle Änderungen werden übernommen, auch wenn die Reihenfolge beim Einfügen falsch ist.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synchronisieren der Verfolgungstabelle für die Schemaänderungen an allen Endpunkten

Sie können die Verfolgungstabelle für die Schemaänderungen an allen Endpunkten synchronisieren, indem Sie die vorhandene oder eine neue Synchronisierungsgruppe verwenden. Stellen Sie sicher, dass die Änderungen in der Nachverfolgungstabelle mit allen Endpunkten synchronisiert werden können, insbesondere wenn Sie die Synchronisierung in eine Richtung in verwenden.

Synchronisieren Sie die Verlaufstabelle der Schemaänderungen nicht, da diese Tabelle an verschiedenen Endpunkten unterschiedliche Zustände beibehält.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Übernehmen der Schemaänderungen in einer Synchronisierungsgruppe

Es werden nur Schemaänderungen repliziert, die in der Datenbank vorgenommen wurden, in der der DDL-Trigger erstellt wird. In anderen Datenbanken vorgenommene Schemaänderungen werden nicht repliziert.

Nachdem die Schemaänderungen an allen Endpunkten repliziert wurden, müssen Sie zusätzliche Schritte unternehmen, um das Synchronisierungsschema zu aktualisieren, sodass die Synchronisierung der neuen Spalten gestartet bzw. beendet wird.

#### <a name="add-new-columns"></a>Hinzufügen von Spalten

1.  Nehmen Sie die Schemaänderung vor.

1.  Vermeiden Sie jede Datenänderung an den neuen Spalten, bis Sie den Schritt zum Erstellen des Triggers abgeschlossen haben.

1.  Warten Sie, bis die Schemaänderungen für alle Endpunkte übernommen wurden.

1.  Aktualisieren Sie das Datenbankschema, und fügen Sie die neue Spalte dem Synchronisierungsschema hinzu.

1.  Die Daten in der neuen Spalte werden beim nächsten Synchronisierungsvorgang synchronisiert.

#### <a name="remove-columns"></a>Entfernen von Spalten

1.  Entfernen Sie die Spalten aus dem Synchronisierungsschema. Die Datensynchronisierung hält die Synchronisierung der Daten in diesen Spalten an.

1.  Nehmen Sie die Schemaänderung vor.

1.  Aktualisieren Sie das Datenbankschema.

#### <a name="update-data-types"></a>Aktualisieren von Datentypen

1.  Nehmen Sie die Schemaänderung vor.

1.  Warten Sie, bis die Schemaänderungen für alle Endpunkte übernommen wurden.

1.  Aktualisieren Sie das Datenbankschema.

1.  Wenn der neue und der alte Datentyp nicht vollständig kompatibel sind, z.B. wenn Sie von `int` zu `bigint` wechseln, tritt möglicherweise ein Synchronisierungsfehler auf, bevor die Schritte zum Erstellen des Triggers abgeschlossen sind. Die Synchronisierung ist bei einem erneuten Versuch erfolgreich.

#### <a name="rename-columns-or-tables"></a>Umbenennen von Spalten oder Tabellen

Durch das Umbenennen von Spalten oder Tabellen wird die Datensynchronisierung angehalten. Erstellen Sie eine neue Tabelle oder Spalte, füllen Sie die Daten ein, und löschen Sie dann die alte Tabelle oder Spalte, anstatt sie umzubenennen.

#### <a name="other-types-of-schema-changes"></a>Andere Typen von Schemaänderungen

Für andere Typen von Schemaänderungen – z.B. das Erstellen von gespeicherten Prozeduren oder das Löschen eines Indexes – ist das Aktualisieren des Synchronisierungsschemas nicht erforderlich.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a> Problembehandlung bei der automatisierten Replikation von Schemaänderungen

Die in diesem Artikel beschriebene Replikationslogik funktioniert in einigen Situationen nicht mehr. Dies ist z.B. der Fall, wenn Sie eine Schemaänderung in einer lokalen Datenbank vorgenommen haben, die in der Azure SQL-Datenbank-Instanz nicht unterstützt wird. Dann schlägt die Synchronisierung der Tabelle der Schemaänderungsnachverfolgung fehl. Dieses Problem muss manuell behoben werden:

1.  Deaktivieren Sie den DDL-Trigger, und vermeiden Sie weitere Schemaänderungen, bis das Problem behoben ist.

1.  Deaktivieren Sie in der Endpunktdatenbank, in der das Problem auftritt, den AFTER INSERT-Trigger an dem Endpunkt, an dem die Schemaänderung nicht vorgenommen werden kann. Durch diese Aktion kann der Schemaänderungsbefehl synchronisiert werden.

1.  Lösen Sie den Synchronisierungsvorgang aus, um die Tabelle der Schemaänderungsnachverfolgung zu synchronisieren.

1.  In der Endpunktdatenbank, in der das Problem auftritt, fragen Sie die Verlaufstabelle für die Schemaänderungen ab, um die ID des zuletzt angewandten Schemaänderungsbefehls zu erhalten.

1.  Fragen Sie die Tabelle der Schemaänderungsnachverfolgung ab, um alle Befehle aufzulisten, deren ID größer ist als der ID-Wert, den Sie im vorherigen Schritt abgerufen haben.

    a.  Ignorieren Sie die Befehle, die in der Endpunktdatenbank nicht ausgeführt werden können. Es ist erforderlich, dass Sie sich mit der Schemainkonsistenz befassen. Nehmen Sie die Änderungen am ursprünglichen Schema zurück, wenn sich die Inkonsistenz auf Ihre Anwendung auswirkt.

    b.  Wenden Sie die Befehle, die angewendet werden sollen, manuell an.

1.  Aktualisieren Sie die Verlaufstabelle für die Schemaänderungen, und setzen Sie die zuletzt verwendete ID auf den richtigen Wert.

1.  Überprüfen Sie nochmals, ob das Schema aktuell ist.

1.  Aktivieren Sie erneut den im zweiten Schritt deaktivierten AFTER INSERT-Trigger.

1.  Aktivieren Sie erneut den im ersten Schritt deaktivierten DDL-Trigger.

Wenn Sie die Datensätze in der Tabelle der Schemaänderungsnachverfolgung bereinigen möchten, verwenden Sie DELETE anstelle von TRUNCATE. Führen Sie niemals ein erneutes Seeding für die Identitätsspalte in der Tabelle der Schemaänderungsnachverfolgung mithilfe von DBCC CHECKIDENT aus. Sie können neue Tabellen der Schemaänderungsnachverfolgung erstellen und den Tabellennamen im DDL-Trigger aktualisieren, wenn ein erneutes Seeding erforderlich ist.

## <a name="other-considerations"></a><a name="other"></a> Weitere Überlegungen

-   Datenbankbenutzer, die die Hub- und Mitgliederdatenbanken konfigurieren, müssen über ausreichende Berechtigungen verfügen, um die Schemaänderungsbefehle auszuführen.

-   Sie können weitere Filter im DDL-Trigger hinzufügen, um Schemaänderungen nur in ausgewählten Tabellen oder Vorgängen zu replizieren.

-   Sie können Schemaänderungen nur in der Datenbank vornehmen, in der der DDL-Trigger erste wird.

-   Wenn Sie eine Änderung in einer SQL Server-Datenbank vornehmen, stellen Sie sicher, dass die Schemaänderung in Azure SQL-Datenbank unterstützt wird.

-   Wenn Schemaänderungen in anderen Datenbanken als der Datenbank vorgenommen werden, in der der DDL-Trigger erstellt wird, werden die Änderungen nicht repliziert. Zur Vermeidung dieses Problems können Sie DDL-Trigger erstellen, um Änderungen an anderen Endpunkten zu blockieren.

-   Wenn Sie das Schema der Tabelle der Schemaänderungsnachverfolgung ändern müssen, deaktivieren Sie den DDL-Trigger, bevor Sie die Änderung vornehmen, und wenden Sie die Änderung dann manuell auf alle Endpunkte an. Das Aktualisieren des Schemas in einem AFTER INSERT-Trigger für die gleiche Tabelle funktioniert nicht.

-   Führen Sie kein erneutes Seeding der Identitätsspalte mit DBCC CHECKIDENT durch.

-   Verwenden Sie TRUNCATE nicht, um Daten in der Tabelle der Schemaänderungsnachverfolgung zu bereinigen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur SQL-Datensynchronisierung finden Sie unter:

-   Übersicht: [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](sql-data-sync-data-sql-server-sql-database.md)
-   Einrichten der Datensynchronisierung
    - Im Portal – [Tutorial: Einrichten der SQL-Datensynchronisierung zum Synchronisieren von Daten zwischen Azure SQL-Datenbank und SQL Server](sql-data-sync-sql-server-configure.md)
    - Mit PowerShell
        -  [Verwenden von PowerShell zum Synchronisieren zwischen mehreren Datenbanken in Azure SQL-Datenbank](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Verwenden von PowerShell zum Synchronisieren zwischen einer Datenbank in Azure SQL-Datenbank und einer Datenbank in einer SQL Server-Instanz](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Datensynchronisierungs-Agent: [Datensynchronisierungs-Agent für die Azure SQL-Datensynchronisierung](sql-data-sync-agent-overview.md)
-   Bewährte Methoden: [Bewährte Methoden für die Azure SQL-Datensynchronisierung](sql-data-sync-best-practices.md)
-   Überwachung: [Überwachen der SQL-Datensynchronisierung mit Azure Monitor-Protokollen](./monitor-tune-overview.md)
-   Problembehandlung: [Behandeln von Problemen mit der Azure SQL-Datensynchronisierung]()
-   Aktualisieren des Synchronisierungsschemas
    -   Mit PowerShell: [Verwenden von PowerShell zum Aktualisieren des Synchronisierungsschemas in einer bestehenden Synchronisierungsgruppe](scripts/update-sync-schema-in-sync-group.md)