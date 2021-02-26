---
title: Regionsübergreifender Lastenausgleich (Vorschau)
titleSuffix: Azure Load Balancer
description: Übersicht über die regionsübergreifende Lastenausgleichsebene für Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: 89bf920a5a5dd833425f1b41bd206beaae9d30fd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946265"
---
# <a name="cross-region-load-balancer-preview"></a>Regionsübergreifender Lastenausgleich (Vorschau)

Azure Load Balancer verteilt Datenflüsse, die beim Lastenausgleichs-Front-End eingehen, auf Instanzen des Back-End-Pools.

Azure Load Balancer Standard unterstützt regionsübergreifenden Lastenausgleich, wodurch georedundante Hochverfügbarkeitsszenarien ermöglicht werden, beispielsweise:

* Eingehender Datenverkehr aus mehreren Regionen.
* [Sofortiges globales Failover](#regional-redundancy) zur nächsten optimalen regionalen Bereitstellung.
* Regionsübergreifende Lastverteilung in die nächstgelegene Azure-Region mit [extrem geringer Latenz](#ultra-low-latency).
* Möglichkeit, hinter einem einzelnen Endpunkt [zentral hoch- oder herunterzuskalieren](#ability-to-scale-updown-behind-a-single-endpoint).
* [Statische IP](#static-ip)
* [Client-IP-Beibehaltung](#client-ip-preservation)
* [Aufbauen auf vorhandener Lastenausgleichslösung](#build-cross-region-solution-on-existing-azure-load-balancer) ohne Lernkurve

> [!IMPORTANT]
> Der regionsübergreifende Lastenausgleich befindet sich derzeit in der Vorschauphase und kann im Portal bereitgestellt werden. Melden Sie sich bei **https://preview.portal.azure.com** an, um das Feature anzuzeigen und bereitzustellen. </br> </br>
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Der regionsübergreifende Lastenausgleich bietet die gleichen Vorteile wie Hochleistung und niedrige Latenz wie der regionale Standardlastenausgleich. 

Die Front-End-IP-Konfiguration Ihres regionsübergreifenden Lastenausgleichs ist statisch und wird in den [meisten Azure-Regionen](#participating-regions) angekündigt.

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Abbildung des regionsübergreifenden Lastenausgleichs." border="true":::

> [!NOTE]
> Der Back-End-Port Ihrer Lastenausgleichsregel für den regionsübergreifenden Lastenausgleich muss mit dem Front-End-Port der Lastenausgleichsregel/NAT-Regel für eingehenden Datenverkehr für den regionalen Standardlastenausgleich identisch sein. 

### <a name="regional-redundancy"></a>Regionale Redundanz

Konfigurieren Sie die regionale Redundanz durch Hinzufügen einer globalen öffentlichen Front-End-IP-Adresse zu Ihren vorhandenen Lastenausgleichsmodulen. 

Wenn eine Region ausfällt, wird der Datenverkehr an den nächstgelegenen fehlerfreien Lastenausgleich weitergeleitet.  

Der Integritätstest des regionsübergreifenden Lastenausgleichs erfasst alle 20 Sekunden Informationen zur Verfügbarkeit. Wenn die Verfügbarkeit eines regionalen Lastenausgleichs auf 0 sinkt, erkennt der regionsübergreifende Lastenausgleich den Fehler. Der regionale Lastenausgleich wird dann von der Rotation ausgenommen. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="Abbildung der Ansicht des globalen Regionsdatenverkehrs." border="true":::

### <a name="ultra-low-latency"></a>Extrem geringe Latenz

Der Lastenausgleichsalgorithmus für geografische Nähe basiert auf dem geografischen Standort Ihrer Benutzer und Ihren regionalen Bereitstellungen. 

Der von einem Client gestartete Datenverkehr erreicht die nächstgelegene Region und durchläuft den globalen Microsoft-Netzwerkbackbone, um die nächstgelegene regionale Bereitstellung zu erreichen. 

Beispielsweise verfügen Sie über einen regionsübergreifenden Lastenausgleich mit Standard-Lastenausgleichsmodule in Azure-Regionen:

* USA (Westen)
* Nordeuropa

Wenn Datenverkehr in Seattle gestartet wird, durchläuft er die Region „USA, Westen“. Diese Region ist die Seattle nächstgelegene teilnehmende Region. Der Datenverkehr wird an den nächstgelegenen Regionslastenausgleich weitergeleitet, d. h. an „USA, Westen“.

Der regionsübergreifende Azure-Lastenausgleich verwendet den Lastenausgleichsalgorithmus für geografische Nähe für die Routingentscheidung. 

Der konfigurierte Lastenverteilungsmodus des regionalen Lastenausgleichsmoduls wird verwendet, um die endgültige Routingentscheidung zu treffen, wenn mehrere regionale Lastenausgleichsmodule für geografische Nähe verwendet werden.

Weitere Informationen finden Sie unter [Konfigurieren des Verteilungsmodus für Azure Load Balancer](./load-balancer-distribution-mode.md).


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Möglichkeit, hinter einem einzelnen Endpunkt zentral hoch- oder herunterzuskalieren

Wenn Sie den globalen Endpunkt eines regionsübergreifenden Lastenausgleichs für Kunden verfügbar machen, können Sie regionale Bereitstellungen hinter dem globalen Endpunkt ohne Kundenbeeinträchtigung hinzufügen oder entfernen. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>Statische IP
Der regionsübergreifende Lastenausgleich verfügt über eine statische öffentliche IP-Adresse, wodurch sichergestellt wird, dass die IP-Adresse unverändert bleibt. Weitere Informationen zu statischen IP-Adressen finden Sie [hier](../virtual-network/public-ip-addresses.md#allocation-method).

### <a name="client-ip-preservation"></a>Client-IP-Beibehaltung
Der regionsübergreifende Lastenausgleich ist ein Layer-4-Passthrough-Netzwerklastenausgleich. Dieses Passthrough-Verfahren behält die ursprüngliche IP-Adresse des Pakets bei.  Die ursprüngliche IP-Adresse ist für den Code verfügbar, der auf dem virtuellen Computer ausgeführt wird. Diese Beibehaltung ermöglicht Ihnen das Anwenden von Programmlogik, die für eine IP-Adresse spezifisch ist.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Erstellen einer regionsübergreifenden Lösung für einen vorhandenem Azure Load Balancer
Der Back-End-Pool des regionsübergreifenden Lastenausgleichs enthält mindestens einen regionalen Lastenausgleich. 

Fügen Sie Ihre vorhandenen Lastenausgleichsbereitstellungen einem regionsübergreifenden Lastenausgleich für eine hochverfügbare, regionsübergreifende Bereitstellung hinzu.

In der **Startregion** wird der regionsübergreifende Lastenausgleich bereitgestellt. Diese Region wirkt sich nicht darauf aus, wie der Datenverkehr weitergeleitet wird. Wenn eine Startregion ausfällt, wirkt sich dies nicht auf den Fluss des Datenverkehrs aus.

### <a name="home-regions"></a>Startregionen
* USA (Ost) 2
* USA (Westen)
* Europa, Westen
* Asien, Südosten
* USA (Mitte)
* Nordeuropa
* Asien, Osten

> [!NOTE]
> Sie können Ihren regionsübergreifenden Lastenausgleich nur in einer der acht oben aufgeführten Regionen bereitstellen.

In einer **teilnehmenden Region** ist die globale öffentliche IP-Adresse des Lastenausgleichs verfügbar. 

Der vom Benutzer gestartete Datenverkehr wird über das Microsoft-Kernnetzwerk in die nächstgelegene teilnehmende Region übertragen. 

Der regionsübergreifende Lastenausgleich leitet den Datenverkehr an den entsprechenden regionalen Lastenausgleich weiter.

### <a name="participating-regions"></a>Teilnehmende Regionen
* East US 
* Europa, Westen 
* USA (Mitte) 
* USA (Ost) 2 
* USA (Westen) 
* Nordeuropa 
* USA Süd Mitte 
* USA, Westen 2 
* UK, Süden 
* Asien, Südosten 
* USA Nord Mitte 
* Japan, Osten 
* Asien, Osten 
* USA, Westen-Mitte 
* Australien, Südosten 
* Australien (Osten) 
* Indien, Mitte 

## <a name="limitations"></a>Einschränkungen

* Regionsübergreifende Front-End-IP-Konfigurationen sind nur öffentlich. Ein internes Front-End wird derzeit nicht unterstützt.

* Ein privater oder interner Lastenausgleich kann nicht dem Back-End-Pool des regionsübergreifenden Lastenausgleich hinzugefügt werden. 

* Regionsübergreifende IPv6-Front-End-IP-Konfigurationen werden nicht unterstützt. 

* Zurzeit kann kein Integritätstest konfiguriert werden. Bei einem standardmäßigen Integritätstest werden alle 20 Sekunden automatisch Verfügbarkeitsinformationen zum regionalen Lastenausgleich abgerufen. 

* Azure Kubernetes Service (AKS) kann derzeit nicht in regionsübergreifende Load Balancer integriert werden. Beim Einrichten eines regionsübergreifenden Load Balancers vor einem öffentlichen Load Balancer, der mit AKS bereitgestellt wurde, ist der Verlust der Verbindung zu erwarten.

## <a name="pricing-and-sla"></a>Preise und SLA
Regionsübergreifender Lastenausgleich, verwendet die [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) des Standardlastenausgleichs.

 
## <a name="next-steps"></a>Nächste Schritte

- Siehe [Tutorial: Erstellen eines regionsübergreifenden Lastenausgleichs im Azure-Portal](tutorial-cross-region-portal.md), um einen regionsübergreifenden Lastenausgleich zu erstellen.
- Informationen zum Erstellen einen standardmäßigen regionsbezogenen Lastenausgleichs finden Sie unter [Erstellen eines öffentlichen Standardlastenausgleichs](quickstart-load-balancer-standard-public-portal.md).
- Weitere Informationen zu [Azure Load Balancer](load-balancer-overview.md).
