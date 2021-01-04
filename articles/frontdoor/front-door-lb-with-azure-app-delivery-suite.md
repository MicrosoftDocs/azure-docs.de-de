---
title: Azure Front Door – Lastenausgleich mit der Azure-Suite für die Anwendungsbereitstellung | Microsoft-Dokumentation
description: In diesem Artikel werden die Empfehlungen von Microsoft Azure für den Lastenausgleich mit der Azure-Suite für die Anwendungsbereitstellung erläutert.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 50e047325ad17710794b1640715ab1938373fe85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019462"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Lastenausgleich mit der Azure-Suite für die Anwendungsbereitstellung

## <a name="introduction"></a>Einführung
Microsoft Azure bietet zahlreiche globale und regionale Dienste für das Verwalten der Verteilung von Netzwerkdatenverkehr und Lastenausgleich: 

* Application Gateway
* Front Door 
* Load Balancer  
* Traffic Manager

In Verbindung mit den vielen Regionen und der zonenbasierten Architektur von Azure kann Ihnen die kombinierte Verwendung dieser Dienste die Erstellung stabiler, skalierbarer Hochleistungsanwendungen ermöglichen.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="Suite für die Anwendungsbereitstellung":::
 
Die Dienste werden in zwei Kategorien unterteilt:
1. **Globale Lastenausgleichsdienste** wie Traffic Manager und Azure Front Door verteilen den Datenverkehr von Endbenutzern auf Ihre regionalen Back-Ends, Clouds und sogar Ihre lokalen Hybriddienste. Der globale Lastenausgleich leitet den Datenverkehr zum nächstgelegenen Dienst-Back-End weiter und reagiert auf Änderungen der Dienstzuverlässigkeit, um immer Verfügbarkeit und Hochleistung für Ihre Benutzer zu gewährleisten. 
1. **Regionale Lastenausgleichsdienste** wie Load Balancer und Application Gateways bieten die Möglichkeit, Datenverkehr auf virtuelle Computer (VMs) in virtuellen Netzwerken (VNETs) oder Dienstendpunkte innerhalb einer Region zu verteilen.

Wenn Sie diese globalen und regionalen Dienste kombinieren, profitiert Ihre Anwendung von zuverlässigem, gesichertem End-to-End-Datenverkehr, der von Ihren Endbenutzern an Ihre IaaS-, PaaS- oder lokalen Dienste gesendet wird. Im nächsten Abschnitt wird jeder dieser Dienste beschrieben.

## <a name="global-load-balancing"></a>Globaler Lastenausgleich
**Traffic Manager** enthält einen globalen DNS-Lastenausgleich. Der Dienst untersucht eingehende DNS-Anforderungen und antwortet mit einem fehlerfreien Back-End. Dies geschieht gemäß der vom Kunden ausgewählten Routingrichtlinie. Es gibt die folgenden Routingmethoden:
- **Durch Leistungsrouting werden Anforderungen an das nächstgelegene Back-End mit der geringsten Latenz gesendet.
- **Prioritätsorientiertes Routing**, um den gesamten Datenverkehr zu einem Back-End zu leiten, wobei andere Back-Ends als Sicherung fungieren.
- **Gewichtetes Roundrobinrouting** verteilt Datenverkehr basierend auf der Gewichtung, die jedem Back-End zugewiesen ist.
- **Geografisches Routing** stellt sicher, dass Anforderungen, die aus bestimmten geografischen Regionen stammen, von für diese Regionen zugeordneten Back-Ends verarbeitet werden. (Beispielsweise sollten alle Anforderungen aus Spanien an die Azure-Region „Frankreich, Mitte“ weitergeleitet werden.)
- **Subnetzrouting** ermöglicht Ihnen das Zuordnen von IP-Adressbereichen zu Back-Ends, damit eingehende Anforderungen für diese IP-Adressen an das jeweilige Back-End gesendet werden. (Beispielsweise sollten alle Benutzer, die aus dem IP-Adressbereich Ihres Unternehmens eine Verbindung herstellen, andere Webinhalte erhalten als die allgemeinen Benutzer.)

Der Client stellt eine direkte Verbindung mit diesem Back-End her. Azure Traffic Manager erkennt, wenn ein Back-End fehlerhaft ist, und leitet die Clients dann an eine andere fehlerfreie Instanz weiter. Weitere Informationen zu diesem Dienst finden Sie in der Dokumentation zu [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).

**Azure Front Door** bietet eine Beschleunigung dynamischer Websites mit globalem HTTP-Lastenausgleich.  Der Dienst untersucht eingehende HTTP-Anforderungen und leitet sie zum nächstgelegenen Dienst-Back-End bzw. zur nächstgelegenen Region für den angegebenen Hostnamen, den URL-Pfad und die konfigurierten Regeln.  
Azure Front Door Service beendet HTTP-Anforderungen im Edgebereich des Microsoft-Netzwerks und führt aktiv Tests durch, um Änderungen der Anwendungs- und Infrastrukturintegrität oder der Wartezeit zu erkennen.  Anschließend leitet Azure Front Door Service den Datenverkehr immer zum schnellsten und verfügbaren (fehlerfreien) Back-End. Weitere Informationen zu diesem Dienst finden Sie in den Artikeln zur [Routingarchitektur](front-door-routing-architecture.md) und zu den [Routingmethoden für Datenverkehr](front-door-routing-methods.md).

## <a name="regional-load-balancing"></a>Regionaler Lastenausgleich
Application Gateway stellt einen ADC (Application Delivery Controller) als Dienst bereit und bietet damit verschiedene Layer-7-Lastenausgleichsfunktionen für Ihre Anwendung. So können Kunden die Produktivität von Webfarmen optimieren, indem die CPU-intensive TLS-Terminierung zum Application Gateway verlagert wird. Andere zusätzliche Routingfunktionen für Ebene 7 umfassen auch Roundrobin-Verteilung des eingehenden Datenverkehrs, cookiebasierte Sitzungsaffinität, Routing auf URL-Pfadbasis, und Möglichkeit zum Hosten von mehreren Websites hinter einem einzelnen Application Gateway. Application Gateway kann als Endpunkt mit Internetzugriff, rein interner Endpunkt oder als Kombination dieser beiden Optionen konfiguriert werden. Application Gateway wird vollständig von Azure verwaltet und bietet Skalierbarkeit und Hochverfügbarkeit. Die Anwendung umfasst viele Diagnose- und Protokollierungsfunktionen zur Verbesserung der Verwaltbarkeit.

Load Balancer ist ein integraler Bestandteil des Azure SDN-Stapels und bietet Ihnen leistungsfähige Lastenausgleichsdienste der Ebene 4 mit geringer Wartezeit für alle UDP- und TCP-Protokolle. Sie können öffentliche oder interne Endpunkte mit Lastenausgleich konfigurieren, indem Sie Regeln definieren, die eingehende Verbindungen Back-End-Pools zuordnen. Mit Überwachung von Integritätstests unter Verwendung von TCP oder HTTPS können Sie die Dienstverfügbarkeit einfacher verwalten.

## <a name="choosing-a-global-load-balancer"></a>Auswahl eines globalen Lastenausgleichs
Für den globalen Lastenausgleich und das globale Routing stehen Ihnen Traffic Manager und Azure Front Door Service zur Auswahl. Beachten Sie bei Ihrer Entscheidung die Ähnlichkeiten und Unterschiede zwischen den beiden Diensten.   Beide Dienste bieten:
- **Multi-Georedundanz:** Datenverkehr wird beim Ausfall einer Region nahtlos zur nächsten Region weitergeleitet. Es ist kein Eingreifen durch den Anwendungsbesitzer notwendig.
- **Routing zur nächstgelegenen Region**: Datenverkehr wird automatisch zur nächstgelegenen Region weitergeleitet.

</br>In der folgenden Tabelle werden die Unterschiede zwischen Traffic Manager und Azure Front Door beschrieben:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Alle Protokolle**: Da Traffic Manager auf der DNS-Ebene arbeitet, können Sie jede Art von Netzwerkdatenverkehr weiterleiten (TTP, TCP, UDP, usw.). | **HTTP-Beschleunigung**: Mit Azure Front Door wird Datenverkehr am Rand des Microsoft-Netzwerks über einen Proxy übermittelt. Bei HTTP/S-Anforderungen treten Latenz- und Durchsatzverbesserungen auf, wodurch die Latenz bei der TLS-Aushandlung reduziert wird.|
|**Lokales Routing:** Beim Routing auf einer DNS-Ebene wird Datenverkehr immer von Punkt zu Punkt übermittelt.  Für das Routing von Ihrer Filiale zu Ihrem lokalen Rechenzentrum kann ein direkter Pfad verwendet werden. Mit Traffic Manager ist dies selbst in Ihrem eigenen Netzwerk möglich. | **Unabhängige Skalierbarkeit**: Da Azure Front Door Service HTTP-Anforderungen verarbeitet, können Anforderungen an unterschiedliche URL-Pfade basierend auf Regeln und der Integrität der einzelnen Anwendungsmicroservices zu verschiedenen Back-End-/regionalen Dienstpools (Microservices) geleitet werden.|
|**Abrechnungsformat:** Die DNS-basierte Abrechnung passt sich Ihren Benutzern an und bleibt bei Diensten mit mehr Benutzern preislich konstant, um die Kosten bei höherer Nutzung zu senken. |**Inlinesicherheit**: Azure Front Door Service ermöglicht die Verwendung von Regeln (z.B. Ratenbegrenzung und Hinzufügen von IP-Adressen zu Zugriffssteuerungslisten), mit denen Sie Ihre Back-Ends schützen können, bevor Datenverkehr Ihre Anwendung erreicht. 

</br>Aufgrund der Vorteile, die Azure Front Door Service in Bezug auf die Leistung, Funktionsfähigkeit und Sicherheit für HTTP-Workloads bietet, empfehlen wir Kunden die Verwendung von Azure Front Door Service für ihre HTTP-Workloads. Traffic Manager und Azure Front Door Service können parallel verwendet werden, um den gesamten Datenverkehr für Ihre Anwendung zu verarbeiten. 

## <a name="building-with-azures-application-delivery-suite"></a>Erstellen von Anwendungen mit der Azure-Suite für die Anwendungsbereitstellung 
Alle Websites, APIs und Dienste sollten geografisch redundant sein und Datenverkehr an Benutzer nach Möglichkeit vom nächstgelegenen Standort übermitteln.  Wenn Sie mehrere Lastenausgleichsdienste kombinieren, können Sie geografische und zonale Redundanz erzielen, um Zuverlässigkeit und Leistung zu maximieren.

Die folgende Abbildung zeigt eine Beispielarchitektur, bei der eine Kombination all dieser Dienste verwendet wird, um einen globalen Webdienst zu erstellen. Der Architekt hat Traffic Manager zum Weiterleiten von Datenverkehr an globale Back-Ends für die Datei- und Objektübermittlung verwendet. Es wird zwar Front Door verwendet, zum globalen Weiterleiten von URL-Pfaden an den Dienst, die dem Muster „/store/*“ entsprechen, wurde jedoch eine Migration zu App Service ausgeführt. Schließlich werden alle anderen Anforderungen an regionale Application Gateways weitergeleitet.

Der Anwendungsentwickler hat in jeder Region des IaaS-Diensts entschieden, dass alle URLs, die dem Muster „/images/*“ entsprechen, von einem dedizierten Pool von VMs aus bereitgestellt werden. Dieser Pool von VMs unterscheidet sich von dem Rest der Webfarm.

Außerdem muss der standardmäßige VM-Pool, der den dynamischen Inhalt bereitstellt, mit einer Back-End-Datenbank kommunizieren, die auf einem Cluster mit hoher Verfügbarkeit gehostet wird. Die gesamte Bereitstellung wird über Azure Resource Manager konfiguriert.

Das folgende Diagramm zeigt die Architektur dieses Szenarios:

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="Detaillierte Architektur der Suite für die Anwendungsbereitstellung":::

> [!NOTE]
> Dieses Beispiel zeigt nur eine von vielen möglichen Konfigurationen, die Azure für die Lastenausgleichsdienste bietet. Traffic Manager, Azure Front Door Service, Application Gateway und Load Balancer können kombiniert werden, um alle Ihre Anforderungen an den Lastenausgleich optimal zu erfüllen. Wenn beispielsweise eine TLS/SSL-Abladung oder eine Verarbeitung auf Layer 7 nicht notwendig ist, kann Load Balancer anstatt Application Gateway verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
