---
title: 'Konfigurieren von BGP für ein Azure-VPN-Gateway: Resource Manager und Befehlszeilenschnittstelle | Microsoft-Dokumentation'
description: In diesem Artikel wird die Konfiguration von BGP mit einem Azure-VPN-Gateway mithilfe von Azure Resource Manager und Befehlszeilenschnittstelle beschrieben.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: 51402196c8429797b644357822a1e3c08982b384
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209507"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Konfigurieren von BGP auf Azure-VPN-Gateways mithilfe der Befehlszeilenschnittstelle

In diesem Artikel wird das Aktivieren von BGP für eine standortübergreifende S2S-VPN-Verbindung (Site-to-Site) und eine VNET-zu-VNET-Verbindung (also zwischen virtuellen Netzwerken) mithilfe des Azure Resource Manager-Bereitstellungsmodells und der Azure-Befehlszeilenschnittstelle (CLI) beschrieben.

## <a name="about-bgp"></a>Informationen zu BGP

BGP ist das Standardroutingprotokoll, mit dem im Internet üblicherweise Routing- und Erreichbarkeitsinformationen zwischen mehren Netzwerken ausgetauscht werden. Mit BGP wird für die VPN-Gateways und Ihre lokalen VPN-Geräte, die als BGP-Peers oder Nachbarn bezeichnet werden, der Austausch von „Routen“ ermöglicht. Die Routen informieren beide Gateways über die Verfügbarkeit und Erreichbarkeit von Präfixen zum Durchlaufen der beteiligten Gateways oder Router. BGP ermöglicht auch Transitrouting zwischen mehreren Netzwerken. Hierzu werden von einem BGP-Gateway ermittelte Routen eines BGP-Peers an alle anderen BGP-Peers weitergegeben.

Unter [Übersicht über BGP mit Azure-VPN-Gateways](vpn-gateway-bgp-overview.md) finden Sie weitere Informationen zu den Vorteilen von BGP sowie zu den technischen Anforderungen und den Überlegungen in Bezug auf die Verwendung von BGP.

In diesem Artikel erhalten Sie Hilfestellung bei den folgenden Aufgaben:

* [Aktivieren von BGP für Ihr VPN-Gateway](#enablebgp) (erforderlich)

  Sie können einen der folgenden Abschnitte oder beide abschließen:

* [Herstellen einer standortübergreifenden Verbindung mit BGP](#crossprembgp)
* [Herstellen einer VNet-zu-VNet-Verbindung mit BGP](#v2vbgp)

Jeder dieser drei Abschnitte ist ein zentraler Baustein zum Aktivieren von BGP in Ihrer Netzwerkverbindung. Wenn Sie alle drei Abschnitte abgeschlossen haben, erstellen Sie die Topologie wie im folgenden Diagramm:

![BGP-Topologie](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Zum Erstellen eines komplexeren Multihop-Übertragungsnetzwerks, das Ihren Anforderungen entspricht, können Sie diese Abschnitte auch miteinander kombinieren.

## <a name ="enablebgp"></a>Aktivieren von BGP für Ihr VPN-Gateway

Dieser Abschnitt ist erforderlich, bevor Sie Schritte in den anderen beiden Konfigurationsabschnitten ausführen können. Mit den folgenden Konfigurationsschritten werden die BGP-Parameter des Azure VPN Gateways wie in diesem Diagramm dargestellt eingerichtet:

![BGP-Gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Voraussetzungen

Installieren Sie die aktuelle Version der CLI-Befehle (2.0 oder höher). Informationen zum Installieren der CLI-Befehle finden Sie unter [Installieren von Azure CLI 2.0](/cli/azure/install-azure-cli) und [Erste Schritte mit Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Schritt 1: Erstellen und Konfigurieren von „TestVNet1“

#### <a name="Login"></a>1. Verbinden mit Ihrem Abonnement

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Erstellen einer Ressourcengruppe

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen „TestRG1“ am Standort „eastus“ erstellt. Falls Sie in dieser Region bereits über eine Ressourcengruppe verfügen, die Sie zum Erstellen des virtuellen Netzwerks nutzen möchten, können Sie diese verwenden.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Erstellen von TestVNet1

Im folgenden Beispiel werden ein virtuelles Netzwerk namens „TestVNet1“ und drei Subnetze erstellt: „GatewaySubnet“, „FrontEnd“ und „BackEnd“. Beim Ersetzen der Werte ist es wichtig, dass Sie Ihrem Gatewaysubnetz immer den Namen „GatewaySubnet“ geben. Wenn Sie einen anderen Namen verwenden, tritt beim Erstellen des Gateways ein Fehler auf.

Mit dem ersten Befehl werden der Front-End-Adressraum und das Subnetz „FrontEnd“ erstellt. Mit dem zweiten Befehl wird ein zusätzlicher Adressraum für das Subnetz „BackEnd“ erstellt. Mit dem dritten und vierten Befehl werden das Subnetz „BackEnd“ und das Subnetz „GatewaySubnet“ erstellt.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Schritt 2: Erstellen des VPN-Gateways für „TestVNet1“ mit BGP-Parametern

#### <a name="1-create-the-public-ip-address"></a>1. Erstellen der öffentlichen IP-Adresse

Fordern Sie eine öffentliche IP-Adresse an. Die öffentliche IP-Adresse wird dem VPN-Gateway zugeordnet, das Sie für Ihr virtuelles Netzwerk erstellen.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Erstellen des VPN Gateway mit der AS-Nummer

Erstellen Sie das virtuelle Netzwerkgateway für Ihr TestVNet1. Für BGP ist ein routenbasiertes VPN Gateway erforderlich. Außerdem benötigen Sie den zusätzlichen Parameter `-Asn` zum Festlegen der ASN (Nummer des autonomen Systems) für TestVNet1. Das Erstellen eines Gateways kann einige Zeit dauern (45 Minuten oder mehr). 

Wenn Sie diesen Befehl mit dem Parameter `--no-wait` ausführen, werden kein Feedback und keine Ausgabe angezeigt. Der Parameter `--no-wait` ermöglicht die Erstellung des Gateways im Hintergrund. Das bedeutet nicht, dass die Erstellung des VPN-Gateways sofort abgeschlossen ist.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Abrufen der Azure-BGP-Peer-IP-Adresse

Nachdem das Gateway erstellt wurde, müssen Sie die BGP-Peer-IP-Adresse auf dem Azure-VPN-Gateway abrufen. Diese Adresse ist erforderlich, um das VPN-Gateway als BGP-Peer für Ihre lokalen VPN-Geräte zu konfigurieren.

Führen Sie den folgenden Befehl aus, und überprüfen Sie oben in der Ausgabe den Abschnitt `bgpSettings`:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Nachdem das Gateway erstellt wurde, können Sie es verwenden, um eine standortübergreifende Verbindung oder eine VNET-zu-VNET-Verbindung mit BGP herzustellen.

## <a name ="crossprembgp"></a>Herstellen einer standortübergreifenden Verbindung mit BGP

Um eine standortübergreifende Verbindung herzustellen, müssen Sie ein lokales Netzwerkgateway erstellen, um das lokale VPN-Gerät darzustellen. Anschließend verbinden Sie das Azure-VPN-Gateway mit dem Gateway des lokalen Netzwerks. Diese Schritte ähneln zwar der Erstellung von anderen Verbindungen, aber sie enthalten die zusätzlichen Eigenschaften, die zum Angeben der BGP-Konfigurationsparameter erforderlich sind.

![BGP für Standortübergreifende Verbindungen](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Schritt 1: Erstellen und Konfigurieren des lokalen Netzwerkgateways

In dieser Übung fahren wir mit dem Erstellen der im Diagramm gezeigten Konfiguration fort. Achten Sie darauf, dass Sie die Werte durch die Werte ersetzen, die Sie für die Konfiguration verwenden möchten. Beachten Sie beim Verwenden von Gateways des lokalen Netzwerks Folgendes:

* Der Speicherort und die Ressourcengruppe für das lokale Netzwerkgateway und für das VPN-Gateway können gleich oder auch unterschiedlich sein. Dieses Beispiel zeigt die Gateways in verschiedenen Ressourcengruppen und an verschiedenen Speicherorten.
* Das Mindestpräfix, das Sie für das Gateway des lokalen Netzwerks deklarieren müssen, ist die Hostadresse Ihrer BGP-Peer-IP-Adresse auf Ihrem VPN-Gerät. In diesem Fall ist es das /32-Präfix von „10.51.255.254/32“.
* Zur Erinnerung: Sie müssen zwischen Ihren lokalen Netzwerken und dem virtuellen Azure-Netzwerk unterschiedliche BGP-ASNs verwenden. Wenn sie gleich sind, müssen Sie Ihre VNet-ASN ändern, sofern Ihre lokalen VPN-Geräte die ASN bereits für eine Peerverbindung mit anderen BGP-Nachbarn verwenden.

Stellen Sie vor dem Fortfahren sicher, dass Sie den Abschnitt [Aktivieren von BGP für Ihr VPN-Gateway](#enablebgp) dieser Übung abgeschlossen haben und dass Sie weiterhin über eine Verbindung mit Abonnement 1 verfügen. Beachten Sie, dass Sie in diesem Beispiel eine neue Ressourcengruppe erstellen. Beachten Sie auch die beiden zusätzlichen Parameter für das lokale Netzwerkgateway: `Asn` und `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Schritt 2: Verbinden des VNET-Gateways mit dem Gateway des lokalen Netzwerks

In diesem Schritt erstellen Sie die Verbindung zwischen „TestVNet1“ und „Site5“. Sie müssen den Parameter `--enable-bgp` angeben, um BGP für diese Verbindung zu aktivieren. 

In diesem Beispiel sind das Gateway des virtuellen Netzwerks und das Gateway des lokalen Netzwerks in unterschiedlichen Ressourcengruppen angeordnet. Wenn sich die Gateways in unterschiedlichen Ressourcengruppen befinden, müssen Sie die gesamte Ressourcen-ID der beiden Gateways angeben, um eine Verbindung zwischen den virtuellen Netzwerken einzurichten.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Ermitteln der Ressourcen-ID von VNet1GW

Verwenden Sie die Ausgabe des folgenden Befehls, um die Ressourcen-ID für VNet1GW zu erhalten:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Suchen Sie in der Ausgabe nach der Zeile `"id":`. Die Werte innerhalb der Anführungszeichen sind erforderlich, um im nächsten Abschnitt die Verbindung zu erstellen.

Beispielausgabe:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Kopieren Sie die Werte hinter `"id":` in einen Text-Editor wie Editor, damit Sie sie beim Erstellen der Verbindung problemlos einfügen können. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Ermitteln der Ressourcen-ID von Site5

Verwenden Sie den folgenden Befehl, um die Ressourcen-ID von Site5 über die Ausgabe zu erhalten:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Erstellen der Verbindung zwischen TestVNet1 und Site5

In diesem Schritt erstellen Sie die Verbindung zwischen „TestVNet1“ und „Site5“. Wie bereits erwähnt, sind für dasselbe Azure VPN Gateway BGP- und Nicht-BGP-Verbindungen möglich. Sofern BGP in der Verbindungseigenschaft nicht aktiviert ist, wird BGP für diese Verbindung von Azure nicht aktiviert. Dies gilt auch dann, wenn BGP-Parameter bereits für beide Gateways konfiguriert wurden. Ersetzen Sie die Abonnement-IDs durch Ihre eigenen.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Bei dieser Übung werden im folgenden Beispiel die Parameter aufgelistet, die Sie in den BGP-Konfigurationsabschnitt auf dem lokalen VPN-Gerät eingeben:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Nach einigen Minuten sollte die Verbindung hergestellt werden. Die BGP-Peeringsitzung wird gestartet, nachdem die IPsec-Verbindung hergestellt wurde.

## <a name ="v2vbgp"></a>Herstellen einer VNet-zu-VNet-Verbindung mit BGP

In diesem Abschnitt wird eine VNet-zu-VNet-Verbindung mit BGP hinzugefügt, wie im folgenden Diagramm dargestellt: 

![BGP für VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Die folgende Anleitung ist die Fortsetzung der Schritte in den vorherigen Abschnitten. Sie müssen den Abschnitt [Aktivieren von BGP für Ihr VPN-Gateway](#enablebgp) abschließen, um TestVNet1 und das VPN-Gateway mit BGP zu erstellen und zu konfigurieren.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Schritt 1: Erstellen von „TestVNet2“ und des VPN-Gateways

Es ist wichtig sicherzustellen, dass sich der IP-Adressbereich des neuen virtuellen Netzwerks (TestVNet2) nicht mit einem Ihrer VNet-Bereiche überschneidet.

In diesem Beispiel gehören die virtuellen Netzwerke zum gleichen Abonnement. Sie können VNet-zu-VNet-Verbindungen zwischen verschiedenen Abonnements einrichten. Weitere Informationen finden Sie unter [Konfigurieren einer VNET-zu-VNET-Verbindung](vpn-gateway-howto-vnet-vnet-cli.md). Fügen Sie beim Erstellen der Verbindungen zum Aktivieren von BGP `-EnableBgp $True` hinzu.

#### <a name="1-create-a-new-resource-group"></a>1. Erstellen einer neuen Ressourcengruppe

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Erstellen von TestVNet2 in der neuen Ressourcengruppe

Mit dem ersten Befehl werden der Front-End-Adressraum und das Subnetz „FrontEnd“ erstellt. Mit dem zweiten Befehl wird ein zusätzlicher Adressraum für das Subnetz „BackEnd“ erstellt. Mit dem dritten und vierten Befehl werden das Subnetz „BackEnd“ und das Subnetz „GatewaySubnet“ erstellt.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Erstellen der öffentlichen IP-Adresse

Fordern Sie eine öffentliche IP-Adresse an. Die öffentliche IP-Adresse wird dem VPN-Gateway zugeordnet, das Sie für Ihr virtuelles Netzwerk erstellen.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Erstellen des VPN Gateway mit der AS-Nummer

Erstellen Sie das Gateway für virtuelle Netzwerke für TestVNet2. Die Standard-ASN Ihrer Azure-VPN-Gateways muss überschrieben werden. Die ASNs für die verbundenen virtuellen Netzwerke müssen für die Aktivierung von BGP und Transitrouting unterschiedlich sein.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Schritt 2: Verbinden der Gateways von „TestVNet1“ und „TestVNet2“

In diesem Schritt erstellen Sie die Verbindung zwischen „TestVNet1“ und „Site5“. Sie müssen den Parameter `--enable-bgp` angeben, um BGP für diese Verbindung zu aktivieren.

Im folgenden Beispiel sind das Gateway des virtuellen Netzwerks und das Gateway des lokalen Netzwerks in unterschiedlichen Ressourcengruppen angeordnet. Wenn sich die Gateways in unterschiedlichen Ressourcengruppen befinden, müssen Sie die gesamte Ressourcen-ID der beiden Gateways angeben, um eine Verbindung zwischen den virtuellen Netzwerken einzurichten. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Ermitteln der Ressourcen-ID von VNet1GW 

Ermitteln Sie die Ressourcen-ID von „VNet1GW“ in der Ausgabe des folgenden Befehls:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Ermitteln der Ressourcen-ID von VNet2GW

Ermitteln Sie die Ressourcen-ID von „VNet2GW“ in der Ausgabe des folgenden Befehls:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Erstellen der Verbindungen

Erstellen Sie die Verbindung von „TestVNet1“ mit „TestVNet2“ sowie die Verbindung von „TestVNet2“ mit „TestVNet1“. Ersetzen Sie die Abonnement-IDs durch Ihre eigenen.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Aktivieren Sie BGP für *beide* Verbindungen.
> 
> 

Nach Abschluss dieser Schritte wird die Verbindung innerhalb weniger Minuten hergestellt. Die BGP-Peeringsitzung ist aktiv, sobald die VNET-zu-VNET-Verbindung hergestellt wurde.

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Verbindung hergestellt wurde, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen zu diesen Schritten finden Sie unter [Erstellen eines virtuellen Computers](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
