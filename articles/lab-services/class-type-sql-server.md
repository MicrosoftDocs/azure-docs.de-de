---
title: Einrichten eines Labs zum Verwalten und Entwickeln mit Azure SQL-Datenbank | Azure Lab Services
description: Erfahren Sie, wie Sie ein Lab zum Verwalten und Entwickeln mit Azure SQL-Datenbank einrichten.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 50f71ee1ce59f5809fe8905c58f0399cf484f11a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659708"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>Einrichten eines Labs zum Verwalten und Entwickeln mit SQL Server

In diesem Artikel wird beschrieben, wie Sie ein Lab für einen Grundkurs zur Verwaltung und Entwicklung mit SQL Server in Azure Lab Services einrichten.  Datenbankkonzepte werden in den Einführungskursen vermittelt, die in den meisten Informatikfachbereichen an Hochschulen angeboten werden. Structured Query Language (SQL) ist ein internationaler Standard.  SQL ist die Standardsprache für die Verwaltung relationaler Datenbank; hierzu zählen das Hinzufügen, Aufrufen und Verwalten von Inhalten in einer Datenbank.  Ihre größten Vorteile liegen in der schnellen Verarbeitung, erwiesenen Zuverlässigkeit und flexiblen Nutzung.

In diesem Artikel erfahren Sie, wie Sie eine Vorlage für VMs in einem Lab mit [Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)und [Azure Data Studio](https://github.com/microsoft/azuredatastudio) einrichten.  Für das gesamte Lab verwenden wir eine freigegebene [SQL Server Datenbank](../azure-sql/database/sql-database-paas-overview.md)-Instanz. [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md) ist ein PaaS-Datenbank-Engine-Angebot (Platform-as-a-Service) von Azure.

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten dieses Labs benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie ein neues Lab-Konto in Azure Lab Services erstellen. Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie im [Tutorial zum Einrichten eines Lab-Kontos](./tutorial-setup-lab-account.md). Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen für das Lab-Konto, die in der nachfolgenden Tabelle beschrieben werden. Weitere Informationen zum Aktivieren von Marketplace-Images finden Sie unter [Angeben von für Lab-Ersteller verfügbaren Marketplace-Images](specify-marketplace-images.md).

| Lab-Kontoeinstellungen | Instructions |
| ------------------- | ------------ |
| Marketplace-Image | Aktivieren Sie das Image „Visual Studio 2019 Community (aktuelles Release) unter Windows 10 Enterprise N (x64)“ zur Verwendung in Ihrem Labkonto. |

### <a name="shared-resource-configuration"></a>Konfiguration mit freigegebener Ressource

Wenn Sie eine freigegebene Ressource in Lab Services verwenden möchten, müssen Sie zunächst das virtuelle Netzwerk und die Ressourcen selbst erstellen.  Um das virtuelle Netzwerk zu erstellen und mit dem Lab zu verbinden, befolgen Sie die Anweisungen in [Erstellen eines Labs mit einer freigegebenen Ressource in Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Denken Sie daran, dass alle Ressourcen, die sich außerhalb von Lab Services befinden, separat abgerechnet und nicht in die Kostenschätzungen des Labs eingeschlossen werden.

>[!WARNING]
>Freigegebene Ressourcen für ein Lab sollten vor der Erstellung des Labs eingerichtet werden.  Wenn für das VNET nicht *vor* der Erstellung des Labs das [Peering mit dem Labkonto](how-to-connect-peer-virtual-network.md) durchgeführt wird, hat das Lab keinen Zugriff auf die freigegebene Ressource.

Nachdem nun die Netzwerkaspekte berücksichtigt wurden, wird eine SQL Server-Datenbank-Instanz erstellt.  Wir erstellen eine [Einzeldatenbank](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal), denn dies ist die schnellste Bereitstellungsoption für Azure SQL-Datenbank.  Erstellen Sie für andere Bereitstellungsoptionen einen [Pool für elastische Datenbanken](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), eine [verwaltete Instanz](../azure-sql/managed-instance/instance-create-quickstart.md) oder einen [virtuellen SQL-Computer](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md).

1. Wählen Sie im Menü des Azure-Portals die Option **Neue Ressource erstellen** aus.
2. Wählen Sie **SQL-Datenbank** aus, und klicken Sie auf die Schaltfläche **Erstellen**.
3. Wählen Sie auf der Registerkarte **Grundlagen** des Formulars **SQL-Datenbank erstellen** die Ressourcengruppe für die Datenbank aus.  Wir verwenden *sqldb-rg*.
4. Geben Sie als **Datenbankname** *classlabdb* ein.
5. Klicken Sie unter der Einstellung **Server** auf **Neu erstellen**, um einen neuen Server zum Speichern der Datenbank zu erstellen.
6. Geben Sie auf dem Flyout **Neuer Server** den Servernamen ein.  Wir verwenden *classlabdbserver*.  Der Servername muss global eindeutig sein.
7. Geben Sie *azureuser* für **Serveradministratoranmeldung** ein.
8. Geben Sie ein Kennwort ein, das Sie sich leicht merken können.  Das Kennwort muss mindestens acht Zeichen lang sein und Sonderzeichen enthalten.
9. Wählen Sie eine Region als **Speicherort** aus.  Wenn möglich, verwenden Sie den Speicherort des Labkontos und des VNET, für das ein Peering durchgeführt wurde, um die Latenz zu minimieren.
10. Klicken Sie auf **OK**, um zum Formular **SQL-Datenbank erstellen** zurückzukehren.
11. Klicken Sie auf den Link **Datenbank konfigurieren** unter der Einstellung **Compute + Speicher**.
12. Ändern Sie die Datenbankeinstellungen für die Klasse nach Bedarf.  Sie können zwischen den Optionen „Bereitgestellt“ und „Serverlos“ wählen.  In diesem Beispiel verwenden wir die automatisch skalierte Option „Serverlos“ mit 4 für „Max. virtuelle Kerne“ und 1 für „Min. virtuelle Kerne“. Wir behalten für automatisches Anhalten die Einstellung von mindestens 1 Stunde bei. Klicken Sie auf **Anwenden**.
13. Klicken Sie auf **Weiter: Netzwerk**.
14. Wählen Sie auf der Registerkarte „Netzwerk“ die Option „Privater Endpunkt“ für die **Konnektivitätsmethode** aus.
15. Klicken Sie im Abschnitt **Private Endpunkte** auf **Privaten Endpunkt hinzufügen**.
16. Wählen Sie auf dem Flyout **Privaten Endpunkt erstellen** die Ressourcengruppe des virtuellen Netzwerks, für das ein Peering mit dem Labkonto durchgeführt wurde.
17. Verwenden Sie für **Speicherort** denselben Speicherort wie für das virtuelle Netzwerk.
18. Geben Sie *labsql-endpt* für **Name** ein.
19. Belassen Sie „Unterressource des Ziels“ auf SqlServer.
20. Wählen Sie für **Virtuelles Netzwerk** das virtuelle Netzwerk aus, für das ein Peering mit dem Labkonto durchgeführt wurde.
21. Wählen Sie für **Subnetz** das Subnetz aus, in dem der Endpunkt gehostet werden soll.  Die IP-Adresse, die dem Endpunkt zugewiesen ist, wird aus dem Bereich abgeleitet, der diesem Subnetz zugewiesen ist.
22. Setzen Sie **In private DNS-Zone integrieren** auf **Nein** fest. Der Einfachheit halber verwenden wir statt der eigenen privaten DNS-Zone oder unseres eigenen DNS-Servers den Azure-DNS.
23. Klicken Sie auf **OK**.
24. Klicken Sie auf **Weiter: Zusätzliche Einstellungen**.
25. Wählen Sie für die Einstellung **Vorhandene Daten verwenden** die Option **Beispiel** aus.  Die Daten aus der AdventureWorksLT-Datenbank werden verwendet, wenn die Datenbank erstellt wird.
26. Klicken Sie auf **Überprüfen + erstellen**.
27. Klicken Sie auf **Erstellen**.

Nach erfolgreichem Abschluss der SQL-Datenbank-Bereitstellung können wir das Lab erstellen und die Software auf dem Labvorlagencomputer installieren.

### <a name="lab-settings"></a>Lab-Einstellungen

Verwenden Sie beim Einrichten eines Classroom-Labs die Einstellungen in der unten stehenden Tabelle. Weitere Informationen zum Erstellen eines Classroom-Labs finden Sie im Tutorial [Einrichten eines Classroom-Labs](tutorial-setup-classroom-lab.md).

| Lab-Einstellungen | Wert/Anweisungen |
| ------------ | ------------------ |
| VM-Größe | Mittel. Diese Größe eignet sich am besten für relationale Datenbanken, speicherinternes Caching und Analysen. |
| VM-Image | Visual Studio 2019 Community (aktuelles Release) unter Windows 10 Enterprise N (x64) |

Nachdem wir nun das Lab erstellt haben, ändern wir den Vorlagencomputer mit der benötigten Software.

## <a name="visual-studio"></a>Visual Studio

Das oben ausgewählte Image umfasst [Visual Studio 2019 Community](https://visualstudio.microsoft.com/vs/community/).  Alle Workloads und Toolsets sind bereits auf dem Image installiert.  Verwenden Sie den Visual Studio-Installer, um [ggf. benötigte optionale Tools zu installieren](/visualstudio/install/modify-visual-studio?view=vs-2019).  [Melden Sie sich bei Visual Studio an](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio), um die Community-Edition zu entsperren.

Visual Studio enthält das Toolset zur **Datenspeicherung und -verarbeitung**, das SQL Server Data Tools (SSDT) enthält.  Weitere Informationen zu den SSDT-Funktionen finden Sie unter [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools?view=sql-server-ver15).  Informationen zum Überprüfen, ob die Verbindung mit dem freigegebenen SQL Server für die Klasse erfolgreich ist, finden Sie unter [Gewusst wie: Herstellen einer Verbindung mit einer Datenbank und Durchsuchen vorhandener Objekte](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15). Wenn Sie dazu aufgefordert werden, fügen Sie die IP-Adresse des Vorlagencomputers der [Liste zulässiger Computer](../azure-sql/database/firewall-configure.md) hinzu, die eine Verbindung mit Ihrer SQL Server-Instanz herstellen können.

Visual Studio unterstützt mehrere Workloads einschließlich **Web und Cloud** und **Desktop und Mobil**.  Beide Workloads unterstützen SQL Server als Datenquelle. Weitere Informationen zur Verwendung von ASP.NET Core in SQL Server finden Sie im Tutorial [Erstellen einer ASP.NET Core- und SQL-Datenbank-App in Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md).  Stellen Sie mit der [System.Data.SqlClient](/dotnet/api/system.data.sqlclient)-Bibliothek eine Verbindung mit einer SQL-Datenbank-Instanz von einer [Xamarin](/xamarin)-App her.

## <a name="install-azure-data-studio"></a>Installieren von Azure Data Studio

[Azure Data Studio](https://github.com/microsoft/azuredatastudio) ist eine plattformübergreifende Desktopumgebung mit mehreren Datenbanken für Datenexperten, die die Familie lokaler und cloudbasierter Datenplattformen unter Windows, macOS und Linux verwenden.

1. Laden Sie das [*System*-Installationsprogramm für Windows für Azure Data Studio](https://go.microsoft.com/fwlink/?linkid=2127432) herunter. Installationsprogramme für andere unterstützte Betriebssysteme finden Sie auf der Downloadseite von [Azure Data Studio](/sql/azure-data-studio/download).
2. Wählen Sie auf der Seite **Lizenzvertrag** die Option **Ich stimme dem Lizenzvertrag zu**. Klicken Sie auf **Weiter**.
3. Klicken Sie auf der Seite **Zielspeicherort auswählen** auf **Weiter**.
4. Klicken Sie auf der Seite **Startmenüordner auswählen** auf **Weiter**.
5. Aktivieren Sie auf der Seite **Weitere Aufgaben auswählen** die Option **Desktopsymbol erstellen**, wenn ein Desktopsymbol angezeigt werden soll.  Klicken Sie auf **Weiter**.
6. Klicken Sie für **Bereit zur Installation** auf **Weiter**.
7. Warten Sie die Ausführung des Installationsprogramms ab.  Klicken Sie auf **Fertig stellen**.

Nachdem Sie nun Azure Data Studio installiert haben, können Sie die Verbindung mit Azure SQL-Datenbank herstellen.

1. Klicken Sie auf der Seite **Willkommen** von Azure Data Studio auf den Link **Neue Verbindung**.
2. Geben Sie im Feld **Verbindungsdetails** die erforderlichen Informationen ein.
    - Setzen Sie **Server** auf *classlabdbserver.database.windows.net*.
    - Legen Sie für **Benutzer** den Namen *azureuser* fest.
    - Legen Sie für **Kennwort** das zum Erstellen der Datenbank verwendete Kennwort fest.
    - Aktivieren Sie **Kennwort speichern**.
    - Wählen Sie für **Datenbank** *classlabdb*.
3. Klicken Sie auf **Verbinden**.

## <a name="install-sql-server-management-studio"></a>Installieren von SQL Server Management Studio

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) ist eine integrierte Umgebung zum Verwalten jeder beliebigen SQL-Infrastruktur.  SSMS wird von Datenbankadministratoren zum Bereitstellen, Überwachen und Aktualisieren der Dateninfrastruktur verwendet.

1. [Herunterladen von SQL Server Management Studio](https://aka.ms/ssmsfullsetup) Starten Sie nach dem Herunterladen das Installationsprogramm.
2. Klicken Sie auf der Seite **Willkommen** auf **Installieren**.
3. Klicken Sie auf der Seite **Setup abgeschlossen** auf **Schließen**.
4. Starten Sie SQL Server Management Studio.  
5. Klicken Sie auf der Seite **Konfigurationsprozess für Abhängigkeiten** auf **Schließen**.

Da SSMS jetzt installiert ist, können Sie eine [Verbindung mit einer SQL Server-Instanz herstellen und sie abfragen](/sql/ssms/tutorials/connect-query-sql-server). Verwenden Sie beim Einrichten der Verbindung die folgenden Werte:

- Servertyp: Datenbank-Engine
- Servername: *classlabdbserver.database.windows.net*
- Authentifizierung: SQL Server-Authentifizierung
- Anmeldung: *azureuser*
- Kennwort: das zum Erstellen der Datenbank verwendete Kennwort.

## <a name="cost-estimate"></a>Kostenschätzung

Betrachten wir eine mögliche Kostenschätzung für diesen Kurs. Diese Schätzung umfasst nicht die Kosten für das Ausführen der SQL Server-Instanz.  Aktuelle Informationen zu den Datenbankpreisen finden Sie unter [Preise für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database).

Wir setzen einen Kurs mit 25 Kursteilnehmern an. Es ist eine Kursdauer von 20 Stunden geplant. Zudem erhält jeder Kursteilnehmer ein Kontingent von 10 Stunden für Hausaufgaben und Aufgaben außerhalb der regulären Kurszeiten. Als Größe des virtuellen Computers wurde „mittel“ ausgewählt, das entspricht 42 Lab-Einheiten.

Hier ist ein Beispiel für eine mögliche Kostenschätzung für diesen Kurs:

25 Kursteilnehmer \* (20 geplante Stunden \+ 10 Kontingentstunden) * 0,42 USD pro Stunde = 315,00 USD

>[!IMPORTANT]
>Diese Kostenschätzung dient ausschließlich zu Beispielzwecken. Weitere Informationen zu den aktuellen Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Einrichtungsschritte sind für alle Labs gleich.

- [Erstellen und Verwalten einer Classroom-Vorlage in Azure Lab Services](how-to-create-manage-template.md)
- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)