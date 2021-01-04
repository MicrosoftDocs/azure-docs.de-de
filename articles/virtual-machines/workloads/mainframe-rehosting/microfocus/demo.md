---
title: Einrichten von Micro Focus CICS BankDemo für Micro Focus Enterprise Developer 4.0 auf virtuellen Azure-Computern
description: Führen Sie die Micro Focus BankDemo-Anwendung auf virtuellen Azure-Computern aus, um zu erfahren, wie Sie Micro Focus Enterprise Server und Enterprise Developer verwenden.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 7fb72b9a7d0d655f99d1e5cf194f7c6f26976a37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95976192"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Einrichten von Micro Focus CICS BankDemo für Micro Focus Enterprise Developer 4.0 in Azure

Wenn Sie Micro Focus Enterprise Server 4.0 und Enterprise Developer 4.0 in Azure einrichten, können Sie Bereitstellungen von IBM z/OS-Workloads testen. In diesem Artikel wird erläutert, wie Sie CICS BankDemo einrichten – eine Beispielanwendung, die im Lieferumfang von Enterprise Developer enthalten ist.

CICS steht für „Customer Information Control System“, die Transaktionsplattform, die von vielen wichtigen Onlineanwendungen verwendet wird. Mit der BankDemo-Anwendung lässt sich sehr gut veranschaulichen, wie Enterprise Server und Enterprise Developer funktionieren und wie Sie eine tatsächliche Anwendung einschließlich Endkundenterminals verwalten und bereitstellen.

> [!NOTE]
> In Kürze verfügbar: Anweisungen zum Einrichten von [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) auf Azure-VMs.

## <a name="prerequisites"></a>Voraussetzungen

- Eine VM mit [Enterprise Developer](set-up-micro-focus-azure.md). Beachten Sie, dass Enterprise Developer zu Entwicklungs- und Testzwecken mit einer vollständigen Instanz von Enterprise Server ausgeliefert wird. Diese Instanz ist die Enterprise Server-Instanz, die für diese Demo verwendet wird.

- [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Laden Sie die Software auf die Enterprise Developer-VM herunter, und installieren Sie sie. Enterprise Server erfordert eine Datenbank für die Verwaltung von CICS-Regionen, und die BankDemo-Anwendung verwendet auch eine SQL Server-Datenbank namens BANKDEMO. Bei dieser Demo wird davon ausgegangen, dass Sie SQL Server Express für beide Datenbanken verwenden. Wählen Sie beim Installationsprozess die Standardinstallation aus.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS wird verwendet, um die Datenbanken zu verwalten und ein T-SQL-Skript auszuführen. Laden Sie die Software auf die Enterprise Developer-VM herunter, und installieren Sie sie.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) mit dem neuesten Service Pack oder [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/), das Sie kostenlos herunterladen können.

- Rumba Desktop oder einen anderen 3270-Emulator.

## <a name="configure-the-windows-environment"></a>Konfigurieren der Windows-Umgebung

Nachdem Sie Enterprise Developer 4.0 auf der VM installiert haben, müssen Sie die mitgelieferte Enterprise Server-Instanz konfigurieren. Zu diesem Zweck müssen Sie einige weitere Windows-Features installieren, wie im Folgenden beschrieben.

1. Melden Sie sich über RDP bei der Enterprise Server 4.0-VM an, die Sie erstellt haben.

2. Klicken Sie auf das Symbol **Suchen** neben der Schaltfläche **Start**, und geben Sie **Windows-Features** ein. Der Server-Manager-Assistent zum Hinzufügen von Rollen und Features wird geöffnet.

3. Wählen Sie die Rolle **Webserver (IIS)** aus, und aktivieren Sie dann die folgenden Optionen:

    - Webverwaltungstools
    - Kompatibilität mit der IIS 6-Verwaltung (wählen Sie alle verfügbaren Features aus)
    - IIS-Verwaltungskonsole
    - IIS-Verwaltungsskripts und -tools
    - IIS-Verwaltungsdienst

4. Wählen Sie **WWW-Dienste** aus, und aktivieren Sie die folgenden Optionen:

     Anwendungsentwicklungsfeatures:
    - .NET-Erweiterbarkeit
    - ASP.NET
    - Allgemeine HTTP-Features: Fügen Sie alle verfügbaren Features hinzu
    - Systemzustand und Diagnose: Fügen Sie alle verfügbaren Features hinzu
    - Sicherheit:
        - Standardauthentifizierung
        - Windows-Authentifizierung

5. Wählen Sie **Windows-Prozessaktivierungsdienst** und alle untergeordneten Elemente aus.

6. Aktivieren Sie für **Features** den Eintrag **Microsoft .NET Framework 3.5.1** sowie die folgenden Optionen:

    - Windows Communication Foundation – HTTP-Aktivierung
    - Windows Communication Foundation – Nicht-HTTP-Aktivierung

7. Aktivieren Sie für **Features** den Eintrag **Microsoft .NET Framework 4.6.** sowie die folgenden Optionen:

   - Named Pipe-Aktivierung
   - TCP-Aktivierung
   - TCP-Portfreigabe

     ![Assistent zum Hinzufügen von Rollen und Features: Rollendienste](media/01-demo-roles.png)

8. Wenn Sie alle Optionen ausgewählt haben, wählen Sie **Weiter**, um mit der Installation zu beginnen.

9. Navigieren Sie nach der Installation der Windows-Features zu **Systemsteuerung \> System und Sicherheit \> Verwaltungstools**, und wählen Sie **Dienste** aus. Scrollen Sie nach unten, und stellen Sie sicher, dass die folgenden Dienste ausgeführt werden und auf **Automatisch** festgelegt sind:

    - **NetTcpPortSharing**
    - **Net.Pipe-Listeneradapter**
    - **Net.Tcp-Listeneradapter**

10. Um die IIS- und WAS-Unterstützung zu konfigurieren, suchen Sie im Menü die **Micro Focus Enterprise Developer-Eingabeaufforderung (64 Bit)** , und führen Sie diese als **Administrator** aus.

11. Geben Sie **wassetup -i** ein, und drücken Sie die **EINGABETASTE**.

12. Nachdem das Skript ausgeführt wurde, können Sie das Fenster schließen.

## <a name="configure-the-local-system-account-for-sql-server"></a>Konfigurieren des lokalen Systemkontos für SQL Server

Einige Enterprise Server-Prozesse müssen sich bei SQL Server anmelden und Datenbanken sowie weitere Objekte erstellen. Diese Prozesse verwenden das lokale Systemkonto, daher müssen Sie diesem Konto Systemadministratorrechte erteilen.

1. Starten Sie **SSMS**, und klicken Sie auf **Verbinden**, um mithilfe der Windows-Authentifizierung eine Verbindung mit der lokalen SQLEXPRESS Server-Instanz herzustellen. Die Instanz sollte in der Liste **Servername** verfügbar sein.

2. Erweitern Sie auf der linken Seite den Ordner **Sicherheit**, und wählen Sie **Anmeldungen** aus.

3. Wählen Sie **NT AUTHORITY\\SYSTEM** und dann **Eigenschaften** aus.

4. Wählen Sie **Serverrollen** aus, und aktivieren Sie das Kontrollkästchen **sysadmin**.

     ![SSMS-Objekt-Explorer-Fenster: Anmeldenameneigenschaften](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>Erstellen der BankDemo-Datenbank und aller zugehörigen Objekte

1. Öffnen Sie den **Windows Explorer**, und navigieren Sie zu **C:\\Benutzer\\Öffentlich\\Dokumente\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo\\SQL**.

2. Kopieren Sie den Inhalt der Datei **BankDemoCreateAll.SQL** in die Zwischenablage.

3. Öffnen Sie **SSMS**. Klicken Sie auf der rechten Seite auf **Server**, und wählen Sie **Neue Abfrage** aus.

4. Fügen Sie den Inhalt der Zwischenablage in das Feld **Neue Abfrage** ein.

5. Führen Sie die SQL-Abfrage aus, indem Sie oberhalb der Abfrage auf der Registerkarte **Befehl** auf **Ausführen** klicken.

Die Abfrage sollte fehlerfrei ausgeführt werden. Nach Abschluss verfügen Sie über die Beispieldatenbank für die BankDemo-Anwendung.

![Ausgabe von „SQLQuery1.sql“](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Überprüfen, ob die Datenbanktabellen und -objekte erstellt wurden

1. Klicken Sie mit der rechten Maustaste auf die Datenbank **BANKDEMO**, und wählen Sie **Aktualisieren** aus.

2. Erweitern Sie die **Datenbank**, und wählen Sie **Tabellen** aus. Eine Ansicht wie die folgende sollte zu sehen sein.

     ![Erweiterte BANKDEMO-Tabelle im Objekt-Explorer](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>Erstellen der Anwendung in Enterprise Developer

1. Öffnen Sie Visual Studio, und melden Sie sich an.

2. Wählen Sie unter der Menüoption **Datei** den Eintrag **Projekt/Projektmappe öffnen** aus, navigieren Sie zu **C:\\Benutzer\\Öffentlich\\Dokumente\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**, und wählen Sie die Datei **sln** aus.

3. Nehmen Sie sich etwas Zeit, um die Objekte zu untersuchen. COBOL-Programme werden im Projektmappen-Explorer mit der Erweiterung CBL sowie als CopyBooks (CPY) und im JCL-Format angezeigt.

4. Klicken Sie mit der rechten Maustaste auf das **BankDemo2**-Projekt, und wählen Sie **Als Startprojekt festlegen** aus.

    > [!NOTE]
    > Das BankDemo-Projekt nutzt HCOSS (Hostkompatibilitätsoption für SQL Server), die für diese Demo aber nicht verwendet wird.

5. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **BankDemo2**, und wählen Sie **Erstellen** aus.

    > [!NOTE]
    > Die Erstellung auf Projektmappenebene führt zu Fehlern, da HCOSS nicht konfiguriert wurde.

6. Wenn das Projekt erstellt wurde, sehen Sie sich das Fenster **Ausgabe** an. Es sollte in etwa wie die folgende Abbildung aussehen.

     ![Ausgabefenster mit erfolgreicher Erstellung](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>Bereitstellen der BankDemo-Anwendung in der Regionsdatenbank

1. Öffnen Sie eine Enterprise Developer-Eingabeaufforderung (64 Bit) als Administrator.

2. Navigieren Sie zu **%PUBLIC%\\Documents\\Micro Focus\\Enterprise Developer\\samples\\Mainframe\\CICS\\DotNet\\BankDemo**.

3. Führen Sie an der Eingabeaufforderung den Befehl **bankdemodbdeploy** aus, und geben Sie den Parameter für den Bereitstellungsort der Datenbank an, z.B.:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Stellen Sie sich, dass Sie einen Schrägstrich verwenden, keinen umgekehrten Schrägstrich (\\). Die Ausführung dieses Skripts dauert eine Weile.

![Verwaltung: Fenster mit Enterprise Developer-Eingabeaufforderung](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>Erstellen der BankDemo-Region in Enterprise Administrator für .NET

1. Öffnen Sie die Benutzeroberfläche **Enterprise Server for .NET Administration**.

2. Um das MMC-Snap-In zu starten, wählen Sie im Windows-**Startmenü** den Eintrag **Micro Focus Enterprise Developer \> Configuration \> Enterprise Server for .NET Admin** aus. (Unter Windows Server wählen Sie **Micro Focus Enterprise Developer \> Enterprise Server for .NET Admin** aus).

3. Erweitern Sie im linken Bereich den Container **Regions** (Regionen), und klicken Sie mit der rechten Maustaste auf **CICS**.

4. Wählen Sie **Define Region** (Region definieren) aus, um eine neue CICS-Region namens **BANKDEMO** zu erstellen, die in der (lokalen) Datenbank gehostet wird.

5. Geben Sie die Datenbankserverinstanz an, klicken Sie auf **Weiter**, und geben Sie dann den Regionsnamen **BANKDEMO** ein.

     ![Dialogfeld zum Definieren der Region](media/07-demo-cics.png)

6. Um die Regionsdefinitionsdatei für die regionsübergreifende Datenbank auszuwählen, suchen Sie die Datei **region\_bankdemo\_db.config** im Pfad **C:\\Benutzer\\Öffentlich\\Dokumente\\Micro Focus\\Enterprise Developer\\Samples\\Mainframe\\CICS\\DotNet\\BankDemo**.

     ![Definieren der Region – Eingeben des Regionsnamens BANKDEMO](media/08-demo-cics.png)

7. Klicken Sie auf **Fertig stellen**.

## <a name="create-xa-resource-definitions"></a>Erstellen von XA-Ressourcendefinitionen

1. Erweitern Sie auf der linken Seite der Benutzeroberfläche **Enterprise Server for .NET Administration** den Eintrag **System** und dann den Eintrag **XA Resource Definitions** (XA-Ressourcendefinitionen). Diese Einstellungen legen fest, wie die Region mit Enterprise Server und den Anwendungsdatenbanken interagiert.

2. Klicken Sie mit der rechten Maustaste auf **XA Resource Definitions** (XA-Ressourcendefinitionen), und wählen Sie **Add Server Instance** (Serverinstanz hinzufügen) aus.

3. Wählen Sie im Dropdownfeld den Eintrag **Database Service Instance** (Datenbankdienstinstanz) aus. Dies ist SQLEXPRESS auf dem lokalen Computer.

4. Wählen Sie aus dem Container **XA Resource Definitions (machinename\\sqlexpress)** (XA-Ressourcendefinitionen (machinename\sqlexpress)) die Instanz aus, und klicken Sie auf **Add** (Hinzufügen).

5. Wählen Sie **Database XA Resource Definition** (XA-Ressourcendefinition für Datenbank) aus, und geben Sie **BANKDEMO** als **Name** und **Region** ein.

     ![Bildschirm für neue XA-Ressourcendefinition für Datenbank](media/09-demo-xa.png)

6. Klicken Sie auf die Auslassungspunkte ( **…** ), um den Assistenten „Connection String“ (Verbindungszeichenfolge) zu öffnen. Geben Sie unter **Server Name** (Servername) die Zeichenfolge **(local)\\SQLEXPRESS** ein. Wählen Sie als **Logon** (Anmeldung) die Option **Windows Authentication** (Windows-Authentifizierung) aus. Geben Sie **BANKDEMO** als Datenbanknamen ein.

     ![Bildschirm zum Bearbeiten der Verbindungszeichenfolge](media/10-demo-string.png)

7. Testen Sie die Verbindung.

## <a name="start-the-bankdemo-region"></a>Starten der Region BANKDEMO

> [!NOTE]
> Der erste Schritt ist wichtig: Sie müssen die Region so einrichten, dass sie die von Ihnen soeben erstellte XA-Ressourcendefinition verwendet.

1. Navigieren Sie im Container **Regions** (Regionen) zu **BANKDEMO CICS Region**, und wählen Sie im Bereich **Actions** (Aktionen) die Option **Edit Region Startup File** (Startdatei für Region bearbeiten) aus. Scrollen Sie nach unten zu den SQL-Eigenschaften, und geben Sie **bankdemo** als **XA resource name** (XA-Ressourcenname) ein, oder verwenden Sie die Auslassungspunkte, um den Namen auszuwählen.

2. Klicken Sie auf das Symbol zum **Speichern**, um die Änderungen zu speichern.

3. Klicken Sie im Bereich **Console** (Konsole) auf **BANKDEMO CICS Region**, und wählen Sie **Start/Stop Region** (Region starten/anhalten) aus.

4. Wählen Sie im unteren Bereich des Felds **Start/Stop Region** (Region starten/beenden), das im mittleren Bereich angezeigt wird, die Option **Start** aus. Nach wenigen Sekunden wird die Region gestartet.

     ![SQL-Feld zum Starten/Beenden](media/11-demo-sql.png)

     ![Bildschirm für gestartete CICS-Region für BANKDEMO](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Erstellen eines Listeners

Erstellen Sie einen Listener für die TN3270-Sitzungen, die auf die BankDemo-Anwendung zugreifen.

1. Erweitern Sie im linken Bereich **Configuration Editors** (Konfigurations-Editoren), und wählen Sie **Listener** aus.

2. Klicken Sie auf das Symbol **Open File** (Datei öffnen), und wählen Sie die Datei **seelistener.exe.config** aus. Diese Datei wird bearbeitet und bei jedem Start von Enterprise Server geladen.

3. Beachten Sie die beiden zuvor definierten Regionen (ESDEMO und JCLDEMO).

4. Um eine neue Region für BANKDEMO zu erstellen, klicken Sie mit der rechten Maustaste auf **Regions** (Regionen), und wählen Sie **Add Region** (Region hinzufügen) aus.

5. Wählen Sie die Region **BANKDEMO** aus.

6. Fügen Sie einen TN3270-Kanal hinzu, indem Sie mit der rechten Maustaste auf die Region **BANKDEMO** klicken und **Add Channel** (Kanal hinzufügen) auswählen.

7. Geben Sie unter **Name** die Zeichenfolge **TN3270** ein. Geben Sie als **Port** den Wert **9024** ein. Die ESDEMO-Anwendung verwendet Port 9230, weshalb Sie einen anderen Port verwenden müssen.

8. Klicken Sie zum Speichern der Datei auf das Symbol zum **Speichern**, oder klicken Sie auf **Datei** \> **Speichern**.

9. Klicken Sie zum Starten des Listeners auf das Symbol **Start Listener** (Listener starten), oder klicken Sie auf **Options**\>**Start Listener** (Optionen > Listener starten).

     ![Fenster für Listenerkonfigurations-Editor](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Konfigurieren von Rumba für den Zugriff auf die BankDemo-Anwendung

Als Letztes müssen Sie mithilfe des 3270-Emulators Rumba eine 3270-Sitzung konfigurieren. Mit diesem Schritt ermöglichen Sie den Zugriff auf die BankDemo-Anwendung über den erstellten Listener.

1. Starten Sie Rumba Desktop über das Windows-**Startmenü**.

2. Wählen Sie im Menüelement **Connections** (Verbindungen) den Eintrag **TN3270** aus.

3. Klicken Sie auf **Insert** (Einfügen), und geben Sie **127.0.0.1** als IP-Adresse und **9024** als benutzerdefinierten Port ein.

4. Klicken Sie im unteren Bereich des Dialogfelds auf **Connect** (Verbinden). Ein schwarzer CICS-Bildschirm wird angezeigt.

5. Geben Sie **bank** ein, um den 3270-Startbildschirm für die BankDemo-Anwendung anzuzeigen.

6. Geben Sie **B0001** als Benutzer-ID und eine beliebige Zeichenfolge für das Kennwort ein. Der erste Bildschirm wird geöffnet: BANK20.

![Mainframeanzeige: Willkommensbildschirm](media/14-demo.png)
![Mainframeanzeige: Rumba – Bildschirm für Demonstration im Subsystem](media/15-demo.png)

Glückwunsch! Sie führen jetzt eine CICS-Anwendung in Azure aus, die Micro Focus Enterprise Server verwendet.

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen von Enterprise Server in Docker-Containern in Azure](run-enterprise-server-container.md)
- [Mainframemigration – Portal](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Virtuelle Computer](../../../linux/overview.md)
- [Problembehandlung](../../../troubleshooting/index.yml)
- [Demystifying mainframe to Azure migration](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/) (Beseitigen von Irrtümern in Bezug auf die Migration von Mainframesystemen zu Azure)
