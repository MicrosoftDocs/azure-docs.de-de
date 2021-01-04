---
title: 'Herstellen einer P2S-Verbindung zwischen einem Computer und einem virtuellen Netzwerk unter Verwendung der Zertifikatauthentifizierung: Azure-Portal – klassisch'
titleSuffix: Azure VPN Gateway
description: Erstellen Sie eine klassische P2S-VPN-Gatewayverbindung (Point-to-Site) mit dem Azure-Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657073"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Konfigurieren einer Point-to-Site-Verbindung unter Verwendung der Zertifikatauthentifizierung (klassisch)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

In diesem Artikel wird erläutert, wie Sie ein VNET mit einer Point-to-Site-Verbindung erstellen. Sie erstellen dieses VNET mit dem klassischen Bereitstellungsmodell im Azure-Portal. In dieser Konfiguration werden Zertifikate verwendet, um den Client, von dem die Verbindung hergestellt wird, zu authentifizieren (entweder selbstsigniert oder von der Zertifizierungsstelle ausgegeben). Sie können diese Konfiguration auch mit einem anderen Bereitstellungstool oder Modell erstellen, indem Sie Optionen verwenden, die in den folgenden Artikeln beschrieben werden:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure-Portal (klassisch)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Sie verwenden ein P2S-VPN-Gateway (Point-to-Site), um von einem einzelnen Clientcomputer aus eine sichere Verbindung mit Ihrem virtuellen Netzwerk herzustellen. Eine P2S-VPN-Verbindung ist nützlich, wenn Sie an einem Remotestandort eine Verbindung mit Ihrem VNET herstellen möchten. Wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen, ist ein P2S-VPN (und nicht ein S2S-VPN) ebenfalls eine nützliche Lösung. Eine P2S-Verbindung wird hergestellt, indem Sie die Verbindung vom Clientcomputer aus starten.

> [!IMPORTANT]
> Das klassische Bereitstellungsmodell unterstützt nur Windows-VPN-Clients und verwendet das Secure Socket Tunneling-Protokolls (SSTP), ein SSL-basiertes VPN-Protokoll. Zur Unterstützung anderer VPN-Clients müssen Sie Ihr VNET mit dem Resource Manager-Bereitstellungsmodell erstellen. Das Resource Manager-Bereitstellungsmodell unterstützt neben SSTP auch IKEv2-VPN. Weitere Informationen finden Sie unter [Informationen zu P2S-Verbindungen](point-to-site-about.md).
>
>

![P2S-Diagramm](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>Einstellungen und Anforderungen

### <a name="requirements"></a>Anforderungen

Für Point-to-Site-Verbindungen mit Zertifikatauthentifizierung ist Folgendes erforderlich. In diesem Artikel werden die Schritte beschrieben, mit denen Sie diese Elemente erstellen können.

* Ein dynamisches VPN-Gateway.
* Der öffentliche Schlüssel (CER-Datei) für ein Stammzertifikat (in Azure hochgeladen). Dieser Schlüssel wird als vertrauenswürdiges Zertifikat betrachtet und für die Authentifizierung verwendet.
* Ein Clientzertifikat, das über das Stammzertifikat generiert und auf jedem Clientcomputer installiert wurde, der eine Verbindung herstellen wird. Dieses Zertifikat wird für die Clientauthentifizierung verwendet.
* Ein VPN-Clientkonfigurationspaket muss generiert und auf jedem Clientcomputer installiert werden, der eine Verbindung herstellen wird. Das Clientkonfigurationspaket konfiguriert den nativen VPN-Client, der sich bereits im Betriebssystem befindet, mit den notwendigen Informationen für die Herstellung einer Verbindung mit dem VNET.

Point-to-Site-Verbindungen erfordern weder ein VPN-Gerät noch eine lokale öffentliche IP-Adresse. Die VPN-Verbindung wird über das Secure Socket Tunneling-Protokoll (SSTP) hergestellt. Auf Serverseite werden die SSTP-Versionen 1.0, 1.1 und 1.2 unterstützt. Der Client entscheidet, welche Version verwendet wird. Unter Windows 8.1 und höher wird standardmäßig SSTP 1.2 verwendet.

Weitere Informationen finden Sie unter [Informationen zu Point-to-Site-Verbindungen](point-to-site-about.md) und [Häufig gestellte Fragen](#faq).

### <a name="example-settings"></a>Beispieleinstellungen

Nutzen Sie die folgenden Werte zum Erstellen einer Testumgebung oder zum besseren Verständnis der Beispiele in diesem Artikel:

* **Ressourcengruppe:** TestRG
* **VNet-Name:** VNet1
* **Adressraum:** 192.168.0.0/16 <br>In diesem Beispiel verwenden wir nur einen einzelnen Adressraum. Sie können für Ihr VNet aber auch mehrere Adressräume verwenden.
* **Subnetzname:** FrontEnd
* **Subnetzadressbereich:** 192.168.1.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Region:** (USA) USA, Osten
* **Clientadressraum**: 172.16.201.0/24 <br> VPN-Clients, die über diese Point-to-Site-Verbindung eine Verbindung mit dem VNET herstellen, erhalten eine IP-Adresse aus dem angegebenen Pool.
* **Verbindungstyp**: Wählen Sie **Point-to-Site** aus.
* **Adressbereich des Gatewaysubnetzes (CIDR-Block):** 192.168.200.0/24

Stellen Sie zunächst sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Erstellen eines virtuellen Netzwerks

Wenn Sie bereits über ein VNet verfügen, sollten Sie überprüfen, ob die Einstellungen mit Ihrem Entwurf des VPN Gateways kompatibel sind. Achten Sie besonders auf Subnetze, die sich unter Umständen mit anderen Netzwerken überlappen.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>Erstellen eines VPN-Gateways

1. Navigieren Sie zu dem VNET, das Sie erstellt haben.
1. Wählen Sie auf der VNET-Seite unter „Einstellungen“ die Option **Gateway** aus. Auf der Seite **Gateway** wird das Gateway für das virtuelle Netzwerk angezeigt. Dieses virtuelle Netzwerk verfügt noch nicht über ein Gateway. Klicken Sie auf den Hinweis mit folgender Angabe: **Klicken Sie hier, um eine Verbindung und ein Gateway hinzuzufügen**.
1. Wählen Sie auf der Seite **VPN-Verbindung und Gateway konfigurieren** die folgenden Einstellungen aus:

   * Verbindungstyp: Point-to-Site
   * Clientadressraum: Fügen Sie den IP-Adressbereich hinzu, aus dem die VPN-Clients bei der Verbindungsherstellung eine IP-Adresse erhalten. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem VNET überschneiden, mit dem Sie Verbindungen herstellen möchten.
1. Lassen Sie das Kontrollkästchen **Jetzt kein Gateway konfigurieren** deaktiviert. Sie erstellen später ein Gateway.
1. Wählen Sie am unteren Rand der Seite die Option **Nächster Schritt: Gateway >** .
1. Wählen Sie auf der Registerkarte **Gateway** die folgenden Werte aus:

   * **Size:** Bei der Größe handelt es sich um die Gateway-SKU für Ihr virtuelles Netzwerkgateway. Im Azure-Portal ist standardmäßig die SKU **Standard** ausgewählt. Weitere Informationen zu Gateway-SKUs finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Routingtyp:** Für eine Point-to-Site-Verbindung müssen Sie **Dynamisch** auswählen. Statisches Routing funktioniert nicht.
   * **Gatewaysubnetz:** Dieses Feld wurde bereits automatisch ausgefüllt. Sie können den Namen nicht ändern. Wenn Sie versuchen, den Namen mithilfe von PowerShell oder auf andere Weise zu ändern, funktioniert das Gateway nicht ordnungsgemäß.
   * **Adressbereich (CIDR-Block):** Es ist zwar möglich, ein Gatewaysubnetz von /29 zu erstellen, es wird jedoch empfohlen, ein größeres Subnetz mit mehr Adressen zu erstellen und mindestens /28 oder /27 auszuwählen. Damit steht eine ausreichend hohe Anzahl von Adressen für mögliche zusätzliche Konfigurationen zur Verfügung, die Sie zukünftig vielleicht benötigen. Vermeiden Sie bei der Verwendung von Gatewaysubnetzen die Zuordnung einer Netzwerksicherheitsgruppe (NSG) zum Gatewaysubnetz. Das Zuordnen einer Netzwerksicherheitsgruppe zu diesem Subnetz kann dazu führen, dass das VPN-Gateway nicht mehr wie erwartet funktioniert.
1. Wählen Sie zum Überprüfen Ihrer Einstellungen **Überprüfen + erstellen** aus.
1. Wenn die Validierung erfolgreich war, wählen Sie **Erstellen** aus. Je nach ausgewählter Gateway-SKU kann die Erstellung eines VPN-Gateways bis zu 45 Minuten dauern.

## <a name="create-certificates"></a><a name="generatecerts"></a>Erstellen von Zertifikaten

Azure verwendet Zertifikate zur Authentifizierung von VPN-Clients für P2S-VPNs. Sie laden die Informationen des öffentlichen Schlüssels des Stammzertifikats in Azure hoch. Der öffentliche Schlüssel wird dann als *vertrauenswürdig* betrachtet. Clientzertifikate müssen über das vertrauenswürdige Stammzertifikat erstellt und dann auf jedem Clientcomputer installiert werden, der sich im Zertifikatspeicher „Certificates-Current User\Personal\Certificates“ befindet. Mit diesem Zertifikat wird der Client authentifiziert, wenn er eine Verbindung mit dem VNET herstellt. 

Wenn Sie selbstsignierte Zertifikate verwenden, müssen diese anhand bestimmter Parameter erstellt werden. Sie können ein selbstsigniertes Zertifikat anhand der Anweisungen für [PowerShell und Windows 10](vpn-gateway-certificates-point-to-site.md) oder [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) erstellen. Führen Sie unbedingt die Schritte in diesen Anweisungen aus, wenn Sie selbstsignierte Stammzertifikate verwenden und Clientzertifikate aus dem selbstsignierten Stammzertifikat erstellen. Andernfalls sind die von Ihnen erstellten Zertifikate nicht mit P2S-Verbindungen kompatibel, und Ihnen wird ein Verbindungsfehler angezeigt.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Beschaffen des öffentlichen Schlüssels (CER-Format) für das Stammzertifikat

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Generieren eines Clientzertifikats

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Hochladen der CER-Datei des Stammzertifikats

Nachdem das Gateway erstellt wurde, laden Sie die CER-Datei (mit den Informationen zum öffentlichen Schlüssel) für ein vertrauenswürdiges Stammzertifikat auf den Azure-Server hoch. Laden Sie nicht den privaten Schlüssel für das Stammzertifikat hoch. Nach dem Hochladen des Zertifikats verwendet Azure es, um Clients zu authentifizieren, auf denen ein aus dem vertrauenswürdigen Stammzertifikat generiertes Clientzertifikat installiert ist. Sie können später bei Bedarf weitere (insgesamt bis zu 20) vertrauenswürdige Stammzertifikate hochladen.

1. Navigieren Sie zu dem virtuellen Netzwerk, das Sie erstellt haben.
1. Wählen Sie unter **Einstellungen** die Option **Point-to-Site-Verbindungen** aus.
1. Wählen Sie **Zertifikat verwalten** aus.
1. Wählen Sie die Option **Hochladen**.
1. Wählen Sie im Bereich **Zertifikat hochladen** das Ordnersymbol aus, und navigieren Sie zu dem Zertifikat, das hochgeladen werden soll.
1. Wählen Sie die Option **Hochladen**.
1. Nachdem das Zertifikat erfolgreich hochgeladen wurde, können Sie es auf der Seite „Zertifikat verwalten“ anzeigen. Möglicherweise müssen Sie **Aktualisieren** auswählen, um das soeben hochgeladene Zertifikat anzuzeigen.

## <a name="configure-the-client"></a>Konfigurieren des Clients

Um über ein Point-to-Site-VPN eine Verbindung mit einem VNET herstellen zu können, muss auf jedem Client ein Paket zum Konfigurieren des nativen Windows VPN-Clients installiert werden. Das Konfigurationspaket konfiguriert den nativen Windows-VPN-Client mit den Einstellungen, die zum Herstellen einer Verbindung mit dem virtuellen Netzwerk erforderlich sind.

Sie können auf jedem Clientcomputer das gleiche VPN-Clientkonfigurationspaket verwenden – vorausgesetzt, es handelt sich dabei um die passende Version für die Architektur des jeweiligen Clients. Eine Liste mit den unterstützten Clientbetriebssystemen finden Sie unter [Informationen zu Point-to-Site-Verbindungen](point-to-site-about.md) und [Häufig gestellte Fragen](#faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Generieren und Installieren eines VPN-Clientkonfigurationspakets

1. Navigieren Sie zu den Einstellungen für **Point-to-Site-Verbindungen** für das VNET.
1. Wählen Sie oben auf der Seite das Downloadpaket für das Clientbetriebssystem aus, unter dem es installiert wird:

   * Wählen Sie für 64-Bit-Clients das Paket **VPN-Client (64 Bit)** aus.
   * Wählen Sie für 32-Bit-Clients das Paket **VPN-Client (32 Bit)** aus.

1. In Azure wird ein Paket mit den spezifischen für den Client erforderlichen Einstellungen generiert. Jedes Mal, wenn Sie Änderungen am VNET oder Gateway vornehmen, müssen Sie ein neues Clientkonfigurationspaket herunterladen und auf den Clientcomputern installieren.
1. Wählen Sie nach dem Generieren des Pakets die Option **Herunterladen** aus.
1. Installieren Sie das Clientkonfigurationspaket auf dem Clientcomputer. Wenn bei der Installation das SmartScreen-Popupfenster „Der Computer wurde durch Windows geschützt“ angezeigt wird, wählen Sie **Weitere Informationen** und dann **Trotzdem ausführen** aus. Sie können das Paket auch speichern und auf anderen Clientcomputern installieren.

### <a name="install-a-client-certificate"></a>Installieren eines Clientzertifikats

In dieser Übung wurde das Clientzertifikat beim Generieren automatisch auf Ihrem Computer installiert. Um eine P2S-Verbindung mit einem anderen Clientcomputer als dem für die Generierung der Clientzertifikate verwendeten Computer herzustellen, müssen Sie das generierte Clientzertifikat auf dem entsprechenden Computer installieren.

Beim Installieren eines Clientzertifikats benötigen Sie das Kennwort, das beim Exportieren des Clientzertifikats erstellt wurde. In der Regel können Sie das Zertifikat installieren, indem Sie einfach darauf doppelklicken. Weitere Informationen finden Sie unter [Installieren eines exportierten Clientzertifikats](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect-to-your-vnet"></a>Herstellen der Verbindung mit Ihrem VNet

>[!NOTE]
>Auf dem Clientcomputer, von dem aus Sie die Verbindung herstellen, müssen Sie über Administratorrechte verfügen.
>

1. Wechseln Sie auf dem Clientcomputer zu „VPN-Einstellungen“.
1. Wählen Sie das VPN aus, das Sie erstellt haben. Wenn Sie die Beispieleinstellungen verwendet haben, hat die Verbindung die Bezeichnung **Group TestRG VNet1**.
1. Wählen Sie **Verbinden** aus.
1. Wählen Sie im Feld „Microsoft Azure Virtual Network“ die Option **Verbinden** aus. Wenn eine Popupmeldung zum Zertifikat angezeigt wird, wählen Sie **Weiter** aus, um erhöhte Rechte zu verwenden, Wählen Sie anschließend **Ja** aus, um die Konfigurationsänderungen zu übernehmen.
1. Wenn die Verbindung hergestellt wurde, wird die Benachrichtigung **Verbunden** angezeigt.

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Überprüfen der VPN-Verbindung

1. Stellen Sie sicher, dass Ihre VPN-Verbindung aktiv ist. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten auf Ihrem Clientcomputer, und führen Sie **ipconfig/all** aus.
1. Zeigen Sie die Ergebnisse an. Beachten Sie, dass die IP-Adresse, die Sie erhalten, eine Adresse aus dem Adressbereich der P2S-Verbindung ist, den Sie beim Erstellen des virtuellen Netzwerks angegeben haben. Die Ergebnisse sollten in etwa wie folgt aussehen:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a>So stellen Sie eine Verbindung mit einer virtuellen Maschine her

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>So können Sie vertrauenswürdige Stammzertifikate hinzufügen oder entfernen

Sie können vertrauenswürdige Stammzertifikate hinzufügen und aus Azure entfernen. Wenn Sie ein Stammzertifikat entfernen, können Clients, für die über diesen Stamm ein Zertifikat generiert wurde, nicht mehr authentifiziert werden und keine Verbindung mehr herstellen. Damit diese Clients sich wieder authentifizieren und eine Verbindung herstellen können, müssen Sie ein neues, aus einem für Azure vertrauenswürdigen Stammzertifikat generiertes Clientzertifikat installieren.

### <a name="add-a-trusted-root-certificate"></a>Hinzufügen eines vertrauenswürdigen Stammzertifikats

Mit den gleichen Schritten, die Sie zum Hinzufügen des ersten vertrauenswürdigen Stammzertifikats ausgeführt haben, können Sie bis zu 20 vertrauenswürdige CER-Stammzertifikatsdateien in Azure hinzufügen.

### <a name="remove-a-trusted-root-certificate"></a>Entfernen eines vertrauenswürdigen Stammzertifikats

1. Wählen Sie auf der Seite für das VNET im Abschnitt **Point-to-Site-Verbindungen** die Option **Zertifikat verwalten** aus.
1. Wählen Sie neben dem Zertifikat, das Sie entfernen möchten, die Auslassungspunkte und anschließend **Löschen** aus.

## <a name="to-revoke-a-client-certificate"></a>So sperren Sie ein Clientzertifikat

Bei Bedarf können Sie ein Clientzertifikat sperren. Anhand der Zertifikatsperrliste können Sie basierend auf einzelnen Clientzertifikaten selektiv Punkt-zu-Standort-Verbindungen verweigern. Diese Methode unterscheidet sich vom Entfernen eines vertrauenswürdigen Stammzertifikats. Wenn Sie ein vertrauenswürdiges Stammzertifikat (CER-Datei) aus Azure entfernen, wird der Zugriff für alle Clientzertifikate gesperrt, die mit dem gesperrten Stammzertifikat generiert oder signiert wurden. Wenn Sie anstelle des Stammzertifikats ein Clientzertifikat sperren, können die anderen Zertifikate, die auf der Grundlage des Stammzertifikats generiert wurden, weiterhin zur Authentifizierung für die P2S-Verbindung verwendet werden.

Üblicherweise wird das Stammzertifikat zum Verwalten des Zugriffs auf Team- oder Organisationsebene verwendet. Eine genauer abgestufte Steuerung des Zugriffs für einzelne Benutzer erfolgt hingegen mit gesperrten Clientzertifikaten.

Sie können ein Clientzertifikat sperren, indem Sie den Fingerabdruck der Sperrliste hinzufügen.

1. Rufen Sie den Fingerabdruck des Clientzertifikats ab. Weitere Informationen finden Sie unter [Vorgehensweise: Abrufen des Fingerabdrucks eines Zertifikats](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Kopieren Sie die Informationen in einen Text-Editor, und entfernen Sie alle Leerzeichen, sodass eine fortlaufende Zeichenfolge entsteht.
1. Navigieren Sie zu **Point-to-Site-VPN-Verbindung**, und wählen Sie dann **Zertifikat verwalten** aus.
1. Wählen Sie **Sperrliste** zum Öffnen der Seite **Sperrliste** aus.
1. Fügen Sie in **Fingerabdruck** den Zertifikatfingerabdruck als durchgehende Textzeile (ohne Leerzeichen) ein.
1. Wählen Sie **+ Zu Liste hinzufügen** aus, um den Fingerabdruck in der Zertifikatssperrliste (CRL) hinzuzufügen.

Nach Abschluss der Aktualisierung kann das Zertifikat nicht mehr für die Verbindungsherstellung verwendet werden. Clients, die versuchen, unter Verwendung dieses Zertifikats eine Verbindung herzustellen, erhalten eine Meldung mit dem Hinweis, dass das Zertifikat nicht mehr gültig ist.

## <a name="faq"></a><a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Nächste Schritte

* Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](../index.yml) .

* Weitere Informationen zu Netzwerken und virtuellen Computern finden Sie unter [Azure- und Linux-VM-Netzwerke (Übersicht)](../virtual-machines/network-overview.md).

* Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Azure Punkt-zu-Standort-Verbindungsprobleme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).