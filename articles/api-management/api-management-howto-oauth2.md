---
title: Autorisieren von Entwicklerkonten mithilfe von OAuth 2.0 in API Management
titleSuffix: Azure API Management
description: Erfahren Sie, wie Sie Benutzer mit OAuth 2.0 in API Management autorisieren. OAuth 2.0 sichert die API, sodass Benutzer nur auf Ressourcen zugreifen können, für die sie über Berechtigungen verfügen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/14/2020
ms.author: apimpm
ms.openlocfilehash: fae4e349d46425c0c2b2b923d6a61e2e588708c1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077250"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Autorisieren von Entwicklerkonten mithilfe von OAuth 2.0 in Azure API Management

Viele APIs unterstützen [OAuth 2.0](https://oauth.net/2/) zum Schützen der API und um sicherzustellen, dass nur autorisierte Benutzer Zugriff erhalten und nur auf die Ressourcen zugreifen können, für die sie berechtigt sind. Um die interaktive Entwicklerkonsole von Azure API Management mit solchen APIs zu verwenden, ermöglicht der Dienst Ihnen das Konfigurieren Ihrer Dienstinstanz für die Zusammenarbeit mit einer OAuth 2.0-aktivierten API.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Voraussetzungen

Diese Anleitung beschreibt, wie Sie eine Instanz des API Management-Diensts zur Verwendung der OAuth 2.0-Autorisierung für Entwicklerkonten konfigurieren. Es wird jedoch nicht behandelt, wie Sie einen OAuth 2.0-Anbieter konfigurieren. Die Konfiguration ist je nach OAuth 2.0-Anbieter unterschiedlich, die Schritte sind jedoch ähnlich, und die beim Konfigurieren von OAuth 2.0 in Ihrer Instanz des API Management-Diensts erforderlichen Informationen sind gleich. In diesem Thema werden Beispiele zur Verwendung von Azure Active Directory als OAuth 2.0-Anbieter gezeigt.

> [!NOTE]
> Weitere Informationen zum Konfigurieren von OAuth 2.0 mithilfe von Azure Active Directory finden Sie im Beispiel [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Konfigurieren eines OAuth 2.0-Autorisierungsservers in API Management

> [!NOTE]
> Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen unter [Erstellen einer API Management-Dienstinstanz][Create an API Management service instance].

1. Klicken Sie im Menü auf der linken Seite auf die Registerkarte „OAuth 2.0“ und dann auf **+ Hinzufügen**.

    ![OAuth 2.0-Menü](./media/api-management-howto-oauth2/oauth-01.png)

2. Geben Sie in die Felder **Name** und **Beschreibung** einen Namen bzw. eine optionale Beschreibung ein.

    > [!NOTE]
    > Anhand dieser Felder wird der OAuth 2.0-Autorisierungsserver innerhalb der aktuellen Instanz des API Management-Diensts identifiziert. Die Werte der Felder stammen nicht vom OAuth 2.0-Server.

3. Geben Sie die **Seiten-URL für Clientregistrierung ein**. Auf dieser Seite können Benutzer ihre Konten erstellen und verwalten. Die Seite variiert je nach OAuth 2.0-Anbieter. Die **Seiten-URL für Clientregistrierung** verweist auf die Seite, die Benutzer zum Erstellen und Konfigurieren eigener Konten für OAuth 2.0-Anbieter nutzen können, die eine Benutzerverwaltung für Konten unterstützen. Ein Beispiel ist `https://contoso.com/login`. Einige Organisationen nutzen diese Funktionalität nicht, selbst wenn der OAuth 2.0-Anbieter sie unterstützt. Wenn für Ihren OAuth 2.0-Anbieter keine Benutzerverwaltung für Konten konfiguriert ist, geben Sie hier eine Platzhalter-URL ein, z. B. die URL Ihres Unternehmens oder eine URL wie `https://placeholder.contoso.com`.

    ![Neuer OAuth 2.0-Server](./media/api-management-howto-oauth2/oauth-02.png)

4. Der nächste Abschnitt des Formulars enthält die Einstellungen **Autorisierungsberechtigungstypen** , **Autorisierungsendpunkt-URL** und **Autorisierungsanforderungsmethode**.

    Geben Sie die **Autorisierungsberechtigungstypen** an, indem Sie die gewünschten Typen aktivieren. **Autorisierungscode** wird standardmäßig festgelegt.

    Geben Sie die **Autorisierungsendpunkt-URL** ein. Für Azure Active Directory lautet diese URL ähnlich wie die folgende, wobei `<tenant_id>` durch die Client-ID Ihres Azure AD-Mandanten ersetzt wird.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    Über **Methode für Autorisierungsanforderung** wird festgelegt, wie die Autorisierungsanforderung an den OAuth 2.0-Server gesendet wird. Per Voreinstellung ist **GET** ausgewählt.

5. Anschließend müssen **URL für Tokenendpunkt** , **Methoden für Clientauthentifizierung** , **Sendemethode für Zugriffstoken** und **Standardbereich** angegeben werden.

    ![Screenshot: Bildschirm „OAuth2-Dienst hinzufügen“](./media/api-management-howto-oauth2/oauth-03.png)

    Bei einem Azure Active Directory OAuth 2.0-Server weist **Token-Endpunkt-URL** das folgende Format auf. Hierbei verwendet `<TenantID>` das Format `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Die Standardeinstellung für **Clientauthentifizierungsmethoden** lautet **Basis** , und die **Sendemethode für Zugriffstoken** ist **Autorisierungsheader**. Diese Werte werden zusammen mit **Standardmäßiger Geltungsbereich** in diesem Abschnitt des Formulars festgelegt.

6. Der Abschnitt **Clientanmeldeinformationen** enthält die Werte für **Client-ID** und **Geheimer Clientschlüssel** , die beim Erstellen und Konfigurieren Ihres OAuth 2.0-Servers abgerufen werden. Nachdem die Werte für **Client-ID** und **Geheimer Clientschlüssel** angegeben wurden, wird der **Umleitungs-URI** für den **Autorisierungscode** generiert. Die URI wird zum Konfigurieren der Antwort-URL in Ihrer OAuth 2.0-Serverkonfiguration verwendet.

    Im neuen Entwicklerportal hat das URI-Suffix das folgende Format:

    - `/signin-oauth/code/callback/{authServerName}` für den Flow zur Autorisierungscodegenehmigung
    - `/signin-oauth/implicit/callback` für den Flow zur impliziten Genehmigung

    ![Screenshot: Zeigt, wo die Clientanmeldeinformationen für den neuen OAuth2-Dienst hinzugefügt werden](./media/api-management-howto-oauth2/oauth-04.png)

    Falls für **Autorisierungsberechtigungstypen** die Einstellung **Ressourcenbesitzer-Kennwort** festgelegt ist, werden diese Berechtigungen im Abschnitt **Ressourcenbesitzer-Kennwortberechtigungen** festgelegt. Andernfalls lassen Sie den Abschnitt leer.

    Nachdem Sie das Formular ausgefüllt haben, klicken Sie auf **Erstellen** , um die OAuth 2.0-Autorisierungsserverkonfiguration für API Management zu speichern. Nach dem Speichern der Serverkonfiguration können Sie APIs wie im nächsten Abschnitt beschrieben zur Nutzung konfigurieren.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Konfigurieren einer API zum Verwenden der OAuth 2.0-Benutzerauthentifizierung

1. Klicken Sie im Menü **API Management** auf der linken Seite auf **APIs**.

    ![OAuth 2.0-APIs](./media/api-management-howto-oauth2/oauth-05.png)

2. Klicken Sie auf den Namen der gewünschten API und dann auf **Einstellungen**. Scrollen Sie zum Abschnitt **Sicherheit** , und aktivieren Sie dann das Kontrollkästchen für **OAuth 2.0**.

    ![OAuth 2.0-Einstellungen](./media/api-management-howto-oauth2/oauth-06.png)

3. Wählen Sie in der Dropdownliste den gewünschten **Autorisierungsserver** aus, und klicken Sie dann auf **Speichern**.

    ![Screenshot: Hervorhebung des ausgewählten Autorisierungsservers und der Schaltfläche „Speichern“](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Legacy-Entwicklerportal – Testen der OAuth 2.0-Benutzerautorisierung

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Nachdem Sie Ihren OAuth 2.0-Autorisierungsserver und Ihre API zu dessen Nutzung konfiguriert haben, können Sie ihn testen, indem Sie zum Entwicklerportal wechseln und eine API aufrufen. Klicken Sie auf der Seite **Übersicht** der Azure API Management-Instanz im oberen Menü auf **Developer portal (legacy)** (Entwicklerportal (Legacy)).

Klicken Sie im Hauptmenü auf **APIs** , und wählen Sie **Echo API** aus.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Falls nur eine API konfiguriert oder für Ihr Konto sichtbar ist, können Sie auf APIs klicken, um direkt zu den Operationen für diese API zu gelangen.

Wählen Sie die Operation **GET Resource** aus, klicken Sie auf **Konsole öffnen** , und wählen Sie dann aus der Dropdownliste **Autorisierungscode** aus.

![Konsole öffnen][api-management-open-console]

Wenn der **Autorisierungscode** ausgewählt wurde, wird ein Popup-Fenster mit dem Anmeldeformular des OAuth 2.0-Anbieters angezeigt. In diesem Beispiel wird das Anmeldeformular von Azure Active Directory bereitgestellt.

> [!NOTE]
> Falls Sie Popup-Fenster deaktiviert haben, werden Sie dazu aufgefordert, sie im Browser zu aktivieren. Wählen Sie danach erneut **Autorisierungscode** aus. Daraufhin wird das Anmeldeformular angezeigt.

![Anmelden][api-management-oauth2-signin]

Nachdem Sie sich angemeldet haben, werden die **Anforderungsheader** mit einem `Authorization : Bearer`-Header ausgefüllt, der die Anforderung autorisiert.

![Token des Anforderungsheaders][api-management-request-header-token]

Nun können Sie die gewünschten Werte für die restlichen Parameter konfigurieren und die Anforderung absenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von OAuth 2.0 und API Management finden Sie im folgenden Video und dem zugehörigen [Artikel](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps
