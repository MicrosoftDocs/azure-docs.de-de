---
title: 'VPN Gateway: Azure AD-Mandant für P2S-VPN-Verbindungen: Azure AD-Authentifizierung'
description: Erfahren Sie, wie Sie einen Azure AD-Mandanten für die P2S Open VPN-Authentifizierung einrichten.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 3055c9dd1294af81c6c52603dd60bb5aa6075abd
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92777854"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Erstellen eines Azure Active Directory-Mandanten für Verbindungen mit dem P2S OpenVPN-Protokoll

Beim Herstellen einer Verbindung mit Ihrem VNet können Sie die zertifikatbasierte Authentifizierung oder die RADIUS-Authentifizierung verwenden. Wenn Sie das Open VPN-Protokoll verwenden, können Sie jedoch auch die Azure Active Directory-Authentifizierung verwenden. Dieser Artikel unterstützt Sie beim Einrichten eines Azure AD-Mandanten für die P2S Open VPN-Authentifizierung.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Überprüfen des Azure AD-Mandanten

Stellen Sie sicher, dass Sie über einen Azure AD-Mandanten verfügen. Wenn Sie über keinen Azure AD Mandanten verfügen, können Sie mithilfe der Schritte im Artikel [Erstellen eines neuen Mandanten](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) einen neuen Mandanten erstellen:

* Organisationsname
* Name der Anfangsdomäne

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="Neuer Azure AD-Mandant" border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Erstellen von Azure AD-Mandantenbenutzern

Ihr Azure AD Mandant benötigt die folgenden Konten: ein globales Administratorkonto und ein Masterbenutzerkonto. Das Masterbenutzerkonto wird als Masterkonto für die Einbettung verwendet (Dienstkonto). Wenn Sie ein Azure AD-Mandantenbenutzerkonto erstellen, passen Sie die Verzeichnisrolle für den Typ des Benutzers an, den Sie erstellen möchten.

Führen Sie die Schritte unter [Hinzufügen oder Löschen von Benutzern: Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md) aus, um mindestens zwei Benutzer für Ihren Azure AD-Mandanten zu erstellen. Achten Sie darauf, dass Sie die **Verzeichnisrolle** ändern, um die Kontotypen zu erstellen:

* Globaler Administrator
* Benutzer

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Aktivieren der Azure AD-Authentifizierung im Aktivieren der Azure AD-Authentifizierung im VPN-Gateway

1. Suchen Sie die Verzeichnis-ID des Verzeichnisses, das Sie für die Authentifizierung verwenden möchten. Sie wird im Abschnitt „Eigenschaften“ der Active Directory-Seite aufgeführt.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="Screenshot der Verzeichniseigenschaften" lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. Kopieren Sie die Verzeichnis-ID.

1. Melden Sie sich beim Azure-Portal als Benutzer an, dem die Rolle als **globaler Administrator** zugewiesen ist.

1. Geben Sie als nächstes Administratoreinwilligung. Kopieren Sie die URL für den jeweiligen Bereitstellungsspeicherort und fügen Sie sie in die Adressleiste Ihres Browsers ein:

   Öffentlich

   ```
   https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
   ````

   Azure Government

   ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
   ````

   Microsoft Cloud Deutschland

   ```
   https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
   ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

   > [!NOTE]
   > Wenn Sie zur Bereitstellung der Einwilligung ein globales Administratorkonto verwenden, das nicht nativ zum Azure AD-Mandanten gehört, ersetzen Sie „common“ durch die Azure AD-Verzeichnis-ID in der URL. Möglicherweise müssen Sie „common“ auch in bestimmten anderen Fällen durch Ihre Verzeichnis-ID ersetzen.
   >

1. Wählen Sie das Konto **Globaler Administrator** aus, wenn Sie dazu aufgefordert werden.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="Konto auswählen" border="false":::
1. Wählen Sie **Akzeptieren** aus, wenn Sie dazu aufgefordert werden.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="Screenshot mit der Meldung „Berechtigungen angefordert: Akzeptieren“ für Ihre Organisation mit Details und der Option zum Akzeptieren." border="false":::
1. Unter ihrem Azure AD wird in **Unternehmensanwendungen** **Azure-VPN** aufgeführt.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="Screenshot der Seite „Alle Anwendungen“" lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. Wenn Sie noch keine funktionsfähige Point-to-Site-Umgebung haben, folgen Sie der Anweisung zu deren Erstellung. Informationen zum Erstellen und Konfigurieren eines Point-to-Site-VPN-Gateways finden Sie unter [Erstellen eines Point-to-Site-VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

    > [!IMPORTANT]
    > Die Basic-SKU wird für OpenVPN nicht unterstützt.

1. Aktivieren Sie Azure AD-Authentifizierung auf dem VPN-Gateway, indem Sie zu **Punkt-zu-Standort-Konfiguration** navigieren und **OpenVPN (SSL)** als **Tunneltyp** auswählen. Wählen Sie **Azure Active Directory** als **Authentifizierungstyp** aus, und geben Sie dann die entsprechenden Informationen im Abschnitt **Azure Active Directory** ein.

   * **Tenant:** TenantID für den Azure AD-Mandanten ```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **Audience:** ApplicationID der Azure AD-Unternehmens-App „Azure VPN“ ```{AppID of the "Azure VPN" AD Enterprise app}```

   * **Aussteller:** URL des Sicherheitstokendiensts ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="SAzure VPN" border="false":::

   > [!NOTE]
   > Achten Sie darauf, den nachgestellten Schrägstrich am Ende des `AadIssuerUri`-Werts anzugeben. Andernfalls kann es sein, dass die Verbindung fehlschlägt.
   >

1. Erstellen Sie das Profil, indem Sie es durch Klicken auf den Link **VPN-Client herunterladen** herunterladen.

1. Extrahieren Sie die heruntergeladene ZIP-Datei.

1. Navigieren Sie zum entzippten Ordner „AzureVPN“.

1. Notieren Sie sich den Speicherort der Datei „azurevpnconfig.xml“. Die Datei „azurevpnconfig.xml“ enthält die Einstellung für die VPN-Verbindung und kann direkt in die Azure VPN Client-Anwendung importiert werden. Sie können diese Datei auch per e-Mail oder anderweitig an alle Benutzer verteilen, die eine Verbindung herstellen müssen. Der Benutzer benötigt gültige Azure AD-Anmeldeinformationen, um eine Verbindung herstellen zu können.

## <a name="next-steps"></a>Nächste Schritte

Um eine Verbindung mit Ihrem virtuellen Netzwerk herzustellen, müssen Sie ein VPN-Clientprofil erstellen und konfigurieren. Entsprechende Informationen finden Sie unter [Konfigurieren eines VPN-Clients für P2S-VPN-Verbindungen](openvpn-azure-ad-client.md).
