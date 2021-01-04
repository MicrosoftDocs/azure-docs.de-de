---
title: Routing von Datenverkehr für virtuelle Azure-Netzwerke
titlesuffix: Azure Virtual Network
description: Es wird beschrieben, wie VNet-Datenverkehr von Azure weitergeleitet wird und wie Sie das Routing von Azure anpassen können.
services: virtual-network
documentationcenter: na
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2017
ms.author: aldomel
ms.openlocfilehash: ca6460497fa026feca503df741ad6811a95fb9e3
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96936929"
---
# <a name="virtual-network-traffic-routing"></a>Routing von Datenverkehr für virtuelle Netzwerke

Es wird beschrieben, wie Azure Datenverkehr zwischen Azure, lokalen Speicherorten und Internetressourcen weiterleitet. Azure erstellt automatisch eine Routentabelle für jedes Subnetz in einem virtuellen Azure-Netzwerk und fügt der Tabelle Systemstandardrouten hinzu. Weitere Informationen zu virtuellen Netzwerken und Subnetzen finden Sie unter [Virtuelle Netzwerke im Überblick](virtual-networks-overview.md). Sie können einige Systemrouten von Azure durch [benutzerdefinierte Routen](#custom-routes) außer Kraft setzen und den Routentabellen zusätzliche benutzerdefinierte Routen hinzufügen. Azure leitet ausgehenden Datenverkehr aus einem Subnetz basierend auf den Routen in der Routentabelle eines Subnetzes weiter.

## <a name="system-routes"></a>Systemrouten

Azure erstellt automatisch Systemrouten und weist die Routen allen Subnetzen eines virtuellen Netzwerks zu. Sie können keine Systemrouten erstellen oder entfernen, aber Sie können einige Systemrouten durch [benutzerdefinierte Routen](#custom-routes) außer Kraft setzen. Azure erstellt Standardsystemrouten für jedes Subnetz und fügt zusätzliche [optionale Standardrouten](#optional-default-routes) für spezifische Subnetze hinzu (bzw. jedes Subnetz, wenn Sie bestimmte Azure-Funktionen nutzen).

### <a name="default"></a>Standard

Jede Route enthält ein Adresspräfix und einen Typ des nächsten Hops. Wenn Datenverkehr, der ein Subnetz verlässt, an eine IP-Adresse innerhalb des Adresspräfix einer Route gesendet wird, ist die Route mit dem Präfix die von Azure verwendete Route. Erfahren Sie mehr darüber, [wie Azure eine Route auswählt](#how-azure-selects-a-route), wenn mehrere Routen die gleichen Präfixe oder überlappende Präfixe enthalten. Bei jeder Erstellung eines virtuellen Netzwerks erstellt Azure automatisch die folgenden Standardsystemrouten für jedes Subnetz im virtuellen Netzwerk:

|`Source` |Adresspräfixe                                        |Typ des nächsten Hops  |
|-------|---------                                               |---------      |
|Standard|Für das virtuelle Netzwerk eindeutig                           |Virtuelles Netzwerk|
|Standard|0.0.0.0/0                                               |Internet       |
|Standard|10.0.0.0/8                                              |Keine           |
|Standard|192.168.0.0/16                                          |Keine           |
|Standard|100.64.0.0/10                                           |Keine           |

Die in der obigen Tabelle aufgeführten Typen des nächsten Hops geben an, wie Datenverkehr von Azure weitergeleitet wird, der für das angegebene Adresspräfix bestimmt ist. Erklärungen zu den Typen des nächsten Hops:

* **Virtuelles Netzwerk:** Leitet Datenverkehr zwischen Adressbereichen im [Adressraum](manage-virtual-network.md#add-or-remove-an-address-range) eines virtuellen Netzwerks weiter. Azure erstellt eine Route mit einem Adresspräfix, das jeweils dem Adressbereich entspricht, der im Adressraum eines virtuellen Netzwerks definiert ist. Wenn für den Adressraum des virtuellen Netzwerks mehrere Adressbereiche definiert sind, erstellt Azure für jeden Adressbereich eine gesonderte Route. Azure leitet Datenverkehr automatisch zwischen Subnetzen weiter, indem die Routen verwendet werden, die für jeden Adressbereich erstellt wurden. Es ist nicht erforderlich, dass Sie Gateways für Azure definieren, um Datenverkehr zwischen Subnetzen weiterzuleiten. Ein virtuelles Netzwerk enthält zwar Subnetze, und jedes Subnetz verfügt über einen definierten Adressbereich, aber von Azure werden *keine* Standardrouten für Subnetzadressbereiche erstellt. Der Grund ist, dass jeder Subnetzadressbereich in einem Adressbereich des Adressraums eines virtuellen Netzwerks liegt.<br>
* **Internet**: Leitet Datenverkehr, der vom Adresspräfix angegeben wird, in das Internet weiter. Für die Systemstandardroute wird das Adresspräfix 0.0.0.0/0 angegeben. Wenn Sie die Standardrouten von Azure nicht außer Kraft setzen, leitet Azure Datenverkehr für alle Adressen, die nicht von einem Adressbereich in einem virtuellen Netzwerk angegeben sind, in das Internet weiter. Es gilt aber folgende Ausnahme: Wenn die Zieladresse die Adresse von einem der Azure-Dienste ist, leitet Azure den Datenverkehr über das Azure-Backbonenetzwerk direkt an den Dienst weiter, und nicht in das Internet. Der Datenverkehr zwischen Azure-Diensten wird nicht über das Internet übertragen. Dies gilt unabhängig davon, in welcher Azure-Region das virtuelle Netzwerk vorhanden ist oder in welcher Azure-Region eine Instanz des Azure-Diensts bereitgestellt wird. Sie können die Standardsystemroute von Azure für das Adresspräfix 0.0.0.0/0 durch eine [benutzerdefinierte Route](#custom-routes) außer Kraft setzen.<br>
* **Keine:** Datenverkehr, der an den Typ **Keine** des nächsten Hops weitergeleitet wird, wird verworfen und nicht an Orte außerhalb des Subnetzes weitergeleitet. Azure erstellt automatisch Standardrouten für die folgenden Adresspräfixe:<br>

    * **10.0.0.0/8 und 192.168.0.0/16**: Für die private Nutzung in RFC 1918 reserviert.<br>
    * **100.64.0.0/10**: In RFC 6598 reserviert.

    Wenn Sie einen der obigen Adressbereiche innerhalb des Adressraums eines virtuellen Netzwerks zuweisen, ändert Azure den Typ des nächsten Hops für die Route automatisch von **Keine** in **Virtuelles Netzwerk**. Wenn Sie einen Adressbereich dem Adressraum eines virtuellen Netzwerks zuweisen, der eines der vier reservierten Adresspräfixe enthält (aber nicht identisch damit ist), entfernt Azure die Route für das Präfix und fügt eine Route für das von Ihnen hinzugefügte Adresspräfix mit **Virtuelles Netzwerk** als Typ des nächsten Hops hinzu.

### <a name="optional-default-routes"></a>Optionale Standardrouten

Azure fügt zusätzliche Standardsystemrouten für unterschiedliche Azure-Funktionen hinzu. Dies gilt aber nur, wenn Sie die Funktionen aktivieren. Je nach Funktion fügt Azure optionale Standardrouten entweder spezifischen Subnetzen im virtuellen Netzwerk oder allen Subnetzen in einem virtuellen Netzwerk hinzu. Hier sind die zusätzlichen Systemrouten und Typen des nächsten Hops aufgeführt, die von Azure hinzugefügt werden können, wenn Sie die unterschiedlichen Funktionen aktivieren:

|`Source`                 |Adresspräfixe                       |Typ des nächsten Hops|Subnetz im virtuellen Netzwerk, dem die Route hinzugefügt wird|
|-----                  |----                                   |---------                    |--------|
|Standard                |Eindeutig für das virtuelle Netzwerk, z.B.: 10.1.0.0/16|VNet-Peering                 |All|
|Gateway des virtuellen Netzwerks|Präfixe, die lokal oder per BGP angekündigt werden oder im Gateway des lokalen Netzwerks konfiguriert sind     |Gateway des virtuellen Netzwerks      |All|
|Standard                |Mehrere                               |VirtualNetworkServiceEndpoint|Nur das Subnetz, für das ein Dienstendpunkt aktiviert ist.|

* **VNET-Peering**: Wenn Sie ein VNET-Peering zwischen zwei virtuellen Netzwerken erstellen, wird eine Route für jeden Adressbereich innerhalb des Adressraums der einzelnen virtuellen Netzwerke hinzugefügt, für die ein Peering erstellt wird. Weitere Informationen hierzu finden Sie unter [Peering virtueller Netzwerke](virtual-network-peering-overview.md).<br>
* **Gateway für virtuelle Netzwerke**: Mindestens eine Route mit Angabe von *Gateway für virtuelle Netzwerke* als Typ des nächsten Hops wird hinzugefügt, wenn ein Gateway für virtuelle Netzwerke einem virtuellen Netzwerk hinzugefügt wird. Die Quelle ist ebenfalls das *Gateway für virtuelle Netzwerke*, da das Gateway die Routen dem Subnetz hinzufügt. Wenn Ihr Gateway des lokalen Netzwerks BGP-Routen ([Border Gateway Protocol](#border-gateway-protocol)) mit einem Gateway des virtuellen Azure-Netzwerks austauscht, wird eine Route für jede Route hinzugefügt, die vom Gateway des lokalen Netzwerks weiterverteilt wird. Es wird empfohlen, lokale Routen unter den größtmöglichen Adressbereichen zusammenzufassen, damit die geringste Anzahl von Routen an ein Gateway des virtuellen Azure-Netzwerks weiterverteilt wird. Es gelten Einschränkungen dafür, wie viele Routen Sie an ein Gateway des virtuellen Azure-Netzwerks weiterverteilen können. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).<br>
* **VirtualNetworkServiceEndpoint**: Die öffentlichen IP-Adressen für bestimmte Dienste werden der Routentabelle von Azure hinzugefügt, wenn Sie einen Dienstendpunkt für den Dienst aktivieren. Dienstendpunkte werden für einzelne Subnetze in einem virtuellen Netzwerk aktiviert, sodass die Route nur der Routentabelle eines Subnetzes hinzugefügt wird, für das ein Dienstendpunkt aktiviert ist. Die öffentlichen IP-Adressen von Azure-Diensten ändern sich regelmäßig. Azure verwaltet die Adressen in der Routentabelle automatisch, wenn sich die Adressen ändern. Erfahren Sie mehr zu [VNET-Dienstendpunkten](virtual-network-service-endpoints-overview.md) und zu den Diensten, für die Sie Dienstendpunkte erstellen können.<br>

    > [!NOTE]
    > Die Typen **VNet-Peering** und **VirtualNetworkServiceEndpoint** des nächsten Hops werden nur Routentabellen von Subnetzen in virtuellen Netzwerken hinzugefügt, die mit dem Azure Resource Manager-Bereitstellungsmodell erstellt wurden. Die Typen des nächsten Hops werden dagegen nicht Routentabellen hinzugefügt, die Subnetzen von virtuellen Netzwerken zugeordnet sind, wenn diese mit dem klassischen Bereitstellungsmodell erstellt wurden. Erfahren Sie mehr über Azure-[Bereitstellungsmodelle](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="custom-routes"></a>Benutzerdefinierte Routen

Sie erstellen vom Benutzer definierte Routen, indem Sie entweder [benutzerdefinierte](#user-defined) Routen erstellen oder [BGP](#border-gateway-protocol)-Routen (Border Gateway Protocol) zwischen Ihrem Gateway des lokalen Netzwerks und einem Gateway des virtuellen Azure-Netzwerks austauschen. 

### <a name="user-defined"></a>Benutzerdefiniert

Sie können benutzerdefinierte (statische) Routen in Azure erstellen, um Standardsystemrouten von Azure außer Kraft zu setzen oder der Routentabelle eines Subnetzes zusätzliche Routen hinzuzufügen. In Azure erstellen Sie eine Routentabelle und ordnen die Routentabelle dann null oder mehr Subnetzen eines virtuellen Netzwerks zu. Jedem Subnetz können null oder mehr Routentabellen zugeordnet sein. Informationen zur maximalen Anzahl von Routen, die Sie einer Routentabelle hinzufügen können, und zur maximalen Anzahl von benutzerdefinierten Routentabellen, die Sie pro Azure-Abonnement erstellen können, finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Wenn Sie eine Routentabelle erstellen und einem Subnetz zuordnen, werden die darin enthaltenen Routen mit den Standardrouten, die Azure einem Subnetz standardmäßig hinzufügt, kombiniert oder dadurch außer Kraft gesetzt.

Beim Erstellen einer benutzerdefinierten Route können Sie die folgenden Typen des nächsten Hops angeben:

* **Virtuelles Gerät**: Ein virtuelles Gerät ist eine VM, auf der in der Regel eine Netzwerkanwendung wie z.B. eine Firewall ausgeführt wird. Informationen zu verschiedenen vorkonfigurierten virtuellen Netzwerkgeräten, die Sie in einem virtuellen Netzwerk bereitstellen können, finden Sie im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Wenn Sie eine Route mit dem Hop-Typ **Virtuelles Gerät** erstellen, können Sie auch eine IP-Adresse des nächsten Hops angeben. Bei der IP-Adresse kann es sich um Folgendes handeln:

    * Die [private IP-Adresse](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) einer Netzwerkschnittstelle, die an einen virtuellen Computer angefügt ist. Für jede Netzwerkschnittstelle, die an einen virtuellen Computer zum Weiterleiten von Netzwerkdatenverkehr an eine andere Adresse als die eigene Adresse angefügt ist, muss hierfür die Azure-Option *Enable IP forwarding* (IP-Weiterleitung aktivieren) aktiviert sein. Mit der Einstellung wird die Azure-Überprüfung der Quelle und des Ziels für eine Netzwerkschnittstelle deaktiviert. Erfahren Sie mehr dazu, wie Sie die [IP-Weiterleitung für eine Netzwerkschnittstelle aktivieren](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). *Enable IP forwarding* (IP-Weiterleitung aktivieren) ist zwar eine Azure-Einstellung, aber unter Umständen müssen Sie die IP-Weiterleitung auch im Betriebssystem des virtuellen Computers aktivieren, damit das Gerät Datenverkehr zwischen privaten IP-Adressen weiterleitet, die Azure-Netzwerkschnittstellen zugewiesen sind. Wenn das Gerät Datenverkehr an eine öffentliche IP-Adresse weiterleiten muss, muss der Datenverkehr entweder per Proxy verarbeitet werden, oder für die private IP-Adresse der Quelle muss eine Netzwerkadressübersetzung in die eigene private IP-Adresse durchgeführt werden. Für diese private IP-Adresse wird von Azure dann die Netzwerkadressübersetzung in eine öffentliche IP-Adresse durchgeführt, bevor der Datenverkehr ins Internet gesendet wird. Informationen zum Bestimmen der erforderlichen Einstellungen auf dem virtuellen Computer finden Sie in der Dokumentation für Ihr Betriebssystem oder Ihre Netzwerkanwendung. Informationen zu den Grundlagen von ausgehenden Verbindungen in Azure finden Sie unter [Grundlegendes zu ausgehenden Verbindungen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>

        > [!NOTE]
        > Stellen Sie ein virtuelles Gerät in einem anderen Subnetz als dem Subnetz bereit, in dem die Ressourcen, die über das virtuelle Gerät weitergeleitet werden, bereitgestellt werden. Das Bereitstellen des virtuellen Geräts in demselben Subnetz und das anschließende Anwenden einer Routentabelle in dem Subnetz, aus dem Datenverkehr über das virtuelle Gerät geleitet wird, kann zu Routingschleifen führen, bei denen der Datenverkehr das Subnetz niemals verlässt.

    * Die private IP-Adresse eines [internen Lastenausgleichs](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) von Azure. Ein Lastenausgleich wird häufig im Rahmen einer [Hochverfügbarkeitsstrategie für virtuelle Netzwerkgeräte](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json) verwendet.

    Sie können eine Route mit 0.0.0.0/0 als Adresspräfix und „Virtuelles Gerät“ als Typ des nächsten Hops definieren, sodass das Gerät den Datenverkehr untersuchen und ermitteln kann, ob dieser weitergeleitet oder verworfen werden soll. Wenn Sie eine benutzerdefinierte Route erstellen möchten, die das Adresspräfix 0.0.0.0/0 enthält, sollten Sie zuerst [Adresspräfix 0.0.0.0/0](#default-route) lesen.

* **Gateway für virtuelle Netzwerke**: Geben Sie an, wenn Datenverkehr, der für bestimmte Adresspräfixe bestimmt ist, an ein Gateway für virtuelle Netzwerke weitergeleitet werden soll. Das Gateway für virtuelle Netzwerke muss mit dem Typ **VPN** erstellt werden. Sie können ein Gateway für virtuelle Netzwerke, das mit dem Typ **ExpressRoute** erstellt wurde, nicht in einer benutzerdefinierten Route angeben, da bei ExpressRoute für benutzerdefinierte Routen BGP verwendet werden muss. Sie können eine Route definieren, mit der Datenverkehr, der für das Adresspräfix 0.0.0.0/0 bestimmt ist, an ein [routenbasiertes](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype) Gateway für virtuelle Netzwerke geleitet wird. Es kann sein, dass Sie lokal ein Gerät nutzen, mit dem der Datenverkehr untersucht und ermittelt wird, ob der Datenverkehr weitergeleitet oder verworfen werden soll. Wenn Sie eine benutzerdefinierte Route für das Adresspräfix 0.0.0.0/0 erstellen möchten, sollten Sie zuerst [Adresspräfix 0.0.0.0/0](#default-route) lesen. Anstatt eine benutzerdefinierte Route für das Adresspräfix 0.0.0.0/0 zu konfigurieren, können Sie eine Route mit dem Präfix 0.0.0.0/0 per BGP ankündigen, wenn Sie [BGP für ein Gateway für virtuelle Netzwerke (VPN) aktiviert haben](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>
* **Keine:** Geben Sie an, wenn Datenverkehr für ein Adresspräfix verworfen und nicht an ein Ziel weitergeleitet werden soll. Wenn Sie eine Funktion nicht vollständig konfiguriert haben, wird in Azure für einige optionale Systemrouten ggf. *Keine* angegeben. Wenn *Keine* beispielsweise als **IP-Adresse für nächsten Hop** mit *Gateway für virtuelle Netzwerke* oder *Virtuelles Gerät* unter **Typ des nächsten Hops** angezeigt wird, kann dies daran liegen, dass das Gerät nicht ausgeführt wird oder nicht vollständig konfiguriert ist. Azure erstellt [Standardrouten](#default) des Systems für reservierte Adresspräfixe mit **Keine** als Typ des nächsten Hops.<br>
* **Virtuelles Netzwerk:** Geben Sie an, wenn Sie das Standardrouting in einem virtuellen Netzwerk außer Kraft setzen möchten. Unter [Routingbeispiel](#routing-example) finden Sie ein Beispiel dafür, warum es ratsam sein kann, eine Route mit dem Hop-Typ **Virtuelles Netzwerk** zu erstellen.<br>
* **Internet**: Geben Sie an, wenn Sie Datenverkehr, der für ein Adresspräfix im Internet bestimmt ist, explizit weiterleiten möchten oder wenn Datenverkehr, der für Azure-Dienste mit öffentlichen IP-Adressen bestimmt ist, im Azure-Backbonenetzwerk verbleiben soll.

Es ist nicht möglich, **VNet-Peering** oder **VirtualNetworkServiceEndpoint** als Typ des nächsten Hops in benutzerdefinierten Routen anzugeben. Routen mit **VNet-Peering** oder **VirtualNetworkServiceEndpoint** als Typ des nächsten Hops werden von Azure nur dann erstellt, wenn Sie ein VNet-Peering oder einen Dienstendpunkt konfigurieren.

## <a name="next-hop-types-across-azure-tools"></a>Typ des nächsten Hops für Azure-Tools

Der Name, der für Typen des nächsten Hops angezeigt und referenziert wird, unterscheidet sich für das Azure-Portal und Befehlszeilentools und die Bereitstellungsmodelle „Azure Resource Manager“ und „klassisch“. In der folgenden Tabelle sind die Namen aufgeführt, die zum Verweisen auf den Typ des nächsten Hops mit den unterschiedlichen Tools und [Bereitstellungsmodellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) verwendet werden:

|Typ des nächsten Hops                   |Die Azure CLI und PowerShell (Resource Manager) |Die klassische Azure CLI und PowerShell (klassisch)|
|-------------                   |---------                                       |-----|
|Gateway des virtuellen Netzwerks         |VirtualNetworkGateway                           |VPNGateway|
|Virtuelles Netzwerk                 |VNetLocal                                       |VNETLocal (im ASM-Modus in der klassischen CLI nicht verfügbar)|
|Internet                        |Internet                                        |Internet (im ASM-Modus in der klassischen CLI nicht verfügbar)|
|Virtuelles Gerät               |VirtualAppliance                                |VirtualAppliance|
|Keine                            |Keine                                            |NULL (im ASM-Modus in der klassischen CLI nicht verfügbar)|
|Peering in virtuellen Netzwerken         |VNet-Peering                                    |Nicht verfügbar|
|VNET-Dienstendpunkt|VirtualNetworkServiceEndpoint                   |Nicht verfügbar|

### <a name="border-gateway-protocol"></a>Border Gateway Protocol

Ein Gateway des lokalen Netzwerks kann Routen mit einem Gateway des virtuellen Azure-Netzwerks austauschen, indem das Border Gateway Protocol (BGP) verwendet wird. Die Nutzung von BGP mit einem Gateway des virtuellen Azure-Netzwerks ist abhängig von dem Typ, den Sie beim Erstellen des Gateways ausgewählt haben. Für die Auswahl des Typs gilt Folgendes:

* **ExpressRoute**: Sie müssen BGP verwenden, um lokale Routen zum Microsoft-Edgerouter anzukündigen. Sie können keine benutzerdefinierten Routen zur Weiterleitung des Datenverkehrs zum ExpressRoute-Gateway für virtuelle Netzwerke erstellen, wenn Sie ein Gateway für virtuelle Netzwerke mit folgendem Typ bereitstellen: ExpressRoute. Sie können benutzerdefinierte Routen beispielsweise zur Weiterleitung des Datenverkehrs von ExpressRoute zum virtuellen Netzwerkgerät verwenden.<br>
* **VPN**: Optional können Sie BGP verwenden. Ausführliche Informationen finden Sie unter [Übersicht über BGP mit Azure-VPN-Gateways](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Wenn Sie Routen mit Azure per BGP austauschen, wird der Routentabelle aller Subnetze in einem virtuellen Netzwerk für jedes angekündigte Präfix eine separate Route hinzugefügt. Die Route wird mit *Gateway für virtuelle Netzwerke* als Quelle und Typ des nächsten Hops hinzugefügt. 

Die ER- und VPN Gateway-Routenverteilung kann für ein Subnetz mithilfe einer Eigenschaft für eine Routentabelle deaktiviert werden. Wenn Sie Routen unter Verwendung von BGP mit Azure austauschen, werden der Routingtabelle von Subnetzen mit deaktivierter Verteilung von Gatewayrouten virtueller Netzwerke keine Routen hinzugefügt. Die Konnektivität mit VPN-Verbindungen wird über [benutzerdefinierte Routen](#custom-routes) mit *Gateway für virtuelle Netzwerke* als Typ des nächsten Hops erreicht. **Die Routenweitergabe sollte für das GatewaySubnet nicht deaktiviert werden. Das Gateway funktioniert nicht, wenn diese Einstellung deaktiviert ist.** Ausführliche Informationen finden Sie unter [Deaktivieren der Routenverteilung von Gatewayrouten des virtuellen Netzwerks](manage-route-table.md#create-a-route-table).

## <a name="how-azure-selects-a-route"></a>Auswahl einer Route durch Azure

Wenn Datenverkehr in ausgehender Richtung aus einem Subnetz gesendet wird, wählt Azure eine Route basierend auf der IP-Zieladresse aus, indem der längste Präfixübereinstimmungsalgorithmus verwendet wird. Eine Routentabelle hat beispielsweise zwei Routen: Mit einer Route wird das Adresspräfix 10.0.0.0/24 angegeben, und mit der anderen Route wird das Adresspräfix 10.0.0.0/16 angegeben. Azure leitet den für 10.0.0.5 bestimmten Datenverkehr an den Typ des nächsten Hops weiter, der in der Route mit dem Adresspräfix 10.0.0.0/24 angegeben ist, weil 10.0.0.0/24 ein längeres Präfix als 10.0.0.0/16 ist (auch wenn 10.0.0.5 innerhalb beider Adresspräfixe liegt). Azure leitet für 10.0.1.5 bestimmten Datenverkehr an den Typ des nächsten Hops weiter, der in der Route mit dem Adresspräfix 10.0.0.0/16 angegeben ist, weil 10.0.1.5 nicht im Adresspräfix 10.0.0.0/24 enthalten ist. Aus diesem Grund ist die Route mit dem Adresspräfix 10.0.0.0/16 das längste Präfix, für das sich eine Übereinstimmung ergibt.

Wenn mehrere Routen das gleiche Präfix enthalten, wählt Azure den Routentyp nach den folgenden Prioritätsregeln aus:

1. Benutzerdefinierte Route
1. BGP-Route
1. Systemroute

> [!NOTE]
> Systemrouten für Datenverkehr im Zusammenhang mit virtuellen Netzwerken, virtuellen Netzwerken über Peerings oder VNET-Dienstendpunkten sind die bevorzugten Routen, auch wenn BGP-Routen spezifischer sind.

Eine Routentabelle enthält beispielsweise die folgenden Routen:


|`Source`   |Adresspräfixe  |Typ des nächsten Hops           |
|---------|---------         |-------                 |
|Standard  | 0.0.0.0/0        |Internet                |
|Benutzer     | 0.0.0.0/0        |Gateway des virtuellen Netzwerks |

Wenn Datenverkehr für eine IP-Adresse außerhalb der Adresspräfixe von anderen Routen in der Routentabelle bestimmt ist, wählt Azure die Route mit der Quelle **Benutzer** aus, da benutzerdefinierte Routen eine höhere Priorität als Systemstandardrouten haben.

Eine umfassende Routentabelle mit Erklärungen der Routen in der Tabelle finden Sie unter [Routingbeispiel](#routing-example).

## <a name="00000-address-prefix"></a><a name="default-route"></a>Adresspräfix 0.0.0.0/0

Eine Route mit dem Adresspräfix 0.0.0.0/0 weist Azure an, wie Datenverkehr für eine IP-Adresse, die nicht innerhalb des Adresspräfixes einer anderen Route in einer Routentabelle des Subnetzes liegt, weitergeleitet wird. Bei der Erstellung eines Subnetzes erstellt Azure eine Route vom Typ [Standard](#default) zum Adresspräfix 0.0.0.0/0 mit **Internet** als Typ des nächsten Hops. Wenn Sie diese Route nicht außer Kraft setzen, leitet Azure den gesamten Datenverkehr, der für IP-Adressen außerhalb des Adresspräfix einer anderen Route bestimmt ist, in das Internet weiter. Die Ausnahme besteht darin, dass Datenverkehr an die öffentlichen IP-Adressen von Azure im Azure-Backbonenetzwerk verbleibt und nicht in das Internet weitergeleitet wird. Wenn Sie diese Route mit einer [benutzerdefinierten](#custom-routes) Route außer Kraft setzen, wird Datenverkehr für Adressen, die nicht innerhalb der Adresspräfixe anderer Routen in der Routentabelle liegen, an ein virtuelles Netzwerkgerät oder ein Gateway für virtuelle Netzwerke gesendet. Dies hängt davon ab, was Sie in einer benutzerdefinierten Route angegeben haben.

Wenn Sie das Adresspräfix 0.0.0.0/0 außer Kraft setzen, werden zusätzlich dazu, dass der ausgehende Datenverkehr aus dem Subnetz über das Gateway für virtuelle Netzwerke oder das virtuelle Gerät fließt, die folgenden Änderungen für das Azure-Standardrouting vorgenommen: 

* Azure sendet den gesamten Datenverkehr an den Typ des nächsten Hops, der in der Route angegeben ist – einschließlich des Datenverkehrs, der für öffentliche IP-Adressen von Azure-Diensten bestimmt ist. Wenn **Internet** als Typ des nächsten Hops für die Route mit dem Adresspräfix 0.0.0.0/0 angegeben ist, verlässt Datenverkehr aus dem Subnetz, der für die öffentlichen IP-Adressen von Azure-Diensten bestimmt ist, niemals das Azure-Backbonenetzwerk. Dies gilt unabhängig von der Azure-Region, in der sich das virtuelle Netzwerk oder die Azure-Dienstressource befinden. Aber wenn Sie eine benutzerdefinierte Route oder BGP-Route mit **Gateway für virtuelle Netzwerke** oder **Virtuelles Gerät** als nächstem Hop-Typ erstellen, wird der gesamte Datenverkehr (einschließlich des Datenverkehrs, der an öffentliche IP-Adressen von Azure-Diensten gesendet wird, für die Sie keine [Dienstendpunkte](virtual-network-service-endpoints-overview.md) aktiviert haben) an den Typ des nächsten Hops gesendet, der in der Route angegeben ist. Wenn Sie einen Dienstendpunkt für einen Dienst aktiviert haben, wird Datenverkehr für den Dienst nicht an den Typ des nächsten Hops in einer Route mit dem Adresspräfix 0.0.0.0/0 weitergeleitet. Der Grund hierfür ist, dass Adresspräfixe für den Dienst in der Route angegeben werden, die von Azure erstellt wird, wenn Sie den Dienstendpunkt aktivieren, und die Adresspräfixe für den Dienst länger als 0.0.0.0/0 sind.
* Es ist nicht mehr möglich, aus dem Internet direkt auf Ressourcen im Subnetz zuzugreifen. Sie können über das Internet indirekt auf Ressourcen im Subnetz zugreifen, wenn eingehender Datenverkehr das Gerät durchläuft, das über den Typ des nächsten Hops für eine Route mit dem Adresspräfix 0.0.0.0/0 angegeben ist, bevor die Ressource im virtuellen Netzwerk erreicht ist. Wenn die Route die hier angegebenen Werte für den Typ des nächsten Hops enthält, gilt Folgendes:<br>

    * **Virtuelles Gerät**: Das Gerät muss folgende Anforderungen erfüllen:<br>

        * Erreichbarkeit über das Internet<br>
        * Zugewiesene öffentliche IP-Adresse<br>
        * Keine Zuordnung einer Netzwerksicherheitsgruppen-Regel, die die Kommunikation mit dem Gerät verhindert<br>
        * Keine Verweigerung der Kommunikation<br>
        * Möglichkeit zur Übersetzung der Netzwerkadresse und zur Weiterleitung oder Übergabe des Datenverkehrs an die Zielressource im Subnetz per Proxy und Rückgabe des Datenverkehrs ins Internet

    * **Gateway für virtuelle Netzwerke**: Wenn es sich beim Gateway um ein ExpressRoute-Gateway für virtuelle Netzwerke handelt, kann ein lokal angeordnetes Gerät mit Internetverbindung die Übersetzung der Netzwerkadresse und die Weiterleitung durchführen oder den Datenverkehr per Proxy an die Zielressource im Subnetz übergeben (per [privatem Peering](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#privatepeering) von ExpressRoute). 

Wenn Ihr virtuelles Netzwerk mit einem Azure-VPN-Gateway verbunden ist, ordnen Sie dem [Gatewaysubnetz](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) keine Routingtabelle zu, die eine Route mit dem Ziel 0.0.0.0/0 enthält. Andernfalls ist die ordnungsgemäße Funktion des Gateways gefährdet. Ausführliche Informationen finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](../vpn-gateway/vpn-gateway-vpn-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gatewayports) in der Frage *Warum werden auf meinem VPN-Gateway bestimmte Ports geöffnet?* .

Implementierungsdetails bei Verwendung von Gateways für virtuelle Netzwerke zwischen dem Internet und Azure finden Sie unter [DMZ between Azure and your on-premises datacenter](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) (DMZ zwischen Azure und Ihrem lokalen Datencenter).

## <a name="routing-example"></a>Routingbeispiel

Zur Erläuterung der Konzepte in diesem Artikel wird in den nächsten Abschnitten Folgendes beschrieben:

* Szenario mit Anforderungen<br>
* Erforderliche benutzerdefinierte Routen zur Erfüllung der Anforderungen<br>
* Routentabelle für ein Subnetz mit den Standardrouten und benutzerdefinierten Routen zur Erfüllung der Anforderungen

> [!NOTE]
> Dieses Beispiel ist nicht als empfohlene Implementierung oder bewährte Methode gedacht. Es soll nur dem besseren Verständnis der Konzepte in diesem Artikel dienen.

### <a name="requirements"></a>Requirements (Anforderungen)

1. Implementieren Sie zwei virtuelle Netzwerke in derselben Azure-Region, und ermöglichen Sie für Ressourcen die Kommunikation zwischen den virtuellen Netzwerken.
1. Ermöglichen Sie für ein lokales Netzwerk die sichere Kommunikation mit beiden virtuellen Netzwerken per VPN-Tunnel über das Internet. *Alternativ hierzu können Sie auch eine ExpressRoute-Verbindung verwenden, aber in diesem Beispiel wird eine VPN-Verbindung genutzt.*
1. Für ein Subnetz in einem virtuellen Netzwerk:

    * Erzwingen Sie für den gesamten ausgehenden Datenverkehr des Subnetzes – mit Ausnahme des Datenverkehrs an Azure Storage und innerhalb des Subnetzes – zur Untersuchung und Protokollierung den Weg über ein virtuelles Netzwerkgerät.<br>
    * Datenverkehr zwischen privaten IP-Adressen innerhalb des Subnetzes sollte nicht untersucht werden. Ermöglichen Sie es, dass Datenverkehr zwischen allen Ressourcen direkt fließen kann.<br>
    * Verwerfen Sie den gesamten ausgehenden Datenverkehr, der für das andere virtuelle Netzwerk bestimmt ist.<br>
    * Lassen Sie ausgehenden Datenverkehr, der für den Azure-Speicher bestimmt ist, direkt in den Speicher fließen, ohne dass der Umweg über ein virtuelles Netzwerkgerät erzwungen wird.

1. Lassen Sie den gesamten Datenverkehr zwischen allen anderen Subnetzen und virtuellen Netzwerken zu.

### <a name="implementation"></a>Implementierung

In der folgenden Abbildung ist eine Implementierung per Azure Resource Manager-Bereitstellungsmodell dargestellt, für die die obigen Anforderungen erfüllt sind:

![Netzwerkdiagramm](./media/virtual-networks-udr-overview/routing-example.png)

Mit den Pfeilen ist der Weg des Datenverkehrs angegeben. 

### <a name="route-tables"></a>Routentabellen

#### <a name="subnet1"></a>Subnet1

Die Routentabelle für *Subnet1* in der Abbildung enthält die folgenden Routen:

|id  |`Source` |State  |Adresspräfixe    |Typ des nächsten Hops          |IP-Adresse des nächsten Hops|Name der benutzerdefinierten Route| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Standard|Ungültig|10.0.0.0/16         |Virtuelles Netzwerk        |                   |              |
|2   |Benutzer   |Aktiv |10.0.0.0/16         |Virtuelles Gerät      |10.0.100.4         |Within-VNet1  |
|3   |Benutzer   |Aktiv |10.0.0.0/24         |Virtuelles Netzwerk        |                   |Within-Subnet1|
|4   |Standard|Ungültig|10.1.0.0/16         |VNet-Peering           |                   |              |
|5   |Standard|Ungültig|10.2.0.0/16         |VNet-Peering           |                   |              |
|6   |Benutzer   |Aktiv |10.1.0.0/16         |Keine                   |                   |ToVNet2-1-Drop|
|7   |Benutzer   |Aktiv |10.2.0.0/16         |Keine                   |                   |ToVNet2-2-Drop|
|8   |Standard|Ungültig|10.10.0.0/16        |Gateway des virtuellen Netzwerks|[X.X.X.X]          |              |
|9   |Benutzer   |Aktiv |10.10.0.0/16        |Virtuelles Gerät      |10.0.100.4         |To-On-Prem    |
|10  |Standard|Aktiv |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Standard|Ungültig|0.0.0.0/0           |Internet               |                   |              |
|12  |Benutzer   |Aktiv |0.0.0.0/0           |Virtuelles Gerät      |10.0.100.4         |Default-NVA   |

Es folgen Erklärungen der einzelnen Routen-IDs:

1. Azure hat diese Route automatisch für alle Subnetze in *Virtual-network-1* hinzugefügt, da 10.0.0.0/16 der einzige Adressbereich ist, der im Adressraum für das virtuelle Netzwerk definiert ist. Wenn die benutzerdefinierte Route in Route ID2 nicht erstellt worden wäre, würde Datenverkehr, der an eine Adresse zwischen 10.0.0.1 und 10.0.255.254 gesendet wird, innerhalb des virtuellen Netzwerks weitergeleitet werden. Der Grund ist, dass das Präfix länger als 0.0.0.0/0 ist und nicht innerhalb der Adresspräfixe von anderen Routen liegt. Azure hat den Status automatisch von *Aktiv* in *Ungültig* geändert, als ID2 (eine benutzerdefinierte Route) hinzugefügt wurde, da sie über das gleiche Präfix wie die Standardroute verfügt und Standardrouten von benutzerdefinierten Routen außer Kraft gesetzt werden. Der Status dieser Route lautet weiterhin *Aktiv* für *Subnet2*, da die Routentabelle, in der sich die benutzerdefinierte Route ID2 befindet, *Subnet2* nicht zugeordnet ist.
2. Diese Route wurde von Azure hinzugefügt, als eine benutzerdefinierte Route für das Adresspräfix 10.0.0.0/16 dem Subnetz *Subnet1* im virtuellen Netzwerk *Virtual-network-1* zugeordnet wurde. Die benutzerdefinierte Route gibt 10.0.100.4 als IP-Adresse des virtuellen Geräts an, da die Adresse die private IP-Adresse ist, die der VM des virtuellen Geräts zugewiesen ist. Die Routentabelle, in der diese Route enthalten ist, ist *Subnet2* nicht zugeordnet und wird daher in der Routentabelle für *Subnet2* auch nicht angezeigt. Diese Route setzt die Standardroute für das Präfix 10.0.0.0/16 (ID1) außer Kraft, über die Datenverkehr, der an 10.0.0.1 und 10.0.255.254 im virtuellen Netzwerk adressiert ist, automatisch über den Typ des nächsten Hops für das virtuelle Netzwerk weitergeleitet wurde. Diese Route ist vorhanden, um [Anforderung](#requirements) 3 zu erfüllen und für den gesamten ausgehenden Datenverkehr den Weg über ein virtuelles Gerät zu erzwingen.
3. Diese Route wurde von Azure hinzugefügt, als eine benutzerdefinierte Route für das Adresspräfix 10.0.0.0/24 dem Subnetz *Subnet1* zugeordnet wurde. Der Datenverkehr, der für Adressen zwischen 10.0.0.1 und 10.0.0.254 bestimmt ist, verbleibt im Subnetz und wird nicht an das virtuelle Gerät weitergeleitet, das in der vorherigen Regel (ID2) angegeben ist. Der Grund ist, dass ein längeres Präfix als für die Route ID2 verwendet wird. Diese Route wurde *Subnet2* nicht zugeordnet, sodass sie in der Routentabelle für *Subnet2* nicht angezeigt wird. Diese Route setzt die Route ID2 für Datenverkehr in *Subnet1* effektiv außer Kraft. Diese Route ist vorhanden, um [Anforderung](#requirements) 3 zu erfüllen.
4. Azure hat die Routen unter den IDs 4 und 5 für alle Subnetze in *Virtual-network-1* automatisch hinzugefügt, als für das virtuelle Netzwerk das Peering mit *Virtual-network-2* durchgeführt wurde. *Virtual-network-2* hat in seinem Adressraum zwei Adressbereiche: 10.1.0.0/16 und 10.2.0.0/16, sodass Azure eine Route für jeden Bereich hinzugefügt hat. Wenn die benutzerdefinierte Route unter den Routen-IDs 6 und 7 nicht erstellt worden wäre, würde Datenverkehr, der an eine Adresse zwischen 10.1.0.1 bis 10.1.255.254 und 10.2.0.1 bis 10.2.255.254 gesendet wird, an das per Peering verknüpfte virtuelle Netzwerk weitergeleitet werden. Der Grund ist, dass das Präfix länger als 0.0.0.0/0 ist und nicht innerhalb der Adresspräfixe von anderen Routen liegt. Azure hat den Status automatisch von *Aktiv* in *Ungültig* geändert, als die Routen unter den IDs 6 und 7 hinzugefügt wurden. Der Grund ist, dass sie über die gleichen Präfixe wie die Routen unter den IDs 4 und 5 verfügen und dass Standardrouten von benutzerdefinierten Routen außer Kraft gesetzt werden. Der Status der Routen in ID 4 und 5 ist immer noch *Aktiv* für *Subnet2*, da die Routentabelle, in der sich die benutzerdefinierten Routen in ID 6 und 7 befinden, nicht *Subnet2* zugeordnet ist. Ein VNet-Peering wurde erstellt, um [Anforderung](#requirements) 1 zu erfüllen.
5. Hier gilt die gleiche Erklärung wie für ID4.
6. Azure hat diese Route und die Route unter ID7 hinzugefügt, als die benutzerdefinierten Routen für die Adresspräfixe 10.1.0.0/16 und 10.2.0.0/16 dem Subnetz *Subnet1* zugeordnet wurden. Datenverkehr, der für Adressen zwischen 10.1.0.1 bis 10.1.255.254 und 10.2.0.1 bis 10.2.255.254 bestimmt ist, wird von Azure verworfen und nicht an das per Peering verknüpfte virtuelle Netzwerk weitergeleitet, da Standardrouten von benutzerdefinierten Routen außer Kraft gesetzt werden. Die Routen wurden *Subnet2* nicht zugeordnet, sodass sie in der Routentabelle für *Subnet2* nicht angezeigt werden. Die Routen setzen die Routen ID4 und ID5 für Datenverkehr außer Kraft, der *Subnet1* verlässt. Die Routen ID6 und ID7 sind vorhanden, um [Anforderung](#requirements) 3 zum Verwerfen von Datenverkehr zu erfüllen, der für das andere virtuelle Netzwerk bestimmt ist.
7. Hier gilt die gleiche Erklärung wie für ID6.
8. Azure hat diese Route automatisch für alle Subnetze in *Virtual-network-1* hinzugefügt, als im virtuellen Netzwerk ein Gateway für virtuelle Netzwerke vom Typ VPN erstellt wurde. Azure hat die öffentliche IP-Adresse des Gateways für virtuelle Netzwerke der Routentabelle hinzugefügt. Datenverkehr, der an Adressen zwischen 10.10.0.1 und 10.10.255.254 gesendet wird, wird an das Gateway für virtuelle Netzwerke weitergeleitet. Das Präfix ist länger als 0.0.0.0/0 und liegt nicht innerhalb der Adresspräfixe der anderen Routen. Ein Gateway für virtuelle Netzwerke wurde erstellt, um [Anforderung](#requirements) 2 zu erfüllen.
9. Diese Route wurde von Azure hinzugefügt, als eine benutzerdefinierte Route für das Adresspräfix 10.10.0.0/16 der Routentabelle hinzugefügt wurde, die *Subnet1* zugeordnet ist. Diese Route setzt ID8 außer Kraft. Die Route sendet den gesamten Datenverkehr, der für das lokale Netzwerk bestimmt ist, zur Untersuchung an ein virtuelles Netzwerkgerät, anstatt Datenverkehr direkt lokal weiterzuleiten. Diese Route wurde erstellt, um [Anforderung](#requirements) 3 zu erfüllen.
10. Azure hat diese Route automatisch dem Subnetz hinzugefügt, als ein Dienstendpunkt für einen Azure-Dienst für das Subnetz aktiviert wurde. Azure leitet Datenverkehr aus dem Subnetz über das Azure-Infrastrukturnetzwerk an eine öffentliche IP-Adresse des Diensts weiter. Das Präfix ist länger als 0.0.0.0/0 und liegt nicht innerhalb der Adresspräfixe der anderen Routen. Es wurde ein Dienstendpunkt erstellt, um [Anforderung](#requirements) 3 zu erfüllen und zu ermöglichen, dass für Azure Storage bestimmter Datenverkehr direkt an Azure Storage geleitet wird.
11. Azure hat diese Route automatisch der Routentabelle aller Subnetze in *Virtual-network-1* und *Virtual-network-2* hinzugefügt. Das Adresspräfix 0.0.0.0/0 ist das kürzeste Präfix. Datenverkehr, der an Adressen innerhalb eines längeren Adresspräfix gesendet wird, wird basierend auf anderen Routen weitergeleitet. Standardmäßig leitet Azure den gesamten Datenverkehr, der nicht für die in einer der anderen Routen angegebenen Adressen bestimmt ist, in das Internet weiter. Azure hat den Status für das Subnetz *Subnet1* automatisch von *Aktiv* in *Ungültig* geändert, als eine benutzerdefinierte Route für das Adresspräfix 0.0.0.0/0 (ID12) dem Subnetz zugeordnet wurde. Der Status dieser Route lautet weiterhin *Aktiv* für alle anderen Subnetze in beiden virtuellen Netzwerken, da die Route keinen anderen Subnetzen in anderen virtuellen Netzwerken zugeordnet ist.
12. Diese Route wurde von Azure hinzugefügt, als eine benutzerdefinierte Route für das Adresspräfix 0.0.0.0/0 dem Subnetz *Subnet1* zugeordnet wurde. Die benutzerdefinierte Route gibt 10.0.100.4 als IP-Adresse des virtuellen Geräts an. Diese Route ist *Subnet2* nicht zugeordnet, sodass sie in der Routentabelle für *Subnet2* nicht angezeigt wird. Der gesamte Datenverkehr für alle Adressen, die nicht in den Adresspräfixen der anderen Routen enthalten sind, wird an das virtuelle Gerät gesendet. Mit dem Hinzufügen dieser Route wurde der Status der Standardroute für *Subnet1* für das Adresspräfix 0.0.0.0/0 (ID11) von *Aktiv* in *Ungültig* geändert, da eine Standardroute von einer benutzerdefinierten Route außer Kraft gesetzt wird. Diese Route ist vorhanden, um die dritte [Anforderung](#requirements) zu erfüllen.

#### <a name="subnet2"></a>Subnet2

Die Routentabelle für *Subnet2* in der Abbildung enthält die folgenden Routen:

|`Source`  |State  |Adresspräfixe    |Typ des nächsten Hops             |IP-Adresse des nächsten Hops|
|------- |-------|------              |-------                   |--------           
|Standard |Aktiv |10.0.0.0/16         |Virtuelles Netzwerk           |                   |
|Standard |Aktiv |10.1.0.0/16         |VNet-Peering              |                   |
|Standard |Aktiv |10.2.0.0/16         |VNet-Peering              |                   |
|Standard |Aktiv |10.10.0.0/16        |Gateway des virtuellen Netzwerks   |[X.X.X.X]          |
|Standard |Aktiv |0.0.0.0/0           |Internet                  |                   |
|Standard |Aktiv |10.0.0.0/8          |Keine                      |                   |
|Standard |Aktiv |100.64.0.0/10       |Keine                      |                   |
|Standard |Aktiv |192.168.0.0/16      |Keine                      |                   |

Die Routentabelle für *Subnet2* enthält alle von Azure erstellten Standardrouten und das optionale VNet-Peering sowie die optionalen Routen des Gateways für virtuelle Netzwerke. Azure hat die optionalen Routen allen Subnetzen im virtuellen Netzwerk hinzugefügt, als das Gateway und das Peering dem virtuellen Netzwerk hinzugefügt wurden. Azure hat die Routen für die Adresspräfixe 10.0.0.0/8, 192.168.0.0/16 und 100.64.0.0/10 aus der Routentabelle für *Subnet1* entfernt, als die benutzerdefinierte Route für das Adresspräfix 0.0.0.0/0 *Subnet1* hinzugefügt wurde.  

## <a name="next-steps"></a>Nächste Schritte

* [Create a user-defined route table with routes and a network virtual appliance](tutorial-create-route-table-portal.md) (Erstellen einer benutzerdefinierten Routentabelle mit Routen und einem virtuellen Netzwerkgerät)<br>
* [Konfigurieren von BGP für Azure VPN Gateways mithilfe von PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
* [Use BGP with ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits) (Verwenden von BGP mit ExpressRoute)<br>
* [View all routes for a subnet](diagnose-network-routing-problem.md) (Anzeigen aller Routen für ein Subnetz). In einer benutzerdefinierten Routentabelle werden Ihnen nur die benutzerdefinierten Routen angezeigt, nicht die Standard- und BGP-Routen für ein Subnetz. Beim Anzeigen aller Routen werden die Standard-, BGP- und benutzerdefinierten Routen für das Subnetz angegeben, in dem sich eine Netzwerkschnittstelle befindet.<br>
* [Herausfinden des Typs des nächsten Hops ](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zwischen einem virtuellen Computer und einer IP-Zieladresse. Mit dem Azure Network Watcher-Feature „Nächster Hop“ können Sie ermitteln, ob Datenverkehr ein Subnetz verlässt und an den gewünschten Ort weitergeleitet wird.
