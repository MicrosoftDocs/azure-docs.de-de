---
title: Code für XEvent-Ringpuffer
description: Zeigt ein Transact-SQL-Codebeispiel (für Azure SQL-Datenbank), das durch die Verwendung des Ringpufferziels eine einfache und schnelle Methode zum Erfassen und Ausgeben von Daten bietet.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: a646588616b874e40b1ed2a5a0b5e691b075075d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487302"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>Code des Ringpufferziels für erweiterte Ereignisse in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Sie suchen ein vollständiges Codebeispiel für die einfachste schnelle Möglichkeit zum Erfassen und Melden von Informationen für erweiterte Ereignisse während eines Tests. Das einfachste Ziel für erweiterte Ereignisdaten ist das [Ringpufferziel](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)).

In diesem Thema finden Sie ein Transact-SQL-Codebeispiel, mit dem folgende Aufgaben ausgeführt werden:

1. Erstellen einer Tabelle mit Daten zur Veranschaulichung.
2. Erstellen einer Sitzung für ein vorhandenes erweitertes Ereignis (**sqlserver.sql_statement_starting**).

   * Das Ereignis ist auf SQL-Anweisungen beschränkt, die eine bestimmte Update-Zeichenfolge enthalten: **statement LIKE '%UPDATE tabEmployee%'** .
   * Senden der Ausgabe des Ereignisses an ein Ziel vom Typ Ringpuffer (**package0.ring_buffer**).
3. Starten der Ereignissitzung.
4. Ausgeben einer Reihe einfacher SQL UPDATE-Anweisungen.
5. Ausgeben einer SQL SELECT-Anweisung, um die Ereignisausgabe vom Ringpuffer abzurufen.

   * **sys.dm_xe_database_session_targets** und andere dynamische Verwaltungssichten (DMVs) werden verknüpft.
6. Anhalten der Ereignissitzung.
7. Verwerfen des Ringpufferziels, um die zugehörigen Ressourcen freizugeben.
8. Verwerfen der Ereignissitzung und der Demotabelle.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sie können sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/)registrieren.
* Jede Datenbank, in der eine Tabelle erstellt werden kann.
  
  * Optional können Sie in wenigen Minuten [eine **AdventureWorksLT**-Demodatenbank erstellen](single-database-create-quickstart.md).
* SQL Server Management Studio („ssms.exe“), im Idealfall die aktuelle monatliche Updateversion.
  Sie können "ssms.exe" in der neuesten Version wie folgt herunterladen:
  
  * Im Thema [Herunterladen von SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
  * [Über diesen direkten Link zum Herunterladen.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Codebeispiel

Das folgende Codebeispiel für einen Ringpuffer kann mit kleineren Änderungen sowohl für Azure SQL-Datenbank als auch für Microsoft SQL Server ausgeführt werden. Der Unterschied besteht im Vorhandensein des Knotens „_database“ im Namen einiger dynamischer Verwaltungssichten (DMVs) in der FROM-Klausel in Schritt 5. Beispiel:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>Ringpufferinhalte

Für die Ausführung des Codebeispiels wurde `ssms.exe` verwendet.

Um die Ergebnisse anzuzeigen, haben wir auf die Zelle unterhalb der Spaltenüberschrift **target_data_XML** geklickt.

Anschließend haben wir im Ergebnisbereich auf die Zelle unterhalb der Spaltenüberschrift **target_data_XML** geklickt. Mit diesem Klicken wurde in „ssms.exe“ eine weitere Dateiregisterkarte erstellt, auf welcher der Inhalt der Ergebniszelle angezeigt wurde (als XML).

Der folgende Block zeigt die Ausgabe. Wenngleich die Ausgabe lang erscheint, umfasst sie lediglich zwei **\<event>** -Elemente.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>Freigeben der von Ihrem Ringpuffer verwendeten Ressourcen

Wenn Sie mit der Verwendung Ihres Ringpuffers fertig sind, können Sie ihn entfernen und seine Ressourcen freigeben. Verwenden Sie dazu wie nachfolgend gezeigt einen **ALTER**-Befehl:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

Die Definition Ihrer Ereignissitzung wird aktualisiert, jedoch nicht verworfen. Sie können zu einem späteren Zeitpunkt eine weitere Instanz des Ringpuffers zu Ihrer Ereignissitzung hinzufügen.

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>Weitere Informationen

Hauptthemen für erweiterte Ereignisse in Azure SQL-Datenbank:

* [Überlegungen zu erweiterten Ereignissen in Azure SQL-Datenbank:](xevent-db-diff-from-svr.md) In diesem Thema werden verschiedene Aspekte im Zusammenhang mit erweiterten Ereignissen gegenübergestellt, die sich bei Azure SQL-Datenbank und Microsoft SQL Server unterscheiden.

Weitere Themen mit Codebeispielen für erweiterte Ereignisse finden Sie unter den folgenden Links. Allerdings müssen Sie routinemäßig alle Beispiele dahingehend prüfen, ob das Beispiel für Microsoft SQL Server oder Azure SQL-Datenbank gilt. Dann können Sie entscheiden, ob kleinere Änderungen erforderlich sind, um das Beispiel auszuführen.

* Codebeispiel für Azure SQL-Datenbank: [Code des Ziels „Ereignisdatei“ für erweiterte Ereignisse in Azure SQL-Datenbank](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->