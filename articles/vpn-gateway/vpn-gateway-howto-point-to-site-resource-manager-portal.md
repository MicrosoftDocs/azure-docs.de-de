---
title: 'Herstellen einer Verbindung mit einem VNet unter Verwendung eines Point-to-Site-VPN und der Zertifikatauthentifizierung: Portal'
titleSuffix: Azure VPN Gateway
description: Verbinden Sie Windows-, macOS- und Linux-Clients auf sichere Weise mit einem virtuellen Azure-Netzwerk, indem Sie eine P2S-Verbindung und selbstsignierte oder von einer Zertifizierungsstelle ausgestellte Zertifikate verwenden. In diesem Artikel wird das Azure-Portal verwendet.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/09/2020
ms.author: cherylmc
ms.openlocfilehash: 0b2fa06bc04bdb584367312b1e89939ed386b4f2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952847"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Konfigurieren einer Point-to-Site-VPN-Verbindung mit einem VNET unter Verwendung der nativen Azure-Zertifikatauthentifizierung: Azure-Portal

Dieser Artikel enthält Informationen zum sicheren Verbinden von einzelnen Clients, auf denen Windows, Linux oder macOS ausgeführt wird, mit einem Azure-VNET. Eine P2S-VPN-Verbindung ist nützlich, wenn Sie an einem Remotestandort (beispielsweise bei der Telearbeit zu Hause oder in einer Konferenz) eine Verbindung mit Ihrem VNET herstellen möchten. Sie können anstelle einer Site-to-Site-VPN-Verbindung auch P2S verwenden, wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen. Point-to-Site-Verbindungen erfordern weder ein VPN-Gerät noch eine öffentliche IP-Adresse. P2S erstellt die VPN-Verbindung entweder über SSTP (Secure Socket Tunneling Protocol) oder IKEv2. Weitere Informationen zu Point-to-Site-VPN-Verbindungen finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md).

:::image type="content" source="./media\vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Herstellen einer Verbindung zwischen einem Computer und einem Azure VNET – Diagramm der Point-to-Site-Verbindung":::

Weitere Informationen zu Point-to-Site-VPN finden Sie unter [Informationen zu Point-to-Site-VPN](point-to-site-about.md). Informationen zum Erstellen dieser Konfiguration mithilfe von Azure PowerShell finden Sie unter [Konfigurieren eines Point-to-Site-VPN mit Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

### <a name="example-values"></a><a name="example"></a>Beispielwerte

Sie können die folgenden Werte zum Erstellen einer Testumgebung oder zum besseren Verständnis der Beispiele in diesem Artikel nutzen:

* **VNet-Name:** VNet1
* **Adressraum:** 10.1.0.0/16<br>In diesem Beispiel verwenden wir nur einen einzelnen Adressraum. Sie können für Ihr VNet aber auch mehrere Adressräume verwenden.
* **Subnetzname:** FrontEnd
* **Subnetzadressbereich:** 10.1.0.0/24
* **Abonnement:** Falls Sie über mehrere Abonnements verfügen, vergewissern Sie sich, dass Sie das richtige Abonnement verwenden.
* **Ressourcengruppe:** TestRG1
* **Standort:** East US
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Name des Gateways des virtuellen Netzwerks:** VNet1GW
* **Gatewaytyp**: VPN
* **VPN-Typ:** routenbasiert
* **Öffentliche IP-Adresse:** VNet1GWpip
* **Verbindungstyp:** Punkt-zu-Standort
* **Clientadresspool:** 172.16.201.0/24<br>VPN-Clients, die über diese Point-to-Site-Verbindung eine Verbindung mit dem VNet herstellen, erhalten eine IP-Adresse aus dem Clientadresspool.

## <a name="1-create-a-virtual-network"></a><a name="createvnet"></a>1. Erstellen eines virtuellen Netzwerks

Stellen Sie zunächst sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Erstellen eines Gateways für das virtuelle Netzwerk

In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern.

>[!NOTE]
>Die Basic-Gateway-SKU unterstützt keine IKEv2- oder RADIUS-Authentifizierung. Wenn Sie planen, Verbindungen von Mac-Clients mit Ihrem virtuellen Netzwerk zuzulassen, sollten Sie nicht die Basic-SKU verwenden.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="3-generate-certificates"></a><a name="generatecert"></a>3. Generieren von Zertifikaten

Zertifikate werden von Azure zum Authentifizieren von Clients verwendet, die eine Verbindung mit einem VNET über eine Point-to-Site-VPN-Verbindung herstellen. Nachdem Sie ein Stammzertifikat abgerufen haben, müssen Sie die Informationen des öffentlichen Schlüssels in Azure [hochladen](#uploadfile). Das Stammzertifikat wird dann von Azure als „vertrauenswürdig“ für die Verbindung mit dem virtuellen Netzwerk über P2S betrachtet. Sie generieren darüber hinaus Clientzertifikate aus dem vertrauenswürdigen Stammzertifikat und installieren sie dann auf den einzelnen Clientcomputern. Mit dem Clientzertifikat wird der Client authentifiziert, wenn er eine Verbindung mit dem VNET initiiert. 

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>Generieren eines Stammzertifikats

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>Generieren von Clientzertifikaten

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="4-add-the-client-address-pool"></a><a name="addresspool"></a>4. Hinzufügen des Clientadresspools

Der Clientadresspool ist ein Bereich privater IP-Adressen, die Sie angeben. Den Clients, die eine Verbindung über ein P2S-VPN herstellen, wird dynamisch eine IP-Adresse aus diesem Bereich zugewiesen. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem VNET überschneiden, mit dem Sie Verbindungen herstellen möchten. Wenn Sie mehrere Protokolle konfigurieren und SSTP eines der Protokolle ist, wird der konfigurierte Adresspool gleichmäßig auf die konfigurierten Protokolle aufgeteilt.

1. Navigieren Sie nach Erstellung des Gateways für virtuelle Netzwerke auf dem Blatt des Gateways für virtuelle Netzwerke zum Abschnitt **Einstellungen**. Klicken Sie unter **Einstellungen** auf **Punkt-zu-Standort-Konfiguration**. Wählen Sie **Jetzt konfigurieren** aus, um die Konfigurationsseite zu öffnen.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Seite „Point-to-Site-Konfiguration“" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Auf der Seite **Point-to-Site-Konfiguration** können Sie verschiedene Einstellungen konfigurieren. Wenn der Tunnel- oder Authentifizierungstyp auf dieser Seite nicht angezeigt wird, verwendet Ihr Gateway die Basic-SKU. Die Basic-SKU unterstützt keine IKEv2- oder RADIUS-Authentifizierung. Wenn Sie diese Einstellungen verwenden möchten, müssen Sie das Gateway löschen und mit einer anderen Gateway-SKU neu erstellen.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/address-pool.png" alt-text="Angeben des Adresspools" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/address-pool.png":::
1. Fügen Sie im Feld **Adresspool** den Bereich mit privaten IP-Adressen hinzu, den Sie verwenden möchten. VPN-Clients wird aus dem von Ihnen angegebenen Bereich dynamisch eine IP-Adresse zugewiesen. Der Wert der kleinstmöglichen Subnetzmaske beträgt 29 Bit für die Aktiv/Passiv-Konfiguration und 28 Bit für die Aktiv/Aktiv-Konfiguration.
1. Wechseln Sie zum nächsten Abschnitt, um den Tunneltyp zu konfigurieren.

## <a name="5-configure-tunnel-type"></a><a name="tunneltype"></a>5. Konfigurieren des Tunneltyps

Wählen Sie den Tunneltyp aus. Die Tunneloptionen sind OpenVPN, SSTP und IKEv2.

* Der strongSwan-Client unter Android und Linux und der native IKEv2-VPN-Client unter iOS und OSX verwenden nur den IKEv2-Tunnel für die Verbindungsherstellung.
* Windows-Clients probieren es zunächst mit IKEv2. Wenn keine Verbindung hergestellt wird, verwenden sie SSTP.
* Sie können den OpenVPN-Client verwenden, um eine Verbindung mit dem OpenVPN-Tunneltyp herzustellen.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel-ike.png" alt-text="Tunneltyp":::

## <a name="6-configure-authentication-type"></a><a name="authenticationtype"></a>6. Konfigurieren des Authentifizierungstyps

Wählen Sie unter **Authentifizierungstyp** die Option **Azure-Zertifikat** aus.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/azure-certificate.png" alt-text="Authentifizierungstyp":::

## <a name="7-upload-the-root-certificate-public-certificate-data"></a><a name="uploadfile"></a>7. Hochladen der Daten des öffentlichen Zertifikats für das Stammzertifikat

Sie können weitere vertrauenswürdige Stammzertifikate hochladen (bis zu 20 insgesamt). Nach dem Hochladen der Daten des öffentlichen Zertifikats kann Azure sie verwenden, um Clients zu authentifizieren, auf denen ein aus dem vertrauenswürdigen Stammzertifikat generiertes Clientzertifikat installiert ist. Laden Sie die Informationen des öffentlichen Schlüssels für das Stammzertifikat in Azure hoch.

1. Die Zertifikate werden auf der Seite **Point-to-Site-Konfiguration** im Abschnitt **Stammzertifikat** hinzugefügt.
1. Vergewissern Sie sich, dass Sie das Stammzertifikat als X.509-CER-Datei mit Base-64-Codierung exportiert haben. Das Zertifikat muss in diesem Format exportiert werden, damit Sie es mit einem Text-Editor öffnen können.
1. Öffnen Sie das Zertifikat mit einem Text-Editor (beispielsweise mit dem Windows-Editor). Stellen Sie beim Kopieren der Zertifikatsdaten sicher, dass Sie den Text als fortlaufende Zeile ohne Wagenrückläufe oder Zeilenvorschübe kopieren. Es kann erforderlich sein, Ihre Ansicht im Text-Editor so zu ändern, dass die Symbole bzw. alle Zeichen angezeigt werden, damit die Wagenrückläufe bzw. Zeilenvorschübe sichtbar sind. Kopieren Sie nur den folgenden Abschnitt als fortlaufende Zeile:

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="Zertifikatdaten" border="false":::
1. Fügen Sie die Zertifikatdaten im Feld **Daten des öffentlichen Zertifikats** ein. Geben Sie dem Zertifikat einen **Namen**, und wählen Sie dann **Speichern** aus. Sie können bis zu 20 vertrauenswürdige Stammzertifikate hinzufügen.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png" alt-text="Einfügen von Zertifikatdaten" border="false":::
1. Wählen Sie oben auf der Seite die Option **Speichern** aus, um alle Konfigurationseinstellungen zu speichern.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png" alt-text="Speichern der Konfiguration" border="false":::

## <a name="8-install-an-exported-client-certificate"></a><a name="installclientcert"></a>8. Installieren eines exportierten Clientzertifikats

Wenn Sie eine P2S-Verbindung mit einem anderen Clientcomputer als dem für die Generierung der Clientzertifikate verwendeten Computer herstellen möchten, müssen Sie ein Clientzertifikat installieren. Beim Installieren eines Clientzertifikats benötigen Sie das Kennwort, das beim Exportieren des Clientzertifikats erstellt wurde.

Stellen Sie sicher, dass das Clientzertifikat zusammen mit der gesamten Zertifikatkette als PFX-Datei exportiert wurden (Standardeinstellung). Andernfalls sind die Stammzertifikatinformationen nicht auf dem Clientcomputer vorhanden, und der Client kann nicht ordnungsgemäß authentifiziert werden.

Die Installationsschritte finden Sie unter [Installieren eines Clientzertifikats für Point-to-Site-Verbindungen mit Azure-Zertifikatauthentifizierung](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="9-generate-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>9. Generieren und Installieren des Konfigurationspakets für VPN-Clients

Die VPN-Clientkonfigurationsdateien enthalten Einstellungen zum Konfigurieren von Geräten, die eine P2S-Verbindung mit einem VNET herstellen. Anweisungen zum Erstellen und Installieren von VPN-Clientkonfigurationsdateien finden Sie unter [Erstellen und Installieren von VPN-Clientkonfigurationsdateien für P2S-Konfigurationen mit nativer Azure-Zertifikatauthentifizierung](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Herstellen einer Verbindung mit Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>So stellen Sie eine Verbindung von einem Windows-VPN-Client her

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>So stellen Sie eine Verbindung von einem Mac-VPN-Client her

Suchen Sie im Dialogfeld „Netzwerk“ nach dem gewünschten Clientprofil, geben Sie die Einstellungen aus der Datei [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac) an, und wählen Sie dann **Verbinden** aus.

Ausführliche Anweisungen finden Sie unter [Installieren – Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac). Wenn beim Herstellen einer Verbindung Probleme auftreten, stellen Sie sicher, dass das Gateway des virtuellen Netzwerks nicht die Basic-SKU verwendet. Die Basic-SKU wird für Mac-Clients nicht unterstützt.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png" alt-text="VPN-Clientverbindung unter Mac" border="false":::

## <a name="to-verify-your-connection"></a><a name="verify"></a>So überprüfen Sie Ihre Verbindung

Diese Anweisungen gelten für Windows-Clients.

1. Um sicherzustellen, dass die VPN-Verbindung aktiv ist, öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie *Ipconfig/all* aus.
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

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Herstellen einer Verbindung mit einem virtuellen Computer

Diese Anweisungen gelten für Windows-Clients.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Stellen Sie sicher, dass das VPN-Clientkonfigurationspaket generiert wurde, nachdem die IP-Adressen des DNS-Server für das VNET angegeben wurden. Wenn Sie die IP-Adressen des DNS-Servers aktualisiert haben, generieren Sie ein neues VPN-Clientkonfigurationspaket und installieren es.

* Überprüfen Sie mit „ipconfig“ die IPv4-Adresse, die dem Ethernet-Adapter auf dem Computer zugewiesen ist, von dem aus Sie die Verbindung herstellen. Wenn sich die IP-Adresse im Adressbereich des VNETs befindet, mit dem Sie die Verbindung herstellen, oder im Adressbereich von „VPNClientAddressPool“ liegt, wird dies als sich überschneidender Adressraum bezeichnet. Falls sich Ihr Adressraum auf diese Weise überschneidet, kommt der Netzwerkdatenverkehr nicht bei Azure an, sondern verbleibt im lokalen Netzwerk.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>So können Sie vertrauenswürdige Stammzertifikate hinzufügen oder entfernen

Sie können vertrauenswürdige Stammzertifikate hinzufügen und aus Azure entfernen. Wenn Sie ein Stammzertifikat entfernen, können Clients, für die über diesen Stamm ein Zertifikat generiert wurde, nicht authentifiziert werden und somit auch keine Verbindung herstellen. Wenn Sie für einen Client die Authentifizierung und Verbindungsherstellung durchführen möchten, müssen Sie ein neues Clientzertifikat installieren, das aus einem für Azure vertrauenswürdigen (hochgeladenen) Stammzertifikat generiert wurde.

### <a name="to-add-a-trusted-root-certificate"></a>So fügen Sie ein vertrauenswürdiges Stammzertifikat hinzu

Sie können Azure bis zu 20 vertrauenswürdige CER-Stammzertifikatdateien hinzufügen. Eine entsprechende Anleitung finden Sie in diesem Artikel im Abschnitt zum [Hochladen eines vertrauenswürdigen Stammzertifikats](#uploadfile).

### <a name="to-remove-a-trusted-root-certificate"></a>Entfernen eines vertrauenswürdigen Stammzertifikats

1. Navigieren Sie zum Entfernen eines vertrauenswürdigen Zertifikats zur Seite **Punkt-zu-Standort-Konfiguration** für Ihr Gateway für virtuelle Netzwerke.
1. Suchen Sie auf der Seite im Abschnitt **Stammzertifikat** nach dem Zertifikat, das Sie entfernen möchten.
1. Wählen Sie neben dem Zertifikat die Auslassungspunkte und anschließend „Entfernen“ aus.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>So sperren Sie ein Clientzertifikat

Sie können Clientzertifikate sperren. Anhand der Zertifikatsperrliste können Sie basierend auf einzelnen Clientzertifikaten selektiv Punkt-zu-Standort-Verbindungen verweigern. Das ist nicht dasselbe wie das Entfernen eines vertrauenswürdigen Stammzertifikats. Wenn Sie ein vertrauenswürdiges Stammzertifikat (CER-Datei) aus Azure entfernen, wird der Zugriff für alle Clientzertifikate gesperrt, die mit dem gesperrten Stammzertifikat generiert oder signiert wurden. Wenn Sie anstelle des Stammzertifikats ein Clientzertifikat sperren, können die anderen Zertifikate, die auf der Grundlage des Stammzertifikats generiert wurden, weiterhin für die Authentifizierung verwendet werden.

Üblicherweise wird das Stammzertifikat zum Verwalten des Zugriffs auf Team- oder Organisationsebene verwendet. Eine genauer abgestufte Steuerung des Zugriffs für einzelne Benutzer erfolgt hingegen mit gesperrten Clientzertifikaten.

### <a name="revoke-a-client-certificate"></a>Sperren eines Clientzertifikats

Sie können ein Clientzertifikat sperren, indem Sie den Fingerabdruck der Sperrliste hinzufügen.

1. Rufen Sie den Fingerabdruck des Clientzertifikats ab. Weitere Informationen finden Sie unter [Vorgehensweise: Abrufen des Fingerabdrucks eines Zertifikats](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Kopieren Sie ihn in einen Text-Editor, und entfernen Sie alle Leerzeichen, sodass eine fortlaufende Zeichenfolge entsteht.
1. Navigieren Sie zur Seite **Punkt-zu-Standort-Konfiguration** des Gateways für virtuelle Netzwerke. Dies ist die Seite, die Sie zum [Hochladen eines vertrauenswürdigen Stammzertifikats](#uploadfile) verwendet haben.
1. Geben Sie im Abschnitt **Gesperrte Zertifikate** einen Anzeigenamen für das Zertifikat ein. (Dabei muss es sich nicht um den allgemeinen Namen des Zertifikats handeln.)
1. Kopieren Sie die Fingerabdruck-Zeichenfolge, und fügen Sie sie in das Feld **Fingerabdruck** ein.
1. Der Fingerabdruck wird überprüft und automatisch der Sperrliste hinzugefügt. Auf dem Bildschirm wird eine Meldung mit dem Hinweis angezeigt, dass die Liste aktualisiert wird. 
1. Nach Abschluss der Aktualisierung kann das Zertifikat nicht mehr für die Verbindungsherstellung verwendet werden. Clients, die versuchen, unter Verwendung dieses Zertifikats eine Verbindung herzustellen, erhalten eine Meldung mit dem Hinweis, dass das Zertifikat nicht mehr gültig ist.

## <a name="point-to-site-faq"></a><a name="faq"></a>Point-to-Site – Häufig gestellte Fragen

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](../index.yml) . Weitere Informationen zu Netzwerken und virtuellen Computern finden Sie unter [Azure- und Linux-VM-Netzwerke (Übersicht)](../virtual-machines/network-overview.md).

Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Probleme mit Azure P2S-Verbindungen (Point-to-Site)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
