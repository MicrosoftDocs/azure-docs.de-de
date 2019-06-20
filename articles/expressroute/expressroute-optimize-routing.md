---
title: 'Optimieren des Routings – ExpressRoute-Verbindungen: Azure | Microsoft-Dokumentation'
description: Diese Seite enthält Informationen zur Optimierung des Routings, wenn Sie über mehr als eine ExpressRoute-Verbindung verfügen, mit der die Verbindung zwischen Microsoft und Ihrem Unternehmensnetzwerk hergestellt werden kann.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 65c23b05cfcb623f8e2870df813f5516b3039d5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60883519"
---
# <a name="optimize-expressroute-routing"></a>Optimieren von ExpressRoute-Routing
Wenn Sie mehrere ExpressRoute-Verbindungen nutzen, verfügen Sie über mehr als einen Weg zur Herstellung einer Verbindung mit Microsoft. Dies kann ein suboptimales Routing zur Folge haben. Es kann also sein, dass Ihr Datenverkehr für den Weg zu Microsoft und von Microsoft in Ihr Netzwerk mehr Zeit benötigt. Je länger der Netzwerkpfad, desto höher die Latenz. Die Latenz wirkt sich direkt auf die Anwendungsleistung und die Benutzerfreundlichkeit aus. In diesem Artikel wird dieses Problem veranschaulicht, und es wird beschrieben, wie Sie das Routing mit den standardmäßigen Routingtechnologien optimieren.

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Suboptimales Routing (Kunde an Microsoft)
Wir sehen uns nun ein Beispiel zum Routingproblem an. Angenommen, Sie verfügen über zwei Niederlassungen in den USA: eine in Los Angeles und eine in New York. Die Niederlassungen sind über ein Wide Area Network (WAN) verbunden, wobei es sich entweder um Ihr eigenes Backbonenetzwerk oder die IP-VPN-Verbindung Ihres Service Providers handeln kann. Sie verfügen über zwei ExpressRoute-Verbindungen – eine in der Region „USA, Westen“ und eine in der Region „USA, Osten“ –, die ebenfalls per WAN verbunden sind. Es sind also zwei Wege zum Herstellen der Verbindung mit dem Microsoft-Netzwerk vorhanden. Nehmen Sie weiter an, dass Sie über jeweils eine Azure-Bereitstellung (z.B. Azure App Service) in „USA, Westen“ und „USA, Osten“ verfügen. Sie möchten die Benutzer in Los Angeles mit Azure in der Region „USA, Westen“ und die Benutzer in New York mit Azure in der Region „USA, Osten“ verbinden, da durch Ihren Dienstadministrator vorgesehen ist, dass Benutzer in beiden Niederlassungen auf die Azure-Dienste in ihrer Nähe zugreifen. So soll eine optimale Leistung erzielt werden. Leider funktioniert dieser Plan nur für die Benutzer an der Ostküste gut, aber nicht für die Benutzer an der Westküste. Die Ursache des Problems ist Folgendes: Für jede ExpressRoute-Verbindung kündigen wir für Sie sowohl das Präfix in der Azure-Region „USA, Osten“ (23.100.0.0/16) als auch das Präfix in der Azure-Region „USA, Westen“ (13.100.0.0/16) an. Wenn Sie nicht wissen, welches Präfix für welche Region gilt, können Sie die Präfixe auch nicht unterschiedlich behandeln. Für Ihr WAN kann der Eindruck entstehen, dass beide Präfixe näher an „USA, Osten“ als an „USA, Westen“ liegen, sodass die Benutzer beider Niederlassungen an die ExpressRoute-Verbindung in der Region „USA, Osten“ geleitet werden. Das Ergebnis ist, dass es in der Niederlassung in Los Angeles viele unzufriedene Benutzer gibt.

![ExpressRoute-Fall 1 – Problem: Suboptimales Routing (Kunde an Microsoft)](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Lösung: Verwenden von BGP-Communitys
Zum Optimieren des Routings für die Benutzer beider Niederlassungen müssen Sie wissen, welches Präfix für die Azure-Region „USA, Westen“ und welches Präfix für die Region „USA, Osten“ gilt. Wir codieren diese Informationen mithilfe von [BGP-Communitywerten](expressroute-routing.md). Wir haben jeder Azure-Region einen eindeutigen BGP-Communitywert zugewiesen, z.B. „12076:51004“ für „USA, Osten“ und „12076:51006“ für „USA, Westen“. Da Sie jetzt wissen, welches Präfix für welche Azure-Region gilt, können Sie konfigurieren, welche ExpressRoute-Verbindung vorgezogen werden sollte. Da wir BGP zum Austauschen von Routinginformationen verwenden, können Sie die lokale Einstellung von BGP nutzen, um das Routing zu beeinflussen. In unserem Beispiel können Sie 13.100.0.0/16 in „USA, Westen“ einen höheren Wert für die lokale Einstellung als für „USA, Osten“ zuweisen (bzw. einen höheren Wert für die lokale Einstellung für 23.100.0.0/16 in „USA, Osten“ als in „USA, Westen“). Mit dieser Konfiguration wird Folgendes sichergestellt: Wenn beide Wege zu Microsoft verfügbar sind, verwenden die Benutzer in Los Angeles die ExpressRoute-Verbindung in „USA, Westen“ zum Herstellen der Verbindung mit Azure „USA, Westen“, während die Benutzer in New York die ExpressRoute-Verbindung in „USA, Osten“ zum Herstellen der Verbindung mit Azure „USA, Osten“ verwenden. Das Routing ist somit auf beiden Seiten optimiert. 

![ExpressRoute-Fall 1 – Lösung: Verwenden von BGP-Communitys](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> Das gleiche Verfahren kann unter Verwendung der lokalen Einstellung (Local Preference) auch auf die Weiterleitung vom Kunden zum Azure Virtual Network angewendet werden. Wir verknüpfen den BGP-Community-Wert nicht mit den Präfixen, die von Azure für Ihr Netzwerk angekündigt werden. Da Sie aber wissen, welche Ihrer Bereitstellungen des virtuellen Netzwerks sich jeweils in der Nähe der einzelnen Büros befindet, können Sie Ihre Router entsprechend konfigurieren, damit Sie eine ExpressRoute-Verbindung einer anderen Verbindung vorziehen können.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Suboptimales Routing (Microsoft an Kunde)
Dies ist ein weiteres Beispiel, bei dem Verbindungen von Microsoft einen längeren Weg zum Erreichen Ihres Netzwerks zurücklegen. In diesem Fall verwenden Sie lokale Exchange-Server und Exchange Online in einer [Hybridumgebung](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Ihre Niederlassungen sind mit einem WAN verbunden. Sie kündigen die Präfixe Ihrer lokalen Server in beiden Niederlassungen gegenüber Microsoft über die beiden ExpressRoute-Verbindungen an. Exchange Online initiiert Verbindungen mit den lokalen Servern in Fällen wie der Postfachmigration. Unglücklicherweise wird die Verbindung mit der Niederlassung in Los Angeles an die ExpressRoute-Verbindung in „USA, Osten“ geleitet, bevor der Web über den ganzen Kontinent zurück an die Westküste zurückgelegt wird. Die Ursache des Problems ist ähnlich wie im ersten Fall. Ohne entsprechenden Hinweis kann das Microsoft-Netzwerk nicht ermitteln, welches Kundenpräfix näher an „USA, Osten“ und welches näher an „USA, Westen“ liegt. So wird der falsche Weg zu Ihrer Niederlassung in Los Angeles gewählt.

![ExpressRoute-Fall 2: Suboptimales Routing (Microsoft an Kunde)](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Lösung: Voranstellen von AS PATH
Es gibt zwei Lösungen des Problems. Die erste Lösung besteht darin, dass Sie einfach das lokale Präfix 177.2.0.0/31 für die Niederlassung in Los Angeles unter der ExpressRoute-Verbindung in „USA, Westen“ und das lokale Präfix 177.2.0.2/31 für die Niederlassung in New York unter der ExpressRoute-Verbindung in „USA, Osten“ ankündigen. Das Ergebnis ist, dass für Microsoft nur noch ein Weg zum Herstellen der Verbindung mit Ihren Niederlassungen vorhanden ist. Die Mehrdeutigkeit wurde beseitigt, und das Routing wurde optimiert. Bei diesem Aufbau müssen Sie sich Gedanken über Ihre Failoverstrategie machen. Falls der Weg zu Microsoft per ExpressRoute unterbrochen wird, müssen Sie sicherstellen, dass Exchange Online weiterhin eine Verbindung mit Ihren lokalen Servern herstellen kann. 

Die zweite Lösung besteht darin, weiterhin beide Präfixe unter beiden ExpressRoute-Verbindungen anzukündigen und darauf hinzuweisen, welches Präfix in der Nähe welcher Niederlassung liegt. Da die BGP-Voranstellung von AS Path unterstützt wird, können Sie AS Path für Ihr Präfix konfigurieren, um das Routing zu beeinflussen. In diesem Beispiel können Sie AS PATH für 172.2.0.0/31 in „USA, Osten“ verlängern, damit die ExpressRoute-Verbindung in „USA, Westen“ für Datenverkehr vorgezogen wird, der für dieses Präfix bestimmt ist (da von unserem Netzwerk angenommen wird, dass der Weg zu diesem Präfix im Westen kürzer ist). Auf ähnliche Weise können Sie AS PATH für 172.2.0.2/31 in „USA, Westen“ verlängern, damit von uns die ExpressRoute-Verbindung in „USA, Osten“ vorgezogen wird. Das Routing ist dann für beide Büros optimiert. Wenn bei diesem Aufbau eine ExpressRoute-Verbindung unterbrochen wird, kann Exchange Online Sie trotzdem noch über eine andere ExpressRoute-Verbindung und Ihr WAN erreichen. 

> [!IMPORTANT]
> Wir entfernen private AS-Nummern in AS PATH für die unter Microsoft-Peering empfangenen Präfixe. Sie müssen in AS PATH öffentliche AS-Nummern anfügen, um das Routing für Microsoft-Peering zu beeinflussen.
> 
> 

![ExpressRoute-Fall 2 – Lösung: Voranstellen von AS PATH](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Die hier aufgeführten Beispiele gelten zwar für Microsoft-Peering und öffentliches Peering, für privates Peering werden jedoch die gleichen Funktionen unterstützt. Das Voranstellen von AS PATH funktioniert darüber hinaus in einer einzelnen ExpressRoute-Verbindung, sodass die Auswahl der primären und sekundären Pfade beeinflusst werden kann.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Suboptimales Routing zwischen virtuellen Netzwerken
Mit ExpressRoute können Sie die Kommunikation von virtuellem Netzwerk zu virtuellem Netzwerk („VNet“) ermöglichen, indem Sie diese mit einer ExpressRoute-Verbindung verknüpfen. Bei einer Verknüpfung mit mehreren ExpressRoute-Verbindungen kann das suboptimale Routing zwischen den VNets erfolgen. Wir sehen uns hierzu ein Beispiel an. Sie verfügen über zwei ExpressRoute-Verbindungen, eine in „USA, Westen“ und eine in „USA, Osten“. In jeder Region sind zwei VNets vorhanden. Ihre Webserver werden in dem einem VNet und die Anwendungsserver im anderen VNet bereitgestellt. Aus Redundanzgründen verknüpfen Sie die beiden VNets in jeder Region sowohl mit der lokalen ExpressRoute-Verbindung als auch mit der ExpressRoute-Verbindung am Remotestandort. Wie unten zu sehen ist, führen von jedem VNet zwei Pfade zum anderen VNet. Die VNets haben keine Informationen dazu, welche ExpressRoute-Verbindung lokal und welche remote ist. Da das ECMP-Routing (Equal-Cost-Multi-Path) durchgeführt wird, um den Lastenausgleich für Datenverkehr zwischen VNets zu ermöglichen, werden für einige Datenverkehrsabläufe der längere Pfad und die Weiterleitung über die ExpressRoute-Remoteverbindung gewählt.

![ExpressRoute-Fall 3: Suboptimales Routing zwischen virtuellen Netzwerken](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Lösung: Zuweisen einer Verbindung vom Typ „Hohe Gewichtung zu lokal“
Die Lösung ist einfach. Da Sie wissen, wo sich die VNets und die Verbindungen befinden, können Sie uns mitteilen, welcher Pfad für die einzelnen VNets jeweils bevorzugt genutzt werden soll. Speziell für dieses Beispiel gilt, dass Sie der lokalen Verbindung eine höhere Gewichtung als der Remoteverbindung verleihen (siehe Konfigurationsbeispiel [hier](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection)). Wenn ein VNet das Präfix des anderen VNet über mehrere Verbindungen empfängt, wird die Verbindung mit der höchsten Gewichtung bevorzugt zum Senden von Datenverkehr verwendet, der für dieses Präfix bestimmt ist.

![ExpressRoute-Fall 3 – Lösung: Zuweisen einer hohen Gewichtung zur lokalen Verbindung](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> Außerdem können Sie die Weiterleitung aus dem VNet an Ihr lokales Netzwerk beeinflussen, wenn Sie über mehrere ExpressRoute-Verbindungen verfügen, indem Sie die Gewichtung einer Verbindung konfigurieren, anstatt das Voranstellen von AS PATH anzuwenden. Diese Vorgehensweise wird oben im zweiten Szenario beschrieben. Für jedes Präfix sehen wir uns vor der AS PATH-Länge immer die Gewichtung der Verbindung an, wenn die Entscheidung darüber getroffen werden soll, wie der Datenverkehr gesendet wird.
>
>
