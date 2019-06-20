---
title: Azure Front Door Service – Lastenausgleich mit der Azure-Suite für die Anwendungsbereitstellung | Microsoft-Dokumentation
description: In diesem Artikel werden die Empfehlungen von Microsoft Azure für den Lastenausgleich mit der Azure-Suite für die Anwendungsbereitstellung erläutert.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 3d5c0ac068a6644f3499da6c3b642a4a04408370
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736495"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Lastenausgleich mit der Azure-Suite für die Anwendungsbereitstellung

## <a name="introduction"></a>Einführung
Microsoft Azure bietet zahlreiche globale und regionale Dienste für das Verwalten der Verteilung von Netzwerkdatenverkehr und Lastenausgleich: Traffic Manager, Front Door Service, Application Gateway und Load Balancer.  In Verbindung mit den vielen Regionen und der zonenbasierten Architektur von Azure ermöglicht Ihnen die kombinierte Verwendung dieser Dienste die Erstellung stabiler, skalierbarer Hochleistungsanwendungen.

![Suite für die Anwendungsbereitstellung ][1]
 
Die Dienste werden in zwei Kategorien unterteilt:
1. **Globale Lastenausgleichsdienste** wie Traffic Manager und Azure Front Door Service verteilen den Datenverkehr von den Endbenutzern auf Ihre regionalen Back-Ends, Clouds oder sogar Ihre lokalen Hybriddienste. Der globale Lastenausgleich leitet den Datenverkehr zum nächstgelegenen Dienst-Back-End und reagiert auf Änderungen der Zuverlässigkeit oder Leistung des Diensts, um immer die maximale Leistung für Ihre Benutzer zu gewährleisten. 
2. **Regionale Lastenausgleichsdienste** wie Load Balancer Standard und Application Gateway bieten die Möglichkeit, Datenverkehr in virtuellen Netzwerken (VNETs) auf Ihre VMs oder zonenbasierten Dienstendpunkte innerhalb einer Region zu verteilen.

Indem Sie globale und regionale Dienste in Ihrer Anwendung kombinieren, erhalten Sie eine zuverlässige, leistungsstarke und sichere End-to-End-Lösung für die Weiterleitung von Datenverkehr zwischen Ihren Benutzern und Ihren IaaS-, PaaS- oder lokalen Diensten. Im nächsten Abschnitt wird jeder dieser Dienste beschrieben.

## <a name="global-load-balancing"></a>Globaler Lastenausgleich
**Traffic Manager** enthält einen globalen DNS-Lastenausgleich. Der Dienst untersucht eingehende DNS-Anforderungen und antwortet mit einem fehlerfreien Back-End. Dies geschieht in Übereinstimmung mit der vom Kunden ausgewählten Routingrichtlinie. Es gibt die folgenden Routingmethoden:
- Leistungsorientiertes Routing, um den Anforderer im Hinblick auf die Wartezeit zum nächstgelegenen Back-End zu verweisen
- Prioritätsorientiertes Routing, um den gesamten Datenverkehr zu einem Back-End zu leiten, wobei andere Back-Ends als Sicherung fungieren
- Gewichtetes Roundrobinrouting, das den Datenverkehr basierend auf der Gewichtung verteilt, die jedem Back-End zugewiesen ist
- Geografisches Routing, um sicherzustellen, dass Anforderer in bestimmten geografischen Regionen zu den Back-Ends geleitet werden, die diesen Regionen zugeordnet sind (z.B. sollten alle Anforderungen aus Spanien in die Azure-Region „Frankreich, Mitte“ geleitet werden)
- Subnetzrouting, das Ihnen die Zuordnung von IP-Adressbereichen zu Back-Ends ermöglicht, damit Anforderungen von diesen Adressen zum angegebenen Back-End gesendet werden (z. B. sollten alle Benutzer, die über den IP-Adressbereich Ihrer Unternehmenszentrale eine Verbindung herstellen, andere Webinhalte erhalten als die allgemeinen Benutzer)

Der Client stellt eine direkte Verbindung mit diesem Back-End her. Azure Traffic Manager erkennt, wenn ein Back-End fehlerhaft ist, und leitet die Clients dann an eine andere fehlerfreie Instanz weiter. Weitere Informationen zu diesem Dienst finden Sie in der Dokumentation zu [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).

**Azure Front Door Service** bietet eine Beschleunigung dynamischer Websites mit globalem HTTP-Lastenausgleich.  Der Dienst untersucht eingehende HTTP-Anforderungen und leitet sie zum nächstgelegenen Dienst-Back-End bzw. zur nächstgelegenen Region für den angegebenen Hostnamen, den URL-Pfad und die konfigurierten Regeln.  
Azure Front Door Service beendet HTTP-Anforderungen im Edgebereich des Microsoft-Netzwerks und führt aktiv Tests durch, um Änderungen der Anwendungs- und Infrastrukturintegrität oder der Wartezeit zu erkennen.  Anschließend leitet Azure Front Door Service den Datenverkehr immer zum schnellsten und verfügbaren (fehlerfreien) Back-End. Weitere Informationen zu diesem Dienst finden Sie in den Artikeln zur [Routingarchitektur](front-door-routing-architecture.md) und zu den [Routingmethoden für Datenverkehr](front-door-routing-methods.md).

## <a name="regional-load-balancing"></a>Regionaler Lastenausgleich
Application Gateway stellt einen ADC (Application Delivery Controller) als Dienst bereit und bietet damit verschiedene Layer-7-Lastenausgleichsfunktionen für Ihre Anwendung. So wird Kunden die Optimierung der Webfarmproduktivität ermöglicht, indem die CPU-intensive SSL-Beendigung zum Application Gateway verlagert wird. Andere Routingfunktionen für Ebene 7 umfassen Roundrobin-Verteilung des eingehenden Datenverkehrs, cookiebasierte Sitzungsaffinität, Routing auf URL-Pfadbasis, und Möglichkeit zum Hosten von mehreren Websites hinter einem einzelnen Application Gateway. Application Gateway kann als Gateway mit Internetanbindung, rein internes Gateway oder als Kombination dieser beiden Optionen konfiguriert werden. Application Gateway wird vollständig über Azure verwaltet und ist skalierbar und hoch verfügbar. Die Anwendung umfasst viele Diagnose- und Protokollierungsfunktionen zur Verbesserung der Verwaltbarkeit.
Load Balancer ist ein integraler Bestandteil des Azure SDN-Stapels und bietet leistungsfähige Layer-4-Lastenausgleichsdienste mit geringer Wartezeit für alle UDP- und TCP-Protokolle. Der Dienst verwaltet eingehende und ausgehende Verbindungen. Sie können öffentliche und interne Lastenausgleichs-Endpunkte konfigurieren und Regeln definieren, um eingehende Verbindungen zu Back-End-Pool-Zielen mit TCP und HTTP Integritätstest-Optionen zuzuordnen, damit die Dienstverfügbarkeit verwaltet werden kann.


## <a name="choosing-a-global-load-balancer"></a>Auswahl eines globalen Lastenausgleichs
Für den globalen Lastenausgleich und das globale Routing stehen Ihnen Traffic Manager und Azure Front Door Service zur Auswahl. Beachten Sie bei Ihrer Entscheidung die Ähnlichkeiten und Unterschiede zwischen den beiden Diensten.   Beide Dienste bieten:
- **Multi-Georedundanz:** Datenverkehr wird beim Ausfall einer Region nahtlos zur nächsten passenden Region weitergeleitet. Es ist kein Eingreifen durch den Anwendungsbesitzer notwendig.
- **Routing zur nächstgelegenen Region**: Datenverkehr wird automatisch zur nächstgelegenen Region weitergeleitet.

</br>In der folgenden Tabelle werden die Unterschiede zwischen Traffic Manager und Azure Front Door Service beschrieben:</br>

| Traffic Manager | Azure Front Door Service |
| --------------- | ------------------------ |
|**Alle Protokolle**: Da Traffic Manager auf der DNS-Ebene arbeitet, können Sie jede Art von Netzwerkdatenverkehr weiterleiten (TTP, TCP, UDP, usw.). | **HTTP-Beschleunigung**: Mit Azure Front Door Service wird Datenverkehr am Rand des Microsoft-Netzwerks über einen Proxy übermittelt.  Da die Wartezeit für die SSL-Aushandlung reduziert wird und „heiße“ Verbindungen zwischen Azure Front Door Service und Ihrer Anwendung verwendet werden, führt dies zu einer Verbesserung der Wartezeit und des Durchsatzes für HTTP(S)-Anforderungen.|
|**Lokales Routing:** Beim Routing auf einer DNS-Ebene wird Datenverkehr immer von Punkt zu Punkt übermittelt.  Für das Routing von Ihrer Filiale zu Ihrem lokalen Rechenzentrum kann ein direkter Pfad verwendet werden. Mit Traffic Manager ist dies selbst in Ihrem eigenen Netzwerk möglich. | **Unabhängige Skalierbarkeit**: Da Azure Front Door Service HTTP-Anforderungen verarbeitet, können Anforderungen an unterschiedliche URL-Pfade basierend auf Regeln und der Integrität der einzelnen Anwendungsmicroservices zu verschiedenen Back-End-/regionalen Dienstpools (Microservices) geleitet werden.|
|**Abrechnungsformat:** Die DNS-basierte Abrechnung passt sich Ihren Benutzern an und bleibt bei Diensten mit mehr Benutzern preislich konstant, um die Kosten bei höherer Nutzung zu senken. |**Inlinesicherheit**: Azure Front Door Service ermöglicht die Verwendung von Regeln (z.B. Ratenbegrenzung und Hinzufügen von IP-Adressen zu Zugriffssteuerungslisten), mit denen Sie Ihre Back-Ends schützen können, bevor Datenverkehr Ihre Anwendung erreicht. 

</br>Aufgrund der Vorteile, die Azure Front Door Service in Bezug auf die Leistung, Funktionsfähigkeit und Sicherheit für HTTP-Workloads bietet, empfehlen wir Kunden die Verwendung von Azure Front Door Service für ihre HTTP-Workloads.    Traffic Manager und Azure Front Door Service können parallel verwendet werden, um den gesamten Datenverkehr für Ihre Anwendung zu verarbeiten. 

## <a name="building-with-azures-application-delivery-suite"></a>Erstellen von Anwendungen mit der Azure-Suite für die Anwendungsbereitstellung 
Alle Websites, APIs und Dienste sollten geografisch redundant sein und Datenverkehr an die Benutzer nach Möglichkeit vom nächstgelegenen (geringste Wartezeit) Standort übermitteln.  Durch Kombinieren der Dienste von Traffic Manager, Azure Front Door Service, Application Gateway und Load Balancer können Sie geografisch und zonal redundante Anwendungen erstellen und so die Zuverlässigkeit, Skalierbarkeit und Leistung maximieren.

Das folgende Diagramm zeigt ein Beispiel, bei dem eine Kombination all dieser Dienste verwendet wird, um einen globalen Webdienst zu erstellen.   In diesem Fall hat sich der Architekt für folgende Lösung entschieden: Dateien und Objekte werden mithilfe von Traffic Manager zu globalen Back-Ends geleitet. Azure Front Door Service wird dagegen verwendet, um URL-Pfade, die dem Muster „/store/*“ entsprechen, global zu dem zu App Service migrierten Dienst zu leiten und alle anderen Anforderungen zu regionalen Application Gateway-Instanzen weiterzuleiten.

Der Anwendungsentwickler hat sich entschieden, innerhalb der Region für den IaaS-Dienst alle URLs, die dem Muster „/images/*“ entsprechen, über einen dedizierten Pool von VMs zu verarbeiten, die sich vom Rest der Webfarm unterscheiden.

Außerdem muss der standardmäßige VM-Pool, der den dynamischen Inhalt bereitstellt, mit einer Back-End-Datenbank kommunizieren, die auf einem Cluster mit hoher Verfügbarkeit gehostet wird. Die gesamte Bereitstellung wird mit Azure Resource Manager eingerichtet.

Das folgende Diagramm zeigt die Architektur dieses Szenarios:

![Detaillierte Architektur der Suite für die Anwendungsbereitstellung][2] 

> [!NOTE]
> Dieses Beispiel zeigt nur eine von vielen möglichen Konfigurationen, die Azure für die Lastenausgleichsdienste bietet. Traffic Manager, Azure Front Door Service, Application Gateway und Load Balancer können kombiniert werden, um alle Ihre Anforderungen an den Lastenausgleich optimal zu erfüllen. Wenn beispielsweise die SSL-Auslagerung oder Verarbeitung der Ebene 7 nicht notwendig ist, kann Load Balancer anstatt Application Gateway verwendet werden.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
