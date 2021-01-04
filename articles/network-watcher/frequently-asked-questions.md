---
title: Häufig gestellte Fragen zu Azure Network Watcher | Microsoft-Dokumentation
description: In diesem Artikel werden häufig gestellte Fragen zum Dienst Azure Network Watcher beantwortet.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: 959062d493d9eb47204be2488f216b70804b3605
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965764"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Häufig gestellte Fragen zu Azure Network Watcher
Dier Dienst [Azure Network Watcher](./network-watcher-monitoring-overview.md) stellt eine Sammlung von Tools für die Überwachung, Diagnose, Metrikanzeige sowie zur Aktivierung oder Deaktivierung von Protokollen für Ressourcen in einem virtuellen Azure-Netzwerk bereit. In diesem Artikel werden häufig gestellte Fragen zum Dienst beantwortet.

## <a name="general"></a>Allgemein

### <a name="what-is-network-watcher"></a>Was ist Network Watcher?
Network Watcher ist für die Überwachung und Wiederherstellung der Netzwerkintegrität von IaaS-Komponenten (Infrastructure-as-a-Service) konzipiert. Dazu zählen virtuelle Computer, virtuelle Netzwerke, Application Gateway-Instanzen, Lastenausgleichsmodule und andere Ressourcen in einem virtuellen Azure-Netzwerk. Dabei handelt es sich nicht um eine Lösung für die Überwachung der PaaS-Infrastruktur (Platform-as-a-Service) oder zum Abrufen von Analysen zu Web- oder Mobiltechnologien.

### <a name="what-tools-does-network-watcher-provide"></a>Welche Tools bietet Network Watcher?
Network Watcher bietet drei Hauptgruppen von Funktionen.
* Überwachung
  * In der [Topologieansicht](./view-network-topology.md) sehen Sie die Ressourcen in Ihrem virtuellen Netzwerk und die Beziehungen zwischen ihnen.
  * Im [Verbindungsmonitor](./connection-monitor.md) können Sie die Konnektivität und Wartezeit zwischen einer VM und einer anderen Netzwerkressource überwachen.
  * Der [Netzwerkleistungsmonitor](../azure-monitor/insights/network-performance-monitor.md) ermöglicht Ihnen, Konnektivität und Wartezeiten in hybriden Netzwerkarchitekturen, bei ExpressRoute-Leitungen und von Dienst- und Anwendungsendpunkten zu überwachen.  
* Diagnose
  * Die Funktion [Überprüfen des IP-Flusses](./network-watcher-ip-flow-verify-overview.md) ermöglicht Ihnen das Erkennen von Problemen beim Filtern von Datenverkehr auf VM-Ebene.
  * [Nächster Hop ](./network-watcher-next-hop-overview.md) unterstützt Sie beim Überprüfen von Datenverkehrsrouten und Erkennen von Routingproblemen.
  * Die Funktion [Problembehandlung für Verbindung](./network-watcher-connectivity-portal.md) bietet eine einmalige Prüfung der Konnektivität und Wartezeit zwischen einer VM und einer anderen Netzwerkressource.
  * Die [Paketerfassung](./network-watcher-packet-capture-overview.md) ermöglicht das Erfassen des gesamten Datenverkehrs auf einer VM in Ihrem virtuellen Netzwerk.
  * Die [VPN-Problembehandlung ](./network-watcher-troubleshoot-overview.md) führt mehrere Diagnoseprüfungen für VPN-Gateways und-Verbindungen aus, um beim Beheben von Problemen zu helfen.
* Protokollierung
  * [NSG-Flussprotokolle](./network-watcher-nsg-flow-logging-overview.md) ermöglichen Ihnen das Protokollieren des gesamten Datenverkehrs in Ihren [Netzwerksicherheitsgruppen (NSGs)](../virtual-network/network-security-groups-overview.md).
  * [Traffic Analytics](./traffic-analytics.md) verarbeitet Ihre NSG-Flowprotokolldaten, sodass Sie Ihren Netzwerkdatenverkehr visualisieren, abfragen, analysieren und verstehen können.


Weitere Informationen finden Sie auf der [Seite mit der Übersicht über Network Watcher](./network-watcher-monitoring-overview.md).


### <a name="how-does-network-watcher-pricing-work"></a>Welche Preise gelten für Network Watcher?
Besuchen Sie die Seite [Preise](https://azure.microsoft.com/pricing/details/network-watcher/), auf der Sie die Network Watcher-Komponenten und ihre Preise finden.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>In welchen Regionen ist Network Watcher verfügbar und wird unterstützt?
Sie können die aktuelle regionale Verfügbarkeit auf der Azure-Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) anzeigen.

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Welche Berechtigungen sind erforderlich, um Network Watcher zu verwenden?
Weitere Informationen finden Sie in der Liste [Erforderliche Azure-RBAC-Berechtigungen (Role-Based Access Control) für die Verwendung von Network Watcher-Funktionen](./required-rbac-permissions.md). Für die Bereitstellung von Ressourcen benötigen Sie „Mitwirkender“-Berechtigungen für NetworkWatcherRG (siehe unten).

### <a name="how-do-i-enable-network-watcher"></a>Wie kann ich Network Watcher aktivieren?
Der Network Watcher-Dienst wird [automatisch](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) für jedes Abonnement aktiviert.

### <a name="what-is-the-network-watcher-deployment-model"></a>Was ist das Bereitstellungsmodell von Network Watcher?
Die übergeordnete Network Watcher-Ressource wird mit einer eindeutigen Instanz in den einzelnen Regionen bereitgestellt. Namensformat: NetworkWatcher_RegionName. Beispiel: „NetworkWatcher_centralus“ ist die Network Watcher-Ressource für die Region „USA, Mitte“.

### <a name="what-is-the-networkwatcherrg"></a>Was ist NetworkWatcherRG?
Network Watcher-Ressourcen befinden sich in der ausgeblendeten **NetworkWatcherRG**-Ressourcengruppe, die automatisch erstellt wird. Die „NSG-Flussprotokolle“-Ressource ist z. B. eine untergeordnete Ressource von Network Watcher und wird in der NetworkWatcherRG aktiviert.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Warum muss ich die Network Watcher-Erweiterung installieren? 
Die Network Watcher-Erweiterung ist für alle Features erforderlich, die Datenverkehr von einer VM generieren oder abfangen müssen. 

### <a name="which-features-require-the-network-watcher-extension"></a>Welche Features erfordert die Network Watcher-Erweiterung?
Die Network Watcher-Erweiterung wird für die Features „Paketerfassung“, „Verbindungsproblembehandlung“ und „Verbindungsmonitor“ benötigt.

### <a name="what-are-resource-limits-on-network-watcher"></a>Welche Ressourcengrenzwerte gelten für Network Watcher?
Alle Grenzwerte finden Sie unter [Network Watcher-Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md#network-watcher-limits).  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Warum ist pro Region nur eine einzelne Network Watcher-Instanz zulässig? 
Network Watcher muss lediglich einmal für ein Abonnement aktiviert werden, damit die zugehörigen Features funktionieren. Hierbei handelt es sich nicht um eine Diensteinschränkung.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Wie kann ich die Network Watcher-Ressource verwalten? 
Die Network Watcher-Ressource stellt den Back-End-Dienst für Network Watcher dar und wird vollständig von Azure verwaltet. Kunden müssen sie nicht verwalten. Vorgänge wie Verschieben werden für die Ressource nicht unterstützt. Jedoch [kann die Ressource gelöscht werden](./network-watcher-create.md#delete-a-network-watcher-in-the-portal). 

## <a name="service-availability-and-redundancy"></a>Dienstverfügbarkeit und Redundanz 

### <a name="is-the-network-watcher-service-zone-resilient"></a>Ist die Dienstzone von Network Watcher resilient? 
Ja. Der Network Watcher-Dienst ist standardmäßig zonenresilient. 

### <a name="how-do-i-configure-the-network-watcher-service-to-be-zone-resilient"></a>Wie konfiguriere ich den Network Watcher-Dienst so, dass er zonenresilient ist? 
Es ist keine Kundenkonfiguration erforderlich, um Zonenresilienz zu ermöglichen. Zonenresilienz für Network Watcher-Ressourcen ist standardmäßig verfügbar und wird vom Dienst selbst verwaltet. 

## <a name="nsg-flow-logs"></a>NSG-Flussprotokolle

### <a name="what-does-nsg-flow-logs-do"></a>Welche Aufgabe haben NSG-Flussprotokolle?
Azure-Netzwerkressourcen können mithilfe von [Netzwerksicherheitsgruppen (NSGs)](../virtual-network/network-security-groups-overview.md) kombiniert und verwaltet werden. NSG-Flussprotokolle ermöglichen Ihnen, Flussinformationen mit 5 Tupeln zum gesamten Datenverkehr durch Ihre NSGs zu protokollieren. Die unformatierten Flowprotokolle werden in ein Azure Storage-Konto geschrieben, in dem Sie nach Bedarf weiterverarbeitet, analysiert, abgefragt oder exportiert werden können.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Wie verwende ich NSG-Flussprotokolle mit einem Speicherkonto hinter einer Firewall?

Wenn Sie ein Speicherkonto hinter einer Firewall verwenden möchten, müssen Sie eine Ausnahme für vertrauenswürdige Microsoft-Dienste für den Zugriff auf Ihr Speicherkonto bereitstellen:

* Navigieren Sie zum Speicherkonto, indem Sie im Portal oder auf der Seite [Speicherkonten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) den Namen des Speicherkontos in das Feld für die globale Suche eingeben.
* Wählen Sie unter **EINSTELLUNGEN** die Option **Firewalls und virtuelle Netzwerke**.
* Wählen Sie unter „Zugriff erlauben von“ die Option **Ausgewählte Netzwerke** aus. Aktivieren Sie anschließend unter **Ausnahmen** das Kontrollkästchen neben **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**. 
* Falls die Option bereits ausgewählt ist, ist keine Änderung erforderlich.  
* Suchen Sie auf der Seite [NSG-Flussprotokolle: Übersicht](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) nach ihrer Ziel-NSG, und aktivieren Sie NSG-Flussprotokolle mit dem oben ausgewählten Speicherkonto.

Sie können nach einigen Minuten die Speicherprotokolle überprüfen, um zu ermitteln, ob ein aktualisierter Zeitstempel vorhanden ist oder eine neue JSON-Datei erstellt wurde.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Wie verwende ich NSG-Flussprotokolle mit einem Speicherkonto hinter einem Dienstendpunkt?

NSG-Flussprotokolle sind ohne weitere Konfiguration mit Dienstendpunkten kompatibel. Weitere Informationen finden Sie im [Tutorial zum Aktivieren von Dienstendpunkten](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) in Ihrem virtuellen Netzwerk.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Worin besteht der Unterschied bei den Flowprotokollen der Version 1 und 2?
In Flowprotokolle der Version 2 wurde des Konzept des *Flowzustands* eingeführt, gemäß dem Informationen zu Bytes und Paketen gespeichert werden. [Weitere Informationen](./network-watcher-nsg-flow-logging-overview.md#log-format).

## <a name="next-steps"></a>Nächste Schritte
 - Besuchen Sie unsere [Seite mit der Übersicht über die Dokumentation](./index.yml), auf der Sie einige Tutorials finden, die Ihnen den Einstieg in Network Watcher erleichtern.