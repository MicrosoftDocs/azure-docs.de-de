---
title: Arbeiten mit vorübergehenden Fehlern
description: Erfahren Sie, wie Sie einen SQL-Verbindungsfehler oder vorübergehenden Fehler beim Herstellen von Verbindungen mit Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics behandeln, diagnostizieren und verhindern.
keywords: SQL-Verbindung,Verbindungszeichenfolge,Verbindungsprobleme,vorübergehender Fehler,Verbindungsfehler
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: dalechen
ms.author: ninarn
ms.reviewer: sstein, vanto
ms.date: 01/14/2020
ms.openlocfilehash: f8c94e36a1a6d1f675e9d6a7dde456dbf6eb8897
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791357"
---
# <a name="troubleshoot-transient-connection-errors-in-sql-database-and-sql-managed-instance"></a>Behandeln vorübergehender Verbindungsfehler in SQL-Datenbank und SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

In diesem Artikel wird beschrieben, wie Sie Verbindungsfehler und vorübergehende Fehler verhindern, behandeln, diagnostizieren und beheben, die bei Ihrer Clientanwendung während der Interaktion mit Azure SQL-Datenbank, Azure SQL Managed Instance und Azure Synapse Analytics auftreten. Erfahren Sie, wie Sie die Wiederholungslogik konfigurieren, die Verbindungszeichenfolge erstellen und andere Verbindungseinstellungen anpassen.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Vorübergehender Fehler

Bei einem vorübergehenden Fehler liegt ein Problem zugrunde, das sich nach kurzer Zeit von selbst löst. Wenn das Azure-System Hardwareressourcen für einen besseren Lastenausgleich bei verschiedenen Workloads rasch verschiebt, treten gelegentlich vorübergehende Fehler auf. Die meisten dieser Neukonfigurationsereignisse dauern weniger als 60 Sekunden. Während der Neukonfiguration kann es beim Herstellen von Verbindungen mit der Datenbank in SQL-Datenbank zu Problemen kommen. Anwendungen, die eine Verbindung mit der Datenbank herstellen, sollten dafür ausgelegt sein, vorübergehende Fehler zu tolerieren. Behandeln Sie diese Fehler, indem Sie Wiederholungslogik im Code implementieren und vermeiden, dass sie Benutzern als Anwendungsfehler gemeldet werden.

Wenn Ihr Clientprogramm ADO.NET verwendet, wird eine **SqlException** -Ausnahme ausgelöst, um das Programm über den vorübergehenden Fehler zu informieren.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Vorübergehende Fehler beim Herstellen einer Verbindung und bei Befehlen

Sie können je nach Situation versuchen, die Verbindung mit SQL-Datenbank und SQL Managed Instance wiederzuverwenden oder erneut herzustellen:

- **Beim Versuch, eine Verbindung herzustellen, tritt ein vorübergehender Fehler auf.**

Wiederholen Sie den Verbindungsversuch nach einigen Sekunden.

- **Während eines SQL-Datenbank- und SQL Managed Instance-Abfragebefehls tritt ein vorübergehender Fehler auf.**

Versuchen Sie nicht sofort, den Befehl erneut auszuführen. Stellen Sie stattdessen nach einiger Zeit eine neue Verbindung her, und führen Sie den Befehl erneut aus.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Wiederholungslogik für vorübergehende Fehler

Clientprogramme, bei denen gelegentlich ein vorübergehender Fehler auftritt, sind stabiler, wenn sie Wiederholungslogik enthalten. Wenn Ihr Programm über Middleware eines Drittanbieters mit der Datenbank in SQL-Datenbank kommuniziert, erkundigen Sie sich beim Anbieter, ob die Middleware Wiederholungslogik für vorübergehende Fehler enthält.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Prinzipien für Wiederholungsversuche

- Falls ein vorübergehender Fehler auftritt, wiederholen Sie den Verbindungsversuch.
- Wenn bei einer `SELECT`-Anweisung in SQL-Datenbank oder SQL Managed Instance ein vorübergehender Fehler auftritt, sollte diese nicht sofort wiederholt werden. Stellen Sie stattdessen eine neue Verbindung her, und führen Sie dann die `SELECT`-Anweisung erneut aus.
- Wenn bei einer `UPDATE`-Anweisung in SQL-Datenbank oder SQL Managed Instance ein vorübergehender Fehler auftritt, stellen Sie eine neue Verbindung her, bevor Sie die UPDATE-Anweisung wiederholen. Die Wiederholungslogik muss sicherstellen, dass entweder die gesamte Datenbanktransaktion vollständig ausgeführt wurde oder dass für die gesamte Transaktion ein Rollback ausgeführt wird.

### <a name="other-considerations-for-retry"></a>Weitere Überlegungen für Wiederholungsversuche

- Bei einem Batchprogramm, das nach Ende der Geschäftszeiten automatisch gestartet und vor Beginn des nächsten Tags beendet wird, sind lange Intervalle zwischen den Wiederholungsversuchen unproblematisch.
- Bei Benutzeroberflächenprogrammen sollte jedoch berücksichtigt werden, dass die Benutzer bei zu langen Wartezeiten dazu tendieren, das Programm zu beenden. Wiederholungen nach wenigen Sekunden sollten vermieden werden, da das System durch zu viele Anforderungen überlastet werden kann.

### <a name="interval-increase-between-retries"></a>Steigerung der Intervalle zwischen Wiederholungsversuchen

Es wird empfohlen, vor dem ersten Wiederholungsversuch fünf Sekunden zu warten. Wiederholungsversuche nach weniger als fünf Sekunden können den Clouddienst überfordern. Für jeden nachfolgenden Wiederholungsversuch sollte die Verzögerung exponentiell steigen, bis zu einem Maximum von 60 Sekunden.

Die Sperrfrist für Clients, die ADO.NET verwenden, wird unter [Verbindungspooling (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling) erörtert.

Darüber hinaus kann es sinnvoll sein, eine maximale Anzahl von Wiederholungsversuchen festzulegen, bevor das Programm beendet wird.

### <a name="code-samples-with-retry-logic"></a>Codebeispiele mit Wiederholungslogik

Codebeispiele mit Wiederholungslogik finden Sie unter:

- [Herstellen resilienter Azure SQL-Verbindungen mit ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Herstellen resilienter Azure SQL-Verbindungen mit PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testen der Wiederholungslogik

Um Ihre Wiederholungslogik zu testen, müssen Sie einen Fehler simulieren oder verursachen, der behandelt werden kann, während Ihr Programm weiterhin ausgeführt wird.

#### <a name="test-by-disconnecting-from-the-network"></a>Testen der Logik, indem der Computer vom Netzwerk getrennt wird

Eine Möglichkeit, um Ihre Wiederholungslogik zu testen, besteht darin, Ihren Clientcomputer vom Netzwerk zu trennen, während das Programm ausgeführt wird. Der Fehler lautet:

- **SqlException.Number** = 11001
- Meldung: „Es ist kein solcher Host bekannt.“

Im Rahmen des ersten Wiederholungsversuchs können Sie Ihren Clientcomputer wieder mit dem Netzwerk verbinden und dann versuchen, eine Verbindung herzustellen.

Um diesen Test in der Praxis umzusetzen, trennen Sie Ihren Computer vom Netzwerk, bevor Sie das Programm starten. Als Folge erkennt Ihr Programm einen Laufzeitparameter, der folgende Auswirkungen hat:

- 11001 wird zeitweilig zur Liste der Fehler hinzugefügt, die als vorübergehend betrachtet werden.
- Das Programm führt den ersten Verbindungsversuch durch.
- Nachdem der Fehler ermittelt wurde, wird 11001 aus der Liste entfernt.
- Eine Meldung fordert den Benutzer auf, den Computer an das Netzwerk anzuschließen.
- Die weitere Ausführung wird angehalten (über die **Console.ReadLine** -Methode oder über ein Dialogfeld mit der Schaltfläche „OK“). Nachdem der Computer mit dem Netzwerk verbunden wurde, drückt der Benutzer die EINGABETASTE.
- Es wird erneut versucht, eine Verbindung herzustellen (dieser Versuch sollte erfolgreich sein).

#### <a name="test-by-misspelling-the-user-name-when-connecting"></a>Testen der Logik, indem beim Herstellen der Verbindung ein falscher Benutzername eingegeben wird

Ihr Programm kann vor dem ersten Verbindungsversuch mit Absicht einen falschen Benutzernamen verwenden. Der Fehler lautet:

- **SqlException.Number** = 18456
- Meldung: „Fehler bei der Anmeldung für den Benutzer 'WRONG_MyUserName'.“

Beim ersten Wiederholungsversuch kann Ihr Programm den Namen korrigieren und erneut versuchen, eine Verbindung herzustellen.

Um diesen Test in der Praxis umzusetzen, erkennt Ihr Programm einen Laufzeitparameter, der folgende Auswirkungen hat:

- 18456 wird zeitweilig zur Liste der Fehler hinzugefügt, die als vorübergehend betrachtet werden.
- Der Benutzername wird mit Absicht um „WRONG_“ ergänzt.
- Nachdem der Fehler ermittelt wurde, wird 18456 aus der Liste entfernt.
- „WRONG_“ wird vom Benutzernamen entfernt.
- Es wird erneut versucht, eine Verbindung herzustellen (dieser Versuch sollte erfolgreich sein).

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>SqlConnection-Parameter von .NET für wiederholte Verbindungsversuche

Wenn Ihr Clientprogramm mithilfe der .NET Framework-Klasse **System.Data.SqlClient.SqlConnection** eine Verbindung mit der Datenbank in SQL-Datenbank herstellt, sollten Sie .NET 4.6.1 oder höher (oder .NET Core) verwenden, damit Sie das Feature für wiederholte Verbindungsversuche nutzen können. Weitere Informationen zu dem Feature finden Sie unter [SqlConnection.ConnectionString-Eigenschaft](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring?view=netframework-4.8&preserve-view=true).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Beim Erstellen der [Verbindungszeichenfolge](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring) für Ihr **SqlConnection** -Objekt sollten Sie die Werte der folgenden Parameter abstimmen:

- **ConnectRetryCount** :&nbsp;&nbsp;Standardwert 1 im Bereich von 0 bis 255
- **ConnectRetryInterval** :&nbsp;&nbsp;Standardwert 10 Sekunden im Bereich von 1 bis 60
- **Verbindungstimeout** :&nbsp;&nbsp;Standardwert 15 Sekunden im Bereich von 0 bis 2147483647

Insbesondere sollte für Ihre ausgewählten Werte die folgende Gleichung gelten: Verbindungstimeout = ConnectRetryCount * ConnectionRetryInterval

Beispiel: Wenn die Anzahl 3 ist und das Intervall 10 Sekunden beträgt, hätte das System bei einem Timeout von nur 29 Sekunden nicht genügend Zeit für den dritten und letzten Verbindungsversuch: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Vorübergehende Fehler beim Herstellen einer Verbindung und bei Befehlen

Mit den Parametern **ConnectRetryCount** und **ConnectRetryInterval** kann Ihr **SqlConnection** -Objekt den Verbindungsversuch wiederholen, ohne Ihr Programm zu unterbrechen, sodass das Programm die Steuerung behält. Die Wiederholungen können in folgenden Situationen auftreten:

- SqlConnection.Open-Methodenaufruf
- SqlConnection.Execute-Methodenaufruf

Es gibt eine Besonderheit. Wenn ein vorübergehender Fehler auftritt, während Ihre *Abfrage* ausgeführt wird, wiederholt das **SqlConnection** -Objekt weder den Verbindungsversuch noch Ihre Abfrage. Allerdings überprüft **SqlConnection** sehr schnell die Verbindung, bevor die Abfrage für die Ausführung gesendet wird. Wenn bei der schnellen Überprüfung ein Verbindungsproblem festgestellt wird, wiederholt **SqlConnection** den Verbindungsvorgang. Ist die Wiederholung erfolgreich, wird die Abfrage zur Ausführung gesendet.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Sollte „ConnectRetryCount“ mit der Wiederholungslogik der Anwendung kombiniert werden?

Angenommen, Ihre Anwendung verfügt über eine zuverlässige benutzerdefinierte Wiederholungslogik. Sie könnte den Verbindungsversuch viermal wiederholen. Wenn Sie **ConnectRetryInterval** und **ConnectRetryCount** = 3 zur Verbindungszeichenfolge hinzufügen, erhöhen Sie die Anzahl der Wiederholungsversuche auf 4 * 3 = 12 Wiederholungen. Möglicherweise ist eine so hohe Anzahl von Wiederholungsversuchen nicht erwünscht.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-your-database-in-sql-database"></a>Verbindungen mit Ihrer Datenbank in SQL-Datenbank

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Verbindung: Verbindungszeichenfolge

Die Zeichenfolge für Verbindungen mit der Datenbank unterscheidet sich etwas von der Zeichenfolge, über die Verbindungen mit SQL Server hergestellt werden. Sie können die Verbindungszeichenfolge für Ihre Datenbank aus dem [Azure-Portal](https://portal.azure.com/) kopieren.

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Verbindung: IP-Adresse

SQL-Datenbank muss so konfiguriert werden, dass Verbindungen von der IP-Adresse des Computers akzeptiert werden, auf dem Ihr Clientprogramm gehostet wird. Um diese Konfiguration vorzunehmen, bearbeiten Sie die Firewalleinstellungen über das [Azure-Portal](https://portal.azure.com/).

Wenn Sie die IP-Adresse nicht konfigurieren, tritt bei Ihrem Programm ein Fehler auf, und in einer Fehlermeldung wird die erforderliche IP-Adresse angezeigt.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../../includes/sql-database-include-ip-address-22-v12portal.md)]

Weitere Informationen finden Sie unter [Konfigurieren von Firewalleinstellungen in SQL-Datenbank](firewall-configure.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Verbindung: Ports

Normalerweise muss auf dem Computer, auf dem Ihr Clientprogramm gehostet wird, sichergestellt werden, dass lediglich Port 1433 für die ausgehende Kommunikation geöffnet ist.

Wenn Ihr Clientprogramm z. B. auf einem Windows-Computer gehostet wird, kann Port 1433 über die Windows-Firewall auf dem Host geöffnet werden.

1. Öffnen Sie die Systemsteuerung.
2. Wählen Sie **Alle Systemsteuerungselemente** > **Windows-Firewall** > **Erweiterte Einstellungen** > **Ausgehende Regeln** > **Aktionen** > **Neue Regel** aus.

Wenn Ihr Clientprogramm auf einem virtuellen Azure-Computer gehostet wird, lesen Sie [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](adonet-v12-develop-direct-route-ports.md).

Hintergrundinformationen zur Konfiguration von Ports und IP-Adressen in Ihrer Datenbank finden Sie in den Erläuterungen zur [Firewall für Azure SQL-Datenbank](firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Verbindung: ADO.NET 4.6.2 oder höher

Wenn Ihr Programm ADO.NET-Klassen wie **System.Data.SqlClient.SqlConnection** für Verbindungen mit SQL-Datenbank verwendet, sollten Sie .NET Framework, Version 4.6.2 oder höher, verwenden.

#### <a name="starting-with-adonet-462"></a>Ab ADO.NET 4.6.2

- Der Versuch zum Öffnen der Verbindung sollte für Azure SQL sofort wiederholt werden, wodurch die Leistung cloudfähiger Apps verbessert wird.

#### <a name="starting-with-adonet-461"></a>Ab ADO.NET 4.6.1

- Die Zuverlässigkeit von SQL-Datenbank lässt sich verbessern, wenn Sie eine Verbindung mit der **SqlConnection.Open** -Methode öffnen. Die **Open** -Methode umfasst jetzt bestmögliche Wiederholungsmechanismen, die bei bestimmten vorübergehenden Fehlern innerhalb des Verbindungstimeouts ausgeführt werden.
- Das Verbindungspooling wird unterstützt und umfasst einen effizienten Mechanismus, der überprüft, ob das für Ihr Programm bereitgestellte Verbindungsobjekt funktionsfähig ist.

Bei Verwendung eines Verbindungsobjekts aus einem Verbindungspool sollte Ihr Programm die Verbindung vorübergehend schließen, wenn diese nicht umgehend verwendet wird. Eine Verbindung erneut zu öffnen, ist nicht aufwendig, eine neue Verbindung zu erstellen, schon.

Wenn Sie ADO.NET 4.0 oder eine frühere Version verwenden, sollten Sie ein Upgrade auf die aktuelle ADO.NET-Version durchführen. Ab August 2018 können Sie [ADO.NET 4.6.2 herunterladen](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnose

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnose: Testen, ob Hilfsprogramme eine Verbindung herstellen können

Wenn Ihr Programm keine Verbindung mit der Datenbank in SQL-Datenbank herstellen kann, können Sie zu Diagnosezwecken versuchen, eine Verbindung mit einem Hilfsprogramm herzustellen. Idealerweise verwendet das Hilfsprogramm für die Verbindung dieselbe Bibliothek wie Ihr Programm.

Auf einem Windows-Computer können Sie die folgenden Hilfsprogramme nutzen:

- SQL Server Management Studio (ssms.exe), das ADO.NET für die Verbindung nutzt, oder
- `sqlcmd.exe`, das [ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server) für die Verbindung nutzt

Sobald Ihr Programm verbunden ist, testen Sie, ob eine kurze SQL-SELECT-Abfrage erfolgreich ausgeführt wird.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnose: Überprüfen der offenen Ports

Wenn Sie annehmen, dass Verbindungsversuche aufgrund von Portproblemen fehlschlagen, können Sie ein Hilfsprogramm auf Ihrem Computer ausführen, das die Portkonfigurationen angibt.

Unter Linux können die folgenden Hilfsprogramme nützlich sein:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Ändern Sie den Beispielwert in Ihre IP-Adresse.

Unter Windows kann das Hilfsprogramm [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) nützliche Informationen liefern. Nachfolgend eine Beispielabfrage für die Portinformationen einer Datenbank in SQL-Datenbank, die auf einem Laptop ausgeführt wurde:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnose: Protokollieren der Fehler

Periodisch auftretende Probleme lassen sich mitunter am besten diagnostizieren, indem Sie über mehrere Tage oder Wochen hinweg ein allgemeines Muster ermitteln.

Dabei kann es hilfreich sein, sämtliche Fehler auf dem Client zu protokollieren. Möglicherweise lassen sich die Protokolleinträge mit den Fehlerdaten in Zusammenhang bringen, die SQL-Datenbank intern protokolliert.

Enterprise Library 6 (EntLib60) bietet verwaltete .NET-Klassen zur Unterstützung der Protokollierung. Weitere Informationen finden Sie unter [5 – Protokollierung leicht gemacht: mit dem Protokollierungsanwendungsblock](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnose: Überprüfen der Systemprotokolle auf Fehler

Nachfolgend finden Sie einige Transact-SQL-SELECT-Anweisungen, mit denen Fehlerprotokolle und andere Informationen abgefragt werden.

| Protokollabfrage | BESCHREIBUNG |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |Die [sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)-Ansicht bietet Informationen zu einzelnen Ereignissen, einschließlich solcher, die vorübergehende Fehler oder Verbindungsfehler verursachen können.<br/><br/>Idealerweise können Sie die Werte **start_time** oder **end_time** den Zeiten zuordnen, zu denen in Ihrem Clientprogramm Probleme aufgetreten sind.<br/><br/>Sie müssen eine Verbindung mit der *Masterdatenbank* herstellen, um diese Abfrage auszuführen. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |Die [sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)-Ansicht bietet aggregierte Werte der Ereignistypen für die weitere Diagnose.<br/><br/>Sie müssen eine Verbindung mit der *Masterdatenbank* herstellen, um diese Abfrage auszuführen. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnose: Suche nach Problemereignissen im Protokoll von SQL-Datenbank

Sie können im Protokoll von SQL-Datenbank Einträge zu Problemereignissen suchen. Führen Sie die folgende Transact-SQL-SELECT-Anweisung in der *Masterdatenbank* aus:

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Zurückgegebene Zeilen aus „sys.fn_xe_telemetry_blob_target_read_file“

Das folgende Beispiel zeigt, wie eine zurückgegebene Zeile aussehen könnte. Die gezeigten Nullwerte sind in anderen Zeilen häufig keine Nullwerte.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

Bei Enterprise Library 6 (EntLib60) handelt es sich um ein Framework aus .NET-Klassen, mit denen Sie stabile Clouddienstclients implementieren können, wobei einer der Dienste SQL-Datenbank ist. Unter [Enterprise Library 6 – April 2013](/previous-versions/msp-n-p/dn169621(v=pandp.10)) finden Sie Themen zu den verschiedenen Bereichen, in denen EntLib60 hilfreich sein kann.

EntLib60 kann beispielsweise für Wiederholungslogik zur Behandlung von vorübergehenden Fehlern hilfreich sein. Weitere Informationen finden Sie unter [4 – Hartnäckigkeit, das Geheimnis aller Erfolge: Anwendungsblock zum Behandeln vorübergehender Fehler](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

> [!NOTE]
> Der Quellcode für EntLib60 steht im [Download Center](https://go.microsoft.com/fwlink/p/?LinkID=290898) zum öffentlichen Download bereit. Microsoft plant keine weiteren Funktions- oder Wartungsupdates für EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60-Klassen für vorübergehende Fehler und Wiederholungsversuche

Die folgenden EntLib60-Klassen sind besonders nützlich für Wiederholungslogik. All diese Klassen befinden sich im Namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** oder einem untergeordneten Namespace.

Im Namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** :

- **RetryPolicy**
  - **ExecuteAction**
- **ExponentialBackoff**
- **SqlDatabaseTransientErrorDetectionStrategy**
- **ReliableSqlConnection**
  - **ExecuteCommand**

Im Namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport** :

- **AlwaysTransientErrorDetectionStrategy**
- **NeverTransientErrorDetectionStrategy**

Unter folgenden Links finden Sie weitere Informationen zu EntLib60:

- Kostenloses E-Book: [Developer's Guide to Microsoft Enterprise Library, 2nd Edition](https://www.microsoft.com/download/details.aspx?id=41145).
- Bewährten Methoden: [Allgemeiner Leitfaden zum Wiederholen von Vorgängen](/azure/architecture/best-practices/transient-faults) bietet eine detaillierte Erläuterung wichtiger Aspekte im Zusammenhang mit Wiederholungslogik.
- NuGet-Download: [Enterprise Library – Transient Fault Handling Application Block 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Der Protokollierungsblock

- Der Protokollierungsblock ist eine äußerst flexible und umfassend konfigurierbare Lösung, die Folgendes ermöglicht:
  - Erstellen und Speichern von Protokollmeldungen an diversen Speicherorten.
  - Kategorisieren und Filtern von Meldungen.
  - Erfassen von Kontextinformationen für Debugging und Ablaufverfolgung sowie für Überwachung und allgemeine Protokollierungsanforderungen.
- Der Protokollierungsblock abstrahiert die Protokollierungsfunktionalität vom Protokollziel, sodass der Anwendungscode unabhängig von Speicherort und Typ des Zielprotokollspeichers einheitlich ist.

Weitere Informationen finden Sie unter [5 – Protokollierung leicht gemacht: mit dem Protokollierungsanwendungsblock](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Quellcode der IsTransient-Methode von EntLib60

Nachfolgend wird der C#-Quellcode für die Methode **IsTransient** der Klasse **SqlDatabaseTransientErrorDetectionStrategy** gezeigt. Anhand dieses Quellcodes wird ermittelt, welche Fehler als vorübergehend eingestuft werden und einen Wiederholungsversuch rechtfertigen (April 2013).

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Verbindungsbibliotheken für SQL-Datenbank und SQL Server](connect-query-content-reference-guide.md#libraries)
- [Verbindungspooling (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [*Retrying* ist eine Apache 2.0-lizenzierte Allzweckbibliothek für Wiederholungen, die in Python](https://pypi.python.org/pypi/retrying) geschrieben wurde und die Implementierung von Wiederholungsverhalten in praktisch jedem Anwendungsfall vereinfacht.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php