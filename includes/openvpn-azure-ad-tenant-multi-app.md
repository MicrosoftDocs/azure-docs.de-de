---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/15/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3236f9c60cb359349d96e93f674c3e278e44f1e
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376001"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Erstellen des Azure AD-Mandanten

Erstellen Sie mit den im Artikel [Erstellen eines neuen Mandanten](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) beschriebenen Schritten einen Azure AD-Mandanten:

* Organisationsname
* Name der Anfangsdomäne

  Beispiel:

   ![Neuer Azure AD-Mandant](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Erstellen von Mandantenbenutzern

In diesem Schritt erstellen Sie zwei Azure AD-Mandantenbenutzer: Ein globales Administratorkonto und ein Masterbenutzerkonto. Das Masterbenutzerkonto wird als Masterkonto für die Einbettung verwendet (Dienstkonto). Wenn Sie ein Azure AD-Mandantenbenutzerkonto erstellen, passen Sie die Verzeichnisrolle für den Typ des Benutzers an, den Sie erstellen möchten. Verwenden Sie die Schritte in [diesem Artikel](../articles/active-directory/fundamentals/add-users-azure-active-directory.md), um mindestens zwei Benutzer für Ihren Azure AD-Mandanten zu erstellen. Achten Sie darauf, dass Sie die **Verzeichnisrolle** ändern, um die Kontotypen zu erstellen:

* Globaler Administrator
* Benutzer

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Registrieren des VPN-Clients

Registrieren des VPN-Client im Azure AD-Mandanten.

1. Suchen Sie die Verzeichnis-ID des Verzeichnisses, das Sie für die Authentifizierung verwenden möchten. Sie wird im Abschnitt „Eigenschaften“ der Active Directory-Seite aufgeführt.

    ![Verzeichnis-ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Kopieren Sie die Verzeichnis-ID.

3. Melden Sie sich beim Azure-Portal als Benutzer an, dem die Rolle als **globaler Administrator** zugewiesen ist.

4. Geben Sie als nächstes Administratoreinwilligung. Kopieren Sie die URL für den jeweiligen Bereitstellungsspeicherort und fügen Sie sie in die Adressleiste Ihres Browsers ein:

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

> [!NOTE]
> Wenn Sie zur Bereitstellung der Einwilligung ein globales Administratorkonto verwenden, das nicht nativ zum Azure AD-Mandanten gehört, ersetzen Sie „common“ durch die Azure AD-Verzeichnis-ID in der URL. Möglicherweise müssen Sie „common“ auch in bestimmten anderen Fällen durch Ihre Verzeichnis-ID ersetzen.
>

5. Wählen Sie das Konto **Globaler Administrator** aus, wenn Sie dazu aufgefordert werden.

    ![Verzeichnis-ID 2](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Wählen Sie **Akzeptieren** aus, wenn Sie dazu aufgefordert werden.

    ![Der Screenshot zeigt ein Fenster mit der Meldung, dass Berechtigungen angefordert wurden. Zudem wird „Akzeptieren“ für Ihre Organisation sowie Informationen zur Anforderung angezeigt.](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Unter ihrem Azure AD wird in **Unternehmensanwendungen** **Azure-VPN** aufgeführt.

     ![Azure-VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Registrieren zusätzlicher Anwendungen

In diesem Schritt registrieren Sie zusätzliche Anwendungen für verschiedene Benutzer und Gruppen.

1. Klicken Sie unter Ihrem Azure Active Directory auf die Option **App-Registrierungen** und dann auf **+ Neue Registrierung**.

    ![Azure-VPN 2](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Geben Sie auf der Seite **Registrieren einer Anwendung** den **Namen** ein. Wählen Sie unter **Unterstützte Kontotypen** den gewünschten aus, und klicken Sie dann auf **Registrieren**.

    ![Azure-VPN 3](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Nachdem die neue Anwendung registriert wurde, klicken Sie unter dem Blatt „Anwendung“ auf **Eine API verfügbar machen**.

4. Klicken Sie auf **+ Bereich hinzufügen**.

5. Belassen Sie den Standardwert für **Anwendungs-ID-URI**. Klicken Sie auf **Speichern und fortfahren**.

    ![Azure-VPN 4](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Füllen Sie die Pflichtfelder aus, und stellen Sie sicher, dass **Zustand** auf **Aktiviert** gesetzt ist. Klicken Sie auf **Bereich hinzufügen**.

    ![Azure-VPN 5](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Klicken Sie auf **Eine API verfügbar machen** und dann auf **+ Eine Clientanwendung hinzufügen**.  Geben Sie für **Client-ID** abhängig von der Cloud die folgenden Werte ein:

    - Geben Sie **41b23e61-6c1e-4545-b367-cd054e0ed4b4** für Azure **Public** ein.
    - Geben Sie **51bb15d4-3a4f-4ebf-9dca-40096fe32426** für Azure **Government** ein.
    - Geben Sie **538ee9e6-310a-468d-afef-ea97365856a9** für Azure **Germany** ein.
    - Geben Sie **49f817b6-84ae-4cc0-928c-73f27289b3aa** für Azure **China 21Vianet** ein.

8. Klicken Sie auf **Anwendung hinzufügen**.

    ![Azure-VPN 6](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Kopieren Sie auf der Seite **Übersicht** die **Anwendungs-ID (Client)** . Sie benötigen diese Informationen zum Konfigurieren Ihres/Ihrer VPN-Gateway(s).

    ![Azure-VPN 7](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Wiederholen Sie die Schritte in diesem Abschnitt [Registrieren zusätzlicher Anwendungen](#register-apps), um so viele Anwendungen zu erstellen, wie für Ihre Sicherheitsanforderung erforderlich sind. Jede Anwendung wird einem VPN-Gateway zugeordnet und kann über eine jeweils andere Benutzergruppe verfügen. Einem Gateway kann nur eine Anwendung zugeordnet werden.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Zuweisen von Benutzern zu Anwendungen

Zuweisen der Benutzer zu Ihren Anwendungen.

1. Wählen Sie unter **Azure AD > Unternehmensanwendungen** die neu registrierte Anwendung aus, und klicken Sie auf **Eigenschaften**. Stellen Sie sicher, dass **Benutzerzuweisung erforderlich?** auf **Ja** festgelegt ist. Klicken Sie auf **Speichern**.

    ![Azure-VPN 8](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Klicken Sie auf der Anwendungsseite auf **Benutzer und Gruppen** und dann auf **Benutzer hinzufügen**.

    ![Azure-VPN 9](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Klicken Sie unter **Zuweisung hinzufügen** auf die Option **Benutzer und Gruppen**. Wählen Sie die Benutzer aus, die auf diese VPN-Anwendung zugreifen können sollen. Klicken Sie auf **Auswählen**.

    ![Azure-VPN 10](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
