---
title: Verarbeiten zeitintensiver gespeicherter Prozeduren im SQL-Connector
description: Verarbeiten gespeicherter Prozeduren, bei denen bei Verwendung des SQL-Connectors in Azure Logic Apps ein Timeout auftritt
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102690"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Behandeln von Timeouts bei gespeicherten Prozeduren im SQL-Connector für Azure Logic Apps

Wenn Ihre Logik-App mit derart großen Resultsets arbeitet, dass der [SQL-Connector](../connectors/connectors-create-api-sqlazure.md) nicht alle Ergebnisse gleichzeitig zurückgibt, oder wenn Sie mehr Kontrolle über die Größe und Struktur Ihrer Resultsets wünschen, können Sie eine [gespeicherte Prozedur](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) erstellen, die die Ergebnisse nach Ihren Wünschen organisiert. Der SQL-Connector bietet viele Back-End-Funktionen, auf die Sie über [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zugreifen können, sodass Sie Geschäftsaufgaben, die mit SQL-Datenbanktabellen arbeiten, einfacher automatisieren können.

Beispiel: Beim Abrufen oder Einfügen mehrerer Zeilen kann Ihre Logik-App diese Zeilen mithilfe einer [**Until** -Schleife](../logic-apps/logic-apps-control-flow-loops.md#until-loop) innerhalb dieser [Grenzwerte](../logic-apps/logic-apps-limits-and-config.md) durchlaufen. Wenn Ihre Logik-App jedoch mit Tausenden oder Millionen von Zeilen arbeiten muss, sollten Sie die Kosten aufgrund von Aufrufen der Datenbank minimieren. Weitere Informationen finden Sie unter [Verarbeiten von Massendaten mit dem SQL-Connector](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data).

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>Zeitlimit bei Ausführung gespeicherter Prozeduren

Beim SQL-Connector gilt für gespeicherte Prozeduren ein Zeitlimit [unter 2 Minuten](/connectors/sql/#known-issues-and-limitations). Einige gespeicherte Prozeduren benötigen möglicherweise mehr Zeit, was zu einem `504 Timeout`-Fehler führt. Mitunter sind zu diesem Zweck einige zeitintensive Prozesse explizit als gespeicherte Prozeduren programmiert. Wenn Sie diese Prozeduren aus Azure Logic Apps aufrufen, können aufgrund dieses Zeitlimits Probleme entstehen. Obwohl der SQL-Connector einen asynchronen Modus nicht nativ unterstützt, können Sie dieses Problem umgehen und diesen Modus simulieren, indem Sie einen SQL-Abschlussauslöser, eine native SQL-Passthrough-Abfrage, eine Zustandstabelle und serverseitige Aufträge verwenden. Für diese Aufgabe können Sie den [Azure-Agent für elastische Aufträge](../azure-sql/database/elastic-jobs-overview.md) für [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md) verwenden. Für [lokales SQL Server](/sql/sql-server/sql-server-technical-documentation) und [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) können Sie den [SQL Server-Agent](/sql/ssms/agent/sql-server-agent) nutzen.

Angenommen, Sie haben die folgende zeitintensive gespeicherte Prozedur, deren Ausführungsdauer das Zeitlimit überschreitet. Wenn Sie diese gespeicherte Prozedur in einer Logik-App unter Verwendung des SQL-Connectors ausführen, erhalten Sie als Ergebnis den Fehler `HTTP 504 Gateway Timeout`.

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

Anstatt die gespeicherte Prozedur direkt aufzurufen, können Sie sie asynchron im Hintergrund ausführen, indem Sie einen *Auftrags-Agent* verwenden. Sie können die Ein- und Ausgaben in einer Zustandstabelle speichern, mit der Sie dann über Ihre Logik-App interagieren können. Wenn Sie die Ein- und Ausgaben nicht benötigen oder die Ergebnisse bereits in der gespeicherten Prozedur in eine Tabelle schreiben, können Sie diesen Ansatz vereinfachen.

> [!IMPORTANT]
> Stellen Sie sicher, dass Ihre gespeicherte Prozedur und alle Aufträge *idempotent* sind, was bedeutet, dass sie mehrmals ausgeführt werden können, ohne die Ergebnisse zu beeinflussen. Wenn die asynchrone Verarbeitung fehlschlägt oder ein Timeout eintritt, kann der Auftrags-Agent den Schritt und damit Ihre gespeicherte Prozedur mehrmals wiederholen. Um eine Duplizierung der Ausgabe zu vermeiden, sollten Sie, ehe Sie Objekte erstellen, diese [ bewährten Methoden und Ansätze](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts) noch einmal prüfen.

Im nächsten Abschnitt wird beschrieben, wie Sie den Azure-Agent für elastische Aufträge für Azure SQL-Datenbank verwenden können. Für SQL Server und Azure SQL Managed Instance können Sie den SQL Server-Agent nutzen. Einige Verwaltungsdetails unterscheiden sich, aber die grundlegenden Schritte bleiben die gleichen wie beim Einrichten eines Auftrags-Agents für Azure SQL-Datenbank.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Auftrags-Agent für Azure SQL-Datenbank

Verwenden Sie zum Erstellen eines Auftrags, der die gespeicherte Prozedur für [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md) ausführen kann, den [Azure-Agent für elastische Aufträge](../azure-sql/database/elastic-jobs-overview.md). Erstellen Sie Ihren Auftrags-Agent im Azure-Portal. Bei diesem Ansatz werden mehrere gespeicherte Prozeduren zur Datenbank hinzugefügt, die vom Agent verwendet und auch als *Agent-Datenbank* bezeichnet wird. Sie können dann einen Auftrag erstellen, der Ihre gespeicherte Prozedur in der Zieldatenbank ausführt und am Ende die Ausgabe erfasst.

Bevor Sie den Auftrag erstellen können, müssen Sie Berechtigungen, Gruppen und Ziele einrichten, wie in der [vollständigen Dokumentation für den Azure-Agent für elastische Aufträge](../azure-sql/database/elastic-jobs-overview.md) beschrieben. Sie müssen auch, wie in den folgenden Abschnitten beschrieben, eine unterstützende Tabelle in der Zieldatenbank erstellen.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>Erstellen einer Zustandstabelle zum Registrieren von Parametern und Speichern von Eingaben

SQL-Agent-Aufträge lassen keine Eingabeparameter zu. Erstellen Sie stattdessen in der Zieldatenbank eine Zustandstabelle, in der Sie die Parameter registrieren und Eingaben speichern, die für das Aufrufen Ihrer gespeicherten Prozeduren verwendet werden sollen. Alle Schritte des Agent-Auftrags erfolgen in der Zieldatenbank, aber die gespeicherten Prozeduren des Auftrags werden in der Agent-Datenbank ausgeführt. 

Erstellen Sie die Zustandstabelle mithilfe dieses Schemas:

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

So sieht die resultierende Tabelle in [SQL Server Management Studio (SMSS)](/sql/ssms/download-sql-server-management-studio-ssms) aus:

![Screenshot der erstellten Zustandstabelle, in der Eingaben für die gespeicherte Prozedur gespeichert werden.](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

Um eine gute Leistung zu gewährleisten und sicherzustellen, dass der Agent-Auftrag den zugehörigen Datensatz finden kann, verwendet die Tabelle die Auftragsausführungs-ID (`jobid`) als Primärschlüssel. Wenn Sie möchten, können Sie auch für die Eingabeparameter einzelne Spalten hinzufügen. Das zuvor beschriebene Schema kommt im Allgemeinen mit mehreren Parametern zurecht, ist jedoch auf die von `NVARCHAR(MAX)` berechnete Größe beschränkt.

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>Erstellen eines Auftrags auf oberster Ebene zum Ausführen der gespeicherten Prozedur

Um die zeitintensive gespeicherte Prozedur auszuführen, erstellen Sie in der Agent-Datenbank diesen Auftrags-Agent auf oberster Ebene:

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

Fügen Sie nun Schritte zum Auftrag hinzu, die die gespeicherte Prozedur parametrisieren, ausführen und abschließen. Standardmäßig tritt für einen Auftragsschritt nach 12 Stunden ein Timeout ein. Wenn Ihre gespeicherte Prozedur mehr Zeit benötigt oder Sie möchten, dass das Timeout der Prozedur früher erfolgt, können Sie den Parameter `step_timeout_seconds` in einen anderen Wert ändern, der in Sekunden angegeben wird. Standardmäßig hat ein Schritt zehn integrierte Wiederholungsversuche mit einem Backoff-Timeout zwischen den einzelnen Wiederholungsversuchen, das Sie zu Ihrem Vorteil nutzen können.

Es folgen die Schritte, die Sie hinzufügen müssen:

1. Warten Sie, bis die Parameter in der Tabelle `LongRunningState` angezeigt werden.

   Dieser erste Schritt wartet darauf, dass die Parameter zur Tabelle `LongRunningState` hinzugefügt werden, was kurz nach Start des Auftrags geschieht. Wenn die Auftragsausführungs-ID (`jobid`) nicht zur Tabelle `LongRunningState` hinzugefügt wird, schlägt der Schritt lediglich fehl, und das standardmäßige Wiederholungs- oder Backoff-Timeout erledigt das Warten:

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Fragen Sie die Parameter aus der Zustandstabelle ab, und übergeben Sie sie an die gespeicherte Prozedur. Auch bei diesem Schritt wird die Prozedur im Hintergrund ausgeführt. 

   Wenn Ihre gespeicherte Prozedur keine Parameter benötigt, rufen Sie die gespeicherte Prozedur einfach direkt auf. Andernfalls verwenden Sie zur Übergabe des Parameters `@timespan` den Parameter `@callparams`, den Sie auch zur Übergabe zusätzlicher Parameter erweitern können.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Schließen Sie den Auftrag ab, und zeichnen Sie die Ergebnisse auf.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>Starten des Auftrags und Übergeben der Parameter

Um den Auftrag zu starten, verwenden Sie eine native Passthrough-Abfrage mit der Aktion [**SQL-Abfrage ausführen**](/connectors/sql/#execute-a-sql-query-(v2)), und übertragen Sie die Parameter des Auftrags sofort in die Zustandstabelle. Um Eingaben für das Attribut `jobid` in der Zieltabelle bereitzustellen, fügt Logic Apps eine **For Each** -Schleife hinzu, die die Tabellenausgabe der vorhergehenden Aktion durchläuft. Führen Sie für jede Auftragsausführungs-ID die Aktion **Zeile einfügen** aus, die die dynamische Datenausgabe`ResultSets JobExecutionId` verwendet, um die Parameter für den Auftrag hinzuzufügen, mit denen er entpackt und an die gespeicherte Prozedur übergeben werden soll.

![Screenshot mit Aktionen, die zum Starten des Auftrags und Übergeben von Parametern an die gespeicherte Prozedur zu verwenden sind.](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

Wenn der Auftrag abgeschlossen ist, aktualisiert der Auftrag die Tabelle `LongRunningState`, sodass Sie mithilfe des [Triggers **Beim Ändern eines Elements**](/connectors/sql/#when-an-item-is-modified-(v2)) einfach das Auslösen entsprechend dem Ergebnis vornehmen können. Wenn Sie die Ausgabe nicht benötigen oder bereits einen Trigger haben, der eine Ausgabetabelle überwacht, können Sie diesen Teil überspringen.

![Screenshot des SQL-Triggers bei Änderung eines Elements.](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>Auftrags-Agent für SQL Server oder Azure SQL Managed Instance

Im selben Szenario können Sie den [SQL Server-Agent ](/sql/ssms/agent/sql-server-agent) für [lokales SQL Server](/sql/sql-server/sql-server-technical-documentation) und [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) nutzen. Obwohl sich einige Verwaltungsdetails unterscheiden, bleiben die grundlegenden Schritte die gleichen wie beim Einrichten eines Auftrags-Agents für Azure SQL-Datenbank.

## <a name="next-steps"></a>Nächste Schritte

[Herstellen einer Verbindung mit SQL Server, Azure SQL-Datenbank oder Azure SQL Managed Instance](../connectors/connectors-create-api-sqlazure.md)

