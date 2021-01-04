---
title: 'Azure ExpressRoute: Entwerfen für die Notfallwiederherstellung'
description: Diese Seite enthält architektonische Empfehlungen für die Notfallwiederherstellung bei Verwendung von Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: duau
ms.openlocfilehash: 2a5730cd75ccb76d25897e9109555113f7355c2f
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "92202412"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Entwurf für die Notfallwiederherstellung mit privatem ExpressRoute-Peering

ExpressRoute wurde für Hochverfügbarkeit entwickelt, um Konnektivität für private Netzwerke mit Microsoft-Ressourcen auf Netzbetreiberniveau bereitzustellen. Das heißt, gibt es keine einzelne Fehlerquelle im ExpressRoute-Pfad im Microsoft-Netzwerk. Entwurfsüberlegungen zum Maximieren der Verfügbarkeit einer ExpressRoute-Verbindung finden Sie unter [Entwerfen für Hochverfügbarkeit mit ExpressRoute][HA].

Wir wollen jedoch Murphys populäres Sprichwort nicht vergessen: *Alles, was schiefgehen kann, wird auch schiefgehen*. Konzentrieren wir uns in diesem Artikel also auf Lösungen, die über Fehler hinausgehen, die mit einer einzigen ExpressRoute-Verbindung behoben werden können. Mit anderen Worten, lassen Sie uns in diesem Artikel Überlegungen zur Netzwerkarchitektur anstellen, um eine robuste Back-End-Netzwerkverbindung für die Notfallwiederherstellung mit georedundanten ExpressRoute-Verbindungen aufzubauen.

>[!NOTE]
>Die in diesem Artikel beschriebenen Konzepte gelten gleichermaßen, wenn eine ExpressRoute-Leitung innerhalb oder außerhalb eine Virtual WAN erstellt wird.
>

## <a name="need-for-redundant-connectivity-solution"></a>Erfordernis einer redundanten Konnektivitätslösung

Es gibt Möglichkeiten und Fälle, bei denen ein ganzer regionaler Dienst (sei es von Microsoft, Netzwerkdienstanbietern, Kunden oder anderen Clouddienstanbietern) beeinträchtigt wird. Die Hauptursache für solche flächendeckenden Beeinträchtigungen des Diensts sind Naturkatastrophen. Daher ist es für die Geschäftskontinuität und unternehmenskritische Anwendungen wichtig, die Notfallwiederherstellung zu planen.   

Unabhängig davon, ob Sie Ihre unternehmenskritischen Anwendungen in einer Azure-Region, lokal oder anderswo ausführen, können Sie eine andere Azure-Region als Failoverstandort verwenden. Die folgenden Artikel behandeln Notfallwiederherstellung aus Anwendungs- und Front-End-Zugriffsperspektiven:

- [Notfallwiederherstellung auf Unternehmensebene][Enterprise DR]
- [SMB-Notfallwiederherstellung mit Azure Site Recovery][SMB DR]

Wenn Sie sich bei unternehmenskritischen Vorgängen auf die ExpressRoute-Konnektivität zwischen Ihrem lokalen Netzwerk und Microsoft verlassen, sollte Ihr Notfallwiederherstellungsplan auch georedundante Netzwerkverbindungen enthalten. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Herausforderungen beim Verwenden mehrerer ExpressRoute-Verbindungen

Wenn Sie dieselbe Gruppe von Netzwerken über mehrere Verbindungen miteinander verbinden, führen Sie parallele Pfade zwischen den Netzwerken ein. Parallele Pfade können, wenn sie nicht richtig entworfen werden, zu asymmetrischem Routing führen. Wenn Sie zustandsbehaftete Entitäten (z.B. NAT, Firewall) im Pfad haben, kann asymmetrisches Routing den Datenfluss blockieren.  In der Regel werden Sie über den privaten ExpressRoute-Peeringpfad auf keine zustandsbehafteten Entitäten wie NAT oder Firewalls treffen. Daher blockiert asymmetrisches Routing über privates ExpressRoute-Peering nicht unbedingt den Datenfluss.
 
Wenn Sie jedoch für Datenverkehr einen Lastenausgleich über georedundante parallele Pfade durchführen, würden Sie unabhängig davon, ob zustandsbehaftete Entitäten vorhanden sind, eine inkonsistente Netzwerkleistung feststellen. In diesem Artikel sehen wir uns an, wie diese Herausforderungen gelöst werden können.

## <a name="small-to-medium-on-premises-network-considerations"></a>Überlegungen zu kleinen bis mittelgroßen lokalen Netzwerken

Sehen Sie sich das Beispielnetzwerk an, das in der folgenden Abbildung dargestellt wird. Im Beispiel wird die georedundante ExpressRoute-Konnektivität zwischen einem lokalen Standort von Contoso und dem VNET von Contoso in einer Azure-Region hergestellt. In der Abbildung zeigt eine durchgehende grüne Linie den bevorzugten Pfad (über ExpressRoute 1) und die gestrichelte Linie den Standbypfad (über ExpressRoute 2) an.

[![1]][1]

Wenn Sie ExpressRoute-Konnektivität für die Notfallwiederherstellung entwerfen, müssen Sie Folgendes berücksichtigen:

- Verwenden von georedundanten ExpressRoute-Verbindungen
- Verwenden unterschiedlicher Dienstanbieternetzwerke für verschiedene ExpressRoute-Verbindungen
- Entwerfen der einzelnen ExpressRoute-Verbindungen für [Hochverfügbarkeit][HA]
- Beenden der verschiedenen ExpressRoute-Verbindungen an einem anderen Standort im Kundennetzwerk

Wenn Sie Routen über alle ExpressRoute-Pfade identisch ankündigen, führt Azure standardmäßig den Lastenausgleich für lokal gebundenen Datenverkehr über alle ExpressRoute-Pfade hinweg mit ECMP-Routing (Equal-Cost Multi-Path) durch.

Bei den georedundanten ExpressRoute-Verbindungen müssen wir jedoch unterschiedliche Netzwerkleistungen mit unterschiedlichen Netzwerkpfaden (insbesondere für die Netzwerklatenz) berücksichtigen. Um eine konsistentere Netzwerkleistung im normalen Betrieb zu erreichen, sollten Sie die ExpressRoute-Verbindung bevorzugen, die die geringste Latenzzeit bietet.

Sie können Azure so beeinflussen, dass eine ExpressRoute-Verbindung einer anderen vorgezogen wird, indem Sie eine der folgenden Techniken verwenden (in der Reihenfolge ihrer Effektivität):

- Ankündigen der spezifischeren Route vor der bevorzugten ExpressRoute-Verbindung im Vergleich zu anderen ExpressRoute-Verbindungen
- Konfigurieren einer höheren Verbindungsgewichtung für die Verbindung, die das virtuelle Netzwerk mit der bevorzugten ExpressRoute-Verbindung verbindet
- Ankündigen der Routen über weniger bevorzugte ExpressRoute-Verbindungen mit einem längeren AS-Pfad (Voranstellen des AS-Pfads)

### <a name="more-specific-route"></a>Spezifischere Route

Die folgende Abbildung veranschaulicht die Beeinflussung der ExpressRoute-Pfadauswahl durch eine spezifischere Routenankündigung. Im dargestellten Beispiel wird der lokale /24-IP-Bereich von Contoso als zwei /25-Adressbereiche über den bevorzugten Pfad (ExpressRoute 1) und als /24 über den Standbypfad (ExpressRoute 2) angekündigt.

[![2]][2]

Da /25 im Vergleich zu /24 spezifischer ist, würde Azure den für 10.1.11.11.0/24 bestimmten Datenverkehr im Normalzustand über ExpressRoute 1 senden. Wenn beide Verbindungen von ExpressRoute 1 unterbrochen werden, würde das VNET die 10.1.11.11.0/24-Routenankündigung nur über ExpressRoute 2 erkennen. Daher wird in diesem Fehlerzustand die Standbyverbindung verwendet.

### <a name="connection-weight"></a>Verbindungsgewichtung

Der folgende Screenshot zeigt das Konfigurieren der Gewichtung einer ExpressRoute-Verbindung über das Azure-Portal.

[![3]][3]

Die folgende Abbildung veranschaulicht die Beeinflussung der ExpressRoute-Pfadauswahl durch eine Verbindungsgewichtung. Die Standardverbindungsgewichtung ist 0. Im folgenden Beispiel wird die Gewichtung der Verbindung für ExpressRoute 1 als 100 konfiguriert. Wenn ein VNET ein Routenpräfix empfängt, das über mehrere ExpressRoute-Verbindungen angekündigt wird, bevorzugt das VNET die Verbindung mit der höchsten Gewichtung.

[![4]][4]

Wenn beide Verbindungen von ExpressRoute 1 unterbrochen werden, würde das VNET die 10.1.11.11.0/24-Routenankündigung nur über ExpressRoute 2 erkennen. Daher wird in diesem Fehlerzustand die Standbyverbindung verwendet.

### <a name="as-path-prepend"></a>Vorangestellter AS-Pfad

Die folgende Abbildung veranschaulicht die Beeinflussung der ExpressRoute-Pfadauswahl durch einen vorangestellten AS-Pfad. In der Abbildung gibt die Routenankündigung über ExpressRoute 1 das Standardverhalten von eBGP an. Für die Ankündigung der Route über ExpressRoute 2 wird die ASN des lokalen Netzwerks außerdem dem AS-Pfad der Route vorangestellt. Wenn die gleiche Route über mehrere ExpressRoute-Verbindungen empfangen wird, würde das VNET über das eBGP-Routenauswahlverfahren die Route mit dem kürzesten AS-Pfad bevorzugen. 

[![5]][5]

Wenn beide Verbindungen von ExpressRoute 1 ausfallen, würde das VNET die 10.1.11.11.0/24-Routenankündigung nur über ExpressRoute 2 erkennen. Folglich würde der längere AS-Pfad irrelevant werden. Aus diesem Grund würde die Standbyverbindung in diesem Fehlerzustand verwendet.

Wenn Sie Azure mithilfe einer dieser Techniken so beeinflussen, dass eine Ihrer ExpressRoute-Verbindungen gegenüber anderen bevorzugt wird, müssen Sie außerdem sicherstellen, dass auch das lokale Netzwerk den gleichen ExpressRoute-Pfad für Azure-gebundenen Datenverkehr bevorzugt, um asymmetrische Datenflüsse zu vermeiden. In der Regel wird der Wert für die lokale Einstellung verwendet, um das lokale Netzwerk so zu beeinflussen, dass es eine ExpressRoute-Verbindung gegenüber anderen bevorzugt. Die lokale Einstellung ist eine interne BGP-Metrik (iBGP). Die BGP-Route mit dem höchsten Wert für die lokale Einstellung wird bevorzugt.

> [!IMPORTANT]
> Wenn Sie bestimmte ExpressRoute-Verbindungen als Standby verwenden, müssen Sie diese aktiv verwalten und den Failovervorgang in regelmäßigen Abständen testen. 
> 

## <a name="large-distributed-enterprise-network"></a>Großes verteiltes Unternehmensnetzwerk

Wenn Sie über ein großes verteiltes Unternehmensnetzwerk verfügen, verwenden Sie wahrscheinlich mehrere ExpressRoute-Verbindungen. In diesem Abschnitt sehen wir uns an, wie eine Notfallwiederherstellung mithilfe der Aktiv-Aktiv-ExpressRoute-Verbindungen entworfen wird, ohne zusätzliche Standbyverbindungen zu benötigen. 

Sehen Sie sich das Beispiel an, das in der folgenden Abbildung dargestellt wird. Im Beispiel verfügt Contoso über zwei lokale Standorte, die mit zwei Contoso-IaaS-Bereitstellungen in zwei verschiedenen Azure-Regionen über ExpressRoute-Verbindungen an zwei verschiedenen Peeringstandorten verbunden sind. 

[![6]][6]

Die Art und Weise, wie wir die Notfallwiederherstellung planen, besitzt Einfluss darauf, wie regionsübergreifender Datenverkehr an standortübergreifenden Datenverkehr (/region1/region2 an location2/location1) weitergeleitet wird. Betrachten wir zwei verschiedene Notfallarchitekturen, die regions-/standortübergreifenden Datenverkehr auf verschiedene Weise weiterleiten.

### <a name="scenario-1"></a>Szenario 1

Im ersten Szenario entwerfen wir die Notfallwiederherstellung so, dass der gesamte Datenverkehr zwischen einer Azure-Region und dem lokalen Netzwerk im stabilen Zustand über die lokale ExpressRoute-Verbindung fließt. Wenn die lokale ExpressRoute-Verbindung ausfällt, wird die ExpressRoute-Remoteverbindung für alle Datenströme zwischen Azure und dem lokalen Netzwerk verwendet.

Szenario 1 wird in der folgenden Abbildung gezeigt. In der Abbildung geben grüne Linien Pfade für den Datenverkehrsfluss zwischen VNet1 und lokalen Netzwerken an. Die blauen Linien geben Pfade für den Datenverkehrsfluss zwischen VNet2 und lokalen Netzwerken an. Durchgehende Linien zeigen den gewünschten Pfad im stabilen Zustand an, und die gestrichelten Linien zeigen den Datenverkehrspfad beim Ausfall der entsprechenden ExpressRoute-Verbindung an, die den stabilen Datenverkehrsfluss übernimmt. 

[![7]][7]

Sie können das Szenario mithilfe der Verbindungsgewichtung entwerfen, um VNETs so zu beeinflussen, dass sie die Verbindung mit dem lokalen Peeringstandort ExpressRoute für den lokalen netzwerkgebundenen Datenverkehr vorziehen. Um die Lösung zu vervollständigen, müssen Sie symmetrischen umgekehrten Datenverkehr sicherstellen. Sie können die lokale Einstellung für die iBGP-Sitzung zwischen Ihren BGP-Routern (auf denen ExpressRoute-Verbindungen auf der lokalen Seite beendet werden) verwenden, um eine ExpressRoute-Verbindung zu bevorzugen. Die Lösung wird in der folgenden Abbildung dargestellt. 

[![8]][8]

### <a name="scenario-2"></a>Szenario 2

Szenario 2 wird in der folgenden Abbildung dargestellt. In der Abbildung geben grüne Linien Pfade für den Datenverkehrsfluss zwischen VNet1 und lokalen Netzwerken an. Die blauen Linien geben Pfade für den Datenverkehrsfluss zwischen VNet2 und lokalen Netzwerken an. Im stabilen Zustand (durchgezogene Linien in der Abbildung) fließt der gesamte Datenverkehr zwischen VNETs und lokalen Standorten größtenteils über einen Microsoft-Backbone und durchläuft die Verbindung zwischen lokalen Standorten nur im Fehlerzustand (gestrichelte Linien im Diagramm) einer ExpressRoute-Verbindung.

[![9]][9]

Die Lösung wird in der folgenden Abbildung dargestellt. Wie gezeigt, können Sie das Szenario entweder mit einer spezifischeren Route (Option 1) oder einem vorangestellten AS-Pfad (Option 2) entwerfen, um die VNET-Pfadauswahl zu beeinflussen. Um die lokale Netzwerkroutenauswahl für den Azure-gebundenen Datenverkehr zu beeinflussen, müssen Sie die Verbindung zwischen dem lokalen Standort als weniger bevorzugt konfigurieren. Wie Sie die Verbindung als bevorzugt konfigurieren, hängt vom Routingprotokoll ab, das innerhalb des lokalen Netzwerks verwendet wird. Sie können die lokale Einstellung mit iBGP oder die Metrik mit IGP (OSPF oder IS-IS) verwenden.

[![10]][10]


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben wir erläutert, wie Sie die Notfallwiederherstellung einer privaten Peeringkonnektivität einer ExpressRoute-Verbindung konzipieren können. Die folgenden Artikel behandeln Notfallwiederherstellung aus Anwendungs- und Front-End-Zugriffsperspektiven:

- [Notfallwiederherstellung auf Unternehmensebene][Enterprise DR]
- [SMB-Notfallwiederherstellung mit Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "Überlegungen zu kleinen bis mittelgroßen lokalen Netzwerken"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "Einfluss auf die Pfadauswahl mithilfe von spezifischeren Routen"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Konfigurieren der Verbindungsgewichtung über das Azure-Portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "Einfluss auf die Pfadauswahl mithilfe von Verbindungsgewichtung"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "Einfluss auf die Pfadauswahl durch AS-Pfadvoranstellung"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "Überlegungen zu großen verteilten lokalen Netzwerken"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "Szenario 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "Aktiv/Aktiv-ExpressRoute-Verbindungen: Lösung 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "Szenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "Aktiv/Aktiv-ExpressRoute-Verbindungen: Lösung 2"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending