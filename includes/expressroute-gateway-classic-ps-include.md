---
title: include file
description: include file
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0c05eb88df27ba9e71c0f61bad75457d893a7ce
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563536"
---
> [!NOTE]
> Diese Beispiele gelten nicht für die gemeinsame Verwendung von S2S und ExpressRoute.
> Weitere Informationen zur Verwendung von Gateways in einer Konfiguration für die gemeinsame Verwendung finden Sie unter [Konfigurieren von parallel bestehenden Verbindungen](../articles/expressroute/expressroute-howto-coexist-classic.md#gw).

## <a name="add-a-gateway"></a>Hinzufügen eines Gateways

Wenn Sie ein Gateway zu einem virtuellen Netzwerk mit dem klassischen Ressourcenmodell hinzufügen, ändern Sie die Netzwerkkonfigurationsdatei direkt vor der Erstellung des Gateways. Zum Erstellen eines Gateways müssen in der Datei die in den nachfolgenden Beispielen gezeigten Werte vorhanden sein. Wenn Ihrem virtuellen Netzwerk zuvor ein Gateway zugeordnet war, sind einige dieser Werte bereits vorhanden. Ändern Sie die Datei, um die untenstehenden Werte zu nutzen.

### <a name="download-the-network-configuration-file"></a>Herunterladen der Netzwerkkonfigurationsdatei

1. Laden Sie die Netzwerkkonfigurationsdatei herunter, wie im Artikel [Netzwerkkonfigurationsdatei](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) beschrieben steht. Öffnen Sie die Datei in einem Text-Editor.
2. Fügen Sie einen lokalen Netzwerkstandort zur Datei hinzu. Sie können ein beliebiges gültiges Adresspräfix verwenden. Für das VPN-Gateway können Sie jede gültige IP-Adresse angeben. Die Adresswerte in diesem Abschnitt werden zwar nicht für ExpressRoute-Vorgänge verwendet, sind aber für die Dateivalidierung erforderlich. Im Beispiel steht „branch1“ für den Namen des Standorts. Sie können auch einen anderen Namen verwenden, sollten aber darauf achten, denselben Wert im Abschnitt „Gateway“ in der Datei zu benutzen.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Navigieren Sie zu VirtualNetworkSites, und ändern Sie die Eingaben in den Feldern.

   * Stellen Sie sicher, dass das Gatewaysubnetz für Ihr virtuelles Netzwerk vorhanden ist. Sollte dies nicht der Fall sein, können Sie nun ein Gatewaysubnetz hinzufügen. Der Name muss „GatewaySubnet“ sein.
   * Stellen Sie sicher, dass der Abschnitt „Gateway“ in der Datei vorhanden ist. Fügen Sie ihn anderenfalls hinzu. Dies ist erforderlich, um das virtuelle Netzwerk dem Standort des lokalen Netzwerks zuzuordnen (der das Netzwerk darstellt, mit dem Sie sich verbinden).
   * Vergewissern Sie sich, dass der Verbindungstyp auf „Dediziert“ eingestellt ist. Dies ist für ExpressRoute-Verbindungen erforderlich.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Speichern Sie die Datei, und laden Sie sie in Azure hoch.

### <a name="create-the-gateway"></a>Erstellen des Gateways

Verwenden Sie den folgenden Befehl, um ein Gateway zu erstellen. Ersetzen Sie die vorhandenen durch Ihre eigenen Werte.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Erstellung des Gateways überprüfen

Verwenden Sie den folgenden Befehl, um zu überprüfen, ob das Gateway erstellt wurde. Mit diesem Befehl wird auch die Gateway-ID abgerufen, die Sie für andere Vorgänge benötigen.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Ändern der Größe eines Gateways

Es gibt eine Reihe von [Gateway-SKUs](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Sie können jederzeit den folgenden Befehl verwenden, um die Gateway-SKU zu ändern.

> [!IMPORTANT]
> Dieser Befehl kann nicht für das UltraPerformance-Gateway verwendet werden. Wenn Sie das Gateway in ein UltraPerformance-Gateway ändern möchten, entfernen Sie zunächst das vorhandene ExpressRoute-Gateway, und erstellen Sie anschließend ein neues UltraPerformance-Gateway. Wenn Sie das Gateway von einem UltraPerformance-Gateway herabstufen möchten, entfernen Sie zunächst das UltraPerformance-Gateway, und erstellen Sie anschließend ein neues Gateway.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Entfernen eines Gateways

Verwenden Sie den folgenden Befehl, um ein Gateway zu entfernen.

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```