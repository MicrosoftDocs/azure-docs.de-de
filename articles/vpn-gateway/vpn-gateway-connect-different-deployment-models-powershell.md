---
title: 'Verbinden von klassischen virtuellen Netzwerken mit Azure Resource Manager-VNETs: PowerShell'
description: Erstellen einer VPN-Verbindung zwischen klassischen VNETs und Resource Manager-VNETs mithilfe von VPN Gateway und PowerShell
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 7012a696684a4e7bc1d3b52943b11ba413c43037
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376487"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Verbinden von virtuellen Netzwerken aus verschiedenen Bereitstellungsmodellen mit PowerShell

In diesem Artikel wird erläutert, wie Sie klassische VNETs mit Resource Manager-VNETs verbinden, damit die Ressourcen in den separaten Bereitstellungsmodellen miteinander kommunizieren können. Die Schritte in diesem Artikel werden mit PowerShell ausgeführt. Sie können diese Konfiguration aber auch im Azure-Portal durch Auswählen des Artikels in dieser Liste erstellen.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Das Verbinden eines klassischen VNet mit einem Resource Manager-VNet ähnelt dem Verbinden eines VNet mit einem lokalen Standort. Beide Verbindungstypen verwenden ein VPN-Gateway, um einen sicheren Tunnel mit IPsec/IKE bereitzustellen. Sie können auch eine Verbindung zwischen VNets in unterschiedlichen Abonnements und Regionen erstellen. Es ist auch möglich, VNets zu verbinden, die bereits über Verbindungen mit lokalen Netzwerken verfügen, sofern sie mit einem dynamischen oder routenbasierten Gateway konfiguriert wurden. Weitere Informationen zu VNet-zu-VNet-Verbindungen finden Sie am Ende dieses Artikels unter [Häufig gestellte Fragen zu VNet-zu-VNet-Verbindungen](#faq) . 

Falls Sie noch kein Gateway für virtuelle Netzwerke besitzen und keins erstellen möchten, können Sie stattdessen die VNETS mittels VNET-Peering verbinden. Beim VNET-Peering wird kein VPN-Gateway verwendet. Weitere Informationen finden Sie unter [VNet-Peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Voraussetzungen

Die folgenden Schritte führen Sie durch die erforderlichen Einstellungen zum Konfigurieren eines dynamischen oder routenbasierten Gateways für jedes VNet und die Erstellung einer VPN-Verbindung zwischen den Gateways. Diese Konfiguration unterstützt keine statischen oder richtlinienbasierten Gateways.

### <a name="prerequisites"></a><a name="pre"></a>Voraussetzungen

* Beide VNets wurden bereits erstellt. Weitere Informationen zum Erstellen eines virtuellen Resource Manager-Netzwerks finden Sie unter [Erstellen einer Ressourcengruppe und eines virtuellen Netzwerks](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Weitere Informationen zum Erstellen eines klassischen virtuellen Netzwerks finden Sie unter [Erstellen eines virtuellen Netzwerks (klassisch)](/previous-versions/azure/virtual-network/create-virtual-network-classic).
* Die Adressbereiche für die VNets überlappen weder einander noch die Bereiche für andere Verbindungen, mit denen die Gateways verbunden werden können.
* Sie haben die aktuellen PowerShell-Cmdlets installiert. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/) . Installieren Sie sowohl die SM-Cmdlets (Dienstverwaltung) als auch die RM-Cmdlets (Resource Manager). 

### <a name="example-settings"></a><a name="exampleref"></a>Beispieleinstellungen

Sie können diese Werte zum Erstellen einer Testumgebung verwenden oder zum besseren Verständnis der Beispiele in diesem Artikel heranziehen.

**Einstellungen für das klassische VNet**

VNet-Name = ClassicVNet <br>
Standort = USA, Westen <br>
Adressräume des virtuellen Netzwerks = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Name des lokalen Netzwerks = RMVNetLocal <br>
Gatewaytyp = DynamicRouting

**Einstellungen für das Resource Manager-VNet**

VNet-Name = RMVNet <br>
Ressourcengruppe = RG1 <br>
IP-Adressräume des virtuellen Netzwerks = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Standort = USA, Osten <br>
Name der öffentlichen Gateway-IP = gwpip <br>
Lokales Netzwerkgateway = ClassicVNetLocal <br>
Name des Gateways für virtuelle Netzwerke = RMGateway <br>
Gateway-IP-Adressierungskonfiguration = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Abschnitt 1 – Konfigurieren des klassischen VNet
### <a name="1-download-your-network-configuration-file"></a>1. Herunterladen der Netzwerkkonfigurationsdatei
1. Melden Sie sich in der PowerShell-Konsole mit erhöhten Rechten bei Ihrem Azure-Konto an. Das folgende Cmdlet fordert Sie zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto auf. Nach dem Anmelden werden Ihre Kontoeinstellungen heruntergeladen, damit sie Azure PowerShell zur Verfügung stehen. In diesem Abschnitt werden die klassischen Azure PowerShell-Cmdlets für die Dienstverwaltung (Service Management, SM) verwendet.

   ```azurepowershell
   Add-AzureAccount
   ```

   Rufen Sie jetzt Ihr Azure-Abonnement ab.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Wenn Sie über mehr als ein Abonnement verfügen, wählen Sie das Abonnement aus, das Sie verwenden möchten.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exportieren Sie Ihre Azure-Netzwerkkonfigurationsdatei, indem Sie den folgenden Befehl ausführen. Sie können den Speicherort der zu exportierenden Datei bei Bedarf ändern.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Öffnen Sie die heruntergeladene XML-Datei, um sie zu bearbeiten. Ein Beispiel für die Netzwerkkonfigurationsdatei finden Sie unter [Network Configuration Schema](/previous-versions/azure/reference/jj157100(v=azure.100))(Netzwerkkonfigurationsschema).

### <a name="2-verify-the-gateway-subnet"></a>2. Überprüfen des Gatewaysubnetzes
Fügen Sie Ihrem VNet im Element **VirtualNetworkSites** ein Gatewaysubnetz hinzu, sofern noch keines erstellt wurde. Bei der Verwendung der Netzwerkkonfigurationsdatei MUSS für das Gatewaysubnetz der Name „GatewaySubnet“ verwendet werden. Andernfalls kann es von Azure nicht als Gatewaysubnetz erkannt und verwendet werden.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Beispiel:**

```xml
<VirtualNetworkSites>
  <VirtualNetworkSite name="ClassicVNet" Location="West US">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/24</AddressPrefix>
    </AddressSpace>
    <Subnets>
      <Subnet name="Subnet-1">
        <AddressPrefix>10.0.0.0/27</AddressPrefix>
      </Subnet>
      <Subnet name="GatewaySubnet">
        <AddressPrefix>10.0.0.32/29</AddressPrefix>
      </Subnet>
    </Subnets>
  </VirtualNetworkSite>
</VirtualNetworkSites>
```

### <a name="3-add-the-local-network-site"></a>3. Hinzufügen des lokalen Netzwerkstandorts
Der lokale Netzwerkstandort, den Sie hinzufügen, stellt das RM-VNet dar, mit dem Sie eine Verbindung herstellen möchten. Sofern noch nicht vorhanden, fügen Sie der Datei ein **LocalNetworkSites**-Element hinzu. An diesem Punkt der Konfiguration kann für die „VPNGatewayAddress“ eine beliebige gültige öffentliche IP-Adresse verwendet werden, weil wir das Gateway für das Resource Manager-VNet noch nicht erstellt haben. Nachdem wir das Gateway erstellt haben, ersetzen wir diese Platzhalter-IP-Adresse durch die richtige öffentliche IP-Adresse, die dem RM-Gateway zugewiesen wurde.

```xml
<LocalNetworkSites>
  <LocalNetworkSite name="RMVNetLocal">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  </LocalNetworkSite>
</LocalNetworkSites>
```

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Zuordnen des VNET zum lokalen Netzwerkstandort
In diesem Abschnitt geben wir den lokalen Netzwerkstandort an, mit dem Sie das VNet verbinden möchten. In diesem Fall handelt es sich dabei um das Resource Manager-VNet, auf das Sie zuvor verwiesen haben. Stellen Sie sicher, dass die Namen übereinstimmen. In diesem Schritt wird kein Gateway erstellt. Er dient zum Angeben des lokalen Netzwerks, mit dem das Gateway eine Verbindung herstellt.

```xml
<Gateway>
  <ConnectionsToLocalNetwork>
    <LocalNetworkSiteRef name="RMVNetLocal">
      <Connection type="IPsec" />
    </LocalNetworkSiteRef>
  </ConnectionsToLocalNetwork>
</Gateway>
```

### <a name="5-save-the-file-and-upload"></a>5. Speichern und Hochladen der Datei
Speichern Sie die Datei, und importieren Sie sie dann in Azure, indem Sie den folgenden Befehl ausführen. Stellen Sie sicher, dass Sie den Dateipfad wie für Ihre Umgebung erforderlich ändern.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Es wird ein ähnliches Ergebnis anzeigt, dass auf den erfolgreichen Import hinweist.

```output
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                                
Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 
```

### <a name="6-create-the-gateway"></a>6. Erstellen des Gateways

Vor dem Ausführen dieses Beispiels schlagen Sie in der heruntergeladenen Netzwerkkonfigurationsdatei die exakten Namen nach, die von Azure erwartet werden. Die Netzwerkkonfigurationsdatei enthält die Werte für Ihre klassischen virtuellen Netzwerke. Aufgrund der Unterschiede zwischen den Bereitstellungsmodellen werden die Namen für klassische VNETs in einigen Fällen in der Netzwerkkonfigurationsdatei geändert, wenn Einstellungen für klassische VNETs im Azure-Portal erstellt werden. Beispiel: Wenn Sie mithilfe des Azure-Portals ein klassisches VNet mit dem Namen „Classic VNet“ in einer Ressourcengruppe mit dem Namen „ClassicRG“ erstellt haben, wird der Name in der Netzwerkkonfigurationsdatei in „Group ClassicRG Classic VNet“ umgewandelt. Wenn Sie einen VNET-Namen angeben, der Leerzeichen enthält, setzen Sie den Wert in Anführungszeichen.


Verwenden Sie das folgende Beispiel, um ein Gateway mit dynamischem Routing zu erstellen:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Sie können den Gatewaystatus mithilfe des Cmdlets **Get-AzureVNetGateway** überprüfen.

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Abschnitt 2 – Konfigurieren des RM-VNET-Gateways



Als Voraussetzungen müssen Sie bereits ein Resource Manager-VNET erstellt haben. In diesem Schritt erstellen Sie ein VPN-Gateway für das Resource Manager-VNET. Bevor Sie diese Schritte ausführen, müssen Sie die öffentliche IP-Adresse für das Gateway des klassischen VNETs abrufen. 

1. Melden Sie sich in der PowerShell-Konsole bei Ihrem Azure-Konto an. Das folgende Cmdlet fordert Sie zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto auf. Wenn Sie sich angemeldet haben, werden Ihre Kontoeinstellungen heruntergeladen, sodass sie in Azure PowerShell verfügbar sind. Optional können Sie mit der Funktion „Ausprobieren“ Azure Cloud Shell im Browser starten.

   Wenn Sie Azure Cloud Shell verwenden, überspringen Sie das folgende Cmdlet:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Um zu überprüfen, ob Sie das richtige Abonnement verwenden, führen Sie das folgende Cmdlet aus:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Wenn Sie über mehrere Abonnements verfügen, geben Sie das Abonnement an, das Sie verwenden möchten.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Erstellen Sie ein Gateway für das lokale Netzwerk. In einem virtuellen Netzwerk ist mit dem Gateway für das lokale Netzwerk in der Regel Ihr lokaler Standort gemeint. In diesem Fall ist mit dem lokalen Netzwerkgateway Ihr klassisches VNet gemeint. Geben Sie für das Gateway einen Namen ein, anhand dessen Azure darauf verweisen kann, und geben Sie auch das Präfix für den Adressraum an. Azure verwendet das IP-Adresspräfix, um zu ermitteln, welcher Datenverkehr an Ihren lokalen Standort gesendet werden soll. Falls Sie diese Informationen später (vor dem Erstellen des Gateways) anpassen müssen, können Sie die Werte ändern und das Beispiel erneut ausführen.
   
   **-Name** ist der Name, den Sie dem lokalen Netzwerkgateway zuweisen möchten.<br>
   **-AddressPrefix** ist der Adressraum für Ihr klassisches VNET.<br>
   **-GatewayIpAddress** ist die öffentliche IP-Adresse des Gateways für das klassische VNET. Denken Sie daran, den folgenden Beispieltext „n.n.n.n.“ so zu ändern, dass er die richtige IP-Adresse wiedergibt.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Fordern Sie eine öffentliche IP-Adresse an, die dem Gateway des virtuellen Netzwerks für das Resource Manager-VNet zugeordnet wird. Es ist nicht möglich, die IP-Adresse selbst anzugeben. Die IP-Adresse wird dynamisch dem Gateway des virtuellen Netzwerks zugewiesen. Dies bedeutet aber nicht, dass sich die IP-Adresse ändert. Die IP-Adresse des Gateways des virtuellen Netzwerks ändert sich nur, wenn das Gateway gelöscht und neu erstellt wird. Sie ändert sich nicht, wenn die Größe geändert wird, das Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das Gateway durchgeführt werden.

   In diesem Schritt legen wir auch eine Variable fest, die in einem späteren Schritt verwendet wird.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Überprüfen Sie, ob Ihr virtuelles Netzwerk über ein Gatewaysubnetz verfügt. Falls kein Gatewaysubnetz vorhanden ist, fügen Sie es hinzu. Stellen Sie sicher, dass für das Gatewaysubnetz der Name *GatewaySubnet* angegeben ist.
5. Rufen Sie das Subnetz ab, das für das Gateway verwendet wird, indem Sie den folgenden Befehl ausführen. In diesem Schritt legen wir auch eine Variable fest, die im nächsten Schritt verwendet wird.
   
   **-Name** ist der Name Ihres Resource Manager-VNet.<br>
   **-ResourceGroupName** ist die Ressourcengruppe, der das VNET zugeordnet ist. Das Gatewaysubnetz für dieses VNet muss bereits vorhanden sein und den Namen *GatewaySubnet* haben, damit es einwandfrei funktioniert.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Erstellen Sie die Konfiguration für die Gateway-IP-Adressierung. Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. Verwenden Sie das folgende Beispiel, um Ihre Gatewaykonfiguration zu erstellen.

   In diesem Schritt müssen die id-Eigenschaft aus dem Subnetz und die IP-Adressobjekte an die Parameter **-SubnetId** und **-PublicIpAddressId** übergeben werden. Sie können keine einfache Zeichenfolge verwenden. Diese Variablen werden im Schritt zum Anfordern einer öffentlichen IP-Adresse und im Schritt zum Abrufen des Subnetzes festgelegt.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Erstellen Sie das Resource Manager-Gateway für virtuelle Netzwerke, indem Sie den folgenden Befehl ausführen. `-VpnType` muss auf *RouteBased* festgelegt sein. Die Gatewayerstellung kann 45 Minuten oder länger dauern.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Kopieren Sie die öffentliche IP-Adresse, nachdem das VPN-Gateway erstellt wurde. Diese Adresse verwenden Sie beim Konfigurieren der lokalen Netzwerkeinstellungen für Ihr klassisches VNet. Sie können das folgende Cmdlet verwenden, um die öffentliche IP-Adresse abzurufen. Die öffentliche IP-Adresse ist in der Ausgabe als *IpAddress* aufgeführt.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Abschnitt 3 – Ändern der Einstellungen für den lokalen Standort des klassischen VNET

In diesem Abschnitt arbeiten Sie mit dem klassischen VNet. Sie ersetzen die Platzhalter-IP-Adresse, die Sie beim Festlegen der Einstellungen für den lokalen Standort verwendet haben, die zum Herstellen der Verbindung mit dem Resource Manager-VNet-Gateway verwendet wird. Da Sie mit dem klassischen VNET arbeiten, verwenden Sie die lokal auf Ihrem Computer installierte PowerShell-Instanz, nicht die Azure Cloud Shell-Instanz „TryIt“.

1. Exportieren Sie die Netzwerkkonfigurationsdatei.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Ändern Sie den Wert für „VPNGatewayAddress“ mit einem Texteditor. Ersetzen Sie die Platzhalter-IP-Adresse durch die öffentliche IP-Adresse des Resource Manager-Gateways, und speichern Sie dann die Änderungen.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importieren Sie die geänderte Netzwerkkonfigurationsdatei in Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Abschnitt 4 – Erstellen einer Verbindung zwischen den Gateways
Zum Erstellen einer Verbindung zwischen den Gateways ist PowerShell erforderlich. Sie müssen Ihr Azure-Konto hinzufügen, um die klassische Version der PowerShell-Cmdlets zu verwenden. Verwenden Sie hierzu **Add-AzureAccount**.

1. Legen Sie in der PowerShell-Konsole die gemeinsam verwendeten Schlüssel fest. Vor dem Ausführen der Cmdlets schlagen Sie in der heruntergeladenen Netzwerkkonfigurationsdatei die exakten Namen nach, die von Azure erwartet werden. Wenn Sie einen VNet-Namen angeben, der Leerzeichen enthält, setzen Sie den Wert in einfache Anführungszeichen.<br><br>Im folgenden Beispiel ist **-VNetName** der Name des klassischen VNet und **-LocalNetworkSiteName** der Name, den Sie für den Standort des lokalen Netzwerks angegeben haben. **-SharedKey** ist ein Wert, den Sie generieren und angeben. Im Beispiel wurde 'abc123' verwendet, Sie können jedoch einen komplexeren Wert generieren und verwenden. In erster Linie muss der hier angegebene Wert mit dem Wert identisch sein, den Sie im nächsten Schritt beim Erstellen der Verbindung angeben. Es sollte **Status: Erfolgreich** zurückgegeben werden.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Erstellen Sie die VPN-Verbindung, indem Sie die folgenden Befehle ausführen:
   
   Legen Sie die Variablen fest.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Erstellen Sie die Verbindung. Beachten Sie, dass **-ConnectionType** „IPsec“ ist, und nicht „Vnet2Vnet“.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Abschnitt 5 – Überprüfen der Verbindungen

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>So überprüfen Sie die Verbindung des klassischen VNet mit dem Resource Manager-VNet

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure-Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>So überprüfen Sie die Verbindung des Resource Manager-VNet mit dem klassischen VNet

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure-Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Häufig gestellte Fragen zu VNet-zu-VNet-Verbindungen

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
