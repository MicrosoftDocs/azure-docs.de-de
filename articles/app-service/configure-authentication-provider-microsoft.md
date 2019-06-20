---
title: 'Konfigurieren der Authentifizierung über ein Microsoft-Konto: Azure App Service'
description: Erfahren Sie, wie Sie die Microsoft-Kontoauthentifizierung für Ihre App Services-Anwendung konfigurieren.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e3da856efd7d44f15f9de27c9e38375d40dc211d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60850964"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In diesem Thema wird demonstriert, wie Sie Azure App Services zur Verwendung eines Microsoft-Kontos als Authentifizierungsanbieter konfigurieren. 

## <a name="register-microsoft-account"></a>Registrieren Ihrer App mit einem Microsoft-Konto
1. Melden Sie sich beim [Azure-Portal]an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie Ihre **URL**. Sie wird später benötigt, um die App mit einem Microsoft-Konto zu konfigurieren.
2. Navigieren Sie im Microsoft Account Developer Center zur Seite [Eigene Anwendungen] , und melden Sie sich, falls erforderlich, mit Ihrem Microsoft-Konto an.
3. Klicken Sie auf **App hinzufügen**, geben Sie einen Anwendungsnamen ein, und klicken Sie auf **Erstellen**.
4. Notieren Sie sich die **Anwendungs-ID**, da Sie sie später benötigen. 
5. Klicken Sie unter „Plattformen“ auf **Plattform hinzufügen** , und wählen Sie „Web“ aus.
6. Geben Sie unter „Umleitungs-URIs“ den Endpunkt der Anwendung an, und klicken Sie dann auf **Speichern**. 
   
   > [!NOTE]
   > Der Umleitungs-URI ist die URL Ihrer Anwendung mit angefügtem Pfad */.auth/login/microsoftaccount/callback*. Beispiel: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.
   
7. Klicken Sie unter „Anwendungsgeheimnisse“ auf **Neues Kennwort generieren**. Notieren Sie sich den angezeigten Wert. Er wird nicht wieder angezeigt, nachdem Sie die Seite verlassen haben.

    > [!IMPORTANT]
    > Bei dem Kennwort handelt es sich um eine wichtige Information für die Anmeldung. Teilen Sie das Kennwort keiner anderen Person mit, und geben Sie es nicht in einer Clientanwendung weiter.
    
8. Klicken Sie unten auf der Seite auf **Speichern**.

## <a name="secrets"></a>Hinzufügen von Microsoft-Kontoinformationen zu Ihrer App Service-Anwendung
1. Navigieren Sie im [Azure-Portal] zu Ihrer Anwendung, und klicken Sie auf **Einstellungen** > **Authentifizierung/Autorisierung**.
2. Falls das Authentifizierungs-/Autorisierungsfeature nicht aktiviert ist, legen Sie es auf **Ein**fest.
3. Klicken Sie auf **Microsoft-Konto**. Fügen Sie die Werte für die Anwendungs-ID und das Kennwort ein, die Sie zuvor erhalten haben, und aktivieren Sie optional alle Bereiche, die bei Ihrer Anwendung erforderlich sind. Klicken Sie dann auf **OK**.
   
    ![][1]
   
    Standardmäßig erfolgt die Authentifizierung über App Service, wobei jedoch der Zugriff auf die Inhalte Ihrer Website und APIs nicht autorisiert wird. Sie müssen die Benutzer in Ihrem App-Code autorisieren.
4. (Optional) Um den Zugriff auf Ihre Website ausschließlich auf Benutzer zu beschränken, die von Microsoft authentifiziert wurden, legen Sie für **Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Microsoft-Konto** fest. Dadurch müssen alle Anforderungen authentifiziert werden. Alle nicht authentifizierten Anforderungen werden zur Authentifizierung an Microsoft umgeleitet.
5. Klicken Sie auf **Speichern**.

Sie können nun ein Microsoft-Konto für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"></a>Verwandte Inhalte
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Eigene Anwendungen]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-Portal]: https://portal.azure.com/
