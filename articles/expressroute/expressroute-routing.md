---
title: 'Azure ExpressRoute: Routinganforderungen'
description: Diese Seite enthält ausführliche Anforderungen für das Konfigurieren und Verwalten des Routings für ExpressRoute-Verbindungen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: duau
ms.openlocfilehash: 208fe9c5bfa20bf5b2daab296af9f955db67a33d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007170"
---
# <a name="expressroute-routing-requirements"></a>ExpressRoute-Routinganforderungen
Zum Herstellen einer Verbindung mit Microsoft-Clouddiensten per ExpressRoute müssen Sie das Routing einrichten und verwalten. Einige Konnektivitätsanbieter bieten das Einrichten und Verwalten des Routings als verwalteten Dienst an. Fragen Sie bei Ihrem Konnektivitätsanbieter nach, ob dieser Dienst angeboten wird. Ist dies nicht der Fall, müssen Sie folgende Anforderungen erfüllen:

Der Artikel [ExpressRoute-Verbindungen und Routingdomänen](expressroute-circuit-peerings.md) enthält eine Beschreibung der Routingsitzungen, die zum Herstellen der Konnektivität eingerichtet werden müssen.

> [!NOTE]
> Microsoft unterstützt für Konfigurationen mit Hochverfügbarkeit keine Routerredundanzprotokolle (wie HSRP oder VRRP). Wir nutzen ein redundantes Paar mit BGP-Sitzungen pro Peering, um Hochverfügbarkeit sicherzustellen.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>IP-Adressen für Peerings
Sie müssen einige Blöcke mit IP-Adressen reservieren, um das Routing zwischen Ihrem Netzwerk und den Microsoft Enterprise-Edgeroutern (MSEEs) zu konfigurieren. Dieser Abschnitt enthält eine Liste mit den Anforderungen, und es werden die Regeln zur Beschaffung und Verwendung dieser IP-Adressen beschrieben.

### <a name="ip-addresses-used-for-azure-private-peering"></a>IP-Adressen für privates Azure-Peering
Sie können entweder private IP-Adressen oder öffentliche IP-Adressen verwenden, um die Peerings zu konfigurieren. Der Adressbereich zum Konfigurieren der Routen darf sich nicht mit Adressbereichen überlappen, die in Azure zum Erstellen von virtuellen Netzwerken verwendet werden. 

* Sie müssen ein /29-Subnetz oder zwei /30-Subnetze für Routingschnittstellen reservieren.
* Die für das Routing verwendeten Subnetze können entweder private IP-Adressen oder öffentliche IP-Adressen sein.
* Die Subnetze dürfen nicht mit dem Bereich in Konflikt stehen, der vom Kunden für die Nutzung in der Microsoft Cloud reserviert ist.
* Wenn ein /29-Subnetz verwendet wird, wird es in zwei /30-Subnetze unterteilt. 
  * Das erste /30-Subnetz wird für die primäre Verknüpfung verwendet, das zweite für die sekundäre Verknüpfung.
  * Für jedes /30-Subnetz müssen Sie zuerst die IP-Adresse des /30-Subnetzes auf dem Router verwenden. Microsoft verwendet die zweite IP-Adresse des /30-Subnetzes zum Einrichten einer BGP-Sitzung.
  * Sie müssen beide BGP-Sitzungen einrichten, damit unsere [Vereinbarungen zum Servicelevel](https://azure.microsoft.com/support/legal/sla/) gültig sind.  

#### <a name="example-for-private-peering"></a>Beispiel für privates Peering
Wenn Sie zum Einrichten des Peerings „a.b.c.d/29“ verwenden, wird eine Unterteilung in zwei /30-Subnetze vorgenommen. Beachten Sie im folgenden Beispiel, wie das Subnetz „a.b.c.d/29“ verwendet wird:

* „a.b.c.d/29“ wird in „a.b.c.d/30“ und „a.b.c.d+4/30“ unterteilt und über die Bereitstellungs-APIs an Microsoft übergeben.
  * Sie verwenden „a.b.c.d+1“ als VRF-IP für das primäre PE-Element, und Microsoft nutzt „a.b.c.d+2“ als VRF-IP für den primären MSEE.
  * Sie verwenden „a.b.c.d+5“ als VRF-IP für das sekundäre PE-Element, und Microsoft nutzt „a.b.c.d+6“ als VRF-IP für den sekundären MSEE.

Betrachten wir den Fall, in dem Sie 192.168.100.128/29 zum Einrichten des privaten Peerings auswählen. 192.168.100.128/29 enthält die Adressen von 192.168.100.128 bis 192.168.100.135, für die Folgendes gilt:

* 192.168.100.128/30 wird link1 zugewiesen, wobei der Anbieter 192.168.100.129 und Microsoft 192.168.100.130 verwendet.
* 192.168.100.132/30 wird link2 zugewiesen, wobei der Anbieter 192.168.100.133 und Microsoft 192.168.100.134 verwendet.

### <a name="ip-addresses-used-for-microsoft-peering"></a>IP-Adressen für Microsoft-Peering
Sie müssen eigene öffentliche IP-Adressen zum Einrichten der BGP-Sitzungen verwenden. Microsoft muss in der Lage sein, die Eigentümerschaft der IP-Adressen über Routing Internet Registries (RIR) und Internet Routing Registries (IRR) zu überprüfen.

* Die im Portal für angekündigte öffentliche Präfixe aufgeführten IP-Adressen für Microsoft-Peering erstellen ACLs für die Microsoft-Kernrouter, um eingehenden Datenverkehr von diesen IP-Adressen zuzulassen. 
* Sie müssen ein eindeutiges /29-Subnetz (IPv4) oder /125-Subnetz (IPv6) oder zwei /30-Subnetze (IPv4) oder zwei /126-Subnetze (IPv6) verwenden, um das BGP-Peering für jedes Peering pro ExpressRoute-Verbindung einzurichten (falls Sie über mehrere verfügen).
* Wenn ein /29-Subnetz verwendet wird, wird es in zwei /30-Subnetze unterteilt.
* Das erste /30-Subnetz wird für die primäre Verknüpfung verwendet, das zweite für die sekundäre Verknüpfung.
* Für jedes /30-Subnetz müssen Sie zuerst die IP-Adresse des /30-Subnetzes auf dem Router verwenden. Microsoft verwendet die zweite IP-Adresse des /30-Subnetzes zum Einrichten einer BGP-Sitzung.
* Wenn ein /125-Subnetz verwendet wird, wird es in zwei /126-Subnetze unterteilt.
* Das erste /126-Subnetz wird für die primäre Verknüpfung verwendet, das zweite für die sekundäre Verknüpfung.
* Für jedes /126-Subnetz müssen Sie die erste IP-Adresse des /126-Subnetzes auf dem Router verwenden. Microsoft verwendet die zweite IP-Adresse des /126-Subnetzes zum Einrichten einer BGP-Sitzung.
* Sie müssen beide BGP-Sitzungen einrichten, damit unsere [Vereinbarungen zum Servicelevel](https://azure.microsoft.com/support/legal/sla/) gültig sind.

### <a name="ip-addresses-used-for-azure-public-peering"></a>IP-Adressen für öffentliches Azure-Peering

> [!NOTE]
> Öffentliches Azure-Peering ist für neue Verbindungen nicht verfügbar.
> 

Sie müssen eigene öffentliche IP-Adressen zum Einrichten der BGP-Sitzungen verwenden. Microsoft muss in der Lage sein, die Eigentümerschaft der IP-Adressen über Routing Internet Registries (RIR) und Internet Routing Registries (IRR) zu überprüfen. 

* Sie müssen ein eindeutiges /29-Subnetz oder zwei /30-Subnetze verwenden, um das BGP-Peering für jedes Peering pro ExpressRoute-Verbindung einzurichten (falls Sie über mehr als ein Peering verfügen). 
* Wenn ein /29-Subnetz verwendet wird, wird es in zwei /30-Subnetze unterteilt. 
  * Das erste /30-Subnetz wird für die primäre Verknüpfung verwendet, das zweite für die sekundäre Verknüpfung.
  * Für jedes /30-Subnetz müssen Sie zuerst die IP-Adresse des /30-Subnetzes auf dem Router verwenden. Microsoft verwendet die zweite IP-Adresse des /30-Subnetzes zum Einrichten einer BGP-Sitzung.
  * Sie müssen beide BGP-Sitzungen einrichten, damit unsere [Vereinbarungen zum Servicelevel](https://azure.microsoft.com/support/legal/sla/) gültig sind.

## <a name="public-ip-address-requirement"></a>Öffentliche IP-Adresse – Anforderungen

### <a name="private-peering"></a>Privates Peering
Sie können für das private Peering öffentliche oder private IPv4-Adressen verwenden. Wir stellen eine End-to-End-Isolation für Ihren Datenverkehr bereit, sodass beim privaten Peering keine Überlappungen von Adressen mit anderen Kunden möglich sind. Diese Adressen werden nicht im Internet angekündigt. 

### <a name="microsoft-peering"></a>Microsoft-Peering
Über den Microsoft-Peeringpfad können Sie eine Verbindung mit Microsoft-Clouddiensten herstellen. Die Liste der Dienste umfasst Microsoft 365-Dienste wie z.B. Exchange Online, SharePoint Online, Skype for Business und Microsoft Teams. Microsoft unterstützt die bidirektionale Konnektivität für das Microsoft-Peering. Für Datenverkehr, der für Microsoft-Clouddienste bestimmt ist, müssen vor dem Eintritt in das Microsoft-Netzwerk gültige, öffentliche IPv4-Adressen verwendet werden.

Stellen Sie sicher, dass Ihre IP-Adresse und die AS-Nummer für Sie in einer der folgenden Registrierungen registriert sind:

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

Falls Ihre Präfixe und Ihre AS-Nummer in den obigen Registrierungen nicht Ihnen zugewiesen sind, müssen Sie eine Supportanfrage zur manuellen Überprüfung Ihrer Präfixe und ASN stellen. Dazu benötigt der Support Belege – beispielsweise ein Autorisierungsschreiben, das beweist, dass Sie zur Verwendung der Ressourcen berechtigt sind.

Für Microsoft-Peering kann eine private AS-Nummer verwendet werden, dies erfordert jedoch ebenfalls eine manuelle Überprüfung. Darüber hinaus entfernen wir private AS-Nummern in „AS PATH“ für die empfangenen Präfixe. Dadurch können Sie in „AS PATH“ keine privaten AS-Nummern anfügen, um das [Routing für Microsoft-Peering zu beeinflussen](expressroute-optimize-routing.md). 

> [!IMPORTANT]
> Kündigen Sie nicht die gleiche öffentliche IP-Route mit dem öffentlichen Internet und über ExpressRoute an. Um das Risiko zu reduzieren, dass es durch eine Fehlkonfiguration zu asymmetrischem Routing kommt, empfehlen wir dringend, Microsoft über ExpressRoute [NAT-IP-Adressen](expressroute-nat.md) aus einem Bereich anzukündigen, der im Internet nicht angekündigt wird. Falls dies nicht möglich ist, müssen Sie sicherstellen, dass Sie über ExpressRoute einen spezifischeren Bereich ankündigen als über die Internetverbindung. Neben der öffentliche Route für die NAT können Sie auch für Routen über ExpressRoute die öffentlichen IP-Adressen ankündigen, die von den Servern in Ihrem lokalen Netzwerk verwendet werden, die mit Microsoft 365-Endpunkten innerhalb von Microsoft kommunizieren. 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>Öffentliches Peering (veraltet – nicht für neue Verbindungen verfügbar)
Der öffentliche Azure-Peeringpfad ermöglicht, dass Sie zu allen in Azure gehosteten Diensten über die öffentliche IP-Adresse eine Verbindung herstellen können. Dazu zählen Dienste, die unter [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md) aufgeführt sind und die von ISVs auf Microsoft Azure gehostet werden. Die Konnektivität mit Microsoft Azure-Diensten für öffentliches Peering wird immer von Ihrem Netzwerk aus in das Microsoft-Netzwerk initiiert. Sie müssen öffentliche IP-Adressen für den Datenverkehr verwenden, der für das Microsoft-Netzwerk bestimmt ist.

> [!IMPORTANT]
> Über Microsoft-Peering kann auf alle Azure-PaaS-Dienste zugegriffen werden.
>   

Für öffentliches Peering kann eine private AS-Nummer verwendet werden.

## <a name="dynamic-route-exchange"></a>Dynamischer Routenaustausch
Der Routingaustausch verläuft über das eBGP-Protokoll. EBGP-Sitzungen werden zwischen den MSEEs und Ihren Routern eingerichtet. Die Authentifizierung von BGP-Sitzungen ist nicht unbedingt erforderlich. Bei Bedarf kann ein MD5-Hash konfiguriert werden. Unter [Konfigurieren des Routings](how-to-routefilter-portal.md) und [Bereitstellungsworkflows für ExpressRoute-Verbindungen und Verbindungszustände](expressroute-workflows.md) finden Sie Informationen zum Konfigurieren von BGP-Sitzungen.

## <a name="autonomous-system-numbers"></a>Autonome Systemnummern
Microsoft verwendet AS 12076 für öffentliches und privates Azure-Peering sowie für Microsoft-Peering. Wir haben ASNs von 65515 bis 65520 für die interne Verwendung reserviert. Sowohl AS-Nummern mit 16 als auch mit 32 Bit werden unterstützt.

Es gibt keine Anforderungen in Bezug auf die Symmetrie der Datenübertragung. Die Weiterleitungs- und Rückgabepfade können unterschiedliche Routerpaare durchlaufen. Identische Routen müssen von beiden Seiten über mehrere Verbindungspaare angekündigt werden, die in Ihrem Besitz sind. Routenmetriken müssen nicht identisch sein.

## <a name="route-aggregation-and-prefix-limits"></a>Routenaggregation- und Präfixgrenzen
Wir unterstützen bis zu 4.000 Präfixe, die uns per privatem Azure-Peering angekündigt werden. Dies kann auf bis zu 10.000 Präfixe erhöht werden, wenn das ExpressRoute Premium-Add-On aktiviert wird. Wir akzeptieren bis zu 200 Präfixe pro BGP-Sitzung für das öffentliche Azure-Peering und das Microsoft-Peering. 

Die BGP-Sitzung wird verworfen, wenn die Anzahl von Präfixen den Grenzwert überschreitet. Wir akzeptieren Standardrouten nur über den Link für privates Peering. Der Anbieter muss die Standardroute und private IP-Adressen (RFC 1918) aus den Pfaden für das öffentliche Azure-Peering und das Microsoft-Peering herausfiltern. 

## <a name="transit-routing-and-cross-region-routing"></a>Transitrouting und regionsübergreifendes Routing
ExpressRoute kann nicht als Transitrouter konfiguriert werden. Transitrouting-Dienste erhalten Sie bei Ihrem Konnektivitätsanbieter.

## <a name="advertising-default-routes"></a>Ankündigen von Standardrouten
Standardrouten sind nur für Sitzungen mit privatem Azure-Peering zulässig. In diesem Fall wird der gesamte Datenverkehr aus den zugeordneten virtuellen Netzwerken an Ihr Netzwerk geleitet. Das Ankündigen von Standardrouten für das private Peering führt dazu, dass der Internetpfad aus Azure blockiert wird. Sie müssen den Edge-Bereich Ihres Unternehmens nutzen, um Datenverkehr für in Azure gehostete Dienste aus dem Internet und in das Internet zu leiten. 

 Um die Konnektivität mit anderen Azure-Diensten und Infrastrukturdiensten zu ermöglichen, müssen Sie sicherstellen, dass eines der folgenden Elemente vorhanden ist:

* Öffentliches Azure-Peering ist zum Weiterleiten von Datenverkehr an öffentliche Endpunkte aktiviert.
* Sie verwenden das benutzerdefinierte Routing, um Internetkonnektivität für alle Subnetze zuzulassen, die dies erfordern.

> [!NOTE]
> Das Ankündigen von Standardrouten führt dazu, dass die Aktivierung von Windows- und anderen VM-Lizenzen verloren geht. Führen Sie die Schritte [dieser Anleitung](/archive/blogs/mast/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling) aus, um dies zu umgehen.
> 
> 

## <a name="support-for-bgp-communities"></a><a name="bgp"></a>Unterstützung für BGP-Communitys
Dieser Abschnitt enthält eine Übersicht darüber, wie BGP-Communitys mit ExpressRoute verwendet werden. Microsoft kündigt Routen in den Pfaden für das öffentliche Peering und Microsoft-Peering an, und die Routen sind dabei mit den entsprechenden Communitywerten versehen. Die Gründe für diese Vorgehensweise und die Details zu den Communitywerten sind weiter unten beschrieben. Microsoft berücksichtigt aber keine Communitywerte, mit denen Routen gekennzeichnet sind, die gegenüber Microsoft angekündigt werden.

Wenn Sie an einem Peeringstandort in einer geopolitischen Region per ExpressRoute eine Verbindung mit Microsoft herstellen, haben Sie Zugriff auf alle Microsoft-Clouddienste innerhalb dieser geopolitischen Grenze. 

Wenn Sie beispielsweise in Amsterdam per ExpressRoute mit Microsoft verbunden sind, haben Sie Zugriff auf alle Microsoft-Clouddienste, die in Nordeuropa und Westeuropa gehostet werden. 

Die Seite [ExpressRoute-Partner und Peeringstandorte](expressroute-locations.md) enthält eine ausführliche Liste mit geopolitischen Regionen, dazugehörigen Azure-Regionen und entsprechenden ExpressRoute-Peeringstandorten.

Sie können mehr als eine ExpressRoute-Verbindung pro geopolitischer Region erwerben. Wenn Sie über mehrere Verbindungen verfügen, ergeben sich für Sie aufgrund der Georedundanz daraus erhebliche Vorteile in Bezug auf Hochverfügbarkeit. Bei Verwendung mehrerer ExpressRoute-Verbindungen erhalten Sie die gleiche Gruppe von Präfixen, die von Microsoft für das Microsoft-Peering und die öffentlichen Peeringpfade bekannt gegeben werden. Dies bedeutet, dass Sie dann mehrere Pfade aus dem Netzwerk zu Microsoft nutzen können. Dies kann unter Umständen dazu führen, dass in Ihrem Netzwerk suboptimale Routingentscheidungen getroffen werden. Dies kann die Konnektivität für verschiedene Dienste beeinträchtigen. Sie können die Communitywerte nutzen, um die richtigen Routingentscheidungen zu treffen und [optimales Routing für Benutzer](expressroute-optimize-routing.md) zu bieten.

| **Microsoft Azure-Region** | **Regionale BGP-Community** | **Speicher-BGP-Community** | **SQL BGP-Community** | **Cosmos DB-BGP-Community** | **Backup BGP-Community** |
| --- | --- | --- | --- | --- | --- |
| **Nordamerika** | |
| East US | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 | 12076:55004 |
| USA (Ost) 2 | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 | 12076:55005 |
| USA (Westen) | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 | 12076:55006 |
| USA, Westen 2 | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 | 12076:55026 |
| USA, Westen-Mitte | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 | 12076:55027 |
| USA Nord Mitte | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 | 12076:55007 |
| USA Süd Mitte | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 | 12076:55008 |
| USA (Mitte) | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 | 12076:55009 |
| Kanada, Mitte | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 | 12076:55020 |
| Kanada, Osten | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 | 12076:55021 |
| **Südamerika** | |
| Brasilien Süd | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 | 12076:55014 |
| **Europa** | |
| Nordeuropa | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 | 12076:55003 |
| Europa, Westen | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 | 12076:55002 |
| UK, Süden | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 | 12076:55024 |
| UK, Westen | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 | 12076:55025 |
| Frankreich, Mitte | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 | 12076:55030 |
| Frankreich, Süden | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 | 12076:55031 |
| Schweiz, Norden | 12076:51038 | 12076:52038 | 12076:53038 | 12076:54038 | 12076:55038 |
| Schweiz, Westen | 12076:51039 | 12076:52039 | 12076:53039 | 12076:54039 | 12076:55039 | 
| Deutschland, Norden | 12076:51040 | 12076:52040 | 12076:53040 | 12076:54040 | 12076:55040 | 
| Deutschland, Westen-Mitte | 12076:51041 | 12076:52041 | 12076:53041 | 12076:54041 | 12076:55041 | 
| Norwegen, Osten | 12076:51042 | 12076:52042 | 12076:53042 | 12076:54042 | 12076:55042 | 
| Norwegen, Westen | 12076:51043 | 12076:52043 | 12076:53043 | 12076:54043 | 12076:55043 | 
| **Asien-Pazifik** | |
| Asien, Osten | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 | 12076:55010 |
| Asien, Südosten | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 | 12076:55011 |
| **Japan** | |
| Japan, Osten | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 | 12076:55012 |
| Japan, Westen | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 | 12076:55013 |
| **Australien** | |
| Australien (Osten) | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 | 12076:55015 |
| Australien, Südosten | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 | 12076:55016 |
| **Australische Behörden** | |
| Australien, Mitte | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 | 12076:55032 |
| Australien, Mitte 2 | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 | 12076:55033 |
| **Indien** | |
| Indien, Süden | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 | 12076:55019 |
| Indien, Westen | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 | 12076:55018 |
| Indien, Mitte | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 | 12076:55017 |
| **Korea** | |
| Korea, Süden | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 | 12076:55028 |
| Korea, Mitte | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 | 12076:55029 |
| **Südafrika**| |
| Südafrika, Norden | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 | 12076:55034 |
| Südafrika, Westen | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 | 12076:55035 |
| **Vereinigte Arabische Emirate**| |
| Vereinigte Arabische Emirate, Norden | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 | 12076:55036 |
| VAE, Mitte | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 | 12076:55037 |


Alle Routen, die von Microsoft angekündigt werden, werden mit dem entsprechenden Communitywert gekennzeichnet. 

> [!IMPORTANT]
> Globale Präfixe werden mit einem entsprechenden Communitywert markiert.
> 
> 

### <a name="service-to-bgp-community-value"></a>Dienst zu BGP-Communitywert
Zusätzlich zu den obigen Kennzeichnungen versieht Microsoft Präfixe auch basierend auf dem zugehörigen Dienst mit einer Kennzeichnung. Dies gilt nur für das Microsoft-Peering. Die Tabelle unten enthält die Zuordnung des Diensts zum BGP-Communitywert. Sie können das Cmdlet „Get-AzBgpServiceCommunity“ ausführen, um eine vollständige Liste der neuesten Werte abzurufen.

| **Service** | **BGP-Communitywert** |
| --- | --- |
| Exchange Online\*\* | 12076:5010 |
| SharePoint Online\*\* | 12076:5020 |
| Skype For Business Online\*\*/\*\*\* | 12076:5030 |
| CRM Online\*\*\*\* |12076:5040 |
| Azure Global Services\* | 12076:5050 |
| Azure Active Directory |12076:5060 |
| Azure Resource Manager |12076:5070 |
| Andere Office 365 Online-Dienste** | 12076:5100 |

\*Azure Global Services enthält zurzeit nur Azure DevOps.
\*\* Autorisierung von Microsoft erforderlich, siehe [Konfigurieren von Routenfiltern für das Microsoft-Peering](how-to-routefilter-portal.md)\
\*\*\* In dieser Community werden auch die erforderlichen Routen für Microsoft Teams-Dienste veröffentlicht.\
\*\*\*\* CRM Online unterstützt Dynamics v8.2 und niedriger. Wählen Sie für höhere Versionen die regionale Community für Ihre Dynamics-Bereitstellungen aus.

> [!NOTE]
> Microsoft berücksichtigt keine BGP-Communitywerte, die von Ihnen für die gegenüber Microsoft angekündigten Routen festgelegt werden.
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>BGP-Communityunterstützung in nationalen Clouds

| **Azure-Region für nationale Clouds**| **BGP-Communitywert** |
| --- | --- |
| **US Government** |  |
| US Gov Arizona | 12076:51106 |
| US Gov Iowa | 12076:51109 |
| US Government, Virginia | 12076:51105 |
| US Gov Texas | 12076:51108 |
| US DoD, Mitte | 12076:51209 |
| US DoD, Osten | 12076:51205 |


| **Dienst in nationalen Clouds** | **BGP-Communitywert** |
| --- | --- |
| **US Government** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype for Business Online |12076:5130 |
| Azure Active Directory |12076:5160 |
| Andere Office 365 Online-Dienste |12076:5200 |

## <a name="next-steps"></a>Nächste Schritte
* Konfigurieren Sie Ihre ExpressRoute-Verbindung.
  
  * [Erstellen und Ändern einer Verbindung](expressroute-howto-circuit-arm.md)
  * [Erstellen und Ändern einer Peeringkonfiguration](expressroute-howto-routing-arm.md)
  * [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)