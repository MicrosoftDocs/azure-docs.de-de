---
title: 'Erstellen und Installieren von VPN-Clientkonfigurationsdateien für P2S-RADIUS-Verbindungen: PowerShell: Azure | Microsoft-Dokumentation'
description: Erstellen Sie Windows-, Mac OS X- und Linux-VPN-Client-Konfigurationsdateien für Verbindungen, die RADIUS-Authentifizierung verwenden.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/27/2019
ms.author: cherylmc
ms.openlocfilehash: 34d8eb976a2a1e173f234be214799832dae7e9ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66115384"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Erstellen und Installieren von Clientkonfigurationsdateien für P2S-RADIUS-Authentifizierung

Zum Herstellen einer P2S-Verbindung (Point-to-Site) mit einem virtuellen Netzwerk müssen Sie das Clientgerät konfigurieren, über das die Verbindung hergestellt werden soll. Sie können P2S-VPN-Verbindungen über Windows-, Mac OS X- und Linux-Clientgeräte erstellen. 

Wenn Sie die RADIUS-Authentifizierung verwenden, stehen mehrere Authentifizierungsoptionen zur Verfügung: Authentifizierung per Benutzername/Kennwort, zertifikatbasierte Authentifizierung sowie weitere Authentifizierungstypen. Die VPN-Clientkonfiguration ist je nach Authentifizierungstyp unterschiedlich. Um den VPN-Client zu konfigurieren, verwenden Sie Clientkonfigurationsdateien, die die erforderlichen Einstellungen enthalten. Dieser Artikel hilft Ihnen beim Erstellen und Installieren der VPN-Clientkonfiguration für den gewünschten RADIUS-Authentifizierungstyp.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Der Konfigurationsworkflow für die P2S-RADIUS-Authentifizierung lautet wie folgt:

1. [Richten Sie das Azure-VPN-Gateway für P2S-Konnektivität ein](point-to-site-how-to-radius-ps.md).
2. [Richten Sie den RADIUS-Server für die Authentifizierung ein](point-to-site-how-to-radius-ps.md#radius). 
3. **Rufen Sie die VPN-Clientkonfiguration für die gewünschte Authentifizierungsoption ab, um den VPN-Client damit einzurichten** (dieser Artikel).
4. [Schließen Sie die P2S-Konfiguration ab, und stellen Sie eine Verbindung her](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Wenn nach der Erstellung des VPN-Clientkonfigurationsprofils Änderungen an der P2S-VPN-Konfiguration (beispielsweise am VPN-Protokolltyp oder -Authentifizierungstyp) vorgenommen werden, müssen Sie eine neue VPN-Clientkonfiguration auf den Benutzergeräten erstellen und installieren.
>
>

Entscheiden Sie zunächst, welchen Authentifizierungstyp Sie verwenden möchten: Benutzername/Kennwort, Zertifikat oder andere Authentifizierungstypen. Jeder Abschnitt enthält Schritte für Windows, Mac OS X und Linux (derzeit sind Schritte nur eingeschränkt verfügbar).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="adeap"></a>Authentifizierung mit Benutzername und Kennwort

Sie können die Authentifizierung mit Benutzername und Kennwort per Active Directory konfigurieren oder sich ohne Active Directory authentifizieren. Stellen Sie in jedem Szenario sicher, dass alle Benutzer, die eine Verbindung herstellen, über Benutzername/Kennwort verfügen und über RADIUS authentifiziert werden können.

Sie können beim Konfigurieren der Authentifizierung per Benutzername/Kennwort nur eine Konfiguration für das EAP-MSCHAPv2-Authentifizierungsprotokoll mit Benutzername und Kennwort erstellen. In den Befehlen ist `EapMSChapv2` als `-AuthenticationMethod` angegeben.

### <a name="usernamefiles"></a> 1. Generieren der VPN-Clientkonfigurationsdateien

Sie können die VPN-Clientkonfigurationsdateien über das Azure-Portal oder mithilfe von Azure PowerShell erstellen.

#### <a name="azure-portal"></a>Azure-Portal

1. Navigieren Sie zum Gateway für virtuelle Netzwerke.
2. Klicken Sie auf **Point-to-Site-Konfiguration**.
3. Klicken Sie auf **VPN-Client herunterladen**.
4. Wählen Sie den Client aus, und geben Sie alle erforderlichen Informationen an.
5. Klicken Sie auf **Herunterladen**, um die ZIP-Datei zu generieren.
6. Die ZIP-Datei wird heruntergeladen – in der Regel in Ihren Ordner für Downloads.

#### <a name="azure-powershell"></a>Azure PowerShell

Generieren Sie VPN-Clientkonfigurationsdateien zur Verwendung für die Authentifizierung mit Benutzername und Kennwort. Sie können die VPN-Clientkonfigurationsdateien mit dem folgenden Befehl generieren:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Bei Ausführung des Befehls wird ein Link zurückgegeben. Kopieren Sie den Link, und fügen Sie ihn in einen Webbrowser ein, um **VpnClientConfiguration.zip** herunterzuladen. Entzippen Sie die Datei, um die folgenden Ordner anzuzeigen: 
 
* **WindowsAmd64** und **WindowsX86**: Diese Ordner enthalten das Windows-64-Bit- bzw. das Windows-32-Bit-Installer-Paket. 
* **Generic**: Dieser Ordner enthält allgemeine Informationen zum Erstellen Ihrer eigenen VPN-Clientkonfiguration. Dieser Ordner wird für Konfigurationen der Authentifizierung mit Benutzername und Kennwort nicht benötigt.
* **Mac**: Wenn Sie beim Erstellen des Gateways für virtuelle Netzwerke IKEv2 konfiguriert haben, wird ein Ordner mit dem Namen **Mac** angezeigt, der die Datei **mobileconfig** enthält. Diese Datei wird zum Konfigurieren von Mac-Clients verwendet.

Wenn Sie die Clientkonfigurationsdateien bereits erstellt haben, können Sie diese mithilfe des Cmdlets `Get-AzVpnClientConfiguration` abrufen. Wenn Sie jedoch Änderungen an der P2S-VPN-Konfiguration (beispielsweise am VPN-Protokolltyp oder -Authentifizierungstyp) vornehmen, wird die Konfiguration nicht automatisch aktualisiert. Sie müssen das Cmdlet  `New-AzVpnClientConfiguration` ausführen, um einen neuen Konfigurationsdownload zu erstellen.

Um zuvor generierte Clientkonfigurationsdateien abzurufen, verwenden Sie den folgenden Befehl:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="setupusername"></a> 2. VPN-Clients konfigurieren

Sie können die folgenden VPN-Clients konfigurieren:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux über strongSwan](#adlinuxcli)
 
#### <a name="adwincli"></a>Setup für Windows-VPN-Clients

Sie können auf jedem Windows-Clientcomputer das gleiche VPN-Clientkonfigurationspaket verwenden – vorausgesetzt, es handelt sich dabei um die passende Version für die Architektur des jeweiligen Clients. Die Liste mit den unterstützten Clientbetriebssystemen finden Sie in den [häufig gestellten Fragen](vpn-gateway-vpn-faq.md#P2S).

Führen Sie die folgenden Schritte aus, um den nativen Windows-VPN-Client für die Zertifkatauthentifizierung zu konfigurieren:

1. Wählen Sie die VPN-Clientkonfigurationsdateien, die der Architektur des Windows-Computers entsprechen. Wählen Sie für eine 64-Bit-Prozessorarchitektur das Installer-Paket **VpnClientSetupAmd64** aus. Wählen Sie für eine 32-Bit-Prozessorarchitektur das Installer-Paket **VpnClientSetupX86** aus. 
2. Installieren Sie das Paket per Doppelklick. Wenn ein SmartScreen-Popupelement angezeigt wird, wählen Sie **Weitere Informationen** > **Trotzdem ausführen**.
3. Navigieren Sie auf dem Clientcomputer zu **Netzwerkeinstellungen**, und wählen Sie **VPN** aus. Die VPN-Verbindung zeigt den Namen des virtuellen Netzwerks an, mit dem eine Verbindung hergestellt wird. 

#### <a name="admaccli"></a>Setup des Mac-VPN-Clients (OS X)

1. Wählen Sie die Datei **VpnClientSetup mobileconfig**, und senden Sie sie an alle Benutzer. Sie können sie per E-Mail senden oder eine andere Methode verwenden.

2. Navigieren Sie auf dem Mac zur Datei **mobileconfig**.

   ![Speicherort der Datei „mobileconfig“](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Optionaler Schritt: Wenn Sie ein benutzerdefiniertes DNS angeben möchten, fügen Sie der Datei **mobileconfig** die folgenden Zeilen hinzu:

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        <array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. Doppelklicken Sie auf das Profil, um es zu installieren, und wählen Sie **Weiter** aus. Der Profilname ist identisch mit dem Namen Ihres virtuellen Netzwerks.

   ![Installationsmeldung](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Wählen Sie **Weiter**, um dem Absender des Profils zu vertrauen und die Installation fortzusetzen.

   ![Bestätigungsmeldung](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Während der Profilinstallation haben Sie die Möglichkeit, den Benutzernamen und das Kennwort für die VPN-Authentifizierung anzugeben. Es ist nicht erforderlich, diese Informationen einzugeben. Wenn Sie sie angegeben, werden die Informationen gespeichert und automatisch verwendet, wenn Sie eine Verbindung initiieren. Wählen Sie **Installieren**, um den Vorgang fortzusetzen.

   ![Felder für Benutzername und Kennwort für VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Geben Sie einen Benutzernamen und ein Kennwort für die Berechtigungen ein, die zum Installieren des Profils auf Ihrem Computer erforderlich sind. Klicken Sie auf **OK**.

   ![Felder für Benutzername und Kennwort für die Profilinstallation](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. Nach der Installation des Profils wird es im Dialogfeld **Profile** angezeigt. Dieses Dialogfeld kann später auch über **Systemeinstellungen** geöffnet werden.

   ![Dialogfeld „Profile“](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. Öffnen Sie zum Zugreifen auf die VPN-Verbindung über **Systemeinstellungen** das Dialogfeld **Netzwerk**.

   ![Symbole in den Systemeinstellungen](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. Die VPN-Verbindung wird als **IkeV2-VPN** angezeigt. Sie können den Namen durch Aktualisieren der Datei **mobileconfig** ändern.

    ![Details für die VPN-Verbindung](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Wählen Sie **Authentifizierungseinstellungen**. Wählen Sie in der Liste **Benutzername**, und geben Sie Ihre Anmeldeinformationen ein. Wenn Sie die Anmeldeinformationen zuvor eingegeben haben, wird **Benutzername** in der Liste automatisch ausgewählt, und Benutzername und Kennwort werden vorab aufgefüllt. Wählen Sie **OK**, um die Einstellungen zu speichern.

    ![Authentifizierungseinstellungen](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. Zurück im Dialogfeld **Netzwerk** wählen Sie **Anwenden** um die Änderungen zu speichern. Wählen Sie **Verbinden** aus, um die Verbindung zu initiieren.

#### <a name="adlinuxcli"></a>Einrichtung des Linux-VPN-Clients über strongSwan

Die folgenden Anweisungen wurden über von strongSwan 5.5.1 auf Ubuntu 17.0.4 erstellt. Die Anzeige kann je nach Ihrer Version von Linux und strongSwan abweichen.

1. Öffnen Sie das **Terminal**, und installieren Sie **strongSwan** und den Netzwerk-Manager, indem Sie den Befehl im Beispiel ausführen. Wenn ein Fehler im Zusammenhang mit `libcharon-extra-plugins` angezeigt wird, ersetzen Sie diesen Wert durch `strongswan-plugin-eap-mschapv2`.

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Wählen Sie das Symbol für den **Netzwerk-Manager** (Pfeil nach oben/nach unten), und wählen Sie **Verbindungen bearbeiten**.

   ![Auswahl von „Verbindungen bearbeiten“ im Netzwerk-Manager](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Erstellen Sie über die Schaltfläche **Hinzufügen** eine neue Verbindung.

   ![Schaltfläche „Hinzufügen“ für eine Verbindung](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Wählen Sie im Dropdownmenü **IPsec/IKEv2 (strongswan)** und dann **Erstellen** aus. Sie können Ihre Verbindung in diesem Schritt umbenennen.

   ![Auswählen des Verbindungstyps](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Öffnen Sie die Datei **vpnSettings.xml** im Ordner **Allgemein** der heruntergeladenen Clientkonfigurationsdateien. Suchen Sie das Tag mit dem Namen `VpnServer`, und kopieren Sie den Namen, der mit `azuregateway` beginnt und mit `.cloudapp.net` endet.

   ![Inhalt der Datei „VpnSettings.xml“](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Fügen Sie diesen Namen im Feld **Adresse** Ihrer neuen VPN-Verbindung im Abschnitt **Gateway** ein. Wählen Sie anschließend das Ordnersymbol am Ende des Felds **Zertifikat**, navigieren Sie zum Ordner **Allgemein**, und wählen Sie dort die Datei **VpnServerRoot**.
7. Wählen Sie im Abschnitt **Client** der Verbindung **EAP** als **Authentifizierung**, und geben Sie Ihren Benutzernamen und Ihr Kennwort ein. Möglicherweise müssen Sie das Schlosssymbol auf der rechten Seite wählen, um diese Informationen zu speichern. Wählen Sie anschließend **Speichern** aus.

   ![Bearbeiten der Verbindungseinstellungen](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Wählen Sie das Symbol für den **Netzwerk-Manager** (Pfeil nach oben/nach unten), und halten Sie den Mauszeiger auf **VPN-Verbindungen**. Sie sehen die von Ihnen erstellte VPN-Verbindung. Wählen Sie die Verbindung aus, um sie zu initiieren.

   ![Verbindung „VPN-Radius“ im Netzwerk-Manager](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certeap"></a>Zertifikatauthentifizierung
 
Sie können VPN-Clientkonfigurationsdateien für die RADIUS-Zertifikatauthentifizierung erstellen, die das EAP-TLS-Protokoll nutzt. In der Regel wird ein vom Unternehmen ausgestelltes Zertifikat zum Authentifizieren eines Benutzers für VPN verwendet. Stellen Sie sicher, dass alle Benutzer, die eine Verbindung herstellen, ein Zertifikat auf ihren Geräten installiert haben und das Zertifikat von Ihrem RADIUS-Server überprüft werden kann.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

In den Befehlen ist `EapTls` als `-AuthenticationMethod` angegeben. Während der Zertifikatauthentifizierung überprüft der Client den RADIUS-Server durch Überprüfen des Zertifikats. Bei `-RadiusRootCert` handelt es sich um die CER-Datei mit dem Stammzertifikat, das zum Überprüfen des RADIUS-Servers verwendet wird.

Auf jedem VPN-Client-Gerät muss ein Clientzertifikat installiert werden. In einigen Fällen besitzt ein Windows-Gerät mehrere Clientzertifikate. Während der Authentifizierung kann das zur Anzeige eines Popupdialogfelds führen, in dem alle Zertifikate aufgeführt sind. Der Benutzer muss dann das zu verwendende Zertifikat auswählen. Das richtige Zertifikat kann durch Angabe des Stammzertifikats herausgefiltert werden, mit dem das Clientzertifikat verkettet werden soll. 

`-ClientRootCert` ist die CER-Datei mit dem Stammzertifikat. Dies ist ein optionaler Parameter. Wenn das Gerät, über das Sie die Verbindung herstellen möchten, nur über ein Clientzertifikat verfügt, müssen Sie diesen Parameter nicht angeben.

### <a name="certfiles"></a>1. Generieren der VPN-Clientkonfigurationsdateien

Generieren Sie VPN-Clientkonfigurationsdateien zur Verwendung für die Zertifikatauthentifizierung. Sie können die VPN-Clientkonfigurationsdateien mit dem folgenden Befehl generieren:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Bei Ausführung des Befehls wird ein Link zurückgegeben. Kopieren Sie den Link, und fügen Sie ihn in einen Webbrowser ein, um „VpnClientConfiguration.zip“ herunterzuladen. Entzippen Sie die Datei, um die folgenden Ordner anzuzeigen:

* **WindowsAmd64** und **WindowsX86**: Diese Ordner enthalten das Windows-64-Bit- bzw. das Windows-32-Bit-Installer-Paket. 
* **GenericDevice**: Dieser Ordner enthält allgemeine Informationen zum Erstellen Ihrer eigenen VPN-Clientkonfiguration.

Wenn Sie die Clientkonfigurationsdateien bereits erstellt haben, können Sie diese mithilfe des Cmdlets `Get-AzVpnClientConfiguration` abrufen. Wenn Sie jedoch Änderungen an der P2S-VPN-Konfiguration (beispielsweise am VPN-Protokolltyp oder -Authentifizierungstyp) vornehmen, wird die Konfiguration nicht automatisch aktualisiert. Sie müssen das Cmdlet  `New-AzVpnClientConfiguration` ausführen, um einen neuen Konfigurationsdownload zu erstellen.

Um zuvor generierte Clientkonfigurationsdateien abzurufen, verwenden Sie den folgenden Befehl:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="setupusername"></a> 2. VPN-Clients konfigurieren

Sie können die folgenden VPN-Clients konfigurieren:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (unterstützt, noch keine Schritte im Artikel)

#### <a name="certwincli"></a>Setup für Windows-VPN-Clients

1. Wählen Sie ein Konfigurationspaket aus, und installieren Sie es auf dem Clientgerät. Wählen Sie für eine 64-Bit-Prozessorarchitektur das Installer-Paket **VpnClientSetupAmd64** aus. Wählen Sie für eine 32-Bit-Prozessorarchitektur das Installer-Paket **VpnClientSetupX86** aus. Wenn ein SmartScreen-Popupelement angezeigt wird, wählen Sie **Weitere Informationen** > **Trotzdem ausführen**. Sie können das Paket auch speichern und auf anderen Clientcomputern installieren.
2. Jeder Client benötigt ein Clientzertifikat für die Authentifizierung. Installieren Sie das Clientzertifikat. Informationen zu Clientzertifikaten finden Sie unter [Generieren und Exportieren von Zertifikaten für Point-to-Site-Verbindungen mithilfe von PowerShell unter Windows 10 oder Windows Server 2016](vpn-gateway-certificates-point-to-site.md). Informationen zum Installieren eines generierten Zertifikats finden Sie unter [Installieren eines Zertifikats auf Windows-Clients](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Navigieren Sie auf dem Clientcomputer zu **Netzwerkeinstellungen**, und wählen Sie **VPN** aus. Die VPN-Verbindung zeigt den Namen des virtuellen Netzwerks an, mit dem eine Verbindung hergestellt wird.

#### <a name="certmaccli"></a>Setup des Mac-VPN-Clients (OS X)

Sie müssen für jedes Mac-Gerät ein eigenes Profil erstellen, das eine Verbindung mit dem virtuellen Azure-Netzwerk herstellt. Das liegt daran, dass für diese Geräte das Benutzerzertifikat für die Authentifizierung im Profil angegeben werden muss. Der Ordner **Allgemein** enthält alle zum Erstellen eines Profils notwendigen Informationen:

* **VpnSettings.xml** enthält wichtige Einstellungen wie Serveradresse und Tunneltyp.
* **VpnServerRoot.cer** enthält das Stammzertifikat, das zum Überprüfen des VPN-Gateways während der P2S-Verbindungseinrichtung erforderlich ist.
* **RadiusServerRoot.cer** enthält das Stammzertifikat, das zum Überprüfen des RADIUS-Servers während der Authentifizierung erforderlich ist.

Führen Sie die folgenden Schritte aus, um den nativen VPN-Client auf dem Mac für die Zertifikatauthentifizierung zu konfigurieren:

1. Importieren Sie die Stammzertifikate **VpnServerRoot** und **RadiusServerRoot** auf Ihrem Mac. Kopieren Sie die einzelnen Dateien auf Ihren Mac, doppelklicken Sie darauf, und wählen Sie dann **Hinzufügen**.

   ![Hinzufügen des Zertifikats „VpnServerRoot“](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Hinzufügen des Zertifikats „RadiusServerRoot“](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Jeder Client benötigt ein Clientzertifikat für die Authentifizierung. Installieren Sie das Clientzertifikat auf dem Clientgerät.
3. Öffnen Sie das Dialogfeld **Netzwerk** unter **Netzwerkeinstellungen**. Wählen Sie **+** um ein neues VPN-Clientverbindungsprofil für eine P2S-Verbindung mit dem virtuellen Azure-Netzwerk zu erstellen.

   Für **Schnittstelle** ist der Wert **VPN** und für **VPN-Typ** der Wert **IKEv2** angegeben. Geben Sie im Feld **Dienstname** einen Namen für das Profil ein, und wählen Sie dann **Erstellen**, um das VPN-Clientverbindungsprofil zu erstellen.

   ![Schnittstellen- und Dienstnameninformationen](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Kopieren Sie aus der Datei **VpnSettings.xml** im Ordner **Allgemein** den Tagwert **VpnServer**. Fügen Sie diesen Wert in die Felder **Serveradresse** und **Remote-ID** des Profils ein. Lassen Sie das Feld **Local ID** (Lokale ID) leer.

   ![Serverinformationen](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Wählen Sie **Authentifizierungseinstellungen** und dann **Zertifikat** aus. 

   ![Authentifizierungseinstellungen](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klicken Sie auf **Auswählen**, um das Zertifikat auszuwählen, das Sie für die Authentifizierung verwenden möchten.

   ![Auswählen eines vorhandenen Zertifikats für die Authentifizierung](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. Unter **Choose An Identity** (Identität auswählen) wird eine Liste mit Zertifikaten zur Auswahl angezeigt. Wählen Sie das richtige Zertifikat aus, und wählen Sie dann **Weiter**.

   ![Liste „Choose An Identity“ (Identität auswählen)](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. Geben Sie im Feld **Local ID** (Lokale ID) den Namen des Zertifikats (aus Schritt 6) ein. In diesem Beispiel lautet er **ikev2Client.com**. Wählen Sie dann die Schaltfläche **Anwenden**, um die Änderungen zu speichern.

   ![Feld „Local ID“ (Lokale ID)](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. Wählen Sie im Dialogfeld **Netzwerk** die Option **Anwenden**, um alle Änderungen zu speichern. Wählen Sie dann **Verbinden**, um die P2S-Verbindung mit dem virtuellen Azure-Netzwerk zu starten.

## <a name="otherauth"></a>Arbeiten mit anderen Authentifizierungstypen oder -protokollen

Wenn Sie einen anderen Authentifizierungstyp (etwa OTP) oder ein anderes Authentifizierungsprotokoll (etwa PEAP-MSCHAPv2 anstelle von EAP-MSCHAPv2) verwenden möchten, müssen Sie ein eigenes VPN-Clientkonfigurationsprofil erstellen. Für die Erstellung des Profils benötigen Sie Informationen wie die IP-Adresse des Gateways für virtuelle Netzwerke, den Tunneltyp und Routen mit geteilten Tunneln. Diese Informationen können mit den folgenden Schritten abgerufen werden:

1. Generieren Sie mit dem Cmdlet `Get-AzVpnClientConfiguration` die VPN-Clientkonfiguration für EapMSChapv2.

2. Entzippen Sie die Datei „VpnClientConfiguration.zip“, und suchen Sie den Ordner **GenericDevice**. Ignorieren Sie die Ordner mit den Windows-Installationsprogrammen für 64-Bit- und 32-Bit-Architekturen.
 
3. Der Ordner **GenericDevice** enthält eine XML-Datei mit dem Namen **VpnSettings**. Diese Datei enthält alle erforderlichen Informationen:

   * **VpnServer**: Der vollqualifizierte Domänenname des Azure-VPN-Gateways. Dies ist die Adresse, mit der der Client eine Verbindung herstellt.
   * **VpnType**: Der zum Herstellen einer Verbindung verwendete Tunneltyp.
   * **Routes**: Routen, die in Ihrem Profil konfiguriert werden müssen, damit nur Datenverkehr für das virtuelle Azure-Netzwerk über den P2S-Tunnel gesendet wird.
   
   Der Ordner **GenericDevice** enthält außerdem eine CER-Datei mit dem Namen **VpnServerRoot**. Diese Datei enthält das Stammzertifikat, das zum Überprüfen des Azure-VPN-Gateways während der Einrichtung der P2S-Verbindung erforderlich ist. Installieren Sie das Zertifikat auf allen Geräten, die eine Verbindung mit dem virtuellen Azure-Netzwerk herstellen.

## <a name="next-steps"></a>Nächste Schritte

Rufen Sie erneut den Artikel zum [Abschließen Ihrer P2S-Konfiguration](point-to-site-how-to-radius-ps.md) auf.

Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Probleme mit Azure P2S-Verbindungen (Point-to-Site)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
