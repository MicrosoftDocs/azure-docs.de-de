---
title: 'Tutorial: Aktivieren der Authentifizierung in einer Single-Page-Webanwendung'
titleSuffix: Azure AD B2C
description: In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory B2C zum Bereitstellen von Benutzeranmeldungen für eine JavaScript-basierte Single-Page-Webanwendung verwenden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6a9f3b864bd8aba2140c7d32d4b5474ff7b95f88
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171227"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Tutorial: Aktivieren der Authentifizierung in einer Single-Page-Webanwendung mit Azure AD B2C

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory B2C (Azure AD B2C) für die Registrierung und Anmeldung von Benutzern in einer Single-Page-Webanwendung (Single-Page Application, SPA) verwenden. Dazu wird einer der folgenden Flows verwendet:
* [OAuth 2.0-Autorisierungscodefluss (mit PKCE)](./authorization-code-flow.md) (unter Verwendung von [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser))
* [OAuth 2.0-Flow für die implizite Genehmigung](./implicit-flow-single-page-application.md) (unter Verwendung von [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core))

Dieses Tutorial ist der erste Teil einer zweiteiligen Reihe und beinhaltet Folgendes:

> [!div class="checklist"]
> * Hinzufügen einer Antwort-URL zu einer in Ihrem Azure AD B2C-Mandanten registrierten Anwendung
> * Herunterladen eines Codebeispiels von GitHub
> * Ändern des Codes der Beispielanwendung zur Verwendung mit Ihrem Mandanten
> * Registrieren mithilfe Ihres Benutzerablaufs für die Registrierung/Anmeldung

Im [nächsten Tutorial](tutorial-single-page-app-webapi.md) dieser Reihe wird der Web-API-Teil des Codebeispiels aktiviert.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen die folgenden Azure AD B2C-Ressourcen, bevor Sie mit den Schritten dieses Tutorials fortfahren können:

* [Azure AD B2C-Mandant](tutorial-create-tenant.md)
* In Ihrem Mandanten [registrierte Anwendung](tutorial-register-spa.md)
* Im Mandanten [erstellte Benutzerabläufe](tutorial-create-user-flows.md)

Darüber hinaus benötigen Sie in Ihrer lokalen Entwicklungsumgebung Folgendes:

* [Visual Studio Code](https://code.visualstudio.com/) oder ein anderer Code-Editor
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualisieren der Anwendung

Im [zweiten Tutorial](./tutorial-register-spa.md), das Sie zur Vorbereitung absolviert haben, wurde eine Single-Page-Webanwendung unter Azure AD B2C registriert. Um die Kommunikation mit dem Codebeispiel in diesem Tutorial zu ermöglichen, muss der Anwendungsregistrierung eine Antwort-URL (auch Umleitungs-URI genannt) hinzugefügt werden.

Zum Aktualisieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie unsere neue einheitliche Benutzeroberfläche **App-Registrierungen** oder unsere alte Benutzeroberfläche **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](./app-registrations-training-guide.md)

#### <a name="app-registrations-auth-code-flow"></a>[App-Registrierungen (Autorisierungscodeflow)](#tab/app-reg-auth/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen**, die Registerkarte **Anwendungen mit Besitzer** und dann die Anwendung *spaapp1* aus.
1. Wählen Sie unter **Single-Page-Webanwendung** den Link **URI hinzufügen** aus, und geben Sie dann `http://localhost:6420` ein.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie **Übersicht**.
1. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt, wenn Sie den Code in der Single-Page-Webanwendung aktualisieren.

#### <a name="app-registrations-implicit-flow"></a>[App-Registrierungen (impliziter Flow)](#tab/app-reg-implicit/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen**, die Registerkarte **Anwendungen mit Besitzer** und dann die Anwendung *spaapp1* aus.
1. Wählen Sie unter **Single-Page-Webanwendung** den Link **URI hinzufügen** aus, und geben Sie dann `http://localhost:6420` ein.
1. Aktivieren Sie unter **Implizite Genehmigung** die Kontrollkästchen **Zugriffstoken** und **ID-Token** (falls erforderlich), und wählen Sie **Speichern** aus.
1. Wählen Sie **Übersicht**.
1. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt, wenn Sie den Code in der Single-Page-Webanwendung aktualisieren.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Anwendungen (Legacy)** und anschließend die Anwendung *spaapp1* aus.
1. Fügen Sie unter **Antwort-URL** Folgendes hinzu: `http://localhost:6420`.
1. Wählen Sie **Speichern** aus.
1. Notieren Sie sich auf der Eigenschaftenseite die **Anwendungs-ID**. Sie verwenden die App-ID in einem späteren Schritt, wenn Sie den Code in der Single-Page-Webanwendung aktualisieren.

* * *

## <a name="get-the-sample-code"></a>Laden Sie den Beispielcode herunter

In diesem Tutorial wird ein von GitHub heruntergeladenes Codebeispiel für die Verwendung mit Ihrem B2C-Mandanten konfiguriert. Das Beispiel veranschaulicht, wie eine Single-Page-Webanwendung Azure AD B2C für die Benutzerregistrierung und -anmeldung sowie zum Aufrufen einer geschützten Web-API verwenden kann. (Die Web-API wird im nächsten Tutorial der Reihe aktiviert.)

* Beispiel für den MSAL.js 2.x-Autorisierungscodeflow:

    [Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip), oder klonen Sie das Beispiel aus GitHub:

    ```
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```
* Beispiel für den impliziten MSAL.js 1.x-Flow:

    [Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip), oder klonen Sie das Beispiel aus GitHub:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="update-the-sample"></a>Aktualisieren des Beispiels

Nachdem Sie nun das Beispiel vorliegen haben, können Sie den Code mit dem Namen Ihres Azure AD B2C-Mandanten und der Anwendungs-ID aus dem vorherigen Schritt aktualisieren.

#### <a name="auth-code-flow-sample"></a>[Beispiel für den Autorisierungscodeflow](#tab/config-auth/)

1. Öffnen Sie die Datei *authConfig.js* im Ordner *App*.
1. Suchen Sie im Objekt `msalConfig` die Zuweisung für `clientId`, und ersetzen Sie sie durch die **Anwendungs-ID (Client)** , die Sie sich in einem früheren Schritt notiert haben.
1. Öffnen Sie die Datei `policies.js`.
1. Suchen Sie die Einträge unter `names`, und ersetzen Sie ihre Zuweisung durch die Namen der Benutzerflows, die Sie in einem früheren Schritt erstellt haben, etwa `B2C_1_signupsignin1`.
1. Suchen Sie die Einträge unter `authorities`, und ersetzen Sie sie nach Bedarf durch die Namen der Benutzerflows, die Sie in einem früheren Schritt erstellt haben, etwa `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`.
1. Suchen Sie die Zuweisung für `authorityDomain`, und ersetzen Sie sie durch `<your-tenant-name>.b2clogin.com`.
1. Öffnen Sie die Datei `apiConfig.js`.
1. Suchen Sie die Zuweisung für `b2cScopes`, und ersetzen Sie die URL durch die Bereichs-URL, die Sie für die Web-API erstellt haben, z. B. `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]`.
1. Suchen Sie die Zuweisung für `webApi`, und ersetzen Sie die aktuelle URL durch die URL, unter der Sie Ihre Web-API in Schritt 4  bereitgestellt haben, z. B. `webApi: http://localhost:5000/hello`.

#### <a name="implicit-flow-sample"></a>[Beispiel für den impliziten Flow](#tab/config-implicit/)

1. Öffnen Sie die Datei *authConfig.js* im Ordner *JavaScriptSPA*.
1. Suchen Sie im Objekt `msalConfig` die Zuweisung für `clientId`, und ersetzen Sie sie durch die **Anwendungs-ID (Client)** , die Sie sich in einem früheren Schritt notiert haben.
1. Öffnen Sie die Datei `policies.js`.
1. Suchen Sie die Einträge unter `names`, und ersetzen Sie ihre Zuweisung durch die Namen der Benutzerflows, die Sie in einem früheren Schritt erstellt haben, etwa `B2C_1_signupsignin1`.
1. Suchen Sie die Einträge unter `authorities`, und ersetzen Sie sie nach Bedarf durch die Namen der Benutzerflows, die Sie in einem früheren Schritt erstellt haben, etwa `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`.
1. Öffnen Sie die Datei `apiConfig.js`.
1. Suchen Sie die Zuweisung für `b2cScopes`, und ersetzen Sie die URL durch die Bereichs-URL, die Sie für die Web-API erstellt haben, z. B. `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]`.
1. Suchen Sie die Zuweisung für `webApi`, und ersetzen Sie die aktuelle URL durch die URL, unter der Sie Ihre Web-API in Schritt 4  bereitgestellt haben, z. B. `webApi: http://localhost:5000/hello`.

* * *

Der sich ergebende Code sollte in etwa wie folgt aussehen:

#### <a name="auth-code-flow-sample"></a>[Beispiel für den Autorisierungscodeflow](#tab/review-auth/)

*authConfig.js:*

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js:*

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "fabrikamb2c.b2clogin.com"
}
```

*apiConfig.js:*

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

#### <a name="implicit-flow-sample"></a>[Beispiel für den impliziten Flow](#tab/review-implicit/)

*authConfig.js:*

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js:*

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```

*apiConfig.js:*

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

* * *


## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Öffnen Sie ein Konsolenfenster, und navigieren Sie zu dem Verzeichnis mit dem Beispiel. 

    - Für das Beispiel für den MSAL.js 2.x-Autorisierungscodeflow:

        ```console
        cd ms-identity-b2c-javascript-spa
        ```
    - Für das Beispiel für den impliziten MSAL.js 1.x-Flow: 

        ```console
        cd active-directory-b2c-javascript-msal-singlepageapp
        ```

1. Führen Sie die folgenden Befehle aus:

    ```console
    npm install && npm update
    npm start
    ```

    Im Konsolenfenster wird die Portnummer des lokal ausgeführten Node.js-Servers angezeigt:

    ```console
    Listening on port 6420...
    ```
1. Navigieren Sie zu `http://localhost:6420`, um die auf Ihrem lokalen Computer ausgeführte Webanwendung anzuzeigen.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Webbrowser mit lokal ausgeführter Single-Page-Webanwendung":::

### <a name="sign-up-using-an-email-address"></a>Registrieren mit einer E-Mail-Adresse

Diese Beispielanwendung unterstützt Registrierung, Anmeldung und Kennwortzurücksetzung. In diesem Tutorial registrieren Sie sich mit einer E-Mail-Adresse.

1. Wählen Sie **Anmelden** aus, um den in einem vorherigen Schritt angegebenen Benutzerablauf *B2C_1_signupsignin1* zu initiieren.
1. Von Azure AD B2C wird eine Anmeldeseite mit einem Registrierungslink angezeigt. Da Sie noch nicht über ein Konto verfügen, klicken Sie auf den Link **Jetzt registrieren**.
1. Der Registrierungsworkflow zeigt eine Seite an, über die die Identität des Benutzers anhand einer E-Mail-Adresse erfasst und überprüft wird. Darüber hinaus werden im Rahmen des Registrierungsworkflows auch das Kennwort des Benutzers sowie die angeforderten Attribute erfasst, die im Benutzerablauf definiert wurden.

    Verwenden Sie eine gültige E-Mail-Adresse, und bestätigen Sie sie mithilfe des Prüfcodes. Legen Sie ein Kennwort fest. Geben Sie Werte für die angeforderten Attribute ein.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Vom Azure AD B2C-Benutzerablauf angezeigte Registrierungsseite":::

1. Wählen Sie **Erstellen** aus, um im Azure AD B2C-Verzeichnis ein lokales Konto zu erstellen.

Wenn Sie **Erstellen** auswählen, wird von der Anwendung der Name des angemeldeten Benutzers angezeigt.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Webbrowser mit Single-Page-Webanwendung und angemeldetem Benutzer":::

Wenn Sie die Anmeldung testen möchten, klicken Sie auf die Schaltfläche **Abmelden**, wählen Sie **Anmelden** aus, und melden Sie sich mit der bei der Registrierung eingegebenen E-Mail-Adresse und dem entsprechenden Kennwort an.

### <a name="what-about-calling-the-api"></a>Was ist mit dem Aufrufen der API?

Wenn Sie nach der Anmeldung die Schaltfläche **API aufrufen** auswählen, wird anstelle der Ergebnisse des API-Aufrufs die Seite des Benutzerablaufs für die Registrierung/Anmeldung angezeigt. Dies ist zu erwarten, da der API-Teil der Anwendung noch nicht für die Kommunikation mit einer Web-API-Anwendung konfiguriert wurde, die in *Ihrem* Azure AD B2C-Mandanten registriert wurde.

An diesem Punkt versucht die Anwendung immer noch, mit der im Demomandanten (fabrikamb2c.onmicrosoft.com) registrierten API zu kommunizieren, und da Sie nicht mit diesem Mandanten authentifiziert sind, wird die Registrierungs-/Anmeldeseite angezeigt.

Fahren Sie mit dem nächsten Tutorial dieser Reihe fort, um die geschützte API zu aktivieren. (Weitere Informationen finden Sie im Abschnitt [Nächste Schritte](#next-steps).)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Single-Page-Webanwendung so konfiguriert, dass sie mit einem Benutzerablauf in Ihrem Azure AD B2C-Mandanten verwendet werden kann, um eine Registrierungs- und Anmeldefunktion bereitzustellen. Dazu haben Sie folgende Schritte ausgeführt:

> [!div class="checklist"]
> * Hinzufügen einer Antwort-URL zu einer in Ihrem Azure AD B2C-Mandanten registrierten Anwendung
> * Herunterladen eines Codebeispiels von GitHub
> * Ändern des Codes der Beispielanwendung zur Verwendung mit Ihrem Mandanten
> * Registrieren mithilfe Ihres Benutzerablaufs für die Registrierung/Anmeldung

Fahren Sie jetzt mit dem nächsten Tutorial der Reihe fort, um über die Single-Page-Webanwendung Zugriff auf eine geschützte Web-API zu gewähren:

> [!div class="nextstepaction"]
> [Tutorial: Schützen einer Node.js-Web-API und Gewähren von Zugriff auf eine Node.js-Web-API über eine Single-Page-Webanwendung mithilfe von Azure AD B2C >](tutorial-single-page-app-webapi.md)