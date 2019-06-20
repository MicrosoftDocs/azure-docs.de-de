---
title: Entwerfen von global verfügbaren Diensten mit Azure SQL-Datenbank | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Anwendungsentwurf für hoch verfügbare Dienste mithilfe von Azure SQL-Datenbank erstellen.
keywords: cloudbasierte Notfallwiederherstellung,Notfallwiederherstellungslösungen,App-Datensicherung,Georeplikation,Planen der Geschäftskontinuität
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 46232afcaf9504d4cfbd80160e2d7e7ea958d600
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61488185"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Entwerfen von global verfügbaren Diensten mit Azure SQL-Datenbank

Beim Erstellen und Bereitstellen von Clouddiensten mit Azure SQL-Datenbank verwenden Sie [aktive Georeplikation](sql-database-active-geo-replication.md) oder [Autofailover-Gruppen](sql-database-auto-failover-group.md), um bei regionalen Ausfällen und schwerwiegenden Fehlern für Resilienz zu sorgen. Mit demselben Feature können Sie global verteilte Anwendungen erstellen, die für den lokalen Zugriff auf die Daten optimiert sind. In diesem Artikel werden gängige Anwendungsmuster beschrieben, einschließlich der Vor- und Nachteile der einzelnen Optionen.

> [!NOTE]
> Wenn Sie Premium-Datenbanken, unternehmenskritische Datenbanken oder Pools für elastische Datenbanken verwenden, können Sie sie resistent gegenüber regionalen Ausfällen machen, indem Sie sie auf die Konfiguration der zonenredundanten Bereitstellung umstellen. Informationen finden Sie unter [Hochverfügbarkeit und Azure SQL-Datenbank](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Szenario 1: Verwenden von zwei Azure-Regionen für die Geschäftskontinuität mit minimalen Ausfallzeiten

In diesem Szenario weist die Anwendung die folgenden Merkmale auf:

* Die Anwendung ist in einer Azure-Region aktiv.
* Alle Datenbanksitzungen erfordern Lese- und Schreibzugriff (RW) auf Daten.
* Die Webebene und die Datenebene müssen verbunden werden, um Latenzzeiten und Datenverkehrskosten zu reduzieren.
* Im Wesentlichen stellen Ausfallzeiten für diese Anwendungen ein höheres Unternehmensrisiko dar als Datenverluste.

In diesem Fall wird die Topologie für die Anwendungsbereitstellung für den Umgang mit regionalen Notfällen optimiert, bei denen das Failover für alle Anwendungskomponenten gleichzeitig ausgeführt werden muss. Diese Topologie ist im folgenden Diagramm dargestellt. Um geografische Redundanz zu erzielen, werden die Ressourcen der Anwendung in Region A und Region B bereitgestellt. Die Ressourcen in Region B werden jedoch erst genutzt, wenn in Region A Fehler auftreten. Zwischen den beiden Regionen wird eine Failovergruppe konfiguriert, um die Datenbankkonnektivität, die Replikation und das Failover zu verwalten. Der Webdienst in beiden Regionen wird so konfiguriert, dass der Zugriff auf die Datenbank über den Lese-/Schreiblistener **&lt;Name der Failovergruppe&gt;.database.windows.net** erfolgt (1). Traffic Manager ist für die [prioritätsbasierte Routingmethode](../traffic-manager/traffic-manager-configure-priority-routing-method.md) eingerichtet (2).  

> [!NOTE]
> [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) wird in diesem Artikel ausschließlich zu Demonstrationszwecken genutzt. Sie können jede Lastenausgleichslösung einsetzen, die die prioritätsbasierte Routingmethode unterstützt.

Das folgende Diagramm zeigt diese Konfiguration vor einem Ausfall:

![Szenario 1: Konfiguration vor dem Ausfall](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Nach einem Ausfall in der primären Region erkennt der SQL-Datenbank-Dienst, dass auf die primäre Datenbank nicht zugegriffen werden kann, und löst basierend auf den Parametern der Richtlinie für automatisches Failover ein Failover auf die sekundäre Region aus (1). Abhängig von Ihrer Anwendungs-SLA können Sie eine Toleranzperiode konfigurieren, die die Zeit zwischen der Erkennung des Ausfalls und dem Failover selbst steuert. Es ist möglich, dass Traffic Manager das Endpunktfailover initiiert, bevor die Failovergruppe das Failover der Datenbank auslöst. In diesem Fall kann die Webanwendung die Verbindung mit der Datenbank nicht sofort wiederherstellen. Die Verbindungswiederherstellungen sind jedoch automatisch erfolgreich, sobald das Datenbankfailover abgeschlossen ist. Wenn die fehlerhafte Region wiederhergestellt und wieder online ist, stellt die alte primäre Datenbank automatisch wieder eine Verbindung als neue sekundäre Datenbank her. Das folgende Diagramm veranschaulicht die Konfiguration nach dem Failover.

> [!NOTE]
> Alle committeten Transaktionen nach dem Failover gehen bei der Verbindungswiederherstellung verloren. Nach Abschluss des Failovers kann die Anwendung in Region B die Verbindung wiederherstellen und die Verarbeitung der Benutzeranforderungen erneut starten. Sowohl die Webanwendung als auch die primäre Datenbank befinden sich nun in Region B und bleiben verbunden.

![Szenario 1: Konfiguration nach Failover](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Bei einem Ausfall in Region B wird der Replikationsprozess zwischen der primären und der sekundären Datenbank unterbrochen, die Verbindung zwischen beiden bleibt jedoch erhalten (1). Traffic Manager erkennt, dass die Verbindung mit Region B unterbrochen ist und markiert den Endpunkt „web-app-2“ als beeinträchtigt (2). Dies hat keine Auswirkung auf die Leistung der Anwendung, die Datenbank ist jedoch ungeschützt, und es besteht ein höheres Risiko eines Datenverlusts für den Fall, dass Region A auch ausfällt.

> [!NOTE]
> Für die Notfallwiederherstellung empfehlen wir eine Konfiguration, bei der die Anwendungsbereitstellung auf zwei Regionen beschränkt ist. Der Grund ist, dass die meisten Azure-Gebiete nur zwei Regionen aufweisen. Diese Konfiguration bietet keinen Schutz Ihrer Anwendung vor einem gleichzeitigen schwerwiegenden Ausfall beider Regionen. Im unwahrscheinlichen Fall eines solchen Ausfalls können Sie Ihre Datenbanken mithilfe eines [Geowiederherstellungsvorgangs](sql-database-disaster-recovery.md#recover-using-geo-restore) in einer dritten Region wiederherstellen.
>

 Nach Behebung der Ausfallursache wird die sekundäre Datenbank automatisch mit der primären synchronisiert. Während der Synchronisierung kann die Leistung der primären Datenbank beeinträchtigt werden. Die spezifischen Auswirkungen hängen von der in der neuen primären Datenbank erfassten Datenmenge seit dem Failover ab. Das folgende Diagramm zeigt einen Ausfall in der sekundären Region:

![Szenario 1: Konfiguration nach einem Ausfall in der sekundären Region](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Die **Hauptvorteile** dieses Entwurfsmusters sind:

* Die gleiche Webanwendung wird in beiden Regionen ohne regionsspezifische Konfiguration bereitgestellt und erfordert keine zusätzliche Logik zum Verwalten des Failovers.
* Die Leistung der Anwendung ist vom Failover nicht betroffen, da die Webanwendung und die Datenbank stets verbunden sind.

Der **Hauptnachteil** besteht darin, dass die Anwendungsressourcen in Region B die meiste Zeit zu gering ausgelastet sind.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Szenario 2: Azure-Regionen für die Geschäftskontinuität mit maximaler Beibehaltung von Daten

Diese Option eignet sich am besten für Anwendungen mit den folgenden Merkmalen:

* Jeder Datenverlust stellt ein hohes Geschäftsrisiko dar. Das Datenbankfailover darf nur als letzte Möglichkeit in Frage kommen, falls der Ausfall durch einen schwerwiegenden Fehler verursacht wird.
* Die Anwendung unterstützt schreibgeschützte Betriebsmodi und Betriebsmodi mit Lese-/Schreibzugriff und kann für gewisse Zeit im „schreibgeschützten Modus“ betrieben werden.

In diesem Muster wechselt die Anwendung in den schreibgeschützten Modus, sobald bei Lese-/Schreibverbindungen Timeoutfehler auftreten. Die Webanwendung wird in beiden Regionen bereitgestellt und enthält eine Verbindung mit dem Lese-/Schreib-Listenerendpunkt und eine andere Verbindung mit dem schreibgeschützten Listenerendpunkt (1). Das Traffic Manager-Profil sollte die [prioritätsbasierte Routingmethode](../traffic-manager/traffic-manager-configure-priority-routing-method.md) verwenden. Die [Endpunktüberwachung](../traffic-manager/traffic-manager-monitoring.md) sollte für den Anwendungsendpunkt in jeder Region aktiviert sein (2).

Das folgende Diagramm veranschaulicht diese Konfiguration vor einem Ausfall:

![Szenario 2: Konfiguration vor dem Ausfall](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Wenn Traffic Manager einen Fehler in der Konnektivität mit Region A erkennt, leitet er den Benutzerdatenverkehr automatisch zur Anwendungsinstanz in Region B um. Bei diesem Muster ist wichtig, dass Sie die Toleranzperiode mit Datenverlust auf einen ausreichend hohen Wert festlegen, z.B. 24 Stunden. Dies garantiert, dass Datenverluste verhindert werden, wenn der Ausfall innerhalb dieser Zeit behoben wird. Wenn die Webanwendung in Region B aktiviert wird, treten bei den Lese-/ Schreibvorgängen Fehler auf. An diesem Punkt sollte sie in den schreibgeschützten Modus wechseln (1). In diesem Modus werden die Anforderungen automatisch an die sekundäre Datenbank weitergeleitet. Wenn der Ausfall durch einen schwerwiegenden Fehler verursacht wird, kann er sehr wahrscheinlich nicht innerhalb der Toleranzperiode behoben werden. Nach Ablauf der Toleranzperiode löst die Failovergruppe das Failover aus. Danach ist der Lese-/Schreiblistener verfügbar, und die Verbindungen mit ihm sind erfolgreich (2). Das folgende Diagramm veranschaulicht die zwei Phasen des Wiederherstellungsprozesses.

> [!NOTE]
> Wenn die Ausfallursache in der primären Region innerhalb der Toleranzperiode beseitigt wird, erkennt Traffic Manager die Wiederherstellung der Konnektivität in der primären Region und leitet den Datenverkehr der Benutzer wieder zur Anwendungsinstanz in Region A. Diese Anwendungsinstanz nimmt den Betrieb im Lese-/Schreibmodus unter Verwendung der primären Datenbank in Region A wie im vorherigen Diagramm veranschaulicht wieder auf.

![Szenario 2: Phasen der Notfallwiederherstellung](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Bei einem Ausfall in Region B erkennt Traffic Manager den Ausfall des Endpunkts „web-app-2“ in Region B und markiert ihn als beeinträchtigt (1). In der Zwischenzeit leitet die Failovergruppe den schreibgeschützten Listener nach Region A um (2). Der Endbenutzer merkt nichts von diesem Ausfall, aber die primäre Datenbank wird während des Ausfalls verfügbar gemacht. Das folgende Diagramm zeigt einen Fehler in der sekundären Region:

![Szenario 2: Ausfall der sekundären Region](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Nach Behebung des Ausfalls wird die sekundäre Datenbank sofort mit der primären synchronisiert, und der schreibgeschützte Listener wird wieder zur sekundären Datenbank in Region B umgeleitet. Während der Synchronisierung könnte die Leistung der primären Datenbank abhängig vom Umfang der zu synchronisierenden Daten leicht beeinträchtigt werden.

Dieses Entwurfsmuster bietet mehrere **Vorteile**:

* Datenverluste während temporärer Ausfälle werden verhindert.
* Ausfallzeiten hängen nur davon ab, wie schnell Traffic Manager den Verbindungsausfall erkennt, was konfigurierbar ist.

Der **Nachteil** besteht darin, dass die Anwendung im schreibgeschützten Modus betrieben werden können muss.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Szenario 3: Verlegung der Anwendung in ein anderes geografisches Gebiet ohne Datenverlust und nahezu keine Ausfallzeiten

In diesem Szenario weist die Anwendung die folgenden Merkmale auf:

* Die Endbenutzer greifen aus verschiedenen geografischen Gebieten auf die Anwendung zu.
* Die Anwendung umfasst schreibgeschützte Workloads, die nicht von der vollständigen Synchronisierung mit den neuesten Updates abhängig sind.
* Der Schreibzugriff auf Daten sollte im gleichen geografischen Gebiet für die Mehrheit der Benutzer unterstützt werden.
* Die Leselatenz ist für die Endbenutzer entscheidend.

Um diese Anforderungen zu erfüllen, müssen Sie sicherstellen, dass das Benutzergerät **immer** eine Verbindung mit der Anwendung herstellt, die im selben Gebiet für die schreibgeschützten Vorgänge (z.B. Durchsuchen von Daten, Analysen usw.) bereitgestellt wird. Die OLTP-Vorgänge werden hingegen **meistens** im selben Gebiet verarbeitet. Tagsüber werden OLTP-Vorgänge beispielsweise im selben geografischen Gebiet verarbeitet, außerhalb der Arbeitszeiten können sie jedoch in einem anderen Gebiet verarbeitet werden. Wenn die Aktivitäten der Endbenutzer hauptsächlich während der Arbeitszeiten erfolgen, können Sie die optimale Leistung in den meisten Fällen für die Mehrheit der Benutzer garantieren. Im folgenden Diagramm wird diese Topologie veranschaulicht.

Die Ressourcen der Anwendung sollten in jedem Gebiet mit erheblichem Nutzungsbedarf bereitgestellt werden. Wenn die Anwendung beispielsweise in den USA, der Europäischen Union und in Südostasien aktiv genutzt wird, sollte sie in allen diesen Gebieten bereitgestellt werden. Die primäre Datenbank sollte am Ende der Arbeitszeiten dynamisch zwischen den Gebieten umgeleitet werden. Diese Methode wird als „Follow the sun“-Methode bezeichnet. Die OLTP-Workload stellt immer über den Lese-/Schreiblistener **&lt;Name der Failovergruppe&gt;.database.windows.net** eine Verbindung mit der Datenbank her (1). Die schreibgeschützte Workload stellt direkt über den Serverendpunkt der Datenbank **&lt;Servername&gt;.database.windows.net** eine Verbindung mit der lokalen Datenbank her (2). Traffic Manager ist mit der [leistungsorientierten Routingmethode](../traffic-manager/traffic-manager-configure-performance-routing-method.md) konfiguriert. Dadurch wird sichergestellt, dass das Gerät des Endbenutzers mit dem Webdienst in der nächstgelegenen Region verbunden ist. Traffic Manager sollte mit aktivierter Endpunktüberwachung für jeden Webdienstendpunkt eingerichtet werden (3).

> [!NOTE]
> Mit der Konfiguration der Failovergruppe wird definiert, welche Region für das Failover verwendet wird. Da sich die neue primäre Datenbank in einem anderen Gebiet befindet, führt das Failover zu längeren Latenzzeiten für OLTP-Workloads und schreibgeschützte Workloads, bis die beeinträchtigte Region wieder online ist.

![Szenario 3: Konfiguration mit der primären Datenbank in „USA, Osten“](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Am Tagesende (z.B. um 23:00 Uhr Ortszeit) sollten die aktiven Datenbanken zur nächsten Region („Europa, Norden“) umgeleitet werden. Diese Aufgabe kann mithilfe des [Azure Scheduler-Diensts](../scheduler/scheduler-intro.md) vollständig automatisiert werden.  Diese Aufgabe umfasst die folgenden Schritte:

* Umleiten des primären Servers in der Failovergruppe nach „Europa, Norden“ mit freundlichem Failover (1)
* Entfernen der Failovergruppe zwischen „USA, Osten“ und „Europa, Norden“
* Erstellen einer neuen Failovergruppe mit dem gleichen Namen, jedoch zwischen „Europa, Norden“ und „Asien, Osten“ (2)
* Hinzufügen des primären Servers in „Europa, Norden“ und des sekundären Servers in „Asien, Osten“ zu dieser Failovergruppe (3)

Das folgende Diagramm veranschaulicht die neue Konfiguration nach dem geplanten Failover:

![Szenario 3: Umleiten des primären Servers nach „Europa, Norden“](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Bei einem Ausfall in „Europa, Norden“ wird das automatische Datenbankfailover beispielsweise von der Failovergruppe initiiert. Dies führt dazu, dass die Anwendung vorzeitig in die nächste Region verschoben wird (1).  In diesem Fall ist „USA, Osten“ die einzige verbleibende sekundäre Region, bis „Europa, Norden“ wieder online ist. Die verbleibenden zwei Regionen sind durch das Wechseln ihrer Rollen für die Kunden in allen drei geografischen Gebieten verfügbar. Azure Scheduler muss entsprechend angepasst werden. Da die verbleibenden Regionen zusätzlichen Benutzerdatenverkehr aus Europa erhalten, wird die Leistung der Anwendung nicht nur durch zusätzliche Latenz, sondern auch durch eine erhöhte Anzahl von Endbenutzerverbindungen beeinträchtigt. Nachdem der Ausfall in „Europa, Norden“ behoben wurde, wird die sekundäre Datenbank dort sofort mit der aktuellen primären Datenbank synchronisiert. Das folgende Diagramm veranschaulicht einen Ausfall in „Europa, Norden“:

![Szenario 3: Ausfall in „Europa, Norden“](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Sie können die Zeit verringern, während der sich die Benutzerfreundlichkeit der Anwendung in Europa durch die langen Latenzzeiten für Endbenutzer verschlechtert. Dazu sollten Sie proaktiv eine Kopie der Anwendung bereitstellen und die sekundären Datenbanken in einer anderen lokalen Region („Europa, Westen“) als Ersatz für die offline geschaltete Anwendungsinstanz in „Europa, Norden“ erstellen. Wenn Letztere wieder online ist, können Sie entscheiden, ob weiterhin „Europa, Westen“ verwendet wird oder ob die Kopie der Anwendung dort entfernt und wieder „Europa, Norden“ verwendet wird.

Die **Hauptvorteile** dieses Entwurfsmusters sind:

* Die schreibgeschützte Anwendungsworkload greift immer auf Daten in der nächstgelegenen Region zu.
* Die Anwendungsworkload mit Lese-/Schreibzugriff greift während des Zeitraums der höchsten Aktivität in jedem Gebiet auf Daten in der nächstgelegenen Region zu.
* Da die Anwendung in mehreren Regionen bereitgestellt wird, kann ein Ausfall in einer der Regionen ohne erhebliche Ausfallzeiten überbrückt werden.

Es gibt jedoch auch einige **Nachteile**:

* Ein regionaler Ausfall führt durch längere Wartezeiten zu Beeinträchtigungen im jeweiligen geografischen Gebiet. Die Lese-/Schreibworkloads sowie die schreibgeschützten Workloads werden von der Anwendung in einem anderen Gebiet bereitgestellt.
* Die schreibgeschützten Workloads müssen in jeder Region eine Verbindung mit einem anderen Endpunkt herstellen.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planen der Geschäftskontinuität: Auswählen eines Anwendungsentwurfs für die cloudbasierte Notfallwiederherstellung

Für Ihre spezifische Strategie einer cloudbasierten Notfallwiederherstellung können diese Muster kombiniert oder erweitert werden, um die Anforderungen Ihrer Anwendung bestmöglich zu erfüllen.  Wie bereits erwähnt, basiert die von Ihnen gewählte Strategie auf der SLA, die Sie Ihren Kunden anbieten möchten, und auf der Topologie der Anwendungsbereitstellung. Um Ihnen die Entscheidung zu erleichtern, werden in der folgenden Tabelle die Optionen basierend auf der RPO (Recovery Point Objective) und der geschätzten Wiederherstellungszeit verglichen.

| Muster | RPO | Geschätzte Wiederherstellungszeit |
|:--- |:--- |:--- |
| Aktiv-/Passiv-Bereitstellung für Notfallwiederherstellung mit zusammengestelltem Datenbankzugriff |Lese-/Schreibzugriff < 5 Sek. |Zeitpunkt der Fehlererkennung + DNS-TTL |
| Aktiv-/Aktiv-Bereitstellung für den Anwendungslastenausgleich |Lese-/Schreibzugriff < 5 Sek. |Zeitpunkt der Fehlererkennung + DNS-TTL |
| Aktiv-/Passiv-Bereitstellung für die Beibehaltung von Daten |Schreibgeschützter Zugriff < 5 Sek. | Schreibgeschützter Zugriff = 0 |
||Lese-/Schreibzugriff = 0 | Lese-/Schreibzugriff = Zeitpunkt der Fehlererkennung + Toleranzperiode mit Verlust von Daten |
|||

## <a name="next-steps"></a>Nächste Schritte

* Eine Übersicht und verschiedene Szenarien zum Thema Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
* Informationen zur aktiven Georeplikation finden Sie unter [Aktive Georeplikation](sql-database-active-geo-replication.md).
* Informationen zu Autofailover-Gruppen finden Sie unter [Autofailover-Gruppen](sql-database-auto-failover-group.md).
* Informationen zur Verwendung der aktiven Georeplikation mit Pools für elastische Datenbanken finden Sie unter [Strategien zur Notfallwiederherstellung mit Pools für elastische Datenbanken](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
