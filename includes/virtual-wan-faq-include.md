---
title: include file
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d275f2f4cad83c8f378c471e5a3fa1313d6c0e7d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99624867"
---
### <a name="is-azure-virtual-wan-in-ga"></a>Ist Azure Virtual WAN allgemein verfügbar?

Ja, Azure Virtual WAN ist allgemein verfügbar. Virtual WAN besteht jedoch aus mehreren Funktionen und Szenarien. In Virtual WAN gibt es Funktionen oder Szenarien, für die Microsoft das Vorschautag anwendet. In diesen Fällen befindet sich die jeweilige Funktion oder das Szenario selbst in der Vorschauphase. Wenn Sie keine bestimmte Previewfunktion verwenden, gilt die reguläre Unterstützung für die allgemeine Verfügbarkeit. Weitere Informationen zur Unterstützung der Vorschau finden Sie unter [Zusätzliche Nutzungsbedingungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Muss der Benutzer über eine Hub-and-Spoke-Anordnung mit SD-WAN/VPN-Geräten verfügen, um Azure Virtual WAN nutzen zu können?

Virtual WAN verfügt über viele Funktionen, die in einer zentralen Benutzeroberfläche zusammengefasst sind, z. B. Site-/Site-to-Site-VPN-Konnektivität, Benutzer-/P2S-Konnektivität, ExpressRoute-Konnektivität, Virtual Network-Konnektivität, VPN/ExpressRoute-Konnektivität, transitive VNET-zu-VNET-Konnektivität, zentralisiertes Routing, Azure Firewall- und Firewall Manager-Sicherheit, Überwachung, ExpressRoute-Verschlüsselung und viele mehr. Sie müssen nicht all diese Anwendungsfälle abdecken, um mit der Nutzung von Virtual WAN beginnen zu können. Sie können mit nur einem Anwendungsfall starten.

Die Virtual WAN-Architektur ist eine Hub-and-Spoke-Architektur mit integrierter Skalierung und Leistung, wobei Branches (VPN/SD-WAN-Geräte), Benutzer (Azure-VPN-, openVPN- oder IKEv2-Clients), ExpressRoute-Leitungen und virtuelle Netzwerke als „Spokes“ für virtuelle Hubs dienen. Alle Hubs sind per Standard-Virtual WAN vollständig miteinander vernetzt, damit Benutzer den Microsoft-Backbone für die Any-to-Any-Konnektivität (alle Spokes) nutzen können. Zur Nutzung einer Hub-and-Spoke-Anordnung mit SD-WAN/VPN-Geräten können Benutzer dies entweder manuell im Azure Virtual WAN-Portal einrichten oder CPE für Virtual WAN-Partner (SD-WAN/VPN) nutzen, um die Konnektivität mit Azure herzustellen.

Virtual WAN-Partner ermöglichen die Automatisierung in Bezug auf die Konnektivität. Hierbei handelt es sich um eine Option zum Exportieren der Geräteinformationen nach Azure, Herunterladen der Azure-Konfiguration und Herstellen der Konnektivität mit dem Azure Virtual WAN-Hub. Für die Point-to-Site/Benutzer-VPN-Konnektivität unterstützen wir den [Azure-VPN-](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN- und IKEv2-Client.

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Können Sie vollständig vermaschte Hubs in einem Virtual WAN deaktivieren?

Virtual WAN ist in zwei Varianten verfügbar: Basic und Standard. In Virtual WAN vom Typ „Basic“ sind die Hubs nicht vermascht. In einem Virtual WAN vom Typ „Standard“ werden die Hubs vermascht und automatisch verbunden, wenn das virtuelle WAN zum ersten Mal eingerichtet wird. Für den Benutzer sind keine bestimmten Schritte erforderlich. Der Benutzer muss die Funktionalität auch nicht deaktivieren oder aktivieren, um vermaschte Hubs zu erhalten. Virtual WAN bietet Ihnen viele Routingoptionen zum Steuern des Datenverkehrs zwischen beliebigen Spokes (VNet, VPN oder ExpressRoute). Es bietet die Einfachheit vollständig vermaschter Hubs und auch die Flexibilität, Datenverkehr gemäß Ihren Anforderungen weiterzuleiten.

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Wie werden Verfügbarkeitszonen und Resilienz in Virtual WAN gehandhabt?

Virtual WAN ist eine Sammlung von Hubs und Diensten, die innerhalb des Hubs zur Verfügung gestellt werden. Der Benutzer kann gemäß seinen Anforderungen beliebig viele Virtual WAN-Instanzen besitzen. In einem Virtual WAN-Hub gibt es mehrere Dienste wie VPN, ExpressRoute usw. Jeder dieser Dienste (mit Ausnahme der Azure Firewall) wird in einer Region der Verfügbarkeitszonen eingesetzt, sofern die Region Verfügbarkeitszonen unterstützt. Wenn eine Region nach der anfänglichen Bereitstellung im Hub zu einer Verfügbarkeitszone wird, kann der Benutzer die Gateways neu erstellen, wodurch eine Bereitstellung der Verfügbarkeitszone ausgelöst wird. Alle Gateways werden in einem Hub als „Aktiv/Aktiv“ zur Verfügung gestellt, was bedeutet, dass in einem Hub die Resilienz integriert ist. Benutzer können Verbindungen mit mehreren Hubs herstellen, wenn sie eine regionsübergreifende Resilienz wünschen.

Obwohl das Konzept von Virtual WAN global ist, basiert die eigentliche virtuelle WAN-Ressource auf dem Resource Manager und wird regional bereitgestellt. Falls die virtuelle WAN-Region selbst ein Problem aufweisen sollte, werden alle Hubs in diesem virtuellen WAN weiterhin unverändert funktionieren, aber der Benutzer kann keine neuen Hubs erstellen, bis die virtuelle WAN-Region verfügbar ist.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Welcher Client wird für Azure Virtual WAN-Benutzer-VPN (Point-to-Site) unterstützt?

Virtual WAN unterstützt den [Azure-VPN-](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN- oder einen beliebigen IKEv2-Client. Die Azure AD-Authentifizierung wird mit dem Azure-VPN-Client unterstützt. Es ist mindestens ein Windows 10-Client mit der Betriebssystemversion 17763.0 oder höher erforderlich.  OpenVPN-Clients können die zertifikatbasierte Authentifizierung unterstützen. Nachdem auf dem Gateway die zertifikatbasierte Authentifizierung ausgewählt wurde, wird die OVPN-Datei zum Herunterladen Ihres Geräts angezeigt. IKEv2 unterstützt sowohl die Zertifikat- als auch die RADIUS-Authentifizierung. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Für Benutzer-VPN (Point-to-Site): Warum ist der P2S-Clientpool in zwei Routen unterteilt?

Jedes Gateway verfügt über zwei Instanzen. Die Aufteilung wird durchgeführt, damit jede Gatewayinstanz separat IP-Clientadressen für verbundene Clients zuordnen und der Datenverkehr aus dem virtuellen Netzwerk zurück an die richtige Gatewayinstanz geleitet wird, um Instanz-Hops zwischen Gateways zu vermeiden.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Wie füge ich DNS-Server für P2S-Clients hinzu?

Es gibt zwei Optionen zum Hinzufügen von DNS-Servern für die P2S-Clients. Die erste Methode wird bevorzugt, da sie die benutzerdefinierten DNS-Server dem Gateway und nicht dem Client hinzufügt.

1. Verwenden Sie das folgende PowerShell-Skript, um die benutzerdefinierten DNS-Server hinzuzufügen. Ersetzen Sie die Werte für Ihre Umgebung.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. Falls Sie Azure VPN Client für Windows 10 verwenden, können Sie die heruntergeladene XML-Profildatei ändern und vor dem Importieren die Tags **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** hinzufügen.

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Für Benutzer-VPN (Point-to-Site): Wie viele Clients werden unterstützt?

Jedes P2S-Gateway eines Benutzer-VPN verfügt über zwei Instanzen, und jede Instanz unterstützt jeweils eine bestimmte maximale Anzahl von Benutzern, wenn sich die Skalierungseinheit ändert. Für Skalierungseinheit 1 bis 3 werden 500 Verbindungen, für Skalierungseinheit 4 bis 6 werden 1.000 Verbindungen, für Skalierungseinheit 7 bis 12 werden 5.000 Verbindungen, und für Skalierungseinheit 13 bis 18 werden bis zu 10.000 Verbindungen unterstützt.

Ein Beispiel: Angenommen, der Benutzer wählt eine Skalierungseinheit aus. Jede Skalierungseinheit steht für ein bereitgestelltes Aktiv/Aktiv-Gateway, und jede Instanz (in diesem Fall zwei) unterstützt bis zu 500 Verbindungen. Da Sie 500 Verbindungen * 2 pro Gateway erhalten können, bedeutet dies nicht, dass Sie 1000 (statt der 500) für diese Skalierungseinheit einplanen. Möglicherweise müssen Instanzen gewartet werden, wobei die Konnektivität für die zusätzlichen 500 unterbrochen werden kann, wenn Sie die empfohlene Anzahl von Verbindungen überschreiten. Planen Sie darüber hinaus auch Ausfallzeit ein, falls Sie für die Skalierungseinheit das Hoch- oder Herunterskalieren durchführen oder die Point-to-Site-Konfiguration auf dem VPN-Gateway ändern möchten.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Worin besteht der Unterschied zwischen einem virtuellen Azure-Netzwerkgateway (VPN-Gateway) und einem Azure Virtual WAN-VPN-Gateway?

Virtual WAN ermöglicht eine umfassende Site-to-Site-Konnektivität und ist auf Durchsatz, Skalierbarkeit und Benutzerfreundlichkeit ausgelegt. Wenn Sie einen Standort mit einem Virtual WAN-VPN-Gateway verbinden, unterscheidet sich dies von einem regulären Gateway für virtuelle Netzwerke, für das der Gatewaytyp „VPN“ verwendet wird. Wenn Sie eine ExpressRoute-Leitung mit einem virtuellen WAN-Hub verbinden, wird eine andere Ressource für das ExpressRoute-Gateway als für das reguläre Gateway für virtuelle Netzwerke mit dem Gatewaytyp „ExpressRoute“ verwendet. 

Beim virtuellen WAN wird sowohl für VPN als auch für ExpressRoute ein aggregierter Durchsatz von bis zu 20 GBit/s unterstützt. Das virtuelle WAN verfügt auch über eine Automatisierung in Bezug auf die Konnektivität mit einem Ökosystem aus CPE-Branchgerät-Partnern. CPE-Branchgeräte weisen eine integrierte Automatisierung auf, die automatisch bereitgestellt wird und für die eine Verbindung mit Azure Virtual WAN hergestellt wird. Diese Geräte sind über ein ständig wachsendes Ökosystem von SD-WAN- und VPN-Partnern erhältlich. Siehe die [Liste der bevorzugten Partner](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Inwiefern unterscheidet sich Virtual WAN von einem Azure-Gateway für virtuelle Netzwerke?

Das VPN des Gateways für virtuelle Netzwerke ist auf 30 Tunnel begrenzt. Für Verbindungen sollten Sie bei einem größeren VPN-Umfang Virtual WAN verwenden. Sie können bis zu 1.000 Branchverbindungen pro Region (virtueller Hub) mit einer Aggregierung von 20 GBit/s pro Hub verbinden. Eine Verbindung ist ein Aktiv-Aktiv-Tunnel vom lokalen VPN-Gerät zum virtuellen Hub. Sie können einen Hub pro Region nutzen, sodass Sie mehr als 1.000 Branches über Hubs verbinden können.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Was ist eine Virtual WAN-Gatewayskalierungseinheit?

Eine Skalierungseinheit ist eine Einheit, die zum Auswählen eines aggregierten Durchsatzes eines Gateways im virtuellen Hub definiert wird. 1 VPN-Skalierungseinheit = 500 MBit/s. 1 ExpressRoute-Skalierungseinheit = 2 GBits/s. Beispiel: Für 10 VPN-Skalierungseinheiten gilt demnach Folgendes: 500 MBit/s · 10 = 5 GBit/s.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Welche Geräteanbieter (Virtual WAN-Partner) werden unterstützt?

Derzeit wird die vollständig automatisierte Virtual WAN-Umgebung von vielen Partnern unterstützt. Weitere Informationen finden Sie auf der Seite mit den Informationen zu [Virtual WAN-Partnern](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Was sind die Automatisierungsschritte für Virtual WAN-Partner?

Informationen zu den Automatisierungsschritten für Partner finden Sie unter [Konfigurieren von Virtual WAN-Automatisierung – für Virtual WAN-Partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Muss ich ein Gerät eines bevorzugten Partners nutzen?

Nein. Sie können ein beliebiges VPN-fähiges Gerät nutzen, das die Anforderungen von Azure für die IKEv2/IKEv1-IPsec-Unterstützung erfüllt. Virtual WAN verfügt auch über CPE-Partnerlösungen, die die Konnektivität zu Azure Virtual WAN automatisieren und so die Einrichtung von IPsec-VPN-Verbindungen im großem Stil erleichtern.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Wie automatisieren Virtual WAN-Partner die Konnektivität mit Azure Virtual WAN?

Softwaredefinierte Konnektivitätslösungen verwalten ihre Branchgeräte normalerweise mithilfe eines Controllers oder über ein Center für die Gerätebereitstellung. Für den Controller können Azure-APIs verwendet werden, um die Konnektivität mit Azure Virtual WAN zu automatisieren. Die Automatisierung umfasst das Hochladen von Branchinformationen, Herunterladen der Azure-Konfiguration, Einrichten von IPSec-Tunneln zu virtuellen Azure-Hubs und automatische Einrichten der Konnektivität vom Branchgerät zu Azure Virtual WAN. Wenn Sie über Hunderte von Branches verfügen, ist das Verbinden über Virtual WAN-CPE-Partner einfach, weil aufgrund des Onboardingverfahrens das aufwändige Einrichten, Konfigurieren und Verwalten der IPsec-Konnektivität entfällt. Weitere Informationen finden Sie unter [Konfigurieren von Virtual WAN-Automatisierung – für Virtual WAN-Partner (Vorschauversion)](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Was geschieht, wenn ein von mir verwendetes Gerät nicht in der Liste der Virtual WAN-Partner aufgeführt ist? Kann ich über dieses Gerät dennoch eine Verbindung mit dem Azure Virtual WAN-VPN herstellen?

Ja, solange das Gerät IPsec IKEv1 oder IKEv2 unterstützt. Virtual WAN-Partner automatisieren die Konnektivität vom Gerät zu Azure-VPN-Endpunkten. Dies umfasst die Automatisierung von Schritten wie beispielsweise „Hochladen von Branchinformationen“, „IPsec und Konfiguration“ und „Konnektivität“. Da Ihr Gerät nicht aus dem Ökosystem eines Virtual WAN-Partners stammt, müssen Sie die Azure-Konfiguration manuell übernehmen und Ihr Gerät aktualisieren, um eine IPsec-Verbindung einzurichten.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Wie erfolgt das Onboarding für neue Partner, die nicht in der Liste mit den Einführungspartnern aufgeführt sind?

Alle virtuellen WAN-APIs sind offene APIs. Sie können zur Dokumentation [Automatisierung für Virtual WAN-Partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) wechseln, um die technische Machbarkeit zu beurteilen. Ein idealer Partner verfügt über ein Gerät, das für IKEv1- oder IKEv2-IPSec-Konnektivität bereitgestellt werden kann. Nachdem das Unternehmen die Automatisierungsarbeiten für sein CPE-Gerät auf der Grundlage der oben genannten Automatisierungsrichtlinien abgeschlossen hat, können Sie sich an azurevirtualwan@microsoft.com wenden, um hier aufgeführt zu werden: [Konnektivität über Partner]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Wenn Sie ein Kunde sind, der eine bestimmte Unternehmenslösung als Virtual WAN-Partner aufgeführt haben möchte, fordern Sie das Unternehmen auf, sich mit Virtual WAN in Verbindung zu setzen, indem Sie eine E-Mail an azurevirtualwan@microsoft.com senden.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Wie werden SD-WAN-Geräte durch Virtual WAN unterstützt?

Virtual WAN-Partner automatisieren die IPsec-Verbindung mit Azure-VPN-Endpunkten. Wenn es sich bei dem Virtual WAN-Partner um einen SD-WAN-Anbieter handelt, schließt dies ein, dass der SD-WAN-Controller die Automatisierung und IPsec-Verbindung mit Azure-VPN-Endpunkten verwaltet. Wenn das SD-WAN-Gerät anstelle des Azure-VPN einen eigenen Endpunkt für proprietäre SD-WAN-Funktionen erfordert, können Sie den SD-WAN-Endpunkt in einem Azure VNET bereitstellen, das neben Azure Virtual WAN vorhanden ist.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Wie viele VPN-Geräte können sich mit einem einzelnen Hub verbinden?

Pro virtuellem Hub werden bis zu 1.000 Verbindungen unterstützt. Jede Verbindung besteht aus vier Verknüpfungen, und jede Verknüpfungsverbindung unterstützt zwei Tunnel mit einer Aktiv/Aktiv-Konfiguration. Die Tunnel enden in einem virtuellen Azure-Hub (VPN-Gateway). Links stellen die physische ISP-Verbindung am Branch/VPN-Gerät dar.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Was ist eine Branchverbindung mit Azure Virtual WAN?

Bei einer Verbindung zwischen einem Branch oder VPN-Gerät und Azure Virtual WAN handelt es sich um eine VPN-Verbindung, die den VPN-Standort und die Azure VPN Gateway-Instanz in einem virtuellen Hub virtuell miteinander verbindet.

### <a name="what-happens-if-the-on-premises-vpn-device-only-has-1-tunnel-to-an-azure-virtual-wan-vpn-gateway"></a>Was passiert, wenn das lokale VPN-Gerät nur über einen einzelnen Tunnel zu einem Azure Virtual WAN-VPN-Gateway verfügt?

Eine Azure Virtual WAN-Verbindung umfasst zwei Tunnel. Ein Virtual WAN-VPN-Gateway wird auf einem virtuellen Hub im Aktiv/Aktiv-Modus bereitgestellt, was impliziert, dass separate Tunnel von lokalen Geräten vorhanden sind, die in separaten Instanzen enden. Dies ist die Empfehlung für alle Benutzer. Wenn der Benutzer allerdings nur einen einzelnen Tunnel mit einer der Instanzen des Virtual WAN-VPN-Gateways verwenden möchte, gilt Folgendes: Falls die Gatewayinstanz aus irgendeinem Grund (Wartung, Patching usw.) in den Offlinezustand versetzt wird, wird der Tunnel auf die sekundäre aktive Instanz verlagert, wodurch es für den Benutzer zu einer erneuten Verbindungsherstellung kommen kann. BGP-Sitzung werden nicht instanzübergreifend verschoben.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kann vom lokalen VPN-Gerät eine Verbindung mit mehreren Hubs hergestellt werden?

Ja. Der Datenverkehr erfolgt zu Beginn vom lokalen Gerät zum nächsten Microsoft-Netzwerkedge und dann zum virtuellen Hub.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Sind neue Resource Manager-Ressourcen für Virtual WAN verfügbar?
  
Ja. Virtual WAN verfügt über neue Resource Manager-Ressourcen. Weitere Informationen finden Sie in der entsprechenden [Übersicht](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kann ich mein bevorzugtes virtuelles Netzwerkgerät (in einem NVA-VNET) mit Azure Virtual WAN bereitstellen und verwenden?

Ja. Sie können Ihr bevorzugte VNET des virtuellen Netzwerkgeräts mit Azure Virtual WAN verbinden.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Kann ich ein virtuelles Netzwerkgerät innerhalb des virtuellen Hubs erstellen?

Ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) kann nicht innerhalb eines virtuellen Hubs bereitgestellt werden. Sie können es jedoch in einem Spoke-VNET erstellen, das mit dem virtuellen Hub verbunden ist, und ein geeignetes Routing für direkten Datenverkehr nach Ihren Anforderungen ermöglichen.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Kann ein Spoke-VNET über ein Gateway für virtuelle Netzwerke verfügen?

Nein. Das Spoke-VNET kann nicht über ein Gateway für virtuelle Netzwerke verfügen, wenn es mit dem virtuellen Hub verbunden ist.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Gibt es Unterstützung für BGP bei VPN-Konnektivität?

Ja. BGP wird unterstützt. Wenn Sie einen VPN-Standort erstellen, können Sie die BGP-Parameter darin angeben. Dies führt dazu, dass alle in Azure für diesen Standort erstellten Verbindungen für BGP aktiviert sind.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Sind Informationen zu Lizenzen oder Preisen für Virtual WAN vorhanden?

Ja. Informieren Sie sich auf der [Preisseite](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Ist es möglich, Azure Virtual WAN mit einer Resource Manager-Vorlage zu erstellen?

Eine einfache Konfiguration eines Virtual WAN mit einem Hub und einem VPN-Standort kann mit einer [Schnellstartvorlage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network) erstellt werden. Virtual WAN ist in erster Linie ein von REST oder vom Portal gesteuerter Dienst.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Können Spoke-VNETs, die über einen virtuellen Hub verbunden sind, miteinander kommunizieren (V2V-Transit)?

Ja. Eine Virtual WAN-Instanz vom Typ „Standard“ unterstützt die transitive VNET-zu-VNET-Konnektivität über den Virtual WAN-Hub, mit dem die VNETs verbunden sind. Im Virtual WAN-Kontext werden diese Pfade für VNETs, die mit einem Virtual WAN-Hub in einer einzelnen Region verbunden sind, als „lokale Virtual WAN-VNET-Übertragung“ bezeichnet. Für VNETs, die über mehrere Virtual WAN-Hubs in mindestens zwei Regionen verbunden sind, werden sie als „globale Virtual WAN-VNET-Übertragung“ bezeichnet.

In einigen Szenarien können Spoke-VNETs auch mittels direktem Peering miteinander verbunden werden. Dazu wird zusätzlich zu lokaler oder globaler Virtual WAN-VNET-Übertragung das [Peering virtueller Netzwerke](../articles/virtual-network/virtual-network-peering-overview.md) verwendet. In diesem Fall hat das VNET-Peering Vorrang vor der transitiven Verbindung über den Virtual WAN-Hub.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Ist die Konnektivität zwischen Branches für Virtual WAN zulässig?

Ja. Die Konnektivität zwischen Branches ist für Virtual WAN zulässig. Branch ist konzeptionell anwendbar auf VPN-Standort, ExpressRoute-Leitungen oder Point-to-Site-/Benutzer-VPN-Benutzer. Branch zu Branch ist standardmäßig aktiviert. Die entsprechende Einstellung befindet sich in der **WAN-Konfiguration**. Dies ermöglicht VPN-Branches/-Benutzern die Verbindungsherstellung mit anderen VPN-Branches und ermöglicht außerdem Transitkonnektivität zwischen VPN- und ExpressRoute-Benutzern.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Verläuft der Datenverkehr zwischen den Branches über Azure Virtual WAN?

Ja. Datenverkehr zwischen Branches durchläuft Azure Virtual WAN.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Ist für die Virtual WAN-Instanz eine ExpressRoute-Verbindung mit jeder Site erforderlich?

Nein. Für Virtual WAN ist keine ExpressRoute-Verbindung mit jedem Standort erforderlich. Ihre Standorte können über eine ExpressRoute-Leitung mit einem Anbieternetzwerk verbunden sein. Für Standorte, die sowohl über ExpressRoute mit einem virtuellen Hub als auch über ein IPsec-VPN mit dem gleichen Hub verbunden sind, bietet der virtuelle Hub Transitkonnektivität zwischen dem VPN- und dem ExpressRoute-Benutzer.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Gibt es bei Verwendung von Azure Virtual WAN einen Grenzwert für den Netzwerkdurchsatz oder die Anzahl der Verbindungen?

Der Netzwerkdurchsatz wird in einem virtuellen WAN-Hub pro Dienst angegeben. Sie können beliebig viele virtuelle WANs verwenden, aber jedes Virtual WAN gestattet nur einen Hub pro Region. In jedem Hub beträgt der aggregierte VPN-Durchsatz bis zu 20 GBit/s, der aggregierte ExpressRoute-Durchsatz bis zu 20 GBit/s und der aggregierte Benutzer-VPN/Point-to-Site-VPN-Durchsatz bis zu 20 GBit/s. Der Router im virtuellen Hub unterstützt bis zu 50 GBit/s für VNET-zu-VNET-Datenverkehr und geht übergreifend für alle mit einem einzelnen virtuellen Hub verbundenen VNETs von einer Gesamtworkload von 2.000 virtuellen Computern aus.

VPN-Standorte stellen die Konnektivität mit einem Hub über Verbindungen her. Virtual WAN unterstützt bis zu 1.000 Verbindungen oder 2.000 IPsec-Tunnel pro virtuellem Hub. Wenn Remotebenutzer eine Verbindung mit einem virtuellen Hub herstellen, verbinden sie sich mit dem P2S-VPN-Gateway, das je nach der für das P2S-VPN-Gateway im virtuellen Hub ausgewählten Skalierungseinheit (Bandbreite) bis zu 10.000 Benutzer unterstützt.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Wie hoch ist der VPN-Gesamtdurchsatz eines VPN-Tunnels und einer Verbindung?

Der VPN-Gesamtdurchsatz eines Hubs beträgt basierend auf der ausgewählten Skalierungseinheit des VPN-Gateways bis zu 20 GBit/s. Der Durchsatz wird von allen vorhandenen Verbindungen gemeinsam genutzt. Jeder Tunnel einer Verbindung kann bis zu 1 GBit/s unterstützen.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Kann ich NAT-T für meine VPN-Verbindungen verwenden?

Ja, NAT Traversal (NAT-T) wird unterstützt. Das Virtual WAN-VPN-Gateway erfüllt KEINE NAT-ähnliche Funktion für die inneren Pakete, die bei den IPSec-Tunneln ein-/ausgehen. Stellen Sie in dieser Konfiguration sicher, dass das lokale Gerät den IPSec-Tunnel initiiert.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Die 20 GBit/s-Einstellung für den virtuellen Hub wird im Portal nicht angezeigt. Wie konfiguriere ich dies?

Navigieren Sie im Portal zum VPN-Gateway innerhalb eines Hubs, und klicken Sie auf die Skalierungseinheit, um die entsprechende Einstellung festzulegen.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Lässt Virtual WAN für das lokale Gerät die parallele Nutzung mehrerer ISPs zu, oder wird immer nur ein VPN-Tunnel verwendet?

Lokale Gerätelösungen können Datenverkehrsrichtlinien anwenden, um den Datenverkehr über mehrere Tunnel in den Azure Virtual WAN-Hub (VPN-Gateway im virtuellen Hub) zu lenken.

### <a name="what-is-global-transit-architecture"></a>Was ist die Architektur für globale Übertragungen?

Informationen zur Architektur für globale Übertragungen finden Sie unter [Architektur mit einem globalen Transitnetzwerk und Azure Virtual WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Wie wird Datenverkehr über den Azure-Backbone geleitet?

Für den Datenverkehr wird das folgende Muster verwendet: Branchgerät > ISP > Microsoft-Netzwerkedge > Microsoft-Domänencontroller (Hub-VNET) > Microsoft-Netzwerkedge > ISP > Branchgerät

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Was wird bei diesem Modell für jede Site benötigt? Nur eine Internetverbindung?

Ja. Eine Internetverbindung und ein physisches Gerät, das IPsec unterstützt – vorzugsweise von einem unserer integrierten [Virtual WAN-Partner](../articles/virtual-wan/virtual-wan-locations-partners.md). Optional können Sie die Konfiguration und Konnektivität mit Azure auf Ihrem bevorzugten Gerät manuell verwalten.

### <a name="how-do-i-enable-default-route-00000-for-a-connection-vpn-expressroute-or-virtual-network"></a>Wie aktiviere ich die Standardroute (0.0.0.0/0) für eine Verbindung (VPN, ExpressRoute oder Virtual Network)?

Ein virtueller Hub kann eine erlernte Standardroute an eine Verbindung vom Typ „Virtuelles Netzwerk“, „Site-to-Site-VPN“ oder „ExpressRoute“ weitergeben, wenn das Flag für die Verbindung auf „Aktiviert“ festgelegt ist. Dieses Flag ist sichtbar, wenn der Benutzer eine VNET-Verbindung, eine VPN-Verbindung oder eine ExpressRoute-Verbindung bearbeitet. Das Flag ist standardmäßig deaktiviert, wenn für eine Site oder eine ExpressRoute-Leitung eine Verbindung mit einem Hub besteht. Es ist standardmäßig aktiviert, wenn eine VNET-Verbindung hinzugefügt wird, um ein VNET mit einem virtuellen Hub zu verbinden.

Der Ursprung der Standardroute liegt nicht auf dem Virtual WAN-Hub. Sie wird weitergegeben, wenn sie dem Virtual WAN-Hub bereits bekannt ist, weil darin eine Firewall bereitgestellt wurde, oder wenn für eine andere verbundene Site die Tunnelerzwingung aktiviert ist. Eine Standardroute wird nicht zwischen Hubs weitergegeben.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Wie wählt der virtuelle Hub in einer Virtual WAN-Instanz den besten Pfad für eine Route von mehreren Hubs aus?

Wenn ein virtueller Hub von mehreren Remotehubs die gleichen Routeninformationen erhält, wird folgende Entscheidungsreihenfolge verwendet:

1. Längste Präfixübereinstimmung
1. Lokale Routen zwischen Hubs. (Der virtuelle Hub weist 65520 bis 65520 für AS zwischen Hubs zu.)
1. Statische Routen über BGP Dies steht im Zusammenhang mit der Entscheidung, die der virtuelle Hubrouter trifft. Wenn der Entscheidungsträger jedoch das VPN-Gateway ist, bei dem ein Standort Routen über BGP ankündigt oder statische Adresspräfixe bereitstellt, können statische Routen gegenüber BGP-Routen bevorzugt werden.
1. ExpressRoute (ER) über VPN: ER wird gegenüber VPN bevorzugt, wenn der Kontext ein lokaler Hub ist. Transitkonnektivität zwischen ExpressRoute-Leitungen ist nur über Global Reach verfügbar. Daher kann in Szenarien, in denen die ExpressRoute-Leitung mit einem Hub verbunden und eine weitere ExpressRoute-Leitung mit einem anderen Hub mit VPN-Verbindung verbunden ist, VPN für Szenarien mit Übertragungen zwischen Hubs bevorzugt werden.
1. AS-Pfadlänge

### <a name="does-the-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Ermöglicht der Virtual WAN-Hub Konnektivität zwischen ExpressRoute-Leitungen?

Die Übertragung zwischen ER-zu-ER erfolgt immer über Global Reach. Virtuelle Hubgateways werden in DC- oder Azure-Regionen bereitgestellt. Wenn zwei ExpressRoute-Leitungen über Global Reach verbunden sind, muss der Datenverkehr nicht den ganzen Weg von den Edgeroutern bis zum virtuellen Hub-DC zurücklegen.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Gibt es ein Gewichtungskonzept für Azure Virtual WAN-ExpressRoute-Leitungen oder VPN-Verbindungen?

Wenn mehrere ExpressRoute-Leitungen mit einem virtuellen Hub verbunden sind, bietet die Routinggewichtung für die Verbindung einen Mechanismus, mit dem ExpressRoute im virtuellen Hub eine Leitung der anderen vorzieht. Es gibt keinen Mechanismus, um eine VPN-Verbindung zu gewichten. Azure bevorzugt immer eine ExpressRoute-Verbindung gegenüber einer VPN-Verbindung innerhalb eines einzelnen Hubs.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Wird von Virtual WAN für ausgehenden Azure-Datenverkehr ExpressRoute gegenüber VPN bevorzugt?

Ja. Von Virtual WAN wird für ausgehenden Azure-Datenverkehr ExpressRoute gegenüber VPN bevorzugt.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Wenn ein Virtual WAN-Hub über eine ExpressRoute-Leitung und eine damit verbundene VPN-Site verfügt: Wie kann erreicht werden, dass eine VPN-Verbindungsroute gegenüber ExpressRoute bevorzugt wird?

Wenn eine ExpressRoute-Leitung mit einem virtuellen Hub verbunden ist, sind die Microsoft-Edgerouter jeweils der erste Knoten für die Kommunikation zwischen der lokalen Umgebung und Azure. Diese Edgerouter kommunizieren mit den Virtual WAN-ExpressRoute-Gateways, die wiederum vom Router des virtuellen Hubs über Routen informiert werden, von dem sämtliche Routen zwischen allen Gateways in Virtual WAN gesteuert werden. Von den Microsoft-Edgeroutern werden ExpressRoute-Routen des virtuellen Hubs mit einer höheren Priorität als Routen verarbeitet, über die von der lokalen Umgebung informiert wird.

Falls die VPN-Verbindung aus irgendeinem Grund zum primären Medium für den virtuellen Hub wird und Routen von dieser Verbindung erhalten soll (z. B. bei Failoverszenarien zwischen ExpressRoute und VPN), passiert Folgendes: Sofern der VPN-Standort nicht über längere AS-Pfade verfügt, gibt der virtuelle Hub erlernte VPN-Routen weiterhin an das ExpressRoute-Gateway weiter. Dies führt dazu, dass die Microsoft-Edgerouter VPN-Routen gegenüber den Routen der lokalen Umgebung den Vorzug geben.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Wenn zwei Hubs (Hub 1 und 2) verbunden sind und es eine ExpressRoute-Leitung gibt, die als Schleife mit beiden Hubs verbunden ist, wie ist der Pfad für ein mit Hub 1 verbundenes VNET, um ein mit Hub 2 verbundenes VNET zu erreichen?

Das derzeitige Verhalten besteht darin, für VNET-zu-VNET-Konnektivität den ExpressRoute-Leitungspfad gegenüber Hub-zu-Hub vorzuziehen. In einem virtuellen WAN-Setup wird dies jedoch nicht empfohlen. Das Virtual WAN-Team arbeitet an einer Lösung, um die Bevorzugung von Hub-zu-Hub gegenüber dem ExpressRoute-Pfad zu ermöglichen. Es wird empfohlen, mehrere ExpressRoute-Leitungen (verschiedene Anbieter) mit einem Hub zu verbinden und die von Virtual WAN bereitgestellte Hub-zu-Hub-Konnektivität für den regionsübergreifenden Datenverkehr zu nutzen.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Können Hubs in einer anderen Ressourcengruppe in Virtual WAN erstellt werden?

Ja. Diese Option ist derzeit nur über PowerShell verfügbar. Das Virtual WAN-Portal erfordert, dass sich die Hubs in der gleichen Ressourcengruppe befinden wie die Virtual WAN-Ressource.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Wird IPv6 in Virtual WAN unterstützt?

IPv6 wird im Virtual WAN-Hub und seinen Gateways nicht unterstützt. Wenn Sie über ein VNET mit IPv4- und IPv6-Unterstützung verfügen und das VNET mit einer Virtual WAN-Instanz verbinden möchten, wird dieses Szenario derzeit nicht unterstützt.

Für das Point-to-Site-VPN-Szenario (Benutzer) mit Internetabzweigung über Azure Firewall müssen Sie wahrscheinlich IPv6-Konnektivität auf Ihrem Clientgerät deaktivieren, um die Weiterleitung des Datenverkehrs an den Virtual WAN-Hub zu erzwingen. Das liegt daran, dass von modernen Geräten IPv6-Adressen verwendet werden.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Welche API-Version wird für die Verwendung durch Skripts empfohlen, mit denen verschiedene Virtual WAN-Funktionen automatisiert werden?

Hierfür ist mindestens die Version „05-01-2020“ (1. Mai 2020) erforderlich.

### <a name="are-there-any-virtual-wan-limits"></a>Gibt es Grenzwerte für Virtual WAN?

Informationen finden Sie auf der Seite „Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen“ im Abschnitt [Grenzwerte für Virtual WAN](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits).

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Welche Unterschiede bestehen zwischen den Virtual WAN-Typen („Basic“ und „Standard“)?

Weitere Informationen finden Sie unter [Virtual WANs des Typs „Basic“ und „Standard“](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Informationen zu den Preisen finden Sie auf der Seite [Virtual WAN – Preise](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="does-virtual-wan-store-customer-data"></a>Speichert Virtual WAN Kundendaten?

Nein. Virtual WAN speichert keine Kundendaten.

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>Gibt es Anbieter verwalteter Dienste (Managed Service Providers, MSPs), die Virtual WAN für Benutzer als Dienst verwalten können?

Ja. Eine Liste mit Lösungen von Anbietern verwalteter Dienste, die über Azure Marketplace erhältlich sind, finden Sie unter [Azure Marketplace-Angebote nach Azure Networking-MSP-Partnern](../articles/networking/networking-partners-msp.md#msp).
