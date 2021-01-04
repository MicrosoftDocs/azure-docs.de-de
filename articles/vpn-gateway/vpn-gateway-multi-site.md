---
title: 'Verbinden eines VNets mit mehreren Sites über VPN Gateway: Klassisch'
description: Verbinden Sie mehrere lokale Standorte mithilfe eines VPN-Gateways mit einem klassischen virtuellen Netzwerk.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 168bb9e06c73ec27ec1304813023889c9549b8e6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660694"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Hinzufügen einer Standort-zu-Standort-Verbindung mit einem VNet über eine vorhandene VPN-Gatewayverbindung (klassisch)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisch)](vpn-gateway-multi-site.md)
>
>

In diesem Artikel wird beschrieben, wie Sie mithilfe von PowerShell Site-to-Site-Verbindungen (S2S) zu einem VPN-Gateway hinzufügen, für das bereits eine Verbindung besteht. Diese Art der Verbindung wird häufig als Multi-Site-Konfiguration bezeichnet. Die Schritte in diesem Artikel beziehen sich auf virtuelle Netzwerke, die mithilfe des klassischen Bereitstellungsmodells (auch als Dienstverwaltung bezeichnet) erstellt wurden. Diese Schritte gelten nicht für Konfigurationen von parallel bestehenden ExpressRoute- und S2S-Verbindungen.

### <a name="deployment-models-and-methods"></a>Bereitstellungsmodelle und -methoden

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Die folgende Tabelle wird aktualisiert, wenn neue Artikel und weitere Tools für diese Konfiguration verfügbar werden. Wenn ein Artikel verfügbar ist, fügen wir in der Tabelle einen direkten Link dazu ein.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Über das Herstellen einer Verbindung

Sie können mehrere lokale Standorte mit einem einzigen virtuellen Netzwerk verbinden. Dies ist besonders beim Erstellen von Hybrid Cloud-Lösungen interessant. Das Erstellen einer Mehrfachstandort- bzw. Multisite-Verbindung mit Ihrem virtuellen Azure-Netzwerkgateway ähnelt sehr dem Erstellen anderer Standort-zu-Standort-Verbindungen. Sie können dafür ein vorhandenes Azure-VPN-Gateway verwenden, sofern das Gateway dynamisch (routenbasiert) ist.

Wenn bereits ein statisches Gateway mit Ihrem virtuellen Netzwerk verbunden ist, können Sie den Gatewaytyp in „dynamisch“ ändern, um mehrere Standorte einzubinden. Dafür müssen Sie das virtuelle Netzwerk nicht neu erstellen. Stellen Sie vor dem Ändern des Routingtyps sicher, dass Ihr lokales VPN-Gateway routenbasierte VPN-Konfigurationen unterstützt.

![Multi-Site-Diagramm](./media/vpn-gateway-multi-site/multisite.png "Mehrere Websites")

## <a name="points-to-consider"></a>Zu berücksichtigende Punkte

**Es ist nicht möglich, Änderungen an diesem virtuellen Netzwerk über das Portal vorzunehmen.** Sie müssen die Netzwerkkonfigurationsdatei ändern, anstatt das Portal zu verwenden. Wenn Sie Änderungen im Portal vornehmen, überschreiben diese Ihre Multisite-Verweiseinstellungen für dieses virtuelle Netzwerk.

Sie werden sich während der Einrichtung der Multisite-Verbindung schnell mit der Verwendung der Netzwerkkonfigurationsdatei vertraut machen. Wenn jedoch mehrere Personen an Ihrer Netzwerkkonfiguration arbeiten, müssen Sie sicherstellen, dass jeder über diese Einschränkung informiert ist. Dies bedeutet nicht, dass Sie das Portal überhaupt nicht verwenden können. Sie können es für alle anderen Aufgaben verwenden, nur nicht zum Ändern der Konfiguration dieses speziellen virtuellen Netzwerks.

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass Sie über Folgendes verfügen:

* Kompatible VPN-Hardware für jeden lokalen Standort. Lesen Sie unter [Informationen zu VPN-Geräten und Gateways für virtuelle Netzwerkverbindungen](vpn-gateway-about-vpn-devices.md) nach, ob das Gerät, das Sie verwenden möchten, bekanntermaßen kompatibel ist.
* Eine externe öffentliche IPv4-Adresse für jedes VPN-Gerät. Die IP-Adresse darf sich nicht hinter einer NAT befinden. Dies ist eine Voraussetzung
* Eine Person, die sich mit der Konfiguration Ihrer VPN-Hardware auskennt. Sie müssen über profunde Kenntnisse der Konfiguration des VPN-Geräts verfügen oder jemanden mit entsprechenden Fachkenntnissen hinzuziehen.
* Die IP-Adressbereiche, die Sie für Ihr virtuelles Netzwerk verwenden möchten (sofern Sie sie noch nicht erstellt haben).
* Die IP-Adressbereiche für alle lokalen Netzwerkstandorte, mit denen Sie eine Verbindung herstellen. Sie müssen sicherstellen, dass sich die IP-Adressbereiche der lokalen Netzwerkstandorte nicht überlappen. Andernfalls wird das Hochladen der Konfiguration vom Portal oder der REST-API abgelehnt.<br>Wenn Sie z. B. zwei lokale Netzwerkstandorte haben, die beide den IP-Adressbereich 10.2.3.0/24 enthalten, und ein Paket die Zieladresse 10.2.3.3 aufweist, würde Azure nicht wissen, an welchen Standort Sie das Paket senden möchten, weil sich die Adressbereiche überlappen. Zur Vermeidung von Routingproblemen lässt Azure das Hochladen von Konfigurationsdateien mit überlappenden Bereichen nicht zu.

### <a name="working-with-azure-powershell"></a>Arbeiten mit Azure PowerShell

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. Erstellen eines Standort-zu-Standort-VPN
Sie verfügen bereits über ein Standort-zu-Standort-VPN mit einem Gateway mit dynamischem Routing. In diesem Fall können Sie direkt mit dem [Exportieren der Konfigurationseinstellungen für das virtuelle Netzwerk](#export)fortfahren. Andernfalls führen Sie die folgenden Schritte aus:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Wenn Sie bereits über ein virtuelles Standort-zu-Standort-Netzwerk verfügen, für dieses aber ein Gateway mit statischem (richtlinienbasiertem) Routing konfiguriert ist, gehen Sie wie folgt vor:
1. Ändern Sie den Gatewaytyp in dynamisches Routing. Für ein VPN mit mehreren Standorten ist ein Gateway mit dynamischem Routing (auch als „routenbasiert“ bezeichnet) erforderlich. Um den Gatewaytyp zu ändern, müssen Sie zuerst das vorhandene Gateway löschen und dann ein neues erstellen.
2. Konfigurieren Sie das neue Gateway, und erstellen Sie den VPN-Tunnel. Eine Anleitung finden Sie im Abschnitt [Angeben der SKU und des VPN-Typs](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Achten Sie darauf, als Routingtyp „Dynamisch“ anzugeben.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Wenn Sie nicht über ein virtuelles Standort-zu-Standort-Netzwerk verfügen, gehen Sie wie folgt vor:
1. Erstellen Sie Ihr virtuelles Site-to-Site-Netzwerk mithilfe der folgenden Anleitung: [Erstellen eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung](./vpn-gateway-howto-site-to-site-classic-portal.md).  
2. Konfigurieren Sie mithilfe der folgenden Anleitung ein Gateway mit dynamischem Routing: [Konfigurieren eines VPN-Gateways](./vpn-gateway-howto-site-to-site-classic-portal.md). Denken Sie daran, **dynamisches Routing** als Gatewaytyp auszuwählen.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. Exportieren der Netzwerkkonfigurationsdatei

Öffnen Sie die PowerShell-Konsole mit erhöhten Rechten. Verwenden Sie den folgenden Befehl, um zur Dienstverwaltung zu wechseln:

```powershell
azure config mode asm
```

Stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

```powershell
Add-AzureAccount
```

Exportieren Sie Ihre Azure-Netzwerkkonfigurationsdatei, indem Sie den folgenden Befehl ausführen. Sie können den Speicherort der zu exportierenden Datei bei Bedarf ändern.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Öffnen der Netzwerkkonfigurationsdatei
Öffnen Sie die Netzwerkkonfigurationsdatei, die Sie im letzten Schritt heruntergeladen haben. Verwenden Sie dazu einen beliebigen XML-Editor. Die Datei sollte in etwa wie folgt aussehen:

```xml
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <LocalNetworkSites>
      <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
          <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
      </LocalNetworkSite>
      <LocalNetworkSite name="Site2">
        <AddressSpace>
          <AddressPrefix>10.2.0.0/16</AddressPrefix>
          <AddressPrefix>10.3.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
        <AddressSpace>
          <AddressPrefix>10.20.0.0/16</AddressPrefix>
          <AddressPrefix>10.21.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FE">
            <AddressPrefix>10.20.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BE">
            <AddressPrefix>10.20.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.20.2.0/29</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

## <a name="4-add-multiple-site-references"></a>4. Hinzufügen von mehreren Standortverweisen
Beim Hinzufügen oder Entfernen der Informationen für Standortverweise nehmen Sie Konfigurationsänderungen an „ConnectionsToLocalNetwork/LocalNetworkSiteRef“ vor. Wenn Sie einen neuen Verweis für einen lokalen Standort hinzufügen, erstellt Azure einen neuen Tunnel. Das folgende Beispiel zeigt die Netzwerkkonfiguration für eine Einzelstandortverbindung. Speichern Sie die Datei, wenn Sie alle Änderungen vorgenommen haben.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Zum Hinzufügen zusätzlicher Standortverweise (Erstellen einer Konfiguration mit mehreren Standorten) fügen Sie, wie im folgenden Beispiel gezeigt, einfach weitere Zeilen des Typs „LocalNetworkSiteRef“ hinzu:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importieren der Netzwerkkonfigurationsdatei
Importieren Sie die Netzwerkkonfigurationsdatei. Wenn Sie diese Datei mit den Änderungen importieren, werden die neuen Tunnel hinzugefügt. Die Tunnel verwenden das dynamische Gateway, das Sie zuvor erstellt haben. Sie können PowerShell verwenden, um die Datei zu importieren.

## <a name="6-download-keys"></a>6. Herunterladen der Schlüssel
Nachdem die neuen Tunnel hinzugefügt wurden, rufen Sie mit dem PowerShell-Cmdlet „Get-AzureVNetGatewayKey“ die vorinstallierten IPsec-/IKE-Schlüssel für jeden Tunnel ab.

Beispiel:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Sie können auch die REST-API *Get Virtual Network Gateway Shared Key* zum Abrufen der vorinstallierten Schlüssel verwenden.

## <a name="7-verify-your-connections"></a>7. Überprüfen der Verbindungen
Überprüfen Sie den Multisite-Tunnelstatus. Nachdem Sie die Schlüssel für jeden Tunnel heruntergeladen haben, sollten Sie die Verbindungen überprüfen. Verwenden Sie wie im folgenden Beispiel gezeigt „Get-AzureVnetConnection“, um eine Liste der virtuellen Netzwerktunnel abzurufen. „VNet1“ ist der Name des VNets.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Beispielrückgabe:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu VPN-Gateways finden Sie unter [Informationen zu VPN-Gateways](vpn-gateway-about-vpngateways.md).