---
title: include file
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9d8482fdf8e914801fb77e2ab1712145fa3ccea0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077485"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Was ist der Unterschied zwischen einem Gateway für virtuelle Azure-Netzwerke (VPN Gateway) und einem Azure Virtual WAN-Gateway (vpngateway)?

Virtual WAN ermöglicht eine umfassende Site-to-Site-Konnektivität und ist auf Durchsatz, Skalierbarkeit und Benutzerfreundlichkeit ausgelegt. ExpressRoute und Point-to-Site-Konnektivität sind derzeit in der Vorschauphase. CPE-Branchgeräte werden automatisch bereitgestellt und mit Azure Virtual WAN verbunden. Diese Geräte sind über ein ständig wachsendes Ökosystem von SD-WAN- und VPN-Partnern erhältlich. Siehe die [Liste der bevorzugten Partner](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Was ist eine Branchverbindung mit Azure Virtual WAN?

Eine Verbindung zwischen einem Branchgerät und Azure Virtual WAN mit zwei Aktiv/Aktiv-IPsec-Tunneln

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Welche Geräteanbieter (Virtual WAN-Partner) werden beim Start unterstützt?

Derzeit wird die vollständig automatisierte Virtual WAN-Umgebung von vielen Partnern unterstützt. Weitere Informationen finden Sie auf der Seite mit den Informationen zu [Virtual WAN-Partnern](https://go.microsoft.com/fwlink/p/?linkid=2019615). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Was sind die Automatisierungsschritte für Virtual WAN-Partner?

Informationen zu den Automatisierungsschritten für Partner finden Sie unter [Konfigurieren von Virtual WAN-Automatisierung – für Virtual WAN-Partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Muss ich ein Gerät eines bevorzugten Partners nutzen?

Nein. Sie können ein beliebiges VPN-fähiges Gerät nutzen, das die Anforderungen von Azure für die IKEv2/IKEv1-IPsec-Unterstützung erfüllt.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Wie automatisieren Virtual WAN-Partner die Konnektivität mit Azure Virtual WAN?

Softwaredefinierte Konnektivitätslösungen verwalten ihre Branchgeräte normalerweise mithilfe eines Controllers oder über ein Center für die Gerätebereitstellung. Für den Controller können Azure-APIs verwendet werden, um die Konnektivität mit Azure Virtual WAN zu automatisieren. Weitere Informationen finden Sie unter „Konfigurieren von Virtual WAN-Automatisierung – für Virtual WAN-Partner“.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Ändern sich durch Virtual WAN vorhandene Konnektivitätsfeatures?   

Es werden keine vorhandenen Azure-Konnektivitätsfeatures geändert.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Sind neue Resource Manager-Ressourcen für Virtual WAN verfügbar?
  
Ja. Mit Virtual WAN werden neue Resource Manager-Ressourcen eingeführt. Weitere Informationen finden Sie in der entsprechenden [Übersicht](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Wie viele VPN-Geräte können sich mit einem einzelnen Hub verbinden?

Pro virtuellem Hub werden bis zu 1.000 Verbindungen unterstützt. Jede Verbindung umfasst zwei Tunnel mit einer Aktiv-Aktiv-Konfiguration. Die Tunnel enden in einem virtuellen Azure-Hub (vpngateway).

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kann das lokale VPN-Gerät eine Verbindung mit mehreren Hubs herstellen?

Ja. Der Datenverkehr erfolgt zu Beginn vom lokalen Gerät zum nächsten Microsoft-Edge und dann zum Virtual Hub.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Wird das globale VNET-Peering für Azure Virtual WAN unterstützt? 

 Nein.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Können Spoke-VNETs, die über einen virtuellen Hub verbunden sind, miteinander kommunizieren?

Ja. Spoke-VNETs können direkt über das Peering virtueller Netzwerke kommunizieren. Transitiv über den Hub kommunizierende VNETs werden jedoch nicht unterstützt. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kann ich mein bevorzugtes virtuelles Netzwerkgerät (in einem NVA-VNET) mit Azure Virtual WAN bereitstellen und verwenden?

Ja. Sie können Ihr bevorzugte VNET des virtuellen Netzwerkgeräts mit Azure Virtual WAN verbinden. Verbinden Sie zunächst das VNET des virtuellen Netzwerkgeräts über eine Hub-VNET-Verbindung mit dem Hub. Erstellen Sie dann eine Virtual Hub-Route mit einem nächsten Hop, der auf das virtuelle Gerät zeigt. Sie können der Routingtabelle des virtuellen Hubs mehrere Routen zuweisen. Alle Spokes, die mit dem VNET des virtuellen Netzwerkgeräts verbunden sind, müssen außerdem mit dem virtuellen Hub verbunden sein. Dies stellt sicher, dass die Spoke-VNET-Routen an lokale Systeme weitergegeben werden.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Kann ein NVA-VNET über ein Gateway für virtuelle Netzwerke verfügen?

Nein. Das NVA-VNET kann nicht über ein Gateway für virtuelle Netzwerke verfügen, wenn es mit dem virtuellen Hub verbunden ist. 

### <a name="is-there-support-for-bgp"></a>Ist Unterstützung für BGP vorhanden?

Ja. BGP wird unterstützt. Wenn Sie einen VPN-Standort erstellen, können Sie die BGP-Parameter darin angeben. Dies führt dazu, dass alle in Azure für diesen Standort erstellten Verbindungen für BGP aktiviert sind. Wenn Sie über ein VNET mit einem virtuellen Netzwerkgerät verfügen und das VNET des virtuellen Netzwerkgeräts an einen Virtual WAN-Hub angefügt ist, muss BGP für die Spokes, die an das VNET des virtuellen Netzwerkgeräts angefügt sind, deaktiviert werden, um sicherzustellen, dass Routen für ein VNET eines virtuellen Netzwerkgeräts richtig angekündigt werden. Verbinden Sie zusätzlich die Spoke-VNETs mit dem VNET des virtuellen Hubs, um sicherzustellen, dass die Spoke-VNET-Routen an lokale Systeme weitergegeben werden.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Kann ich Datenverkehr per UDR auf dem virtuellen Hub weiterleiten?

Ja, Sie können Datenverkehr mithilfe der Routingtabelle des virtuellen Hubs an ein VNET weiterleiten.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Sind Informationen zu Lizenzen oder Preisen für Virtual WAN vorhanden?
 
Ja. Informieren Sie sich auf der [Preisseite](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Wie wird der Preis eines Hubs berechnet?
 
Sie zahlen für den Dienst im Hub. Zehn Branches oder lokale Geräte, die eine Verbindung mit Azure Virtual WAN erforderlich machen, setzen beispielsweise voraus, dass eine Verbindung mit VPN-Endpunkten im Hub hergestellt wird. Bei einem VPN, bei dem 1 Skalierungseinheit 500 MBit/s entspricht, wird dies mit 0,361 USD/Stunde berechnet. Jede Verbindung wird mit 0,08 USD/Stunde berechnet. Für 10 Verbindungen belaufen sich die Gesamtkosten des Diensts auf 0,361 USD + 0,8 USD/Stunde. Zudem fallen Datengebühren für ausgehenden Datenverkehr von Azure an. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Wie erfolgt das Onboarding für neue Partner, die nicht in der Liste mit den Einführungspartnern aufgeführt sind?

Senden Sie eine E-Mail an azurevirtualwan@microsoft.com. Ein idealer Partner verfügt über ein Gerät, das für IKEv1- oder IKEv2-IPSec-Konnektivität bereitgestellt werden kann.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Was geschieht, wenn ein von mir verwendetes Gerät nicht in der Liste der Virtual WAN-Partner aufgeführt ist? Kann ich über dieses Gerät dennoch eine Verbindung mit dem Azure Virtual WAN-VPN herstellen?

Ja, solange das Gerät IPsec IKEv1 oder IKEv2 unterstützt. Virtual WAN-Partner automatisieren die Konnektivität vom Gerät zu Azure-VPN-Endpunkten. Dies umfasst die Automatisierung von Schritten wie beispielsweise „Hochladen von Branchinformationen“, „IPsec und Konfiguration“ und „Konnektivität“. Da Ihr Gerät nicht aus dem Ökosystem eines Virtual WAN-Partners stammt, müssen Sie die Azure-Konfiguration manuell übernehmen und Ihr Gerät aktualisieren, um eine IPsec-Verbindung einzurichten. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Ist es möglich, Azure Virtual WAN mit einer Resource Manager-Vorlage zu erstellen?

Eine einfache Konfiguration eines Virtual WAN mit einem Hub und einem VPN-Standort kann mit einer [Azure-Schnellstartvorlage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network) erstellt werden. Virtual WAN ist in erster Linie ein von REST oder vom Portal gesteuerter Dienst.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Ist die Konnektivität zwischen Branches für Virtual WAN zulässig?

Ja. Die Konnektivität zwischen Zweigstellen ist in Virtual WAN für VPN und VPN zu ExpressRoute zulässig. Site-to-Site-VPN ist allgemein verfügbar. ExpressRoute und Point-to-Site-Konnektivität sind derzeit in der Vorschauphase.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Läuft der Datenverkehr zwischen den Branches über Azure Virtual WAN?

Ja.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Inwiefern unterscheidet sich Virtual WAN vom vorhandenen Azure-Gateway für virtuelle Netzwerke?

Das VPN des Gateways für virtuelle Netzwerke ist auf 30 Tunnel begrenzt. Für Verbindungen sollten Sie bei einem größeren VPN-Umfang Virtual WAN verwenden. Sie können im Hub bis zu 1.000 Branchverbindungen mit 2 GBit/s für alle Regionen verbinden, mit Ausnahme von „USA, Westen-Mitte“. Für die Region „USA, Westen-Mitte“ sind 20 GBit/s verfügbar. In Zukunft werden auch für weitere Regionen 20 GBit/s eingeführt. Eine Verbindung ist ein Aktiv-Aktiv-Tunnel vom lokalen VPN-Gerät zum virtuellen Hub. Sie können einen Hub pro Region nutzen, sodass Sie mehr als 1.000 Branches über Hubs verbinden können.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Wie werden SD-WAN-Geräte durch Virtual WAN unterstützt?

Virtual WAN-Partner automatisieren die IPsec-Verbindung mit Azure-VPN-Endpunkten. Wenn es sich bei dem Virtual WAN-Partner um einen SD-WAN-Anbieter handelt, schließt dies ein, dass der SD-WAN-Controller die Automatisierung und IPsec-Verbindung mit Azure-VPN-Endpunkten verwaltet. Wenn das SD-WAN-Gerät anstelle des Azure-VPN einen eigenen Endpunkt für proprietäre SD-WAN-Funktionen erfordert, können Sie den SD-WAN-Endpunkt in einem Azure VNET bereitstellen, das neben Azure Virtual WAN vorhanden ist.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Ist für diese Virtual WAN-Instanz eine ExpressRoute-Verbindung mit jeder Site erforderlich?

Nein. Für diese Virtual WAN-Instanz ist keine ExpressRoute-Verbindung mit jeder Site erforderlich. Es wird IPsec-Site-to-Site-Standardkonnektivität über Internetlinks vom Gerät zu einem Azure Virtual WAN-Hub genutzt. Ihre Standorte können über eine ExpressRoute-Leitung mit einem Anbieternetzwerk verbunden sein. Für Standorte, die über ExpressRoute in Virtual Hub (in der Vorschauphase) verbunden sind, können Standorte Zweigstelle-zu-Zweigstelle-Datenverkehr zwischen VPN und ExpressRoute aufweisen. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Gibt es bei Verwendung von Azure Virtual WAN einen Grenzwert für den Netzwerkdurchsatz?

Die Anzahl von Branches ist auf 1.000 Verbindungen pro Hub/Region und auf maximal 2 G im Hub begrenzt. Eine Ausnahme stellt die Region „USA, Westen-Mitte“ dar, für die insgesamt 20 GBit/s verfügbar sind. Für die Zukunft ist die Einführung von 20 GBit/s auch für andere Regionen geplant.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Wie viele VPN-Verbindungen werden von einem Virtual WAN-Hub unterstützt?

Ein Azure Virtual WAN-Hub kann bis zu 1.000 Site-to-Site-Verbindungen und 10.000 Point-to-Site-Verbindungen gleichzeitig unterstützen.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Wie hoch ist der VPN-Gesamtdurchsatz eines VPN-Tunnels und einer Verbindung?

Der VPN-Gesamtdurchsatz eines Hubs beträgt basierend auf der ausgewählten Skalierungseinheit bis zu 20 GBit/s. Der Durchsatz wird von allen vorhandenen Verbindungen gemeinsam genutzt.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Lässt Virtual WAN für das lokale Gerät die parallele Nutzung mehrerer ISPs zu, oder wird immer nur ein VPN-Tunnel verwendet?

Eine eingehende Verbindung in das Virtual WAN-VPN ist immer ein Aktiv/Aktiv-Tunnel (für Resilienz innerhalb desselben Hubs oder derselben Region), für den eine im Branch verfügbare Verbindung verwendet wird. Bei dieser Verbindung kann es sich um eine ISP-Verbindung im lokalen Branch handeln. Virtual WAN umfasst keine spezielle Logik, um mehrere ISPs parallel einzurichten. Die Verwaltung eines Failovers über ISP im Branch ist ein vollständig branchbasierter Netzwerkvorgang. Für die Pfadauswahl im Branch können Sie Ihre bevorzugte SD-WAN-Lösung verwenden.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Wie wird Datenverkehr über den Azure-Backbone geleitet?

Für den Datenverkehr wird das folgende Muster verwendet: Branchgerät > ISP > Microsoft Edge > Microsoft-Domänencontroller (Hub-VNET) > Microsoft Edge > ISP > Branchgerät

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Was wird bei diesem Modell für jede Site benötigt? Nur eine Internetverbindung?

Ja. Eine Internetverbindung und ein physisches Gerät, das IPsec unterstützt – vorzugsweise von einem unserer integrierten [Partner](https://go.microsoft.com/fwlink/p/?linkid=2019615). Sie können optional die Konfiguration und Konnektivität mit Azure auf Ihrem bevorzugten Gerät manuell verwalten.
