---
title: SaaS-Tutorial für Einzelmandanten
description: Stellen Sie eine eigenständige SaaS-Anwendung für einzelne Mandanten, die Azure SQL-Datenbank verwendet, bereit, und untersuchen Sie sie.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/07/2018
ms.openlocfilehash: 896f4edb1e94c1eca06b046382727a5042375e3b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793278"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Bereitstellen und Untersuchen einer eigenständigen SaaS-Anwendung für einzelne Mandanten, die Azure SQL-Datenbank verwendet
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial stellen Sie die Wingtip Tickets-SaaS-Beispielanwendung bereit, die mit dem Muster für eigenständige Anwendungen oder App-pro-Mandant entwickelt wurde, und untersuchen sie.  Die Anwendung ist darauf ausgelegt, Features von Azure SQL-Datenbank zu veranschaulichen, mit denen die Aktivierung von mehrinstanzenfähigen SaaS-Szenarien vereinfacht wird.

Das Muster für eigenständige Anwendungen oder App-pro-Mandant stellt eine Anwendungsinstanz für jeden Mandanten bereit.  Jede Anwendung wird für einen bestimmten Mandanten konfiguriert und in einer separaten Azure-Ressourcengruppe bereitgestellt. Für eine Lösung für mehrere Mandanten werden mehrere Instanzen der Anwendung bereitgestellt. Dieses Muster eignet sich am besten für eine kleinere Anzahl von Mandanten, wenn die Isolierung der Instanzen die höchste Priorität hat. Azure bietet Partnerprogramme, mit denen Ressourcen im Abonnement eines Mandanten bereitgestellt und durch einen Dienstanbieter im Auftrag des Mandanten verwaltet werden. 

In diesem Tutorial stellen Sie drei eigenständige Anwendungen für drei Mandanten in Ihrem Azure-Abonnement bereit.  Sie haben Vollzugriff auf die einzelnen Anwendungskomponenten, um diese untersuchen und verwenden zu können.

Der Quellcode der Anwendung und die Verwaltungsskripts sind im GitHub-Repository [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) verfügbar. Die Anwendung wurde mit Visual Studio 2015 erstellt und lässt sich ohne Update auf Visual Studio 2019 nicht erfolgreich öffnen und kompilieren.


In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
> * Bereitstellen der eigenständigen SaaS-Anwendung Wingtip Tickets
> * Abrufen des Quellcodes der Anwendung und der Verwaltungsskripts
> * Informationen zu Servern und Datenbanken, aus denen sich die App zusammensetzt

Es werden noch weitere Tutorials veröffentlicht. In diesen Tutorials können Sie verschiedene Verwaltungsszenarien basierend auf diesem Anwendungsmuster erkunden.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Bereitstellen der eigenständigen SaaS-Anwendung Wingtip Tickets

Stellen Sie die App für die drei bereitgestellten Mandanten bereit:

1. Klicken Sie jeweils auf die blaue Schaltfläche **Deploy to Azure** (In Azure bereitstellen), um die Bereitstellungsvorlage im [Azure-Portal](https://portal.azure.com) zu öffnen. Für jede Vorlage sind zwei Parameterwerte erforderlich: ein Name für eine neue Ressourcengruppe und ein Benutzername, mit dem diese Bereitstellung von anderen Bereitstellungen der App unterschieden wird. Im nächsten Schritt sind Details zum Festlegen dieser Werte angegeben.

   **Contoso Concert Hall**   
   [![Abbildung einer Schaltfläche mit der Bezeichnung „Bereitstellung in Azure“](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-contoso)

   **Dogwood Dojo**   
   [![Abbildung einer Schaltfläche mit der Bezeichnung „Bereitstellung in Azure“](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-dogwood)

   **Fabrikam Jazz Club**   
   [![Abbildung einer Schaltfläche mit der Bezeichnung „Bereitstellung in Azure“](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-fabrikam)
 
2. Geben Sie die erforderlichen Parameterwerte für jede Bereitstellung ein.

    > [!IMPORTANT]
    > Der Schutz einiger Authentifizierungs- und Serverfirewalls wurde zu Vorführungszwecken absichtlich aufgehoben. **Erstellen Sie eine neue Ressourcengruppe** für jede Anwendungsbereitstellung.  Verwenden Sie keine vorhandene Ressourcengruppe. Verwenden Sie diese Anwendung und alle damit erstellten Ressourcen nicht für die Produktion. Wenn Sie sich umfassend mit den Anwendungen vertraut gemacht haben, löschen Sie alle Ressourcengruppen, um die zugehörige Abrechnung einzustellen.

    Es wird empfohlen, bei Ressourcennamen nur Kleinbuchstaben, Zahlen und Bindestriche zu verwenden.
    * Wählen Sie für **Ressourcengruppe** die Option „Neu erstellen“ aus, und geben Sie dann für die Ressourcengruppe einen Namen in Kleinbuchstaben an. **wingtip-sa-\<venueName\>-\<user\>** ist das empfohlene Muster.  Ersetzen Sie \<venueName\> durch den Namen des Veranstaltungsorts ohne Leerzeichen. Ersetzen Sie \<user\> durch den Benutzerwert von unten.  Mit diesem Muster können Ressourcengruppennamen z.B. *wingtip-sa-contosoconcerthall-af1* , *wingtip-sa-dogwooddojo-af1* oder *wingtip-sa-fabrikamjazzclub-af1* lauten.
    * Wählen Sie einen **Speicherort** in der Dropdownliste aus.

    * Es wird empfohlen, für **Benutzer** einen kurzen Wert auszuwählen, z.B. Ihre Initialen und eine Ziffer (etwa *af1* ).


3. **Bereitstellen der Anwendung** .

    * Klicken Sie auf die entsprechende Option, um den Geschäftsbedingungen zuzustimmen.
    * Klicken Sie auf **Kaufen** .

4. Überwachen Sie den Status aller drei Bereitstellungen, indem Sie auf **Benachrichtigungen** (das Glockensymbol rechts neben dem Suchfeld) klicken. Das Bereitstellen der Apps dauert ca. 5 Minuten.


## <a name="run-the-applications"></a>Ausführen der Anwendungen

In der App werden Veranstaltungsorte vorgestellt.  Die Veranstaltungsorte sind die Mandanten der Anwendung. Jeder Veranstaltungsort erhält eine personalisierte Website, über die Veranstaltungen aufgeführt und Tickets verkauft werden können. Beispiele hierfür sind Konzerthallen, Jazz-Clubs und Sportclubs. Im Beispiel legt der Typ des Veranstaltungsorts das Hintergrundfoto fest, das auf der Website des Veranstaltungsorts angezeigt.   Beim Modell mit eigenständigen Anwendungen erhält jeder Veranstaltungsort eine separate Anwendungsinstanz mit zugehöriger eigenständiger Azure SQL-Datenbank-Instanz.

1. Öffnen Sie die Ereignisseite für jeden der drei Mandanten in eigenen Browserregisterkarten:

   - http://events.contosoconcerthall.&lt;Benutzer&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;Benutzer&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;Benutzer&gt;.trafficmanager.net

     (Ersetzen Sie in jeder URL &lt;Benutzer&gt; durch den Benutzerwert Ihrer Bereitstellung.)

   ![Events](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Zum Steuern der Verteilung eingehender Anforderungen nutzt die App [*Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md). Jede mandantenspezifische App-Instanz enthält den Namen des Mandanten als Teil des Domänennamens in der URL. Alle Mandanten-URLs enthalten Ihren spezifischen Wert für **Benutzer** . Die URLs haben das folgende Format:
- http://events.&lt;Veranstaltungsort&gt;.&lt; Benutzer&gt;.trafficmanager.net

Der **Speicherort** der Datenbank jedes Mandanten ist in den App-Einstellungen der zugehörigen bereitgestellten App enthalten.

In einer Produktionsumgebung erstellen Sie normalerweise einen CNAME-DNS-Eintrag zum [*Zuordnen einer Unternehmensinternetdomäne*](../../traffic-manager/traffic-manager-point-internet-domain.md) zur URL des Traffic Manager-Profils.


## <a name="explore-the-servers-and-tenant-databases"></a>Untersuchen der Server und Mandantendatenbanken

Betrachten wir einige der Ressourcen, die bereitgestellt wurden:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Liste der Ressourcengruppen.
2. Die drei Mandantenressourcengruppen sollten angezeigt werden.
3. Öffnen Sie die Ressourcengruppe **wingtip-sa-fabrikam-&lt;Benutzer&gt;** mit den Ressourcen für die Bereitstellung des Fabrikam Jazz Club.  Der Server **fabrikamjazzclub-&lt;Benutzer&gt;** enthält die Datenbank **fabrikamjazzclub** .

Jede Mandantendatenbank ist eine *eigenständige* Datenbank mit 50 DTUs.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<!--
* Additional [tutorials that build on the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](elastic-pool-overview.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](./elastic-jobs-overview.md)
-->

- Weitere Informationen zu mehrinstanzenfähigen SaaS-Anwendungen finden Sie unter [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Löschen von Ressourcengruppen zum Beenden der Abrechnung ##

Wenn Sie die Nutzung des Beispiels beendet haben, löschen Sie alle Ressourcengruppen, die Sie erstellt haben, um die zugehörige Abrechnung zu beenden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen der eigenständigen SaaS-Anwendung Wingtip Tickets
> * Informationen zu Servern und Datenbanken, aus denen sich die App zusammensetzt
> * Löschen von Beispielressourcen, um die zugehörige Abrechnung einzustellen

Arbeiten Sie als Nächstes das Tutorial [Bereitstellen und Katalogisieren](saas-standaloneapp-provision-and-catalog.md) durch, in dem Sie die Verwendung eines Katalogs von Mandanten untersuchen, die viele mandantenübergreifende Szenarien ermöglicht, z.B. Schemaverwaltung oder Mandantenanalyse.
