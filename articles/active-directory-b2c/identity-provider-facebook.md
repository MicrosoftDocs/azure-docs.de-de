---
title: Einrichten der Registrierung und Anmeldung mit einem Facebook-Konto
titleSuffix: Azure AD B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Facebook-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e0e71bc0e3a81b5ab2f455224ed2ed4281532d55
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98952673"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Facebook-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Erstellen einer Facebook-Anwendung

Wenn Sie die Anmeldung für Benutzer mit einem Facebook-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie eine Anwendung im [Facebook-App-Dashboard](https://developers.facebook.com/) erstellen. Weitere Informationen finden Sie unter [App-Entwicklung](https://developers.facebook.com/docs/development). Wenn Sie noch über kein Facebook-Konto verfügen, können Sie sich unter [https://www.facebook.com/](https://www.facebook.com/) registrieren.

1. Melden Sie sich auf der [Facebook-Entwickler-Website](https://developers.facebook.com/) mit den Anmeldeinformationen für Ihr Facebook-Konto an.
1. Wenn Sie dies noch nicht getan haben, müssen Sie sich als Facebook-Entwickler registrieren. Dazu wählen Sie oben rechts auf der Seite die Option **Get Started** (Erste Schritte) aus, akzeptieren die Facebook-Richtlinien und führen die Registrierungsschritte aus.
1. Wählen Sie **My Apps** (Meine Apps) und dann **Create App** (App erstellen) aus.
1. Wählen Sie **Build Connected Experiences** (Verbundene Umgebungen erstellen) aus.
1. Geben Sie unter **Display Name** einen Anzeigenamen und unter **Contact Email** (Kontakt-E-Mail) eine gültige E-Mail-Adresse ein.
1. Wählen Sie **App-ID erstellen** aus. Hierzu müssen Sie möglicherweise die Richtlinien für die Facebook-Plattform akzeptieren und eine Online-Sicherheitsüberprüfung durchführen.
1. Wählen Sie **Settings** > **Basic** (Einstellungen > Allgemeines) aus.
    1. Wählen Sie eine **Kategorie**, z.B. `Business and Pages`. Dieser Wert ist für Facebook erforderlich, wird aber nicht für Azure AD B2C verwendet.
    1. Geben Sie eine URL für **Terms of Service URL** (URL der Nutzungsbedingungen) ein, z. B. `http://www.contoso.com/tos`. Die Richtlinien-URL ist eine von Ihnen verwaltete Seite, die Nutzungsbedingungen Ihrer Anwendung enthält.
    1. Geben Sie unter **Privacy Policy URL** (Datenrichtlinien-URL) eine URL ein, z.B. `http://www.contoso.com/privacy`. Die Richtlinien-URL ist eine von Ihnen verwaltete Seite, die Datenschutzinformationen zu Ihrer Anwendung enthält.
1. Wählen Sie unten auf der Seite die Option **Add Platform** (Plattform hinzufügen) und dann **Website** aus.
1. Geben Sie im Feld **Website-URL** die Adresse Ihrer Website (z. B. `https://contoso.com`) ein. 
1. Klicken Sie auf **Save changes** (Änderungen speichern).
1. Kopieren Sie im oberen Bereich der Seite den Wert von **App ID** (App-ID).
1. Wählen Sie **Show** (Anzeigen) aus, und kopieren Sie den Wert unter **App Secret** (App-Geheimnis). Sie benötigen beide Angaben, um Facebook als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. **App Secret** handelt es sich um eine wichtige Sicherheitsinformation für die Anmeldung.
1. Wählen Sie im Menü das **Pluszeichen** neben **PRODUCTS** (Produkte) aus. Wählen Sie unter **Add Products to Your App** (Produkte Ihrer App hinzufügen) unter **Facebook Login** (Facebook-Anmeldung) **Set up** (Einrichten) aus.
1. Wählen Sie im Menü **Facebook Login** (Facebook-Anmeldung) und dann **Settings** (Einstellungen) aus.
1. Geben Sie `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` unter **Valid OAuth redirect URIs** (Gültige OAuth-Umleitungs-URIs) ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten. Wählen Sie unten auf der Seite die Option **Save Changes** (Änderungen speichern) aus.
1. Klicken Sie rechts oben auf der Seite auf den Statusselektor, und legen Sie ihn auf **On** (Ein) fest, um die Anwendung öffentlich und Ihre Facebook-Anwendung für Azure AD B2C verfügbar zu machen. Wählen Sie anschließend **Switch Mode** (Modus wechseln) aus.  An diesem Punkt sollte sich der Status von **Entwicklung** in **Live** ändern.

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>Konfigurieren von Facebook als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Identitätsanbieter** und dann **Facebook** aus.
1. Geben Sie einen **Namen** ein. Beispiel: *Facebook*.
1. Geben Sie für die **Client-ID** die App-ID der Facebook-Anwendung ein, die Sie zuvor erstellt haben.
1. Geben Sie das zuvor notierte App-Geheimnis als **Geheimer Clientschlüssel** ein.
1. Wählen Sie **Speichern** aus.

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Hinzufügen von Facebook als Identitätsanbieter zu einem Benutzerflow 

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie Facebook als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **Facebook** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen das zuvor notierte App-Geheimnis in Ihrem Azure AD B2C-Mandanten speichern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Mandanten enthält.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
5. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
6. Klicken Sie unter **Optionen** auf `Manual`.
7. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `FacebookSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
8. Geben Sie im Feld **Geheimnis** das zuvor notierte App-Geheimnis ein.
9. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
10. Klicken Sie auf **Erstellen**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurieren eines Facebook-Kontos als Identitätsanbieter

1. Ersetzen Sie in der Datei `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** den Wert `client_id` durch die ID der Facebook-Anwendung:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>Hochladen und Testen der Richtlinie

Aktualisieren Sie als Nächstes die Datei der vertrauenden Seite, mit der die erstellte User Journey initiiert wird.

1. Laden Sie die Datei *TrustFrameworkExtensions.xml* in Ihren Mandanten hoch.
1. Wählen Sie unter **Benutzerdefinierte Richtlinien** die Richtlinie **B2C_1A_signup_signin** aus.
1. Wählen Sie für **Anwendung auswählen** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie **Jetzt ausführen**, und wählen Sie „Facebook“ aus, um sich bei Facebook anzumelden und die benutzerdefinierte Richtlinie zu testen.

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie das [Facebook-Token an Ihre Anwendung übergeben](idp-pass-through-user-flow.md).
