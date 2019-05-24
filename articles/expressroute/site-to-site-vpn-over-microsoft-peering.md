---
title: Konfigurieren eines Site-to-Site-VPN über Microsoft-Peering ExpressRoute – Azure | Microsoft-Dokumentation
description: Konfigurieren Sie IPsec-/IKE-Verbindungen mit Azure über eine ExpressRoute-/Microsoft-Peeringverbindung mithilfe eines Site-to-Site-VPN-Gateways.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f35ed65b25d469b524e7174affecb45ad7c4735c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115754"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurieren eines Site-to-Site-VPN über ExpressRoute-/Microsoft-Peering

Dieser Artikel hilft Ihnen bei der Konfiguration einer sicheren, verschlüsselten Verbindung zwischen Ihrem lokalen Netzwerk und Ihren virtuellen Azure-Netzwerken (VNets) über eine private ExpressRoute-Verbindung. Sie können Microsoft-Peering nutzen, um einen Site-to-Site-IPsec/IKE-VPN-Tunnel zwischen den von Ihnen ausgewählten lokalen Netzwerken und Azure VNets einzurichten. Die Konfiguration eines sicheren Tunnels über ExpressRoute ermöglicht den Datenaustausch unter Beachtung von Vertraulichkeit, Authentizität und Integrität ohne Wiedergabe.

>[!NOTE]
>Wenn Sie ein Site-to-Site-VPN über Microsoft-Peering einrichten, werden Ihnen die Kosten für das VPN-Gateway und den VPN-Ausgang in Rechnung gestellt. Weitere Informationen finden Sie unter [VPN Gateway: Preise](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="architecture"></a>Architektur


  ![Übersicht über die Konnektivität](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Für Hochverfügbarkeit und Redundanz können Sie mehrere Tunnel über die beiden MSEE-PE-Paare einer ExpressRoute-Verbindung konfigurieren und einen Lastenausgleich zwischen den Tunneln aktivieren.

  ![Optionen für Hochverfügbarkeit](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

VPN-Tunnel über Microsoft-Peering können entweder über ein VPN-Gateway oder ein entsprechendes NVA (Network Virtual Appliance, virtuelles Netzwerkgerät) abgeschlossen werden, das im Azure Marketplace erhältlich ist. Sie können Routen statisch oder dynamisch über die verschlüsselten Tunnel austauschen, ohne den Routenaustausch dem zugrundeliegenden Microsoft-Peering zugänglich zu machen. In den Beispielen in diesem Artikel wird BGP (abweichend von der BGP-Sitzung, die zur Erstellung des Microsoft-Peerings verwendet wurde) verwendet, um Präfixe dynamisch über die verschlüsselten Tunnel auszutauschen.

>[!IMPORTANT]
>Auf der lokalen Seite wird das Microsoft-Peering typischerweise in der DMZ abgeschlossen, während das private Peering in der Kernnetzwerkzone abgeschlossen wird. Die beiden Zonen werden mit Firewalls getrennt. Wenn Sie das Microsoft-Peering ausschließlich für die Aktivierung von sicherem Tunneling über ExpressRoute konfigurieren, denken Sie daran, nur die öffentlichen IPs von Interesse zu filtern, die über das Microsoft-Peering angekündigt werden.
>
>

## <a name="workflow"></a>Workflow

1. Konfigurieren Sie das Microsoft-Peering für Ihre ExpressRoute-Verbindung.
2. Kündigen Sie ausgewählte regionale öffentliche Azure-Präfixe Ihrem lokalen Netzwerk über Microsoft-Peering an.
3. Konfigurieren eines VPN-Gateways und Einrichten von IPsec-Tunneln
4. Konfigurieren Sie das lokale VPN-Gerät.
5. Erstellen Sie die Site-to-Site-IPsec/IKE-Verbindung.
6. (Optional) Konfigurieren Sie Firewalls/Filterung auf dem lokalen VPN-Gerät.
7. Testen und überprüfen Sie die IPsec-Kommunikation über die ExpressRoute-Verbindung.

## <a name="peering"></a>1. Konfigurieren des Microsoft-Peerings

Um eine Site-to-Site-VPN-Verbindung über ExpressRoute zu konfigurieren, müssen Sie ExpressRoute-/Microsoft-Peering nutzen.

* Um eine neue ExpressRoute-Verbindung zu konfigurieren, starten Sie mit dem Artikel [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md), und fahren Sie dann mit [Erstellen und Ändern einer ExpressRoute-Verbindung ](expressroute-howto-circuit-arm.md) fort.

* Wenn Sie bereits über eine ExpressRoute-Verbindung verfügen, aber Microsoft-Peering nicht konfiguriert haben, konfigurieren Sie Microsoft-Peering mithilfe des Artikels [Erstellen und Ändern des Peerings für eine ExpressRoute-Verbindung](expressroute-howto-routing-arm.md#msft).

Sobald Sie Ihre Verbindung und Microsoft-Peering konfiguriert haben, können Sie sie ganz einfach auf der Seite **Übersicht** im Azure-Portal anzeigen.

![Verbindung](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. Konfigurieren von Routenfiltern

Durch einen Routenfilter können Sie die Dienste identifizieren, die Sie über das Microsoft-Peering Ihrer ExpressRoute-Verbindung nutzen möchten. Im Wesentlichen handelt es sich um eine Whitelist aller BGP-Communitywerte. 

![Routenfilter](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

In diesem Beispiel erfolgt die Bereitstellung nur in der Region *Azure USA, Westen 2*. Eine Routenfilterregel wurde hinzugefügt, um nur die Anzeige von Regionalpräfixen von Azure USA, Westen 2 zu erlauben, die den BGP-Communitywert *12076:51026* haben. Sie geben die Regionalpräfixe an, die Sie zulassen möchten, indem Sie **Regel verwalten** auswählen.

Innerhalb des Routenfilters müssen Sie auch die ExpressRoute-Verbindungen auswählen, für die der Routenfilter gilt. Sie können die ExpressRoute-Verbindungen wählen, indem Sie **Verbindung hinzufügen** auswählen. In der vorigen Abbildung ist der Routenfilter der ExpressRoute-Beispielverbindung zugeordnet.

### <a name="configfilter"></a>2.1 Konfigurieren des Routenfilters

Konfigurieren Sie einen Routenfilter. Anweisungen finden Sie unter [Konfigurieren von Routenfiltern für das Microsoft-Peering](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2 Überprüfen von BGP-Routen

Nachdem Sie das Microsoft-Peering erfolgreich über Ihre ExpressRoute-Verbindung erstellt und der Verbindung einen Routenfilter zugeordnet haben, können Sie die von MSEEs empfangenen BGP-Routen auf den PE-Geräten überprüfen, die das Peering mit den MSEEs aufweisen. Der Überprüfungsbefehl variiert je nach Betriebssystem Ihrer PE-Geräte.

#### <a name="cisco-examples"></a>Cisco-Beispiele

Dieses Beispiel verwendet einen Cisco IOS-XE-Befehl. Im Beispiel wird eine VRF-Instanz (Virtual Routing and Forwarding) verwendet, um den Peeringdatenverkehr zu isolieren.

```
show ip bgp vpnv4 vrf 10 summary
```

Die folgende Teilausgabe zeigt, dass 68 Präfixe vom Nachbarn \*.243.229.34 mit der ASN 12076 (MSEE) empfangen wurden:

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Um die Liste der vom Nachbarn empfangenen Präfixe anzuzeigen, verwenden Sie das folgende Beispiel:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Um zu bestätigen, dass Sie den richtigen Satz von Präfixen erhalten, können Sie eine Querprüfung durchführen. Die folgende Ausgabe des Azure PowerShell-Befehls listet die über Microsoft-Peering angekündigten Präfixe für jeden der Dienste und für jede der Azure-Regionen auf:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="vpngateway"></a>3. Konfigurieren des VPN-Gateways und der IPsec-Tunnels

In diesem Abschnitt werden die IPsec VPN-Tunnel zwischen dem Azure-VPN-Gateway und dem lokalen VPN-Gerät erstellt. In den Beispielen werden VPN-Geräte des Typs Cisco Cloud Service Router (CSR1000) verwendet.

Das folgende Diagramm zeigt die IPsec VPN-Tunnel, die zwischen dem lokalen VPN-Gerät 1 und dem Azure VPN-Gateway-Instanzpaar eingerichtet wurden. Die beiden IPsec-VPN-Tunnel, die zwischen dem lokalen VPN-Gerät 2 und dem Azure-VPN-Gateway-Instanzpaar eingerichtet wurden, sind im Diagramm nicht dargestellt und die Konfigurationsdetails nicht aufgeführt. Zusätzliche VPN-Tunnel verbessern jedoch die Hochverfügbarkeit.

  ![VPN-Tunnel](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Über das IPsec-Tunnelpaar wird eine eBGP-Sitzung aufgebaut, um private Netzwerkrouten auszutauschen. Das folgende Diagramm zeigt die eBGP-Sitzung, die über das IPsec-Tunnelpaar aufgebaut wurde:

  ![eBGP-Sitzungen über Tunnelpaar](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Das folgende Diagramm zeigt den abstrahierten Überblick über das Beispielnetzwerk:

  ![Beispielnetzwerk](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Informationen zu den Beispielen von Azure Resource Manager-Vorlagen

In den Beispielen werden das VPN-Gateway und die IPsec-Tunnelabschlüsse mithilfe einer Azure Resource Manager-Vorlage konfiguriert. Wenn Sie mit der Verwendung von Resource Manager-Vorlagen noch nicht vertraut oder sich mit den Grundlagen von Resource Manager-Vorlagen vertraut machen möchten, siehe [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md). Die Vorlage in diesem Abschnitt erstellt eine neue Azure-Umgebung (VNet). Wenn Sie bereits über ein VNet verfügen, können Sie in der Vorlage darauf verweisen. Wenn Sie mit den VPN-Gateway-IPsec/IKE-Site-to-Site Konfigurationen nicht vertraut sind, lesen Sie [Erstellen einer Site-to-Site-Verbindung](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Sie müssen keine Azure Resource Manager-Vorlagen verwenden, um diese Konfiguration zu erstellen. Sie können diese Konfiguration über das Azure-Portal oder PowerShell erstellen.
>
>

### <a name="variables3"></a>3.1 Deklarieren der Variablen

In diesem Beispiel entsprechen die Variablendeklarationen dem Beispielnetzwerk. Wenn Sie Variablen deklarieren, ändern Sie diesen Abschnitt entsprechend Ihrer Umgebung.

* Die Variable **LocalAddressPrefix** ist ein Array lokaler IP-Adressen zum Abschließen des IPsec-Tunnels.
* **gatewaySku** bestimmt den VPN-Durchsatz. Weitere Informationen zu „gatewaySku“ und „vpnType“ finden Sie unter [Konfigurationseinstellungen für VPN Gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Preise finden Sie unter [VPN Gateway: Preise](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Legen Sie **vpnType** auf **RouteBased** fest.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="vnet"></a>3.2 Erstellen des virtuellen Netzwerks (VNet)

Wenn Sie den VPN-Tunneln ein vorhandenes VNet zuordnen, können Sie diesen Schritt überspringen.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="ip"></a>3.3 Zuweisen öffentlicher IP-Adressen zu VPN-Gatewayinstanzen
 
Weisen Sie jeder Instanz eines VPN-Gateways eine öffentliche IP-Adresse zu.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="termination"></a>3.4 Angeben des lokalen VPN-Tunnelabschlusses (Gateway des lokalen Netzwerks)

Auf die lokalen VPN-Geräte wird als **Gateway des lokalen Netzwerks** verwiesen. Der folgende JSON-Codeausschnitt gibt auch Details des BGP-Remotepeers an:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="creategw"></a>3.5 Erstellen des VPN-Gateways

In diesem Abschnitt der Vorlage wird das VPN-Gateway mit den erforderlichen Einstellungen für eine Aktiv-Aktiv-Konfiguration konfiguriert. Berücksichtigen Sie die folgenden Anforderungen:

* Erstellen Sie das VPN-Gateway mit **RouteBased** als „VpnType“. Diese Einstellung ist obligatorisch, wenn Sie das BGP-Routing zwischen dem VPN-Gateway und lokalen VPN aktivieren möchten.
* Um VPN-Tunnel zwischen den beiden Instanzen des VPN-Gateways und einem bestimmten lokalen Gerät im Aktiv-Aktiv-Modus einzurichten, wird der Parameter **activeActive** in der Resource Manager-Vorlage auf **true** festgelegt. Weitere Informationen zu hoch verfügbaren VPN-Gateways finden Sie unter [Standortübergreifende Verbindungen und VNet-zu-VNet-Verbindungen mit hoher Verfügbarkeit](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Um eBGP-Sitzungen zwischen den VPN-Tunneln zu konfigurieren, müssen Sie auf beiden Seiten zwei verschiedene ASNs angeben. Geben Sie vorzugsweise private ASN-Nummern an. Weitere Informationen finden Sie unter [Übersicht über BGP mit Azure-VPN-Gateways](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="ipsectunnel"></a>3.6 Einrichten der IPsec-Tunnel

Die letzte Aktion des Skripts erstellt IPsec-Tunnel zwischen dem Azure-VPN-Gateway und dem lokalen VPN-Gerät.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="device"></a>4. Konfigurieren des lokalen VPN-Geräts

Das Azure-VPN-Gateway ist mit vielen VPN-Geräten verschiedener Hersteller kompatibel. Konfigurationsinformationen und Geräte, die mit dem VPN-Gateway funktionieren, finden Sie unter [Informationen zu VPN-Geräten](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Beim Konfigurieren des VPN-Geräts benötigen Sie die folgenden Elemente:

* Einen gemeinsam verwendeten Schlüssel. Dies ist derselbe gemeinsame Schlüssel, den Sie beim Erstellen Ihrer Site-to-Site-VPN-Verbindung angeben. In den Beispielen wird ein einfacher gemeinsam verwendeter Schlüssel genutzt. Es wird empfohlen, einen komplexeren Schlüssel zu generieren.
* Die öffentliche IP-Adresse Ihres VPN-Gateways. Sie können die öffentliche IP-Adresse mit dem Azure-Portal, mit PowerShell oder mit der CLI anzeigen. Sie können die öffentliche IP-Adresse des VPN-Gateways mit dem Azure-Portal ermitteln, indem Sie zu „Gateways für virtuelle Netzwerke“ navigieren und auf den Namen Ihres Gateways klicken.

Üblicherweise sind eBGP-Peers direkt miteinander verbunden (oft über eine WAN-Verbindung). Wenn Sie jedoch eBGP over IPsec-VPN-Tunnel über ExpressRoute-/Microsoft-Peering konfigurieren, gibt es zwischen den eBGP-Peers mehrere Routingdomänen. Verwenden Sie den Befehl **ebgp-multihop**, um die eBGP-Nachbarbeziehung zwischen den beiden nicht direkt verbundenen Peers herzustellen. Die ganze Zahl, die auf den Befehl „ebgp-multihop“ folgt, gibt den TTL-Wert in den BGP-Paketen an. Der Befehl **maximum-paths eibgp 2** ermöglicht den Lastenausgleich des Datenverkehrs zwischen den beiden BGP-Pfaden.

### <a name="cisco1"></a>Beispiel für Cisco CSR1000

Das folgende Beispiel zeigt die Konfiguration für Cisco CSR1000 in einem virtuellen Hyper-V-Computer als lokales VPN-Gerät:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="firewalls"></a>5. Konfigurieren von VPN-Gerätefilterung und Firewalls (optional)

Konfigurieren Sie Ihre Firewall und Filterung entsprechend Ihren Anforderungen.

## <a name="testipsec"></a>6. Testen und Überprüfen des IPsec-Tunnels

Der Status von IPsec-Tunneln kann auf dem Azure-VPN-Gateway mithilfe von PowerShell-Befehlen überprüft werden:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Beispielausgabe:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Um den Status der Tunnel auf den Azure-VPN-Gatewayinstanzen unabhängig zu überprüfen, verwenden Sie das folgende Beispiel:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Beispielausgabe:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Sie können auch den Tunnelstatus auf dem lokalen VPN-Gerät überprüfen.

Beispiel für Cisco CSR1000:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Beispielausgabe:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Das Leitungsprotokoll für die VTI (Virtual Tunnel Interface) ändert sich erst nach Abschluss der IKE-Phase 2 in „up“. Der folgende Befehl überprüft die Sicherheitszuordnung:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verifye2e"></a>Überprüfen der End-to-End-Konnektivität zwischen dem internen lokalen Netzwerk und dem Azure VNet

Wenn die IPsec-Tunnel in Betrieb und die statischen Routen ordnungsgemäß festgelegt sind, sollten Sie in der Lage sein, die IP-Adresse des BGP-Remotepeers zu pingen:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verifybgp"></a>Überprüfen der BGP-Sitzungen über IPsec

Überprüfen Sie auf dem Azure-VPN-Gateway den Status des BGP-Peers:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Beispielausgabe:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Um die Liste der Netzwerkpräfixe zu überprüfen, die über eBGP vom lokalen VPN-Konzentrator empfangen wurden, können Sie nach dem Attribut „Origin“ filtern:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

In der Beispielausgabe ist die ASN 65010 die autonome BGP-Systemnummer im lokalen VPN.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

So zeigen Sie die Liste der angekündigten Routen an

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Beispielausgabe:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Beispiel für den lokalen Cisco CSR1000:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

Die Liste der Netzwerke, die dem Azure-VPN-Gateway vom lokalen Cisco CSR1000 angekündigt wurden, kann mit dem folgenden Befehl angezeigt werden:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren des Netzwerkleistungsmonitors für ExpressRoute](how-to-npm.md)

* [Hinzufügen einer Site-to-Site-Verbindung (S2S) zu einem VNet mit einer vorhandenen VPN-Gatewayverbindung](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
