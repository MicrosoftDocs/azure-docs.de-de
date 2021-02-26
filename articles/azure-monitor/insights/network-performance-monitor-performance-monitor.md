---
title: Systemmonitor in Netzwerkleistungsmonitor
description: Die Systemmonitorfunktion im Netzwerkleistungsmonitor unterstützt Sie bei der Überwachung der Netzwerkkonnektivität an verschiedenen Punkten in Ihrem Netzwerk. Sie können Cloudbereitstellungen und lokale Standorte, mehrere Rechenzentren und Zweigstellen sowie unternehmenswichtige Microservices/Anwendungen mit mehreren Ebenen überwachen.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 6502d4259aa383c1466967d01282cecfbb8d1ded
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832962"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Netzwerkleistungsmonitor-Lösung: Leistungsüberwachung

> [!IMPORTANT]
> Ab dem 1. Juli 2021 ist es nicht mehr möglich, neue Tests in einem vorhandenen Arbeitsbereich hinzufügen oder einen neuen Arbeitsbereich im Netzwerkleistungsmonitor zu aktivieren. Sie können weiterhin die Tests verwenden, die vor dem 1. Juli 2021 erstellt wurden. [Migrieren Sie Ihre Tests vor dem 29. Februar 2024 aus dem Netzwerkleistungsmonitor zum neuen Verbindungsmonitor](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) in Azure Network Watcher, um die Dienstunterbrechungen im Zusammenhang mit Ihren aktuellen Workloads zu minimieren.

Die Systemmonitorfunktion im [Netzwerkleistungsmonitor](network-performance-monitor.md) unterstützt Sie bei der Überwachung der Netzwerkkonnektivität an verschiedenen Punkten in Ihrem Netzwerk. Sie können Cloudbereitstellungen und lokale Standorte, mehrere Rechenzentren und Zweigstellen sowie unternehmenswichtige Microservices/Anwendungen mit mehreren Ebenen überwachen. Mithilfe des Systemmonitors können Sie Netzwerkprobleme erkennen, bevor sie Ihre Benutzer beinträchtigen. Wichtige Vorteile ergeben sich daraus, dass Sie die folgenden Aufgaben ausführen können: 

- Überwachen von Verlusten und Wartezeiten in verschiedenen Subnetzen und Festlegen von Warnungen.
- Überwachen aller Pfade (einschließlich redundanter Pfade) des Netzwerks.
- Behandeln von vorübergehenden Netzwerkproblemen und Point-in-Time-Netzwerkproblemen, die schwer reproduzierbar sind.
- Ermitteln des spezifischen Netzwerksegments, das für die Leistungsbeeinträchtigung verantwortlich ist.
- Überwachen der Integrität des Netzwerks ohne SNMP.


![Netzwerkleistungsmonitor](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguration
Öffnen Sie die [Netzwerkleistungsmonitor-Lösung](network-performance-monitor.md), und wählen Sie die Schaltfläche **Konfigurieren** aus, um die Konfiguration für den Netzwerkleistungsmonitor zu öffnen.

![Konfigurieren des Netzwerkleistungsmonitors](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Erstellen neuer Netzwerke

Ein Netzwerk im Netzwerkleistungsmonitor ist ein logischer Container für Subnetze. Damit können Sie die Überwachung Ihrer Netzwerkinfrastruktur gemäß Ihren Anforderungen strukturieren. Sie können ein Netzwerk mit einem Anzeigenamen erstellen und entsprechend Ihrer Geschäftslogik Subnetze hinzufügen. Sie können z.B. ein Netzwerk mit dem Namen „London“ erstellen und alle Subnetze in Ihrem Rechenzentrum „London“ hinzufügen. Oder Sie können ein Netzwerk mit dem Namen *ContosoFrontEnd* erstellen und diesem Netzwerk alle Subnetze mit dem Namen „Contoso“ hinzufügen, die für das Front-End Ihrer Anwendung relevant sind. Die Lösung erstellt automatisch ein Standardnetzwerk mit allen Subnetzen, die in Ihrer Umgebung ermittelt wurden. 

Wann immer Sie ein Netzwerk erstellen, fügen Sie diesem ein Subnetz hinzu. Anschließend wird dieses Subnetz aus dem Standardnetzwerk entfernt. Wenn Sie ein Netzwerk löschen, werden alle seine Subnetze automatisch an das Standardnetzwerk zurückgegeben. Das Standardnetzwerk fungiert also als Container für alle Subnetze, die nicht in einem benutzerdefinierten Netzwerk enthalten sind. Sie können das Standardnetzwerk weder bearbeiten noch löschen. Es verbleibt stets im System. Sie können jedoch beliebig viele benutzerdefinierte Netzwerke erstellen. In den meisten Fällen werden die Subnetze in Ihrer Organisation in mehreren Netzwerken angeordnet. Erstellen Sie mindestens ein Netzwerk, um die Subnetze für Ihre Geschäftslogik zu gruppieren.

So erstellen Sie ein neues Netzwerk:


1. Wählen Sie die Registerkarte **Netzwerke** aus.
1. Wählen Sie  **Netzwerk hinzufügen** aus, und geben Sie dann den Netzwerknamen und eine Beschreibung ein. 
2. Wählen Sie mindestens ein Subnetz aus, und wählen Sie dann **Hinzufügen** aus. 
3. Wählen Sie zum Speichern der Konfiguration **Speichern** aus. 


### <a name="create-monitoring-rules"></a>Erstellen von Überwachungsregeln 

Der Systemmonitor generiert Integritätsereignisse, wenn der Schwellenwert der Leistung von Netzwerkverbindungen zwischen zwei Subnetzwerken oder zwischen zwei Netzwerken überschritten wird. Das System kann diese Schwellenwerte automatisch erlernen. Sie können auch benutzerdefinierte Schwellenwerte angeben. Das System erstellt automatisch eine Standardregel, die immer dann ein Integritätsereignis generiert, wenn der Verlust oder die Wartezeit zwischen einem Netzwerk-/Subnetzwerkverbindungspaar den vom System erlernten Schwellenwert überschreitet. Dieser Vorgang erleichtert der Lösung die Überwachung Ihrer Netzwerkinfrastruktur, solange Sie noch nicht alle Überwachungsregeln explizit erstellt haben. Wenn die Standardregel aktiviert ist, senden alle Knoten synthetische Transaktionen an alle anderen Knoten, die Sie für die Überwachung aktiviert haben. Die Standardregel eignet sich für kleine Netzwerke. Ein Beispiel hierfür ist ein Szenario, bei dem auf einer kleinen Anzahl von Servern ein Microservice ausgeführt wird und Sie die Konnektivität aller Server miteinander sicherstellen möchten.

>[!NOTE]
> Es wird empfohlen, die Standardregel zu deaktivieren und benutzerdefinierte Überwachungsregeln zu erstellen. Dies gilt insbesondere bei großen Netzwerken, in denen Sie sehr viele Knoten für die Überwachung verwenden. Benutzerdefinierte Überwachungsregeln können den von der Lösung generierten Datenverkehr verringern und helfen Ihnen, die Überwachung Ihres Netzwerks zu organisieren.

Erstellen Sie Überwachungsregeln gemäß Ihrer Geschäftslogik. Sie möchten beispielsweise die Leistung der Netzwerkverbindung von zwei Zweigstellen mit dem Hauptsitz des Unternehmens überwachen. Gruppieren Sie alle Subnetze in Zweigstelle1 im Netzwerk Z1. Gruppieren Sie dann alle Subnetze in Zweigstelle2 im Netzwerk Z2. Schließlich gruppieren Sie alle Subnetze im Hauptsitz des Unternehmens in Netzwerk H. Erstellen Sie zwei Überwachungsregeln: eine Regel zwischen Z1 und H und eine andere Regel zwischen Z2 und H. 

So erstellen Sie benutzerdefinierte Überwachungsregeln:

1. Wählen Sie auf der Registerkarte **Überwachen** die Option **Regel hinzufügen** aus, und geben Sie den Regelnamen und eine Beschreibung ein.
2. Wählen Sie das zu überwachende Netzwerk- oder Subnetzwerkverbindungspaar aus den Listen aus. 
3. Wählen Sie aus der Netzwerkdropdownliste das Netzwerk aus, das die gewünschten Subnetzwerke enthält. Wählen Sie dann die Subnetzwerke aus der entsprechenden Subnetzwerkdropdownliste aus. Wählen Sie **Alle Subnetzwerke** aus, wenn alle Subnetzwerke in einer Netzwerkverbindung überwacht werden sollen. Wählen Sie analog die übrigen gewünschten Subnetzwerke aus. Sie können **Ausnahme hinzufügen** auswählen, um die Überwachung bestimmter Subnetzwerkverbindungen aus Ihrer Auswahl zu entfernen. 
4. Bei der Ausführung synthetischer Transaktionen können Sie zwischen den Protokollen ICMP und TCP wählen. 
5. Wenn keine Integritätsereignisse für die von Ihnen ausgewählten Elemente erstellt werden sollen, deaktivieren Sie das Kontrollkästchen **Integritätsüberwachung für die mit dieser Regel abgedeckten Verbindungen aktivieren**. 
6. Wählen Sie Überwachungsbedingungen aus. Geben Sie Schwellenwerte ein, um benutzerdefinierte Schwellenwerte für das Generieren von Integritätsereignissen festzulegen. Sobald der Wert einer Bedingung den für Sie festgelegten Schwellenwert für das ausgewählte Netzwerk- oder Subnetzwerkpaar überschreitet, wird ein Integritätsereignis generiert. 
7. Wählen Sie zum Speichern der Konfiguration **Speichern** aus. 

Nachdem Sie eine Überwachungsregel gespeichert haben, können Sie diese Regel in die Warnungsverwaltung integrieren, indem Sie **Warnung erstellen** auswählen. Eine Warnungsregel wird automatisch mit der Suchabfrage erstellt. Andere erforderlichen Parameter werden automatisch ergänzt. Mithilfe einer Warnungsregel können Sie neben den vorhandenen Warnungen im Netzwerkleistungsmonitor auch E-Mail-basierte Warnungen erhalten. Warnungen können auch Problembehandlungsaktionen mit Runbooks auslösen oder mithilfe von Webhooks mit vorhandenen Dienstverwaltungslösungen integriert werden. Zum Bearbeiten der Warnungseinstellungen wählen Sie **Warnung verwalten** aus. 

Nun können Sie weitere Systemmonitorregeln erstellen oder zum Dashboard der Lösung wechseln, um die Funktion zu verwenden.

### <a name="choose-the-protocol"></a>Auswählen des Protokolls

Der Netzwerkleistungsmonitor verwendet synthetische Transaktionen zum Berechnen von Netzwerkleistungmetriken wie Paketverlusten und Verbindungslatenz. Um dieses Konzept besser zu verstehen, sollten Sie einen Netzwerkleistungsmonitor-Agent mit einem Ende einer Netzwerkverbindung verbinden. Dieser Netzwerkleistungsmonitor-Agent sendet Testpakete an einen zweiten Netzwerkleistungsmonitor-Agent, der mit einem anderen Ende des Netzwerks verbunden ist. Der zweite Agent antwortet mit Antwortpaketen. Dieser Vorgang wird einige Male wiederholt. Durch Messen der Anzahl von Antworten und Empfangszeit jeder Antwort kann der erste Netzwerkleistungsmonitor-Agent die Verbindungslatenz und Paketverluste messen. 

Format, Größe und Reihenfolge dieser Pakete richten sich nach dem Protokoll, das Sie bei der Erstellung von Überwachungsregeln auswählen. Je nach Protokoll der Pakete können die zwischengeschalteten Netzwerkgeräte (Router, Switches usw.) diese Pakete möglicherweise unterschiedlich verarbeiten. Folglich wirkt sich Ihre Protokollauswahl auf die Genauigkeit der Ergebnisse aus. Ihre Wahl des Protokolls bestimmt auch, ob Sie nach Bereitstellen der Netzwerkleistungsmonitor-Lösung manuelle Schritte ausführen müssen. 

Der Netzwerkleistungsmonitor bietet Ihnen für die Ausführung synthetischer Transaktionen die Wahl zwischen den Protokollen ICMP und TCP. Wenn Sie beim Erstellen einer synthetischen Transaktionsregel ICMP auswählen, verwenden die Netzwerkleistungsmonitor-Agents ICMP ECHO-Nachrichten zum Berechnen von Netzwerklatenz und Paketverlusten. ICMP ECHO verwendet dieselbe Nachricht, die vom gängigen Hilfsprogramm Ping gesendet wird. Bei Verwenden von TCP als Protokoll senden Netzwerkleistungsmonitor-Agents TCP SYN-Pakete über das Netzwerk. Auf diesen Schritt folgt ein TCP-Handshake als Abschluss und das anschließende Entfernen der Verbindung mithilfe von RST-Paketen. 

Ehe Sie ein Protokoll auswählen, sollten Sie die folgenden Informationen berücksichtigen: 

* **Ermittlung mehrerer Netzwerkrouten.** TCP ist genauer, wenn mehrere Routen ermitteln werden, und benötigt weniger Agents in jedem Subnetz. Beispielsweise können ein oder zwei Agents, die TCP verwenden, alle redundanten Pfade zwischen Subnetzen ermitteln. Bei der Verwendung von ICMP benötigen Sie mehrere Agents, um das gleiche Ergebnis zu erzielen. Wenn Sie bei Verwendung von ICMP über eine Reihe von Routen zwischen zwei Subnetzen verfügen, benötigen Sie mehr als 5N Agents in einem Quell- oder Zielsubnetz.

* **Genauigkeit der Ergebnisse:** Router und Switches weisen ICMP ECHO-Paketen im Vergleich mit TCP-Paketen tendenziell eine niedrigere Priorität zu. Wenn Netzwerkgeräte in bestimmten Situationen stark ausgelastet sind, geben die von TCP abgerufenen Daten den Paketverlust und die Latenz von Anwendungen präziser zurück. Dies erfolgt, da der meiste Datenverkehr über TCP übertragen wird. In solchen Fällen bietet ICMP im Vergleich mit TCP ungenauere Ergebnisse. 

* **Firewallkonfiguration:** Das TCP-Protokoll erfordert, dass TCP-Pakete an einen Zielport gesendet werden. Der von Netzwerkleistungsmonitor-Agents verwendete Standardport ist 8084. Sie können den Port ändern, wenn Sie Agents konfigurieren. Stellen Sie sicher, dass Ihre Netzwerkfirewalls bzw. (in Azure) NSG-Regeln (Netzwerksicherheitsgruppen) Datenverkehr an diesem Port zulassen. Sie müssen auch sicherstellen, dass die lokale Firewall auf den Computern, auf denen Agents installiert sind, für das Zulassen von Datenverkehr an diesem Port konfiguriert ist. Sie können zum Konfigurieren von Firewallregeln auf Ihren Computern Windows PowerShell-Skripts verwenden. Die Netzwerkfirewall müssen Sie allerdings manuell konfigurieren. Im Gegensatz dazu arbeitet ICMP nicht über einen Port. In den meisten Unternehmensumgebungen ist ICMP-Datenverkehr durch die Firewalls zulässig, damit Sie Netzwerkdiagnosetools wie das Hilfsprogramm Ping einsetzen können. Wenn Sie einen Computer von einem anderen aus pingen können, lässt sich das ICMP-Protokoll nutzen, ohne dass Firewalls manuell konfiguriert werden müssen.

>[!NOTE] 
> Einige Firewalls blockieren möglicherweise ICMP, was zur Neuübertragung führen kann, sodass eine große Anzahl von Ereignissen in Ihrem Sicherheitsinformations- und Ereignisverwaltungssystem auftritt. Stellen Sie sicher, dass das Protokoll, das Sie auswählen, nicht von einer Netzwerkfirewall oder NSG blockiert wird. Andernfalls kann der Netzwerkleistungsmonitor das Netzwerksegment nicht überwachen. Es wird empfohlen, TCP für die Überwachung zu verwenden. Verwenden Sie ICMP in Szenarien, in denen TCP nicht verwendet werden kann. Dazu zählen die folgenden Fälle: 
>
> - Sie verwenden clientbasierte Windows-Knoten, da TCP-RAW-Sockets in Windows-Clients unzulässig sind.
> - Ihre Netzwerkfirewall bzw. NSG blockiert TCP.
> - Sie wissen nicht, wie Sie das Protokoll wechseln können.

Wenn Sie sich während der Bereitstellung für ICMP entscheiden, können Sie jederzeit zu TCP wechseln, indem Sie die Standardüberwachungsregel bearbeiten.

1. Navigieren Sie zu **Netzwerkleistung** > **Überwachen** > **Konfigurieren** > **Überwachen**. Wählen Sie dann **Standardregel** aus. 
2. Scrollen Sie zum Abschnitt **Protokoll**, und wählen Sie das Protokoll aus, das Sie verwenden möchten. 
3. Wählen Sie **Speichern** aus, um die Einstellung zu übernehmen. 

Auch wenn die Standardregel ein bestimmtes Protokoll verwendet, können Sie neue Regeln mit einem anderen Protokoll erstellen. Sie können sogar eine Kombination aus Regeln erstellen, bei der einige der Regeln ICMP und andere TCP verwenden. 

## <a name="walkthrough"></a>Exemplarische Vorgehensweise 

Schauen Sie sich nun eine einfache Untersuchung der Grundursache für ein Integritätsereignis an.

Auf dem Lösungsdashboard zeigt ein Integritätsereignis an, dass eine Netzwerkverbindung fehlerhaft ist. Um das Problem zu untersuchen, wählen Sie die Kachel **Überwachte Netzwerkverbindungen** aus.

Die Drilldownseite zeigt an, dass die Netzwerkverbindung **DMZ2-DMZ1** fehlerhaft ist. Wählen Sie **Subnetzverbindungen anzeigen** für diese Netzwerkverbindung aus. 


Auf der Drilldownseite werden alle Subnetzwerkverbindungen der Netzwerkverbindung **DMZ2-DMZ1** angezeigt. Die Latenz beider Subnetzwerkverbindungen hat den Schwellenwert überschritten und bewirkt die Fehlerhaftigkeit der Netzwerkverbindung. Sie können auch die Latenztrends der beiden Subnetzwerkverbindungen anzeigen. Verwenden Sie das Zeitauswahl-Steuerelement im Diagramm, um sich auf den gewünschten Zeitraum zu konzentrieren. Sie können sehen, zu welcher Tageszeit die Latenz ihren Höchstwert erreicht hat. Später können Sie die Protokolle für diesen Zeitraum durchsuchen, um das Problem zu untersuchen. Wählen Sie **Knotenverbindungen anzeigen** aus, um einen weiteren Drilldown auszuführen. 
 
 ![Seite „Subnetzwerkverbindungen“](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Ähnlich wie bei der vorherigen Seite werden auf der Drilldownseite der jeweiligen Subnetzwerkverbindung die enthaltenen Knotenverbindungen aufgeführt. Hier können Sie ähnliche Aktionen ausführen wie im vorherigen Schritt. Wählen Sie **Topologie anzeigen** aus, um die Topologie zwischen den beiden Knoten anzuzeigen. 
 
 ![Seite „Knotenverbindungen“](media/network-performance-monitor-performance-monitor/node-links.png) 

Auf der Topologiekarte werden alle Pfade zwischen den beiden ausgewählten Knoten dargestellt. Sie können die Hop-by-Hop-Topologie der Routen zwischen zwei Knoten in der Topologiekarte grafisch darstellen. So wissen Sie genau, wie viele Routen zwischen den beiden Knoten vorhanden sind und welche Pfade die Datenpakete verwenden. Leistungsengpässe im Netzwerk werden rot dargestellt. Um eine fehlerhafte Netzwerkverbindung bzw. ein fehlerhaftes Netzwerkgerät zu ermitteln, untersuchen Sie in der Topologiekarte die rot gekennzeichneten Elemente. 

 ![Topologiedashboard mit Topologiekarte](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Sie können die Paketverluste, Latenz und Anzahl der Hops in jedem Pfad im Bereich **Aktion** überprüfen. Verwenden Sie die Scrollleiste, um die Details der fehlerhaften Pfade anzuzeigen. Verwenden Sie die Filter, um die Pfade mit dem fehlerhaften Hop auszuwählen, sodass die Topologie nur für die ausgewählten Pfade dargestellt wird. Mit dem Mausrad können Sie die Topologiekarte vergrößern oder verkleinern. 

In der folgenden Abbildung werden die Grundursachen für die Problembereiche des jeweiligen Abschnitts des Netzwerks in den roten Pfaden und Hops angezeigt. Wählen Sie einen Knoten in der Topologiekarte aus, um die Eigenschaften des Knotens einschließlich FQDN und IP-Adresse anzuzeigen. Durch Auswählen eines Hops wird die IP-Adresse des Hops angezeigt. 
 
![Topologiekarte mit ausgewählten Knoteneigenschaften](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Nächste Schritte
Mit [Protokollsuchen](../log-query/log-query-overview.md) können Sie Detaildatensätze mit Netzwerkleistungsdaten anzeigen.
