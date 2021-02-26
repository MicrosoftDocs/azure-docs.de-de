---
title: Netzwerkleistungsmonitor-Lösung in Azure Log Analytics | Microsoft-Dokumentation
description: Mit der ExpressRoute-Monitor-Funktion im Netzwerkleistungsmonitor können Sie die End-to-End-Konnektivität und -Leistung zwischen Ihren Zweigstellen und Azure über Azure ExpressRoute überwachen.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 91bb303fca4517f84ad8b430f26543e9f116fddc
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833081"
---
# <a name="expressroute-monitor"></a>ExpressRoute-Monitor

> [!IMPORTANT]
> Ab dem 1. Juli 2021 ist es nicht mehr möglich, einem bestehenden Arbeitsbereich neue Tests hinzuzufügen oder einen neuen Arbeitsbereich im Netzwerkleistungsmonitor zu aktivieren. Sie können weiterhin die Tests verwenden, die vor dem 1. Juli 2021 erstellt wurden. [Migrieren Sie Ihre Tests vor dem 29. Februar 2024 aus dem Netzwerkleistungsmonitor zum neuen Verbindungsmonitor](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) in Azure Network Watcher, um die Dienstunterbrechungen im Zusammenhang mit Ihren aktuellen Workloads zu minimieren.

Mit der ExpressRoute-Monitor-Funktion im [Netzwerkleistungsmonitor](network-performance-monitor.md) können Sie die End-to-End-Konnektivität und -Leistung zwischen Ihren Zweigstellen und Azure über Azure ExpressRoute überwachen. Zentrale Vorteile: 

- Automatische Erkennung der mit Ihrem Abonnement verknüpften ExpressRoute-Leitungen
- Nachverfolgung von Bandbreitennutzung, Verlusten und Wartezeiten auf Ebene der Verbindung, des Peerings und von Azure Virtual Network für ExpressRoute
- Erkennung der Netzwerktopologie Ihrer ExpressRoute-Leitungen

![ExpressRoute-Monitor](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfiguration 
Öffnen Sie die [Netzwerkleistungsmonitor](network-performance-monitor.md)-Lösung, und wählen Sie die Schaltfläche **Konfigurieren** aus, um die Konfiguration für den Netzwerkleistungsmonitor zu öffnen.

### <a name="configure-network-security-group-rules"></a>Konfigurieren von Regeln für Netzwerksicherheitsgruppen 
Konfigurieren Sie für die Server in Azure, die zur Überwachung über den Netzwerkleistungsmonitor verwendet werden, Regeln für Netzwerksicherheitsgruppen (NSGs), um TCP-Datenverkehr an dem vom Netzwerkleistungsmonitor für synthetische Transaktionen verwendeten Port zuzulassen. Der Standardport ist 8084. Mit dieser Konfiguration kann der auf virtuellen Azure-Computern installierte Log Analytics-Agent mit einem lokalen Überwachungs-Agent kommunizieren. 

Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter  [Netzwerksicherheitsgruppen](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Bevor Sie mit diesem Schritt fortfahren, installieren Sie den lokalen Server-Agent und den Azure-Server-Agent, und führen Sie das PowerShell-Skript „EnableRules.ps1“ aus. 

 
### <a name="discover-expressroute-peering-connections"></a>Ermitteln der ExpressRoute-Peeringverbindungen 
 
1. Wählen Sie die Ansicht **ExpressRoute-Peerings** aus.
2. Wählen Sie **Jetzt ermitteln** aus, um alle privaten ExpressRoute-Peerings zu ermitteln, die mit den virtuellen Netzwerken in dem mit diesem Azure Log Analytics-Arbeitsbereich verknüpften Azure-Abonnement verbunden sind.

    >[!NOTE]
    > Die Lösung ermittelt derzeit nur private ExpressRoute-Peerings. 

    >[!NOTE]
    > Es werden nur private Peerings ermittelt, die mit den virtuellen Netzwerken verbunden sind, die dem mit diesem Log Analytics-Arbeitsbereich verknüpften Abonnement zugeordnet sind. Wenn ExpressRoute mit virtuellen Netzwerken außerhalb des mit diesem Arbeitsbereich verknüpften Abonnements verbunden ist, erstellen Sie in diesen Abonnements einen Log Analytics-Arbeitsbereich. Verwenden Sie dann den Netzwerkleistungsmonitor zum Überwachen dieser Peerings. 

    ![Konfiguration des ExpressRoute-Monitors](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Nach Abschluss der Ermittlung werden die ermittelten privaten Peeringverbindungen in einer Tabelle aufgeführt. Die Überwachung für diese Peerings ist zunächst deaktiviert. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Aktivieren der Überwachung der ExpressRoute-Peeringverbindungen 

1. Wählen Sie die private Peeringverbindung aus, die Sie überwachen möchten.
2. Aktivieren Sie im rechten Bereich das Kontrollkästchen **Monitor this Peering** (Dieses Peering überwachen). 
3. Wenn für diese Verbindung Integritätsereignisse erstellt werden sollen, wählen Sie **Enable Health Monitoring for this peering** (Integritätsüberwachung für dieses Peering aktivieren) aus. 
4. Wählen Sie Überwachungsbedingungen aus. Sie können benutzerdefinierte Schwellenwerte für das Generieren von Integritätsereignissen festlegen, indem Sie Schwellenwerte eingeben. Sobald der Wert einer Bedingung den festgelegten Schwellenwert für die Peeringverbindung überschreitet, wird ein Integritätsereignis generiert. 
5. Wählen Sie **Agents hinzufügen** aus, um die Überwachungs-Agents auszuwählen, die Sie zum Überwachen dieser Peeringverbindung verwenden möchten. Stellen Sie sicher, dass Sie auf beiden Seiten der Verbindung Agents hinzufügen. Es muss mindestens ein Agent im virtuellen Netzwerk mit diesem Peering verbunden sein. Darüber hinaus muss mindestens ein lokaler Agent mit diesem Peering verbunden sein. 
6. Wählen Sie zum Speichern der Konfiguration **Speichern** aus. 

   ![Konfiguration der ExpressRoute-Überwachung](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Nachdem Sie die Regeln aktiviert und Werte und Agents ausgewählt haben, warten Sie 30 bis 60 Minuten, bis die Werte aufgefüllt wurden und die Kacheln der **ExpressRoute-Überwachung** angezeigt werden. Sobald die Überwachungskacheln angezeigt werden, werden Ihre ExpressRoute-Leitungen und -Verbindungsressourcen vom Netzwerkleistungsmonitor überwacht. 

>[!NOTE]
> Diese Funktion kann zuverlässig für Arbeitsbereiche verwendet werden, für die ein Upgrade auf die neue Abfragesprache durchgeführt wurde.

## <a name="walkthrough"></a>Exemplarische Vorgehensweise 

Das Dashboard des Netzwerkleistungsmonitors zeigt eine Übersicht über die Integrität von ExpressRoute-Leitungen und -Peeringverbindungen. 

![Dashboard des Netzwerkleistungsmonitors](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Verbindungsliste 

Wählen Sie die Kachel „ExpressRoute-Leitungen“ aus, um eine Liste aller überwachten ExpressRoute-Leitungen anzuzeigen. Sie können eine Verbindung auswählen und deren Integritätsstatus, Trenddiagramme für den Paketverlust, Bandbreitennutzung und Latenz anzuzeigen. Die Diagramme sind interaktiv. Sie können ein benutzerdefiniertes Zeitfenster für das Zeichnen der Diagramme auswählen. Ziehen Sie die Maus über einen Bereich des Diagramms, um diesen zu vergrößern und detaillierte Datenpunkte anzuzeigen. 

![Liste der ExpressRoute-Leitungen](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trends für Verlust, Wartezeit und Durchsatz 

Die Diagramme für Bandbreitennutzung, Latenz und Verlust sind interaktiv. Sie können jeden Bereich dieser Diagramme mithilfe der Maussteuerung vergrößern. Außerdem können Sie die Daten für Bandbreite, Latenz und Verlust für andere Intervalle anzeigen. Wählen Sie in der linken oberen Ecke unter der Schaltfläche **Aktionen** die Option  **Datum/Uhrzeit** aus. 

![ExpressRoute-Latenz](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Peeringliste 

Wählen Sie die Kachel **Private Peerings** auf dem Dashboard aus, um eine Liste aller Verbindungen mit virtuellen Netzwerken über privates Peering anzuzeigen. Hier können Sie eine virtuelle Netzwerkverbindung auswählen und deren Integritätsstatus, Trenddiagramme für Paketverlust, Bandbreitennutzung und Latenz anzeigen lassen. 

![ExpressRoute-Peerings](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Verbindungstopologie 

Wählen Sie die Kachel **Topologie** aus, um die Topologie der Verbindung anzuzeigen. Durch diese Aktion gelangen Sie zur Ansicht der Topologie für die ausgewählte Leitung oder das ausgewählte Peering. Das Topologiediagramm enthält die Latenz für jedes Segment im Netzwerk, außerdem wird jeder Schicht-3-Hop durch einen Knoten des Diagramms dargestellt. Durch das Auswählen eines Hops werden weitere Details zum Hop angezeigt. Um den Sichtbarkeitsgrad zu erhöhen, sodass lokale Hops eingeschlossen werden, bewegen Sie den Schieberegler unter **FILTER**. Das Bewegen des Schiebereglers nach links oder rechts vergrößert bzw. verkleinert die Anzahl der Hops im Topologiediagramm. Die Latenz ist für jedes Segment sichtbar, sodass eine schnellere Isolation von Segmenten mit hoher Latenz in Ihrem Netzwerk ermöglicht wird.

![ExpressRoute-Topologie](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Detaillierte Topologieansicht einer Verbindung 

Diese Ansicht zeigt Verbindungen mit virtuellen Netzwerken an. 

![ExpressRoute-Verbindungen mit virtuellen Netzwerken](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnose 

Der Netzwerkleistungsmonitor unterstützt Sie beim Diagnostizieren mehrerer verbindungsbezogener Konnektivitätsprobleme. Einige der Probleme, die auftreten können, sind unten aufgeführt.

Sie können die Benachrichtigungscodes sehen und über **LogAnalytics** Benachrichtigungen dafür einstellen. Auf der **NPM-Diagnose** Seite sehen Sie eine Beschreibung für jede Diagnose Nachricht ausgelöst.

| Benachrichtigungscode (Protokolle) | BESCHREIBUNG |
| --- | --- |
| 5501 | Durchlaufen der sekundären Verbindung der ExpressRoute-Leitung nicht möglich. |
| 5502 | Durchlaufen der primären Verbindung der ExpressRoute-Leitung nicht möglich. |
| 5503 | Es wurde keine Leitung für das mit dem Arbeitsbereich verknüpfte Abonnement gefunden. | 
| 5508 | Es kann nicht ermittelt werden, ob der Datenverkehr über eine Leitung geleitet wird. |
| 5510 | Der Datenverkehr wird nicht über die vorgesehene Leitung geleitet. | 
| 5511 | Der Datenverkehr wird nicht über das vorgesehene virtuelle Netzwerk geleitet. | 

**Die Verbindung ist nicht verfügbar:** Der Netzwerkleistungsmonitor informiert Sie, sobald die Konnektivität zwischen Ihren lokalen Ressourcen und virtuellen Azure-Netzwerken verloren geht. Durch diese Benachrichtigung können Sie proaktive Maßnahmen ergreifen und Ausfallzeiten verringern, bevor Benutzer das Problem eskalieren.

![ExpressRoute-Leitung nicht verfügbar](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Datenverkehr wird nicht über die beabsichtigte Verbindung übertragen:** Der Netzwerkleistungsmonitor benachrichtigt Sie, wenn kein Datenverkehr durch die vorgesehene ExpressRoute-Leitung fließt. Dieses Problem kann auftreten, wenn die Verbindung nicht verfügbar ist und der Datenverkehr über die Ausweichroute übermittelt wird. Es kann auch auftreten, wenn ein Routingproblem vorliegt. Anhand dieser Informationen können Sie proaktiv mögliche Konfigurationsprobleme in Ihren Routingrichtlinien behandeln und sicherstellen, dass die beste und sicherste Route verwendet wird. 

 

**Datenverkehr wird nicht über die primäre Verbindung übertragen:** Der Netzwerkleistungsmonitor benachrichtigt Sie, wenn der Datenverkehr über die sekundäre ExpressRoute-Leitung übertragen wird. In diesem Fall treten zwar keine Konnektivitätsprobleme auf, durch die proaktive Behandlung der Probleme mit der primären Verbindung sind Sie jedoch besser vorbereitet. 

 
![ExpressRoute-Datenverkehrsfluss](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Leistungsminderung aufgrund von Auslastungsspitzen:** Sie können den Bandbreitennutzungstrend mit dem Wartezeitentrend korrelieren, um zu ermitteln, ob die Leistungsminderung bei der Azure-Workload auf eine Bandbreiten-Nutzungsspitze zurückzuführen ist. Anschließend können Sie entsprechende Maßnahmen ergreifen.

![ExpressRoute-Bandbreitennutzung](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Nächste Schritte
Mit [Protokollsuchen](../log-query/log-query-overview.md) können Sie Detaildatensätze mit Netzwerkleistungsdaten anzeigen.
