---
title: 'Erstellen und Installieren von P2S-VPN-Clientkonfigurationsdateien für die Azure-Zertifikatauthentifizierung: Azure'
description: Erstellen und Installieren von VPN-Clientkonfigurationsdateien unter Windows, Linux, Linux (strongSwan) und Mac OS X für die P2S-Zertifikatauthentifizierung
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: cherylmc
ms.openlocfilehash: b590dabbe4b2c6526f2c602aeed64667348eefa9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66113969"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Erstellen und Installieren von VPN-Clientkonfigurationsdateien für P2S-Konfigurationen mit nativer Azure-Zertifikatauthentifizierung

VPN-Clientkonfigurationsdateien sind in einer ZIP-Datei enthalten. Konfigurationsdateien enthalten die Einstellungen, die zum Herstellen von Point-to-Site-Verbindungen mit nativer Azure-Zertifikatauthentifizierung zwischen einem nativen Windows-, Mac IKEv2 VPN- oder Linux-Client und einem VNET erforderlich sind.

Clientkonfigurationsdateien gelten spezifisch für die VPN-Konfiguration für das VNET. Wenn nach der Erstellung der VPN-Clientkonfigurationsdateien Änderungen an der P2S-VPN-Konfiguration (beispielsweise am VPN-Protokolltyp oder -Authentifizierungstyp) vorgenommen werden, müssen Sie neue VPN-Clientkonfigurationsdateien für die Benutzergeräte erstellen. 

* Weitere Informationen zu Point-to-Site-Verbindungen finden Sie unter [About Point-to-Site VPN](point-to-site-about.md) (Informationen zu P2S-VPN).
* OpenVPN-Anweisungen finden Sie unter [Konfigurieren von OpenVPN-Clients für Azure VPN Gateway (Point-to-Site) (Vorschau)](vpn-gateway-howto-openvpn.md) sowie unter [Konfigurieren von OpenVPN-Clients für Azure VPN Gateway (Vorschau)](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate"></a>Generieren der VPN-Clientkonfigurationsdateien

Stellen Sie zunächst sicher, dass für alle Benutzer, die eine Verbindung herstellen, ein gültiges Zertifikat auf den Benutzergeräten installiert ist. Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Install a client certificate for Point-to-Site Azure certificate authentication connections](point-to-site-how-to-vpn-client-install-azure-cert.md) (Installieren eines Clientzertifikats für Verbindungen für die P2S-Azure-Zertifikatauthentifizierung).

Sie können Clientkonfigurationsdateien mithilfe von PowerShell oder mithilfe des Azure-Portals erstellen. Mit beiden Methoden wird die gleiche ZIP-Datei zurückgegeben. Entzippen Sie die Datei, um die folgenden Ordner anzuzeigen:

  * **WindowsAmd64** und **WindowsX86**. Diese Ordner enthalten das Windows-32-Bit- bzw. das 64-Bit-Installer-Paket. Das Installer-Paket **WindowsAmd64** gilt nicht nur für Amd, sondern für alle unterstützten 64-Bit-Windows-Clients.
  * **Allgemein**: Dieser Ordner enthält allgemeine Informationen zum Erstellen Ihrer eigenen VPN-Clientkonfiguration. Der Ordner „Allgemein“ wird bereitgestellt, wenn für das Gateway IKEv2 oder SSTP und IKEv2 konfiguriert wurde. Wenn nur SSTP konfiguriert ist, ist der Ordner „Allgemein“ nicht vorhanden.

### <a name="zipportal"></a>Erstellen von Dateien über das Azure-Portal

1. Navigieren Sie im Azure-Portal zum Gateway für das virtuelle Netzwerk, mit dem Sie eine Verbindung herstellen möchten.
2. Klicken Sie auf der Seite mit dem Gateway für virtuelle Netzwerke auf **Point-to-Site-Konfiguration**.
3. Klicken Sie oben auf der Seite „Point-to-Site-Konfiguration“ auf **VPN-Client herunterladen**. Es dauert einige Minuten, bis das Clientkonfigurationspaket generiert wird.
4. In Ihrem Browser wird ein Hinweis angezeigt, dass eine Clientkonfigurationsdatei im ZIP-Format verfügbar ist. Sie hat den gleichen Namen wie das Gateway. Entzippen Sie die Datei, um die Ordner anzuzeigen.

### <a name="zipps"></a>Generieren von Dateien mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Beim Generieren von VPN-Clientkonfigurationsdateien ist für „-AuthenticationMethod“ der Wert „EapTls“ angegeben. Erstellen Sie die VPN-Clientkonfigurationsdateien mit dem folgenden Befehl:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Kopieren Sie die URL in Ihren Browser, um die ZIP-Datei herunterzuladen. Entzippen Sie dann die Datei, um die Ordner anzuzeigen.

## <a name="installwin"></a>Windows

Sie können auf jedem Windows-Clientcomputer das gleiche VPN-Clientkonfigurationspaket verwenden – vorausgesetzt, es handelt sich dabei um die passende Version für die Architektur des jeweiligen Clients. Die Liste mit den unterstützten Clientbetriebssystemen finden Sie im Abschnitt [Point-to-Site-Verbindungen](vpn-gateway-vpn-faq.md#P2S) der häufig gestellten Fragen zu VPN Gateway.

>[!NOTE]
>Sie müssen über Administratorrechte auf dem Windows-Clientcomputer verfügen, von dem Sie eine Verbindung herstellen.
>
>

Führen Sie die folgenden Schritte aus, um den nativen Windows-VPN-Client für die Zertifkatauthentifizierung zu konfigurieren:

1. Wählen Sie die VPN-Clientkonfigurationsdateien, die der Architektur des Windows-Computers entsprechen. Wählen Sie für eine 64-Bit-Prozessorarchitektur das Installer-Paket „VpnClientSetupAmd64“ aus. Wählen Sie für eine 32-Bit-Prozessorarchitektur das Installer-Paket „VpnClientSetupX86“ aus. 
2. Doppelklicken Sie auf das Paket, um es zu installieren. Sollte ein SmartScreen-Popup erscheinen, klicken Sie auf **Weitere Informationen** und anschließend auf **Trotzdem ausführen**.
3. Navigieren Sie auf dem Clientcomputer zu **Netzwerkeinstellungen**, und klicken Sie auf **VPN**. Die VPN-Verbindung zeigt den Namen des virtuellen Netzwerks an, mit dem eine Verbindung hergestellt wird. 
4. Bevor Sie versuchen, eine Verbindung herzustellen, überprüfen Sie, ob Sie auf dem Clientcomputer ein Clientzertifikat installiert haben. Ein Clientzertifikat ist für die Authentifizierung erforderlich, wenn Sie den Typ „native Azure-Zertifikatauthentifizierung“ verwenden. Weitere Informationen zum Generieren von Zertifikaten finden Sie unter [Generieren von Zertifikaten](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Installieren eines Clientzertifikats](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Sie müssen den IKEv2-VPN-Client auf jedem Mac, der sich mit Azure verbindet, manuell konfigurieren. Azure bietet für die native Azure-Zertifikatauthentifizierung nicht die MOBILECONFIG-Datei. **Allgemein** enthält alle Informationen, die Sie für die Konfiguration benötigen. Enthält der Download nicht den Ordner „Allgemein“, wurde als Tunneltyp wahrscheinlich nicht IKEv2 ausgewählt. Beachten Sie, dass die Basic-SKU des VPN-Gateways IKEv2 nicht unterstützt. Wählen Sie IKEv2 aus, und generieren Sie anschließend die ZIP-Datei erneut, um den Ordner „Allgemein“ abzurufen.<br>Der Ordner „Allgemein“ enthält die folgenden Dateien:

* **VpnSettings.xml**. Diese Datei enthält wichtige Einstellungen wie Serveradresse und Tunneltyp. 
* **VpnServerRoot.cer**. Diese Datei enthält das Stammzertifikat, das zum Überprüfen des Azure-VPN-Gateways während der P2S-Verbindungseinrichtung erforderlich ist.

Führen Sie die folgenden Schritte aus, um den nativen VPN-Client auf dem Mac für die Zertifkatauthentifizierung zu konfigurieren. Sie müssen die folgenden Schritte auf jedem Mac ausführen, den Sie mit Azure verbinden:

1. Importieren Sie das Stammzertifikat **VpnServerRoot** auf Ihrem Mac. Hierzu können Sie die Datei auf den Mac kopieren und darauf doppelklicken. Klicken Sie zum Importieren auf **Hinzufügen**.

   ![Hinzufügen des Zertifikats](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Durch Doppelklicken auf das Zertifikat wird nicht das Dialogfeld **Hinzufügen** angezeigt. Das Zertifikat wird jedoch im richtigen Speicher installiert. Sie können im Anmeldeschlüsselbund in der Zertifikatkategorie nach dem Zertifikat suchen.
    >
  
2. Stellen Sie sicher, dass Sie ein Clientzertifikat installiert haben, das von dem Stammzertifikat, das Sie beim Konfigurieren der P2S-Einstellungen in Azure hochgeladen haben, ausgestellt wurde. Dies unterscheidet sich von der VPNServerRoot-Datei, die Sie im vorherigen Schritt installiert haben. Ein Clientzertifikat wird für die Authentifizierung verwendet und ist erforderlich. Weitere Informationen zum Generieren von Zertifikaten finden Sie unter [Generieren von Zertifikaten](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Weitere Informationen zum Installieren eines Clientzertifikats finden Sie unter [Installieren eines Clientzertifikats](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Öffnen Sie unter **Network Preferences** (Netzwerkeinstellungen) das Dialogfeld **Netzwerk**, und klicken Sie auf **„+“**, um ein neues VPN-Clientverbindungsprofil für eine P2S-Verbindung mit dem Azure-NET zu erstellen.

   Für **Schnittstelle** ist der Wert „VPN“ und für **VPN-Typ** der Wert „IKEv2“ angegeben. Geben Sie im Feld **Dienstname** einen Namen für das Profil ein, und klicken Sie auf **Erstellen**, um das VPN-Clientverbindungsprofil zu erstellen.

   ![Netzwerk](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. Kopieren Sie aus der Datei **VpnSettings.xml** im Ordner **Allgemein** den Tagwert **VpnServer**. Fügen Sie diesen Wert in die Felder **Serveradresse** und **Remote-ID** des Profils ein.

   ![Serverinformationen](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klicken Sie auf **Authentifizierungseinstellungen**, und wählen Sie **Zertifikat** aus. 

   ![Authentifizierungseinstellungen](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Klicken Sie auf **Auswählen...**, um das Clientzertifikat auszuwählen, das Sie für die Authentifizierung verwenden möchten. Dies ist das Zertifikat, das Sie in Schritt 2 installiert haben.

   ![Zertifikat](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. Unter **Choose An Identity** (Identität auswählen) wird eine Liste mit Zertifikaten zur Auswahl angezeigt. Wählen Sie das richtige Zertifikat aus, und klicken Sie auf **Weiter**.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. Geben Sie im Feld **Local ID** (Lokale ID) den Namen des Zertifikats (aus Schritt 6) ein. In diesem Beispiel lautet er „ikev2Client.com“. Klicken Sie auf die Schaltfläche **Übernehmen**, um die Änderungen zu speichern.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Klicken Sie im Dialogfeld **Netzwerk** auf **Übernehmen**, um alle Änderungen zu speichern. Klicken Sie auf **Verbinden**, um die P2S-Verbindung mit dem Azure-VNET zu starten.

## <a name="linuxgui"></a>Linux (strongSwan mit grafischer Benutzeroberfläche)

### <a name="extract-the-key-and-certificate"></a>Extrahieren von Schlüssel und Zertifikat

Für strongSwan müssen Sie den Schlüssel und das Zertifikat aus dem Clientzertifikat (PFX-Datei) extrahieren und sie in einzelnen PEM-Dateien speichern.
Führen Sie die folgenden Schritte aus:

1. Laden Sie OpenSSL von [OpenSSL](https://www.openssl.org/source/) herunter, und installieren Sie das Programm.
2. Öffnen Sie ein Befehlszeilenfenster, und wechseln Sie zu dem Verzeichnis, in dem Sie OpenSSL installiert haben, z.B. „c:\OpenSLL-Win64\bin\'“.
3. Führen Sie den folgenden Befehl aus, um den privaten Schlüssel zu extrahieren und in einer neuen Datei namens „privatekey.pem“ aus dem Clientzertifikat zu speichern:

   ```
   C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
   ```
4. Führen Sie nun den folgenden Befehl zum Extrahieren des öffentlichen Zertifikats aus, und speichern Sie es in einer neuen Datei:

   ```
   C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
   ```

### <a name="install"></a>Installieren und konfigurieren

Die folgenden Anweisungen wurden über von strongSwan 5.5.1 auf Ubuntu 17.0.4 erstellt. Ubuntu 16.0.10 unterstützt die strongSwan-GUI nicht. Wenn Sie Ubuntu 16.0.10 einsetzen möchten, müssen Sie die [Befehlszeile](#linuxinstallcli) verwenden. Die folgenden Beispiele entsprechen abhängig von Ihrer Version von Linux und strongSwan möglicherweise nicht den Bildschirmen, die Ihnen angezeigt werden.

1. Öffnen Sie das **Terminal**, und installieren Sie **strongSwan** und den Netzwerk-Manager, indem Sie den Befehl im Beispiel ausführen. Wenn Sie eine Fehlermeldung zu *libcharon-extra-plugins* erhalten, ersetzen Sie dies durch „strongswan-plugin-eap-mschapv2“.

   ```
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Wählen Sie das Symbol für den **Netzwerk-Manager** (Pfeil nach oben/nach unten) und dann **Verbindungen bearbeiten** aus.

   ![Verbindungen bearbeiten](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Erstellen Sie über die Schaltfläche **Hinzufügen** eine neue Verbindung.

   ![Eine Verbindung hinzufügen](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Wählen Sie **IPsec/IKEv2 (strongswan)** aus dem Dropdownmenü aus, und klicken Sie auf **Erstellen**. Sie können Ihre Verbindung in diesem Schritt umbenennen.

   ![Verbindungstyp auswählen](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Öffnen Sie die Datei **VpnSettings.xml**, die sich im Ordner **Generic** der heruntergeladenen Clientkonfigurationsdateien befindet. Suchen Sie das Tag **VpnServer**, und kopieren Sie den Namen, der mit „azuregateway“ beginnt und mit „cloudapp.net“ endet.

   ![Namen kopieren](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Fügen Sie diesen Namen im Feld **Adresse** Ihrer neuen VPN-Verbindung im Abschnitt **Gateway** ein. Wählen Sie anschließend das Ordnersymbol am Ende des Felds **Zertifikat**, navigieren Sie zum Ordner **Allgemein**, und wählen Sie dort die Datei **VpnServerRoot**.
7. Wählen Sie im Abschnitt **Client** der Verbindung für **Authentifizierung** die Option **Certificate/private key** aus. Wählen Sie für **Zertifikat** und **Privater Schlüssel** das Zertifikat und den privaten Schlüssel aus, die zuvor erstellt wurden. Wählen Sie in **Optionen** die Option **Innere IP-Adresse anfordern** aus. Klicken Sie anschließend auf **Hinzufügen**.

   ![Innere IP-Adresse anfordern](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. Klicken Sie auf das Symbol für den **Netzwerk-Manager** (Pfeil nach oben/nach unten), und wählen Sie **Verbindungen bearbeiten**. Sie sehen die von Ihnen erstellte VPN-Verbindung. Klicken Sie, um die Verbindung zu initiieren.

## <a name="linuxinstallcli"></a>Linux (strongSwan mit Befehlszeilenschnittstelle)

### <a name="install-strongswan"></a>Installieren von strongSwan

Sie können die folgenden CLI-Befehle oder die strongSwan-Schritte auf der [grafischen Benutzeroberfläche](#install) verwenden, um strongSwan zu installieren.

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

### <a name="install-and-configure"></a>Installieren und konfigurieren

1. Laden Sie das VPNClient-Paket aus dem Azure-Portal herunter.
2. Extrahieren Sie die Datei.
3. Kopieren oder verschieben Sie die Datei „VpnServerRoot.cer“ aus dem Ordner **Allgemein** nach „/etc/ipsec.d/cacerts“.
4. Kopieren oder verschieben Sie „cp client.p12“ nach „/etc/ipsec.d/private/“. Diese Datei ist das Clientzertifikat für Azure VPN Gateway.
5. Öffnen Sie Datei „VpnSettings.xml“, und kopieren Sie den Wert `<VpnServer>`. Dieser Wert wird im nächsten Schritt verwendet.
6. Passen Sie die Werte im folgenden Beispiel an, und fügen Sie das Beispiel dann in die Konfiguration „/etc/ipsec.conf“ ein.
  
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
6. Fügen Sie Folgendes in */etc/ipsec.secrets* ein.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Führen Sie die folgenden Befehle aus:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Nächste Schritte

Rufen Sie erneut den Artikel zum [Abschließen Ihrer P2S-Konfiguration](vpn-gateway-howto-point-to-site-rm-ps.md) auf.

Informationen zur Problembehandlung bei P2S-Verbindungen finden Sie in den folgenden Artikeln:

  * [Beheben von Problemen bei Azure-P2S-Verbindungen (Point-to-Site)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Beheben von Problemen bei VPN-Verbindungen von Mac OS X-VPN-Clients](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
