---
title: 'Herstellen einer Point-to-Site-Verbindung zwischen einem Computer und einem virtuellen Netzwerk unter Verwendung der Zertifikatauthentifizierung: Azure Portal – klassisch | Microsoft-Dokumentation'
description: Erstellen Sie eine P2S-VPN-Gatewayverbindung mit dem klassischen Azure-Portal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 74940f3b89237233acd575aa5df441163e00d178
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845456"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Konfigurieren einer Point-to-Site-Verbindung unter Verwendung der Zertifikatauthentifizierung (klassisch)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

In diesem Artikel wird erläutert, wie Sie ein VNET mit einer Point-to-Site-Verbindung erstellen. Sie erstellen dieses VNET mit dem klassischen Bereitstellungsmodell im Azure-Portal. In dieser Konfiguration werden Zertifikate verwendet, um den Client, von dem die Verbindung hergestellt wird, zu authentifizieren (entweder selbstsigniert oder von der Zertifizierungsstelle ausgegeben). Sie können diese Konfiguration auch mit einem anderen Bereitstellungstool oder Modell erstellen, indem Sie Optionen verwenden, die in den folgenden Artikeln beschrieben werden:

> [!div class="op_single_selector"]
> * [Azure-Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure-Portal (klassisch)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Sie verwenden ein P2S-VPN-Gateway (Point-to-Site), um von einem einzelnen Clientcomputer aus eine sichere Verbindung mit Ihrem virtuellen Netzwerk herzustellen. Eine P2S-VPN-Verbindung ist nützlich, wenn Sie an einem Remotestandort eine Verbindung mit Ihrem VNET herstellen möchten. Wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen, ist ein P2S-VPN (und nicht ein S2S-VPN) ebenfalls eine nützliche Lösung. Eine P2S-Verbindung wird hergestellt, indem Sie die Verbindung vom Clientcomputer aus starten.

> [!IMPORTANT]
> Das klassische Bereitstellungsmodell unterstützt nur Windows-VPN-Clients und verwendet das Secure Socket Tunneling-Protokolls (SSTP), ein SSL-basiertes VPN-Protokoll. Zur Unterstützung anderer VPN-Clients müssen Sie Ihr VNET mit dem Resource Manager-Bereitstellungsmodell erstellen. Das Resource Manager-Bereitstellungsmodell unterstützt neben SSTP auch IKEv2-VPN. Weitere Informationen finden Sie unter [Informationen zu P2S-Verbindungen](point-to-site-about.md).
>
>

![P2S-Diagramm](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Voraussetzungen

Für Point-to-Site-Verbindungen mit Zertifikatauthentifizierung wird Folgendes benötigt:

* Ein dynamisches VPN-Gateway.
* Der öffentliche Schlüssel (CER-Datei) für ein Stammzertifikat (in Azure hochgeladen). Dieser Schlüssel wird als vertrauenswürdiges Zertifikat betrachtet und für die Authentifizierung verwendet.
* Ein Clientzertifikat, das über das Stammzertifikat generiert und auf jedem Clientcomputer installiert wurde, der eine Verbindung herstellen wird. Dieses Zertifikat wird für die Clientauthentifizierung verwendet.
* Ein VPN-Clientkonfigurationspaket muss generiert und auf jedem Clientcomputer installiert werden, der eine Verbindung herstellen wird. Das Clientkonfigurationspaket konfiguriert den nativen VPN-Client, der sich bereits im Betriebssystem befindet, mit den notwendigen Informationen für die Herstellung einer Verbindung mit dem VNET.

Point-to-Site-Verbindungen erfordern weder ein VPN-Gerät noch eine lokale öffentliche IP-Adresse. Die VPN-Verbindung wird über das Secure Socket Tunneling-Protokoll (SSTP) hergestellt. Auf Serverseite werden die SSTP-Versionen 1.0, 1.1 und 1.2 unterstützt. Der Client entscheidet, welche Version verwendet wird. Unter Windows 8.1 und höher wird standardmäßig SSTP 1.2 verwendet. 

Weitere Informationen zu Point-to-Site-Verbindungen finden Sie unter [Point-to-Site – Häufig gestellte Fragen](#point-to-site-faq).

### <a name="example-settings"></a>Beispieleinstellungen

Nutzen Sie die folgenden Werte zum Erstellen einer Testumgebung oder zum besseren Verständnis der Beispiele in diesem Artikel:

- **Einstellungen zum Erstellen virtueller Netzwerke (klassisch)**
   - **Name**: Geben Sie *VNet1* ein.
   - **Adressraum**: Geben Sie *192.168.0.0/16* ein. In diesem Beispiel verwenden wir nur einen einzelnen Adressraum. Sie können für Ihr VNet aber auch mehrere Adressräume verwenden, wie im Diagramm zu sehen.
   - **Subnetzname**: Geben Sie *FrontEnd* ein.
   - **Subnetzadressbereich**: Geben Sie *192.168.1.0/24* ein.
   - **Abonnement**: Wählen Sie aus der Liste der verfügbaren Abonnements ein Abonnement aus.
   - **Ressourcengruppe**: Geben Sie *TestRG* ein. Wählen Sie **Neu erstellen** aus, wenn die Ressourcengruppe nicht vorhanden ist.
   - **Standort**: Wählen Sie **USA, Osten** in der Liste aus.

  - **VPN-Verbindungseinstellungen**
    - **Verbindungstyp**: Wählen Sie **Point-to-Site** aus.
    - **Clientadressraum**: Geben Sie *172.16.201.0/24* ein. VPN-Clients, die über diese Point-to-Site-Verbindung eine Verbindung mit dem VNET herstellen, erhalten eine IP-Adresse aus dem angegebenen Pool.

- **Subnetzeinstellungen zur Gatewaykonfiguration**
   - **Name**: Automatisch gefüllt mit *GatewaySubnet*.
   - **Adressbereich**: Geben Sie *192.168.200.0/24* ein. 

- **Einstellungen zur Gatewaykonfiguration**:
   - **Größe**: Wählen Sie die gewünschte Gateway-SKU aus.
   - **Routingtyp**: Wählen Sie **Dynamisch** aus.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Erstellen eines virtuelles Netzwerks und eines VPN-Gateways

Stellen Sie zunächst sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

### <a name="part-1-create-a-virtual-network"></a>Teil 1: Erstellen eines virtuellen Netzwerks

Falls Sie noch nicht über ein virtuelles Netzwerk (VNET) verfügen, erstellen Sie eines. Die Screenshots dienen lediglich zur Veranschaulichung. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen Werte ersetzen. Gehen Sie wie folgt vor, um ein VNet über das Azure-Portal zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann **Ressource erstellen** aus. Die Seite **Neu** wird geöffnet. 

2. Geben Sie *virtuelles Netzwerk* in das Feld **Marketplace durchsuchen** ein, und wählen Sie in der zurückgegebenen Liste den Eintrag **Virtuelles Netzwerk** aus. Die Seite **Virtuelles Netzwerk** wird geöffnet.

3. Wählen Sie in der Liste **Bereitstellungsmodell auswählen** die Option **Klassisch** und dann **Erstellen** aus. Die Seite **Virtuelles Netzwerk erstellen** wird geöffnet.

4. Konfigurieren Sie auf der Seite **Virtuelles Netzwerk erstellen** die VNET-Einstellungen. Auf dieser Seite fügen Sie Ihren ersten Adressraum und einen Adressbereich des Subnetzes hinzu. Nachdem Sie die Erstellung des VNet abgeschlossen haben, können Sie zurückgehen und weitere Subnetze und Adressräume hinzufügen.

   ![Seite „Virtuelles Netzwerk erstellen“](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Wählen Sie in der Dropdownliste das **Abonnement** aus, das Sie verwenden möchten.

6. Wählen Sie eine vorhandene **Ressourcengruppe** aus. Erstellen Sie alternativ eine neue Ressourcengruppe, indem Sie **Neu erstellen** auswählen, und geben Sie einen Namen ein. Falls Sie eine neue Ressourcengruppe erstellen, geben Sie ihr einen Namen, der zu den geplanten Konfigurationswerten passt. Weitere Informationen zu Ressourcengruppen finden Sie unter [Azure Resource Manager – Übersicht](../azure-resource-manager/resource-group-overview.md#resource-groups).

7. Wählen Sie einen **Standort** für Ihr VNET aus. Diese Einstellung bestimmt den geografischen Standort der Ressourcen, die Sie für dieses VNet bereitstellen.

8. Wählen Sie **Erstellen** aus, um das VNET zu erstellen. Auf der Seite **Benachrichtigungen** sehen Sie die Nachricht **Bereitstellung wird durchgeführt**.

8. Sobald Ihr virtuelles Netzwerk erstellt ist, ändert sich die Nachricht auf der Seite **Benachrichtigungen** in **Bereitstellung erfolgreich**. Wählen Sie **An Dashboard anheften** aus, wenn Sie das VNET mühelos auf dem Dashboard finden möchten. 

10. Fügen Sie einen DNS-Server hinzu (optional). Nach der Erstellung des virtuellen Netzwerks können Sie für die Namensauflösung die IP-Adresse eines DNS-Servers hinzufügen. Bei der angegebenen IP-Adresse des DNS-Servers muss es sich um die Adresse eines DNS-Servers handeln, der die Namen für die Ressourcen in Ihrem VNET auflösen kann.

    Wählen Sie zum Hinzufügen eines DNS-Servers **DNS-Server** auf Ihrer VNET-Seite aus. Geben Sie dann die IP-Adresse des DNS-Servers ein, den Sie verwenden möchten, und wählen Sie **Speichern** aus.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Teil 2: Erstellen eines Gatewaysubnetzes und eines Gateways mit dynamischem Routing

In diesem Schritt erstellen Sie ein Gatewaysubnetz und ein Gateway mit dynamischem Routing. Im Azure-Portal für das klassische Bereitstellungsmodell erstellen Sie Gatewaysubnetz und Gateway über die gleichen Konfigurationsseiten. Verwenden Sie das Gatewaysubnetz ausschließlich für die Gatewaydienste. Stellen Sie niemals etwas direkt im Gatewaysubnetz bereit (beispielsweise virtuelle Computer oder andere Dienste).

1. Navigieren Sie im Azure-Portal zu dem virtuellen Netzwerk, für das Sie ein Gateway erstellen möchten.

2. Wählen Sie auf der Seite für Ihr virtuelles Netzwerk **Übersicht** und im Abschnitt **VPN-Verbindungen** **Gateway** aus.

   ![Auswählen zum Erstellen eines Gateways](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Wählen Sie auf der Seite **Neue VPN-Verbindung** die Option **Punkt-zu-Standort** aus.

   ![Point-to-Site-Verbindungstyp](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Fügen Sie für **Clientadressraum** den IP-Adressbereich hinzu, aus dem die VPN-Clients bei der Verbindungsherstellung eine IP-Adresse erhalten. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem VNET überschneiden, mit dem Sie Verbindungen herstellen möchten. Sie können den automatisch ausgefüllten Bereich mit dem privaten IP-Adressbereich überschreiben, den Sie verwenden möchten. Dieses Beispiel zeigt den automatisch ausgefüllten Bereich. 

   ![Clientadressraum](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Wählen Sie **Gateway sofort erstellen** und dann **Optionale Gatewaykonfiguration** aus, um die Seite **Gatewaykonfiguration** zu öffnen.

   ![Auswählen der optionalen Gatewaykonfiguration](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Wählen Sie auf der Seite **Gatewaykonfiguration** **Subnetz** aus, um das Gatewaysubnetz hinzuzufügen. Es ist möglich, ein Gatewaysubnetz zu erstellen, das so klein wie „/29“ ist. Sie sollten jedoch ein größeres Subnetz mit mehr Adressen erstellen und mindestens „/28“ oder „/27“ auswählen. Damit steht eine ausreichend hohe Anzahl von Adressen für mögliche zusätzliche Konfigurationen zur Verfügung, die Sie zukünftig vielleicht benötigen. Vermeiden Sie bei der Verwendung von Gatewaysubnetzen die Zuordnung einer Netzwerksicherheitsgruppe (NSG) zum Gatewaysubnetz. Das Zuordnen einer Netzwerksicherheitsgruppe zu diesem Subnetz kann dazu führen, dass das VPN-Gateway nicht mehr wie erwartet funktioniert. Wählen Sie **OK** aus, um diese Einstellung zu speichern.

   ![Hinzufügen von „GatewaySubnet“](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Wählen Sie die Gatewaygröße **aus** . Bei der Größe handelt es sich um die Gateway-SKU für Ihr virtuelles Netzwerkgateway. Im Azure-Portal ist standardmäßig die SKU **Standard** ausgewählt. Weitere Informationen zu Gateway-SKUs finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Gatewaygröße](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Wählen Sie den **Routingtyp** für Ihr Gateway aus. Für P2S-Konfigurationen wird der Routingtyp **Dynamisch** benötigt. Wählen Sie abschließend **OK** aus.

   ![Konfigurieren des Routingtyps](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Wählen Sie am unteren Rand der Seite **Neue VPN-Verbindung** **OK** aus, um mit der Erstellung Ihres Gateways für virtuelle Netzwerke zu beginnen. Je nach ausgewählter Gateway-SKU kann die Erstellung eines VPN-Gateways bis zu 45 Minuten dauern.
 
## <a name="generatecerts"></a>Erstellen von Zertifikaten

Azure verwendet Zertifikate zur Authentifizierung von VPN-Clients für P2S-VPNs. Sie laden die Informationen des öffentlichen Schlüssels des Stammzertifikats in Azure hoch. Der öffentliche Schlüssel wird dann als *vertrauenswürdig* betrachtet. Clientzertifikate müssen über das vertrauenswürdige Stammzertifikat erstellt und dann auf jedem Clientcomputer installiert werden, der sich im Zertifikatspeicher „Certificates-Current User\Personal\Certificates“ befindet. Mit diesem Zertifikat wird der Client authentifiziert, wenn er eine Verbindung mit dem VNET herstellt. 

Wenn Sie selbstsignierte Zertifikate verwenden, müssen diese anhand bestimmter Parameter erstellt werden. Sie können ein selbstsigniertes Zertifikat anhand der Anweisungen für [PowerShell und Windows 10](vpn-gateway-certificates-point-to-site.md) oder [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) erstellen. Führen Sie unbedingt die Schritte in diesen Anweisungen aus, wenn Sie selbstsignierte Stammzertifikate verwenden und Clientzertifikate aus dem selbstsignierten Stammzertifikat erstellen. Andernfalls sind die von Ihnen erstellten Zertifikate nicht mit P2S-Verbindungen kompatibel, und Ihnen wird ein Verbindungsfehler angezeigt.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Beschaffen des öffentlichen Schlüssels (CER-Format) für das Stammzertifikat

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Generieren eines Clientzertifikats

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Hochladen der CER-Datei des Stammzertifikats

Nachdem das Gateway erstellt wurde, laden Sie die CER-Datei (mit den Informationen zum öffentlichen Schlüssel) für ein vertrauenswürdiges Stammzertifikat auf den Azure-Server hoch. Laden Sie nicht den privaten Schlüssel für das Stammzertifikat hoch. Nach dem Hochladen des Zertifikats verwendet Azure es, um Clients zu authentifizieren, auf denen ein aus dem vertrauenswürdigen Stammzertifikat generiertes Clientzertifikat installiert ist. Sie können später bei Bedarf weitere (insgesamt bis zu 20) vertrauenswürdige Stammzertifikate hochladen.  

1. Wählen Sie auf der Seite für Ihr VNET im Abschnitt **VPN-Verbindungen** die Clientgrafik zum Öffnen der Seite **Point-to-Site-VPN-Verbindung** aus.

   ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Wählen Sie auf der Seite **Point-to-Site-VPN-Verbindung** **Zertifikat verwalten** aus, um die Seite **Zertifikate** zu öffnen.

   ![Seite "Zertifikate"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Wählen Sie auf der Seite **Zertifikate** **Hochladen** aus, um die Seite **Zertifikat hochladen** zu öffnen.

    ![Seite zum Hochladen von Zertifikaten](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Wählen Sie die Ordnergrafik aus, um zur CER-Datei zu navigieren. Wählen Sie die Datei und anschließend **OK** aus. Das hochgeladene Zertifikat wird auf der Seite **Zertifikate** angezeigt.

   ![Hochladen des Zertifikats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Konfigurieren des Clients

Um über ein Point-to-Site-VPN eine Verbindung mit einem VNET herstellen zu können, muss auf jedem Client ein Paket zum Konfigurieren des nativen Windows VPN-Clients installiert werden. Das Konfigurationspaket konfiguriert den nativen Windows-VPN-Client mit den Einstellungen, die zum Herstellen einer Verbindung mit dem virtuellen Netzwerk erforderlich sind.

Sie können auf jedem Clientcomputer das gleiche VPN-Clientkonfigurationspaket verwenden – vorausgesetzt, es handelt sich dabei um die passende Version für die Architektur des jeweiligen Clients. Die Liste mit den unterstützten Clientbetriebssystemen finden Sie im Abschnitt [Point-to-Site – Häufig gestellte Fragen](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Generieren und Installieren eines VPN-Clientkonfigurationspakets

1. Wählen Sie im Azure-Portal auf der Seite **Übersicht** für Ihr VNET im Abschnitt **VPN-Verbindungen** die Clientgrafik aus, um die Seite **Point-to-Site-VPN-Verbindung** zu öffnen.

2. Wählen Sie im oberen Bereich der Seite **Point-to-Site-VPN-Verbindung** das Downloadpaket für das Clientbetriebssystem aus, unter dem es installiert wird:

   * Wählen Sie für 64-Bit-Clients das Paket **VPN-Client (64 Bit)** aus.
   * Wählen Sie für 32-Bit-Clients das Paket **VPN-Client (32 Bit)** aus.

   ![Herunterladen des VPN-Clientkonfigurationspakets](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Laden Sie das Paket nach dem Generieren herunter, und installieren Sie es auf Ihrem Clientcomputer. Sollte eine SmartScreen-Popupmeldung angezeigt werden, wählen Sie **Weitere Informationen** und anschließend **Trotzdem ausführen** aus. Sie können das Paket auch speichern und auf anderen Clientcomputern installieren.

### <a name="install-a-client-certificate"></a>Installieren eines Clientzertifikats

Um eine P2S-Verbindung mit einem anderen Clientcomputer als dem für die Generierung der Clientzertifikate verwendeten Computer herzustellen, installieren Sie ein Clientzertifikat. Beim Installieren eines Clientzertifikats benötigen Sie das Kennwort, das beim Exportieren des Clientzertifikats erstellt wurde. In der Regel können Sie das Zertifikat installieren, indem Sie einfach darauf doppelklicken. Weitere Informationen finden Sie unter [Installieren eines exportierten Clientzertifikats](vpn-gateway-certificates-point-to-site.md#install).


## <a name="connect-to-your-vnet"></a>Herstellen der Verbindung mit Ihrem VNet

>[!NOTE]
>Auf dem Clientcomputer, von dem aus Sie die Verbindung herstellen, müssen Sie über Administratorrechte verfügen.
>
>

1. Um eine Verbindung mit Ihrem VNET herzustellen, navigieren Sie auf dem Clientcomputer zu **VPN-Verbindungen** im Azure-Portal und suchen nach der VPN-Verbindung, die Sie erstellt haben. Die VPN-Verbindung hat den gleichen Namen wie Ihr virtuelles Netzwerk. Wählen Sie **Verbinden**aus. Wenn eine Popupmeldung zu dem Zertifikat angezeigt wird, wählen Sie **Weiter** zur Verwendung erhöhter Rechte aus.

2. Wählen Sie auf der Statusseite **Verbindung** **Verbinden** aus, um die Verbindung herzustellen. Wenn der Bildschirm **Zertifikat auswählen** angezeigt wird, stellen Sie sicher, dass das angezeigte Clientzertifikat richtig ist. Falls nicht, wählen Sie das richtige Zertifikat in der Dropdownliste aus, und wählen Sie dann **OK** aus.

3. Wenn die Verbindung hergestellt wird, sehen Sie die Benachrichtigung **Verbunden**.


### <a name="troubleshooting-p2s-connections"></a>Problembehandlung für P2S-Verbindungen

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Überprüfen der VPN-Verbindung

1. Stellen Sie sicher, dass Ihre VPN-Verbindung aktiv ist. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten auf Ihrem Clientcomputer, und führen Sie **ipconfig/all** aus.
2. Zeigen Sie die Ergebnisse an. Beachten Sie, dass die IP-Adresse, die Sie erhalten, eine Adresse aus dem Adressbereich der P2S-Verbindung ist, den Sie beim Erstellen des virtuellen Netzwerks angegeben haben. Die Ergebnisse sollten in etwa wie folgt aussehen:

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

## <a name="connect-to-a-virtual-machine"></a>Herstellen einer Verbindung mit einem virtuellen Computer

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>Hinzufügen oder Entfernen vertrauenswürdiger Stammzertifikate

Sie können vertrauenswürdige Stammzertifikate hinzufügen und aus Azure entfernen. Wenn Sie ein Stammzertifikat entfernen, können Clients, für die über diesen Stamm ein Zertifikat generiert wurde, nicht mehr authentifiziert werden und keine Verbindung mehr herstellen. Damit diese Clients sich wieder authentifizieren und eine Verbindung herstellen können, müssen Sie ein neues, aus einem für Azure vertrauenswürdigen Stammzertifikat generiertes Clientzertifikat installieren.

### <a name="to-add-a-trusted-root-certificate"></a>So fügen Sie ein vertrauenswürdiges Stammzertifikat hinzu

Sie können Azure bis zu 20 vertrauenswürdige CER-Stammzertifikatdateien hinzufügen. Eine entsprechende Anleitung finden Sie in „Hochladen der CER-Datei des Stammzertifikats“.

### <a name="to-remove-a-trusted-root-certificate"></a>Entfernen eines vertrauenswürdigen Stammzertifikats

1. Wählen Sie auf der Seite für Ihr VNET im Abschnitt **VPN-Verbindungen** die Clientgrafik zum Öffnen der Seite **Point-to-Site-VPN-Verbindung** aus.

   ![Clients](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Wählen Sie auf der Seite **Point-to-Site-VPN-Verbindung** **Zertifikat verwalten** aus, um die Seite **Zertifikate** zu öffnen.

   ![Seite "Zertifikate"](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Wählen Sie auf der Seite **Zertifikate** neben dem Zertifikat, das Sie entfernen möchten, die Auslassungspunkte und anschließend **Löschen** aus.

   ![Löschen eines Stammzertifikats](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Sperren eines Clientzertifikats

Bei Bedarf können Sie ein Clientzertifikat sperren. Anhand der Zertifikatsperrliste können Sie basierend auf einzelnen Clientzertifikaten selektiv Punkt-zu-Standort-Verbindungen verweigern. Diese Methode unterscheidet sich vom Entfernen eines vertrauenswürdigen Stammzertifikats. Wenn Sie ein vertrauenswürdiges Stammzertifikat (CER-Datei) aus Azure entfernen, wird der Zugriff für alle Clientzertifikate gesperrt, die mit dem gesperrten Stammzertifikat generiert oder signiert wurden. Wenn Sie anstelle des Stammzertifikats ein Clientzertifikat sperren, können die anderen Zertifikate, die auf der Grundlage des Stammzertifikats generiert wurden, weiterhin zur Authentifizierung für die P2S-Verbindung verwendet werden.

Üblicherweise wird das Stammzertifikat zum Verwalten des Zugriffs auf Team- oder Organisationsebene verwendet. Eine genauer abgestufte Steuerung des Zugriffs für einzelne Benutzer erfolgt hingegen mit gesperrten Clientzertifikaten.

### <a name="to-revoke-a-client-certificate"></a>So sperren Sie ein Clientzertifikat

Sie können ein Clientzertifikat sperren, indem Sie den Fingerabdruck der Sperrliste hinzufügen.

1. Rufen Sie den Fingerabdruck des Clientzertifikats ab. Weitere Informationen finden Sie unter [Gewusst wie: Abrufen des Fingerabdrucks eines Zertifikats](https://msdn.microsoft.com/library/ms734695.aspx).
2. Kopieren Sie die Informationen in einen Text-Editor, und entfernen Sie alle Leerzeichen, sodass eine fortlaufende Zeichenfolge entsteht.
3. Navigieren Sie zum klassischen virtuellen Netzwerk. Wählen Sie **Point-to-Site-VPN-Verbindung** und dann **Zertifikat verwalten** aus, um die Seite **Zertifikate** zu öffnen.
4. Wählen Sie **Sperrliste** zum Öffnen der Seite **Sperrliste** aus. 
5. Wählen Sie **Zertifikat hinzufügen** aus, um die Seite **Zertifikat der Sperrliste hinzufügen** zu öffnen.
6. Fügen Sie in **Fingerabdruck** den Zertifikatfingerabdruck als durchgehende Textzeile (ohne Leerzeichen) ein. Wählen Sie **OK** aus, um den Vorgang abzuschließen.

Nach Abschluss der Aktualisierung kann das Zertifikat nicht mehr für die Verbindungsherstellung verwendet werden. Clients, die versuchen, unter Verwendung dieses Zertifikats eine Verbindung herzustellen, erhalten eine Meldung mit dem Hinweis, dass das Zertifikat nicht mehr gültig ist.

## <a name="point-to-site-faq"></a>Point-to-Site – Häufig gestellte Fragen

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Nächste Schritte

- Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](https://docs.microsoft.com/azure/) . 

- Weitere Informationen zu Netzwerken und virtuellen Computern finden Sie unter [Azure- und Linux-VM-Netzwerke (Übersicht)](../virtual-machines/linux/network-overview.md).

- Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Azure Punkt-zu-Standort-Verbindungsprobleme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
