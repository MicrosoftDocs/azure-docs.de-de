---
title: 'Schnellstart: Anmelden von Benutzern in JavaScript-Single-Page-Web-Apps | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie aus einer JavaScript-App eine API aufrufen, für die Zugriffstoken erforderlich sind, die über die Microsoft Identity Platform ausgegeben werden.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 60eed96e20ffcebc3ddefccffa2761017e28e0d2
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103616"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Schnellstart: Anmelden von Benutzern und Abrufen eines Zugriffstokens in einer JavaScript-SPA

In diesem Schnellstart laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine JavaScript-Single-Page-Webanwendung (SPA) Benutzer anmelden und Microsoft Graph aufrufen kann. Das Codebeispiel veranschaulicht außerdem das Abrufen eines Zugriffstokens zum Aufrufen der Microsoft Graph-API oder einer beliebigen Web-API.

Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (zum Bearbeiten der Projektdateien)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrieren und Herunterladen Ihrer Schnellstartanwendung
> Verwenden Sie eine der folgenden Optionen, um die Schnellstartanwendung zu starten.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 (Express): Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
>
> 1. Navigieren Sie zur Umgebung des Schnellstarts <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure-Portal – App-Registrierungen</a>.
> 1. Geben Sie einen Namen für Ihre Anwendung ein.
> 1. Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
> 1. Wählen Sie **Registrieren**.
> 1. Befolgen Sie die Anweisungen zum Herunterladen und automatischen Konfigurieren der neuen Anwendung.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 (manuell): Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels
>
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
>
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie einen **Namen** für Ihre Anwendung ein. Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
> 1. Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
> 1. Wählen Sie **Registrieren**. Notieren Sie sich für die spätere Verwendung auf der Seite **Übersicht** den Wert von **Anwendungs-ID (Client)** .
> 1. Für diesen Schnellstart muss der [Flow zur impliziten Genehmigung](v2-oauth2-implicit-grant-flow.md) aktiviert werden. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
> 1. Wählen Sie **Plattformkonfigurationen** > **Plattform hinzufügen** aus. Wählen Sie **Web** aus.
> 1. Legen Sie den Wert für **Umleitungs-URI** auf `http://localhost:3000/` fest. 
> 1. Treffen Sie unter **Implizite Genehmigung und Hybridflows** eine Auswahl für **Zugriffstoken** und **ID-Token**.
> 1. Wählen Sie **Konfigurieren** aus.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
> Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen Sie den **Umleitungs-URI** `http://localhost:3000/` hinzufügen und die Option **Implizite Genehmigung** aktivieren.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]() (Diese Änderungen für mich vornehmen)
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-javascript/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-project"></a>Schritt 2: Herunterladen des Projekts

> [!div renderon="docs"]
> Um das Projekt mit einem Webserver unter Verwendung von Node.js auszuführen, [laden Sie die Kernprojektdateien herunter](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Ausführen des Projekts mit einem Webserver unter Verwendung von Node.js

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Schritt 3: Konfigurieren Ihrer JavaScript-App
>
> Bearbeiten Sie *authConfig.js* im Ordner *JavaScriptSPA*, und legen Sie unter `msalConfig` die Werte `clientID`, `authority` und `redirectUri` fest.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Hierbei gilt:
> - *\<Enter_the_Application_Id_Here>* ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.
> - *\<Enter_the_Cloud_Instance_Id_Here>* ist die Instanz der Azure-Cloud. Geben Sie für die Azure-Hauptcloud oder für die globale Azure-Cloud einfach *https://login.microsoftonline.com* ein. Informationen zu **nationalen** Clouds (etwa für China) finden Sie unter [Nationale Clouds](./authentication-national-cloud.md).
> - *\<Enter_the_Tenant_info_here>* wird auf eine der folgenden Optionen festgelegt:
>    - Wenn Ihre Anwendung *Nur Konten in diesem Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (beispielsweise *contoso.microsoft.com*).
>    - Wenn Ihre Anwendung *Konten in einem beliebigen Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch **Organisationen**.
>    - Wenn Ihre Anwendung *Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten* unterstützt, ersetzen Sie diesen Wert durch **common**. Wenn Sie die Unterstützung *ausschließlich auf persönliche Microsoft-Konten* beschränken möchten, ersetzen Sie diesen Wert durch **consumers**.
>
> > [!TIP]
> > Die Werte für **Anwendungs-ID (Client)** , **Verzeichnis-ID (Mandant)** und **Unterstützte Kontotypen** finden Sie im Azure-Portal auf der Seite **Übersicht** der App.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit
> Wir haben das Projekt mit Werten der Eigenschaften ihrer App konfiguriert.

> [!div renderon="docs"]
>
> Bearbeiten Sie dann im selben Ordner die Datei *graphConfig.js*, um `graphMeEndpoint` und `graphMeEndpoint` für das Objekt `apiConfig` festzulegen.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Hierbei gilt:
> - *\<Enter_the_Graph_Endpoint_Here>* ist der Endpunkt, an den API-Aufrufe gerichtet werden. Geben Sie für den Microsoft Graph-API-Hauptdienst oder den globalen Microsoft Graph-API-Dienst einfach `https://graph.microsoft.com` ein. Weitere Informationen finden Sie unter [Bereitstellungen nationaler Clouds](/graph/deployments).
>
> #### <a name="step-4-run-the-project"></a>Schritt 4: Ausführen des Projekts

Führen Sie das Projekt mit einem Webserver unter Verwendung von [Node.js](https://nodejs.org/en/download/) aus:

1. Führen Sie über das Projektverzeichnis den folgenden Befehl aus, um den Server zu starten:
    ```cmd
    npm install
    npm start
    ```
1. Öffnen Sie einen Webbrowser, und navigieren Sie zu `http://localhost:3000/`.

1. Wählen Sie **Anmelden** aus, um die Anmeldung zu starten, und rufen Sie anschließend die Microsoft Graph-API auf.

Wählen Sie **Anmelden** aus, wenn die Anwendung im Browser geladen wurde. Bei der ersten Anmeldung werden Sie aufgefordert, Ihre Zustimmung zu geben, dass die Anwendung auf Ihr Profil zugreifen und Sie anmelden darf. Bei erfolgreicher Anmeldung sollten Ihre Benutzerprofilinformationen auf der Seite angezeigt werden.

## <a name="more-information"></a>Weitere Informationen

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

![Funktionsweise der JavaScript-Beispiel-SPA: 1. Die SPA initiiert die Anmeldung. 2. Die SPA ruft ein ID-Token von Microsoft Identity Platform ab. 3. Die SPA ruft Abruftoken auf. 4. Microsoft Identity Platform gibt ein Zugriffstoken an die SPA zurück. 5. Die SPA sendet eine HTTP GET-Anforderung mit dem Zugriffstoken an die Microsoft Graph-API. 6. Die Graph-API gibt eine HTTP-Antwort an die SPA zurück.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

Über die MSAL-Bibliothek werden Benutzer angemeldet und die Token angefordert, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet werden. Die Datei *index.html* des Schnellstarts enthält einen Verweis auf die Bibliothek:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Sie können die vorstehende Version durch die letzte veröffentlichte Version unter [MSAL.js-Releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) ersetzen.

Wenn Sie Node.js installiert haben, können Sie die aktuelle Version auch über Node.js-Paket-Manager (npm) herunterladen:

```cmd
npm install msal
```

### <a name="msal-initialization"></a>MSAL-Initialisierung

Im Schnellstartcode wird auch veranschaulicht, wie Sie die MSAL-Bibliothek initialisieren:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Hierbei gilt:  | BESCHREIBUNG |
> |---------|---------|
> |`clientId`     | Die Anwendungs-ID der im Azure-Portal registrierten Anwendung.|
> |`authority`    | (Optional:) Die Autoritäts-URL zur Unterstützung von Kontotypen, wie im Konfigurationsabschnitt oben beschrieben. Die Standardautorität lautet `https://login.microsoftonline.com/common`. |
> |`redirectUri`     | Der konfigurierte Antwort-/Umleitungs-URI der Anwendungsregistrierung. In diesem Fall `http://localhost:3000/`. |
> |`cacheLocation`  | (Optional:) Hiermit wird der Browserspeicher für den Authentifizierungsstatus festgelegt. Der Standardwert lautet „sessionStorage“.   |
> |`storeAuthStateInCookie`  | (Optional:) Die Bibliothek, in der der für die Überprüfung der Authentifizierungsflows in den Browsercookies erforderliche Authentifizierungsanforderungsstatus gespeichert wird. Dieses Cookie wird für die Browser IE und Edge festgelegt, um die Auswirkungen bestimmter [bekannter Probleme](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) zu minimieren. |

Weitere Informationen zu den verfügbaren konfigurierbaren Optionen finden Sie unter [Initialisieren von Clientanwendungen](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Anmelden von Benutzern

Der folgende Codeausschnitt veranschaulicht, wie Sie Benutzer anmelden:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Hierbei gilt:  | BESCHREIBUNG |
> |---------|---------|
> | `scopes`   | (Optional:) Enthält Bereiche, die bei der Anmeldung für die Benutzereinwilligung angefordert werden. Beispiel: `[ "user.read" ]` für Microsoft Graph oder `[ "<Application ID URL>/scope" ]` für benutzerdefinierte Web-APIs (`api://<Application ID>/access_as_user`). |

> [!TIP]
> Alternativ hierzu können Sie auch die `loginRedirect`-Methode verwenden, um die aktuelle Seite auf die Anmeldeseite umzuleiten, anstatt an das Popupfenster.

### <a name="request-tokens"></a>Anfordern von Token

MSAL verwendet drei Methoden zum Abrufen von Token: `acquireTokenRedirect`, `acquireTokenPopup` und `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Automatisches Abrufen eines Benutzertokens

Die Methode `acquireTokenSilent` verwaltet das Abrufen und Erneuern von Token ohne Eingreifen des Benutzers. Nachdem die Methode `loginRedirect` oder `loginPopup` zum ersten Mal ausgeführt wurde, wird normalerweise die `acquireTokenSilent`-Methode zum Abrufen der Token genutzt, die für den Zugriff auf geschützte Ressourcen für nachfolgende Aufrufe verwendet werden. Aufrufe zum Anfordern oder Verlängern von Token werden im Hintergrund ausgeführt.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Hierbei gilt:  | BESCHREIBUNG |
> |---------|---------|
> | `scopes`   | Enthält Bereiche, die für die Rückgabe im Zugriffstoken für die API angefordert werden. Beispiel: `[ "mail.read" ]` für Microsoft Graph oder `[ "<Application ID URL>/scope" ]` für benutzerdefinierte Web-APIs (`api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Interaktives Abrufen eines Benutzertokens

Es gibt jedoch Situationen, in denen Sie die Interaktion der Benutzer mit Microsoft Identity Platform erzwingen. Beispiel:
* Benutzer müssen ihre Anmeldeinformationen gegebenenfalls erneut eingeben, weil ihr Kennwort abgelaufen ist.
* Ihre Anwendung fordert Zugriff auf zusätzliche Ressourcenbereiche an, für die der Benutzer seine Zustimmung erteilen muss.
* Die zweistufige Authentifizierung ist erforderlich.

Das übliche empfohlene Muster für die meisten Anwendungen ist zuerst das Aufrufen von `acquireTokenSilent`, das anschließende Abfangen der Ausnahme und dann das Aufrufen von `acquireTokenPopup` (oder `acquireTokenRedirect`), um eine interaktive Anforderung zu starten.

Durch Aufrufen von `acquireTokenPopup` wird ein Popupfenster für die Anmeldung angezeigt. (Über `acquireTokenRedirect` werden Benutzer an Microsoft Identity Platform umgeleitet.) In diesem Fenster müssen Benutzer interagieren, indem sie ihre Anmeldeinformationen bestätigen, ihre Zustimmung zur erforderlichen Ressource erteilen oder die zweistufige Authentifizierung durchführen.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> In diesem Schnellstart werden bei Verwendung von Microsoft Internet Explorer die Methoden `loginRedirect` und `acquireTokenRedirect` verwendet. Dies liegt an einem [bekannten Problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) bei der Verarbeitung von Popupfenstern in Internet Explorer.

## <a name="next-steps"></a>Nächste Schritte

Eine ausführliche Anleitung zum Erstellen der Anwendung für diesen Schnellstart finden Sie in folgendem Artikel:

> [!div class="nextstepaction"]
> [Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer JavaScript-SPA (Single-Page-Webanwendung)](tutorial-v2-javascript-spa.md)
