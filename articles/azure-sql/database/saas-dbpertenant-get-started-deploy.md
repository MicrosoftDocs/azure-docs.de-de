---
title: SaaS-Tutorial für eine Datenbank pro Mandant
description: Erfahren Sie, wie Sie die mehrinstanzenfähige Wingtip Tickets-SaaS-Anwendung, mit der das Muster mit einer Datenbank pro Mandant und andere SaaS-Muster mithilfe von Azure SQL-Datenbank dargestellt werden, bereitstellen und erkunden.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 497e714289c834e026c6b9b767ed2b7af5442783
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780834"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Bereitstellen und Kennenlernen einer mehrinstanzenfähigen SaaS-App, die das Muster mit einer Datenbank pro Mandant mit Azure SQL-Datenbank verwendet

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial stellen Sie Wingtip Tickets als SaaS-Anwendung mit dem Muster Datenbank pro Mandant bereit und machen sich damit vertraut (Wingtip). Die App verwendet ein Muster mit einer Datenbank pro Mandant, um die Daten mehrerer Mandanten zu speichern. Die App ist dafür ausgelegt, Features von Azure SQL-Datenbank zu veranschaulichen, mit denen die Unterstützung von SaaS-Szenarien vereinfacht wird.

Fünf Minuten nach dem Auswählen von **Bereitstellung in Azure** verfügen Sie über eine mehrinstanzenfähige SaaS-Anwendung. Die App umfasst eine ­Datenbank, die in der Azure SQL-Datenbank ausgeführt wird. Die App wird mit drei Beispielmandanten bereitgestellt, die jeweils über eine eigene Datenbank verfügen. Alle Datenbanken werden in einem Pool für elastische SQL-Datenbanken bereitgestellt. Die App wird unter Ihrem Azure-Abonnement bereitgestellt. Sie haben Vollzugriff auf die einzelnen App-Komponenten, um sich damit vertraut zu machen und zu arbeiten. Der C#-Quellcode der Anwendung und die Verwaltungsskripts sind im GitHub-Repository [WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt] verfügbar.

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
> - Bereitstellen der Wingtip-SaaS-Anwendung
> - Abrufen des Quellcodes der Anwendung und der Verwaltungsskripts
> - Die Server, Pools und Datenbanken, aus denen sich die App zusammensetzt
> - Zuordnen der Mandanten zu den zugehörigen Daten mithilfe des *Katalogs*
> - Bereitstellen eines neuen Mandanten
> - Überwachen der Mandantenaktivität in der App

Eine [Reihe verwandter Tutorials](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) bietet die Möglichkeit, verschiedene SaaS-Entwurfs- und -Verwaltungsmuster kennenzulernen. Die Tutorials gehen über diese Erstbereitstellung hinaus. Während der Tutorials erfahren Sie anhand der bereitgestellten Skripts, wie die verschiedenen SaaS-Muster implementiert werden. Die Skripts veranschaulichen, wie die Entwicklung von SaaS-Anwendungen durch Funktionen von SQL-Datenbank vereinfacht wird.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial muss Azure PowerShell installiert sein. Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Bereitstellen des Wingtip Tickets SaaS-Anwendung

### <a name="plan-the-names"></a>Planen von Namen

In den Schritten in diesem Abschnitt geben Sie einen Benutzerwert an, mit dem sichergestellt wird, dass Ressourcennamen global eindeutig sind. Außerdem stellen Sie einen Namen für die Ressourcengruppe bereit, die alle Ressourcen enthält, die durch eine Bereitstellung der App erstellt werden. Für eine fiktive Person mit dem Namen „Ann Finley“ wird Folgendes empfohlen:

- **Benutzer:** *af1* setzt sich aus Ann Finleys Initialen und einer Ziffer zusammen. Verwenden Sie einen anderen Wert, wenn Sie die App ein zweites Mal bereitstellen. Ein Beispiel wäre „af2“.
- **Ressourcengruppe:** *wingtip-dpt-af1* gibt an, dass es sich um die App für eine Datenbank pro Mandant handelt. Durch Anhängen des Benutzernamens „af1“ wird der Name der Ressourcengruppe mit den Namen der darin enthaltenen Ressourcen korreliert.

Wählen Sie nun Ihre Namen aus, und notieren Sie sich diese.

### <a name="steps"></a>Schritte

1. Wenn Sie **Bereitstellung in Azure** auswählen, wird das Azure-Portal mit der SaaS-Bereitstellungsvorlage für eine Datenbank pro Mandant von Wingtip Tickets geöffnet.

   [![Bild einer Schaltfläche mit der Bezeichnung „Bereitstellung in Azure“](https://azuredeploy.net/deploybutton.png)](https://aka.ms/deploywingtipdpt)

1. Geben Sie die Werte für die erforderlichen Parameter in die Vorlage ein.

    > [!IMPORTANT]
    > Der Schutz einiger Authentifizierungs- und Serverfirewalls wurde zu Vorführungszwecken absichtlich aufgehoben. Es wird empfohlen, eine neue Ressourcengruppe zu erstellen. Verwenden Sie keine vorhandenen Ressourcengruppen, Server oder Pools. Verwenden Sie diese Anwendung, die Skripts oder die damit bereitgestellten Ressourcen nicht für die Produktion. Wenn Sie sich umfassend mit der Anwendung vertraut gemacht haben, löschen Sie diese Ressourcengruppe, um die zugehörige Abrechnung einzustellen.

    - **Ressourcengruppe** : Wählen Sie **Neu erstellen** aus, und geben Sie den eindeutigen Namen ein, den Sie zuvor für die Ressourcengruppe ausgewählt haben.
    - **Standort** : Wählen Sie in der Dropdownliste einen Standort aus.
    - **Benutzer:** Verwenden Sie den zuvor ausgewählten Wert für den Benutzernamen.

1. Stellen Sie die Anwendung bereit.

    a. Wählen Sie die entsprechende Option aus, um den Geschäftsbedingungen zuzustimmen.

    b. Wählen Sie die Option **Kaufen** .

1. Zum Überwachen des Bereitstellungsstatus wählen Sie **Benachrichtigungen** (das Glockensymbol rechts neben dem Suchfeld) aus. Das Bereitstellen der Wingtip Tickets SaaS-App dauert ungefähr fünf Minuten.

   ![Bereitstellung erfolgreich](./media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Herunterladen und Entsperren der Verwaltungsskripts für Wingtip Tickets

Laden Sie, während die Anwendung bereitgestellt wird, den Quellcode und die Verwaltungsskripts herunter.

> [!IMPORTANT]
> Ausführbare Inhalte (Skripts und DLLs) können durch Windows blockiert werden, wenn ZIP-Dateien aus einer externen Quelle heruntergeladen und extrahiert werden. Führen Sie die Schritte zum Entsperren der ZIP-Datei aus, bevor Sie die Skripts extrahieren. Durch das Entsperren wird sichergestellt, dass die Skripts ausgeführt werden dürfen.

1. Navigieren Sie zum GitHub-Repository [WingtipTicketsSaaS-DbPerTenant][github-wingtip-dpt].
1. Wählen Sie **Clone or download** aus.
1. Wählen Sie **ZIP herunterladen** aus, und speichern Sie die Datei.
1. Klicken Sie mit der rechten Maustaste auf die Datei **WingtipTicketsSaaS-DbPerTenant-master.zip** , und wählen Sie **Eigenschaften** aus.
1. Wählen Sie auf der Registerkarte **Allgemein** die Option **Blockierung aufheben** > **Anwenden** aus.
1. Auswählen von **OK** und Extrahieren der Dateien

Skripts befinden sich im Ordner „...\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules“.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aktualisieren der Benutzerkonfigurationsdatei für diese Bereitstellung

Ändern Sie vor dem Ausführen von Skripts die Werte „resource group“ und „user“ in der Datei „User Config“. Legen Sie diese Variablen auf die Werte fest, die Sie während der Bereitstellung angegeben haben.

1. Öffnen Sie „...\\Learning Modules\\**UserConfig.psm1** “ in der PowerShell ISE.
1. Aktualisieren Sie **ResourceGroupName** und **Name** mit den jeweiligen Werten für Ihre Bereitstellung (nur in den Zeilen 10 und 11).
1. Speichern Sie die Änderungen.

Auf diese Werte wird in fast jedem Skript verwiesen.

## <a name="run-the-application"></a>Ausführen der Anwendung

In der App werden Veranstaltungsorte vorgestellt. Beispiele hierfür sind Konzerthallen, Jazz-Clubs und Sportclubs. In Wingtip Tickets werden Veranstaltungsorte als Mandanten registriert. Als Mandant kann ein Veranstaltungsort auf einfache Weise eine Liste mit Veranstaltungen bereitstellen und Tickets an die Kunden verkaufen. Jeder Veranstaltungsort erhält eine personalisierte Website, über die Veranstaltungen aufgeführt und Tickets verkauft werden können.

Innerhalb der App wird für jeden Mandanten eine Datenbank in einem Pool für elastische Datenbanken bereitgestellt.

Auf einer zentralen **Event Hub** -Seite wird eine Liste mit Links zu den Mandanten in Ihrer Bereitstellung aufgeführt.

1. Verwenden Sie die URL zum Öffnen des Event Hub in Ihrem Webbrowser: http://events.wingtip-dpt.&lt;Benutzer&gt;.trafficmanager.net. Ersetzen Sie &lt;Benutzer&gt; durch den Benutzerwert Ihrer Bereitstellung.

    ![Veranstaltungshub](./media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Wählen Sie im Event Hub die Option **Fabrikam Jazz Club** aus.

    ![Events](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Die Wingtip-Anwendung verwendet [*Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md), um die Verteilung eingehender Anforderungen zu steuern. Die URL für den Zugriff auf die Veranstaltungsseite für einen bestimmten Mandanten verwendet das folgende Format:

- http://events.wingtip-dpt.&lt;Benutzer&gt;.trafficmanager.net/fabrikamjazzclub

    In der folgenden Tabelle werden die Teile des oben genannten Formats erläutert.

    | URL-Teil        | BESCHREIBUNG       |
    | :-------------- | :---------------- |
    | events.wingtip-dpt | Die Veranstaltungs-Teile der Wingtip-App<br /><br /> Durch *-dpt* unterscheidet sich die Wingtip Tickets-Implementierung *mit einer Datenbank pro Mandant* von anderen Implementierungen, z.B. der Implementierung mit einer *einzelnen* App pro Mandant ( *-sa* ) oder der Implementierung mit einer *mehrinstanzenfähigen Datenbank* ( *-mt* ). |
    | . *&lt;Benutzer&gt;* | Im Beispiel ist dies *af1* . |
    | .trafficmanager.net/ | Traffic Manager, Basis-URL |
    | fabrikamjazzclub | Gibt den Mandanten mit dem Namen „Fabrikam Jazz Club“ an. |
    | &nbsp; | &nbsp; |

- Der Mandantenname wird von der Veranstaltungs-App aus der URL analysiert.
- Der Mandantenname wird verwendet, um einen Schlüssel zu erstellen.
- Der Schlüssel wird für den Zugriff auf den Katalog verwendet, um den Speicherort der Mandantendatenbank abzurufen.
  - Der Katalog wird mit *Shardzuordnungsverwaltung* implementiert.
- Der Event Hub verwendet erweiterte Metadaten im Katalog, um für jeden Mandanten URLs für die Seiten mit Veranstaltungslisten zu erstellen.

In einer Produktionsumgebung erstellen Sie normalerweise einen CNAME-DNS-Eintrag zum [*Zuordnen einer Unternehmensinternetdomäne*](../../traffic-manager/traffic-manager-point-internet-domain.md) zum DNS-Namen von Traffic Manager.

> [!NOTE]
> Der Verwendungszweck von Traffic Manager ist unter Umständen in diesem Tutorial nicht sofort ersichtlich. Das Ziel dieser Reihe von Tutorials ist es, Muster zu verdeutlichen, die die Skalierung einer komplexen Produktionsumgebung bewältigen können. In einem solchen Fall würden Sie beispielsweise mehrere Webanwendungen auf der ganzen Welt verteilen, die mit Datenbanken kooperieren, und Sie würden Traffic-Manager verwenden, um die Weiterleitung zwischen diesen Instanzen vorzunehmen.
Eine weitere Reihe von Tutorials, die die Verwendung von Traffic Manager veranschaulichen, sind die Tutorials zu [Geowiederherstellung](./saas-dbpertenant-dr-geo-restore.md) und [Georeplikation](./saas-dbpertenant-dr-geo-replication.md). In diesen Tutorials wird Traffic Manager verwendet, um bei einem regionalen Ausfall auf eine Wiederherstellungsinstanz der SaaS-App umzuschalten.

## <a name="start-generating-load-on-the-tenant-databases"></a>Generieren von Last in den Mandantendatenbanken

Nachdem die App bereitgestellt wurde, können wir sie nutzen.

Mit dem PowerShell-Skript *Demo-LoadGenerator* wird eine Workload gestartet, die für alle Mandantendatenbanken ausgeführt wird. Die echte Auslastung vieler SaaS-Apps ist sporadischer Art und nicht vorhersagbar. Um diese Art der Auslastung zu simulieren, erzeugt der Generator eine Auslastung mit zufälligen Auslastungsspitzen oder Aktivitätsbursts für jeden Mandanten. Die Bursts treten in zufälligen Intervallen auf. Es dauert einige Minuten, bis das Auslastungsmuster ermittelt ist. Lassen Sie den Generator mindestens drei oder vier Minuten laufen, bevor Sie die Last überwachen.

1. Öffnen Sie das Skript „...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* “ in der PowerShell ISE.
2. Drücken Sie F5, um das Skript auszuführen und den Lastgenerator zu starten. Lassen Sie in diesem Fall die Standardparameterwerte unverändert.
3. Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie ggf. das zu verwendende Abonnement aus.

Das Lastengeneratorskript startet einen Hintergrundauftrag für jede Datenbank im Katalog und hält dann an. Wenn Sie das Lastgeneratorskript erneut ausführen, hält es zunächst alle aktuell ausgeführten Hintergrundaufträge an, bevor neue Aufträge gestartet werden.

### <a name="monitor-the-background-jobs"></a>Überwachen der Hintergrundaufträge

Wenn Sie die Hintergrundaufträge steuern und überwachen möchten, verwenden Sie folgende Cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Aktionen von Demo-LoadGenerator.ps1

*Demo-LoadGenerator.ps1* imitiert eine aktive Workload von Kundentransaktionen. In den folgenden Schritten wird die Abfolge von Aktionen beschrieben, die *Demo-LoadGenerator.ps1* initiiert:

1. *Demo-LoadGenerator.ps1* startet *LoadGenerator.ps1* im Vordergrund.

    - Beide PS1-Dateien werden unter den Ordnern „Learning Modules\\Utilities\\“ gespeichert.

2. *LoadGenerator.ps1* durchläuft alle Mandantendatenbanken im Katalog.

3. *LoadGenerator.ps1* startet einen PowerShell-Hintergrundauftrag für jede Mandantendatenbank:

    - Standardmäßig werden die Hintergrundaufträge für 120 Minuten ausgeführt.
    - Jeder Auftrag bewirkt eine CPU-basierte Last in einer Mandantendatenbank durch Ausführen von *Sp_CpuLoadGenerator* . Die Intensität und Dauer der Last variiert abhängig von `$DemoScenario`.
    - *Sp_CpuLoadGenerator* durchläuft eine SQL-SELECT-Anweisung, die eine hohe CPU-Auslastung bewirkt. Das Zeitintervall zwischen den Ausgaben der SELECT-Anweisung variiert je nach Parameterwerten, um eine steuerbare CPU-Auslastung zu erzeugen. Auslastungsgrade und Intervalle werden zufällig festgelegt, um realistischere Auslastungen zu simulieren.
    - Diese SQL-Datei wird unter *WingtipTenantDB\\dbo\\StoredProcedures\\* gespeichert.

4. Bei `$OneTime = $false` startet der Lastgenerator die Hintergrundaufträge und wird dann weiterhin ausgeführt. Alle 10 Sekunden überprüft er, ob neue Mandanten bereitgestellt wurden. Wenn Sie `$OneTime = $true` festlegen, startet der Lastgenerator die Hintergrundaufträge und beendet dann die Ausführung im Vordergrund. Behalten Sie `$OneTime = $false` für dieses Tutorial bei.

   Verwenden Sie STRG+C oder den Stoppvorgang STRG+UNTBR, wenn Sie den Lastgenerator beenden oder neu starten möchten.

   Wenn der Lastengenerator weiter im Vordergrund ausgeführt wird, verwenden Sie eine andere Instanz von PowerShell ISE, um andere PowerShell-Skripts auszuführen.

&nbsp;

Bevor Sie zum nächsten Abschnitt übergehen, lassen Sie den Lastgenerator im Zustand für das Aufrufen von Aufträgen weiterlaufen.

## <a name="provision-a-new-tenant"></a>Bereitstellen eines neuen Mandanten

Bei der Erstbereitstellung werden drei Beispielmandanten erstellt. Jetzt erstellen Sie einen weiteren Mandanten, um zu sehen, wie sich dies auf die bereitgestellte Anwendung auswirkt. Der Arbeitsablauf zum Bereitstellen neuer Mandanten in der Wingtip-App wird im [Tutorial zum Bereitstellen und zum Katalog](saas-dbpertenant-provision-and-catalog.md) erläutert. In dieser Phase erstellen Sie in weniger als einer Minute einen neuen Mandanten.

1. Öffnen Sie eine neue PowerShell ISE.
2. Öffnen Sie „...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* “.
3. Drücken Sie F5, um das Skript auszuführen. Lassen Sie die Standardwerte unverändert.

   > [!NOTE]
   > In vielen Wingtip-SaaS-Skripts wird *$PSScriptRoot* für die Navigation in Ordnern zum Aufrufen von Funktionen in anderen Skripts verwendet. Diese Variable wird nur ausgewertet, wenn das vollständige Skript durch das Drücken von F5 ausgeführt wird.  Das Hervorheben und Ausführen einer Auswahl mit F8 kann zu Fehlern führen. Drücken Sie F5, um die Skripts auszuführen.

Die neue Mandantendatenbank wird

- in einem Pool für elastische SQL-Datenbanken erstellt.
- initialisiert.
- im Katalog registriert.

Nach der erfolgreichen Bereitstellung wird die Website für *Veranstaltungen* des neuen Mandanten in Ihrem Browser angezeigt.

![Neuer Mandant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Aktualisieren Sie den Event Hub, damit der neue Mandant in der Liste angezeigt wird.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Untersuchen der Server, Pools und Mandantendatenbanken

Nachdem Sie für die Sammlung der Mandanten jetzt die Ausführung einer Last gestartet haben, sehen wir uns einige der bereitgestellten Ressourcen an.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Liste von SQL-Servern. Öffnen Sie den Server **catalog-dpt-&lt;Benutzer&gt;** .
    - Der Katalogserver enthält die beiden Datenbanken **tenantcatalog** und **basetenantdb** (eine Vorlagendatenbank, die kopiert wird, um neue Mandanten zu erstellen).

   ![Screenshot: Übersichtsseite des Katalogservers mit den beiden Datenbanken.](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Wechseln Sie zurück zur Liste der SQL-Server.

3. Öffnen Sie den Server **tenants1-dpt-&lt;BENUTZER&gt;** , auf dem sich die Mandantendatenbanken befinden.

4. Beachten Sie folgende Elemente:

    - Bei jeder Mandantendatenbank handelt es sich um eine Datenbank vom Typ **Elastisch Standard** in einem Standardpool mit 50 eDTUs.
    - Die Datenbank „Red Maple Racing“ ist die zuvor von Ihnen bereitgestellte Mandantendatenbank.

   ![Server mit Datenbanken](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Überwachen des Pools

Nachdem *LoadGenerator.ps1* mehrere Minuten ausgeführt wurde, sollten genügend Daten verfügbar sein, um einige Überwachungsfunktionen kennenzulernen. Diese Funktionen sind in Pools und Datenbanken integriert.

Navigieren Sie zum Server **tenants1-dpt-&lt;Benutzer&gt;** , und wählen Sie **Pool1** aus, um die Ressourcenverwendung für den Pool anzuzeigen. In den folgenden Diagrammen wurde der Auslastungsgenerator eine Stunde lang ausgeführt.

   ![Überwachen des Pools](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Das erste Diagramm mit der Bezeichnung **Ressourcenverwendung** zeigt die eDTU-Nutzung des Pools.
- Das zweite Diagramm zeigt die eDTU-Nutzung der fünf aktivsten Datenbanken im Pool.

Die beiden Diagramme veranschaulichen, dass Pools für elastische Datenbanken und SQL-Datenbank gut für unvorhersehbare Workloads von SaaS-Anwendungen geeignet sind. Die Diagramme zeigen, dass vier Datenbanken jeweils mit Bursts auf bis zu 40 eDTUs kommen, aber dennoch in einem 50-eDTU-Pool problemlos unterstützt werden. Der 50-eDTU-Pool kann auch größere Workloads unterstützen. Wenn die Datenbanken als einzelne Datenbanken bereitgestellt werden, muss es sich bei jeder um eine S2-Datenbank (50 DTUs) handeln, damit die Bursts unterstützt werden. Die Kosten für vier einzelne S2-Datenbanken würden fast den dreifachen Preis des Pools ausmachen. In der Praxis betreiben SQL-Datenbank-Kunden bis zu 500 Datenbanken in Pools mit 200 eDTUs. Weitere Informationen finden Sie im [Tutorial zur Leistungsüberwachung](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- Weitere Informationen finden Sie in den [weiteren Tutorials, die auf der SaaS-App Wingtip Tickets mit einer Datenbank pro Mandant aufbauen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Weitere Informationen zu Pools für elastische Datenbanken finden Sie unter [Was ist ein Pool für elastische Azure SQL-Datenbanken?](elastic-pool-overview.md).
- Weitere Informationen zu elastischen Aufträgen finden Sie unter [Verwalten horizontal hochskalierter Clouddatenbanken](./elastic-jobs-overview.md).
- Weitere Informationen zu mehrinstanzenfähigen SaaS-Anwendungen finden Sie unter [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> - Bereitstellen der Wingtip Tickets SaaS-Anwendung
> - Die Server, Pools und Datenbanken, aus denen sich die App zusammensetzt
> - Zuordnen der Mandanten zu den zugehörigen Daten mithilfe des *Katalogs*
> - Bereitstellen neuer Mandanten
> - Anzeigen der Poolnutzung zum Überwachen der Mandantenaktivität
> - Löschen von Beispielressourcen, um die zugehörige Abrechnung einzustellen

Arbeiten Sie als Nächstes das [Tutorial zum Bereitstellen und zum Katalog](saas-dbpertenant-provision-and-catalog.md) durch.

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant