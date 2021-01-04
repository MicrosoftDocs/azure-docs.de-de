---
title: Behandeln von häufigen Verbindungsproblemen mit Azure SQL-Datenbank
description: Enthält Schritte zum Beheben von Verbindungsproblemen mit Azure SQL-Datenbank und anderen Problemen mit Azure SQL-Datenbank oder Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2020
ms.openlocfilehash: bcf11ef9b64a02383aad5175c19c5db58c3c39cf
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791340"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Beheben von Konnektivitätsproblemen und anderen Fehlern mit Azure SQL-Datenbank und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Wenn keine Verbindung mit Azure SQL-Datenbank oder Azure SQL Managed Instance hergestellt werden kann, erhalten Sie Fehlermeldungen. Diese Verbindungsprobleme können auf eine Neukonfiguration sowie auf Firewalleinstellungen, ein Verbindungstimeout, falsche Anmeldeinformationen oder auf die Missachtung bewährter Methoden und Entwurfsrichtlinien für den [Anwendungsentwurf](develop-overview.md) zurückzuführen sein. Außerdem können Sie keine Verbindung mehr herstellen, wenn für einige Ressourcen von Azure SQL-Datenbank oder der verwalteten SQL-Instanz der zulässige Höchstwert erreicht wird.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Fehlermeldungen für vorübergehende Fehler (40197, 40613 u. a.)

Die Azure-Infrastruktur verfügt über die Möglichkeit, Server dynamisch neu zu konfigurieren, wenn hohe Workloads im SQL-Datenbankdienst auftreten.  Dieses dynamische Verhalten führt aber u. U. dazu, dass die Verbindung zwischen Ihrem Clientprogramm und der Datenbank oder Instanz getrennt wird. Diese Art Fehlerbedingung wird als *vorübergehender Fehler* bezeichnet. Ereignisse bei der Neukonfiguration von Datenbanken werden durch geplante Ereignisse (z.B. Softwareupgrades) oder ungeplante Ereignisse (z.B. einen Prozessabsturz oder einen Lastenausgleich) hervorgerufen. Die meisten bei einer Neukonfiguration auftretenden Ereignisse sind normalerweise kurzlebig und sollten innerhalb von höchstens 60 Sekunden beendet werden. Trotzdem kann das Beenden dieser Ereignisse beizeiten länger dauern, so z.B., wenn eine große Transaktion eine lang andauernde Wiederherstellung auslöst. Die folgende Tabelle enthält eine Liste der verschiedenen vorübergehenden Fehler, die Anwendungen beim Herstellen einer Verbindung zur SQL-Datenbank empfangen können.

### <a name="list-of-transient-fault-error-codes"></a>Liste der Fehlercodes für vorübergehende Fehler

| Fehlercode | severity | BESCHREIBUNG |
| ---:| ---:|:--- |
| 4060 |16 |Die von der Anmeldung angeforderte „%.&#x2a;ls“-Datenbank kann nicht geöffnet werden. Fehler bei der Anmeldung. Weitere Informationen finden Sie unter [Fehler 4000 bis 4999](/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999).|
| 40197 |17 |Dienstfehler beim Verarbeiten Ihrer Anforderung. Wiederholen Sie den Vorgang. Fehlercode %d.<br/><br/>Sie erhalten diesen Fehler, wenn der Dienst aufgrund von Software- oder Hardwareupgrades, Hardwarefehlern oder sonstigen Failoverproblemen ausgefallen ist. Der Fehlercode (%d), der in der Meldung zum Fehler 40197 enthalten ist, liefert weitere Informationen zur Art des aufgetretenen Fehlers oder Failovers. Beispiele für Fehlercodes, die in die Meldung zum Fehler 40197 eingebettet sind, lauten 40020, 40143, 40166 und 40540.<br/><br/>Wenn die Verbindung wiederhergestellt wird, werden Sie automatisch mit einer fehlerfreien Kopie Ihrer Datenbank verbunden. Ihre Anwendung muss den Fehler 40197 abfangen, den für die Problembehandlung in der Meldung enthaltenen Fehlercode (%d) protokollieren und versuchen, eine neue Verbindung mit SQL-Datenbank herzustellen, bis die Ressourcen verfügbar sind, damit Ihre Verbindung wiederhergestellt wird. Weitere Informationen finden Sie unter [Vorübergehende Fehler](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Der Dienst ist derzeit ausgelastet. Wiederholen Sie die Anforderung in 10 Sekunden. Vorgangs-ID: %ls. Code: %d. Weitere Informationen finden Sie unter <br/>&bull; &nbsp;[Ressourcenlimits für Azure SQL-Datenbank- und Azure Synapse Analytics-Server](resource-limits-logical-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Übersicht über Ressourcenlimits für verwaltete Azure SQL-Instanzen](../managed-instance/resource-limits.md)|
| 40613 |17 |Die „%.&#x2a;ls“-Datenbank auf Server „%.&#x2a;ls“ ist zurzeit nicht verfügbar. Wiederholen Sie den Verbindungsversuch später. Falls das Problem weiterhin besteht, wenden Sie sich an den Kundensupport und geben als Ablaufverfolgungs-ID der Sitzung „%.&#x2a;ls“ an.<br/><br/> Dieser Fehler kann auftreten, wenn bereits eine dedizierte Administratorverbindung (DAC) zur Datenbank besteht. Weitere Informationen finden Sie unter [Vorübergehende Fehler](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Anforderung kann nicht verarbeitet werden. Zum Verarbeiten der Anforderung sind nicht genügend Ressourcen vorhanden.<br/><br/>Der Dienst ist derzeit ausgelastet. Versuchen Sie die Anforderung später erneut. Weitere Informationen finden Sie unter <br/>&bull; &nbsp;[Ressourcenlimits für Azure SQL-Datenbank- und Azure Synapse Analytics-Server](resource-limits-logical-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Übersicht über Ressourcenlimits für verwaltete Azure SQL-Instanzen](../managed-instance/resource-limits.md) |
| 49919 |16 |Die Erstellung oder Aktualisierung der Anforderung kann nicht verarbeitet werden. Für das Abonnement „%ld“ werden derzeit zu viele Erstell- oder Aktualisierungsvorgänge ausgeführt.<br/><br/>Der Dienst ist mit der Verarbeitung mehrerer Erstell- oder Aktualisierungsvorgänge für Ihr Abonnement oder Ihren Server ausgelastet. Zur Ressourcenoptimierung werden Anforderungen derzeit blockiert. Fragen Sie [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) auf ausstehende Vorgänge ab. Warten Sie, bis ausstehende Erstellungs- oder Aktualisierungsanforderungen abgeschlossen sind, oder löschen Sie eine Ihrer ausstehenden Anforderungen, und wiederholen Sie die Anforderung später. Weitere Informationen finden Sie unter <br/>&bull; &nbsp;[Ressourcenlimits für Azure SQL-Datenbank- und Azure Synapse Analytics-Server](resource-limits-logical-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Übersicht über Ressourcenlimits für verwaltete Azure SQL-Instanzen](../managed-instance/resource-limits.md) |
| 49920 |16 |Anforderung kann nicht verarbeitet werden. Für das Abonnement „%ld“ werden derzeit zu viele Vorgänge ausgeführt.<br/><br/>Der Dienst ist mit der Verarbeitung mehrerer Vorgänge für dieses Abonnement ausgelastet. Zur Ressourcenoptimierung werden Anforderungen derzeit blockiert. Fragen Sie [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) auf den Vorgangsstatus ab. Warten Sie, bis ausstehende Anforderungen abgeschlossen sind, oder löschen Sie eine Ihrer ausstehenden Anforderungen, und wiederholen Sie die Anforderung später. Weitere Informationen finden Sie unter <br/>&bull; &nbsp;[Ressourcenlimits für Azure SQL-Datenbank- und Azure Synapse Analytics-Server](resource-limits-logical-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Übersicht über Ressourcenlimits für verwaltete Azure SQL-Instanzen](../managed-instance/resource-limits.md) |
| 4221 |16 |Fehler bei der Anmeldung bei lesbarem sekundärem Replikat aufgrund einer zu langen Wartezeit auf "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". Das Replikat steht zur Anmeldung nicht zur Verfügung, weil Zeilenversionen für Transaktionen fehlen, die beim Neustarten des Replikats in Verarbeitung waren. Dieses Problem kann durch einen Rollback oder durch einen Commit der aktiven Transaktionen auf dem primären Replikat gelöst werden. Vorkommen dieser Bedingung können durch Vermeiden langer Schreibtransaktionen auf dem primären Replikat minimiert werden. |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Schritte zum Beheben vorübergehender Verbindungsprobleme

1. Ziehen Sie das [Microsoft Azure Service-Dashboard](https://azure.microsoft.com/status) für alle bekannten Ausfälle zu Rat, die während der Fehlermeldung durch die Anwendung aufgetreten sind.
2. Für Anwendungen, die Verbindungen zu einem Clouddienst wie Azure SQL-Datenbank herstellen, sollten Sie regelmäßige Rekonfigurationsereignisse erwarten. Daher sollten Sie eine Wiederholungslogik zur Fehlerbehandlung implementieren, anstatt Benutzern diese Anwendungsfehler anzuzeigen.
3. Wenn sich eine Datenbank ihren Ressourcenbegrenzungen nähert, kann dies wie ein vorübergehendes Verbindungsproblem aussehen. Sie [Ressourceneinschränkungen](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached).
4. Wenn Verbindungsprobleme weiterhin bestehen, die Fehlerdauer 60 Sekunden überschreitet oder der Fehler an einem Tag mehrfach auftritt, schicken Sie eine Azure-Supportanfrage, indem Sie auf der **Azure-Support** -Website [Support erhalten](https://azure.microsoft.com/support/options) auswählen.

#### <a name="implementing-retry-logic"></a>Implementieren von Wiederholungslogik

Es wird dringend empfohlen, das Clientprogramm mit einer Wiederholungslogik zu versehen, sodass erneut eine Verbindung hergestellt werden kann, nachdem sich der vorübergehende Fehler selbst korrigiert hat.  Es wird empfohlen, dass vor dem ersten Wiederholungsversuch eine Verzögerungszeit von fünf Sekunden verwendet wird. Wiederholungsversuche nach weniger als fünf Sekunden können den Clouddienst überfordern. Für jeden nachfolgenden Wiederholungsversuch sollte die Verzögerung exponentiell steigen, bis zu einem Maximum von 60 Sekunden.

Codebeispiele zur Wiederholungslogik finden Sie unter:

- [Connect resiliently to SQL with ADO.NET (Herstellen stabiler SQL-Verbindungen mit ADO.NET)](/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Connect resiliently to SQL with PHP (Herstellen stabiler SQL-Verbindungen mit PHP)](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Zusätzliche Informationen zur Behandlung von vorübergehenden Fehlern in Ihrer Anwendung finden Sie unter [Behandeln von Problemen bei vorübergehenden Verbindungsfehlern mit SQL-Datenbank](troubleshoot-common-connectivity-issues.md).

Eine Beschreibung der *Sperrfrist* für Clients, die ADO.NET verwenden, finden Sie im Artikel zum [Verbindungspooling (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>Netzwerkbezogener oder instanzspezifischer Fehler beim Herstellen einer Verbindung mit Ihrem Server

Das Problem tritt auf, wenn die Anwendung keine Verbindung mit dem Server herstellen kann.

Um dieses Problem zu beheben, führen Sie die unter [Schritte zum Beheben häufiger Verbindungsprobleme](#steps-to-fix-common-connection-issues) genannten Schritte (in der angegebenen Reihenfolge) aus.

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Der Server/die Instanz wurde nicht gefunden, oder auf ihn/sie kann nicht zugegriffen werden (Fehler 26, 40, 10053)

### <a name="error-26-error-locating-server-specified"></a>Fehler 26: Fehler beim Bestimmen des angegebenen Servers

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Fehler 40: Serververbindung konnte nicht geöffnet werden.

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Fehler 10053: Beim Empfangen von Ergebnissen vom Server ist ein Fehler auf Übertragungsebene aufgetreten.

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Diese Probleme treten auf, wenn die Anwendung keine Verbindung mit dem Server herstellen kann.

Um diese Probleme zu beheben, führen Sie die unter [Schritte zum Beheben häufiger Verbindungsprobleme](#steps-to-fix-common-connection-issues) genannten Schritte (in der angegebenen Reihenfolge) aus.

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Aufgrund von Firewallproblemen kann keine Verbindung mit dem Server hergestellt werden

### <a name="error-40615-cannot-connect-to--servername-"></a>Fehler 40615: Mit <Servername> kann keine Verbindung hergestellt werden

[Konfigurieren Sie im Azure-Portal die Firewalleinstellungen für Azure SQL-Datenbank](firewall-configure.md), um dieses Problem zu beheben.

### <a name="error-5-cannot-connect-to--servername-"></a>Fehler 5: Mit <Servername> kann keine Verbindung hergestellt werden

Stellen Sie für alle Firewalls zwischen Client und Internet sicher, dass Port 1433 für ausgehende Verbindungen geöffnet ist, um dieses Problem zu beheben.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Die Anmeldung beim Server ist nicht möglich (Fehler 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Fehler bei der Anmeldung für den Benutzer „<Benutzername>“

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Wenden Sie sich zum Beheben dieses Problems an Ihren Dienstadministrator, um einen gültigen Benutzernamen und das zugehörige Kennwort zu erhalten.

In der Regel kann der Dienstadministrator die Anmeldeinformationen mit den folgenden Schritten hinzufügen:

1. Anmelden beim Server mit SQL Server Management Studio (SSMS).
2. Mithilfe der folgenden SQL-Abfrage überprüfen, ob der Anmeldename deaktiviert ist:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Wenn der entsprechende Name deaktiviert ist, kann er mithilfe der folgenden Anweisung aktiviert werden:

   ```sql
   Alter login <User name> enable
   ```

4. Wenn der Benutzername für die SQL Server-Anmeldung nicht vorhanden ist, muss er mithilfe dieser Schritte erstellt werden:

   1. Doppelklicken Sie in SSMS auf **Sicherheit** , um den Bereich zu erweitern.
   2. Klicken Sie mit der rechten Maustaste auf **Anmeldungen** , und wählen Sie dann **Neue Anmeldung** .
   3. Im generierten Skript mit Platzhaltern können Sie die folgende SQL-Abfrage bearbeiten und ausführen:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Doppelklicken Sie auf **Datenbank** .
6. Wählen Sie die Datenbank aus, für die der Benutzer die Berechtigung erhalten soll.
7. Doppelklicken Sie auf **Sicherheit** .
8. Klicken Sie mit der rechten Maustaste auf **Benutzer** , und wählen Sie dann **Neuer Benutzer** .
9. Im generierten Skript mit Platzhaltern können Sie die folgende SQL-Abfrage bearbeiten und ausführen:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > Sie können auch `sp_addrolemember` verwenden, um bestimmte Benutzer bestimmten Datenbankrollen zuzuordnen.

Weitere Informationen finden Sie unter [Steuern und Gewähren des Datenbankzugriffs für SQL-Datenbank und SQL Data Warehouse](./logins-create-manage.md).

## <a name="connection-timeout-expired-errors"></a>Verbindungstimeoutfehler

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Verbindungstimeout abgelaufen

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Timeout abgelaufen

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException: Fehler des zugrunde liegenden Anbieters beim Öffnen

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Mit <Servername> kann keine Verbindung hergestellt werden

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Diese Ausnahmen können aufgrund von Verbindungs- oder Abfrageproblemen auftreten. Um zu bestätigen, dass dieser Fehler nicht durch Konnektivitätsprobleme verursacht wird, siehe [Bestätigen, dass ein Fehler von einem Konnektivitätsproblem verursacht wird](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Verbindungstimeouts treten auf, weil die Anwendung keine Verbindung mit dem Server herstellen kann. Um dieses Problem zu beheben, führen Sie die unter [Schritte zum Beheben häufiger Verbindungsprobleme](#steps-to-fix-common-connection-issues) genannten Schritte (in der angegebenen Reihenfolge) aus.

## <a name="resource-governance-errors"></a>Fehler bei der Ressourcenkontrolle

### <a name="error-10928-resource-id-d"></a>Fehler 10928: Ressourcen-ID: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Verwenden Sie zum Beheben dieses Problems die folgenden Methoden:

- Überprüfen Sie, ob zeitintensive Abfragen ausgeführt werden.

  > [!NOTE]
  > Dies ist ein minimalistischer Ansatz, der das Problem möglicherweise nicht löst.

1. Führen Sie die folgende SQL-Abfrage aus, um die Sicht [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) auf Sperranforderungen zu überprüfen:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Bestimmen Sie den **Eingabepuffer** für „Blockiert andere Prozesse“.
3. Optimieren Sie die Abfrage „Blockiert andere Prozesse“.

   Eine ausführliche Problembehandlung finden Sie unter [Wird meine Abfrage in der Cloud einwandfrei ausgeführt?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)

Wenn die Datenbank trotz Berücksichtigung von Blockierungen und zeitintensiven Abfragen konsistent an ihre Grenzen stößt, sollten Sie ein Upgrade auf eine Edition mit mehr Ressourcen ([Editionen](https://azure.microsoft.com/pricing/details/sql-database/)) in Betracht ziehen.

Weitere Informationen zu dynamischen Verwaltungssichten finden Sie unter [Dynamische Systemverwaltungssichten](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Weitere Informationen zu Datenbankbeschränkungen finden Sie unter [Ressourcenlimits für Azure SQL-Datenbank und Azure Synapse Analytics-Server](./resource-limits-logical-server.md).

### <a name="error-10929-resource-id-1"></a>Fehler 10929: Ressourcen-ID: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Fehler 40501: Der Dienst ist derzeit ausgelastet

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Dies ist ein Engine-Einschränkungsfehler. Er weist darauf hin, dass Ressourcenlimits überschritten werden.

Weitere Informationen zu Ressourcenlimits finden Sie unter [Ressourcenlimits für Azure SQL-Datenbank und Azure Synapse Analytics-Server](./resource-limits-logical-server.md).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Fehler 40544: Das Größenkontingent der Datenbank wurde erreicht

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Dieser Fehler tritt auf, wenn die Datenbank ihr Größenkontingent erreicht hat.

Mithilfe der folgenden Schritte können Sie das Problem umgehen oder zusätzliche Optionen nutzen:

1. Überprüfen Sie die aktuelle Größe der Datenbank, indem Sie das Dashboard im Azure-Portal verwenden.

   > [!NOTE]
   > Um festzustellen, welche Tabellen den meisten Speicherplatz belegen und somit potenzielle Kandidaten für die Bereinigung sind, führen Sie die folgende SQL-Abfrage aus:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Wenn die aktuelle Größe die für Ihre Edition maximal unterstützte Größe nicht überschreitet, können Sie mit ALTER DATABASE die MAXSIZE-Einstellung erhöhen.
3. Wenn die Datenbank bereits die maximal unterstützte Größe für Ihre Edition überschreitet, können Sie einen oder mehrere der folgenden Schritte probieren:

   - Führen Sie normale Aktivitäten zur Bereinigung der Datenbank aus. Bereinigen Sie beispielsweise die unerwünschten Daten mithilfe von TRUNCATE/DELETE, oder verschieben Sie Daten mithilfe von SQL Server Integration Services (SSIS) oder des Hilfsprogramms zum Massenkopieren (Bulk Copy Program, bcp).
   - Partitionieren oder löschen Sie Daten, löschen Sie Indizes, oder informieren Sie sich in der Dokumentation über mögliche Lösungen.
   - Informationen zur Datenbankskalierung finden Sie unter [Skalieren der Ressourcen für einzelne Datenbanken](./single-database-scale.md) und [Skalieren der Ressourcen für Pools für elastische Datenbanken](./elastic-pool-scale.md).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Fehler 40549: Sitzung wurde aufgrund einer zeitintensiven Transaktion beendet

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Wenn diese Fehlermeldung wiederholt auftritt, führen Sie die folgenden Schritte aus, um das Problem zu beheben:

1. Überprüfen Sie die Sicht sys.dm_exec_requests, um alle geöffneten Sitzungen mit einem hohen Wert in der Spalte total_elapsed_time anzuzeigen. Führen Sie diese Prüfung durch Ausführen des folgenden SQL-Skripts durch:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Ermitteln Sie den Eingabepuffer der zeitintensiven Abfrage.
3. Optimieren Sie die Abfrage.

Sie sollten auch Batchverarbeitung für Ihre Abfragen in Erwägung ziehen. Weitere Informationen finden Sie unter [Gewusst wie: Verbessern der Leistung von SQL-Datenbankanwendungen mithilfe von Batchverarbeitung](../performance-improve-use-batching.md).

Eine ausführliche Problembehandlung finden Sie unter [Wird meine Abfrage in der Cloud einwandfrei ausgeführt?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Fehler 40551: Die Sitzung wurde aufgrund übermäßiger TEMPDB-Auslastung beendet

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Um dieses Problem zu umgehen, führen Sie die folgenden Schritte aus:

1. Ändern Sie die Abfragen, um die Verwendung von temporärem Tabellenspeicherplatz zu verringern.
2. Löschen Sie temporäre Objekte, wenn sie nicht mehr benötigt werden.
3. Kürzen Sie Tabellen, oder entfernen Sie nicht verwendete Tabellen.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Fehler 40552: Die Sitzung wurde aufgrund übermäßiger Belegung des Speicherplatzes für das Transaktionsprotokoll beendet

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Probieren Sie die folgenden Methoden aus, um dieses Problem zu beheben:

- Das Problem kann aufgrund von Einfüge-, Aktualisierungs- oder Löschvorgängen auftreten.
Versuchen Sie, die Anzahl der Zeilen zu verringern, die sofort ausgeführt werden, indem Sie Batchverarbeitung oder eine Aufteilung in mehrere kleinere Transaktionen implementieren.
- Das Problem kann aufgrund von Vorgängen zum Neuerstellen des Indexes auftreten. Um dieses Problem zu umgehen, stellen Sie sicher, dass die Anzahl der betroffenen Zeilen in der Tabelle multipliziert mit der durchschnittlichen Größe des aktualisierten Felds in Bytes + 80 kleiner als 2 Gigabyte (GB) ist.

  > [!NOTE]
  > Bei einer Indexneuerstellung muss die durchschnittliche Größe des aktualisierten Felds durch die durchschnittliche Indexgröße ersetzt werden.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Fehler 40553: Die Sitzung wurde aufgrund übermäßiger Speicherauslastung beendet

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Um dieses Problem zu umgehen, versuchen Sie, die Abfrage zu optimieren.

Eine ausführliche Problembehandlung finden Sie unter [Wird meine Abfrage in der Cloud einwandfrei ausgeführt?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabelle mit zusätzlichen Fehlermeldungen bezüglich Ressourcenkontrolle

| Fehlercode | severity | BESCHREIBUNG |
| ---:| ---:|:--- |
| 10928 |20 |Ressourcen-ID: %d. Das %s-Limit für die Datenbank beträgt %d und wurde erreicht. Weitere Informationen finden Sie unter [Ressourcenlimits bei SQL-Datenbank für Einzel- und Pooldatenbanken](resource-limits-logical-server.md).<br/><br/>Mit der Ressourcen-ID wird die Ressource angegeben, für die das Limit erreicht wurde. Bei Arbeitsthreads lautet die Ressourcen-ID „1“. Bei Sitzungen lautet die Ressourcen-ID „2“.<br/><br/>Weitere Informationen zu diesem Fehler und zur Fehlerbehebung finden Sie unter: <br/>&bull; &nbsp;[Ressourcenlimits für Azure SQL-Datenbank- und Azure Synapse Analytics-Server](resource-limits-logical-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Übersicht über Ressourcenlimits für verwaltete Azure SQL-Instanzen](../managed-instance/resource-limits.md) |
| 10929 |20 |Ressourcen-ID: %d. Die %s-Mindestgarantie beträgt %d, der maximale Wert beträgt %d und die aktuelle Nutzung für die Datenbank beträgt %d. Der Server ist jedoch derzeit zu stark ausgelastet, um Anforderungen über %d für diese Datenbank zu unterstützen. Mit der Ressourcen-ID wird die Ressource angegeben, für die das Limit erreicht wurde. Bei Arbeitsthreads lautet die Ressourcen-ID „1“. Bei Sitzungen lautet die Ressourcen-ID „2“. Weitere Informationen finden Sie unter <br/>&bull; &nbsp;[Ressourcenlimits für Azure SQL-Datenbank- und Azure Synapse Analytics-Server](resource-limits-logical-server.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für einzelne Datenbanken](service-tiers-dtu.md)<br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Übersicht über Ressourcenlimits für verwaltete Azure SQL-Instanzen](../managed-instance/resource-limits.md) <br/>Versuchen Sie es andernfalls später noch einmal. |
| 40544 |20 |Das Datenbankkontingent wurde erreicht. Partitionieren oder löschen Sie Daten, löschen Sie Indizes, oder informieren Sie sich in der Dokumentation über mögliche Lösungen. Informationen zur Datenbankskalierung finden Sie unter [Skalieren der Ressourcen für einzelne Datenbanken](single-database-scale.md) und [Skalieren der Ressourcen für Pools für elastische Datenbanken](elastic-pool-scale.md).|
| 40549 |16 |Die Sitzung wird aufgrund einer Transaktion mit langer Laufzeit beendet. Verkürzen Sie die Transaktion. Weitere Informationen finden Sie unter [Gewusst wie: Verbessern der Leistung von SQL-Datenbankanwendungen mithilfe von Batchverarbeitung](../performance-improve-use-batching.md).|
| 40550 |16 |Die Sitzung wurde beendet, da zu viele Sperren abgerufen wurden. Reduzieren Sie die Anzahl der in einer einzelnen Transaktion gelesenen oder geänderten Zeilen. Weitere Informationen finden Sie unter [Gewusst wie: Verbessern der Leistung von SQL-Datenbankanwendungen mithilfe von Batchverarbeitung](../performance-improve-use-batching.md).|
| 40551 |16 |Die Sitzung wurde aufgrund übermäßiger `TEMPDB` -Auslastung beendet. Ändern Sie die Abfrage, um die Verwendung des temporären Tabellenbereichs zu verringern.<br/><br/>Wenn Sie temporäre Objekte verwenden, können Sie Speicherplatz in der `TEMPDB`-Datenbank sparen, indem Sie die temporären Objekte verwerfen, die von der Sitzung nicht mehr benötigt werden. Weitere Informationen zur Verwendung von „tempdb“ in SQL-Datenbank finden Sie unter [Tempdb-Datenbank in SQL-Datenbank](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Die Sitzung wurde aufgrund übermäßiger Verwendung des Speicherplatzes für das Transaktionsprotokoll beendet. Reduzieren Sie die Anzahl der in einer einzelnen Transaktion geänderten Zeilen. Weitere Informationen finden Sie unter [Gewusst wie: Verbessern der Leistung von SQL-Datenbankanwendungen mithilfe von Batchverarbeitung](../performance-improve-use-batching.md).<br/><br/>Versuchen Sie beim Durchführen von Masseneinfügungen mit dem Hilfsprogramm `bcp.exe` oder der `System.Data.SqlClient.SqlBulkCopy`-Klasse, die Option `-b batchsize` oder `BatchSize` zu verwenden, um die Anzahl der Zeilen zu beschränken, die bei jeder Transaktion auf den Server kopiert werden. Versuchen Sie es mit der Option `REBUILD WITH ONLINE = ON`, wenn Sie einen Index mit der `ALTER INDEX`-Anweisung neu erstellen. Informationen zu den Größen von Transaktionsprotokollen für das V-Kern-basierte Kaufmodell finden Sie unter: <br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für einzelne Datenbanken](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Übersicht über Ressourcenlimits für verwaltete Azure SQL-Instanzen](../managed-instance/resource-limits.md)|
| 40553 |16 |Die Sitzung wurde aufgrund übermäßiger Speicherauslastung beendet. Ändern Sie die Abfrage, damit weniger Zeilen verarbeitet werden.<br/><br/>Wenn Sie die Anzahl von `ORDER BY`- und `GROUP BY`-Vorgängen im Transact-SQL-Code reduzieren, verringern sich auch die Arbeitsspeicheranforderungen Ihrer Abfrage. Informationen zur Datenbankskalierung finden Sie unter [Skalieren der Ressourcen für einzelne Datenbanken](single-database-scale.md) und [Skalieren der Ressourcen für Pools für elastische Datenbanken](elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Fehler im Zusammenhang mit Pools für elastische Datenbanken

Die folgenden Fehler beziehen sich auf die Erstellung und Verwendung von Pools für elastische Datenbanken:

| Fehlercode | severity | BESCHREIBUNG | Korrekturmaßnahme |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Der Speichergrenzwert des Pools für elastische Datenbanken wurde erreicht. Die Speicherauslastung für den Pool für elastische Datenbanken darf (%d) MB nicht überschreiten. Es wurde versucht, Daten in eine Datenbank zu schreiben, während die Speicherbegrenzung des Pools für elastische Datenbanken erreicht wurde. Informationen zu Ressourceneinschränkungen finden Sie unter: <br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md) <br/> |Erhöhen Sie nach Möglichkeit die DTUs des Pools für elastische Datenbanken und/oder fügen Sie ihm Speicher hinzu, um die Speicherkapazität zu erweitern, reduzieren Sie die Speichernutzung der einzelnen Datenbanken innerhalb des Pools für elastische Datenbanken, oder entfernen Sie Datenbanken aus dem Pools für elastische Datenbanken. Informationen zur Skalierung von Pools für elastische Datenbanken finden Sie unter [Skalieren von Ressourcen für Pools für elastische Datenbanken](elastic-pool-scale.md).|
| 10929 | 16 |Die %s-Mindestgarantie beträgt %d, der maximale Wert beträgt %d und die aktuelle Nutzung für die Datenbank beträgt %d. Der Server ist jedoch derzeit zu stark ausgelastet, um Anforderungen über %d für diese Datenbank zu unterstützen. Informationen zu Ressourceneinschränkungen finden Sie unter: <br/>&bull; &nbsp;[DTU-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[V-Kern-basierte Einschränkungen für Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md) <br/> Versuchen Sie es andernfalls später noch einmal. DTU-/V-Kern-Mindestanzahl pro Datenbank; DTU-/V-Kern-Höchstanzahl pro Datenbank Die Gesamtanzahl der gleichzeitigen Worker (Anforderungen) in allen Datenbanken im Pool für elastische Datenbanken hat versucht, den Poolgrenzwert zu überschreiten. |Erhöhen Sie nach Möglichkeit die Anzahl von DTUs oder V-Kernen des Pools für elastische Datenbanken, um die Begrenzung für Worker zu steigern, oder entfernen Sie Datenbanken aus dem Pool für elastische Datenbanken. |
| 40844 | 16 |Datenbank „%Ls“ auf Server „%ls“ ist eine Datenbank der „%ls“-Edition in einem Pool für elastische Datenbanken und kann nicht über eine fortlaufende Kopierbeziehung verfügen.  |– |
| 40857 | 16 |Der Pool für elastische Datenbanken wurde für folgenden Server nicht gefunden: „%ls“, Name des Pools für elastische Datenbanken: „%ls“. Der angegebene Pool für elastische Datenbanken ist nicht auf dem angegebenen Server vorhanden. | Geben Sie einen gültigen Namen für den Pool für elastische Datenbanken an. |
| 40858 | 16 |Der Pool für elastische Datenbanken „%ls“ ist bereits auf folgendem Server vorhanden: „%ls“. Der angegebene Pool für elastische Datenbanken ist auf dem angegebenen Server bereits vorhanden. | Geben Sie einen neuen Namen für den Pool für elastische Datenbanken an. |
| 40859 | 16 |Der Pool für elastische Datenbanken unterstützt Dienstebene „%ls“ nicht. Die angegebene Dienstebene wird für die Bereitstellung von Pools für elastische Datenbanken nicht unterstützt. |Geben Sie die richtige Edition an, oder lassen Sie die Dienstebene leer, um die Standarddienstebene zu verwenden. |
| 40860 | 16 |Die Kombination aus dem Pool für elastische Datenbanken „%ls“ und Dienstziel „%ls“ ist ungültig. Der Pool für elastische Datenbanken und die Dienstebene können nur gemeinsam angegeben werden, wenn der Ressourcentyp „ElasticPool“ definiert ist. |Geben Sie die richtige Kombination aus Pool für elastische Datenbanken und Dienstebene an. |
| 40861 | 16 |Die Datenbankedition „%. *ls“ darf sich nicht von der Dienstebene des Pools für elastische Datenbanken („%.* ls“) unterscheiden. Die Datenbankedition unterscheidet sich von der Dienstebene des Pools für elastische Datenbanken. |Geben Sie keine Datenbankedition an, die sich von der Dienstebene des Pools für elastische Datenbanken unterscheidet.  Beachten Sie, dass die Datenbankedition nicht angegeben werden muss. |
| 40862 | 16 |Der Name des Pools für elastische Datenbanken muss angegeben werden, wenn das Dienstziel des Pools für elastische Datenbanken angegeben wurde. Das Dienstziel des Pools für elastische Datenbanken identifiziert einen Pool für elastische Datenbanken nicht eindeutig. |Geben Sie den Namen des Pools für elastische Datenbanken an, wenn Sie das Dienstziel des Pools für elastische Datenbanken verwenden. |
| 40864 | 16 |Die DTU-Anzahl für den Pool für elastische Datenbanken muss mindestens (%d) DTUs für die Dienstebene „%.*ls“ betragen. Es wurde versucht, eine DTU-Anzahl für den Pool für elastische Datenbanken unterhalb des unteren Grenzwerts festzulegen. |Legen Sie die DTU-Einstellung für den Pool für elastische Datenbanken mindestens auf die Untergrenze fest. |
| 40865 | 16 |Die DTU-Anzahl für den Pool für elastische Datenbanken darf höchstens (%d) DTUs für die Dienstebene „%.*ls“ betragen. Es wurde versucht, eine DTU-Anzahl für den Pool für elastische Datenbanken oberhalb des oberen Grenzwerts festzulegen. |Legen Sie die DTU-Einstellung für den Pool für elastische Datenbanken höchstens auf die Obergrenze fest. |
| 40867 | 16 |Die maximalen DTUs pro Datenbank müssen mindestens (%d) für Dienstebene "%.*ls" betragen. Es wurde versucht, eine maximale DTU-Anzahl pro Datenbank festzulegen, die unter der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des Pools für elastische Datenbanken zu verwenden, die die gewünschte Einstellung unterstützt. |
| 40868 | 16 |Die maximale DTU-Anzahl pro Datenbank kann nicht mehr als (%d) für Dienstebene "%.*ls" betragen. Es wurde versucht, eine maximale DTU-Anzahl pro Datenbank festzulegen, die über der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des Pools für elastische Datenbanken zu verwenden, die die gewünschte Einstellung unterstützt. |
| 40870 | 16 |Die minimale DTU-Anzahl pro Datenbank kann nicht mehr als (%d) für Dienstebene "%.*ls" betragen. Es wurde versucht, eine minimale DTU-Anzahl pro Datenbank festzulegen, die über der unterstützten Grenze liegt. | Sie sollten in Betracht ziehen, die Dienstebene des Pools für elastische Datenbanken zu verwenden, die die gewünschte Einstellung unterstützt. |
| 40873 | 16 |Die Anzahl der Datenbanken (%d) und minimalen DTUs pro Datenbank (%d) darf die DTU-Anzahl des Pools für elastische Datenbanken (%d) nicht überschreiten. Es wurde versucht, eine Mindestanzahl von DTUs für den Pool für elastische Datenbanken anzugeben, die die DTU-Anzahl des Pools für elastische Datenbanken überschreitet. | Sie sollten in Betracht ziehen, die Anzahl der DTUs im Pool für elastische Datenbanken zu erhöhen oder die Mindestanzahl der DTUs pro Datenbank zu verringern, oder Sie verringern die Anzahl der Datenbanken im Pool für elastische Datenbanken. |
| 40877 | 16 |Ein Pool für elastische Datenbanken kann nur gelöscht werden, wenn er keine Datenbanken enthält. Der Pool für elastische Datenbanken enthält eine oder mehrere Datenbanken und kann nicht gelöscht werden. |Entfernen Sie Datenbanken aus dem Pool für elastische Datenbanken, um ihn zu löschen. |
| 40881 | 16 |Der Pool für elastische Datenbanken „%.*ls“ hat den Grenzwert für die Anzahl an Datenbanken erreicht.  Der Grenzwert für die Datenbankanzahl im Pool für elastische Datenbanken darf (%d) für einen Pool für elastische Datenbanken mit (%d) DTUs nicht überschreiten. Es wurde versucht, eine Datenbank zu erstellen oder zum Pool für elastische Datenbanken hinzuzufügen, während der Grenzwert für die Datenbankanzahl des Pools für elastische Datenbanken erreicht wurde. | Erhöhen Sie nach Möglichkeit die DTU-Anzahl des Pools für elastische Datenbanken, um die Begrenzung für Datenbanken zu steigern, oder entfernen Sie Datenbanken aus dem Pool für elastische Datenbanken. |
| 40889 | 16 |Die Begrenzung für DTUs oder Speicher für den Pool für elastische Datenbanken „%.*ls“ kann nicht verkleinert werden, da nicht genügend Speicherplatz für die Datenbanken verfügbar wäre. Es wurde versucht, die Speicherbegrenzung des Pools für elastische Datenbanken unter die Speicherauslastung zu verringern. | Reduzieren Sie die Speicherauslastung der einzelnen Datenbanken im Pool für elastische Datenbanken, oder entfernen Sie Datenbanken aus dem Pool, um die DTUs oder Speicherbegrenzung zu verringern. |
| 40891 | 16 |Die Mindestanzahl von DTUs pro Datenbank (%d) darf die Höchstanzahl von DTUs pro Datenbank (%d) nicht überschreiten. Es wurde versucht, die DTU-Mindestanzahl pro Datenbank höher festzulegen, als die maximale DTU-Anzahl pro Datenbank. |Stellen Sie sicher, dass die Mindestanzahl von DTUs pro Datenbank nicht die Höchstanzahl von DTUs pro Datenbank überschreitet. |
| TBD | 16 |Die Speichergröße für eine einzelne Datenbank in einem Pool für elastische Datenbanken darf die maximal zulässige Größe für die Dienstebene des Pools für elastische Datenbanken „%.*ls“ nicht überschreiten. Die maximale Größe der Datenbank überschreitet die maximale Größe, die von der Dienstebene des Pools für elastische Datenbanken zugelassen wird. |Legen Sie die maximale Größe der Datenbank höchstens auf die maximal zulässige Größe der Dienstebene des Pools für elastische Datenbanken fest. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Der von der Anmeldung angeforderte „Master“ der Datenbank kann nicht geöffnet werden. Die Anmeldung ist fehlgeschlagen.

Dieses Problem tritt auf, weil das Konto keine Zugriffsberechtigungen für die Masterdatenbank hat. SQL Server Management Studio (SSMS) versucht jedoch standardmäßig, eine Verbindung mit der Masterdatenbank herzustellen.

Gehen Sie folgendermaßen vor, um das Problem zu beheben:

1. Klicken Sie auf dem Anmeldebildschirm von SSMS auf **Optionen** und dann auf **Verbindungseigenschaften** .
2. Geben Sie in das Feld **Verbindung mit Datenbank herstellen** den standardmäßigen Datenbanknamen des Benutzers als standardmäßige Anmeldedatenbank ein, und klicken Sie dann auf **Verbinden** .

   ![Verbindungseigenschaften](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Bestätigen, dass ein Fehler von einem Konnektivitätsproblem verursacht wird

Um zu bestätigen, dass ein Fehler durch ein Verbindungsproblem verursacht wird, überprüfen Sie die Stapelüberwachung für Frames, die Aufrufe zum Öffnen einer Verbindung wie die folgenden anzeigen (beachten Sie den Verweis auf die **SqlConnection** -Klasse):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Wenn die Ausnahme durch Abfrageprobleme ausgelöst wird, ähnelt der Aufrufstapel dem folgenden (beachten Sie den Verweis auf die **SqlCommand** -Klasse). [Optimieren Sie Ihre Abfragen](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud) in dieser Situation.

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Weitere Anleitungen zum Optimieren der Leistung finden Sie in den folgenden Ressourcen:

- [Verwalten von Azure SQL-Indizes und -Statistiken](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [Manuelles Optimieren der Abfrageleistung in Azure SQL-Datenbank](./performance-guidance.md)
- [Überwachen der Leistung von Azure SQL-Datenbank mit dynamischen Verwaltungssichten](./monitoring-with-dmvs.md)
- [Verwenden des Abfragespeichers in Azure SQL-Datenbank](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)

## <a name="steps-to-fix-common-connection-issues"></a>Schritte zum Beheben häufiger Verbindungsprobleme

1. Stellen Sie sicher, dass TCP/IP auf dem Anwendungsserver als Clientprotokoll aktiviert ist. Weitere Informationen finden Sie unter [Konfigurieren von Clientprotokollen](/sql/database-engine/configure-windows/configure-client-protocols). Überprüfen Sie auf Anwendungsservern, auf denen keine SQL-Tools installiert sind, ob TCP/IP aktiviert ist, indem Sie **cliconfg.exe** (Hilfsprogramm für das SQL Server-Clientnetzwerk) ausführen.
2. Überprüfen Sie die Verbindungszeichenfolge der Anwendung auf ordnungsgemäße Konfiguration. Stellen Sie z. B. sicher, dass in der Verbindungszeichenfolge der richtige Port (1433) und der vollqualifizierte Servername angegeben sind.
Weitere Informationen finden Sie unter [Abrufen von Verbindungsinformationen](./connect-query-ssms.md#get-server-connection-information).
3. Versuchen Sie, den Wert für das Verbindungstimeout zu erhöhen. Wir empfehlen ein Verbindungstimeout von mindestens 30 Sekunden.
4. Testen Sie die Konnektivität zwischen dem Anwendungsserver und der Azure SQL-Datenbank-Instanz, indem Sie [SQL Server Management Studio (SSMS)](./connect-query-ssms.md), eine UDL-Datei, Ping oder Telnet verwenden. Weitere Informationen finden Sie unter [Beheben von Fehlern bei der Konnektivität mit SQL Server ](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) und [Diagnose bei Verbindungsproblemen](./troubleshoot-common-connectivity-issues.md#diagnostics).

   > [!NOTE]
   > Als Problembehandlungsschritt können Sie die Konnektivität auch auf einem anderen Clientcomputer testen.

5. Stellen Sie als bewährte Methode sicher, dass die Wiederholungslogik vorhanden ist. Weitere Informationen zur Wiederholungslogik finden Sie unter [Arbeiten mit Verbindungsproblemen und vorübergehenden Fehlern bei SQL-Datenbank](./troubleshoot-common-connectivity-issues.md).

Wenn Ihr Problem mit diesen Schritten nicht behoben werden kann, versuchen Sie, weitere Daten zu erfassen, und wenden Sie sich dann an den Support. Wenn es sich bei Ihrer Anwendung um einen Clouddienst handelt, aktivieren Sie die Protokollierung. Dieser Schritt gibt einen UTC-Zeitstempel des Fehlers zurück. Außerdem gibt SQL-Datenbank die Ablaufverfolgungs-ID zurück. [Microsoft Customer Support Services](https://azure.microsoft.com/support/options/) kann diese Informationen verwenden.

Weitere Informationen zum Aktivieren der Protokollierung finden Sie unter [Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service](../../app-service/troubleshoot-diagnostic-logs.md).

## <a name="next-steps"></a>Nächste Schritte

- [Verbindungsarchitektur von Azure SQL-Datenbank und Azure Synapse Analytics](./connectivity-architecture.md)
- [Netzwerk-Zugriffssteuerung für Azure SQL-Datenbank und Azure Synapse Analytics](./network-access-controls-overview.md)