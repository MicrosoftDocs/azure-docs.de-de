---
title: 'Herstellen einer Verbindung mit einem VNET von einem Computer – P2S-VPN und native Azure-Zertifikatauthentifizierung: PowerShell'
description: Verbinden Sie Windows- und macOS-Clients über P2S sowie selbstsignierte oder von einer Zertifizierungsstelle ausgestellte Zertifikate sicher mit einem virtuellen Azure-Netzwerk. In diesem Artikel wird PowerShell verwendet.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed0a60c88c33af70b7d780d6c4735c5f8e65b35b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660405"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Konfigurieren einer Point-to-Site-VPN-Verbindung mit einem VNET unter Verwendung der nativen Azure-Zertifikatauthentifizierung: PowerShell

Dieser Artikel enthält Informationen zum sicheren Verbinden von einzelnen Clients unter Windows, Linux oder macOS mit einem Azure-VNET. P2S-VPN-Verbindungen (Point-to-Site) sind nützlich, wenn Sie an einem Remotestandort (beispielsweise bei der Telearbeit zu Hause oder in einer Konferenz) eine Verbindung mit Ihrem VNET herstellen möchten. Sie können anstelle einer Site-to-Site-VPN-Verbindung auch P2S verwenden, wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen. P2S-Verbindungen erfordern weder ein VPN-Gerät noch eine öffentliche IP-Adresse. P2S erstellt die VPN-Verbindung entweder über SSTP (Secure Socket Tunneling Protocol) oder IKEv2.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="Herstellen einer Verbindung zwischen einem Computer und einem Azure VNET – Diagramm der Point-to-Site-Verbindung":::

Unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md) erfahren Sie mehr über P2S-VPN-Verbindungen. Informationen zum Erstellen dieser Konfiguration über das Azure-Portal finden Sie unter [Konfigurieren einer Point-to-Site-VPN-Verbindung mit einem VNET unter Verwendung der nativen Azure-Zertifikatauthentifizierung: Azure-Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> In vielen Schritten dieses Artikels kann Azure Cloud Shell verwendet werden. Zum Generieren von Zertifikaten kann Cloud Shell jedoch nicht dienen. Darüber hinaus müssen Sie zum Hochladen des öffentlichen Schlüssels des Stammzertifikats entweder Azure PowerShell lokal oder das Azure-Portal verwenden.
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. Anmelden

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. Deklarieren von Variablen

In diesem Artikel werden Variablen verwendet, sodass Sie die Werte problemlos an Ihre eigene Umgebung anpassen können, ohne die Beispiele selbst ändern zu müssen. Deklarieren Sie die gewünschten Variablen. Sie können das unten gezeigte Beispiel verwenden und die Werte nach Bedarf durch Ihre eigenen ersetzen. Wenn Sie Ihre PowerShell-/Cloud Shell-Sitzung zu einem beliebigen Zeitpunkt während der Übung schließen, kopieren Sie einfach die Werte noch mal, und fügen Sie sie ein, um die Variablen erneut zu deklarieren.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. Konfigurieren eines VNET

1. Erstellen Sie eine Ressourcengruppe.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. Erstellen Sie die Subnetzkonfigurationen für das virtuelle Netzwerk, und nennen Sie sie *FrontEnd* und *GatewaySubnet*. Diese Präfixe müssen im deklarierten VNet-Adressraum enthalten sein.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. Erstellen des virtuellen Netzwerks

   In diesem Beispiel ist der Serverparameter „-DnsServer“ optional. Wenn ein Wert angegeben wird, wird kein neuer DNS-Server erstellt. Die IP-Adresse des angegebenen DNS-Servers muss dazu in der Lage sein, die Namen für die Ressourcen aufzulösen, mit denen Sie eine Verbindung von Ihrem VNET aus herstellen möchten. In diesem Beispiel wird eine private IP-Adresse verwendet. Bei dieser IP-Adresse handelt es sich aber wahrscheinlich nicht um die IP-Adresse Ihres DNS-Servers. Achten Sie darauf, dass Sie Ihre eigenen Werte verwenden. Der angegebene Wert wird nicht von der P2S-Verbindung oder dem VPN-Client, sondern von den Ressourcen verwendet, die Sie im VNET bereitstellen.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. Geben Sie die Variablen für das virtuelle Netzwerk an, das Sie erstellt haben.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. Ein VPN-Gateway muss über eine öffentliche IP-Adresse verfügen. Sie fordern zuerst die IP-Adressressource an und verweisen dann beim Erstellen des Gateways des virtuellen Netzwerks darauf. Die IP-Adresse wird bei der Erstellung des VPN-Gateways der Ressource dynamisch zugewiesen. VPN Gateway unterstützt derzeit nur die *dynamische* Zuweisung öffentlicher IP-Adressen. Die Zuweisung einer statischen öffentlichen IP-Adresse kann nicht angefordert werden. Das bedeutet jedoch nicht, dass sich die IP-Adresse ändert, nachdem sie Ihrem VPN-Gateway zugewiesen wurde. Die öffentliche IP-Adresse ändert sich nur, wenn das Gateway gelöscht und neu erstellt wird. Sie ändert sich nicht, wenn die Größe geändert wird, das VPN-Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das VPN-Gateway durchgeführt werden.

   Fordern Sie eine dynamisch zugewiesene öffentliche IP-Adresse an.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. Erstellen des VPN-Gateways

In diesem Schritt konfigurieren und erstellen Sie das Gateway des virtuellen Netzwerks für Ihr VNET.

* „-GatewayType“ muss **Vpn** und „-VpnType“ muss **RouteBased** lauten.
* Mit „-VpnClientProtocol“ werden die Tunnelarten angegeben, die Sie aktivieren möchten. Die Tunneloptionen lauten **OpenVPN, SSTP** und **IKEv2**. Sie können eine dieser beiden Optionen oder jede unterstützte Kombination aktivieren. Wenn Sie mehrere Typen aktivieren möchten, geben Sie die Namen mit Kommas als Trennzeichen an. OpenVPN und SSTP können nicht zusammen aktiviert werden. Der strongSwan-Client unter Android und Linux und der native IKEv2-VPN-Client unter iOS und OSX verwenden nur den IKEv2-Tunnel für die Verbindungsherstellung. Windows-Clients probieren zunächst IKEv2. Wird keine Verbindung hergestellt, verwenden sie SSTP. Sie können den OpenVPN-Client verwenden, um eine Verbindung mit dem OpenVPN-Tunneltyp herzustellen.
* Die Basic-SKU des virtuellen Netzwerkgateways unterstützt weder IKEv2-, noch OpenVPN- noch RADIUS-Authentifizierung. Wenn Sie planen, Verbindungen von Mac-Clients mit Ihrem virtuellen Netzwerk zuzulassen, sollten Sie nicht die Basic-SKU verwenden.
* Je nach ausgewählter [Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md) kann die Erstellung eines VPN-Gateways bis zu 45 Minuten dauern. In diesem Beispiel wird IKEv2 verwendet.

1. Konfigurieren und erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. Die Erstellung eines Gateways dauert etwa 45 Minuten.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. Sobald das Gateway erstellt wurde, können Sie es mithilfe des folgenden Beispiels anzeigen. Wenn Sie PowerShell geschlossen haben oder ein Timeout aufgetreten ist, während Ihr Gateway erstellt wurde, können Sie die [Variablen erneut deklarieren](#declare).

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. Hinzufügen des VPN-Clientadresspools

Nachdem das VPN-Gateway erstellt wurde, können Sie den VPN-Clientadresspool hinzufügen. Der Adresspool des VPN-Clients ist der Bereich, aus dem die VPN-Clients bei der Verbindungsherstellung eine IP-Adresse erhalten. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem VNET überschneiden, mit dem Sie Verbindungen herstellen möchten.

In diesem Beispiel wird der Adresspool des VPN-Clients in einem früheren Schritt als [Variable](#declare) deklariert.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. Generieren von Zertifikaten

>[!IMPORTANT]
> Sie können über Azure Cloud Shell keine Zertifikate generieren. Sie müssen eine der in diesem Abschnitt beschriebenen Methoden verwenden. Wenn Sie PowerShell nutzen möchten, müssen Sie es lokal installieren.
>

Zertifikate werden von Azure zur Authentifizierung von VPN-Clients für Point-to-Site-VPNs verwendet. Sie laden die Informationen des öffentlichen Schlüssels des Stammzertifikats in Azure hoch. Der öffentliche Schlüssel wird dann als „vertrauenswürdig“ betrachtet. Clientzertifikate müssen über das vertrauenswürdige Stammzertifikat erstellt und dann auf jedem Clientcomputer installiert werden, der sich im persönlichen Zertifikatspeicher des aktuellen Benutzers befindet. Mit diesem Zertifikat wird der Client authentifiziert, wenn er eine Verbindung mit dem VNET initiiert. 

Wenn Sie selbstsignierte Zertifikate verwenden, müssen diese anhand bestimmter Parameter erstellt werden. Sie können ein selbstsigniertes Zertifikat anhand der Anweisungen für [PowerShell und Windows 10](vpn-gateway-certificates-point-to-site.md) erstellen. Falls Sie nicht Windows 10 besitzen, können Sie [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) verwenden. Beim Erstellen von selbstsignierten Stammzertifikaten und Clientzertifikaten müssen Sie unbedingt die Schritte in den Anweisungen ausführen. Andernfalls sind die erstellten Zertifikate nicht mit P2S-Verbindungen kompatibel, und Ihnen wird ein Verbindungsfehler angezeigt.

### <a name="root-certificate"></a><a name="cer"></a>Stammzertifikat

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. Nach dem Erstellen des Stammzertifikats [exportieren](vpn-gateway-certificates-point-to-site.md#cer) Sie die Daten des öffentlichen Zertifikats (nicht den privaten Schlüssel) als Base64-codierte X.509-CER-Datei.

### <a name="client-certificate"></a><a name="generate"></a>Clientzertifikat

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. [Exportieren](vpn-gateway-certificates-point-to-site.md#clientexport) Sie das Clientzertifikat, nachdem Sie es erstellt haben. Das Clientzertifikat wird auf die Clientcomputer verteilt, die eine Verbindung herstellen.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. Hochladen der Informationen des öffentlichen Schlüssels des Stammzertifikats

Vergewissern Sie sich, dass das VPN-Gateway erstellt wurde. Nach der Erstellung können Sie die CER-Datei (mit den Informationen zum öffentlichen Schlüssel) für ein vertrauenswürdiges Stammzertifikat in Azure hochladen. Nach dem Hochladen der CER-Datei kann Azure die Datei verwenden, um Clients zu authentifizieren, auf denen ein aus dem vertrauenswürdigen Stammzertifikat generiertes Clientzertifikat installiert ist. Sie können später bei Bedarf weitere vertrauenswürdige Stammzertifikate hochladen – bis zu 20 insgesamt.

>[!NOTE]
> Die CER-Datei kann nicht über Azure Cloud Shell hochgeladen werden. Sie können entweder PowerShell lokal auf Ihrem Computer verwenden oder die [Schritte mit dem Azure-Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile) ausführen.
>

1. Deklarieren Sie die Variable für Ihren Zertifikatnamen, und ersetzen Sie den Wert durch Ihren Wert.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. Ersetzen Sie den Dateipfad durch Ihren eigenen Pfad, und führen Sie dann die Cmdlets aus.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Laden Sie die Informationen des öffentlichen Schlüssels in Azure noch. Nachdem die Zertifikatinformationen hochgeladen wurden, betrachtet Azure es als vertrauenswürdiges Stammzertifikat. Stellen Sie beim Hochladen sicher, PowerShell lokal auf Ihrem Computer auszuführen, oder verwenden Sie stattdessen die [Schritte mit dem Azure-Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Sie können den Upload nicht über Azure Cloud Shell ausführen.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. Installieren eines exportierten Clientzertifikats

Mit den folgenden Schritten installieren Sie ein Clientzertifikat auf einem Windows-Client. Zusätzliche Clients und weitere Informationen finden Sie unter [Installieren eines Clientzertifikats](point-to-site-how-to-vpn-client-install-azure-cert.md).

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

Stellen Sie sicher, dass das Clientzertifikat zusammen mit der gesamten Zertifikatkette als PFX-Datei exportiert wurden (Standardeinstellung). Andernfalls sind die Stammzertifikatinformationen nicht auf dem Clientcomputer vorhanden, und der Client kann nicht ordnungsgemäß authentifiziert werden.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. Konfigurieren des VPN-Clients

In diesem Abschnitt konfigurieren Sie den nativen Client für Ihren Computer so, dass dieser eine Verbindung mit dem Gateway des virtuellen Netzwerks herstellen kann. Sie können beispielsweise in den VPN-Einstellungen Ihres Windows-Computers VPN-Verbindungen hinzufügen. Eine P2S-Verbindung erfordert bestimmte Konfigurationseinstellungen. Mit den folgenden Schritten erstellen Sie ein Paket mit den spezifischen Einstellungen, die Ihr nativer VPN-Client benötigt, um eine P2S-Verbindung mit dem virtuellen Netzwerk herstellen zu können.

Sie können die folgenden Kurzbeispiele verwenden, um das Clientkonfigurationspaket zu generieren und zu installieren. Weitere Informationen zu Paketinhalten sowie zusätzliche Anweisungen zum Generieren und Installieren von VPN-Clientkonfigurationsdateien finden Sie unter [Erstellen und Installieren von VPN-Clientkonfigurationsdateien](point-to-site-vpn-client-configuration-azure-cert.md).

Wenn Sie Ihre Variablen erneut deklarieren müssen, finden Sie diese [hier](#declare).

### <a name="to-generate-configuration-files"></a>So generieren Sie Konfigurationsdateien

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>So installieren Sie das Clientkonfigurationspaket

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Herstellen einer Verbindung mit Azure

### <a name="windows-vpn-client"></a>Windows-VPN-Client

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Mac-VPN-Client

Navigieren Sie im Dialogfeld „Netzwerk“ zum gewünschten Clientprofil, und klicken Sie auf **Verbinden**.
Ausführliche Anweisungen finden Sie unter [Installieren – Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac). Wenn beim Herstellen einer Verbindung Probleme auftreten, stellen Sie sicher, dass das Gateway des virtuellen Netzwerks nicht die Basic-SKU verwendet. Die Basic-SKU wird für Mac-Clients nicht unterstützt.

  ![Mac-Verbindung](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>So überprüfen Sie eine Verbindung

Diese Anweisungen gelten für Windows-Clients.

1. Um sicherzustellen, dass die VPN-Verbindung aktiv ist, öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie *Ipconfig/all* aus.
2. Zeigen Sie die Ergebnisse an. Beachten Sie, dass die erhaltene IP-Adresse aus dem P2S-VPN-Clientadresspool stammt, den Sie in Ihrer Konfiguration angegeben haben. Die Ergebnisse sehen in etwa wie in diesem Beispiel aus:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Herstellen einer Verbindung mit einem virtuellen Computer

Diese Anweisungen gelten für Windows-Clients.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Stellen Sie sicher, dass das VPN-Clientkonfigurationspaket generiert wurde, nachdem die IP-Adressen des DNS-Server für das VNET angegeben wurden. Wenn Sie die IP-Adressen des DNS-Servers aktualisiert haben, generieren Sie ein neues VPN-Clientkonfigurationspaket und installieren es.

* Überprüfen Sie mit „ipconfig“ die IPv4-Adresse, die dem Ethernet-Adapter auf dem Computer zugewiesen ist, von dem aus Sie die Verbindung herstellen. Wenn sich die IP-Adresse im Adressbereich des VNETs befindet, mit dem Sie die Verbindung herstellen, oder im Adressbereich von „VPNClientAddressPool“ liegt, wird dies als sich überschneidender Adressraum bezeichnet. Falls sich Ihr Adressraum auf diese Weise überschneidet, kommt der Netzwerkdatenverkehr nicht bei Azure an, sondern verbleibt im lokalen Netzwerk.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>So fügen Sie ein Stammzertifikat hinzu oder entfernen es

Sie können vertrauenswürdige Stammzertifikate hinzufügen und aus Azure entfernen. Wenn Sie ein Stammzertifikat entfernen, können Clients, für die über dieses Stammzertikat ein Zertifikat generiert wurde, nicht authentifiziert werden und somit auch keine Verbindung herstellen. Wenn Sie für einen Client die Authentifizierung und Verbindungsherstellung durchführen möchten, müssen Sie ein neues Clientzertifikat installieren, das aus einem für Azure vertrauenswürdigen (hochgeladenen) Stammzertifikat generiert wurde. Für diese Schritte müssen die Azure PowerShell-Cmdlets lokal auf Ihrem Computer installiert sein (die Verwendung von Azure Cloud Shell ist nicht möglich). Sie können auch das Azure-Portal verwenden, um Stammzertifikate hinzuzufügen.

**Hinzufügen:**

Sie können Azure bis zu 20 CER-Stammzertifikatdateien hinzufügen. Mit den folgenden Schritten fügen Sie ein Stammzertifikat hinzu. 

1. Bereiten Sie die hochzuladende CER-Datei vor:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Laden Sie die Datei hoch. Sie können jeweils nur eine Datei hochladen.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. So überprüfen Sie, ob die Zertifikatdatei hochgeladen wurde:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**Entfernen:**

1. Deklarieren Sie die Variablen. Ändern Sie die Variablen im Beispiel so, dass sie dem Zertifikat entsprechen, das Sie entfernen möchten.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. Entfernen Sie das Zertifikat.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. Überprüfen Sie mit dem folgenden Beispiel, ob das Zertifikat erfolgreich entfernt wurde.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>So sperren oder reaktivieren Sie ein Clientzertifikat

Sie können Clientzertifikate sperren. Anhand der Zertifikatsperrliste können Sie P2S-Verbindungen basierend auf einzelnen Clientzertifikaten selektiv verweigern. Das ist nicht dasselbe wie das Entfernen eines vertrauenswürdigen Stammzertifikats. Wenn Sie ein vertrauenswürdiges Stammzertifikat (CER-Datei) aus Azure entfernen, wird der Zugriff für alle Clientzertifikate gesperrt, die mit dem gesperrten Stammzertifikat generiert oder signiert wurden. Wenn Sie anstelle des Stammzertifikats ein Clientzertifikat sperren, können die anderen Zertifikate, die auf der Grundlage des Stammzertifikats generiert wurden, weiterhin für die Authentifizierung verwendet werden.

Üblicherweise wird das Stammzertifikat zum Verwalten des Zugriffs auf Team- oder Organisationsebene verwendet. Eine genauer abgestufte Steuerung des Zugriffs für einzelne Benutzer erfolgt hingegen mit gesperrten Clientzertifikaten.

**Zum Widerrufen:**

1. Rufen Sie den Fingerabdruck des Clientzertifikats ab. Weitere Informationen finden Sie unter [Vorgehensweise: Abrufen des Fingerabdrucks eines Zertifikats](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).

1. Kopieren Sie ihn in einen Text-Editor, und entfernen Sie alle Leerzeichen, sodass eine fortlaufende Zeichenfolge entsteht. Diese Zeichenfolge wird im nächsten Schritt als Variable deklariert.

1. Deklarieren Sie die Variablen. Deklarieren Sie den Fingerabdruck, den Sie im vorherigen Schritt abgerufen haben.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Fügen Sie den Fingerabdruck der Liste mit den gesperrten Zertifikaten hinzu. Nach dem Hinzufügen des Fingerabdrucks wird „Erfolgreich“ angezeigt.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. Überprüfen Sie, ob der Fingerabdruck zur Zertifikatsperrliste hinzugefügt wurde.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. Nachdem der Fingerabdruck hinzugefügt wurde, kann das Zertifikat nicht mehr zum Herstellen einer Verbindung verwendet werden. Clients, die versuchen, unter Verwendung dieses Zertifikats eine Verbindung herzustellen, erhalten eine Meldung mit dem Hinweis, dass das Zertifikat nicht mehr gültig ist.

**Reaktivieren:**

Sie können ein Clientzertifikat reaktivieren, indem Sie den Fingerabdruck aus der Liste der gesperrten Clientzertifikate entfernen.

1. Deklarieren Sie die Variablen. Achten Sie darauf, dass Sie den richtigen Fingerabdruck für das Zertifikat deklarieren, das Sie reaktivieren möchten.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Entfernen Sie den Zertifikatfingerabdruck aus der Sperrliste.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. Überprüfen Sie, ob der Fingerabdruck aus der Sperrliste entfernt wurde.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Point-to-Site – Häufig gestellte Fragen

Weitere Informationen zu P2S finden Sie in den [häufig gestellten Fragen zu VPN Gateway](vpn-gateway-vpn-faq.md#P2S).

## <a name="next-steps"></a>Nächste Schritte

Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](../index.yml) . Weitere Informationen zu Netzwerken und virtuellen Computern finden Sie unter [Azure- und Linux-VM-Netzwerke (Übersicht)](../virtual-machines/network-overview.md).

Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Azure Punkt-zu-Standort-Verbindungsprobleme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).