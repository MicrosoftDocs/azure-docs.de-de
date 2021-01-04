---
title: Netzwerkleistungsmonitor-Lösung in Azure | Microsoft-Dokumentation
description: Mit dem Netzwerkleistungsmonitor in Azure können Sie die Leistung Ihrer Netzwerke nahezu in Echtzeit überwachen, um Leistungsengpässe im Netzwerk zu erkennen und zu lokalisieren.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 1bb2b7ca22896ed279c8aac215109b7f7bc7854c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394270"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Netzwerkleistungsmonitor-Lösung in Azure

![Symbol des Netzwerkleistungsmonitors](./media/network-performance-monitor/npm-symbol.png)


Der Netzwerkleistungsmonitor ist eine cloudbasierte hybride Netzwerküberwachungslösung, mit der Sie die Netzwerkleistung zwischen verschiedenen Punkten in Ihrer Netzwerkinfrastruktur überwachen können. Er hilft Ihnen auch, die Netzwerkkonnektivität mit Dienst- und Anwendungsendpunkten zu überwachen und die Leistung von Azure ExpressRoute zu überwachen. 

Der Netzwerkleistungsmonitor erkennt Netzwerkprobleme wie ins Nichts führenden Datenverkehr (Blackholing), Routingfehler und Probleme, die mit herkömmlichen Netzwerküberwachungsmethoden nicht erkannt werden können. Die Lösung generiert Warnungen und benachrichtigt Sie, sobald ein Schwellenwert für eine Netzwerkverbindung überschritten wird. Sie gewährleistet außerdem das rechtzeitige Erkennen von Leistungsproblemen im Netzwerk und ordnet die Ursache des Problems einem bestimmten Netzwerksegment oder Gerät zu. 

Der Netzwerkleistungsmonitor bietet drei allgemeine Funktionen: 

* [Systemmonitor](network-performance-monitor-performance-monitor.md): Sie können die Netzwerkkonnektivität für Cloudbereitstellungen und lokale Standorte, für mehrere Rechenzentren und Zweigstellen sowie für unternehmenswichtige Microservices/Anwendungen mit mehreren Ebenen überwachen. Mithilfe des Systemmonitors können Sie Netzwerkprobleme erkennen, bevor sie Ihre Benutzer beinträchtigen.

* [Dienstkonnektivitätsmonitor](network-performance-monitor-service-connectivity.md): Sie können die Benutzerkonnektivität mit Diensten überwachen, die für Sie interessant sind, die Infrastruktur bestimmen, durch die der Pfad führt, und Netzwerkengpässe lokalisieren. Sie wissen noch vor Ihren Benutzern über Ausfälle Bescheid und können genau sehen, wo in Ihrem Netzwerkpfad Probleme vorliegen. 

    Mit dieser Funktion können Sie Tests auf Basis von HTTP, HTTPS, TCP und ICMP ausführen, um die Verfügbarkeit und Antwortzeit Ihres Diensts in nahezu Echtzeit oder historisch zu überwachen. Sie können auch den Beitrag des Netzwerks bei Paketverlusten und Latenz überwachen. Mit einer Netzwerktopologiekarte können Sie Geschwindigkeitsverluste des Netzwerks eingrenzen. Sie können Problemstellen, die entlang des Netzwerkpfads vom Knoten zum Dienst auftreten, mit Latenzdaten für jeden Hop identifizieren. Mithilfe integrierter Tests können Sie ganz ohne Vorkonfiguration die Konnektivität mit Microsoft 365 und Dynamics CRM überwachen. Mit dieser Funktion können Sie die Netzwerkkonnektivität mit einem beliebigen TCP-fähigen Endpunkt überwachen (z.B. Websites, SaaS-Anwendungen, PaaS-Anwendungen und SQL-Datenbanken).

* [ExpressRoute-Monitor](network-performance-monitor-expressroute.md): Überwachen Sie die End-to-End-Konnektivität und -Leistung zwischen Ihren Zweigstellen und Azure über Azure ExpressRoute.  

Weitere Informationen zu den verschiedenen vom [Netzwerkleistungsmonitor](../../networking/network-monitoring-overview.md) unterstützten Funktionen sind online verfügbar.
 
## <a name="supported-regions"></a>Unterstützte Regionen
NPM kann die Konnektivität zwischen Netzwerken und Anwendungen in jedem Teil der Welt von einem Arbeitsbereich aus überwachen, der in einer der folgenden Regionen gehostet wird:
* Nordeuropa
* Europa, Westen
* Schweiz, Norden
* Frankreich, Mitte
* Südafrika, Norden
* Kanada, Mitte
* USA (Westen)
* USA, Westen-Mitte
* USA Nord Mitte
* USA Süd Mitte
* USA, Mitte
* East US
* USA (Ost) 2
* USA, Westen 2
* Japan, Osten
* Südostasien
* Australien, Südosten
* Australien, Mitte
* Australien (Osten)
* Vereinigtes Königreich, Süden
* Asien, Osten
* Korea, Mitte
* Indien, Mitte
* US-Regierung Virginia
* US Gov Arizona
* China, Osten 2


Die Liste der unterstützten Regionen für ExpressRoute-Monitor ist in der [Dokumentation](../../expressroute/how-to-npm.md?utm_swu=8117) verfügbar.


## <a name="set-up-and-configure"></a>Einrichten und Konfigurieren

> [!NOTE]
> Der Log Analytics-Agent wird ggf. auch als Microsoft Monitoring Agent (MMA) oder als OMS-Linux-Agent bezeichnet.

### <a name="install-and-configure-agents"></a>Installieren und Konfigurieren von Agents 

Verwenden Sie zum Installieren von Agents die grundlegenden Prozesse, die unter [Verbinden von Windows-Computern mit Azure Monitor](../platform/agent-windows.md), [Verbinden von Linux-Computern mit Azure Monitor](../../virtual-machines/extensions/oms-linux.md) und [Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor](../platform/om-agents.md) beschrieben werden.

### <a name="where-to-install-the-agents"></a>Bestimmen des Installationsorts für die Agents 

* **Systemmonitor:** Installieren Sie Log Analytics-Agents auf mindestens einem Knoten, der mit den einzelnen Subnetzwerken verbunden ist, über die Sie die Netzwerkkonnektivität mit anderen Subnetzwerken überwachen möchten.

    Zur Überwachung einer Netzwerkverbindung installieren Sie an beiden Endpunkten der Verbindung Agents. Sollten Sie sich hinsichtlich der Topologie Ihres Netzwerks nicht sicher sein, installieren Sie die Agents auf Servern mit kritischen Workloads, zwischen denen Sie die Netzwerkleistung überwachen möchten. Wenn Sie also beispielsweise die Netzwerkverbindung zwischen einem Webserver und einem Server mit SQL überwachen möchten, installieren Sie auf beiden Servern einen Agent. Agents überwachen nicht die eigentlichen Hosts, sondern die Netzwerkkonnektivität (Verbindungen) zwischen Hosts. 

* **Dienstkonnektivitätsmonitor** : Installieren Sie einen Log Analytics-Agent auf jedem Knoten, von dem aus Sie die Netzwerkkonnektivität mit dem Dienstendpunkt überwachen möchten. Sie möchten beispielsweise die Netzwerkkonnektivität mit Microsoft 365 von Ihren Bürostandorten mit den Bezeichnungen O1, O2 und O3 überwachen. Installieren Sie den Log Analytics-Agent auf jeweils mindestens einem Knoten in O1, O2 und O3. 

* **ExpressRoute-Monitor** : Installieren Sie mindestens einen Log Analytics-Agent in Ihrem virtuellen Azure-Netzwerk. Installieren Sie außerdem mindestens einen Agent in Ihrem lokalen-Subnetzwerk, das über privates ExpressRoute-Peering verbunden ist.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurieren von Log Analytics-Agents für die Überwachung 

Der Netzwerkleistungsmonitor verwendet synthetische Transaktionen, um die Netzwerkleistung zwischen Quell- und Ziel-Agents zu überwachen. Bei der Überwachung im Systemmonitor und im Dienstkonnektivitätsmonitor kann zwischen dem TCP- und dem ICMP-Protokoll gewählt werden. Für den ExpressRoute-Monitor steht nur TCP als Überwachungsprotokoll zur Verfügung. Stellen Sie sicher, dass die Firewall die Kommunikation zwischen den für die Überwachung verwendeten Log Analytics-Agents über das ausgewählte Protokoll zulässt. 

* **TCP-Protokoll** : Wenn Sie sich für TCP als Überwachungsprotokoll entschieden haben, öffnen Sie den Firewallport für die Agents, die für den Netzwerkleistungsmonitor und ExpressRoute-Monitor verwendet werden, um sicherzustellen, dass die Agents eine Verbindung miteinander herstellen können. Um den Port auf Windows-Computern zu öffnen, führen Sie das PowerShell-Skript [EnableRules.ps1](https://aka.ms/npmpowershellscript) ohne Parameter in einem PowerShell-Fenster mit Administratorrechten aus.
Auf Linux-Computern müssen die zu verwendenden Portnummern manuell geändert werden. 
* Navigieren Sie zum Pfad „/var/opt/microsoft/omsagent/npm_state“. 
* Öffnen Sie die Datei „npmdregistry“.
* Ändern Sie den Wert für die Portnummer ```“PortNumber:<port of your choice>”```.

 Beachten Sie, dass die verwendeten Portnummern für alle in einem Arbeitsbereich verwendeten Agents identisch sein sollten. 

Das Skript erstellt die für die Lösung erforderlichen Registrierungsschlüssel. Außerdem erstellt es Regeln für die Windows-Firewall, damit Agents TCP-Verbindungen miteinander herstellen können. Die vom Skript erstellten Registrierungsschlüssel geben an, ob die Debugprotokolle und der Pfad zur Protokolldatei protokolliert werden sollen. Ferner definiert das Skript den für die Kommunikation verwendeten TCP-Port des Agents. Die Werte für diese Schlüssel werden vom Skript automatisch festgelegt. Ändern Sie diese Schlüssel nicht manuell. Standardmäßig wird Port 8084 geöffnet. Sie können einen benutzerdefinierten Port verwenden, indem Sie im Skript den Parameter „portNumber“ angeben. Verwenden Sie auf allen Computern, auf denen das Skript ausgeführt wird, den gleichen Port. 

   >[!NOTE]
   > Das Skript konfiguriert nur die lokale Windows-Firewall. Bei Verwendung einer Netzwerkfirewall müssen Sie sicherstellen, dass diese den Datenverkehr zum TCP-Port erlaubt, der vom Netzwerkleistungsmonitor verwendet wird.

   >[!NOTE]
   > Für den Dienstkonnektivitätsmonitor muss das PowerShell-Skript [EnableRules.ps1](https://aka.ms/npmpowershellscript ) nicht ausgeführt werden.

    

* **ICMP-Protokoll** : Wenn Sie sich für ICMP als Überwachungsprotokoll entschieden haben, aktivieren Sie die folgenden Firewallregeln, um ICMP zuverlässig verwenden zu können:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurieren der Projektmappe 

1. Fügen Sie die Netzwerkleistungsmonitor-Lösung Ihrem Arbeitsbereich aus [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) hinzu. Sie können auch den unter [Überwachungslösungen in Azure Monitor](./solutions.md) beschriebenen Prozess verwenden. 
2. Öffnen Sie Ihren Log Analytics-Arbeitsbereich, und wählen Sie die Kachel **Übersicht** aus. 
3. Wählen Sie die Kachel **Netzwerkleistungsmonitor** mit der Meldung *Für die Lösung ist eine weitere Konfiguration erforderlich* aus.

   ![Kachel des Netzwerkleistungsmonitors](media/network-performance-monitor/npm-config.png)

4. Auf der Seite **Setup** finden Sie die Option zum Installieren von Log Analytics-Agents sowie zum Konfigurieren der Agents für die Überwachung in der Ansicht **Allgemeine Einstellungen**. Wenn Sie Log Analytics-Agents installiert und konfiguriert haben, wählen Sie (wie bereits erläutert) die Ansicht **Setup** aus, um die Funktion zu konfigurieren, die Sie verwenden möchten. 

   **Systemmonitor** : Wählen Sie in der Systemmonitor- **Standardregel** aus, welches Protokoll für synthetische Transaktionen verwendet werden soll, und wählen Sie dann **Speichern und fortfahren** aus. Diese Protokollauswahl gilt nur für die vom System generierte Standardregel. Sie müssen das Protokoll jedes Mal explizit auswählen, wenn Sie eine Systemmonitorregel erstellen. Sie können jederzeit zu den Einstellungen der **Standardregel** auf der Registerkarte **Systemmonitor** wechseln und das Protokoll später ändern (diese wird nach Abschluss der Tag-0-Konfiguration angezeigt). Sollten Sie die Systemmonitorfunktion nicht benötigen, können Sie die **Standardregel** über die Standardregeleinstellungen auf der Registerkarte **Systemmonitor** deaktivieren.

   ![Ansicht „Systemmonitor“](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Dienstkonnektivitätsmonitor** : Diese Funktion bietet integrierte, vorkonfigurierte Tests zur Überwachung der Netzwerkkonnektivität mit Microsoft 365 und Dynamics 365 über Ihre Agents. Wählen Sie die Microsoft 365- und Dynamics 365-Dienste aus, die Sie überwachen möchten, indem Sie die Kontrollkästchen neben ihnen aktivieren. Wählen Sie **Agents hinzufügen** aus, um die gewünschten Agents für die Überwachung auszuwählen. Wenn Sie diese Funktion nicht verwenden oder später einrichten möchten, nehmen Sie keine Auswahl vor, und wählen Sie dann **Speichern und fortfahren** aus.

   ![Ansicht „Dienstkonnektivitätsmonitor“](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute-Monitor** : Wählen Sie **Jetzt ermitteln** aus, um alle privaten ExpressRoute-Peerings zu ermitteln, die mit den virtuellen Netzwerken in dem Azure-Abonnement verbunden sind, das mit diesem Log Analytics-Arbeitsbereich verknüpft ist. 

   ![Ansicht „ExpressRoute-Monitor“](media/network-performance-monitor/npm-express-route.png)

   Nach Abschluss der Ermittlung werden die ermittelten Verbindungen und Peerings in einer Tabelle aufgeführt. 

   ![Seite „Konfiguration des Netzwerkleistungsmonitors“](media/network-performance-monitor/npm-private-peerings.png)
    
Die Überwachung für diese Verbindungen und Peerings ist zunächst deaktiviert. Wählen Sie jede Ressource aus, die Sie überwachen möchten, und konfigurieren Sie die Überwachung in der Detailansicht auf der rechten Seite. Wählen Sie zum Speichern der Konfiguration **Speichern** aus. Weitere Informationen finden Sie im Artikel „Konfigurieren der ExpressRoute-Überwachung“. 

Nach Abschluss der Einrichtung dauert es zwischen 30 Minuten und einer Stunde, bis die Daten aufgefüllt wurden. Während die Lösung Daten aus Ihrem Netzwerk aggregiert, wird auf der Kachel **Übersicht** des Netzwerkleistungsmonitors die Meldung *Für die Lösung ist eine weitere Konfiguration erforderlich.* angezeigt. Nachdem die Daten gesammelt und indiziert wurden, ändert sich die Kachel **Übersicht** und informiert Sie in einer Zusammenfassung über die Integrität Ihres Netzwerks. Daraufhin können Sie die Überwachung der Knoten, auf denen Log Analytics-Agents installiert sind, sowie die Subnetze, die in Ihrer Umgebung ermittelt wurden, bearbeiten.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Bearbeiten von Überwachungseinstellungen für Subnetze und Knoten 

Alle Subnetze mit mindestens einem installierten Agent werden auf der Registerkarte **Subnetzwerke** der Konfigurationsseite aufgeführt. 


So aktivieren bzw. deaktivieren Sie die Überwachung bestimmter Subnetzwerke:

1. Aktivieren oder deaktivieren Sie das Kontrollkästchen neben der **Subnetzwerk-ID**. Stellen Sie dann sicher, dass **Zur Überwachung verwenden** je nach Bedarf aktiviert oder deaktiviert ist. Sie können mehrere Subnetze aktivieren oder deaktivieren. Deaktivierte Subnetzwerke werden nicht überwacht, da die Agents dahingehend aktualisiert werden, dass sie keine Pings anderer Agents mehr ausführen. 
2. Wählen Sie die Knoten aus, die Sie in einem bestimmten Subnetz überwachen möchten. Wählen Sie das Subnetzwerk aus der Liste aus, und verschieben Sie die erforderlichen Knoten zwischen den Listen, die nicht überwachte und überwachte Knoten enthalten. Sie können dem Subnetzwerk eine benutzerdefinierte Beschreibung hinzufügen.
3. Wählen Sie zum Speichern der Konfiguration **Speichern** aus. 

#### <a name="choose-nodes-to-monitor"></a>Auswählen zu überwachender Knoten

Alle Knoten, auf denen ein Agent installiert ist, werden auf der Registerkarte **Knoten** aufgeführt. 

1. Aktivieren bzw. deaktivieren Sie die Knoten, die überwacht werden sollen bzw. deren Überwachung beendet werden soll. 
2. Aktivieren bzw. deaktivieren Sie nach Bedarf **Zur Überwachung verwenden**. 
3. Wählen Sie **Speichern** aus. 


Konfigurieren Sie die gewünschten Funktionen:

- [Systemmonitor](network-performance-monitor-performance-monitor.md#configuration)
- [Dienstkonnektivitätsmonitor](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute-Monitor](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Details zur Datensammlung
Zum Erfassen von Informationen zu Verlusten und zur Latenz verwendet der Netzwerkleistungsmonitor TCP SYN-SYNACK-ACK-Handshakepakete, wenn „TCP“ als Protokoll ausgewählt ist. Der Netzwerkleistungsmonitor verwendet „ICMP ECHO REPLY“, wenn „ICMP“ als Protokoll ausgewählt ist. Außerdem wird die Ablaufverfolgungsroute zum Abrufen von Topologieinformationen verwendet.

Die folgende Tabelle zeigt Datensammlungsmethoden und andere Details, wie Daten für den Netzwerkleistungsmonitor gesammelt werden, an.

| Plattform | Direkt-Agent | System Center Operations Manager-Agent | Azure Storage | Operations Manager erforderlich? | Daten vom Operations Manager-Agent über Verwaltungsgruppe gesendet | Sammlungshäufigkeit |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP-Handshakes/ICMP ECHO-Nachrichten werden alle fünf Sekunden gesendet, Daten alle drei Minuten |
 

 
Die Lösung nutzt synthetische Transaktionen, um die Integrität des Netzwerks zu bewerten. Log Analytics-Agents, die an verschiedenen Punkten im Netzwerk installiert sind, tauschen TCP- oder ICMP Echo-Pakete miteinander aus. Ob die Agents TCP-Pakete oder ICMP Echo verwenden, hängt davon ab, welches Protokoll Sie für die Überwachung ausgewählt haben. Dabei erhalten Agents Informationen zur Roundtripzeit und ggf. zu Paketverlust. Jeder Agent führt zudem in regelmäßigen Abständen eine Routenverfolgung (Traceroute) zu anderen Agents aus, um alle zu testenden Routen im Netzwerk zu ermitteln. Anhand dieser Daten können die Agents die Netzwerklatenz und die Paketverluste herleiten. Die Tests werden alle fünf Sekunden wiederholt. Daten werden von den Agents für etwa drei Minuten aggregiert, bevor sie in den Log Analytics-Workspace in Azure Monitor hochgeladen werden.



>[!NOTE]
> Obwohl die Agents häufig miteinander kommunizieren, generieren sie bei den Tests keinen nennenswerten Netzwerkdatenverkehr. Agents bedienen sich lediglich der TCP SYN-SYNACK-ACK-Handshakepakete, um die Datenverluste und die Latenz zu ermitteln. Es werden keine Datenpakete ausgetauscht. Während dieses Vorgangs kommunizieren die Agents nur bei Bedarf miteinander. Die Agentkommunikationstopologie wurde optimiert, um den Netzwerkdatenverkehr zu verringern.

## <a name="use-the-solution"></a>Verwenden der Lösung 

### <a name="network-performance-monitor-overview-tile"></a>Kachel „Übersicht“ des Netzwerkleistungsmonitors 

Nach dem Aktivieren der Netzwerkleistungsmonitor-Lösung wird auf der Kachel der Lösung auf der Seite **Übersicht** eine kurze Übersicht zur Integrität des Netzwerks angezeigt. 

 ![Kachel „Übersicht“ des Netzwerkleistungsmonitors](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Dashboard des Netzwerkleistungsmonitors 

* **Top-Netzwerkintegritätsereignisse** : Diese Seite enthält eine Liste mit den aktuellen Integritätsereignissen und Warnungen im System sowie die Angabe, seit wann das Ereignis aktiv ist. Ein Integritätsereignis oder eine Warnung wird generiert, wenn der Wert der ausgewählten Metrik (Verlust, Wartezeit, Antwortzeit oder Bandbreitennutzung) für die Überwachungsregel den Schwellenwert überschreitet. 

* **ExpressRoute-Monitor** : Auf dieser Seite werden Integritätsübersichten für die verschiedenen ExpressRoute-Peeringverbindungen bereitgestellt, die die Lösung überwacht. Die Kachel **Topologie** zeigt die Anzahl von Netzwerkpfaden durch die ExpressRoute-Verbindungen an, die in Ihrem Netzwerk überwacht werden. Wählen Sie diese Kachel aus, um zur Ansicht **Topologie** zu navigieren.

* **Dienstkonnektivitätsmonitor** : Auf dieser Seite finden Sie Integritätsübersichten für die verschiedenen Tests, die Sie erstellt haben. Die Kachel **Topologie** zeigt an, wie viele Endpunkte überwacht werden. Wählen Sie diese Kachel aus, um zur Ansicht **Topologie** zu navigieren.

* **Systemmonitor** : Diese Seite enthält Integritätszusammenfassungen für die **Netzwerk** - und **Subnetzwerk** verbindungen, die die Lösung überwacht. Die Kachel **Topologie** zeigt die Anzahl von Netzwerkpfaden an, die in Ihrem Netzwerk überwacht werden. Wählen Sie diese Kachel aus, um zur Ansicht **Topologie** zu navigieren. 

* **Allgemeine Abfragen** : Diese Seite enthält eine Sammlung von Suchabfragen zum direkten Abrufen von Rohdaten für die Netzwerküberwachung. Sie können diese Abfragen als Ausgangspunkt für das Erstellen eigener Abfragen für benutzerdefinierte Berichte verwenden. 

   ![Dashboard des Netzwerkleistungsmonitors](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Drilldown für mehr Tiefe 

Sie können auf dem Lösungsdashboard verschiedene Verbindungen auswählen, um zu den für Sie relevanten Bereichen einen Drilldown auszuführen. Wenn beispielsweise auf dem Dashboard eine Warnung oder eine fehlerhafte Netzwerkverbindung angezeigt wird, können Sie diese auswählen, um diesen Umstand näher zu untersuchen. Eine Seite führt alle Subnetzwerkverbindungen für die betreffende Netzwerkverbindung auf. Sie können den Datenverlust, die Latenz und den Integritätsstatus jeder Subnetzwerkverbindung anzeigen. Sie können schnell herausfinden, welche Subnetzwerkverbindung Probleme verursacht. Wählen Sie **Knotenverbindungen anzeigen** aus, um alle Knotenverbindungen für die fehlerhafte Subnetzverbindung anzuzeigen. Nun sehen Sie die einzelnen Knoten-zu-Knoten-Verbindungen und können die fehlerhaften Knotenverbindungen ausfindig machen. 

Wählen Sie **Topologie anzeigen** aus, um die Hop-by-Hop-Topologie der Routen zwischen den Quell- und den Zielknoten anzuzeigen. Die fehlerhaften Routen werden rot dargestellt. Sie können die Latenz anzeigen, die durch jeden Hop verursacht wird, sodass Sie das Problem schnell einem bestimmten Teil des Netzwerks zuordnen können.

 

### <a name="network-state-recorder-control"></a>Steuerelement „Network State Recorder“

Jede Ansicht zeigt eine Momentaufnahme der Netzwerkintegrität zu einem bestimmten Zeitpunkt. Standardmäßig wird der aktuelle Status angezeigt. Die Leiste am oberen Rand der Seite zeigt den Zeitpunkt, für den der Zustand angezeigt wird. Um eine Momentaufnahme Ihres Netzwerkzustands zu einem früheren Zeitpunkt anzuzeigen, wählen Sie **Aktionen** aus. Sie können die automatische Aktualisierung für eine beliebige Seite aktivieren oder deaktivieren, während Sie den aktuellen Status anzeigen. 

 ![Network State Recorder](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trenddiagramme 

Auf jeder Ebene, zu der Sie einen Drilldown ausführen, sehen Sie den Trend der jeweiligen Metrik. Er kann sich um Datenverluste, Latenz, Antwortzeit oder Nutzung der Bandbreite handeln. Mit dem Zeitsteuerelement am oberen Rand des Diagramms können Sie das Zeitintervall für den Trend ändern. 

Trenddiagramme zeigen die Leistung einer Leistungsmetrik im historischen Kontext an. Einige Netzwerkprobleme sind vorübergehender Natur und lassen sich durch einen Blick auf den aktuellen Zustand des Netzwerks nur schwer erkennen. Probleme können kurzfristig auftreten und wieder verschwinden, bevor sie bemerkt werden, um dann zu einem späteren Zeitpunkt erneut aufzutreten. Solche vorübergehender Probleme können auch für Anwendungsadministratoren schwierig sein. Die Probleme zeigen sich oft als unerklärliche Zunahme der Antwortzeit der Anwendung, selbst wenn alle Anwendungskomponenten scheinbar reibungslos ausgeführt werden. 

Sie können diese Arten von Problemen leicht erkennen, indem Sie ein Trenddiagramm untersuchen. Das Problem tritt als eine plötzliche Spitze in der Netzwerklatenz oder als Paketverlust auf. Zum Untersuchen des Problems verwenden Sie den Network State Recorder, um die Netzwerkmomentaufnahme und die Topologie für den Zeitpunkt anzuzeigen, zu dem das Problem aufgetreten ist.

 
![Trenddiagramme](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topologiekarte 

Der Netzwerkleistungsmonitor zeigt die Hop-by-Hop-Topologie von Routen zwischen dem Quell- und dem Zielendpunkt auf einer interaktiven Topologiekarte an. Um die Topologiekarte anzuzeigen, wählen Sie die Kachel **Topologie** im Lösungsdashboard aus. Sie können auch den Linkt **Topologie anzeigen** auf den Drilldownseiten auswählen. 

Die Topologiekarte zeigt, wie viele Routen zwischen Quelle und Ziel vorhanden sind und welche Pfade die Datenpakete nehmen. Außerdem wird die Wartezeit pro Netzwerkhop angezeigt. Alle Pfade, bei denen die Gesamtwartezeit des Pfads über dem Schwellenwert liegt (gemäß der entsprechenden Überwachungsregel), werden rot dargestellt. 

Wenn Sie in der Topologiekarte einen Knoten auswählen oder darauf zeigen, sehen Sie die Eigenschaften des Knotens, z.B. den FQDN und die IP-Adresse. Wählen Sie einen Hop aus, um die zugehörige IP-Adresse anzuzeigen. Den problematischen Netzwerkhop erkennen Sie an der Wartezeit, die er verursacht. Um bestimmte Routen zu filtern, verwenden Sie die Filter im reduzierbaren Aktionsbereich. Zum Vereinfachen der Netzwerktopologien blenden Sie mithilfe des Schiebereglers im Aktionsbereich die zwischenzeitlichen Hops aus. Mit dem Mausrad können Sie die Topologiekarte vergrößern oder verkleinern. 

Bei der in der Karte gezeigten Topologie handelt es sich um eine Layer 3-Topologie, die keine Layer 2-Geräte und -Verbindungen enthält. 

 
![Topologiekarte](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Protokollabfragen in Azure Monitor

Alle im Dashboard des Netzwerkleistungsmonitors und auf den Drilldownseiten grafisch dargestellten Daten sind auch nativ über [Protokollabfragen](../log-query/log-query-overview.md) verfügbar. Sie können eine interaktive Analyse von Daten im Repository ausführen und Daten aus verschiedenen Quellen korrelieren. Sie können auch benutzerdefinierte Warnungen und Ansichten erstellen und die Daten nach Excel, Power BI oder in einen freigegebenen Link exportieren. Der Bereich **Allgemeine Abfragen** im Dashboard enthält einige praktische Abfragen, die Sie als Ausgangspunkt für die Erstellung eigener Abfragen und Berichte verwenden können. 

## <a name="alerts"></a>Alerts

Der Netzwerkleistungsmonitor verwendet die Warnfunktionen von [Azure Monitor](../platform/alerts-overview.md).

Das bedeutet, dass alle Benachrichtigungen mithilfe von [Aktionsgruppen](../platform/action-groups.md) verwaltet werden.  

Gehen Sie als NPM-Benutzer wie folgt vor, um eine Warnung über Log Analytics zu erstellen: 
1. Es wird ein Link angezeigt, der Sie zum Azure-Portal umleitet. Klicken Sie darauf, um das Portal aufzurufen.
2. Klicken Sie auf die Kachel für die Netzwerkleistungsmonitor-Lösung. 
3. Navigieren Sie zu „Konfigurieren“.  
4. Wählen Sie den Test aus, für den Sie eine Warnung erstellen möchten, und führen Sie die nachfolgenden Schritte aus.

Gehen Sie als NPM-Benutzer wie folgt vor, um eine Warnung über das Azure-Portal zu erstellen:  
1. Sie können wahlweise Ihre E-Mail-Adresse direkt eingeben oder Warnungen über Aktionsgruppen erstellen.
2. Wenn Sie Ihre E-Mail-Adresse direkt eingeben möchten, wird eine Aktionsgruppe namens **NPM Email ActionGroup** erstellt und die E-Mail-ID wird dieser Aktionsgruppe hinzugefügt.
3. Wenn Sie sich für Aktionsgruppen entscheiden, müssen Sie eine zuvor erstellte Aktionsgruppe auswählen. [Hier](../platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) erfahren Sie, wie Sie eine Aktionsgruppe erstellen. 
4. Nachdem die Warnung erfolgreich erstellt wurde, können Sie über den Link „Warnungen verwalten“ Ihre Warnungen verwalten. 

Wenn Sie eine Warnung erstellen, erstellt NPM eine abfragebasierte Warnungsregel in Azure Monitor. Diese Abfrage wird standardmäßig alle 5 Minuten ausgelöst. Die ersten 250 Protokollwarnungsregeln von Azure Monitor sind kostenlos. Jede weitere Protokollwarnungsregel wird gemäß den [Warnungspreisen auf der Seite mit den Azure Monitor-Preisen](https://azure.microsoft.com/pricing/details/monitor/) abgerechnet.
Benachrichtigungen werden separat abgerechnet (siehe [Benachrichtigungspreise auf der Seite mit den Azure Monitor-Preisen](https://azure.microsoft.com/pricing/details/monitor/)).


## <a name="pricing"></a>Preise

Informationen zu Preisen sind [online](network-performance-monitor-pricing-faq.md) verfügbar.

## <a name="provide-feedback"></a>Feedback geben 

* **UserVoice:** Sie können Ihre Ideen zu Features des Netzwerkleistungsmonitors veröffentlichen, an denen wir arbeiten sollen. Besuchen Sie die [UserVoice-Seite](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Treten Sie unserer Gruppe bei:** Wir freuen uns über neue Kunden, die unserer Gruppe beitreten möchten. Als Teil der Gruppe erhalten Sie vorab Zugriff auf neue Funktionen und können uns dabei helfen, den Netzwerkleistungsmonitor zu verbessern. Wenn Sie beitreten möchten, füllen Sie diesen [kurzen Fragebogen](https://aka.ms/npmcohort) aus. 

## <a name="next-steps"></a>Nächste Schritte 
Informieren Sie sich ausführlicher über [Systemmonitor](network-performance-monitor-performance-monitor.md), [Dienstkonnektivitätsmonitor](network-performance-monitor-performance-monitor.md) und [ExpressRoute-Monitor](network-performance-monitor-expressroute.md). 

