---
title: 'VPN Gateway: VPN-Client für OpenVPN-P2S-Protokollverbindungen: Azure AD-Authentifizierung'
description: Erfahren Sie, wie Sie P2S VPN verwenden können, um über Azure AD-Authentifizierung eine Verbindung mit Ihrem VNet herzustellen.
services: vpn-gateway
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 8e97a2f077efd4d00eec4a91645dc1b65057ebd9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565006"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Konfigurieren eines VPN-Clients für P2S-VPN-Protokollverbindungen: Azure AD-Authentifizierung

Dieser Artikel unterstützt Sie beim Konfigurieren eines VPN-Clients für die Verbindung zu einem virtuellen Netzwerk mit einem Point-to-Site-VPN und Azure Active Directory-Authentifizierung. Damit Sie mit Azure AD eine Verbindung herstellen und eine Authentifizierung durchführen können, müssen Sie zuerst Ihren Azure AD-Mandanten konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren eines Azure AD-Mandanten](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Die Azure AD-Authentifizierung wird nur für Verbindungen mit dem OpenVPN®-Protokoll unterstützt.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Arbeiten mit Clientprofilen

Zum Herstellen einer Verbindung müssen Sie auf jedem Computer, der eine Verbindung mit dem VNET herstellen möchte, Azure VPN Client herunterladen und ein VPN-Clientprofil konfigurieren. Sie können ein Clientprofil auf einem Computer erstellen, das Profil exportieren und dann auf weiteren Computern importieren.

### <a name="to-download-the-azure-vpn-client"></a>So laden Sie Azure VPN Client herunter

Verwenden Sie diesen [Link](https://go.microsoft.com/fwlink/?linkid=2117554), um Azure VPN Client herunterzuladen. Stellen Sie sicher, dass Azure VPN Client über die Berechtigung für die Ausführung im Hintergrund verfügt. Führen Sie die folgenden Schritte aus, um die Berechtigung zu überprüfen/zu aktivieren:

1. Wechseln Sie zu „Start“, und wählen Sie dann „Einstellungen“ > „Datenschutz“ > „Hintergrund-Apps“ aus.
2. Stellen Sie sicher, dass unter „Hintergrund-Apps“ die Option **Apps die Ausführung im Hintergrund gestatten** aktiviert ist.
3. Aktivieren Sie unter „Wählen Sie aus, welche Apps im Hintergrund ausgeführt werden dürfen“ die Einstellung für „Azure-VPN-Client“ (**Ein**).

  ![Berechtigung (permission)](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>So erstellen Sie ein zertifikatbasiertes Clientprofil

Bei Verwendung eines zertifikatbasierten Profils müssen die entsprechenden Zertifikate auf dem Clientcomputer installiert sein. Weitere Informationen zu Zertifikaten finden Sie unter [Installieren von Clientzertifikaten](certificates-point-to-site.md).

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>So erstellen Sie ein RADIUS-Clientprofil

  ![Radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Der geheime Serverschlüssel kann in das P2S-VPN-Clientprofil exportiert werden.  Anweisungen zum Exportieren eines Clientprofils finden Sie [hier](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>So exportieren und verteilen Sie ein Clientprofil

Sobald Sie über ein Arbeitsprofil verfügen und es an andere Benutzer verteilen müssen, können Sie es mit den folgenden Schritten exportieren:

1. Markieren Sie das VPN-Clientprofil, das Sie exportieren möchten, wählen Sie **...** und dann **Exportieren** aus.

    ![Screenshot der im Menü ausgewählten Option „Exportieren“.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Wählen Sie den Speicherort aus, an dem Sie dieses Profil speichern möchten, lassen Sie den Dateinamen unverändert, und wählen Sie **Speichern** aus, um die XML-Datei zu speichern.

    ![Der Screenshot zeigt ein Dialogfeld „Speichern unter“, in dem Sie einen Dateinamen eingeben können.](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>So importieren Sie ein Clientprofil

1. Wählen Sie auf der Seite **Importieren** aus.

    ![Screenshot der im Menü ausgewählten Option „Importieren“.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Navigieren Sie zur XML-Datei für das Profil und wählen Sie sie aus. Wählen Sie bei ausgewählter Datei **Öffnen** aus.

    ![Der Screenshot zeigt das Dialogfeld „Öffnen“, in dem Sie eine Datei auswählen können.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Geben Sie den Namen des Profils an und wählen Sie **Speichern** aus.

    ![Der Screenshot zeigt den hinzugefügten Verbindungsnamen und die ausgewählte Schaltfläche „Speichern“ an.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Wählen Sie **Verbinden** aus, um die Verbindung zum VPN herzustellen.

    ![Der Screenshot zeigt die Schaltfläche „Verbinden“ für die für die gerade erstellte Verbindung an.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. Nachdem die Verbindung hergestellt wurde, wird das Symbol grün und **Verbunden** angezeigt.

    ![Der Screenshot zeigt die Verbindung mit dem Status „Verbunden“ mit der Option zum Trennen der Verbindung.](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>So löschen Sie ein Clientprofil

1. Wählen Sie die Auslassungszeichen neben dem Clientprofil aus, das Sie löschen möchten. Wählen Sie dann **Entfernen** aus.

    ![Screenshot der im Menü ausgewählten Option „Entfernen“.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Wählen Sie **Entfernen** aus, um den Löschvorgang durchzuführen.

    ![Der Screenshot zeigt ein Bestätigungsdialogfeld mit der Option zum Entfernen oder Abbrechen.](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Erstellen einer Verbindung

1. Wählen Sie auf der Seite **+** aus und klicken Sie dann auf **+ Hinzufügen**.

    ![Screenshot der im Menü ausgewählten Option „Hinzufügen“.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Füllen Sie die Verbindungsinformationen aus. Wenden Sie sich an Ihren Administrator, wenn Sie die Werte nicht kennen. Wählen Sie nach Eingabe der Werte **Speichern** aus.

    ![Der Screenshot zeigt einen Bereich, in dem Sie die erforderlichen Werte eingeben können.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Wählen Sie **Verbinden** aus, um die Verbindung zum VPN herzustellen.

    ![Der Screenshot zeigt die Schaltfläche „Verbinden“ für Ihre Verbindung an.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Wählen Sie die richtigen Anmeldeinformationen und dann **Weiter** aus.

    ![Der Screenshot zeigt das Dialogfeld „Anmelden“ an.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. Nachdem die Verbindung erfolgreich hergestellt wurde, wird das Symbol grün und **Verbunden** angezeigt.

    ![Der Screenshot zeigt die Verbindung mit dem Status „Verbunden“ an.](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>So stellen Sie die Verbindung automatisch her

Diese Schritte unterstützen Sie beim Konfigurieren der Verbindung, sodass sie automatische hergestellt wird und immer aktiv ist.

1. Wählen Sie auf der Homepage für den VPN-Client **VPN-Einstellungen** aus.

    ![Der Screenshot zeigt VPN-Verbindungen an, bei denen Sie VPN-Einstellungen auswählen können.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Wählen Sie im Dialogfeld zum Wechseln von Apps **Ja** aus.

    ![Der Screenshot zeigt eine Überprüfungsmeldung zum Wechseln von Apps an.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Vergewissern Sie sich, dass die Verbindung, die Sie festlegen möchten, noch nicht hergestellt ist, markieren Sie dann das Profil und aktivieren Sie das Kontrollkästchen **Automatisch verbinden**.

    ![Der Screenshot zeigt ein Einstellungsdialogfeld an, in dem Sie die Option „Automatisch verbinden“ auswählen können.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Wählen Sie **Verbinden** aus, um die VPN-Verbindung zu initiieren.

    ![Der Screenshot zeigt die Schaltfläche „Verbinden“ an.](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnose von Verbindungsproblemen

1. Zum Diagnostizieren von Verbindungsproblemen können Sie das **Diagnose**-Tool verwenden. Wählen Sie die **...** neben der VPN-Verbindung aus, die Sie diagnostizieren möchten, um das Menü einzublenden. Wählen Sie dann **Diagnose** aus.

    ![Screenshot der im Menü ausgewählten Option „Diagnose“.](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. Wählen Sie auf der Seite **Verbindungseigenschaften** die Option **Diagnose ausführen** aus.

    ![Der Screenshot zeigt die Schaltfläche „Diagnose ausführen“ für eine Verbindung an.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Melden Sie sich mit Ihren Anmeldeinformationen an.

    ![Der Screenshot zeigt das Dialogfeld „Anmelden“ für diese Aktion an.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Zeigen Sie die Diagnoseergebnisse an.

    ![Screenshot mit den Ergebnissen der Diagnose.](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Wie füge ich DNS-Suffixe zum VPN-Client hinzu?

Sie können die heruntergeladene XML-Datei für das Profil ändern und die Tags **\<dnssuffixes>\<dnssufix> \</dnssufix>\</dnssuffixes>** hinzufügen.

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Wie füge ich dem VPN-Client benutzerdefinierte DNS-Server hinzu?

Sie können die heruntergeladene XML-Datei für das Profil ändern und die Tags **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** hinzufügen.

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> Der OpenVPN-Azure AD-Client verwendet DNS-Einträge der Richtlinientabelle für die Namensauflösung (Name Resolution Policy Table, NRPT). Dies bedeutet, dass in der Ausgabe von `ipconfig /all` keine DNS-Server aufgeführt werden. Verwenden Sie [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) in PowerShell, um die von Ihnen derzeit verwendeten DNS-Einstellungen zu überprüfen.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Wie füge ich dem VPN-Client benutzerdefinierte Routen hinzu?

Sie können die heruntergeladene XML-Datei für das Profil ändern und die Tags **\<includeroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes>** hinzufügen.

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```
### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-force-tunnel"></a>Wie leite ich den gesamten Netzwerkdatenverkehr an den VPN-Tunnel weiter (Tunnel erzwingen)?

Sie können die heruntergeladene XML-Datei für das Profil ändern und die Tags **\<includeroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes>** hinzufügen.

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>0.0.0.0</destination><mask>1</mask>
        </route>
        <route>
            <destination>128.0.0.0</destination><mask>1</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Wie blockiere ich Routen vom VPN-Client (Ausschließen von Routen)?

Sie können die heruntergeladene XML-Datei für das Profil ändern und die Tags **\<excluderoutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</excluderoutes>** hinzufügen.

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```
### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Kann ich das Profil über eine Eingabeaufforderung importieren?

Sie können das Profil über eine Eingabeaufforderung importieren, indem Sie die heruntergeladene Datei **azurevpnconfig.xml** in den Ordner **%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState** legen und den folgenden Befehl ausführen:

```
azurevpn -i azurevpnconfig.xml 
```
Um den Import zu erzwingen, verwenden Sie auch den Schalter **-f**


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Erstellen eines Azure Active Directory-Mandanten für P2S Open VPN-Verbindungen, die die Azure AD-Authentifizierung verwenden](openvpn-azure-ad-tenant.md).