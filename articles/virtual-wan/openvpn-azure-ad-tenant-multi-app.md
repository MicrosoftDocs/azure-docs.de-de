---
title: 'Virtual WAN: Azure AD-Mandant für verschiedene Benutzergruppen: Azure AD-Authentifizierung'
description: Richten Sie einen Azure AD-Mandanten für die P2S-OpenVPN-Authentifizierung ein, und erstellen und registrieren Sie mehrere Apps in Azure AD, um verschiedenen Benutzern und Gruppen unterschiedliche Zugriffsrechte zu erteilen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: 8fcc79991918aecfc26933f2ef5b6e80ea7fa88c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043433"
---
# <a name="create-an-azure-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Erstellen eines Azure Active Directory (AD)-Mandanten für Verbindungen mit dem P2S OpenVPN-Protokoll

Beim Herstellen einer Verbindung mit Ihrem VNet können Sie die zertifikatbasierte Authentifizierung oder die RADIUS-Authentifizierung verwenden. Wenn Sie das Open VPN-Protokoll verwenden, können Sie jedoch auch die Azure Active Directory-Authentifizierung verwenden. Wenn Sie möchten, dass sich verschiedene Benutzergruppen mit verschiedenen Gateways verbinden können, können Sie mehrere Anwendungen in AD registrieren und sie mit verschiedenen Gateways verknüpfen.

Dieser Artikel unterstützt Sie bei der Einrichtung eines Azure AD-Mandanten für die P2S-OpenVPN-Authentifizierung und bei der Erstellung und Registrierung mehrerer Anwendungen in Azure AD, um verschiedenen Benutzern und Gruppen unterschiedliche Zugriffsmöglichkeiten zu geben.

> [!NOTE]
> Die Azure AD-Authentifizierung wird nur für Verbindungen mit dem OpenVPN&reg;-Protokoll unterstützt.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. Erstellen einer neuen P2S-Konfiguration

Eine P2S-Konfiguration definiert die Parameter für das Herstellen der Verbindung mit Remoteclients.

1. Legen Sie die folgenden Variablen fest, und ersetzen Sie dabei die Werte nach Bedarf für Ihre Umgebung.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Führen Sie die folgenden Befehle aus, um die Konfiguration zu erstellen:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Verwenden Sie in den Befehlen oben nicht die Anwendungs-ID des Azure VPN-Clients: Sie erteilt allen Benutzern Zugriff auf das Gateway. Verwenden Sie die ID der Anwendung(en), die Sie registriert haben.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Bearbeiten der Hub-Zuweisung

1. Navigieren Sie unter dem virtuellen WAN zum Blatt **Hubs**.

2. Wählen Sie den Hub aus, dem Sie die VPN-Serverkonfiguration zuordnen möchten, und klicken Sie auf die Auslassungspunkte (...).

    ![Der Screenshot zeigt die im Menü ausgewählte Option zum Bearbeiten eines virtuellen Hubs.](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Klicken Sie auf **Virtuellen Hub bearbeiten**.

4. Aktivieren Sie das Kontrollkästchen **Point-to-Site-Gateway einschließen** , und wählen Sie die gewünschte **Gatewayskalierungseinheit** aus.

    ![Der Screenshot zeigt das Dialogfeld „Virtuellen Hub bearbeiten“, in dem Sie Ihre Gatewayskalierungseinheit auswählen können.](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Geben Sie den **Adresspool** ein, aus dem den VPN-Clients IP-Adressen zugewiesen werden.

6. Klicken Sie auf **Confirm** (Bestätigen).

7. Dieser Vorgang kann bis zu 30 Minuten dauern.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. Herunterladen des VPN-Profils

Verwenden Sie das VPN-Profil, um Ihre Clients zu konfigurieren.

1. Klicken Sie auf der Seite für Ihr virtuelles WAN auf **Benutzer-VPN-Konfigurationen**.

2. Klicken Sie oben auf der Seite auf **Benutzer-VPN-Konfiguration herunterladen**.

3. Nachdem die Erstellung der Datei abgeschlossen wurde, können Sie auf den Link klicken, um sie herunterzuladen.

4. Verwenden Sie die Profildatei, um die VPN-Clients zu konfigurieren.

5. Extrahieren Sie die heruntergeladene ZIP-Datei.

6. Navigieren Sie zum entzippten Ordner „AzureVPN“.

7. Notieren Sie sich den Speicherort der Datei „azurevpnconfig.xml“. Die Datei „azurevpnconfig.xml“ enthält die Einstellung für die VPN-Verbindung und kann direkt in die Azure VPN Client-Anwendung importiert werden. Sie können diese Datei auch per e-Mail oder anderweitig an alle Benutzer verteilen, die eine Verbindung herstellen müssen. Der Benutzer benötigt gültige Azure AD-Anmeldeinformationen, um eine Verbindung herstellen zu können.

## <a name="9-configure-user-vpn-clients"></a>9. Konfigurieren von Benutzer-VPN-Clients

Für die Verbindungsherstellung muss auf jedem Computer, von dem eine Verbindung mit dem VNET hergestellt werden soll, Azure VPN Client heruntergeladen und das in den vorherigen Schritten heruntergeladene VPN-Clientprofil importiert werden.

> [!NOTE]
> Die Azure AD-Authentifizierung wird nur für Verbindungen mit dem OpenVPN&reg;-Protokoll unterstützt.
>

#### <a name="to-download-the-azure-vpn-client"></a>So laden Sie Azure VPN Client herunter

Verwenden Sie diesen [Link](https://go.microsoft.com/fwlink/?linkid=2117554), um Azure VPN Client herunterzuladen.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>So importieren Sie ein Clientprofil

1. Wählen Sie auf der Seite **Importieren** aus.

    ![Screenshot der im Menü ausgewählten Option „Importieren“.](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Navigieren Sie zur XML-Datei für das Profil und wählen Sie sie aus. Wählen Sie bei ausgewählter Datei **Öffnen** aus.

    ![Der Screenshot zeigt das Dialogfeld „Öffnen“, in dem Sie eine Datei auswählen können.](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Geben Sie den Namen des Profils an und wählen Sie **Speichern** aus.

    ![Der Screenshot zeigt den hinzugefügten Verbindungsnamen und die ausgewählte Schaltfläche „Speichern“ an.](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Wählen Sie **Verbinden** aus, um die Verbindung zum VPN herzustellen.

    ![Der Screenshot zeigt die Schaltfläche „Verbinden“ für die für die gerade erstellte Verbindung an.](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Nachdem die Verbindung hergestellt wurde, wird das Symbol grün und **Verbunden** angezeigt.

    ![Der Screenshot zeigt die Verbindung mit dem Status „Verbunden“ mit der Option zum Trennen der Verbindung.](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>So löschen Sie ein Clientprofil

1. Wählen Sie neben dem Clientprofil, das Sie löschen möchten, die Auslassungspunkte (...) aus. Wählen Sie dann **Entfernen** aus.

    ![Screenshot der im Menü ausgewählten Option „Entfernen“.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Wählen Sie **Entfernen** aus, um den Löschvorgang durchzuführen.

    ![Der Screenshot zeigt ein Bestätigungsdialogfeld mit der Option zum Entfernen oder Abbrechen.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>So diagnostizieren Sie Verbindungsprobleme

1. Zum Diagnostizieren von Verbindungsproblemen können Sie das **Diagnose** -Tool verwenden. Wählen Sie neben der VPN-Verbindung, die Sie diagnostizieren möchten, die Auslassungspunkte (...) aus, um das Menü einzublenden. Wählen Sie dann **Diagnose** aus.

    ![Screenshot der im Menü ausgewählten Option „Diagnose“.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Wählen Sie auf der Seite **Verbindungseigenschaften** die Option **Diagnose ausführen** aus.

    ![Der Screenshot zeigt die Schaltfläche „Diagnose ausführen“ für eine Verbindung an.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Melden Sie sich mit Ihren Anmeldeinformationen an.

    ![Der Screenshot zeigt das Dialogfeld „Anmelden“ für diese Aktion an.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Zeigen Sie die Diagnoseergebnisse an.

    ![Screenshot mit den Ergebnissen der Diagnose.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Anzeigen Ihrer Virtual WAN-Instanz

1. Navigieren Sie zum virtuellen WAN.

2. Auf der Seite „Übersicht“ steht jeder Punkt auf der Karte für einen Hub.

3. Im Abschnitt mit den Hubs und Verbindungen können Sie den Hubstatus, die Site, die Region, den VPN-Verbindungsstatus und die Anzahl von ein- und ausgehenden Byte anzeigen.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Bereinigen von Ressourcen

Wenn Sie diese Ressourcen nicht mehr benötigen, können Sie den Befehl [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) verwenden, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu entfernen. Ersetzen Sie „myResourceGroup“ durch den Namen Ihrer Ressourcengruppe, und führen Sie den folgenden PowerShell-Befehl aus:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN finden Sie auf der Seite mit der [Übersicht über Virtual WAN](virtual-wan-about.md).
