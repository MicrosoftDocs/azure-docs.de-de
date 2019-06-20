---
title: Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory B2C – Azure API Management | Microsoft-Dokumentation
description: Es wird beschrieben, wie Benutzer mithilfe von Azure Active Directory B2C in API Management autorisiert werden.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 644cc2a4175043b523d53b39f17483c6f3acfe96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64696731"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory B2C in Azure API Management

## <a name="overview"></a>Übersicht

Bei Azure Active Directory B2C handelt es sich um eine Lösung zur Cloudidentitätsverwaltung für kundenorientierte Web- und Mobilanwendungen. Sie können damit den Zugriff auf Ihr Entwicklerportal verwalten. In diesem Leitfaden wird die in Ihrem API Management-Dienst für die Integration von Azure Active Directory B2C erforderliche Konfiguration beschrieben. Informationen zum Aktivieren des Zugriffs auf das Entwicklerportal mithilfe einer klassischen Azure Active Directory-Instanz finden Sie unter [Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory].

> [!NOTE]
> Zum Ausführen der hier genannten Schritte müssen Sie über einen Azure Active Directory B2C-Mandanten verfügen, in dem eine Anwendung erstellt wird. Außerdem müssen Richtlinien für die Registrierung und Anmeldung eingerichtet sein. Weitere Informationen finden Sie unter [Azure Active Directory B2C – Übersicht].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory B2C

1. Um einzusteigen, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und suchen Sie Ihre API Management-Instanz.

   > [!NOTE]
   > Falls Sie noch keine API Management-Dienstinstanz erstellt haben, helfen Ihnen die Informationen im Abschnitt zum [Erstellen einer API Management-Dienstinstanz][Create an API Management service instance] im Tutorial [Erste Schritte mit Azure API Management][Get started with Azure API Management] weiter.

2. Unter **Identitäten**. Klicken Sie oben auf **+Hinzufügen**.

   Auf der rechten Seite wird der Bereich **Identitätsanbieter hinzufügen** geöffnet. Wählen Sie **Azure Active Directory B2C** aus.
    
   ![Hinzufügen von AAD B2C als Identitätsanbieter][api-management-howto-add-b2c-identity-provider]

3. Kopieren Sie die **Umleitungs-URL**.

   ![Umleitungs-URL für den AAD B2C-Identitätsanbieter][api-management-howto-copy-b2c-identity-provider-redirect-url]

4. Greifen Sie auf einer neuen Registerkarte im Azure-Portal auf Ihren Azure Active Directory B2C-Mandanten zu, und öffnen Sie das Blatt **Anwendungen**.

   ![Neue Anwendung registrieren 1][api-management-howto-aad-b2c-portal-menu]

5. Klicken Sie auf die Schaltfläche **Hinzufügen**, um eine neue Azure Active Directory B2C-Anwendung zu erstellen.

   ![Neue Anwendung registrieren 2][api-management-howto-aad-b2c-add-button]

6. Geben Sie auf dem Blatt **Neue Anwendung** einen Namen für die Anwendung ein. Wählen Sie für **Web-App/Web-API** die Antwort **Ja**, und wählen Sie für **Impliziten Fluss** zulassen ebenfalls **Ja**. Fügen Sie die in Schritt 3 kopierte **Umleitungs-URL** in das Textfeld **Antwort-URL** ein.

   ![Neue Anwendung registrieren 3][api-management-howto-aad-b2c-app-details]

7. Klicken Sie auf die Schaltfläche **Erstellen** . Sobald die Anwendung erstellt wurde, wird sie auf dem Blatt **Anwendungen** angezeigt. Klicken Sie auf den Anwendungsnamen, um die Details anzuzeigen.

   ![Neue Anwendung registrieren 4][api-management-howto-aad-b2c-app-created]

8. Kopieren Sie die **Anwendungs-ID** vom Blatt **Eigenschaften** in die Zwischenablage.

   ![Anwendungs-ID 1][api-management-howto-aad-b2c-app-id]

9. Wechseln Sie zum API Management-Bereich **Identitätsanbieter hinzufügen**, und fügen Sie die ID in das Textfeld **Client-ID** ein.
    
10. Wechseln Sie zur B2C-Anwendungsregistrierung zurück, klicken Sie auf die Schaltfläche **Schlüssel** und anschließend auf **Schlüssel generieren**. Klicken Sie auf **Speichern** , um die Konfiguration zu speichern und den **App-Schlüssel** anzuzeigen. Kopieren Sie den Schlüssel in die Zwischenablage.

    ![App-Schlüssel 1][api-management-howto-aad-b2c-app-key]

11. Wechseln Sie zum API Management-Bereich **Identitätsanbieter hinzufügen** zurück, und fügen Sie den Schlüssel in das Textfeld **Geheimer Clientschlüssel** ein.
    
12. Geben Sie in **Anmeldemandant** den Domänennamen des Azure Active Directory B2C-Mandanten an.

13. Über das Feld **Autorität** können Sie steuern, welche Azure AD B2C-Anmelde-URL verwendet wird. Legen Sie den Wert auf **<Name_Ihres_b2c_Mandanten>.b2clogin.com** fest.

14. Geben Sie die **Registrierungsrichtlinie** und die **Anmelderichtlinie** aus den Richtlinien des B2C-Mandanten an. Optional können Sie auch die Richtlinien für die **Profilbearbeitung** und die **Kennwortzurücksetzung** angeben.

15. Klicken Sie auf **Speichern**, nachdem Sie die gewünschte Konfiguration angegeben haben.

    Nach dem Speichern der Änderungen können Entwickler neue Konten erstellen und sich am Entwicklerportal anmelden, indem sie Azure Active Directory B2C verwenden.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Registrieren für ein Entwicklerkonto mithilfe von Azure Active Directory B2C

1. Zur Registrierung für ein Entwicklerkonto mithilfe von Azure Active Directory B2C öffnen Sie ein neues Browserfenster und wechseln zum Entwicklerportal. Klicken Sie auf die Schaltfläche **Registrieren**.

   ![Entwicklerportal 1][api-management-howto-aad-b2c-dev-portal]

2. Wählen Sie die Option zum Registrieren bei **Azure Active Directory B2C**.

   ![Entwicklerportal 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Sie werden zu der Registrierungsrichtlinie weitergeleitet, die Sie im vorherigen Abschnitt konfiguriert haben. Wählen Sie die Registrierung über Ihre E-Mail-Adresse oder eines Ihrer vorhandenen Social Media-Konten.

   > [!NOTE]
   > Wenn Azure Active Directory B2C als einzige Option im Herausgeberportal auf der Registerkarte **Identitäten** aktiviert ist, werden Sie direkt zur Registrierungsrichtlinie umgeleitet.

   ![Entwicklerportal][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Wenn die Registrierung abgeschlossen ist, werden Sie zurück zum Entwicklerportal geleitet. Sie sind jetzt am Entwicklerportal für Ihre API Management-Dienstinstanz angemeldet.

    ![Registrierung abgeschlossen][api-management-registration-complete]

## <a name="next-steps"></a>Nächste Schritte

*  [Azure Active Directory B2C – Übersicht]
*  [Azure Active Directory B2C: Erweiterbares Richtlinienframework]
*  [Verwenden eines Microsoft-Kontos als Identitätsanbieter in Azure Active Directory B2C]
*  [Verwenden eines Google-Kontos als Identitätsanbieter in Azure Active Directory B2C]
*  [Verwenden eines LinkedIn-Kontos als Identitätsanbieter in Azure Active Directory B2C]
*  [Verwenden eines Facebook-Kontos als Identitätsanbieter in Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Azure Active Directory B2C – Übersicht]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: Erweiterbares Richtlinienframework]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Verwenden eines Microsoft-Kontos als Identitätsanbieter in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Verwenden eines Google-Kontos als Identitätsanbieter in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Verwenden eines Facebook-Kontos als Identitätsanbieter in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Verwenden eines LinkedIn-Kontos als Identitätsanbieter in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
