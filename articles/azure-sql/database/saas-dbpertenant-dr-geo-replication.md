---
title: Notfallwiederherstellung für SaaS-Apps mit Georeplikation
description: Erfahren Sie, wie Sie Azure SQL-Datenbank-Georeplikationen verwenden können, um eine mehrinstanzenfähige SaaS-Anwendung nach einem Ausfall wiederherzustellen.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: ffe5a1d0c9bbdbc416ecce7c36b3710339c4f059
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781021"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Notfallwiederherstellung für eine mehrinstanzenfähige SaaS-Anwendung über Datenbankgeoreplikation
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial erkunden Sie ein vollständiges Szenario zur Notfallwiederherstellung für eine mehrinstanzenfähige SaaS-Anwendung, die mit dem Modell implementiert wurde, bei dem eine Datenbank pro Mandant verwendet wird. Um die App vor einem Ausfall zu schützen, verwenden Sie [_Georeplikation_](active-geo-replication-overview.md), um Replikate für die Katalog- und Mandantendatenbanken in einer anderen Wiederherstellungsregion zu erstellen. Wenn es einen Ausfall gibt, können Sie schnell ein Failover zu diesen Replikaten ausführen, um den normalen Geschäftsbetrieb fortzusetzen. Bei einem Failover werden die Datenbanken in der ursprünglichen Region zu sekundären Replikaten der Datenbanken in der Wiederherstellungsregion. Sobald diese Replikate wieder online geschaltet wurden, werden sie automatisch in den Zustand versetzt, den die Datenbanken in der Wiederherstellungsregion haben. Nach Behebung des Ausfalls führen Sie ein Failback zu den Datenbanken in der ursprünglichen Produktionsregion aus.

In diesem Tutorial werden sowohl der Failover- als auch der Failbackworkflow erläutert. Sie lernen Folgendes:
> [!div class="checklist"]
> 
> * Synchronisieren der Datenbankinformationen sowie der Konfigurationsinformationen für den Pool für elastische Datenbanken im Mandantenkatalog
> * Einrichten einer Wiederherstellungsumgebung in einer anderen Region, wobei die Umgebung die Anwendung, Server und Pools umfasst
> * Verwenden von _Georeplikation_ , um die Katalog- und Mandantendatenbanken in der Wiederherstellungsregion zu replizieren
> * Ausführen eines Failovers der Anwendung sowie der Katalog- und Mandantendatenbanken in die Wiederherstellungsregion 
> * Später Ausführen eines Failovers der Anwendung sowie der Katalog- und Mandantendatenbanken zurück in die ursprüngliche Region, nachdem der Ausfall behoben wurde
> * Aktualisieren des Katalogs, sobald ein Failover für eine Mandantendatenbank ausgeführt wurde, um den primären Speicherort der Datenbank des jeweiligen Mandanten nachzuverfolgen
> * Sicherstellen, dass sich die Anwendung und die primäre Mandantendatenbank immer zusammen in derselben Azure-Region befinden, um die Wartezeit zu verringern  
 

Bevor Sie mit diesem Tutorial beginnen, sollten Sie sich vergewissern, dass die folgenden Voraussetzungen erfüllt sind:
* Die Wingtip Tickets-SaaS-App mit einer Datenbank pro Mandant ist bereitgestellt. Unter [Bereitstellen und Kennenlernen der Wingtip Tickets SaaS-App mit einer Datenbank pro Mandant](saas-dbpertenant-get-started-deploy.md) finden Sie Informationen dazu, wie Sie die App in weniger als fünf Minuten bereitstellen.  
* Azure PowerShell wurde installiert. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Einführung in die Vorgehensweise einer Wiederherstellung mit Georeplikation

![Wiederherstellungsarchitektur](./media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Notfallwiederherstellung (NW) ist ein wichtiger Aspekt für viele Anwendungen, sei es aus Konformitätsgründen oder für Geschäftskontinuität. Sollte es zu einem längeren Dienstausfall kommen, lässt sich die Geschäftsunterbrechung mit einem gut vorbereiteten NW-Plan minimieren. Verwenden von Georeplikation ermöglicht das geringste RPO und RTO, indem Datenbankreplikate in einer Wiederherstellungsregion verwaltet werden, für die kurzfristig ein Failover ausgeführt werden kann.

Ein NW-Plan, der auf Georeplikation basiert, besteht aus drei eigenständigen Teilen:
* Einrichtung: Erstellung und Wartung der Wiederherstellungsumgebung
* Wiederherstellung: Ausführen eines Failovers der Anwendung und der Datenbanken in die Wiederherstellungsumgebung, wenn es zu einem Ausfall kommt
* Rückführung: Ausführen eines Failovers der Anwendung und der Datenbanken zurück in die ursprüngliche Region, nachdem der Ausfall behoben wurde 

Alle Teile müssen sorgfältig überlegt werden, insbesondere bei bedarfsorientiertem Betrieb. Insgesamt muss der Plan mehreren Zielen genügen:

* Einrichten
    * Einrichten und Verwalten einer Spiegelimageumgebung in der Wiederherstellungsregion. Das Erstellen der Pools für elastische Datenbanken und Replizieren aller Datenbanken in dieser Wiederherstellungsumgebung reserviert Kapazität in der Wiederherstellungsregion. Das Verwalten dieser Umgebung umfasst das Replizieren von neuen Mandantendatenbanken, während sie bereitgestellt werden.  
* Wiederherstellung
    * Sofern eine herunterskalierte Wiederherstellungsumgebung verwendet wird, um die täglichen Kosten zu minimieren, müssen Pools und Datenbanken hochskaliert werden, um vollständige Betriebskapazität in der Wiederherstellungsregion zu erreichen.
    * Ermöglichen der Bereitstellung von neuen Mandanten in der Wiederherstellungsregion so bald wie möglich.  
    * Der Plan muss für ein Wiederherstellen der Mandanten in der Reihenfolge ihrer Priorität optimiert sein.
    * Der Plan muss dafür optimiert sein, Mandanten so schnell wie möglich online schalten zu können, indem Schritte nach Möglichkeit parallel ausgeführt werden.
    * Der Plan muss ausfallsicher, neu startbar und idempotent sein.
    * Es muss möglich sein, den Prozess während der Ausführung abzubrechen, wenn die ursprüngliche Region wieder online geschaltet wurde.
* Rückführung 
    * Ausführen eines Failovers von Datenbanken aus der Wiederherstellungsregion in Replikate in der ursprünglichen Region mit minimaler Auswirkung auf Mandanten: kein Verlust von Daten und möglichst kurze Offlinezeit pro Mandant.   

In diesem Tutorial werden diese Herausforderungen mit Funktionen von Azure SQL-Datenbank und der Azure-Plattform gemeistert:

* [Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), um jegliche erforderliche Kapazität so schnell wie möglich zu reservieren. Azure Resource Manager-Vorlagen werden verwendet, um ein Spiegelimage der Produkionsserver und der Pools für elastische Datenbanken in der Wiederherstellungsregion bereitzustellen.
* [Georeplikation](active-geo-replication-overview.md), um für alle Datenbanken asynchron replizierte schreibgeschützte sekundäre Datenbanken zu erstellen. Bei einem Ausfall führen Sie ein Failover zu den Replikaten in der Wiederherstellungsregion aus.  Nach Behebung des Ausfalls führen Sie ein Failback zu den Datenbanken in der ursprünglichen Region aus, ohne dass Daten verloren gehen.
* [Asynchrone](../../azure-resource-manager/management/async-operations.md) Failovervorgänge, die in der Prioritätsreihenfolge der Mandanten gesendet werden, um die Failoverzeit für eine große Anzahl von Datenbanken zu minimieren.
* [Shardverwaltungs-Wiederherstellungsfunktionen](elastic-database-recovery-manager.md), um Datenbankeinträge im Katalog während einer Wiederherstellung und Rückführung zu ändern. Diese Funktionen ermöglichen es der Anwendung, Verbindungen mit den Mandantendatenbanken unabhängig vom Speicherort herzustellen, ohne die Anwendung neu konfigurieren zu müssen.
* [DNS-Aliase für SQL-Server](./dns-alias-overview.md), um eine nahtlose Bereitstellung von neuen Mandanten zu ermöglichen, unabhängig davon, in welcher Region die Anwendung ausgeführt wird. DNS-Aliase werden auch verwendet, damit der Katalogsynchronisierungsprozess eine Verbindung mit dem aktiven Katalog herstellen kann, egal, wo sich dieser befindet.

## <a name="get-the-disaster-recovery-scripts"></a>Abrufen der Notfallwiederherstellungsskripts 

> [!IMPORTANT]
> Wie alle Wingtip Tickets-Verwaltungsskripts haben die NW-Skripts die Qualität von Beispielskripts und sollten daher nicht in einer Produktionsumgebung verwendet werden. 

Die Wiederherstellungsskripts, die in diesem Tutorial verwendet werden, und der Quellcode der Wingtip-Anwendung sind im [GitHub-Repository für Wingtip Tickets SaaS-Datenbank pro Mandant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) verfügbar. Lesen Sie die Schritte zum Herunterladen und Entsperren der Wingtip Tickets-Verwaltungsskripts unter [Allgemeine Hinweise](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="tutorial-overview"></a>Übersicht über das Tutorial
In diesem Tutorial verwenden Sie zunächst Georeplikation, um Replikate der Wingtip Tickets-Anwendung und von deren Datenbanken in einer anderen Region zu erstellen. Anschließend führen Sie ein Failover zu dieser Region aus, um ein Wiederherstellen nach einem Ausfall zu simulieren. Nach Abschluss des Vorgangs ist die Anwendung in der Wiederherstellungsregion voll funktionsfähig.

Später, in einem separaten Rückführungsschritt, führen Sie ein Failover des Katalogs und der Mandantendatenbanken in der Wiederherstellungsregion zu der ursprünglichen Region aus. Die Anwendung und die Datenbanken bleiben während der Rückführung durchgängig verfügbar. Nach Abschluss des Vorgangs ist die Anwendung in der ursprünglichen Region voll funktionsfähig.

> [!Note]
> Die Anwendung wird in dem _Regionspaar_ der Region wiederhergestellt, in dem die Anwendung bereitgestellt wird. Weitere Informationen finden Sie unter [Azure-Regionspaare](../../best-practices-availability-paired-regions.md).

## <a name="review-the-healthy-state-of-the-application"></a>Überprüfen des ordnungsgemäßen Zustands der Anwendung

Bevor Sie den Wiederherstellungsprozess starten, sollten Sie den normalen ordnungsgemäßen Zustand der Anwendung überprüfen.
1. Öffnen Sie in Ihrem Webbrowser den Wingtip Tickets-Event Hub (http://events.wingtip-dpt.&lt;Benutzer&gt;.trafficmanager.net). Ersetzen Sie dabei &lt; Benutzer&gt; durch den Benutzerwert Ihrer Bereitstellung.
    * Scrollen Sie zum unteren Rand der Seite, und beachten Sie den Katalogservernamen und den Standort in der Fußzeile. Der Standort entspricht die Region, in der Sie die App bereitgestellt haben.
    *TIPP: Zeigen Sie mit der Maus auf den Standort, um die Anzeige zu vergrößern.* 
    ![Ordnungsgemäßer Zustand des Veranstaltungshubs in der ursprünglichen Region](./media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klicken Sie auf den Mandanten „Contoso Concert Hall“, um dessen Veranstaltungsseite zu öffnen.
    * Beachten Sie in der Fußzeile den Servernamen des Mandanten. Der Standort ist mit dem Standort des Katalogservers identisch.

3. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Ressourcengruppe, in der die App bereitgestellt wird.
    * Beachten Sie die Region, in der die Server bereitgestellt werden. 

## <a name="sync-tenant-configuration-into-catalog"></a>Synchronisieren der Mandantenkonfiguration in den Katalog

In dieser Aufgabe starten Sie einen Prozess, mit dem die Konfiguration der Server, der Pools für elastische Datenbanken und der Datenbanken in den Mandantenkatalog synchronisiert wird. Der Prozess hält diese Informationen im Katalog auf dem neuesten Stand.  Der Prozess arbeitet mit dem aktiven Katalog, unabhängig davon, ob dieser sich in der ursprünglichen Region oder in der Wiederherstellungsregion befindet. Die Konfigurationsinformationen werden im Wiederherstellungsprozess verwendet, um sicherzustellen, dass die Wiederherstellungsumgebung mit der ursprünglichen Umgebung konsistent ist, und werden dann später während der Rückführung verwendet, um sicherzustellen, dass jegliche Änderungen, die in der Wiederherstellungsumgebung vorgenommen wurden, in die ursprüngliche Region übernommen werden. Der Katalog wird auch verwendet, um den Wiederherstellungsstatus von Mandantenressourcen nachzuverfolgen.

> [!IMPORTANT]
> Der Einfachheit halber werden der Synchronisierungsprozess und andere zeitintensive Wiederherstellungs- und Rückführungsprozesse in diesen Tutorials als lokale PowerShell-Aufträge oder -Sitzungen implementiert, die unter Ihrer Clientbenutzeranmeldung ausgeführt werden. Die Authentifizierungstoken, die ausgegeben werden, wenn Sie sich anmelden, laufen nach einigen Stunden ab, und die Aufträge schlagen dann fehl. In einem Produktionsszenario sollten lange dauernde Prozesse als zuverlässige Azure-Dienste beliebiger Art implementiert werden, die unter einem Dienstprinzipal ausgeführt werden. Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell zum Erstellen eines Dienstprinzipals mit einem Zertifikat](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

1. Öffnen Sie in _PowerShell ISE_ die Datei „...\Learning Modules\UserConfig.psm1“. Ersetzen Sie `<resourcegroup>` und `<user>` in den Zeilen 10 und 11 durch die jeweiligen Werte, die Sie beim Bereitstellen der App verwendet haben.  Speichern Sie die Datei.

2. Öffnen Sie in *PowerShell ISE* das Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1“, und legen Sie Folgendes fest:
    * **$DemoScenario = 1** : Starten eines Hintergrundauftrags, in dem Mandantenserver- und Poolkonfigurationsinformationen in den Katalog synchronisiert werden

3. Drücken Sie **F5** , um das Synchronisierungsskript auszuführen. Es wird eine neue PowerShell-Sitzung geöffnet, in der die Konfiguration von Mandantenressourcen synchronisiert wird.
![Screenshot mit der neuen PowerShell-Sitzung, die geöffnet wurde, um die Konfiguration von Mandantenressourcen zu synchronisieren](./media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Lassen Sie das PowerShell-Fenster im Hintergrund weiter laufen, und führen Sie die weiteren Schritte des Tutorials aus. 

> [!Note]
> Im Synchronisierungsprozess wird die Verbindung mit dem Katalog über einen DNS-Alias hergestellt. Dieser Alias wird während der Wiederherstellung und Rückführung so geändert, dass er auf den aktiven Katalog verweist. Der Synchronisierungsprozess hält den Katalog mit jeder Datenbank- oder Poolkonfigurationsänderung, die in der Wiederherstellungsregion vorgenommen wurde, auf dem neuesten Stand.  Während der Rückführung werden diese Änderungen auf die entsprechenden Ressourcen in der ursprünglichen Region angewendet.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Erstellen von sekundären Datenbankreplikaten in der Wiederherstellungsregion

In dieser Aufgabe starten Sie einen Prozess, in dem eine duplizierte Anwendungsinstanz bereitgestellt wird sowie die Katalog- und alle Mandantendatenbanken in einer Wiederherstellungsregion repliziert werden.

> [!Note]
> In diesem Tutorial wird der Wingtip Tickets-Beispielanwendung Schutz durch Georeplikation hinzugefügt. In einem Produktionsszenario für eine Anwendung, für die Georeplikation verwendet wird, würde jeder Mandant von Anfang an mit einer georeplizierten Datenbank bereitgestellt. Weitere Informationen finden Sie unter [Entwerfen eines hoch verfügbaren Diensts mit Azure SQL-Datenbank](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime).

1. Öffnen Sie in *PowerShell ISE* das Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1“, und legen Sie die folgenden Werte fest:
    * **$DemoScenario = 2** : Erstellen einer Spiegelimage-Wiederherstellungsumgebung und Replizieren von Katalog- und Mandantendatenbanken

2. Drücken Sie **F5** , um das Skript auszuführen. Es wird neue PowerShell-Sitzung geöffnet, um die Replikate zu erstellen.
![Synchronisierungsprozess](./media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Überprüfen des normalen Anwendungszustands

An diesem Punkt wird die Anwendung normal in der ursprünglichen Region ausgeführt, und sie ist durch Georeplikation geschützt.  Schreibgeschützte sekundäre Replikate befinden sich für alle Datenbanken in der Wiederherstellungsregion. 

1. Sehen Sie sich im Azure-Portal Ihre Ressourcengruppen an, und Sie stellen fest, dass in der Wiederherstellungsregion eine Ressourcengruppe mit dem Suffix „-recovery“ erstellt wurde. 

2. Untersuchen Sie die Ressourcen in der Wiederherstellungsressourcengruppe.  

3. Klicken Sie auf die Contoso Concert Hall-Datenbank auf dem _tenants1-dpt -&lt;Benutzer&gt;-recovery_ -Server.  Klicken Sie auf „Georeplikation“ auf der linken Seite. 

    ![Georeplikationslink für Contoso Concert](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Sehen Sie sich auf dieser Karte der Azure-Regionen den Georeplikationslink zwischen der primären Datenbank in der ursprünglichen Region und der sekundären Datenbank in der Wiederherstellungsregion an.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Ausführen eines Failovers für die Anwendung in die Wiederherstellungsregion

### <a name="geo-replication-recovery-process-overview"></a>Übersicht über den Wiederherstellungsprozess mit Georeplikation

In dem Wiederherstellungsskript werden folgende Aufgaben ausgeführt:

1. Der Traffic Manager-Endpunkt für die Web-App in der ursprünglichen Region wird deaktiviert. Durch Deaktivieren des Endpunkts wird verhindert, dass Benutzer eine Verbindung mit der App in einem ungültigen Zustand herstellen, sollte die ursprüngliche Region während der Wiederherstellung wieder online geschaltet werden.

1. Es wird ein Failover der Katalogdatenbank in der Wiederherstellungsregion erzwungen, um diese Datenbank zur primären Datenbank zu machen, und der _activecatalog_ -Alias wird aktualisiert, sodass er auf den Wiederherstellungskatalogserver verweist.

1. Der _newtenant_ -Alias wird aktualisiert, sodass er auf den Mandantenserver in der Wiederherstellungsregion verweist. Durch Ändern dieses Alias wird sichergestellt, dass die Datenbanken für jeden neuen Mandanten in der Wiederherstellungsregion bereitgestellt werden. 

1. Alle vorhandenen Mandanten im Wiederherstellungskatalog werden als offline markiert, damit Zugriff auf Mandantendatenbanken verhindert wird, bevor für diese ein Failover ausgeführt wurde.

1. Die Konfiguration aller Pools für elastische Datenbanken und replizierten Einzeldatenbanken in der Wiederherstellungsregion wird aktualisiert, um diese Konfiguration in der ursprünglichen Region zu spiegeln. (Diese Aufgabe ist nur erforderlich, wenn Pools oder replizierte Datenbanken in der Wiederherstellungsumgebung während des normalen Betriebs zentral herunterskaliert werden, um die Kosten zu reduzieren.)

1. Der Traffic Manager-Endpunkt für die Web-App in der Wiederherstellungsregion wird aktiviert. Durch das Aktivieren dieses Endpunkts wird es der Anwendung ermöglicht, neue Mandanten bereitzustellen. Zu diesem Zeitpunkt sind die vorhandenen Mandanten immer noch offline.

1. Es werden Batches mit Anforderungen gesendet, um ein Failover für die Datenbanken in der Reihenfolge ihrer Priorität zu erzwingen.
    * Die Batches sind so konzipiert, dass das Failover der Datenbanken parallel über alle Pools ausgeführt wird.
    * Failoveranforderungen werden in asynchronen Vorgängen gesendet, damit sie schnell gesendet werden und mehrere Anforderungen gleichzeitig verarbeitet werden können.

   > [!Note]
   > In einem Ausfallszenario sind die primären Datenbanken in der ursprünglichen Region offline.  Bei einem erzwungenen Failover auf dem sekundären Server wird die Verbindung zum primären Server getrennt, ohne dass versucht wird, Transaktionen auszuführen, in der Warteschlange verblieben sind. In einem Übungsszenario für Notfallwiederherstellung, wie in diesem Tutorial, kann es zum Verlust einiger Daten kommen, wenn es während des Failovers Aktualisierungsaktivitäten gibt. Später bei der Rückführung, wenn Sie für die Datenbanken in der Wiederherstellungsregion ein Failover zur ursprünglichen Region ausführen, wird ein normales Failover verwendet, damit sichergestellt ist, dass kein Datenverlust auftritt.

1. Der Dienst wird überwacht, um zu bestimmen, wann ein Failover für Datenbanken ausgeführt wurde. Sobald für eine Mandantendatenbank ein Failover ausgeführt wurde, wird der Katalog aktualisiert, um den Wiederherstellungsstatus der Mandantendatenbank aufzuzeichnen, und der Mandant wird als online markiert.
    * Die Anwendung kann auf die Mandantendatenbanken zugreifen, sobald diese im Katalog als online markiert sind.
    * Eine Summe der „rowversion“-Werte in der Mandantendatenbank wird im Katalog gespeichert. Dieser Wert fungiert als ein Fingerabdruck, über den der Rückführungsprozess bestimmen kann, ob die Datenbank in der Wiederherstellungsregion aktualisiert wurde.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Ausführen des Skripts, um ein Failover zur Wiederherstellungsregion auszuführen

Stellen Sie sich nun vor, dass es einen Ausfall in der Region gibt, in der die Anwendung bereitgestellt wird, und führen Sie das Wiederherstellungsskript aus:

1. Öffnen Sie in *PowerShell ISE* das Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1“, und legen Sie die folgenden Werte fest:
    * **$DemoScenario = 3** : Wiederherstellen der Anwendung in einer Wiederherstellungsregion, indem ein Failover zu Replikaten ausgeführt wird

2. Drücken Sie **F5** , um das Skript auszuführen.  
    * Das Skript wird in einem neuen PowerShell-Fenster geöffnet, und in dem Skript wird eine Reihe von PowerShell-Aufträgen gestartet, die parallel ausgeführt werden. In diesen Aufträgen wird ein Failover der Mandantendatenbanken zur Wiederherstellungsregion ausgeführt.
    * Die Wiederherstellungsregion ist die _gekoppelte Region_ , die der Azure-Region zugeordnet ist, in der Sie die Anwendung bereitgestellt haben. Weitere Informationen finden Sie unter [Azure-Regionspaare](../../best-practices-availability-paired-regions.md). 

3. Überwachen Sie den Status des Wiederherstellungsprozesses im PowerShell-Fenster.
    ![Failoverprozess](./media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Um den Code für die Wiederherstellungsaufträge zu untersuchen, sehen Sie sich die PowerShell-Skripts an, die im Ordner „...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs“ enthalten sind.

### <a name="review-the-application-state-during-recovery"></a>Überprüfen des Anwendungszustands während der Wiederherstellung

Solange der Anwendungsendpunkt in Traffic Manager deaktiviert ist, ist die Anwendung nicht verfügbar. Nachdem für den Katalog ein Failover zur Wiederherstellungsregion ausgeführt wurde und alle Mandanten als offline markiert wurden, wird die Anwendung wieder online geschaltet. Obwohl die Anwendung verfügbar ist, wird jeder Mandant im Veranstaltungshub (Events Hub) als offline angezeigt, bis ein Failover für seine Datenbank ausgeführt wurde. Sie müssen Ihre Anwendung so konzipieren, dass sie mit Mandantendatenbanken umgehen kann, die offline sind.

1. Aktualisieren Sie den Wingtip Tickets-Veranstaltungshub (Events Hub) in Ihrem Webbrowser unmittelbar, nachdem die Katalogdatenbank wiederhergestellt wurde.
   * Sie können in der Fußzeile sehen, dass der Name des Katalogservers jetzt das Suffix _-recovery_ hat und der Server sich in der Wiederherstellungsregion befindet.
   * Mandanten, die noch nicht wiederhergestellt wurden, sind als offline markiert und können nicht ausgewählt werden.  

     > [!Note]
     > Sind nur einige Datenbanken wiederherzustellen, kann es sein, dass Sie den Browser nicht vor Abschluss der Wiederherstellung aktualisieren können. Daher können Sie die Mandanten möglicherweise nicht sehen, während sie offline sind. 
 
     ![Veranstaltungshub (Events Hub) ist offline](./media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Wenn Sie die „Veranstaltungen“-Seite eines Mandanten, der offline ist, direkt öffnen, wird auf der Seite eine „Mandant ist offline“-Benachrichtigung angezeigt. Versuchen Sie beispielsweise, wenn Contoso Concert Hall offline ist, „http://events.wingtip-dpt.&lt;Benutzer&gt;.trafficmanager.net/contosoconcerthall“ zu öffnen.![ Contoso-Seite für offline](./media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Bereitstellen eines neuen Mandanten in der Wiederherstellungsregion
Schon bevor für alle vorhandenen Mandantendatenbanken ein Failover ausgeführt wurde, können Sie neue Mandanten in der Wiederherstellungsregion bereitstellen.  

1. Öffnen Sie in *PowerShell ISE* das Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1“, und legen Sie die folgende Eigenschaft fest:
    * **$DemoScenario = 4** : Bereitstellen eines neuen Mandanten in der Wiederherstellungsregion

2. Drücken Sie **F5** , um das Skript auszuführen und den neuen Mandanten bereitzustellen. 

3. Die Seite für Hawthorn Hall-Veranstaltungen wird im Browser geöffnet, wenn das Skript abgeschlossen ist. Anhand der Fußzeile können Sie sehen, dass die Hawthorn Hall-Datenbank in der Wiederherstellungsregion bereitgestellt wird.
    ![Hawthorn Hall-Seite für Veranstaltungen](./media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. Aktualisieren Sie im Browser die Wingtip Tickets-Seite für den Veranstaltungshub (Events Hub), damit Hawthorn Hall auf der Seite enthalten ist. 
    * Wenn Sie Hawthorn Hall bereitgestellt haben, ohne zu warten, bis die anderen Mandanten wiederherstellt waren, sind andere Mandanten möglicherweise noch offline.


## <a name="review-the-recovered-state-of-the-application"></a>Überprüfen des wiederhergestellten Status der Anwendung

Wenn der Wiederherstellungsprozess abgeschlossen ist, sind die Anwendung und alle Mandanten in der Wiederherstellungsregion voll funktionsfähig. 

1. Sobald im PowerShell-Konsolenfenster angezeigt wird, dass alle Mandanten wiederhergestellt sind, aktualisieren Sie den Veranstaltungshub.  Die Mandanten werden als online angezeigt, so auch der neue Mandant Hawthorn Hall.

    ![Wiederhergestellte und neue Mandanten im Veranstaltungshub](./media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Liste der Ressourcengruppen.  
    * Beachten Sie die Ressourcengruppe, die Sie bereitgestellt haben, und die Wiederherstellungsressourcengruppe mit dem Suffix _-recovery_ .  Die Wiederherstellungsressourcengruppe enthält alle Ressourcen, die während des Wiederherstellungsprozesses erstellt wurden, sowie neue Ressourcen, die während des Ausfalls erstellt wurden.  

3. Öffnen Sie die Wiederherstellungsressourcengruppe, und beachten Sie die folgenden Elemente:
   * Die Wiederherstellungsversionen des Katalog- und des tenants1-Servers mit dem Suffix _-recovery_ .  Die wiederhergestellten Katalog- und Mandantendatenbanken auf diesen Servern haben alle die Namen, die in der ursprünglichen Region verwendet wurden.

   * Der SQL-Server _tenants2-dpt-&lt;Benutzer&gt;-recovery_ .  Dieser Server wird dazu verwendet, neue Mandanten während des Ausfalls bereitzustellen.
   * Der App Service namens _events-wingtip-dpt-&lt;recoveryregion&gt;-&lt;Benutzer&gt_ . Dies ist die Wiederherstellungsinstanz der Veranstaltungen-App. 

     ![Azure-Wiederherstellungsressourcen](./media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Öffnen Sie den SQL-Server _tenants2-dpt-&lt;Benutzer&gt;-recovery_ .  Dieser enthält die Datenbank _hawthornhall_ und den Pool für elastische Datenbanken _Pool1_ .  Die _hawthornhall_ -Datenbank ist im Pool für elastische Datenbanken _Pool1_ als eine elastische Datenbank konfiguriert.

5. Navigieren Sie zurück zur Ressourcengruppe, und klicken Sie auf die Contoso Concert Hall-Datenbank auf dem _tenants1-dpt -&lt;Benutzer&gt;-recovery_ -Server. Klicken Sie auf „Georeplikation“ auf der linken Seite.
    
    ![Contoso-Datenbank nach Failover](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Ändern von Mandantendaten 
In dieser Aufgabe aktualisieren Sie eine der Mandantendatenbanken. 

1. Suchen Sie in Ihrem Browser nach der Veranstaltungsliste für die Contoso Concert Hall, und notieren Sie sich den letzten Veranstaltungsnamen.
2. Legen Sie in *PowerShell ISE* im Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1“ den folgenden Wert fest:
    * **$DemoScenario = 5** : Löschen einer Veranstaltung aus einem Mandanten in der Wiederherstellungsregion
3. Drücken Sie **F5** , um das Skript auszuführen.
4. Aktualisieren Sie die Contoso Concert Hall-Seite für Veranstaltungen (http://events.wingtip-dpt.&lt;Benutzer&gt;.trafficmanager.net/contosoconcerthall – ersetzen Sie &lt; Benutzer&gt; durch den Benutzerwert Ihrer Bereitstellung). Sie können sehen, dass die letzte Veranstaltung gelöscht wurde.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Zurückführen der Anwendung in ihre ursprüngliche Produktionsregion

In dieser Aufgabe wird die Anwendung in ihre ursprüngliche Region zurückgeführt. In einem echten Szenario würden Sie die Rückführung einleiten, wenn der Ausfall behoben ist.

### <a name="repatriation-process-overview"></a>Übersicht über den Rückführungsprozess

![Rückführungsarchitektur](./media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Im Rückführungsprozess wird Folgendes ausgeführt:
1. Alle ausstehenden oder wirksamen Anforderungen zur Datenbankwiederherstellung werden abgebrochen.
2. Der _newtenant_ -Alias wird aktualisiert, sodass er auf den Server der Mandanten in der ursprünglichen Region verweist. Durch Ändern dieses Alias wird sichergestellt, dass die Datenbanken für jeden neuen Mandanten nun in der ursprünglichen Region bereitgestellt werden.
3. Alle geänderten Mandantendaten werden in die ursprüngliche Region übernommen.
4. Für die Mandantendatenbanken wird in der Reihenfolge ihrer Priorität ein Failover ausgeführt.

Durch das Failover werden die Datenbanken tatsächlich in den ursprünglichen Bereich verschoben. Beim Ausführen des Datenbankfailovers werden alle geöffneten Verbindungen getrennt, und die jeweilige Datenbank ist für ein paar Sekunden nicht verfügbar. Anwendungen sollten mit Wiederholungslogik geschrieben werden, um sicherzustellen, dass sie Verbindungen erneut herstellen.  Obwohl diese kurze Verbindungstrennung häufig nicht bemerkt wird, ist es vorteilhaft, Datenbanken außerhalb der Geschäftszeiten zurückzuführen. 


### <a name="run-the-repatriation-script"></a>Ausführen des Rückführungsskripts
Nehmen Sie nun an, dass der Ausfall behoben ist, und führen Sie das Rückführungsskript aus.

1. Vergewissern Sie sich in *PowerShell ISE* im Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1“,

2. dass der Katalogsynchronisierungsprozess weiterhin in seiner PowerShell-Instanz ausgeführt wird.  Starten Sie ihn bei Bedarf neu, indem Sie Folgendes festlegen:
    * **$DemoScenario = 1** : Starten der Synchronisierung der Mandantenserver-, Pool- und Datenbankkonfigurationsinformationen in den Katalog
    * Drücken Sie **F5** , um das Skript auszuführen.

3.  Legen Sie dann Folgendes fest, um den Rückführungsprozess zu starten:
    * **$DemoScenario = 6** : Rückführen der App in ihre ursprüngliche Region
    * Drücken Sie **F5** , um das Wiederherstellungsskript in einem neuen PowerShell-Fenster auszuführen.  Die Rückführung dauert einige Minuten und kann im PowerShell-Fenster überwacht werden.
    ![Rückführungsprozess](./media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Aktualisieren Sie während der Skriptausführung die Seite für den Event Hub (http://events.wingtip-dpt.&lt;Benutzer&gt;.trafficmanager.net).
    * Sie können sehen, dass alle Mandanten im gesamten Verlauf dieses Prozesses online und für Zugriffe verfügbar sind.

5. Nach Abschluss der Rückführung aktualisieren Sie den Veranstaltungshub (Events Hub), und öffnen Sie die Hawthorn Hall-Seite für Veranstaltungen. Sie können sehen, dass diese Datenbank in ihre ursprüngliche Region zurückgeführt wurde.
    ![Veranstaltungshub ist zurückgeführt](./media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Entwerfen der Anwendung, sodass Anwendung und Datenbank in derselben Region zusammengestellt werden 
Die Anwendung ist so entworfen, dass Verbindungen in ihr immer aus einer Instanz in der Region hergestellt werden, in der sich auch die Mandantendatenbank befindet. Dieser Entwurf verringert die Wartezeit zwischen der Anwendung und der Datenbank. Für diese Optimierung wird davon ausgegangen, dass Interaktionen zwischen der App und der Datenbank wesentlich häufiger sind als Interaktionen zwischen Benutzern und der App.  

Mandantendatenbanken können während einer Rückführung für einige Zeit über die Wiederherstellungs- und die ursprüngliche Region verteilt sein. Für jede Datenbank sucht die App über eine DNS-Suche zu dem Namen des Mandantenservers nach der Region, in der sich die Datenbank befindet. In SQL-Datenbank ist der Servername ein Alias. Der als Alias vorliegende Servername enthält der Namen der Region. Wenn sich die Anwendung nicht in derselben Region wie die Datenbank befindet, erfolgt eine Umleitung zu der Instanz, die sich in derselben Region wie der Server befindet. Das Umleiten zu der Instanz in derselben Region wie die Datenbank minimiert die Wartezeit zwischen der App und der Datenbank. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:
> [!div class="checklist"]
> 
> * Synchronisieren der Datenbankinformationen sowie der Konfigurationsinformationen für den Pool für elastische Datenbanken im Mandantenkatalog
> * Einrichten einer Wiederherstellungsumgebung in einer anderen Region, wobei die Umgebung die Anwendung, Server und Pools umfasst
> * Verwenden von _Georeplikation_ , um die Katalog- und Mandantendatenbanken in der Wiederherstellungsregion zu replizieren
> * Ausführen eines Failovers der Anwendung sowie der Katalog- und Mandantendatenbanken in die Wiederherstellungsregion 
> * Ausführen eines Failbacks der Anwendung sowie der Katalog- und Mandantendatenbanken in die ursprüngliche Region, nachdem der Ausfall behoben wurde

Weitere Informationen zu den Technologien, die Azure SQL-Datenbank bereitstellt, um Geschäftskontinuität zu ermöglichen, finden Sie in der Dokumentation [Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Zusätzliche Tutorials, die auf der Wingtip-SaaS-Anwendung aufbauen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)