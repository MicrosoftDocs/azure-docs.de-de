---
title: Bereitstellen einer mehrinstanzenfähigen SaaS-Datenbankanwendung mit Sharding
description: Es wird beschrieben, wie Sie die mehrinstanzenfähige Wingtip Tickets-SaaS-Datenbankanwendung mit Sharding bereitstellen und kennenlernen, mit der SaaS-Muster per Azure SQL-Datenbank dargestellt werden.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/16/2018
ms.openlocfilehash: 4de7e428bff0feaafdec00b0c0014bbaf6acb917
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790966"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Bereitstellen und Kennenlernen einer mehrinstanzenfähigen Anwendung mit Sharding
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial wird beschrieben, wie Sie eine mehrinstanzenfähige SaaS-Beispielanwendung mit dem Namen Wingtip Tickets bereitstellen und untersuchen. Die Wingtip Tickets-App ist dafür ausgelegt, Features von Azure SQL-Datenbank zu veranschaulichen, mit denen die Implementierung von SaaS-Szenarien vereinfacht wird.

Bei dieser Implementierung der Wingtip Tickets-App wird ein mehrinstanzenfähiges Datenbankmuster mit Sharding verwendet. Das Sharding erfolgt nach Mandantenbezeichner. Mandantendaten werden entsprechend den Werten der Mandanten-ID an eine bestimmte Datenbank verteilt. 

Diese Datenbankmuster ermöglichen Ihnen, in jedem Shard oder jeder Datenbank einen oder mehrere Mandanten zu speichern. Sie können die Optimierung nach den niedrigsten Kosten vornehmen, indem jede Datenbank von mehreren Mandanten gemeinsam genutzt wird. Oder Sie können die Optimierung im Hinblick auf Isolation vornehmen, indem Sie in jeder Datenbank nur einen einzigen Mandanten speichern. Ihre Wahl für die Optimierung kann für jeden einzelnen Mandanten unabhängig vorgenommen werden. Sie können Ihre Wahl treffen, wenn der Mandant zum ersten Mal gespeichert wird, oder Sie können Ihre Meinung später ändern. Die Anwendung ist so konzipiert, dass sie in beiden Fällen gut funktioniert.

## <a name="app-deploys-quickly"></a>App wird schnell bereitgestellt

Die App wird in der Azure-Cloud ausgeführt und verwendet Azure SQL-Datenbank. Der folgende Abschnitt zur Bereitstellung enthält die blaue Schaltfläche **Bereitstellung in Azure** . Wenn auf die Schaltfläche geklickt wird, wird die App innerhalb von fünf Minuten vollständig in Ihrem Azure-Abonnement bereitgestellt. Sie haben vollen Zugriff auf die einzelnen Anwendungskomponenten.

Die Anwendung wird mit Daten für drei Beispielmandanten bereitgestellt. Die Mandanten werden zusammen in einer mehrinstanzenfähigen Datenbank gespeichert.

Jeder kann den C#- und PowerShell-Quellcode für Wingtip Tickets aus [dem GitHub-Repository][link-github-wingtip-multitenantdb-55g] herunterladen.

## <a name="learn-in-this-tutorial"></a>In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
> - Bereitstellen der Wingtip Tickets SaaS-Anwendung
> - Abrufen des Quellcodes der Anwendung und der Verwaltungsskripts
> - Informationen zu Servern und Datenbanken, aus denen sich die App zusammensetzt
> - Zuordnen der Mandanten zu den zugehörigen Daten mithilfe des *Katalogs*
> - Bereitstellen eines neuen Mandanten
> - Überwachen der Mandantenaktivität in der App

Eine Reihe von verwandten Tutorials steht zur Verfügung, die auf dieser anfänglichen Bereitstellung aufbauen. In den Tutorials werden eine Reihe von SaaS-Entwurfs- und -Verwaltungsmustern untersucht. Beim Durcharbeiten der Tutorials sollten Sie sich die bereitgestellten Skripts genauer anschauen, um zu sehen, wie die verschiedenen SaaS-Muster implementiert werden.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zum Durchführen dieses Tutorials sicher, dass die folgenden Voraussetzungen erfüllt sind:

- Die neueste Azure PowerShell ist installiert. Ausführliche Informationen finden Sie unter [Erste Schritte mit Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Bereitstellen der Wingtip Tickets-App

### <a name="plan-the-names"></a>Planen von Namen

In den Schritten in diesem Abschnitt geben Sie einen Wert für *Benutzer* an, mit dem sichergestellt wird, dass Ressourcennamen global eindeutig sind, sowie einen Namen für die *Ressourcengruppe* , die alle Ressourcen enthält, die durch eine Bereitstellung der App erstellt werden. Für eine Person mit dem Namen *Ann Finley* wird Folgendes empfohlen:
- *Benutzer:* **af1**  *(Die Initialen mit einer Ziffer. Verwenden Sie einen anderen Wert (z. B. „af2“), wenn Sie die App ein zweites Mal bereitstellen.)*
- *Ressourcengruppe:* **wingtip-mt-af1** *(„wingtip-mt“ gibt an, dass es sich um die mehrinstanzenfähige App mit Sharding handelt. Durch Anhängen des Benutzernamens „af1“ wird der Name der Ressourcengruppe mit den Namen der darin enthaltenen Ressourcen korreliert.)*

Wählen Sie nun Ihre Namen aus, und notieren Sie sich diese. 

### <a name="steps"></a>Schritte

1. Klicken Sie auf die folgende blaue Schaltfläche **Bereitstellung in Azure** .
   - Das Azure-Portal mit der Vorlage für die Wingtip Tickets-SaaS-Bereitstellung wird geöffnet.

     [![Schaltfläche „Bereitstellung in Azure“][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Geben Sie die erforderlichen Parameterwerte für die Bereitstellung ein.

    > [!IMPORTANT]
    > Verwenden Sie für diese Demo keine bereits vorhandenen Ressourcengruppen, -server oder -pools. Wählen Sie stattdessen **Neue Ressourcengruppe erstellen** aus. Wenn Sie sich umfassend mit der Anwendung vertraut gemacht haben, löschen Sie diese Ressourcengruppe, um die zugehörige Abrechnung einzustellen.
    > Verwenden Sie diese Anwendung und alle damit erstellten Ressourcen nicht für die Produktion. Einige Aspekte hinsichtlich der Authentifizierungs- und Serverfirewalleinstellungen sind absichtlich unsicher in der App, um die Demo zu erleichtern.

    - **Ressourcengruppe** : Wählen Sie **Neu erstellen** aus, und geben Sie einen **Namen** für die Ressourcengruppe an (Groß-/Kleinschreibung wird berücksichtigt).
        - Wählen Sie einen **Speicherort** in der Dropdownliste aus.
    - Für **Benutzer** : Es wird empfohlen, einen kurzen Wert für **Benutzer** auszuwählen.

1. **Bereitstellen der Anwendung** .

    - Klicken Sie auf die entsprechende Option, um den Geschäftsbedingungen zuzustimmen.
    - Klicken Sie auf **Kaufen** .

1. Überwachen Sie den Bereitstellungsstatus, indem Sie auf **Benachrichtigungen** klicken (das Glockensymbol rechts neben dem Suchfeld). Das Bereitstellen der Wingtip-App dauert ungefähr fünf Minuten.

   ![Bereitstellung erfolgreich](./media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Herunterladen und Aufheben der Blockierung der Verwaltungsskripts

Laden Sie den Quellcode der Anwendung und die Verwaltungsskripts herunter, während die Anwendung bereitgestellt wird.

> [!NOTE]
> Ausführbare Inhalte (Skripts, DLLs) können durch Windows blockiert werden, wenn ZIP-Dateien aus einer externen Quelle heruntergeladen und extrahiert werden. Führen Sie bei der Extraktion der Skripts aus einer ZIP-Datei die nachfolgenden Schritte aus, um die Blockierung der ZIP-Datei vor der Extraktion aufzuheben. Durch die Aufhebung der Blockierung der ZIP-Datei stellen Sie sicher, dass die Skripts ausgeführt werden dürfen.

1. Browsen Sie zum [GitHub-Repository „WingtipTicketsSaaS-MultiTenantDb“](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klicken Sie auf **Klonen oder herunterladen** .
3. Klicken Sie auf **ZIP herunterladen** , und speichern Sie die Datei.
4. Klicken Sie mit der rechten Maustaste auf die Datei **WingtipTicketsSaaS-MultiTenantDb-master.zip** , und wählen Sie **Eigenschaften** aus.
5. Wählen Sie auf der Registerkarte **Allgemein** die Option **Unblock** (Zulassen), und klicken Sie auf **Übernehmen** .
6. Klicken Sie auf **OK** .
7. Extrahieren Sie die Dateien.

Skripts befinden sich im Ordner „ *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\* “.

## <a name="update-the-configuration-file-for-this-deployment"></a>Aktualisieren der Konfigurationsdatei für diese Bereitstellung

Legen Sie vor dem Ausführen von Skripts die Werte *resource group* und *user* in der Datei **UserConfig.psm1** fest. Legen Sie diese Variablen auf die gleichen Werte fest, die Sie während der Bereitstellung angegeben haben.

1. Öffnen Sie ...\\Learning Modules\\*UserConfig.psm1* in der *PowerShell ISE* .
2. Aktualisieren Sie *ResourceGroupName* und *Name* mit den jeweiligen Werten für Ihre Bereitstellung (nur in den Zeilen 10 und 11).
3. Speichern Sie die Änderungen.

Die in dieser Datei festgelegten Werte werden von allen Skripts verwendet, deshalb ist es wichtig, dass sie korrekt sind. Wenn Sie die App erneut bereitstellen, müssen Sie verschiedene Werte für „Benutzer“ und „Ressourcengruppe“ auswählen. Aktualisieren Sie anschließend die Datei „UserConfig.psm1“ mit den neuen Werten.

## <a name="run-the-application"></a>Ausführen der Anwendung

In der Wingtip-App sind Mandanten Veranstaltungsorte. Ein Veranstaltungsort kann z.B. eine Konzerthalle, ein Sportverein oder ein anderer Ort sein, an dem Veranstaltungen ausgetragen werden. Die Veranstaltungsorte werden in Wingtip als Kunden registriert, und für jeden Veranstaltungsort wird eine Mandanten-ID generiert. Jeder Veranstaltungsort listet seine bevorstehenden Veranstaltungen in Wingtip auf, sodass Tickets für die Veranstaltungen zum Erwerb für die Öffentlichkeit zur Verfügung stehen.

Jeder Veranstaltungsort erhält eine personalisierte Web-App, über die Veranstaltungen aufgeführt und Tickets verkauft werden können. Jede Web-App ist von anderen Mandanten unabhängig und isoliert. In Azure SQL-Datenbank werden alle Daten der einzelnen Mandanten standardmäßig in einer mehrinstanzenfähigen Sharddatenbank gespeichert. Sämtliche Daten werden mit der Mandanten-ID gekennzeichnet.

Auf der zentralen Webseite **Veranstaltungshub** wird eine Liste mit Links zu den Mandanten in der jeweiligen Bereitstellung aufgeführt. Anhand der folgenden Schritte können Sie die Webseite **Veranstaltungshub** und die jeweilige Web-App erkunden:

1. Öffnen Sie den **Ereignis-Hub** in Ihrem Webbrowser:
   - http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net &nbsp; *(Ersetzen Sie &lt; user&gt; durch den Benutzerwert Ihrer Bereitstellung.)*

     ![Events Hub](./media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Klicken Sie im **Ereignis-Hub** auf **Fabrikam Jazz Club** .

   ![Events](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Zum Steuern der Verteilung eingehender Anforderungen nutzt die Wingtip-App den [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). Die Veranstaltungsseite für die einzelnen Mandanten enthalten den Mandantennamen in der URL. Jede URL enthält auch den jeweiligen Benutzerwert. Bei jeder URL wird durch Ausführung der folgenden Schritte die Erfüllung des angezeigten Formats sichergestellt:

- http://events.wingtip-mt.&lt;Benutzer&gt;.trafficmanager.net/ *fabrikamjazzclub*

1. Die Veranstaltungs-App analysiert den Mandantennamen aus der URL. Der Mandantenname in der vorangehenden Beispiel-URL lautet *fabrikamjazzclub* .
2. Die App hashcodiert anschließend den Mandantennamen, um einen Schlüssel für den Zugriff auf einen Katalog mithilfe der [Shardzuordnungsverwaltung](elastic-scale-shard-map-management.md) zu erstellen.
3. Die App sucht nach dem Schlüssel im Katalog und ruft den entsprechenden Speicherort in der Datenbank für den Mandanten ab.
4. Die App verwendet die Speicherortinformationen, um nach einer Datenbank mit allen Daten für den Mandanten zu suchen und auf diese zuzugreifen.

### <a name="events-hub"></a>Veranstaltungshub

1. Im **Veranstaltungshub** werden alle im Katalog registrierten Mandanten sowie deren Veranstaltungen aufgelistet.
2. Der **Ereignis-Hub** ruft mit erweiterten Metadaten im Katalog den Namen des Mandanten für die jeweilige Zuordnung ab, um die URL zu erstellen.

In einer Produktionsumgebung erstellen Sie typischerweise einen CNAME-DNS-Eintrag zum [Zuordnen einer Unternehmensinternetdomäne](../../traffic-manager/traffic-manager-point-internet-domain.md) zum Traffic Manager-Profil.

## <a name="start-generating-load-on-the-tenant-databases"></a>Generieren von Last in den Mandantendatenbanken

Nachdem die App bereitgestellt wurde, können wir sie nutzen. Mit dem PowerShell-Skript *Demo-LoadGenerator* wird eine Workload gestartet, die für jeden Mandanten ausgeführt wird. Die Auslastung vieler echter SaaS-Apps ist häufig sporadischer Art und nicht vorhersagbar. Um diese Art der Auslastung zu simulieren, erzeugt der Generator eine Last, die auf alle Mandanten verteilt wird. Die Last enthält randomisierte Datenverkehrsspitzen auf jedem Mandanten, die in zufälligen Intervallen auftreten. Es dauert mehrere Minuten, bis das Auslastungsmuster erreicht ist. Es ist also am besten, den Generator mindestens drei oder vier Minuten laufen zu lassen, bevor die Auslastung überwacht wird.

1. Öffnen Sie in der *PowerShell ISE* das Skript „...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* “.
2. Drücken Sie **F5** , um das Skript auszuführen und den Last-Generator zu starten (übernehmen Sie zunächst die Standard-Parameterwerte).

Das Skript *Demo-LoadGenerator.ps1* öffnet eine andere PowerShell-Sitzung, in der der Lastengenerator ausgeführt wird. Der Lastengenerator wird in dieser Sitzung als ein Task im Vordergrund ausgeführt, der für jeden Mandanten je einen Lastengenerierungsauftrag im Hintergrund aufruft.

Nachdem der Task im Vordergrund gestartet wurde, bleibt er im Zustand für das Aufrufen von Aufträgen. Der Task startet zusätzliche Hintergrundaufträge für alle neuen Mandanten, die anschließend bereitgestellt werden.

Durch Schließen der PowerShell-Sitzung werden alle Aufträge beendet.

Möglicherweise möchten Sie die Lastengeneratorsitzung neu starten, um andere Parameterwerte zu verwenden. Wenn dies der Fall ist, schließen Sie die PowerShell-Generierungssitzung, und führen Sie *Demo-LoadGenerator.ps1* erneut aus.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Bereitstellen eines neuen Mandanten in der Sharddatenbank

Die erste Bereitstellung umfasst drei Beispielmandanten in der Datenbank *Tenants1* . Erstellen Sie einen anderen Mandanten, und achten Sie darauf, wie sich dieser auf die bereitgestellte Anwendung auswirkt. Drücken Sie in diesem Schritt eine Taste, um einen neuen Mandanten zu erstellen:

1. Öffnen Sie ...\\Learning Modules\\Provision and Catalog\\*Demo-ProvisionTenants.ps1* in der *PowerShell ISE* .
2. Drücken Sie **F5** (nicht **F8** ), um das Skript auszuführen (lassen Sie die Standardwerte zunächst unverändert).

   > [!NOTE]
   > Um einen ausgewählten Teil des Skripts auszuführen, können Sie die PowerShell-Skripts nur durch Drücken der **F5** -Taste ausführen, nicht durch Drücken von **F8** . Denn bei Verwendung der **F8** -Taste wird die Variable *$PSScriptRoot* nicht ausgewertet. Diese Variable ist für viele Skripts zum Navigieren in Ordnern, Aufrufen anderer Skripts oder Importieren von Modulen erforderlich.

Der neue Mandant Red Maple Racing wird zur Datenbank *Tenants1* hinzugefügt und im Katalog registriert. Die Website des neuen Mandanten für den Ticketverkauf für **Ereignisse** wird in Ihrem Browser geöffnet:

![Neuer Mandant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Aktualisieren Sie den **Veranstaltungshub** . Der neue Mandant wird jetzt in der Liste aufgeführt.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Bereitstellen eines neuen Mandanten in der eigenen Datenbank

Das mehrinstanzenfähige Modell mit Shards erlaubt zwei Optionen: Sie können einen neuen Mandanten in einer Datenbank, die andere Mandanten enthält, oder in einer eigenen Datenbank bereitstellen. Ein in einer eigenen Datenbank isolierter Mandant bietet folgende Vorteile:

- Die Leistung der Mandantendatenbank kann gesteuert werden, ohne dass die Anforderungen anderer Mandanten zu kurz kommen.
- Die Datenbank kann ggf. an einem früheren Zeitpunkt wiederhergestellt werden, da keine anderen Mandanten betroffen werden würden.

Sie können auswählen, ob Kunden mit kostenlosen Testversionen oder Kunden des mittleren Segments in mehrinstanzenfähigen Datenbanken verwaltet werden sollen. Premium-Mandanten können in eigenen dedizierten Datenbanken abgelegt werden. Wenn Sie viele Datenbanken erstellen, die nur einen Mandanten enthalten, können Sie diese zur Optimierung der Ressourcenkosten zusammen in einem Pool für elastische Datenbanken verwalten.

Als Nächstes stellen wir einen anderen Mandanten bereit, dieses Mal in seiner eigenen Datenbank:

1. Nehmen Sie in ...\\Learning Modules\\Provision and Catalog\\*Demo-ProvisionTenants.ps1* folgende Änderungen vor: Ändern Sie *$TenantName* in **Salix Salsa** , *$VenueType* in **dance** und *$Scenario* in **2** .

2. Drücken Sie **F5** , um das Skript erneut auszuführen.
    - Indem Sie **F5** drücken, wird der neue Mandant in einer separaten Datenbank bereitgestellt. Die Datenbank und der Mandant werden im Katalog registriert. Dann wird im Browser die Seite „Ereignisse“ des Mandanten geöffnet.

   ![Seite „Salix Salsa-Ereignisse“](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Scrollen Sie zum Ende der Seite. Im Banner sehen Sie den Namen der Datenbank, in der die Mandantendaten gespeichert werden.

3. Aktualisieren Sie den **Veranstaltungshub** . Zwei neue Mandanten werden jetzt in der Liste aufgeführt.

## <a name="explore-the-servers-and-tenant-databases"></a>Untersuchen der Server und Mandantendatenbanken

Betrachten wir nun einige der Ressourcen, die bereitgestellt wurden:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Liste der Ressourcengruppen. Öffnen Sie die Ressourcengruppe, die Sie bei der Bereitstellung der Anwendung erstellt haben.

   ![Ressourcengruppe](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klicken Sie auf den Server **catalog-mt&lt;Benutzer&gt;** . Der Katalogserver enthält zwei Datenbanken: *tenantcatalog* und *basetenantdb* . Die Datenbank *basetenantdb* ist eine leere Vorlagendatenbank. Kopieren Sie diese Vorlagendatenbank, um eine neue Mandantendatenbank für mehrere oder für nur einen Mandanten zu erstellen.

   ![Katalogserver](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Wechseln Sie zurück zur Ressourcengruppe, und wählen den Server *tenants1-mt* aus, auf dem die Mandantendatenbanken gespeichert sind.
    - Die tenants1-Datenbank ist eine mehrinstanzenfähige Datenbank, in der die ursprünglichen drei Mandanten sowie der erste hinzugefügte Mandant gespeichert sind. Sie ist als Standarddatenbank mit 50 Datenübertragungseinheiten (DTU) konfiguriert.
    - Die Datenbank **salixsalsa** enthält den Salix Salsa-Tanztreffpunkt als einzigen Mandanten. Sie ist standardmäßig als eine Standard Edition-Datenbank mit 50 Datenübertragungseinheiten konfiguriert.

   ![Mandantenserver](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Überwachen der Leistung der Datenbank

Wenn der Lastengenerator mehrere Minuten ausgeführt wurde, sollten genügend Telemetriedaten verfügbar sein, um einige der in das Azure-Portal integrierten Datenbanküberwachungsfunktionen zu betrachten.

1. Navigieren Sie zum Server **tenants1-mt&lt;Benutzer&gt;** , und klicken Sie auf **tenants1** , um die Ressourcenverwendung für die Datenbank anzuzeigen, die vier Mandanten enthält. Jeder Mandant unterliegt einer sporadischen hohen Auslastung durch den Lastengenerator:

   ![Überwachen von tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Das DTU-Workloaddiagramm veranschaulicht auf verständliche Weise, wie eine mehrinstanzenfähige Datenbank eine nicht vorhersagbare Arbeitslast über viele Mandanten hinweg unterstützen kann. In diesem Fall wendet der Lastengenerator eine sporadische Last von ungefähr 30 Datenübertragungseinheiten auf jeden Mandant an. Diese Last entspricht einer 60-prozentigen Auslastung einer Datenbank mit 50 Datenübertragungseinheiten. Spitzen, die die 60 % überschreiten, sind das Ergebnis der auf mehrere Mandanten gleichzeitig angewendeten Last.

2. Navigieren Sie zum Server **tenants1-mt&lt;Benutzer&gt;** , und klicken Sie auf die Datenbank **salixsalsa** . Sie können die Ressourcenverwendung für diese Datenbank, die nur einen einzigen Mandanten enthält, sehen.

   ![Datenbank „salixsalsa“](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Der Lastengenerator wendet eine ähnliche Last auf jeder Mandanten an, unabhängig davon, in welcher Datenbank sich der jeweilige Mandant befindet. Sie erkennen, dass die Datenbank **salixsalsa** mit nur einem Mandanten eine viel höhere Auslastung toleriert, als die Datenbank mit mehreren Mandanten. 

### <a name="resource-allocations-vary-by-workload"></a>Nach Workload variierende Ressourcenzuordnungen

In einigen Fällen erfordert eine mehrinstanzenfähige Datenbank mehr Ressourcen, um eine gute Leistung zu erzielen, als eine Datenbank mit nur einem Mandanten. Dies ist jedoch nicht immer der Fall. Die optimale Zuordnung von Ressourcen hängt von den jeweiligen Workloadeigenschaften der Mandanten in Ihrem System ab.

Die vom Lastengeneratorskript generierten Workloads dienen lediglich zur Veranschaulichung.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- Weitere Informationen zu mehrinstanzenfähigen SaaS-Anwendungen finden Sie unter [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen](saas-tenancy-app-design-patterns.md).

- Weitere Informationen zu Pools für elastische Datenbanken finden Sie hier:

  - [Pools für elastische Datenbanken als Hilfe beim Verwalten und Skalieren mehrerer Datenbanken in Azure SQL-Datenbank](elastic-pool-overview.md)
  - [Erweitern mit Azure SQL-Datenbank](elastic-scale-introduction.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> - Bereitstellen der mehrinstanzenfähigen Wingtip Tickets-SaaS-Datenbankanwendung
> - Grundlegende Vorgänge zu Servern und Datenbanken, aus denen sich die App zusammensetzt
> - Zuordnung von Mandanten zu den zugehörigen Daten mithilfe des *Katalogs*
> - Bereitstellen neuer Mandanten in einer Datenbank mit mehreren Mandanten und in einer Datenbank mit nur einem Mandanten
> - Anzeigen der Poolnutzung zum Überwachen der Mandantenaktivität
> - Löschen von Beispielressourcen, um die zugehörige Abrechnung einzustellen

Arbeiten Sie nun das [Tutorial zum Bereitstellen und zum Katalog](saas-multitenantdb-provision-and-catalog.md) durch.


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: /powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Schaltfläche „Bereitstellung in Azure“"