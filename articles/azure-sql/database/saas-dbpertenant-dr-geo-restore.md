---
title: 'SaaS-Apps: Georedundante Sicherungen für die Notfallwiederherstellung'
description: Erfahren Sie, wie Sie georedundante Azure SQL-Datenbank-Sicherungen verwenden, um eine mehrinstanzenfähige SaaS-App nach einem Ausfall wiederherzustellen.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 3fe6095595f5270b18536e6ef46afe4a0a5b3268
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607710"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Verwenden der Geowiederherstellung zum Wiederherstellen einer mehrinstanzenfähigen SaaS-Anwendung aus Datenbanksicherungen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Tutorial wird ein vollständiges Szenario zur Notfallwiederherstellung für eine mehrinstanzenfähige SaaS-Anwendung beschrieben, die mit dem Modell implementiert wurde, bei dem eine Datenbank pro Mandant verwendet wird. Sie verwenden die [Geowiederherstellung](recovery-using-backups.md), um den Katalog und die Mandantendatenbanken aus automatisch verwalteten georedundanten Sicherungen in einer anderen Wiederherstellungsregion wiederherzustellen. Nach Behebung des Ausfalls verwenden Sie die [Georeplikation](active-geo-replication-overview.md), um geänderte Datenbanken in ihre ursprüngliche Region zurückzuführen.

![Das Diagramm zeigt eine ursprüngliche Region und eine Wiederherstellungsregion. Beide verfügen über eine App, einen Katalog, Originalserver und -pools bzw. Spiegelabbilder von Servern und Pools und automatische Sicherungen im Speicher. Die Wiederherstellungsregion akzeptiert dabei die Georeplikation der Sicherung sowie Server und Pool als neue Mandanten.](./media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Die Geowiederherstellung ist die kostengünstigste Notfallwiederherstellungslösung für Azure SQL-Datenbank. Die Wiederherstellung aus georedundanten Sicherungen kann jedoch zu einem Datenverlust von bis zu einer Stunde führen. Der Vorgang kann je nach Größe der einzelnen Datenbanken einige Zeit in Anspruch nehmen. 

> [!NOTE]
> Verwenden Sie die Georeplikation anstelle der Geowiederherstellung, um Anwendungen mit der geringstmöglichen RPO und RTO wiederherzustellen.

In diesem Tutorial werden sowohl der Wiederherstellungs- als auch der Rückführungsworkflow vorgestellt. Folgendes wird vermittelt:
> [!div class="checklist"]
> 
> * Datenbankinformationen und Konfigurationsinformationen für den Pool für elastische Datenbanken im Mandantenkatalog synchronisieren.
> * Eine Spiegelimageumgebung in einer Wiederherstellungsregion einrichten, in der die Anwendung sowie die Server und Pools enthalten sind.   
> * Den Katalog und die Mandantendatenbanken mithilfe der Geowiederherstellung wiederherstellen.
> * Den Mandantenkatalog und die geänderten Mandantendatenbanken nach Behebung des Ausfalls mithilfe der Georeplikation zurückführen.
> * Den Katalog aktualisieren, nachdem jede Datenbank wiederhergestellt (oder zurückgeführt) wurde, um den aktuellen Speicherort der aktiven Kopie der Datenbank jedes Mandanten nachzuverfolgen.
> * Sicherstellen, dass sich die Anwendung und die Mandantendatenbank immer zusammen in derselben Azure-Region befinden, um die Wartezeit zu verringern. 
 

Bevor Sie mit diesem Tutorial beginnen, müssen Sie die folgenden Voraussetzungen erfüllen:
* Stellen Sie die Wingtip Tickets-SaaS-App mit einer Datenbank pro Mandant bereit. Unter [Bereitstellen und Kennenlernen der Wingtip Tickets SaaS-App mit einer Datenbank pro Mandant](saas-dbpertenant-get-started-deploy.md) finden Sie Informationen dazu, wie Sie die Bereitstellung in weniger als fünf Minuten vornehmen. 
* Installieren Sie Azure PowerShell. Ausführliche Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Einführung in die Vorgehensweise einer Geowiederherstellung

Notfallwiederherstellung (NW) ist ein wichtiger Aspekt für viele Anwendungen, sei es aus Konformitätsgründen oder für Geschäftskontinuität. Sollte es zu einem längeren Dienstausfall kommen, können Sie die Geschäftsunterbrechung mit einem gut vorbereiteten Notfallwiederherstellungsplan minimieren. Ein NW-Plan, der auf Geowiederherstellung basiert, muss mehrere Ziele erfüllen:
 * Jegliche erforderliche Kapazität in der ausgewählten Wiederherstellungsregion muss so schnell wie möglich reserviert werden, damit sichergestellt ist, dass sie zum Wiederherstellen der Mandantendatenbanken verfügbar ist.
 * Es muss eine Umgebung für die Spiegelimagewiederherstellung eingerichtet werden, die der ursprünglichen Pool- und Datenbankkonfiguration entspricht. 
 * Der Wiederherstellungsvorgang muss während der Ausführung abgebrochen werden können, wenn die ursprüngliche Region wieder online geschaltet wird.
 * Eine schnelle Mandantenbereitstellung, damit das Onboarding neuer Mandanten so schnell wie möglich wieder gestartet werden kann.
 * Der Plan muss für das Wiederherstellen der Mandanten in der Reihenfolge ihrer Priorität optimiert sein.
 * Der Plan muss dafür optimiert sein, Mandanten schnellstmöglich online schalten zu können, indem Schritte nach Möglichkeit parallel ausgeführt werden.
 * Der Plan muss ausfallsicher, neu startbar und idempotent sein.
 * Rückführen von Datenbanken in ihre ursprüngliche Region mit minimaler Auswirkung auf Mandanten, wenn der Ausfall behoben ist.  

> [!NOTE]
> Die Anwendung wird in der gekoppelten Region der Region wiederhergestellt, in der die Anwendung bereitgestellt wird. Weitere Informationen finden Sie unter [Azure-Regionspaare](../../best-practices-availability-paired-regions.md).   

In diesem Tutorial werden Features von Azure SQL-Datenbank und der Azure-Plattform zur Bewältigung der folgenden Herausforderungen verwendet:

* [Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), um jegliche erforderliche Kapazität so schnell wie möglich zu reservieren. Azure Resource Manager-Vorlagen werden verwendet, um ein Spiegelimage der ursprünglichen Server und der Pools für elastische Datenbanken in der Wiederherstellungsregion bereitzustellen. Außerdem werden ein separater Server und Pool für die Bereitstellung neuer Mandanten erstellt.
* [Clientbibliothek für elastische Datenbanken](elastic-database-client-library.md) (CBED), um einen Mandantendatenbankkatalog zu erstellen und zu verwalten. Der erweiterte Katalog enthält regelmäßig aktualisierte Pool- und Datenbankkonfigurationsinformationen.
* [Shardverwaltungs-Wiederherstellungsfunktionen](elastic-database-recovery-manager.md) der CBED, um Datenbankspeicherorteinträge im Katalog während einer Wiederherstellung und Rückführung zu verwalten.  
* [Geowiederherstellung](../../key-vault/general/disaster-recovery-guidance.md), um den Katalog und die Mandantendatenbanken aus automatisch verwalteten georedundanten Sicherungen wiederherzustellen. 
* [Asynchrone Wiederherstellungsvorgänge](../../azure-resource-manager/management/async-operations.md), die in der Mandantenprioritätsreihenfolge gesendet, vom System für jeden Pool in die Warteschlange eingereiht und in Batches verarbeitet werden, damit der Pool nicht überlastet wird. Diese Vorgänge können bei Bedarf vor oder während der Ausführung abgebrochen werden.   
* [Georeplikation](active-geo-replication-overview.md), um Datenbanken nach einem Ausfall in die ursprüngliche Region zurückzuführen. Bei Verwendung der Georeplikation gibt es keinen Datenverlust und nur minimale Auswirkungen auf den Mandanten.
* [DNS-Aliase für SQL Server](./dns-alias-overview.md), damit der Katalogsynchronisierungsprozess eine Verbindung mit dem aktiven Katalog unabhängig von dessen Speicherort herstellen kann.  

## <a name="get-the-disaster-recovery-scripts"></a>Abrufen der Notfallwiederherstellungsskripts

Die NW-Skripts, die in diesem Tutorial verwendet werden, sind im [GitHub-Repository für Wingtip Tickets SaaS-Datenbank pro Mandant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) verfügbar. Lesen Sie die Schritte zum Herunterladen und Entsperren der Wingtip Tickets-Verwaltungsskripts unter [Allgemeine Hinweise](saas-tenancy-wingtip-app-guidance-tips.md).

> [!IMPORTANT]
> Wie alle Wingtip Tickets-Verwaltungsskripts haben die NW-Skripts die Qualität von Beispielskripts und sollten daher nicht in einer Produktionsumgebung verwendet werden.

## <a name="review-the-healthy-state-of-the-application"></a>Überprüfen des ordnungsgemäßen Zustands der Anwendung
Bevor Sie den Wiederherstellungsprozess starten, sollten Sie den normalen ordnungsgemäßen Zustand der Anwendung überprüfen.

1. Öffnen Sie in Ihrem Webbrowser den Wingtip Tickets-Event Hub (http://events.wingtip-dpt.&lt;Benutzer&gt;.trafficmanager.net). Ersetzen Sie dabei &lt; Benutzer&gt; durch den Benutzerwert Ihrer Bereitstellung.
    
   Scrollen Sie zum unteren Rand der Seite, und beachten Sie den Katalogservernamen und den Standort in der Fußzeile. Der Standort entspricht die Region, in der Sie die App bereitgestellt haben.    

   > [!TIP]
   > Zeigen Sie mit der Maus auf den Standort, um die Anzeige zu vergrößern.

   ![Ordnungsgemäßer Zustand des Veranstaltungshubs in der ursprünglichen Region](./media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Wählen Sie den Mandanten „Contoso Concert Hall“ aus, und öffnen Sie dessen Veranstaltungsseite.

   Beachten Sie in der Fußzeile den Servernamen des Mandanten. Der Standort ist mit dem Standort des Katalogservers identisch.

   ![Ursprüngliche Region von „Contoso Concert Hall“](./media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)   

3. Überprüfen und öffnen Sie im [Azure-Portal](https://portal.azure.com) die Ressourcengruppe, in der Sie die App bereitgestellt haben.

   Beachten Sie die Ressourcen und die Region, in der die Dienstkomponenten der App und die SQL-Datenbank bereitgestellt werden.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synchronisieren der Mandantenkonfiguration in den Katalog

In dieser Aufgabe starten Sie einen Prozess, in dem die Konfiguration der Server, der Pools für elastische Datenbanken und der Datenbanken in den Mandantenkatalog synchronisiert wird. Diese Informationen werden später dazu verwendet, eine Spiegelimageumgebung in der Wiederherstellungsregion zu konfigurieren.

> [!IMPORTANT]
> Der Einfachheit halber werden der Synchronisierungsprozess und andere lange dauernde Wiederherstellungs- und Rückführungsprozesse in diesen Beispielen als lokale PowerShell-Aufträge oder -Sitzungen implementiert, die unter Ihrer Clientbenutzeranmeldung ausgeführt werden. Die Authentifizierungstoken, die ausgegeben werden, wenn Sie sich anmelden, laufen nach einigen Stunden ab, und die Aufträge schlagen dann fehl. In einem Produktionsszenario sollten lange dauernde Prozesse als zuverlässige Azure-Dienste beliebiger Art implementiert werden, die unter einem Dienstprinzipal ausgeführt werden. Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell zum Erstellen eines Dienstprinzipals mit einem Zertifikat](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). 

1. Öffnen Sie in PowerShell ISE die Datei „...\Learning Modules\UserConfig.psm1“. Ersetzen Sie `<resourcegroup>` und `<user>` in den Zeilen 10 und 11 durch die jeweiligen Werte, die Sie beim Bereitstellen der App verwendet haben. Speichern Sie die Datei .

2. Öffnen Sie in PowerShell ISE das Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1“.

    In diesem Tutorial führen Sie jedes der Szenarien in diesem PowerShell-Skript aus. Lassen Sie daher diese Datei geöffnet.

3. Legen Sie Folgendes fest:

    $DemoScenario = 1: Starten eines Hintergrundauftrags, bei dem Mandantenserver- und Poolkonfigurationsinformationen in den Katalog synchronisiert werden.

4. Wählen Sie F5 aus, um das Synchronisierungsskript auszuführen. 

    Diese Informationen werden später verwendet, um sicherzustellen, dass bei der Wiederherstellung ein Spiegelimage der Server, Pools und Datenbanken in der Wiederherstellungsregion erstellt wird.  

    ![Synchronisierungsprozess](./media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Lassen Sie das PowerShell-Fenster im Hintergrund weiter laufen, und führen Sie die weiteren Schritte dieses Tutorials aus.

> [!NOTE]
> Im Synchronisierungsprozess wird die Verbindung mit dem Katalog über einen DNS-Alias hergestellt. Der Alias wird während der Wiederherstellung und Rückführung so geändert, dass er auf den aktiven Katalog verweist. Der Synchronisierungsprozess hält den Katalog mit jeder Datenbank- oder Poolkonfigurationsänderung, die in der Wiederherstellungsregion vorgenommen wurde, auf dem neuesten Stand. Während der Rückführung werden diese Änderungen auf die entsprechenden Ressourcen in der ursprünglichen Region angewendet.

## <a name="geo-restore-recovery-process-overview"></a>Übersicht über den Wiederherstellungsprozess mit Geowiederherstellung

Bei dem Wiederherstellungsprozess mit Geowiederherstellung erfolgen die Bereitstellung der Anwendung und die Wiederherstellung der Datenbanken aus Sicherungen in die Wiederherstellungsregion.

Im Wiederherstellungsprozess wird wie folgt vorgegangen:

1. Der Azure Traffic Manager-Endpunkt für die Web-App in der ursprünglichen Region wird deaktiviert. Durch Deaktivieren des Endpunkts wird verhindert, dass Benutzer eine Verbindung mit der App in einem ungültigen Zustand herstellen, sollte die ursprüngliche Region während der Wiederherstellung wieder online geschaltet werden.

2. Es wird ein Wiederherstellungskatalogserver in der Wiederherstellungsregion bereitgestellt, die Katalogdatenbank wird über Geowiederherstellung wiederhergestellt, und der „activecatalog“-Alias wird aktualisiert, sodass er auf den wiederhergestellten Katalogserver verweist. Durch das Ändern des Katalogalias wird sichergestellt, dass beim Katalogsynchronisierungsprozess immer der aktive Katalog synchronisiert wird.

3. Alle vorhandenen Mandanten im Wiederherstellungskatalog werden als offline markiert, damit Zugriff auf Mandantendatenbanken verhindert wird, bevor diese wiederhergestellt sind.

4. Es wird eine Instanz der App in der Wiederherstellungsregion bereitgestellt, und die App wird so konfiguriert, dass für sie der wiederhergestellte Katalog in dieser Region verwendet wird. Um die Wartezeit auf ein Minimum zu reduzieren, ist die Beispiel-App so konzipiert, dass sie immer eine Verbindung mit einer Mandantendatenbank in derselben Region herstellt.

5. Es werden ein Server und ein Pool für elastische Datenbanken bereitgestellt, in dem neue Mandanten bereitgestellt werden. Durch Erstellen dieser Ressourcen wird sichergestellt, dass sich die Bereitstellung neuer Mandanten und die Wiederherstellung vorhandener Mandanten nicht wechselseitig stören.

6. Der neue Mandantenalias wird aktualisiert, sodass er auf den Server für neue Mandantendatenbanken in der Wiederherstellungsregion verweist. Durch Ändern dieses Alias wird sichergestellt, dass Datenbanken für jeden neuen Mandanten in der Wiederherstellungsregion bereitgestellt werden.
        
7. Es werden Server und Pools für elastische Datenbanken in der Wiederherstellungsregion bereitgestellt, damit Mandantendatenbanken wiederhergestellt werden können. Diese Server und Pools sind ein Spiegelimage der Konfiguration in der ursprünglichen Region. Durch die Bereitstellung von Pools im Voraus wird die Kapazität reserviert, die zum Wiederherstellen aller Datenbanken erforderlich ist.

    Ein Ausfall in einer Region kann großen Druck auf die in der gekoppelten Region verfügbaren Ressourcen ausüben. Wenn Sie für eine Notfallwiederherstellung mit Geowiederherstellung arbeiten, ist schnelles Reservieren von Ressourcen empfehlenswert. Ziehen Sie die Georeplikation in Betracht, wenn es entscheidend ist, dass eine Anwendung in einer bestimmten Region wiederhergestellt wird. 

8. Der Traffic Manager-Endpunkt für die Web-App in der Wiederherstellungsregion wird aktiviert. Durch das Aktivieren dieses Endpunkts wird es der Anwendung ermöglicht, neue Mandanten bereitzustellen. Zu diesem Zeitpunkt sind die vorhandenen Mandanten immer noch offline.

9. Es werden Batches mit Anforderungen gesendet, um die Datenbanken in der Reihenfolge ihrer Priorität wiederherzustellen. 

    * Die Batches sind so konzipiert, dass die Datenbanken parallel über alle Pools wiederhergestellt werden.  

    * Wiederherstellungsanforderungen werden asynchron gesendet, sodass sie schnell gesendet und zur Ausführung in jedem Pool in die Warteschlange gesetzt werden.

    * Weil Wiederherstellungsanforderungen über alle Pools hinweg parallel verarbeitet werden, empfiehlt es sich, wichtige Mandanten über viele Pools zu verteilen. 

10. Der Dienst wird überwacht, um zu bestimmen, wann Datenbanken wiederhergestellt werden. Nach der Wiederherstellung einer Mandantendatenbank wird sie im Katalog als online gekennzeichnet, und es wird eine „rowversion“-Summe für die Mandantendatenbank aufgezeichnet. 

    * Die Anwendung kann auf die Mandantendatenbanken zugreifen, sobald diese im Katalog als online markiert sind.

    * Eine Summe der „rowversion“-Werte in der Mandantendatenbank wird im Katalog gespeichert. Diese Summe fungiert als Fingerabdruck, über den der Rückführungsprozess bestimmen kann, ob die Datenbank in der Wiederherstellungsregion aktualisiert wurde.

## <a name="run-the-recovery-script"></a>Ausführen des Wiederherstellungsskripts

> [!IMPORTANT]
> In diesem Tutorial werden Datenbanken aus georedundanten Sicherungen wiederhergestellt. Obwohl diese Sicherungen in der Regel innerhalb von 10 Minuten verfügbar sind, kann es bis zu einer Stunde dauern, bis sie verfügbar sind. Das Skript wird angehalten, bis sie verfügbar sind.

Stellen Sie sich vor, dass es in der Region, in der die Anwendung bereitgestellt wird, zu einem Ausfall kommt, und führen Sie das Wiederherstellungsskript aus:

1. Legen Sie in PowerShell ISE im Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1“ den folgenden Wert fest:

    $DemoScenario = 2: Wiederherstellen der App in einer Wiederherstellungsregion, indem sie aus georedundanten Sicherungen wiederhergestellt wird.

2. Wählen Sie F5 aus, um das Skript auszuführen.  

    * Das Skript wird in einem neuen PowerShell-Fenster geöffnet, und in ihm werden einige PowerShell-Aufträge gestartet, die parallel ausgeführt werden. In diesen Aufträgen werden Server, Pools und Datenbanken in der Wiederherstellungsregion wiederhergestellt.

    * Die Wiederherstellungsregion ist die gekoppelte Region, die der Azure-Region zugeordnet ist, in der Sie die Anwendung bereitgestellt haben. Weitere Informationen finden Sie unter [Azure-Regionspaare](../../best-practices-availability-paired-regions.md). 

3. Überwachen Sie den Status des Wiederherstellungsprozesses im PowerShell-Fenster.

    ![Screenshot mit dem PowerShell-Fenster, in dem Sie den Status des Wiederherstellungsvorgangs überwachen können](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Um den Code für die Wiederherstellungsaufträge zu untersuchen, sehen Sie sich die PowerShell-Skripts an, die im Ordner „...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\RecoveryJobs“ enthalten sind.

## <a name="review-the-application-state-during-recovery"></a>Überprüfen des Anwendungszustands während der Wiederherstellung
Solange der Anwendungsendpunkt in Traffic Manager deaktiviert ist, ist die Anwendung nicht verfügbar. Der Katalog wird wiederhergestellt, und alle Mandanten werden als offline gekennzeichnet. Der Anwendungsendpunkt in der Wiederherstellungsregion wird dann aktiviert, und die Anwendung ist wieder online. Obwohl die Anwendung verfügbar ist, werden Mandanten im Veranstaltungshub solange als offline angezeigt, bis ihre Datenbanken wiederhergestellt wurden. Sie müssen Ihre Anwendung so konzipieren, dass sie mit Mandantendatenbanken umgehen kann, die offline sind.

* Aktualisieren Sie in Ihrem Webbrowser den Wingtip Tickets-Event Hub, nachdem die Katalogdatenbank wiederhergestellt wurde, aber bevor die Mandanten wieder online sind.

  * Sie können in der Fußzeile sehen, dass der Name des Katalogservers jetzt das Suffix „-recovery“ aufweist und der Server sich in der Wiederherstellungsregion befindet.

  * Mandanten, die noch nicht wiederhergestellt wurden, sind als offline markiert und können nicht ausgewählt werden.   
 
    ![Wiederherstellungsprozess](./media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)  

  * Wenn Sie die Veranstaltungsseite eines Mandanten direkt öffnen, während der Mandant offline ist, wird auf der Seite eine Benachrichtigung angezeigt, dass der Mandant offline ist. Versuchen Sie beispielsweise, wenn Contoso Concert Hall offline ist, „http://events.wingtip-dpt.&lt;Benutzer&gt;.trafficmanager.net/contosoconcerthall“ zu öffnen.

    ![Screenshot einer Seite mit Offlineereignissen](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Bereitstellen eines neuen Mandanten in der Wiederherstellungsregion
Schon bevor Mandantendatenbanken wiederhergestellt sind, können Sie neue Mandanten in der Wiederherstellungsregion bereitstellen. In der Wiederherstellungsregion bereitgestellte neue Mandantendatenbanken werden später mit den wiederhergestellten Datenbanken zurückgeführt.   

1. Legen Sie in PowerShell ISE im Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1“ die folgende Eigenschaft fest:

    $DemoScenario = 3: Bereitstellen eines neuen Mandanten in der Wiederherstellungsregion.

2. Wählen Sie F5 aus, um das Skript auszuführen.

3. Die Hawthorn Hall-Veranstaltungsseite wird im Browser geöffnet, wenn die Bereitstellung abgeschlossen ist. 

    Beachten Sie, dass sich die Hawthorn Hall-Datenbank in der Wiederherstellungsregion befindet.

    ![Hawthorn Hall, bereitgestellt in der Wiederherstellungsregion](./media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. Aktualisieren Sie im Browser die Seite für den Wingtip Tickets-Event Hub, damit Hawthorn Hall auf der Seite enthalten ist. 

    Wenn Sie Hawthorn Hall bereitgestellt haben, ohne zu warten, bis die anderen Mandanten wiederhergestellt wurden, sind andere Mandanten möglicherweise noch offline.

## <a name="review-the-recovered-state-of-the-application"></a>Überprüfen des wiederhergestellten Status der Anwendung

Nach Abschluss des Wiederherstellungsprozesses sind die Anwendung und alle Mandanten in der Wiederherstellungsregion voll funktionsfähig. 

1. Aktualisieren Sie den Event Hub, wenn im PowerShell-Konsolenfenster angezeigt wird, dass alle Mandanten wiederhergestellt wurden. 

    Die Mandanten werden als online angezeigt, auch der neue Mandant Hawthorn Hall.

    ![Wiederhergestellte und neue Mandanten im Event Hub](./media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klicken Sie auf „Contoso Concert Hall“, und öffnen Sie die zugehörige Veranstaltungsseite. 

    In der Fußzeile sehen Sie, dass sich die Datenbank auf dem Wiederherstellungsserver befindet, der sich in der Wiederherstellungsregion befindet.

    ![Contoso in der Wiederherstellungsregion](./media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Liste der Ressourcengruppen.  

    Beachten Sie die Ressourcengruppe, die Sie bereitgestellt haben, und die Wiederherstellungsressourcengruppe mit dem Suffix „-recovery“. Die Wiederherstellungsressourcengruppe enthält alle Ressourcen, die während des Wiederherstellungsprozesses erstellt wurden, sowie neue Ressourcen, die während des Ausfalls erstellt wurden. 

4. Öffnen Sie die Wiederherstellungsressourcengruppe, und beachten Sie die folgenden Elemente:

   * Die Wiederherstellungsversionen des Katalog- und des tenants1-Servers mit dem Suffix „-recovery“. Die wiederhergestellten Katalog- und Mandantendatenbanken auf diesen Servern haben alle die Namen, die in der ursprünglichen Region verwendet wurden.

   * Der SQL-Server „tenants2-dpt-&lt;Benutzer&gt;-recovery“. Dieser Server wird dazu verwendet, neue Mandanten während des Ausfalls bereitzustellen.

   * Der App Service namens „events-wingtip-dpt-&lt;Wiederherstellungsregion&gt;-&lt;Benutzer&gt;. Dies ist die Wiederherstellungsinstanz der Veranstaltungen-App.

     ![Contoso-Ressourcen in der Wiederherstellungsregion](./media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Öffnen Sie den SQL-Server „tenants2-dpt-&lt;Benutzer&gt;-recovery“. Beachten Sie, dass der Server die Datenbank „hawthornhall“ und den Pool für elastische Datenbanken „Pool1“ enthält. Die Datenbank „hawthornhall“ ist im Pool für elastische Datenbanken „Pool1“ als elastische Datenbank konfiguriert.

## <a name="change-the-tenant-data"></a>Ändern der Mandantendaten 
In dieser Aufgabe aktualisieren Sie eine der wiederhergestellten Mandantendatenbanken. Im Rückführungsprozess werden wiederhergestellte Datenbanken, die geändert wurden, in die ursprüngliche Region kopiert. 

1. Suchen Sie in Ihrem Browser nach der Veranstaltungsliste für die Contoso Concert Hall, scrollen Sie durch die Veranstaltungen, und beachten Sie die letzte Veranstaltung („Seriously Strauss“).

2. Legen Sie in PowerShell ISE im Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1“ den folgenden Wert fest:

    $DemoScenario = 4: Löschen eines Ereignisses von einem Mandanten in der Wiederherstellungsregion.

3. Wählen Sie F5 aus, um das Skript auszuführen.

4. Aktualisieren Sie die Contoso Concert Hall-Veranstaltungsseite (http://events.wingtip-dpt.&lt;Benutzer&gt;.trafficmanager.net/contosoconcerthall). Sie können sehen, dass die Veranstaltung „Seriously Strauss“ fehlt.

An dieser Stelle des Tutorials haben Sie die Anwendung wiederhergestellt, die jetzt in der Wiederherstellungsregion ausgeführt wird. Sie haben einen neuen Mandanten in der Wiederherstellungsregion bereitgestellt und Daten von einem der wiederhergestellten Mandanten geändert.  

> [!NOTE]
> Die anderen Tutorials im Beispiel sind nicht so konzipiert, dass sie mit der App im Wiederherstellungsstatus ausgeführt werden können. Wenn Sie die anderen Tutorials untersuchen möchten, müssen Sie die Anwendung zuerst zurückführen.

## <a name="repatriation-process-overview"></a>Übersicht über den Rückführungsprozess

Im Rückführungsprozess werden die Anwendung und deren Datenbanken in ihrer ursprünglichen Region wiederhergestellt, nachdem ein Ausfall behoben wurde.

![Rückführung über Geowiederherstellung](./media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png)   

Im Prozess wird wie folgt vorgegangen:

1. Alle derzeit laufenden Wiederherstellungsaktivitäten werden gestoppt, und alle ausstehenden oder wirksamen Anforderungen zur Datenbankwiederherstellung werden abgebrochen.

2. In der ursprünglichen Region werden die Mandantendatenbanken reaktiviert, die seit dem Ausfall nicht geändert wurden. Diese Datenbanken umfassen die noch nicht wiederhergestellten sowie die wiederhergestellten, aber nicht nachträglich geänderten Datenbanken. Die reaktivierten Datenbanken haben exakt den Zustand wie beim letzten Zugriff von ihren Mandanten.

3. In der ursprünglichen Region wird für den neuen Mandanten ein Spiegelimage seines Servers und seines Pools für elastische Datenbanken bereitgestellt. Nach Abschluss dieses Vorgangs wird der neue Mandantenalias so aktualisiert, dass er auf diesen Server verweist. Das Aktualisieren des Alias bewirkt, dass das Onboarding neuer Mandanten in der ursprünglichen Region statt in der Wiederherstellungsregion erfolgt.

3. Die Georeplikation wird verwendet, um den Katalog aus der Wiederherstellungsregion in die ursprüngliche Region zu verschieben.

4. Die Poolkonfiguration in der ursprünglichen Region wird aktualisiert, damit sie den Änderungen entspricht, die während des Ausfalls in der Wiederherstellungsregion vorgenommen wurden.

5. Es werden die Server und Pools erstellt, die zum Hosten aller neuen Datenbanken erforderlich sind, die während des Ausfalls erstellt wurden.

6. Die Georeplikation wird verwendet, um Mandantendatenbanken, die nach der Wiederherstellung aktualisiert wurden, und alle neuen Mandantendatenbanken zurückzuführen, die während des Ausfalls bereitgestellt wurden. 

7. Ressourcen, die während des Wiederherstellungsprozesses in der Wiederherstellungsregion erstellt wurden, werden bereinigt.

Um die Anzahl der Mandantendatenbanken zu beschränken, die zurückgeführt werden müssen, werden die Schritte 1 bis 3 sofort ausgeführt.  

Schritt 4 wird nur ausgeführt, wenn der Katalog in der Wiederherstellungsregion während des Ausfalls geändert wurde. Der Katalog wird aktualisiert, wenn in der Wiederherstellungsregion neue Mandanten erstellt wurden oder irgendeine Datenbank- oder Poolkonfiguration geändert wurde.

Es ist entscheidend, dass der Schritt 7 nur minimale Unterbrechung verursacht und keine Daten verloren gehen. Um dieses Ziel zu erreichen, verwendet der Prozess die Georeplikation.

Bevor eine Datenbank georepliziert wird, wird die entsprechende Datenbank in der ursprünglichen Region gelöscht. Die Datenbank in der Wiederherstellungsregion wird dann georepliziert, wodurch ein sekundäres Replikat in der ursprünglichen Region erstellt wird. Nach Abschluss der Replikation wird der Mandant im Katalog als offline gekennzeichnet, wodurch alle Verbindungen mit der Datenbank in der Wiederherstellungsregion getrennt werden. Für die Datenbank wird dann ein Failover ausgeführt, sodass alle ausstehenden Transaktionen in der sekundären Datenbank verarbeitet werden, damit keine Daten verloren gehen. 

Bei dem Failover werden die Datenbankrollen vertauscht. Die sekundäre Datenbank in der ursprünglichen Region wird die primäre Datenbank mit Lese-/Schreibzugriff, und die Datenbank in der Wiederherstellungsregion wird eine schreibgeschützte sekundäre Datenbank. Der Mandanteneintrag im Katalog wird so aktualisiert, dass er auf die Datenbank in der ursprünglichen Region verweist, und der Mandant wird als online gekennzeichnet. An diesem Punkt ist die Rückführung der Datenbank abgeschlossen. 

Anwendungen sollten mit Wiederholungslogik geschrieben werden, um sicherzustellen, dass sie Verbindungen automatisch erneut herstellen, wenn diese unterbrochen wurden. Wenn die Verbindungswiederherstellung mithilfe des Katalogs erfolgt, wird die jeweilige Verbindung mit der zurückgeführten Datenbank in der ursprünglichen Region hergestellt. Obwohl die kurze Verbindungstrennung häufig nicht bemerkt wird, ist es vorteilhaft, Datenbanken außerhalb der Geschäftszeiten zurückzuführen.

Nachdem eine Datenbank zurückgeführt wurde, kann die sekundäre Datenbank in der Wiederherstellungsregion gelöscht werden. Für die Datenbank in der ursprünglichen Region wird dann wieder die Geowiederherstellung als Notfallwiederherstellungsschutz verwendet.

In Schritt 8 werden Ressourcen in der Wiederherstellungsregion, einschließlich der Wiederherstellungsserver und -pools, gelöscht.

## <a name="run-the-repatriation-script"></a>Ausführen des Rückführungsskripts
Nehmen Sie an, dass der Ausfall behoben ist, und führen Sie das Rückführungsskript aus.

Wenn Sie dem Tutorial gefolgt sind, werden Fabrikam Jazz Club und Dogwood Dojo vom Skript sofort in der ursprünglichen Region reaktiviert, weil sie nicht geändert wurden. Danach werden der neue Mandant, Hawthorn Hall, und der Mandant Contoso Concert Hall, da er geändert wurde, zurückgeführt. Im Skript wird auch der Katalog zurückgeführt, der aktualisiert wurde, als Hawthorn Hall bereitgestellt wurde.
  
1. Überprüfen Sie in PowerShell ISE im Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1“, ob der Katalogsynchronisierungsprozess weiterhin in seiner PowerShell-Instanz ausgeführt wird. Starten Sie ihn bei Bedarf neu, indem Sie Folgendes festlegen:

    $DemoScenario = 1: Starten der Synchronisierung der Mandantenserver-, Pool- und Datenbankkonfigurationsinformationen in den Katalog.

    Wählen Sie F5 aus, um das Skript auszuführen.

2.  Legen Sie dann Folgendes fest, um den Rückführungsprozess zu starten:

    $DemoScenario = 5: Rückführen der App in ihre ursprüngliche Region.

    Wählen Sie F5 aus, um das Wiederherstellungsskript in einem neuen PowerShell-Fenster auszuführen. Die Rückführung dauert einige Minuten und kann im PowerShell-Fenster überwacht werden.

3. Aktualisieren Sie während der Skriptausführung die Seite für den Event Hub (http://events.wingtip-dpt.&lt;Benutzer&gt;.trafficmanager.net).

    Sie können sehen, dass alle Mandanten im gesamten Verlauf dieses Prozesses online und für Zugriffe verfügbar sind.

4. Wählen Sie den Mandanten „Fabrikam Jazz Club“ aus, um ihn zu öffnen. Wenn Sie diesen Mandanten nicht geändert haben, sehen Sie in der Fußzeile, dass der Server bereits als ursprünglicher Server wiederhergestellt wurde.

5. Öffnen oder aktualisieren Sie die Contoso Concert Hall-Veranstaltungsseite. Beachten Sie in der Fußzeile, dass sich die Datenbank zunächst weiterhin auf dem „-recovery“-Server befindet. 

6. Aktualisieren Sie die Contoso Concert Hall-Veranstaltungsseite nach Abschluss des Rückführungsprozesses, und beachten Sie, dass sich die Datenbank jetzt in Ihrer ursprünglichen Region befindet.

7. Aktualisieren Sie den Event Hub erneut, und öffnen Sie Hawthorn Hall. Beachten Sie, dass sich die zugehörige Datenbank ebenfalls in der ursprünglichen Region befindet. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Bereinigen der Ressourcen in der Wiederherstellungsregion nach der Rückführung
Nach Abschluss der Rückführung können die Ressourcen in der Wiederherstellungsregion problemlos gelöscht werden. 

> [!IMPORTANT]
> Sie sollten diese Ressourcen umgehend löschen, damit sie nicht weiter abgerechnet werden.

Im Wiederherstellungsprozess werden alle Wiederherstellungsressourcen in einer Wiederherstellungsressourcengruppe erstellt. Im Rahmen des Bereinigungsprozesses wird diese Ressourcengruppe gelöscht, und alle Verweise auf die Ressourcen aus dem Katalog werden entfernt. 

1. Legen Sie in PowerShell ISE im Skript „...\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1“ Folgendes fest:
    
    $DemoScenario = 6: Löschen veralteter Ressourcen aus der Wiederherstellungsregion.

2. Wählen Sie F5 aus, um das Skript auszuführen.

Nach dem Bereinigen der Ressourcen hat die Anwendung wieder den Zustand, den sie zu Beginn hatte. An diesem Punkt können Sie das Skript erneut ausführen oder andere Tutorials durcharbeiten.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Entwerfen der Anwendung zur Sicherstellung, dass sich die App und die Datenbank in derselben Region befinden 
Die Anwendung ist so konzipiert, dass Verbindungen immer von einer Instanz in der Region hergestellt werden, in der sich auch die Mandantendatenbank befindet. Dieser Entwurf verringert die Wartezeit zwischen der Anwendung und der Datenbank. Für diese Optimierung wird davon ausgegangen, dass Interaktionen zwischen der App und der Datenbank wesentlich häufiger sind als Interaktionen zwischen Benutzern und der App.  

Mandantendatenbanken können während einer Rückführung für einige Zeit über die Wiederherstellungs- und die ursprüngliche Region verteilt sein. Für jede Datenbank sucht die App über eine DNS-Suche zu dem Namen des Mandantenservers nach der Region, in der sich die Datenbank befindet. Der Servername ist ein Alias. Der als Alias vorliegende Servername enthält der Namen der Region. Wenn sich die Anwendung nicht in derselben Region wie die Datenbank befindet, erfolgt eine Umleitung zu der Instanz, die sich in derselben Region wie der Server befindet. Das Umleiten zu der Instanz in derselben Region wie die Datenbank minimiert die Wartezeit zwischen der App und der Datenbank.  

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:
> [!div class="checklist"]
> 
> * Verwenden des Mandantenkatalogs, um in regelmäßigen Abständen aktualisierte Konfigurationsinformationen zu speichern, sodass eine Spiegelimage-Wiederherstellungsumgebung in einer anderen Region erstellt werden kann
> * Wiederherstellen von Datenbanken in der Wiederherstellungsregion mithilfe der Geowiederherstellung
> * Aktualisieren des Mandantenkatalogs entsprechend den Speicherorten der wiederhergestellten Mandantendatenbanken 
> * Verwenden eines DNS-Alias, um einer Anwendung eine durchgängige Verbindung mit dem Mandantenkatalog zu ermöglichen, ohne dass eine Neukonfiguration erforderlich ist
> * Verwenden der Georeplikation, um wiederhergestellte Datenbanken in ihre ursprüngliche Region zurückzuführen, nachdem ein Ausfall behoben wurde

Führen Sie jetzt das Tutorial [Notfallwiederherstellung für eine mehrinstanzenfähige SaaS-Anwendung über Datenbankgeoreplikation](./saas-dbpertenant-dr-geo-replication.md) aus, um zu erfahren, wie sich durch Verwenden der Georeplikation die Zeit deutlich verkürzen lässt, die zum Wiederherstellen einer umfangreichen mehrinstanzenfähigen Anwendung erforderlich ist.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[Zusätzliche Tutorials, die auf der Wingtip-SaaS-Anwendung aufbauen](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)