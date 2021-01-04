---
title: Azure Load Balancer-Komponenten
description: Übersicht über die Azure Load Balancer-Komponenten
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2020
ms.author: allensu
ms.openlocfilehash: bf7a35e8cedbe62aafb29aa6d9dc8fcb42e90b2e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693765"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer-Komponenten

Azure Load Balancer umfasst ein paar Hauptkomponenten. Diese Komponenten können in Ihrem Abonnement auf folgende Weise konfiguriert werden:

* Azure-Portal
* Azure-Befehlszeilenschnittstelle
* Azure PowerShell
* Resource Manager-Vorlagen

## <a name="frontend-ip-configuration"></a>Front-End-IP-Konfiguration<a name = "frontend-ip-configurations"></a>

Die IP-Adresse Ihres Azure Load Balancers. Sie ist der Kontaktpunkt für Clients. Diese IP-Adressen sind möglich:

- **Öffentliche IP-Adresse**
- **Private IP-Adresse**

Die Art der IP-Adresse bestimmt den **Typ** des erstellten Lastenausgleichs. Wenn Sie eine private IP-Adresse auswählen, wird ein interner Lastenausgleich erstellt. Bei Auswahl einer öffentlichen IP-Adresse wird ein öffentlicher Lastenausgleich erstellt.

|  | Öffentlicher Load Balancer  | Interner Lastenausgleich |
| ---------- | ---------- | ---------- |
| **Front-End-IP-Konfiguration**| Öffentliche IP-Adresse | Private IP-Adresse|
| **Beschreibung** | Bei einem öffentlichen Lastenausgleich werden die öffentliche IP-Adresse und der Port des eingehenden Datenverkehrs der privaten IP-Adresse und dem Port der VM zugeordnet. Für den Antwortdatenverkehr von der VM führt der Lastenausgleich eine Zuordnung in umgekehrter Richtung durch. Sie können bestimmte Typen von Datenverkehr auf verschiedene VMs oder Dienste verteilen, indem Sie Lastenausgleichsregeln anwenden. Sie können zum Beispiel die Netzwerklast von Webanforderungen auf mehrere Webserver verteilen.| Ein interner Lastenausgleich verteilt Datenverkehr auf Ressourcen, die sich in einem virtuellen Netzwerk befinden. Azure schränkt den Zugriff auf die Front-End-IP-Adressen eines virtuellen Netzwerks ein, für die ein Lastausgleich vorgenommen wird. Front-End-IP-Adressen und virtuelle Netzwerke werden nie direkt für einen Internetendpunkt verfügbar gemacht. Interne Branchenanwendungen werden in Azure ausgeführt. Auf sie wird aus Azure oder von lokalen Ressourcen aus zugegriffen. |
| **Unterstützte SKUs** | Basic, Standard | Basic, Standard |

![Beispiel für einen mehrstufigen Lastenausgleich](./media/load-balancer-overview/load-balancer.png)

Load Balancer kann über mehrere Front-End-IP-Adressen verfügen. Erfahren Sie mehr zu [mehreren Front-Ends](load-balancer-multivip-overview.md).

## <a name="backend-pool"></a>Back-End-Pool

Die Gruppe virtueller Computer oder Instanzen in einer VM-Skalierungsgruppe, von denen die eingehende Anforderung verarbeitet wird. Für eine kosteneffiziente Skalierung zur Bewältigung großer Mengen an eingehendem Datenverkehr empfiehlt es sich in der Regel, dem Back-End-Pool weitere Instanzen hinzuzufügen.

Die Konfiguration des Lastenausgleichs wird automatisch angepasst, wenn Sie Instanzen hoch- oder herunterskalieren. Durch Hinzufügen virtueller Computer zum bzw. Entfernen virtueller Computer aus dem Back-End-Pool wird der Lastenausgleich ohne zusätzliche Vorgänge neu konfiguriert. Der Back-End-Pool wird für jeden beliebigen virtuellen Computer im virtuellen Netzwerk verwendet.

Halten Sie bei der Gestaltung Ihres Back-End-Pools die Anzahl der einzelnen Back-End-Pool-Ressourcen so gering wie möglich, um die Dauer von Verwaltungsvorgängen zu optimieren. Es gibt keinen Unterschied in der Datenebenenleistung oder -skalierung.

## <a name="health-probes"></a>Integritätstests

Mithilfe eines Integritätstest wird der Integritätsstatus der Instanzen im Back-End-Pool ermittelt. Konfigurieren Sie während der Erstellung des Lastenausgleichs einen Integritätstest, den der Lastenausgleich verwenden soll.  Dieser Integritätstest bestimmt, ob eine Instanz fehlerfrei ist und Datenverkehr empfangen kann.

Sie können den gewünschten Fehlerschwellenwert für Ihre Integritätstests definieren. Wenn ein Test nicht reagiert, beendet der Load Balancer das Senden neuer Verbindungen an die fehlerhaften Instanzen. Ein Testfehler wirkt sich nicht auf vorhandene Verbindungen aus. Die Verbindung bleibt so lange bestehen, bis die Anwendung:

- den Flow beendet
- eine Leerlauftimeout auftritt
- der virtuelle Computer heruntergefahren wird

Load Balancer verfügt über verschiedene Integritätstesttypen für Endpunkte: TCP, HTTP und HTTPS. [Erfahren Sie mehr zu Load Balancer-Integritätstests](load-balancer-custom-probe-overview.md).

Vom Load Balancer im Tarif „Basic“ werden keine HTTPS-Tests unterstützt. Vom Load Balancer im Tarif „Basic“ werden alle TCP-Verbindungen (einschließlich aktiver Verbindungen) beendet.

## <a name="load-balancing-rules"></a>Lastenausgleichsregeln

Mithilfe einer Lastenausgleichsregel wird definiert, wie eingehender Datenverkehr auf **alle** Instanzen innerhalb des Back-End-Pools verteilt werden soll. Eine Lastenausgleichsregel ordnet mehreren Back-End-IP-Adressen und Ports eine bestimmte Front-End-IP-Konfiguration und einen Port zu.

Verwenden Sie z. B. eine Lastenausgleichsregel für Port 80, um Datenverkehr von Ihrer Front-End-IP-Adresse an Port 80 ihrer Back-End-Instanzen umzuleiten.

<p align="center">
  <img src="./media/load-balancer-components/lbrules.svg" alt= "Figure depicts how Azure Load Balancer directs frontend port 80 to three instances of backend port 80." width="512" title="Lastenausgleichsregeln">
</p>

*Abbildung: Lastenausgleichsregeln*

## <a name="high-availability-ports"></a>Hochverfügbarkeitsports

Eine mit **„protocol - all“ und „port - 0“** konfigurierte Lastenausgleichsregel. 

Diese Regel ermöglicht es, dass eine einzige Regel den Lastenausgleich aller TCP- und UDP-Datenflüsse vornimmt, die an allen Ports einer internen Load Balancer Standard-Instanz eingehen. 

Die Entscheidung über den Lastenausgleich erfolgt pro Datenfluss. Diese Lösung basiert auf der folgenden Verbindung mit fünf Tupeln: 

1. Quell-IP-Adresse
2. Quellport
3. Ziel-IP-Adresse
4. Zielport
5. Protokoll

Die Lastenausgleichsregeln für Hochverfügbarkeitsports unterstützen Sie bei wichtigen Szenarien, z. B. Hochverfügbarkeit und Skalierung für virtuelle Netzwerkgeräte in virtuellen Netzwerken. Das Feature kann hilfreich sein, wenn für eine große Anzahl von Ports ein Lastenausgleich vorgenommen werden muss.

<p align="center">
  <img src="./media/load-balancer-components/harules.svg" alt="Figure depicts how Azure Load Balancer directs all frontend ports to three instances of all backend ports" width="512" title="Hochverfügbarkeitsport-Regeln">
</p>

*Abbildung: Hochverfügbarkeitsport-Regeln*

Weitere Informationen zu [Hochverfügbarkeitsports](load-balancer-ha-ports-overview.md).

## <a name="inbound-nat-rules"></a>Eingehende NAT-Regeln

Eine NAT-Regel für eingehenden Datenverkehr leitet eingehenden Datenverkehr, der an eine Kombination aus Front-End-IP-Adresse und Port gesendet wird weiter. Der Datenverkehr wird an einen **bestimmten** virtuellen Computer oder eine Instanz im Back-End-Pool gesendet. Der Portweiterleitung liegt die gleiche hashbasierte Verteilung zugrunde wie dem Lastenausgleich.

Beispielsweise, wenn Sie RDP-Sitzungen (Remote Desktop Protocol, Remotedesktopprotokoll) oder SSH-Sitzungen (Secure Shell) zum Trennen von VM-Instanzen innerhalb eines Back-End-Pools verwenden möchten. Ports können mehrere interne Endpunkte unter derselben Front-End-IP-Adresse zugeordnet werden. Sie können die Front-End-IP-Adressen verwenden, um für Ihre VMs die Remoteverwaltung ohne zusätzliche Jumpbox durchzuführen.

<p align="center">
  <img src="./media/load-balancer-components/inboundnatrules.svg" alt="Figure depicts how Azure Load Balancer directs frontend ports 3389, 443, and 80 to backend ports with the same values on separate servers." width="512" title="Eingehende NAT-Regeln">
</p>

*Abbildung: NAT-Regeln für eingehenden Datenverkehr*

NAT-Regeln für eingehenden Datenverkehr im Kontext von Virtual Machine Scale Sets sind NAT-Pools für eingehenden Datenverkehr. Weitere Informationen zu [Load Balancer-Komponenten und VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#azure-virtual-machine-scale-sets-with-azure-load-balancer).

## <a name="outbound-rules"></a>Ausgangsregeln

Eine Ausgangsregel konfiguriert die Netzwerkadressenübersetzung (Network Address Translation, NAT) für ausgehenden Datenverkehr für alle virtuellen Computer oder Instanzen, die vom Back-End-Pool identifiziert wurden. Durch diese Regel können Instanzen im Back-End (ausgehend) mit dem Internet oder anderen Endpunkten kommunizieren.

Erfahren Sie mehr zu [ausgehenden Verbindungen und Regeln](load-balancer-outbound-connections.md).

Vom Lastenausgleich im Tarif „Basic“ werden keine Ausgangsregeln unterstützt.

## <a name="limitations"></a>Einschränkungen

- Informationen zu Load Balancer-[Grenzwerten](../azure-resource-manager/management/azure-subscription-service-limits.md) 
- Load Balancer ermöglicht den Lastenausgleich und die Portweiterleitung für bestimmte TCP- oder UDP-Protokolle. Für Lastenausgleichsregeln und NAT-Regeln für eingehenden Datenverkehr werden TCP und UDP unterstützt, aber keine anderen IP-Protokolle, z. B. ICMP.
- Für den ausgehenden Datenfluss von einer Back-End-VM zum Front-End einer internen Load Balancer-Instanz tritt ein Fehler auf.
- Eine Lastenausgleichsregel kann sich nicht über zwei virtuelle Netzwerke erstrecken.  Front-Ends und ihre Back-End-Instanzen müssen sich im gleichen virtuellen Netzwerk befinden.  
- Das Weiterleiten von IP-Fragmenten wird für Lastenausgleichsregeln nicht unterstützt. Die IP-Fragmentierung von UDP- und TCP-Paketen wird für Lastenausgleichsregeln nicht unterstützt. Hochverfügbarkeitsports für Lastenausgleichsregeln können verwendet werden, um vorhandene IP-Fragmente weiterzuleiten. Weitere Informationen finden Sie unter [Übersicht über Hochverfügbarkeitsports](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den ersten Schritten mit einer Load Balancer-Instanz finden Sie unter [Schnellstart: Erstellen einer Load Balancer Standard-Instanz für den Lastenausgleich virtueller Computer im Azure-Portal](quickstart-load-balancer-standard-public-portal.md).
- Weitere Informationen zu [Azure Load Balancer](load-balancer-overview.md).
- Informieren Sie sich über [öffentliche IP-Adressen](../virtual-network/virtual-network-public-ip-address.md).
- Informieren Sie sich über [private IP-Adressen](../virtual-network/private-ip-addresses.md).
- Informationen zu [Standard Load Balancer und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md)
- Weitere Informationen zu [Diagnosen für Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Informationen zur [TCP-Zurücksetzung bei Leerlauf](load-balancer-tcp-reset.md).
- Informationen zu [Load Balancer Standard mit Lastenausgleichsregeln für HA-Ports](load-balancer-ha-ports-overview.md)
- Weitere Informationen zu [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md).
- Lesen Sie weitere Informationen zu [Load Balancer-Grenzwerten](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer).
- Erfahren Sie mehr über die Verwendung der [Portweiterleitung](./tutorial-load-balancer-port-forwarding-portal.md).