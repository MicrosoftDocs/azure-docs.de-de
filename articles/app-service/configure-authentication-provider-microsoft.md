---
title: Konfigurieren der Microsoft-Authentifizierung
description: Erfahren Sie, wie Sie die Authentifizierung mit einem Microsoft-Konto als Identitätsanbieter für Ihre App Services- oder Azure Functions-App konfigurieren.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 9ec9c102680496407106a3bf9b7683890c7a63ee
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043238"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Konfigurieren Ihrer App Service- oder Azure Functions-App zur Verwendung der Microsoft-Kontoanmeldung

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In diesem Thema wird gezeigt, wie Sie Azure App Service oder Azure Functions für die Verwendung von AAD konfigurieren, um persönliche Microsoft-Kontoanmeldungen zu unterstützen.

> [!NOTE]
> Sowohl persönliche Microsoft-Konten als auch Organisationskonten verwenden den AAD-Identitätsanbieter. Zurzeit ist es nicht möglich, diesen Identitätsanbieter so zu konfigurieren, dass beide Arten von Anmeldungen unterstützt werden.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registrieren Ihrer App bei einem Microsoft-Konto

1. Wechseln Sie im Azure-Portal zu [**App-Registrierungen**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade). Falls erforderlich, melden Sie sich mit Ihrem Microsoft-Konto an.
1. Wählen Sie **Neue Registrierung** aus, und geben Sie dann einen Anwendungsnamen ein.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Accounts in any organizational directory (Any Azure AD directory - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)** (Konten in allen Organisationsverzeichnissen (beliebiges Azure AD-Verzeichnis: mehrere Mandanten) und persönliche Microsoft-Konten (z. B. Skype, Xbox)) aus.
1. Wählen Sie in **Umleitungs-URIs** die Option **Web** aus, und geben Sie dann `https://<app-domain-name>/.auth/login/aad/callback` ein. Ersetzen Sie *\<app-domain-name>* durch den Domänennamen Ihrer App.  Beispiel: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Stellen Sie sicher, dass Sie HTTPS-Schema in der URL verwenden.

1. Wählen Sie **Registrieren**.
1. Kopieren Sie die **Anwendungs-ID (Client-ID)** . Sie benötigen die Information später.
1. Wählen Sie im linken Bereich **Zertifikate und Geheimnisse** > **Neuer Client** aus. Geben Sie eine Beschreibung ein, wählen Sie die Gültigkeitsdauer und dann **Hinzufügen** aus.
1. Kopieren Sie den Wert, der auf der Seite **Zertifikate und Geheimnisse** angezeigt wird. Er wird nicht wieder angezeigt, nachdem Sie die Seite verlassen haben.

    > [!IMPORTANT]
    > Der geheime Clientschlüssel (Kennwort) ist eine wichtige Anmeldeinformation. Teilen Sie das Kennwort keiner anderen Person mit, und geben Sie es nicht in einer Clientanwendung weiter.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Hinzufügen von Microsoft-Kontoinformationen zu Ihrer App Service-Anwendung

1. Wechseln Sie im [Azure portal] zu Ihrer Anwendung.
1. Wählen Sie **Einstellungen** > **Authentifizierung/Autorisierung,** aus, und stellen Sie sicher, dass für die **App Service-Authentifizierung** die Option **Ein** festgelegt ist.
1. Wählen Sie unter **Authentifizierungsanbieter** die Option **Azure Active Directory** aus. Wählen Sie unter **Verwaltungsmodus** die Option **Erweitert** aus. Fügen Sie die Anwendungs(Client)-ID und den geheimen Clientschlüssel ein, die Sie zuvor erhalten haben. Verwenden Sie **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** für das Feld **Aussteller-URL**.
1. Klicken Sie auf **OK**.

   App Service bietet Authentifizierung, schränkt aber den autorisierten Zugriff auf den Inhalt und die APIs Ihrer Website nicht ein. Sie müssen die Benutzer in Ihrem App-Code autorisieren.

1. (Optional) Um den Zugriff auf Microsoft-Kontobenutzer zu beschränken, legen Sie **Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist** auf **Mit Azure Active Directory anmelden** fest. Wenn Sie diese Funktion festlegen, erfordert Ihre App, dass alle Anforderungen authentifiziert werden. Sie leitet außerdem alle nicht authentifizierten Anforderungen zur Verwendung von AAD für die Authentifizierung um. Beachten Sie Folgendes: Da Sie Ihre **Aussteller-URL** für die Verwendung des Microsoft-Kontomandanten konfiguriert haben, werden nur persönliche Konten erfolgreich authentifiziert.

   > [!CAUTION]
   > Das Einschränken des Zugriffs auf diese Weise gilt für alle Aufrufe Ihrer App, was für Apps, die eine öffentlich verfügbare Startseite haben, eventuell nicht wünschenswert ist, wie bei vielen Single-Page-Anwendungen. Bei solchen Anwendungen ist möglicherweise die Einstellung **Anonyme Anforderungen zulassen (keine Aktion)** vorzuziehen, sodass die App die Authentifizierung selbst manuell startet. Weitere Informationen finden Sie unter [Authentifizierungsflow](overview-authentication-authorization.md#authentication-flow).

1. Wählen Sie **Speichern** aus.

Sie können nun ein Microsoft-Konto für die Authentifizierung in Ihrer App verwenden.

## <a name="next-steps"></a><a name="related-content"> </a>Nächste Schritte

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portal]: https://portal.azure.com/
