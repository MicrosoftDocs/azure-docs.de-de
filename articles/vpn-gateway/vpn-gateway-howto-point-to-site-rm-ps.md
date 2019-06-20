---
title: 'Herstellen einer Point-to-Site-Verbindung zwischen einem Computer und einem virtuellen Azure-Netzwerk unter Verwendung der nativen Azure-Zertifikatauthentifizierung: PowerShell | Microsoft-Dokumentation'
description: Verbinden Sie Windows- und Mac OS X-Clients auf sichere Weise mit einem virtuellen Azure-Netzwerk, indem Sie eine P2S-Verbindung und selbstsignierte oder von einer Zertifizierungsstelle ausgestellte Zertifikate verwenden. In diesem Artikel wird PowerShell verwendet.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: 822cbc7401de90d63f9079561ced0dfbb911fa2c
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989445"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Konfigurieren einer Point-to-Site-Verbindung mit einem VNET unter Verwendung der nativen Azure-Zertifikatauthentifizierung: PowerShell

Dieser Artikel enthält Informationen zum sicheren Verbinden von einzelnen Clients, auf denen Windows, Linux oder Mac OS X ausgeführt wird, mit einem Azure-VNET. Eine P2S-VPN-Verbindung ist nützlich, wenn Sie an einem Remotestandort (beispielsweise bei der Telearbeit zu Hause oder in einer Konferenz) eine Verbindung mit Ihrem VNET herstellen möchten. Sie können anstelle einer Site-to-Site-VPN-Verbindung auch P2S verwenden, wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen. Point-to-Site-Verbindungen erfordern weder ein VPN-Gerät noch eine öffentliche IP-Adresse. P2S erstellt die VPN-Verbindung entweder über SSTP (Secure Socket Tunneling Protocol) oder IKEv2. Weitere Informationen zu Point-to-Site-VPN-Verbindungen finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md).

![Herstellen einer Verbindung zwischen einem Computer und einem Azure VNet – Point-to-Site-Verbindungsdiagramm](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Architecture

Für native Point-to-Site-Verbindungen mit Azure-Zertifikatauthentifizierung werden die folgenden Komponenten verwendet, die Sie in dieser Übung konfigurieren:

* Ein RouteBased-VPN-Gateway.
* Der öffentliche Schlüssel (CER-Datei) für ein Stammzertifikat (in Azure hochgeladen). Sobald das Zertifikat hochgeladen wurde, wird es als vertrauenswürdiges Zertifikat betrachtet und für die Authentifizierung verwendet.
* Ein Clientzertifikat, das von der Stammzertifizierungsstelle generiert wird. Das auf jedem Clientcomputer, für den eine Verbindung mit dem VNET hergestellt wird, installierte Clientzertifikat. Dieses Zertifikat wird für die Clientauthentifizierung verwendet.
* Eine VPN-Clientkonfiguration. Die VPN-Clientkonfigurationsdateien enthalten die erforderlichen Informationen, damit der Client eine Verbindung mit dem VNET herstellen kann. Die Dateien konfigurieren den vorhandenen nativen VPN-Client des Betriebssystems. Jeder Client, der eine Verbindung herstellt, muss mit den Einstellungen in den Konfigurationsdateien konfiguriert werden.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

In den meisten Schritten dieses Artikels kann Cloud Shell verwendet werden. Allerdings müssen Sie zum Hochladen des öffentlichen Schlüssels des Stammzertifikats entweder PowerShell lokal oder das Azure-Portal verwenden.

### <a name="example"></a>Beispielwerte

Sie können die Beispielwerte zum Erstellen einer Testumgebung oder zum besseren Verständnis der Beispiele in diesem Artikel nutzen. Die Variablen werden in Abschnitt [1](#declare) des Artikels festgelegt. Sie können diese Schritte entweder als exemplarische Vorgehensweise durchlaufen und die Werte nicht ändern, oder Sie können die Werte Ihrer Umgebung entsprechend ändern.

* **Name: VNet1**
* **Adressraum: 192.168.0.0/16** und **10.254.0.0/16**<br>In diesem Beispiel werden mehrere Adressräume verwendet, um zu veranschaulichen, dass diese Konfiguration für mehrere Adressräume geeignet ist. Für diese Konfiguration sind aber nicht mehrere Adressräume erforderlich.
* **Subnetzname: FrontEnd**
  * **Subnetzadressbereich: 192.168.1.0/24**
* **Subnetzname: BackEnd**
  * **Subnetzadressbereich: 10.254.1.0/24**
* **Subnetzname: GatewaySubnet**<br>Der Subnetzname *GatewaySubnet* ist erforderlich, damit das VPN-Gateway funktioniert.
  * **Adressbereich des Gatewaysubnetzes: 192.168.200.0/24** 
* **VPN-Clientadresspool: 172.16.201.0/24**<br>VPN-Clients, die sich über diese Punkt-zu-Standort-Verbindung mit dem VNet verbinden, erhalten eine IP-Adresse aus dem VPN-Clientadresspool.
* **Abonnement:** Falls Sie über mehrere Abonnements verfügen, vergewissern Sie sich, dass Sie das richtige Abonnement verwenden.
* **Ressourcengruppe: TestRG**
* **Standort: USA, Osten**
* **DNS-Server: IP-Adresse** des DNS-Servers, der für die Namensauflösung verwendet werden soll. (Optional)
* **GW-Name: Vnet1GW**
* **Name der öffentlichen IP: VNet1GWPIP**
* **VpnType: RouteBased** 

## <a name="declare"></a>1. Anmelden und Festlegen von Variablen

In diesem Abschnitt melden Sie sich an und deklarieren die für diese Konfiguration verwendeten Werte. Die deklarierten Werte werden in den Beispielskripts verwendet. Ändern Sie die Werte Ihrer Umgebung entsprechend. Alternativ können Sie diese Schritte mit den deklarierten Werten als Übung durchlaufen.

### <a name="sign-in"></a>Anmelden

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>Deklarieren von Variablen

Deklarieren Sie die gewünschten Variablen. Verwenden Sie das unten gezeigte Beispiel, und ersetzen Sie die Werte nach Bedarf durch Ihre eigenen. Wenn Sie Ihre PowerShell-/Cloud Shell-Sitzung zu einem beliebigen Zeitpunkt während der Übung schließen, kopieren Sie einfach die Werte noch mal, und fügen Sie sie ein, um die Variablen erneut zu deklarieren.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="ConfigureVNet"></a>2. Konfigurieren eines VNET

1. Erstellen Sie eine Ressourcengruppe.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```
2. Erstellen Sie die Subnetzkonfigurationen für das virtuelle Netzwerk, und benennen Sie sie mit *FrontEnd*, *BackEnd* und *GatewaySubnet*. Diese Präfixe müssen im deklarierten VNet-Adressraum enthalten sein.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $besub = New-AzVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```
3. Erstellen des virtuellen Netzwerks

   In diesem Beispiel ist der Serverparameter „-DnsServer“ optional. Wenn ein Wert angegeben wird, wird kein neuer DNS-Server erstellt. Die IP-Adresse des angegebenen DNS-Servers muss dazu in der Lage sein, die Namen für die Ressourcen aufzulösen, mit denen Sie eine Verbindung von Ihrem VNET aus herstellen möchten. In diesem Beispiel wird eine private IP-Adresse verwendet. Bei dieser IP-Adresse handelt es sich aber wahrscheinlich nicht um die IP-Adresse Ihres DNS-Servers. Achten Sie darauf, dass Sie Ihre eigenen Werte verwenden. Der angegebene Wert wird nicht von der P2S-Verbindung oder dem VPN-Client, sondern von den Ressourcen verwendet, die Sie im VNET bereitstellen.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Geben Sie die Variablen für das virtuelle Netzwerk an, das Sie erstellt haben.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```
5. Ein VPN-Gateway muss über eine öffentliche IP-Adresse verfügen. Sie fordern zuerst die IP-Adressressource an und verweisen dann beim Erstellen des Gateways des virtuellen Netzwerks darauf. Die IP-Adresse wird bei der Erstellung des VPN-Gateways der Ressource dynamisch zugewiesen. VPN Gateway unterstützt derzeit nur die *dynamische* Zuweisung öffentlicher IP-Adressen. Die Zuweisung einer statischen öffentlichen IP-Adresse kann nicht angefordert werden. Das bedeutet jedoch nicht, dass sich die IP-Adresse ändert, nachdem sie Ihrem VPN-Gateway zugewiesen wurde. Die öffentliche IP-Adresse ändert sich nur, wenn das Gateway gelöscht und neu erstellt wird. Sie ändert sich nicht, wenn die Größe geändert wird, das VPN-Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das VPN-Gateway durchgeführt werden.

   Fordern Sie eine dynamisch zugewiesene öffentliche IP-Adresse an.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="creategateway"></a>3. Erstellen des VPN-Gateways

Konfigurieren und erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet.

* „-GatewayType“ muss **Vpn** und „-VpnType“ muss **RouteBased** lauten.
* Mit „-VpnClientProtocol“ werden die Tunnelarten angegeben, die Sie aktivieren möchten. Die Tunneloptionen sind **OpenVPN, SSTP** und **IKEv2**. Sie können eine dieser beiden Optionen oder jede unterstützte Kombination aktivieren. Wenn Sie mehrere Typen aktivieren möchten, geben Sie die Namen mit Kommas als Trennzeichen an. OpenVPN und SSTP können nicht zusammen aktiviert werden. Der strongSwan-Client unter Android und Linux und der native IKEv2-VPN-Client unter iOS und OSX verwenden nur den IKEv2-Tunnel für die Verbindungsherstellung. Windows-Clients probieren zunächst IKEv2. Wird keine Verbindung hergestellt, verwenden sie SSTP. Sie können den OpenVPN-Client verwenden, um eine Verbindung mit dem OpenVPN-Tunneltyp herzustellen.
* Die Basic-SKU des virtuellen Netzwerkgateways unterstützt weder IKEv2-, noch OpenVPN- noch RADIUS-Authentifizierung. Wenn Sie planen, Verbindungen von Mac-Clients mit Ihrem virtuellen Netzwerk zuzulassen, sollten Sie nicht die Basic-SKU verwenden.
* Je nach ausgewählter [Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md) kann die Erstellung eines VPN-Gateways bis zu 45 Minuten dauern. In diesem Beispiel wird IKEv2 verwendet.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
```

## <a name="addresspool"></a>4. Hinzufügen des VPN-Clientadresspools

Nachdem das VPN-Gateway erstellt wurde, können Sie den VPN-Clientadresspool hinzufügen. Der Adresspool des VPN-Clients ist der Bereich, aus dem die VPN-Clients bei der Verbindungsherstellung eine IP-Adresse erhalten. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem VNET überschneiden, mit dem Sie Verbindungen herstellen möchten. In diesem Beispiel wird der Adresspool des VPN-Clients in Schritt 1 als [Variable](#declare) deklariert.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="Certificates"></a>5. Generieren von Zertifikaten

Zertifikate werden von Azure zur Authentifizierung von VPN-Clients für Punkt-zu-Standort-VPNs verwendet. Sie laden die Informationen des öffentlichen Schlüssels des Stammzertifikats in Azure hoch. Der öffentliche Schlüssel wird dann als „vertrauenswürdig“ betrachtet. Clientzertifikate müssen über das vertrauenswürdige Stammzertifikat erstellt und dann auf jedem Clientcomputer installiert werden, der sich im persönlichen Zertifikatspeicher des aktuellen Benutzers befindet. Mit diesem Zertifikat wird der Client authentifiziert, wenn er eine Verbindung mit dem VNET initiiert. 

Wenn Sie selbstsignierte Zertifikate verwenden, müssen diese anhand bestimmter Parameter erstellt werden. Sie können ein selbstsigniertes Zertifikat anhand der Anweisungen für [PowerShell und Windows 10](vpn-gateway-certificates-point-to-site.md) erstellen. Falls Sie nicht Windows 10 besitzen, können Sie [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) verwenden. Beim Erstellen von selbstsignierten Stammzertifikaten und Clientzertifikaten müssen Sie unbedingt die Schritte in den Anweisungen ausführen. Andernfalls sind die erstellten Zertifikate nicht mit P2S-Verbindungen kompatibel, und Ihnen wird ein Verbindungsfehler angezeigt.

### <a name="cer"></a>1. Beschaffen der CER-Datei für ein Stammzertifikat

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]


### <a name="generate"></a>2. Generieren eines Clientzertifikats

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>6. Hochladen der Informationen des öffentlichen Schlüssels des Stammzertifikats

Vergewissern Sie sich, dass das VPN-Gateway erstellt wurde. Nach der Erstellung können Sie die CER-Datei (mit den Informationen zum öffentlichen Schlüssel) für ein vertrauenswürdiges Stammzertifikat in Azure hochladen. Nach dem Hochladen der CER-Datei kann Azure die Datei verwenden, um Clients zu authentifizieren, auf denen ein aus dem vertrauenswürdigen Stammzertifikat generiertes Clientzertifikat installiert ist. Sie können später bei Bedarf weitere vertrauenswürdige Stammzertifikate hochladen – bis zu 20 insgesamt.

Diese Informationen können Sie nicht mithilfe von Azure Cloud Shell hochladen. Sie können entweder PowerShell lokal auf Ihrem Computer verwenden oder die [Azure-Portal-Schritte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).

1. Deklarieren Sie die Variable für Ihren Zertifikatnamen, und ersetzen Sie den Wert durch Ihren Wert.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```
2. Ersetzen Sie den Dateipfad durch Ihren eigenen Pfad, und führen Sie dann die Cmdlets aus.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   $p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
   ```
3. Laden Sie die Informationen des öffentlichen Schlüssels in Azure noch. Nachdem die Zertifikatinformationen hochgeladen wurden, betrachtet Azure es als vertrauenswürdiges Stammzertifikat.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64
   ```

## <a name="clientcertificate"></a>7. Installieren eines exportierten Clientzertifikats

Wenn Sie eine P2S-Verbindung mit einem anderen Clientcomputer als dem für die Generierung der Clientzertifikate verwendeten Computer herstellen möchten, müssen Sie ein Clientzertifikat installieren. Beim Installieren eines Clientzertifikats benötigen Sie das Kennwort, das beim Exportieren des Clientzertifikats erstellt wurde.

Stellen Sie sicher, dass das Clientzertifikat zusammen mit der gesamten Zertifikatkette als PFX-Datei exportiert wurden (Standardeinstellung). Andernfalls sind die Stammzertifikatinformationen nicht auf dem Clientcomputer vorhanden, und der Client kann nicht ordnungsgemäß authentifiziert werden. 

Die Installationsschritte finden Sie unter [Installieren eines Clientzertifikats für Point-to-Site-Verbindungen mit Azure-Zertifikatauthentifizierung](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="clientconfig"></a>8. Konfigurieren des nativen VPN-Clients

Die VPN-Clientkonfigurationsdateien enthalten Einstellungen zum Konfigurieren von Geräten, die eine P2S-Verbindung mit einem VNET herstellen. Anweisungen zum Erstellen und Installieren von VPN-Clientkonfigurationsdateien finden Sie unter [Erstellen und Installieren von VPN-Clientkonfigurationsdateien für P2S-Konfigurationen mit nativer Azure-Zertifikatauthentifizierung](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect"></a>9. Herstellen einer Verbindung mit Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>So stellen Sie eine Verbindung von einem Windows-VPN-Client her

>[!NOTE]
>Auf dem Windows-Clientcomputer, von dem aus Sie die Verbindung herstellen, müssen Sie über Administratorrechte verfügen.
>
>

1. Um eine Verbindung mit Ihrem VNet herzustellen, navigieren Sie auf dem Clientcomputer zu „VPN-Verbindungen“ und suchen nach der VPN-Verbindung, die Sie erstellt haben. Sie hat den gleichen Namen wie das virtuelle Netzwerk. Klicken Sie auf **Verbinden**. Möglicherweise wird eine Popupmeldung angezeigt, die sich auf die Verwendung des Zertifikats bezieht. Klicken Sie auf **Weiter**, um erhöhte Rechte zu verwenden. 
2. Klicken Sie auf der Statusseite **Verbindung** auf **Verbinden**, um die Verbindung herzustellen. Wenn der Bildschirm **Zertifikat auswählen** angezeigt wird, vergewissern Sie sich, dass das angezeigte Clientzertifikat dem Zertifikat entspricht, die Sie zum Herstellen der Verbindung verwenden möchten. Wenn dies nicht der Fall ist, verwenden Sie den Dropdownpfeil, um das richtige Zertifikat auszuwählen, und klicken Sie dann auf **OK**.

   ![Verbindungsherstellung zwischen VPN-Client und Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. Die Verbindung wurde hergestellt.

   ![Verbindung hergestellt](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

#### <a name="troubleshooting-windows-client-p2s-connections"></a>Problembehandlung bei P2S-Verbindungen auf Windows-Clients

[!INCLUDE [client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>So stellen Sie eine Verbindung von einem Mac-VPN-Client her

Navigieren Sie im Dialogfeld „Netzwerk“ zum gewünschten Clientprofil, und klicken Sie auf **Verbinden**.
Ausführliche Anweisungen finden Sie unter [Installieren – Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac). Wenn beim Herstellen einer Verbindung Probleme auftreten, stellen Sie sicher, dass das Gateway des virtuellen Netzwerks nicht die Basic-SKU verwendet. Die Basic-SKU wird für Mac-Clients nicht unterstützt.

  ![Mac-Verbindung](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>So überprüfen Sie Ihre Verbindung

Diese Anweisungen gelten für Windows-Clients.

1. Um sicherzustellen, dass die VPN-Verbindung aktiv ist, öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie *Ipconfig/all*aus.
2. Zeigen Sie die Ergebnisse an. Beachten Sie, dass die erhaltene IP-Adresse eine der Adressen aus dem Clientadresspool des Punkt-zu-Standort-VPN ist, den Sie in Ihrer Konfiguration angegeben haben. Die Ergebnisse sehen in etwa wie in diesem Beispiel aus:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="connectVM"></a>Herstellen einer Verbindung mit einem virtuellen Computer

Diese Anweisungen gelten für Windows-Clients.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="addremovecert"></a>So fügen Sie ein Stammzertifikat hinzu oder entfernen es

Sie können vertrauenswürdige Stammzertifikate hinzufügen und aus Azure entfernen. Wenn Sie ein Stammzertifikat entfernen, können Clients, für die über dieses Stammzertikat ein Zertifikat generiert wurde, nicht authentifiziert werden und somit auch keine Verbindung herstellen. Wenn Sie für einen Client die Authentifizierung und Verbindungsherstellung durchführen möchten, müssen Sie ein neues Clientzertifikat installieren, das aus einem für Azure vertrauenswürdigen (hochgeladenen) Stammzertifikat generiert wurde.

### <a name="addtrustedroot"></a>So fügen Sie ein vertrauenswürdiges Stammzertifikat hinzu

Sie können Azure bis zu 20 CER-Stammzertifikatdateien hinzufügen. Gehen Sie zum Hinzufügen eines Stammzertifikats wie folgt vor:

#### <a name="certmethod1"></a>Methode 1


Dies ist die effizienteste Methode zum Hochladen eines Stammzertifikats. Sie erfordert die lokale Installation von Azure PowerShell-Cmdlets auf Ihrem Computer (nicht Azure Cloud Shell).

1. Bereiten Sie die hochzuladende CER-Datei vor:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   $p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64_3
   ```
2. Laden Sie die Datei hoch. Sie können jeweils nur eine Datei hochladen.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64_3
   ```

3. So überprüfen Sie, ob die Zertifikatdatei hochgeladen wurde:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

#### <a name="certmethod2"></a>Methode 2 – Azure-Portal

Diese Methode umfasst zwar mehr Schritte als Methode 1, das Ergebnis ist jedoch gleich. Sie wurde für den Fall aufgenommen, dass Sie die Zertifikatdaten anzeigen müssen. Sie erfordert die lokale Installation von Azure PowerShell-Cmdlets auf Ihrem Computer (nicht Azure Cloud Shell).

1. Erstellen Sie das neue Stammzertifikat, das Sie Azure hinzufügen möchten, und bereiten Sie es vor. Exportieren Sie den öffentlichen Schlüssel als Base64-codierte X.509-Datei (.CER), und öffnen Sie diese mit einem Text-Editor. Kopieren Sie die Werte wie im folgenden Beispiel gezeigt:

   ![Zertifikat](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

   > [!NOTE]
   > Stellen Sie beim Kopieren der Zertifikatsdaten sicher, dass Sie den Text als fortlaufende Zeile ohne Wagenrückläufe oder Zeilenvorschübe kopieren. Es kann erforderlich sein, Ihre Ansicht im Text-Editor so zu ändern, dass die Symbole bzw. alle Zeichen angezeigt werden, damit die Wagenrückläufe bzw. Zeilenvorschübe sichtbar sind.
   >
   >

2. Geben Sie den Zertifikatnamen und die Schlüsselinformationen als Variable an. Ersetzen Sie die Informationen durch Ihre eigenen Angaben, wie im folgenden Beispiel gezeigt:

   ```azurepowershell
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```
3. Fügen Sie das neue Stammzertifikat hinzu. Sie können immer nur jeweils ein Zertifikat hinzufügen.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
   ```
4. Mit dem folgenden Beispiel können Sie überprüfen, ob das neue Zertifikat ordnungsgemäß hinzugefügt wurde:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

### <a name="removerootcert"></a>So entfernen Sie ein Stammzertifikat

1. Deklarieren Sie die Variablen.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```
2. Entfernen Sie das Zertifikat.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```
3. Überprüfen Sie mit dem folgenden Beispiel, ob das Zertifikat erfolgreich entfernt wurde.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="revoke"></a>So sperren Sie ein Clientzertifikat

Sie können Clientzertifikate sperren. Anhand der Zertifikatsperrliste können Sie basierend auf einzelnen Clientzertifikaten selektiv Punkt-zu-Standort-Verbindungen verweigern. Das ist nicht dasselbe wie das Entfernen eines vertrauenswürdigen Stammzertifikats. Wenn Sie ein vertrauenswürdiges Stammzertifikat (CER-Datei) aus Azure entfernen, wird der Zugriff für alle Clientzertifikate gesperrt, die mit dem gesperrten Stammzertifikat generiert oder signiert wurden. Wenn Sie anstelle des Stammzertifikats ein Clientzertifikat sperren, können die anderen Zertifikate, die auf der Grundlage des Stammzertifikats generiert wurden, weiterhin für die Authentifizierung verwendet werden.

Üblicherweise wird das Stammzertifikat zum Verwalten des Zugriffs auf Team- oder Organisationsebene verwendet. Eine genauer abgestufte Steuerung des Zugriffs für einzelne Benutzer erfolgt hingegen mit gesperrten Clientzertifikaten.

### <a name="revokeclientcert"></a>Sperren eines Clientzertifikats

1. Rufen Sie den Fingerabdruck des Clientzertifikats ab. Weitere Informationen finden Sie unter [Vorgehensweise: Abrufen des Fingerabdrucks eines Zertifikats](https://msdn.microsoft.com/library/ms734695.aspx).
2. Kopieren Sie ihn in einen Text-Editor, und entfernen Sie alle Leerzeichen, sodass eine fortlaufende Zeichenfolge entsteht. Diese Zeichenfolge wird im nächsten Schritt als Variable deklariert.
3. Deklarieren Sie die Variablen. Deklarieren Sie den Fingerabdruck, den Sie im vorherigen Schritt abgerufen haben.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```
4. Fügen Sie den Fingerabdruck der Liste mit den gesperrten Zertifikaten hinzu. Nach dem Hinzufügen des Fingerabdrucks wird „Erfolgreich“ angezeigt.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```
5. Überprüfen Sie, ob der Fingerabdruck zur Zertifikatsperrliste hinzugefügt wurde.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```
6. Nachdem der Fingerabdruck hinzugefügt wurde, kann das Zertifikat nicht mehr zum Herstellen einer Verbindung verwendet werden. Clients, die versuchen, unter Verwendung dieses Zertifikats eine Verbindung herzustellen, erhalten eine Meldung mit dem Hinweis, dass das Zertifikat nicht mehr gültig ist.

### <a name="reinstateclientcert"></a>So reaktivieren Sie ein Clientzertifikat

Sie können ein Clientzertifikat reaktivieren, indem Sie den Fingerabdruck aus der Liste der gesperrten Clientzertifikate entfernen.

1. Deklarieren Sie die Variablen. Achten Sie darauf, dass Sie den richtigen Fingerabdruck für das Zertifikat deklarieren, das Sie reaktivieren möchten.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```
2. Entfernen Sie den Zertifikatfingerabdruck aus der Sperrliste.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```
3. Überprüfen Sie, ob der Fingerabdruck aus der Sperrliste entfernt wurde.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="faq"></a>Point-to-Site – Häufig gestellte Fragen

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](https://docs.microsoft.com/azure/) . Weitere Informationen zu Netzwerken und virtuellen Computern finden Sie unter [Azure- und Linux-VM-Netzwerke (Übersicht)](../virtual-machines/linux/azure-vm-network-overview.md).

Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Probleme mit Azure Point-to-Site-Verbindungen](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
