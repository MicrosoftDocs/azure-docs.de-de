---
title: Datensynchronisierungs-Agent für die SQL-Datensynchronisierung
description: Es wird beschrieben, wie Sie den Datensynchronisierungs-Agent für die SQL-Datensynchronisierung in Azure installieren und ausführen, um Daten mit SQL Server-Datenbanken zu synchronisieren.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: ed8d51adf5a93b470f287383a4d3eeb866b15236
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791459"
---
# <a name="data-sync-agent-for-sql-data-sync"></a>Datensynchronisierungs-Agent für die SQL-Datensynchronisierung
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Synchronisieren von Daten mit SQL Server-Datenbanken durch die Installation und Konfiguration des Datensynchronisierungs-Agents für die SQL-Datensynchronisierung in Azure. Weitere Informationen zur SQL-Datensynchronisierung finden Sie unter [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> SQL-Datensynchronisierung unterstützt derzeit **keine** verwalteten Azure SQL-Instanzen.

## <a name="download-and-install"></a>Herunterladen und Installieren

Sie können den Datensynchronisierungs-Agent [hier](https://www.microsoft.com/download/details.aspx?id=27693) herunterladen. Um den Datensynchronisierungs-Agent zu aktualisieren, installieren Sie den Agent am selben Speicherort wie der alte Agent. Dadurch wird der ursprüngliche Agent überschrieben.

### <a name="install-silently"></a>Unbeaufsichtigtes Installieren

Geben Sie für die unbeaufsichtigte Installation des Datensynchronisierungs-Agents an der Eingabeaufforderung einen Befehl wie im folgenden Beispiel ein. Überprüfen Sie den Dateinamen der heruntergeladenen MSI-Datei, und geben Sie Ihre eigenen Werte für die Argumente **TARGETDIR** und **SERVICEACCOUNT** an.

- Wenn Sie für **TARGETDIR** keinen Wert angeben, lautet der Standardwert `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Wenn Sie `LocalSystem` als Wert für **SERVICEACCOUNT** angeben, sollten Sie beim Konfigurieren des Agents für die Verbindung mit der SQL Server-Instanz die SQL Server-Authentifizierung verwenden.

- Wenn Sie ein Domänenbenutzerkonto oder ein lokales Benutzerkonto als Wert für **SERVICEACCOUNT** angeben, müssen Sie auch das Kennwort mit dem Argument **SERVICEPASSWORD** angeben. Beispiel: `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-a-sql-server-database"></a>Synchronisieren von Daten mit einer SQL Server-Datenbank

Informationen zum Konfigurieren des Datensynchronisierungs-Agents zum Synchronisieren von Daten mit einer oder mehreren SQL Server-Datenbanken finden Sie unter [Hinzufügen einer SQL Server-Datenbank](sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a> Häufig gestellte Fragen zum Datensynchronisierungs-Agent

### <a name="why-do-i-need-a-client-agent"></a>Wozu benötige ich einen Client-Agent?

Der Dienst der SQL-Datensynchronisierung kommuniziert über den Client-Agent mit SQL Server-Datenbanken. Diese Sicherheitsfunktion verhindert die direkte Kommunikation mit Datenbanken hinter einer Firewall. Bei der Kommunikation des Diensts der SQL-Datensynchronisierung mit dem Agent werden verschlüsselte Verbindungen und ein eindeutiges Token bzw. ein eindeutiger *Agent-Schlüssel* verwendet. Die SQL Server-Datenbanken authentifizieren den Agent mit der Verbindungszeichenfolge und dem Agent-Schlüssel. Diese Vorgehensweise bietet ein hohes Maß an Sicherheit für Ihre Daten.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Wie viele Instanzen der lokalen Agent-Benutzeroberfläche können ausgeführt werden?

Es kann nur eine Instanz der Benutzeroberfläche ausgeführt werden.

### <a name="how-can-i-change-my-service-account"></a>Wie kann ich mein Dienstkonto ändern?

Nach der Installation eines Client-Agents besteht die einzige Möglichkeit zum Ändern des Dienstkontos darin, ihn zu deinstallieren und einen neuen Client-Agent mit dem neuen Dienstkonto zu installieren.

### <a name="how-do-i-change-my-agent-key"></a>Wie ändere ich meinen Agent-Schlüssel?

Ein Agent-Schlüssel kann nur einmal von einem Agent verwendet werden. Er kann nicht wiederverwendet werden, wenn Sie einen Agent entfernen und dann einen neuen Agent installieren, und er kann nicht von mehreren Agents verwendet werden. Wenn Sie einen neuen Schlüssel für einen vorhandenen Agent erstellen müssen, müssen Sie sicherstellen, dass derselbe Schlüssel beim Client-Agent und beim Dienst der SQL-Datensynchronisierung hinterlegt wird.

### <a name="how-do-i-retire-a-client-agent"></a>Wie setze ich einen Client-Agent außer Kraft?

Wenn Sie einen Agent umgehend für ungültig erklären oder außer Kraft setzen möchten, generieren Sie erneut seinen Schlüssel im Portal, übergeben ihn jedoch nicht an die Agent-Benutzeroberfläche. Durch das erneute Generieren eines Schlüssels wird der vorherige Schlüssel ungültig gemacht – unabhängig davon, ob der entsprechende Agent online oder offline ist.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Wie verschiebe ich einen Client-Agent auf einen anderen Computer?

Wenn Sie den lokalen Agent auf einem anderen Computer als dem ausführen möchten, auf dem er sich gerade befindet, führen Sie folgende Schritte aus:

1. Installieren Sie den Agent auf dem gewünschten Computer.
2. Melden Sie sich beim Portal der SQL-Datensynchronisierung an, und generieren Sie erneut einen Agent-Schlüssel für den neuen Agent.
3. Übergeben Sie den neuen Agent-Schlüssel über die Benutzeroberfläche des neuen Agents.
4. Warten Sie, während der Client-Agent die Liste der zuvor registrierten lokalen Datenbanken herunterlädt.
5. Geben Sie Datenbankanmeldeinformationen für alle Datenbanken ein, die als nicht erreichbar angezeigt werden. Diese Datenbanken müssen auf dem neuen Computer erreichbar sein, auf denen der Agent installiert ist.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a> Beheben von Problemen mit dem Datensynchronisierungs-Agent

- [Der Client-Agent kann nicht installiert, deinstalliert oder repariert werden.](#agent-install)

- [Der Client-Agent funktioniert nach dem Abbrechen der Deinstallation nicht mehr.](#agent-uninstall)

- [Meine Datenbank ist in der Agent-Liste nicht enthalten.](#agent-list)

- [Der Client-Agent startet nicht (Fehler 1069).](#agent-start)

- [Ich kann den Agent-Schlüssel nicht übermitteln.](#agent-key)

- [Der Client-Agent kann nicht aus dem Portal gelöscht werden, wenn die zugeordnete lokale Datenbank nicht erreichbar ist.](#agent-delete)

- [Die lokale Synchronisierungs-Agent-App kann keine Verbindung mit dem lokalen Synchronisierungsdienst herstellen.](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a> Der Client-Agent kann nicht installiert, deinstalliert oder repariert werden.

- **Ursache** . Viele Szenarien können diesen Fehler verursachen. Um die genaue Ursache für diesen Fehler zu ermitteln, sehen Sie sich die Protokolle an.

- **Lösung** . Generieren Sie die Windows Installer-Protokolle, und untersuchen Sie diese, um die genaue Fehlerursache zu finden. Sie können die Protokollierung über eine Eingabeaufforderung aktivieren. Wenn Sie beispielsweise `SQLDataSyncAgent-2.0-x86-ENU.msi` als Installationsdatei heruntergeladen haben, generieren und untersuchen Sie die Protokolldateien mithilfe der folgenden Befehlszeilen:

  - Für Installationen: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Für Deinstallationen: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Sie können die Protokollierung auch für alle mit Windows Installer durchgeführten Installationen aktivieren. Im Microsoft Knowledge Base-Artikel [Aktivieren der Windows Installer-Protokollierung](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) erfahren Sie, wie Sie die Protokollierung für Windows Installer mit nur einem Klick aktivieren. Dort finden Sie auch Informationen zum Speicherort der Protokolle.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a> Der Client-Agent funktioniert nach dem Abbrechen der Deinstallation nicht mehr.

Der Client-Agent funktioniert nicht mehr, auch nachdem Sie die Deinstallation abgebrochen haben.

- **Ursache** . Dieses Problem tritt auf, weil der Client-Agent der SQL-Datensynchronisierung keine Anmeldeinformationen speichert.

- **Lösung** . Sie können die folgenden beiden Lösungen ausprobieren:

    -   Verwenden Sie „services.msc“, um die Anmeldeinformationen für den Client-Agent erneut einzugeben.
    -   Deinstallieren Sie den Client-Agent, und installieren Sie einen neuen. Den neuesten Client-Agent können Sie aus dem [Download Center](https://www.microsoft.com/download/details.aspx?id=27693) herunterladen und installieren.

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a> Meine Datenbank ist in der Agent-Liste nicht enthalten.

Wenn Sie versuchen, einer Synchronisierungsgruppe eine bereits vorhandene SQL Server-Datenbank hinzuzufügen, wird die Datenbank nicht in der Agent-Liste angezeigt.

Folgende Szenarien können diesen Fehler verursachen:

- **Ursache** . Client-Agent und Synchronisierungsgruppe befinden sich in unterschiedlichen Rechenzentren.

- **Lösung** . Der Client-Agent und die Synchronisierungsgruppe müssen sich im gleichen Rechenzentrum befinden. Hierfür stehen Ihnen zwei Möglichkeiten zur Verfügung:

    -   Erstellen Sie einen neuen Agent in dem Rechenzentrum, in dem sich die Synchronisierungsgruppe befindet. Registrieren Sie anschließend die Datenbank bei diesem Agent.
    -   Löschen Sie die aktuelle Synchronisierungsgruppe, Erstellen Sie die Synchronisierungsgruppe dann in dem Rechenzentrum neu, in dem sich der Agent befindet.

- **Ursache** . Die Datenbankliste des Client-Agents ist nicht auf dem neuesten Stand.

- **Lösung** . Beenden Sie den Client-Agent-Dienst, und starten Sie ihn neu.

    Der lokale Agent lädt die Liste mit den zugeordneten Datenbanken nur bei der ersten Übermittlung des Agent-Schlüssels herunter. Bei nachfolgenden Übermittlungen wird die Liste nicht heruntergeladen. Daher werden Datenbanken, die während der Verschiebung eines Agents registriert wurden, in der ursprünglichen Agent-Instanz nicht angezeigt.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a> Der Client-Agent startet nicht (Fehler 1069).

Sie stellen fest, dass der Agent auf einem Computer, der SQL Server hostet, nicht ausgeführt wird. Wenn Sie versuchen, den Agent manuell zu starten, wird ein Dialogfeld mit folgender Fehlermeldung angezeigt: „Fehler 1069: Der Dienst wurde aufgrund eines Anmeldefehlers nicht gestartet.“

![Dialogfeld mit dem Datensynchronisierungsfehler 1069](./media/sql-data-sync-agent-overview/sync-error-1069.png)

- **Ursache** . Eine wahrscheinliche Ursache für diesen Fehler ist, dass sich das Kennwort auf dem lokalen Server geändert hat, seit Sie den Agent und das Agent-Kennwort erstellt haben.

- **Lösung** . Legen Sie das Kennwort des Agents auf Ihr aktuelles Serverkennwort fest:

  1. Suchen Sie den Client-Agent-Dienst für die SQL-Datensynchronisierung.  
    a. Wählen Sie **Starten** aus.  
    b. Geben Sie **services.msc** in das Suchfeld ein.  
    c. Klicken Sie in den Suchergebnissen auf **Dienste** .  
    d. Scrollen Sie im Fenster **Dienste** zum Eintrag für den **SQL-Datensynchronisierungs-Agent** .  
  1. Klicken Sie mit der rechten Maustaste auf **SQL-Datensynchronisierungs-Agent** , und wählen Sie **Beenden** aus.
  1. Klicken Sie mit der rechten Maustaste auf **SQL-Datensynchronisierungs-Agent** , und wählen Sie **Eigenschaften** aus.
  1. Klicken Sie im Fenster **Eigenschaften des SQL-Datensynchronisierungs-Agents** auf die Registerkarte **Anmelden** .
  1. Geben Sie Ihr Kennwort in das Textfeld **Kennwort** ein.
  1. Geben Sie das Kennwort im Feld **Kennwort bestätigen** noch einmal ein.
  1. Klicken Sie auf **Apply** (Anwenden) und dann auf **OK** .
  1. Klicken Sie im Fenster **Dienste** mit der rechten Maustaste auf den Dienst **SQL-Datensynchronisierungs-Agent** , und klicken Sie anschließend auf **Starten** .
  1. Schließen Sie das Fenster **Dienste** .

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a> Ich kann den Agent-Schlüssel nicht übermitteln.

Sie versuchen, einen erstellten oder neu erstellten Schlüssel für einen Agent über die SqlAzureDataSyncAgent-Anwendung zu übermitteln. Die Übermittlung kann jedoch nicht abgeschlossen werden.

![Dialogfeld mit Synchronisierungsfehler: Agent-Schlüssel kann nicht übermittelt werden.](./media/sql-data-sync-agent-overview/sync-error-cant-submit-agent-key.png)

- **Voraussetzungen** Bevor Sie fortfahren, überprüfen Sie die folgenden Voraussetzungen:

  - Der Windows-Dienst für die SQL-Datensynchronisierung wird ausgeführt.

  - Das Dienstkonto für den Windows-Dienst „SQL-Datensynchronisierung“ verfügt über Netzwerkzugriff.

  - Der ausgehende Port 1433 ist in Ihrer lokalen Firewallregel geöffnet.

  - Die lokale IP-Adresse wird zum Server oder zur Datenbankfirewallregel für die Synchronisierung der Metadaten-Datenbank hinzugefügt.

- **Ursache** . Durch den Agent-Schlüssel wird jeder lokale Agent eindeutig identifiziert. Der Schlüssel muss zwei Bedingungen erfüllen:

  -   Der Client-Agent-Schlüssel auf dem Server für die SQL-Datensynchronisierung und dem lokalen Computer muss identisch sein.
  -   Der Client-Agent-Schlüssel kann nur einmal verwendet werden.

- **Lösung** . Sollte der Agent nicht funktionieren, ist mindestens eine dieser Bedingungen nicht erfüllt. So beheben Sie das Problem mit dem Agent:

  1. Generieren Sie einen neuen Schlüssel.
  1. Wenden Sie den neuen Schlüssel auf den Agent an.

  So wenden Sie den neuen Schlüssel auf den Agent an:

  1. Wechseln Sie im Explorer zum Installationsverzeichnis Ihres Agents. Das Standardinstallationsverzeichnis lautet C:\\Programme (x86)\\Microsoft SQL Data Sync.
  1. Doppelklicken Sie auf das Unterverzeichnis „bin“.
  1. Öffnen Sie die SqlAzureDataSyncAgent-Anwendung.
  1. Klicken Sie auf **Agent-Schlüssel übermitteln** .
  1. Fügen Sie den Schlüssel aus der Zwischenablage in das dafür vorgesehene Feld ein.
  1. Klicken Sie auf **OK** .
  1. Schließen Sie das Programm.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a> Der Client-Agent kann nicht aus dem Portal gelöscht werden, wenn die zugeordnete lokale Datenbank nicht erreichbar ist.

Wenn ein lokaler, bei einem Client-Agent für die SQL-Datensynchronisierung registrierter Endpunkt (also eine Datenbank) nicht erreichbar ist, kann der Client-Agent nicht gelöscht werden.

- **Ursache** . Der lokale Agent kann nicht gelöscht werden, da die nicht erreichbare Datenbank noch bei dem Agent registriert ist. Wenn Sie versuchen, den Agent zu löschen, versucht der Löschprozess erfolglos, die Datenbank zu erreichen.

- **Lösung** . Verwenden Sie „force delete“, um die nicht erreichbare Datenbank zu löschen.

> [!NOTE]
> Wenn nach der Verwendung von „force delete“ Tabellen mit Synchronisierungsmetadaten zurückbleiben, verwenden Sie `deprovisioningutil.exe`, um diese zu bereinigen.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a> Die lokale Synchronisierungs-Agent-App kann keine Verbindung mit dem lokalen Synchronisierungsdienst herstellen.

- **Lösung** . Probieren Sie die folgenden Schritte aus:

  1. Beenden Sie die App.  
  1. Öffnen Sie den Bereich „Komponentendienste“.  
    a. Geben Sie **services.msc** in das Suchfeld der Taskleiste ein.  
    b. Doppelklicken Sie in den Suchergebnissen auf **Dienste** .  
  1. Beenden Sie den **SQL-Datensynchronisierungsdienst** .
  1. Starten Sie den **SQL-Datensynchronisierungsdienst** neu.  
  1. Öffnen Sie die App erneut.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Ausführen des Datensynchronisierungs-Agents über die Eingabeaufforderung

Sie können die folgenden Befehle des Datensynchronisierungs-Agents an der Eingabeaufforderung ausführen:

### <a name="ping-the-service"></a>Pingen des Diensts

#### <a name="usage"></a>Verwendung

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Beispiel

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Anzeigen der registrierten Datenbanken

#### <a name="usage"></a>Verwendung

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Beispiel

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Übermitteln des Agent-Schlüssels

#### <a name="usage"></a>Verwendung

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Beispiel

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Registrieren einer Datenbank

#### <a name="usage"></a>Verwendung

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Beispiele

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Aufheben der Registrierung einer Datenbank

Wenn Sie diesen Befehl zum Aufheben der Registrierung einer Datenbank verwenden, wird die Bereitstellung der Datenbank vollständig aufgehoben. Wenn die Datenbank an anderen Synchronisierungsgruppen beteiligt ist, werden die anderen Synchronisierungsgruppen durch diesen Vorgang beschädigt.

#### <a name="usage"></a>Verwendung

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Beispiel

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Aktualisieren von Anmeldeinformationen

#### <a name="usage"></a>Verwendung

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Beispiele

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur SQL-Datensynchronisierung finden Sie in den folgenden Artikeln:

-   Übersicht: [Synchronisieren von Daten über mehrere Cloud- und lokale Datenbanken mit SQL-Datensynchronisierung in Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Einrichten der Datensynchronisierung
    - Im Portal – [Tutorial: Einrichten der SQL-Datensynchronisierung zum Synchronisieren von Daten zwischen Azure SQL-Datenbank und SQL Server](sql-data-sync-sql-server-configure.md)
    - Mit PowerShell
        -  [Verwenden von PowerShell zum Synchronisieren zwischen mehreren Datenbanken in Azure SQL-Datenbank](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Verwenden von PowerShell zum Synchronisieren zwischen einer Datenbank in Azure SQL-Datenbank und einer Datenbank in einer SQL Server-Instanz](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Bewährte Methoden: [Bewährte Methoden für die Azure SQL-Datensynchronisierung](sql-data-sync-best-practices.md)
-   Überwachung: [Überwachen der SQL-Datensynchronisierung mit Azure Monitor-Protokollen](./monitor-tune-overview.md)
-   Problembehandlung: [Behandeln von Problemen mit der Azure SQL-Datensynchronisierung]sql-data-sync-troubleshoot.md)
-   Aktualisieren des Synchronisierungsschemas
    -   Mit Transact-SQL: [Automatisieren der Replikation von Schemaänderungen mit der SQL-Datensynchronisierung in Azure](sql-data-sync-update-sync-schema.md)
    -   Mit PowerShell: [Verwenden von PowerShell zum Aktualisieren des Synchronisierungsschemas in einer bestehenden Synchronisierungsgruppe](scripts/update-sync-schema-in-sync-group.md)