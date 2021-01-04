---
title: 'Erstellen und Installieren von Clientkonfigurationsdateien für Point-to-Site-VPN: Zertifikatauthentifizierung'
titleSuffix: Azure VPN Gateway
description: Erstellen und Installieren von VPN-Clientkonfigurationsdateien unter Windows, Linux, Linux (strongSwan) und Mac OS X für die P2S-Zertifikatauthentifizierung
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/11/2020
ms.author: cherylmc
ms.openlocfilehash: c7b186aa1a6f63b1bc3e9dbefa5001faac967762
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556099"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Erstellen und Installieren von VPN-Clientkonfigurationsdateien für P2S-Konfigurationen mit nativer Azure-Zertifikatauthentifizierung

VPN-Clientkonfigurationsdateien sind in einer ZIP-Datei enthalten. Konfigurationsdateien enthalten die Einstellungen, die zum Herstellen von Point-to-Site-Verbindungen mit nativer Azure-Zertifikatauthentifizierung zwischen einem nativen Windows-, Mac IKEv2 VPN- oder Linux-Client und einem virtuellen Netzwerk erforderlich sind.

Clientkonfigurationsdateien gelten spezifisch für die VPN-Konfiguration für das virtuelle Netzwerk. Wenn nach der Erstellung der VPN-Clientkonfigurationsdateien Änderungen an der P2S-VPN-Konfiguration (beispielsweise am VPN-Protokolltyp oder -Authentifizierungstyp) vorgenommen werden, müssen Sie neue VPN-Clientkonfigurationsdateien für die Benutzergeräte erstellen.

* Weitere Informationen zu Point-to-Site-Verbindungen finden Sie unter [About Point-to-Site VPN](point-to-site-about.md) (Informationen zu P2S-VPN).
* OpenVPN-Anweisungen finden Sie unter [Konfigurieren von OpenVPN-Clients für Azure VPN Gateway (Point-to-Site) (Vorschau)](vpn-gateway-howto-openvpn.md) sowie unter [Konfigurieren von OpenVPN-Clients für Azure VPN Gateway (Vorschau)](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generieren der VPN-Clientkonfigurationsdateien

Stellen Sie zunächst sicher, dass für alle Benutzer, die eine Verbindung herstellen, ein gültiges Zertifikat auf den Benutzergeräten installiert ist. Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Install a client certificate for Point-to-Site Azure certificate authentication connections](point-to-site-how-to-vpn-client-install-azure-cert.md) (Installieren eines Clientzertifikats für Verbindungen für die P2S-Azure-Zertifikatauthentifizierung).

Sie können Clientkonfigurationsdateien mithilfe von PowerShell oder mithilfe des Azure-Portals erstellen. Mit beiden Methoden wird die gleiche ZIP-Datei zurückgegeben. Entzippen Sie die Datei, um die folgenden Ordner anzuzeigen:

* **WindowsAmd64** und **WindowsX86**. Diese Ordner enthalten das Windows-32-Bit- bzw. das 64-Bit-Installer-Paket. Das Installer-Paket **WindowsAmd64** gilt nicht nur für Amd, sondern für alle unterstützten 64-Bit-Windows-Clients.
* **Allgemein**: Dieser Ordner enthält allgemeine Informationen zum Erstellen Ihrer eigenen VPN-Clientkonfiguration. Der Ordner „Allgemein“ wird bereitgestellt, wenn für das Gateway IKEv2 oder SSTP und IKEv2 konfiguriert wurde. Wenn nur SSTP konfiguriert ist, ist der Ordner „Allgemein“ nicht vorhanden.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Erstellen von Dateien über das Azure-Portal

1. Navigieren Sie im Azure-Portal zum Gateway für das virtuelle Netzwerk, mit dem Sie eine Verbindung herstellen möchten.
1. Wählen Sie auf der Seite mit dem Gateway für virtuelle Netzwerke die Option **Point-to-Site-Konfiguration** aus.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="Herunterladen des VPN-Clients":::
1. Wählen Sie oben auf der Seite „Point-to-Site-Konfiguration“ die Option **VPN-Client herunterladen** aus. Es dauert einige Minuten, bis das Clientkonfigurationspaket generiert wird.
1. In Ihrem Browser wird ein Hinweis angezeigt, dass eine Clientkonfigurationsdatei im ZIP-Format verfügbar ist. Sie hat den gleichen Namen wie das Gateway. Entzippen Sie die Datei, um die Ordner anzuzeigen.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Generieren von Dateien mithilfe von PowerShell

1. Beim Generieren von VPN-Clientkonfigurationsdateien ist für „-AuthenticationMethod“ der Wert „EapTls“ angegeben. Erstellen Sie die VPN-Clientkonfigurationsdateien mit dem folgenden Befehl:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Kopieren Sie die URL in Ihren Browser, um die ZIP-Datei herunterzuladen. Entzippen Sie dann die Datei, um die Ordner anzuzeigen.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Sie müssen den IKEv2-VPN-Client auf jedem Mac, der sich mit Azure verbindet, manuell konfigurieren. Azure bietet für die native Azure-Zertifikatauthentifizierung nicht die MOBILECONFIG-Datei. **Allgemein** enthält alle Informationen, die Sie für die Konfiguration benötigen. Enthält der Download nicht den Ordner „Allgemein“, wurde als Tunneltyp wahrscheinlich nicht IKEv2 ausgewählt. Beachten Sie, dass die Basic-SKU des VPN-Gateways IKEv2 nicht unterstützt. Wählen Sie IKEv2 aus, und generieren Sie anschließend die ZIP-Datei erneut, um den Ordner „Allgemein“ abzurufen.<br>Der Ordner „Allgemein“ enthält die folgenden Dateien:

* **VpnSettings.xml**. Diese Datei enthält wichtige Einstellungen wie Serveradresse und Tunneltyp. 
* **VpnServerRoot.cer**. Diese Datei enthält das Stammzertifikat, das zum Überprüfen des Azure-VPN-Gateways während der P2S-Verbindungseinrichtung erforderlich ist.

Führen Sie die folgenden Schritte aus, um den nativen VPN-Client auf dem Mac für die Zertifkatauthentifizierung zu konfigurieren. Sie müssen die folgenden Schritte auf jedem Mac ausführen, den Sie mit Azure verbinden:

1. Importieren Sie das Stammzertifikat **VpnServerRoot** auf Ihrem Mac. Hierzu können Sie die Datei auf den Mac kopieren und darauf doppelklicken. Wählen Sie zum Importieren die Option **Hinzufügen** aus.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="Screenshot der Seite für Zertifikate":::
  
    >[!NOTE]
    >Durch Doppelklicken auf das Zertifikat wird nicht das Dialogfeld **Hinzufügen** angezeigt. Das Zertifikat wird jedoch im richtigen Speicher installiert. Sie können im Anmeldeschlüsselbund in der Zertifikatkategorie nach dem Zertifikat suchen.
    >
  
1. Stellen Sie sicher, dass Sie ein Clientzertifikat installiert haben, das von dem Stammzertifikat, das Sie beim Konfigurieren der P2S-Einstellungen in Azure hochgeladen haben, ausgestellt wurde. Dies unterscheidet sich von der VPNServerRoot-Datei, die Sie im vorherigen Schritt installiert haben. Ein Clientzertifikat wird für die Authentifizierung verwendet und ist erforderlich. Weitere Informationen zum Generieren von Zertifikaten finden Sie unter [Generieren von Zertifikaten](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Installieren eines Clientzertifikats](point-to-site-how-to-vpn-client-install-azure-cert.md).
1. Öffnen Sie unter **Netzwerkeinstellungen** das Dialogfeld **Netzwerk**, und wählen Sie das Pluszeichen ( **+** ) aus, um ein neues VPN-Clientverbindungsprofil für eine P2S-Verbindung mit dem virtuellen Azure-Netzwerk zu erstellen.

   Für **Schnittstelle** ist der Wert „VPN“ und für **VPN-Typ** der Wert „IKEv2“ angegeben. Geben Sie im Feld **Dienstname** einen Namen für das Profil ein, und wählen Sie dann **Erstellen** aus, um das VPN-Clientverbindungsprofil zu erstellen.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="Screenshot des Fensters „Netzwerk“ mit der Option zum Auswählen einer Schnittstelle und eines VPN-Typs und Eingeben eines Dienstnamens":::
1. Kopieren Sie aus der Datei **VpnSettings.xml** im Ordner **Allgemein** den Tagwert **VpnServer**. Fügen Sie diesen Wert in die Felder **Serveradresse** und **Remote-ID** des Profils ein.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="Screenshot mit Serverinformationen":::
1. Wählen Sie **Authentifizierungseinstellungen** und dann **Zertifikat** aus. Wählen Sie für **Catalina** die Option **Keine** und dann **Zertifikat** aus.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="Screenshot der Authentifizierungseinstellungen":::

   Wählen Sie für Catalina die Option **Keine** und dann **Zertifikat** aus. **Wählen** Sie das richtige Zertifikat aus.
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="Screenshot des Fensters „Netzwerk“, in dem „Keine“ für Authentifizierungseinstellungen und ein Zertifikat ausgewählt sind":::

1. Klicken Sie auf **Auswählen...** , um das Clientzertifikat auszuwählen, das Sie für die Authentifizierung verwenden möchten. Dies ist das Zertifikat, das Sie in Schritt 2 installiert haben.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="Screenshot des Fensters „Netzwerk“ mit Authentifizierungseinstellungen, in denen ein Zertifikat ausgewählt werden kann":::
1. Unter **Choose An Identity** (Identität auswählen) wird eine Liste mit Zertifikaten zur Auswahl angezeigt. Wählen Sie das richtige Zertifikat und dann **Weiter** aus.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="Screenshot des Dialogfelds „Choose An Identity“ (Identität auswählen), in dem Sie das richtige Zertifikat auswählen können":::

1. Geben Sie im Feld **Local ID** (Lokale ID) den Namen des Zertifikats (aus Schritt 6) ein. In diesem Beispiel lautet er `ikev2Client.com`. Wählen Sie dann **Übernehmen** aus, um die Änderungen zu speichern.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="Screenshot der Option „Übernehmen“":::
1. Wählen Sie im Dialogfeld **Netzwerk** die Option **Übernehmen** aus, um alle Änderungen zu speichern. Wählen Sie dann **Verbinden**, um die P2S-Verbindung mit dem virtuellen Azure-Netzwerk zu starten.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan mit grafischer Benutzeroberfläche)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Installieren von strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Generieren von Zertifikaten

Wenn Sie noch keine Zertifikate generiert haben, verwenden Sie die folgenden Schritte:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Installieren und konfigurieren

Die folgenden Anweisungen wurden für Ubuntu 18.0.4 erstellt. Ubuntu 16.0.10 unterstützt die strongSwan-GUI nicht. Wenn Sie Ubuntu 16.0.10 einsetzen möchten, müssen Sie die [Befehlszeile](#linuxinstallcli) verwenden. Die folgenden Beispiele entsprechen abhängig von Ihrer Version von Linux und strongSwan möglicherweise nicht den Bildschirmen, die Ihnen angezeigt werden.

1. Öffnen Sie das **Terminal**, und installieren Sie **strongSwan** und den Netzwerk-Manager, indem Sie den Befehl im Beispiel ausführen.

   ```
   sudo apt install network-manager-strongswan
   ```
1. Wählen Sie **Einstellungen** aus, und wählen Sie dann **Netzwerk** aus. Erstellen Sie über die Schaltfläche **+** eine neue Verbindung.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="Screenshot der Seite mit Netzwerkverbindungen":::

1. Wählen Sie im Dropdownmenü **IPsec/IKEv2 (strongSwan)** aus, und doppelklicken Sie darauf.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="Screenshot der Seite zum Hinzufügen eines VPN":::

1. Fügen Sie auf der Seite **VPN hinzufügen** einen Namen für die VPN-Verbindung hinzu.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="Screenshot der Auswahl eines Verbindungstyps":::
1. Öffnen Sie die Datei **VpnSettings.xml**, die sich im Ordner **Generic** der heruntergeladenen Clientkonfigurationsdateien befindet. Suchen Sie das Tag **VpnServer**, und kopieren Sie den Namen, der mit „azuregateway“ beginnt und mit „cloudapp.net“ endet.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="Screenshot des Datenkopiervorgangs":::
1. Fügen Sie den Namen im Feld **Adresse** Ihrer neuen VPN-Verbindung im Abschnitt **Gateway** ein. Wählen Sie anschließend das Ordnersymbol am Ende des Felds **Zertifikat**, navigieren Sie zum Ordner **Allgemein**, und wählen Sie dort die Datei **VpnServerRoot**.
1. Wählen Sie im Abschnitt **Client** der Verbindung für **Authentifizierung** die Option **Certificate/private key** aus. Wählen Sie für **Zertifikat** und **Privater Schlüssel** das Zertifikat und den privaten Schlüssel aus, die zuvor erstellt wurden. Wählen Sie in **Optionen** die Option **Innere IP-Adresse anfordern** aus. Klicken Sie anschließend auf **Hinzufügen**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="Screenshot der Option „Innere IP-Adresse anfordern“":::

1. Aktivieren Sie die Verbindung (**Ein**).

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="Screenshot des Kopiervorgangs":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan mit Befehlszeilenschnittstelle)

### <a name="install-strongswan"></a>Installieren von strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generieren von Zertifikaten

Wenn Sie noch keine Zertifikate generiert haben, verwenden Sie die folgenden Schritte:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Installieren und konfigurieren

1. Laden Sie das VPNClient-Paket aus dem Azure-Portal herunter.
1. Entpacken Sie die Datei.
1. Kopieren oder verschieben Sie die Datei **VpnServerRoot.cer** aus dem Ordner **Allgemein** nach **/etc/ipsec.d/cacerts**.
1. Kopieren oder verschieben Sie **cp client.p12** nach **/etc/ipsec.d/private/** . Diese Datei ist das Clientzertifikat für das VPN-Gateway.
1. Öffnen Sie Datei **VpnSettings.xml**, und kopieren Sie den Wert `<VpnServer>`. Dieser Wert wird im nächsten Schritt verwendet.
1. Passen Sie die Werte im folgenden Beispiel an, und fügen Sie das Beispiel dann in die Konfiguration **/etc/ipsec.conf** ein.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. Fügen Sie die folgenden Werte in **/etc/ipsec.secrets** ein.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. Führen Sie die folgenden Befehle aus:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Nächste Schritte

Kehren Sie zum ursprünglichen Artikel zurück, von dem aus Sie begonnen haben, und schließen Sie dann die P2S-Konfiguration ab.

* [Konfigurationsschritte in PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Konfigurationsschritte im Azure-Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
