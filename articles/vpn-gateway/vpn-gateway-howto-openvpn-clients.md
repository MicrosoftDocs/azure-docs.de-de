---
title: Konfigurieren von OpenVPN-Clients für Azure VPN Gateway | Microsoft-Dokumentation
description: Schritte zum Konfigurieren von OpenVPN-Clients für Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 5/21/2019
ms.author: cherylmc
ms.openlocfilehash: fdfabf328ddfa6b5e4b578be5a1b329cb3219a18
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989106"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurieren von OpenVPN-Clients für Azure VPN Gateway

Dieser Artikel umfasst Informationen zum Konfigurieren von **OpenVPN ® Protocol**-Clients.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vergewissern Sie sich, dass Sie die Schritte zum Konfigurieren von OpenVPN für Ihr VPN-Gateway abgeschlossen haben. Einzelheiten finden Sie unter [Konfigurieren von OpenVPN für Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Windows-Clients

1. Laden Sie den OpenVPN-Client von der offiziellen [OpenVPN-Website](https://openvpn.net/index.php/open-source/downloads.html) herunter, und installieren Sie ihn.
2. Laden Sie das VPN-Profil für das Gateway herunter. Sie können es auf der Registerkarte „Point-to-Site-Konfiguration“ im Azure-Portal oder in PowerShell mit dem Cmdlet „New-AzVpnClientConfiguration“ herunterladen.
3. Entzippen Sie das Profil. Öffnen Sie anschließend in Editor im Ordner „OpenVPN“ die Konfigurationsdatei *vpnconfig.ovpn*.
4. [Exportieren](vpn-gateway-certificates-point-to-site.md#clientexport) Sie das P2S-Clientzertifikat, das Sie erstellt und in Ihre P2S-Konfiguration auf dem Gateway hochgeladen haben.
5. Extrahieren Sie den privaten Schlüssel und den Base64-Fingerabdruck aus der *PFX*-Datei. Dazu gibt es verschiedene Möglichkeiten. Die Verwendung von OpenSSL auf Ihrem Computer ist eine Möglichkeit. Die Datei *profileinfo.txt* enthält den privaten Schlüssel und den Fingerabdruck für die Zertifizierungsstelle sowie das Clientzertifikat. Verwenden Sie unbedingt den Fingerabdruck des Clientzertifikats.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Öffnen Sie *profileinfo.txt* in Editor. Um den Fingerabdruck des Clientzertifikats (untergeordnetes Zertifikat) zu erhalten, markieren Sie den Text einschließlich und zwischen „-----BEGIN CERTIFICATE-----“ und „-----END CERTIFICATE-----“ für das untergeordnete Zertifikat, und kopieren Sie ihn. Das untergeordnete Zertifikat finden Sie in der Zeile „subject=/“.
7. Wechseln Sie zur Datei *vpnconfig.ovpn*, die Sie in Schritt 3 in Editor geöffnet haben. Suchen Sie nach dem unten gezeigten Abschnitt, und ersetzen Sie den gesamten Text zwischen „cert“ und „/cert“.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öffnen Sie die Datei *profileinfo.txt* in Editor. Um den privaten Schlüssel zu erhalten, markieren Sie den Text einschließlich und zwischen „-----BEGIN PRIVATE KEY-----“ und „-----END PRIVATE KEY-----“, und kopieren Sie ihn.
9. Kehren Sie zur Datei „vpnconfig.ovpn“ in Editor zurück, und suchen Sie nach diesem Abschnitt. Fügen Sie den privaten Schlüssel ein, indem Sie den gesamten Text zwischen „key“ und „/key“ ersetzen.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Ändern Sie keine anderen Felder. Verwenden Sie die ausgefüllte Konfiguration in der Clienteingabe, um eine Verbindung mit dem VPN herzustellen.
11. Kopieren Sie die Datei „vpnconfig.ovpn“ in den Ordner „C:\Programme\OpenVPN\config“.
12. Klicken Sie mit rechten Maustaste auf das Symbol „OpenVPN“, und wählen Sie „Verbinden“ aus.

## <a name="mac"></a>Mac-Clients

1. Laden Sie einen OpenVPN-Client wie z.B. [Tunnelblick](https://tunnelblick.net/downloads.html) herunter, und installieren Sie ihn. 
2. Laden Sie das VPN-Profil für das Gateway herunter. Sie können es auf der Registerkarte „Point-to-Site-Konfiguration“ im Azure-Portal oder in PowerShell mit dem Cmdlet „New-AzVpnClientConfiguration“ herunterladen.
3. Entzippen Sie das Profil. Öffnen Sie in Editor im Ordner „OpenVPN“ die Konfigurationsdatei „vpnconfig.ovpn“.
4. Füllen Sie den Abschnitt „P2S client certificate“ mit dem öffentlichen Schlüssel für das P2S-Clientzertifikat in Base64 aus. In einem Zertifikat im PEM-Format können Sie einfach die CER-Datei öffnen und den base64-Schlüssel zwischen den Zertifikatskopfzeilen herauskopieren. Unter [Exportieren des öffentlichen Schlüssels](vpn-gateway-certificates-point-to-site.md#cer) finden Sie Informationen dazu, wie Sie ein Zertifikat exportieren können, um den codierten öffentlichen Schlüssel zu erhalten.
5. Füllen Sie den Abschnitt „Private key“ mit dem privaten Schlüssel für das P2S-Clientzertifikat in Base64 aus. Informationen zum Extrahieren privater Schlüssel finden Sie unter [Exportieren des privaten Schlüssels](https://openvpn.net/community-resources/how-to/#pki).
6. Ändern Sie keine anderen Felder. Verwenden Sie die ausgefüllte Konfiguration in der Clienteingabe, um eine Verbindung mit dem VPN herzustellen.
7. Doppelklicken Sie auf die Profildatei, um das Profil in Tunnelblick zu erstellen.
8. Starten Sie Tunnelblick im Anwendungsordner.
9. Klicken Sie auf das Symbol „Tunnelblick“, und wählen Sie „Verbinden“ aus.

> [!IMPORTANT]
>Für das OpenVPN-Protokoll werden nur iOS 11.0 und höher und MacOS 10.13 und höher unterstützt.
>

## <a name="linux"></a>Linux-Clients

1. Starten Sie eine neue Terminalsitzung. Sie können eine neue Sitzung öffnen, indem Sie STRG+ALT+T gleichzeitig drücken.
2. Geben Sie für die Installation der benötigten Komponenten den folgenden Befehl ein:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Laden Sie das VPN-Profil für das Gateway herunter. Das Profil kann auf der Registerkarte „Point-to-Site-Konfiguration“ im Azure-Portal heruntergeladen werden.
4. [Exportieren](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) Sie das P2S-Clientzertifikat, das Sie erstellt und in Ihre P2S-Konfiguration auf dem Gateway hochgeladen haben. 
5. Extrahieren Sie den privaten Schlüssel und den Base64-Fingerabdruck aus der PFX-Datei. Dazu gibt es verschiedene Möglichkeiten. Die Verwendung von OpenSSL auf Ihrem Computer ist eine Möglichkeit.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Die Datei *profileinfo.txt* enthält den privaten Schlüssel und den Fingerabdruck für die Zertifizierungsstelle sowie das Clientzertifikat. Verwenden Sie unbedingt den Fingerabdruck des Clientzertifikats.

6. Öffnen Sie die Datei *profileinfo.txt* in einem Text-Editor. Um den Fingerabdruck des Clientzertifikats (untergeordnetes Zertifikat) zu erhalten, markieren Sie den Text einschließlich und zwischen „-----BEGIN CERTIFICATE-----“ und „-----END CERTIFICATE-----“ für das untergeordnete Zertifikat, und kopieren Sie ihn. Das untergeordnete Zertifikat finden Sie in der Zeile „subject=/“.

7. Öffnen Sie die Datei *vpnconfig.ovpn*, und suchen Sie nach dem unten gezeigten Abschnitt. Ersetzen Sie den gesamten Text zwischen „cert“ und „/cert“.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öffnen Sie die Datei „profileinfo.txt“ in einem Text-Editor. Um den privaten Schlüssel zu erhalten, markieren Sie den Text einschließlich und zwischen „-----BEGIN PRIVATE KEY-----“ und „-----END PRIVATE KEY-----“, und kopieren Sie ihn.

9. Öffnen Sie die Datei „vpnconfig.ovpn“ in einem Text-Editor, und suchen Sie nach diesem Abschnitt. Fügen Sie den privaten Schlüssel ein, indem Sie den gesamten Text zwischen „key“ und „/key“ ersetzen.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ändern Sie keine anderen Felder. Verwenden Sie die ausgefüllte Konfiguration in der Clienteingabe, um eine Verbindung mit dem VPN herzustellen.
11. Um über die Befehlszeile eine Verbindung herzustellen, geben Sie den folgenden Befehl ein:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>
    ```
12. Um über die grafische Benutzeroberfläche eine Verbindung herzustellen, wechseln Sie zu „Systemeinstellungen“.
13. Klicken Sie auf **+** , um eine neue VPN-Verbindung hinzuzufügen.
14. Wählen Sie unter **VPN hinzufügen**, den Befehl **Aus Datei importieren...** aus.
15. Navigieren Sie zur Profildatei, und doppelklicken Sie darauf, oder wählen Sie **Öffnen** aus.
16. Klicken Sie im Fenster **VPN hinzufügen** auf **Hinzufügen**.
  
    ![Aus Datei importieren](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. Sie können eine Verbindung herstellen, indem Sie auf der Seite **Netzwerkeinstellungen** oder unter dem Netzwerksymbol auf der Taskleiste das VPN auf **EIN** festlegen.

## <a name="next-steps"></a>Nächste Schritte

Wenn die VPN-Clients Zugriff auf Ressourcen in einem anderen VNET haben sollen, führen Sie die Anweisungen im Artikel [Konfigurieren einer VNET-zu-VNET-VPN-Gatewayverbindung](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) aus, um eine VNET-zu-VNET-Verbindung einzurichten. Stellen Sie sicher, dass BGP (Border Gateway Protocol) für die Gateways und die Verbindungen aktiviert ist, da andernfalls kein Datenverkehr fließt.

**„OpenVPN“ ist eine Marke von OpenVPN Inc.**
