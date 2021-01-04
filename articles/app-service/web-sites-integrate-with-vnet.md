---
title: Integration einer App in Azure Virtual Network
description: Integrieren einer App in Azure App Service mit virtuellen Azure-Netzwerken
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 764e0262c8a26511c55740aa1797b5ec9b59cc8e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999436"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrieren Ihrer App in ein Azure Virtual Network

In diesem Artikel wird die Azure App Service-Funktion für die VNet-Integration beschrieben, und Sie erfahren, wie Sie die Funktion mit Apps in [Azure App Service](./overview.md) einrichten. Mit [Azure Virtual Network][VNETOverview] (VNET) können Sie viele Ihrer Azure-Ressourcen in einem Netzwerk platzieren, das nicht über das Internet geroutet werden kann. Die VNet-Integrationsfunktion ermöglicht Ihren Apps den Zugriff auf Ressourcen in einem oder über ein VNet. Die VNet-Integration ermöglicht keinen privaten Zugriff auf Ihre Apps.

Azure App Service weist zwei Variationen der VNet-Integrationsfunktion auf:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Aktivieren der VNET-Integration

1. Navigieren Sie zur **Netzwerk** benutzeroberfläche im App Service-Portal. Wählen Sie unter **VNET-Integration** die Option **Zum Konfigurieren hier klicken** aus.

1. Wählen Sie **VNET hinzufügen** aus.

   ![Auswählen der VNET-Integration][1]

1. Die Dropdownliste enthält alle virtuellen Azure Resource Manager-Netzwerke in Ihrem Abonnement in derselben Region. Darunter befindet sich eine Liste der virtuellen Resource Manager-Netzwerke in allen anderen Regionen. Wählen Sie das VNET aus, das Sie integrieren möchten.

   ![Auswählen des VNETs][2]

   * Wenn sich das VNET in derselben Region befindet, erstellen Sie ein neues Subnetz, oder wählen Sie ein leeres bereits vorhandenes Subnetz aus.
   * Wenn Sie ein VNET in einer anderen Region auswählen möchten, müssen Sie über ein VNET-Gateway verfügen, für das Point-to-Site aktiviert ist.
   * Wenn die Integration in ein klassisches VNET erfolgen soll, wählen Sie anstelle der Dropdownliste **Virtuelles Netzwerk** die Option **Klicken Sie hier, um eine Verbindung mit einem klassischen VNET herzustellen** aus. Wählen Sie das gewünschte klassische virtuelle Netzwerk aus. Das Ziel-VNET muss bereits über ein bereitgestelltes Gateway für virtuelle Netzwerke verfügen, für das Point-to-Site aktiviert ist.

    ![Auswählen eines klassischen VNETs][3]

Während der Integration wird Ihre App neu gestartet. Wenn die Integration abgeschlossen ist, werden Details zu dem VNET angezeigt, in das Sie integriert sind.

## <a name="regional-vnet-integration"></a>Regionale VNET-Integration

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Funktionsweise der regionalen VNET-Integration

Apps in App Service werden auf Workerrollen gehostet. Der Tarif „Basic“ und höhere Tarife sind dedizierte Hostingpläne, bei denen keine anderen Kundenworkloads auf den gleichen Workern ausgeführt werden. Die regionale VNET-Integration beruht auf der Einbindung virtueller Schnittstellen mit Adressen im delegierten Subnetz. Da sich die Herkunftsadresse in Ihrem VNET befindet, kann sie ähnlich wie eine VM im VNET auf die meisten Ressourcen zugreifen, die im oder über das VNET verfügbar sind. Die Netzwerkimplementierung unterscheidet sich von der Ausführung eines virtuellen Computers in Ihrem VNET. Aus diesem Grund sind einige Netzwerkfunktionen für dieses Feature noch nicht verfügbar.

![Funktionsweise der regionalen VNET-Integration][5]

Wenn regionale VNet-Integration aktiviert ist, sendet Ihre App ausgehende Aufrufe an das Internet über die üblichen Kanäle. Ihre App verwendet nach wie vor die ausgehenden Adressen, die im Portal für die App-Eigenschaften aufgeführt sind. Was sich für Ihre App ändert, sind Aufrufe an per Dienstendpunkt geschützte Dienste oder RFC 1918-Adressen. Diese Aufrufe werden in Ihr VNET geleitet. Wenn WEBSITE_VNET_ROUTE_ALL auf „1“ festgelegt ist, kann der gesamte ausgehende Datenverkehr in Ihr VNET gesendet werden.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` wird in Windows-Containern aktuell nicht unterstützt.
> 

Die Funktion unterstützt nur eine virtuelle Schnittstelle pro Worker. Eine virtuelle Schnittstelle pro Worker bedeutet eine regionale VNET-Integration pro App Service Plan. Alle Apps im selben App Service-Plan können dieselbe VNet-Integration verwenden. Wenn eine App eine Verbindung mit einem zusätzlichen VNET herstellen muss, müssen Sie einen weiteren App Service-Plan erstellen. Die verwendete virtuelle Schnittstelle ist keine Ressource, auf die Kunden direkten Zugriff haben.

Aufgrund der Funktionsweise dieser Technologie wird der Datenverkehr in Verbindung mit der VNet-Integration nicht in Azure Network Watcher- oder NSG-Flussprotokollen (Netzwerksicherheitsgruppe) aufgeführt.

## <a name="gateway-required-vnet-integration"></a>Von einem Gateway abhängige VNet-Integration

Die von einem Gateway abhängige VNET-Integration unterstützt das Herstellen einer Verbindung mit einem VNET in einer anderen Region oder mit einem klassischen VNET. Von einem Gateway abhängige VNet-Integration:

* Eine App kann nur jeweils mit einem VNET eine Verbindung herstellen.
* Bis zu fünf VNETs können in einen App Service-Plan integriert werden.
* Ermöglicht die Verwendung desselben VNET durch mehrere Apps, ohne dass sich dies auf die für einen App Service-Plan zulässige Gesamtanzahl von VNETs auswirkt. Wenn Sie sechs Apps verwenden, die dasselbe VNET im selben App Service-Plan nutzen, zählt dies als ein verwendetes VNET.
* Unterstützt dank der SLA auf dem Gateway eine SLA mit einer Verfügbarkeit von 99,9 %.
* Ermöglicht Ihren Apps die Verwendung des DNS, mit dem das VNET konfiguriert ist.
* Erfordert ein routenbasiertes Virtual Network-Gateway, das mit einem SSTP-Point-to-Site-VPN konfiguriert ist, bevor es mit einer App verbunden werden kann.

Sie können keine von einem Gateway abhängige VNet-Integration verwenden:

* Mit einem VNET, das mit Azure ExpressRoute verbunden ist.
* Aus einer Linux-App.
* Aus einem [Windows-Container](quickstart-custom-container.md).
* Für den Zugriff auf durch Dienstendpunkte geschützte Ressourcen.
* Mit einem Koexistenzgateway, das sowohl ExpressRoute als auch Point-to-Site- oder Site-to-Site-VPNs unterstützt.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Einrichten eines Gateways in Ihrem virtuellen Azure-Netzwerk ###

So erstellen Sie ein Gateway

1. [Erstellen Sie ein Gatewaysubnetz][creategatewaysubnet] in Ihrem VNET.

1. [Erstellen Sie das VPN-Gateway][creategateway]. Wählen Sie einen routenbasierten VPN-Typ aus.

1. [Legen Sie die Point-to-Site-Adressen fest][setp2saddresses]. Wenn sich das Gateway nicht in der Basic-SKU befindet, muss IKEV2 in der Point-to-Site-Konfiguration deaktiviert und SSTP ausgewählt werden. Der Point-to Site-Adressraum muss in den RFC 1918-Adressblöcken 10.0.0.0/8, 172.16.0.0/12 und 192.168.0.0/16 liegen.

Wenn Sie das Gateway für die Verwendung mit der App Service-VNet-Integration erstellen, müssen Sie kein Zertifikat hochladen. Das Erstellen des Gateways kann 30 Minuten dauern. Sie können Ihre App erst mit Ihrem VNET integrieren, nachdem das Gateway bereitgestellt wurde.

### <a name="how-gateway-required-vnet-integration-works"></a>Funktionsweise der von einem Gateway abhängigen VNet-Integration

Die von einem Gateway abhängige VNet-Integration basiert auf der Point-to-Site-VPN-Technologie. Point-to-Site-VPNs beschränken den Netzwerkzugriff auf den virtuellen Computer, auf dem die App gehostet wird. Apps sind darauf beschränkt, Datenverkehr an das Internet nur über Hybridverbindungen oder VNet-Integration zu senden. Wenn Ihre App über das Portal so konfiguriert wurde, dass sie die von einem Gateway abhängige VNet-Integration verwendet, wird eine komplexe Aushandlung in Ihrem Namen verwaltet, um Zertifikate auf der Gateway- und der Anwendungsseite zu erstellen und zuzuweisen. Als Ergebnis können die Worker, die Ihre Anwendungen hosten, direkte Verbindungen mit dem Gateway des virtuellen Netzwerks im ausgewählten VNET herstellen.

![Funktionsweise der von einem Gateway abhängigen VNet-Integration][6]

### <a name="access-on-premises-resources"></a>Zugriff auf lokale Ressourcen

Apps können durch Integration mit VNETs, die Site-to-Site-Verbindungen aufweisen, auf lokale Ressourcen zugreifen. Wenn Sie die von einem Gateway abhängige VNet-Integration verwenden, aktualisieren Sie Ihre lokalen VPN-Gatewayrouten mit Ihren Point-to-Site-Adressblöcken. Verwenden Sie beim ersten Einrichten des Site-to-Site-VPN die Skripts für die Konfiguration. Die Routen sollten damit richtig eingerichtet werden. Wenn Sie die Point-to-Site-Adressen nach dem Erstellen des Site-to-Site-VPN hinzufügen, müssen Sie die Routen manuell aktualisieren. Die Details zur Vorgehensweise variieren je nach Gateway und sind hier nicht beschrieben. Das Border Gateway Protocol (BGP) kann nicht für eine Site-to-Site-VPN-Verbindung konfiguriert werden.

Für die regionale VNET-Integration ist keine zusätzliche Konfiguration erforderlich, um über Ihr VNET lokale Ressourcen zu erreichen. Sie müssen Ihr VNET lediglich über ExpressRoute oder ein Site-to-Site-VPN mit lokalen Ressourcen verbinden.

> [!NOTE]
> Bei der von einem Gateway abhängigen VNET-Integration wird eine App nicht in ein VNET integriert, das ein ExpressRoute-Gateway enthält. Selbst wenn das ExpressRoute-Gateway im [Koexistenzmodus][VPNERCoex] konfiguriert ist, funktioniert die VNet-Integration nicht. Wenn Sie über eine ExpressRoute-Verbindung auf Ressourcen zugreifen müssen, verwenden Sie die regionale VNET-Integrationsfunktion oder eine in Ihrem VNET ausgeführte [App Service-Umgebung][ASE].
>
>

### <a name="peering"></a>Peering

Wenn Sie Peering mit der regionalen VNet-Integration verwenden, ist keine zusätzliche Konfiguration erforderlich.

Bei der von einem Gateway abhängigen VNet-Integration müssen Sie für das Peering einige zusätzliche Elemente konfigurieren. So konfigurieren Sie Peering für Ihre App

1. Fügen Sie eine Peeringverbindung mit dem VNET hinzu, mit dem Ihre App eine Verbindung herstellt. Aktivieren Sie beim Hinzufügen der Peeringverbindung **Zugriff auf virtuelles Netzwerk zulassen** sowie **Weitergeleiteten Datenverkehr zulassen** und **Gatewaytransit zulassen**.
1. Fügen Sie in dem VNET, das mit dem VNET, mit dem Sie verbunden sind, mittels Peering verknüpft ist, eine Peeringverbindung hinzu. Aktivieren Sie beim Hinzufügen der Peeringverbindung im Ziel-VNET **Zugriff auf virtuelles Netzwerk zulassen**, und wählen Sie **Weitergeleiteten Datenverkehr zulassen** und **Allow remote gateways** (Remotegateways zulassen) aus.
1. Wechseln Sie im Portal zu **App Service-Plan** > **Netzwerk** > **VNet-Integration**-Benutzeroberfläche. Wählen Sie das VNET aus, mit dem Ihre App eine Verbindung herstellt. Fügen Sie den Adressbereich des VNET, das mit dem VNET, mit dem Ihre App verbunden ist, mittels Peering verknüpft ist, im Abschnitt „Routing“ hinzu.

## <a name="manage-vnet-integration"></a>Verwalten der VNet-Integration

Das Verbinden und Trennen der Verbindung mit einem VNET erfolgt auf App-Ebene. Vorgänge, die sich auf die VNet-Integration über mehrere Apps auswirken können, werden auf Ebene des App Service-Plans ausgeführt. Sie können im Portal unter der App über **Netzwerk** > **VNET-Integration** Details zu Ihrem VNET abrufen. Ähnliche Informationen finden Sie auf der App Service-Planebene im Portal unter **App Service-Plan** > **Netzwerk** > **VNET-Integration**.

Der einzige Vorgang, den Sie in der App-Ansicht Ihrer VNET-Integration durchführen können, ist das Trennen der App von dem VNET, mit dem sie derzeit verbunden ist. Um Ihre App von einem VNET zu trennen, wählen Sie **Verbindung trennen** aus. Wenn Sie die Verbindung mit einem VNET trennen, wird die App neu gestartet. Das Trennen der Verbindung führt nicht zu Änderungen in Ihrem VNET. Das Subnetz oder Gateway wird nicht entfernt. Wenn Sie Ihr VNET löschen möchten, trennen Sie zuerst Ihre App vom VNET, und löschen Sie die darin enthaltenen Ressourcen, z. B. Gateways.

Auf der Benutzeroberfläche der VNET-Integration des App Service-Plans werden alle VNET-Integrationen angezeigt, die von den Apps in Ihrem App Service-Plan verwendet werden. Wählen Sie das gewünschte VNET aus, um Informationen dazu anzuzeigen. Es gibt zwei Aktionen, die Sie hier für die VNet-Integration mit erforderlichem Gateway durchführen können:

* **Netzwerk synchronisieren**: Der Vorgang zum Synchronisieren des Netzwerks wird nur für die von einem Gateway abhängige VNet-Integration verwendet. Durch die Netzwerksynchronisierung wird sichergestellt, dass Ihre Zertifikate und Netzwerkinformationen synchronisiert sind. Wenn Sie dem VNET ein DNS hinzufügen oder dieses ändern, führen Sie eine Netzwerksynchronisierung durch. Mit diesem Vorgang werden alle Apps, die dieses VNET verwenden, neu gestartet. Dieser Vorgang funktioniert nicht, wenn Sie eine App und ein VNet verwenden, die zu verschiedenen Abonnements gehören.
* **Routen hinzufügen**: Durch das Hinzufügen von Routen wird ausgehender Datenverkehr in Ihr VNET geleitet.

### <a name="gateway-required-vnet-integration-routing"></a>Routing der von einem Gateway abhängige VNet-Integration
Die in Ihrem VNET definierten Routen werden zum Weiterleiten des Datenverkehrs aus der App in Ihr VNET verwendet. Wenn Sie weiteren ausgehenden Datenverkehr in das VNET senden möchten, fügen Sie diese Adressblöcke hier hinzu. Diese Funktion kann nur mit der von einem Gateway abhängigen VNet-Integration. Routingtabellen wirken sich nicht auf den App-Datenverkehr aus, wenn von einem Gateway abhängige VNet-Integration so verwendet wird wie regionale VNet-Integration.

### <a name="gateway-required-vnet-integration-certificates"></a>Zertifikate der von einem Gateway abhängigen VNet-Integration
Wenn die von einem Gateway abhängige VNet-Integration aktiviert ist, müssen Zertifikate ausgetauscht werden, um die Sicherheit der Verbindung zu gewährleisten. Zusammen mit den Zertifikaten werden die DNS-Konfiguration, Routen und anderen Elemente, mit denen das Netzwerk beschrieben wird, ausgetauscht.

Wenn Zertifikate oder Netzwerkinformationen geändert werden, wählen Sie **Netzwerk synchronisieren** aus. Wenn Sie **Netzwerk synchronisieren** auswählen, bewirkt dies einen kurzen Ausfall der Verbindung zwischen der App und dem VNET. Die App wird nicht neu gestartet, aber der Konnektivitätsverlust kann dazu führen, dass Ihre Website nicht richtig funktioniert.

## <a name="pricing-details"></a>Preisübersicht
Bei der Funktion für die regionale VNet-Integration fallen neben den Gebühren für den App Service-Plantarif keine zusätzlichen Gebühren an.

Bei der Verwendung der von einem Gateway abhängigen VNet-Integrationsfunktion fallen drei Gebühren an:

* **Gebühren laut App Service-Plantarif**: Ihre Apps müssen sich in einem der App Service-Pläne „Standard“, „Premium“, „PremiumV2“ oder „PremiumV3“ befinden. Weitere Informationen zu diesen Kosten finden Sie unter [App Service-Preise][ASPricing].
* **Datenübertragungskosten**: Es gibt eine Gebühr für ausgehende Daten, auch wenn sich das VNET im selben Rechenzentrum befindet. Diese Gebühren werden unter [Datenübertragung – Preisübersicht][DataPricing] beschrieben.
* **Kosten für VPN Gateway**: Es gibt Kosten für das virtuelle Netzwerkgateway, das für das Point-to-Site-VPN erforderlich ist. Weitere Informationen finden Sie unter [VPN-Gateway: Preise][VNETPricing].

## <a name="troubleshooting"></a>Problembehandlung

> [!NOTE]
> VNET-Integration wird für Docker Compose-Szenarien in App Service nicht unterstützt.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

CLI-Unterstützung für die regionale VNet-Integration ist verfügbar. Für den Zugriff auf die folgenden Befehle [installieren Sie die Azure CLI][installCLI].

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

PowerShell-Unterstützung für regionale VNet-Integration ist ebenfalls verfügbar, aber Sie müssen generische Ressourcen mit einem Eigenschaftsarray der Subnetzressourcen-ID (resourceID) erstellen.

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


Für die von einem Gateway abhängige VNet-Integration können Sie App Service mithilfe von PowerShell in ein virtuelles Azure-Netzwerk integrieren. Ein ausführungsbereites Skript finden Sie unter [Verbinden einer App in Azure App Service mit einem virtuellen Azure-Netzwerk](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli?view=azure-cli-latest%2f
[privateendpoints]: networking/private-endpoint.md