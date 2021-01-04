---
title: 'Schnellstart: Anmelden von Benutzern in Angular-Single-Page-Webanwendungen | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie aus einer Angular-App eine API aufrufen, für die Zugriffstoken erforderlich sind, die über die Microsoft Identity Platform ausgegeben werden.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: 72d9aefcdda5d224b256c6e969b7a75259a97005
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95750772"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Schnellstart: Anmelden von Benutzern und Abrufen eines Zugriffstokens in einer Angular-Single-Page-Webanwendung

In diesem Schnellstart laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine Angular-Single-Page-Webanwendung (SPA) Benutzer anmelden und Microsoft Graph aufrufen kann. Das Codebeispiel veranschaulicht das Abrufen eines Zugriffstokens zum Aufrufen der Microsoft Graph-API oder einer beliebigen Web-API.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) zum Bearbeiten von Projektdateien oder [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) zum Ausführen des Projekts

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Registrieren und Herunterladen der Schnellstart-App
> Verwenden Sie eine der folgenden Optionen, um die Schnellstart-App zu starten:
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Option 1 (Express): Registrieren und automatisches Konfigurieren der App und anschließendes Herunterladen des Codebeispiels
>
> 1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
> 1. Sollte Ihr Konto Zugriff auf mehrere Mandanten haben, wählen Sie rechts oben das entsprechende Konto aus, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten (Azure Active Directory) fest.
> 1. Öffnen Sie im Azure-Portal den neuen Bereich [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und klicken Sie auf **Registrieren**.
> 1. Navigieren Sie zum Schnellstartbereich, und zeigen Sie den Angular-Schnellstart an. Befolgen Sie die Anweisungen zum Herunterladen und automatischen Konfigurieren der neuen Anwendung.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Option 2 (manuell): Registrieren Sie die Anwendung und das Codebeispiel, und konfigurieren Sie sie manuell.
>
> #### <a name="step-1-register-the-application"></a>Schritt 1: Registrieren der Anwendung
>
> 1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
> 1. Sollte Ihr Konto Zugriff auf mehrere Mandanten haben, wählen Sie rechts oben Ihr Konto aus, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
> 1. Befolgen Sie die Anleitung zum [Registrieren einer Single-Page-Webanwendung](./scenario-spa-app-registration.md) im Azure-Portal.
> 1. Fügen Sie im Bereich **Authentifizierung** Ihrer App-Registrierung eine neue Plattform hinzu, und registrieren Sie den Umleitungs-URI: `http://localhost:4200/`.
> 1. In dieser Schnellstartanleitung wird der [Flow zur impliziten Genehmigung](v2-oauth2-implicit-grant-flow.md) verwendet. Wählen Sie die Einstellungen vom Typ **Implizite Genehmigung** für **ID-Token** und **Zugriffstoken** aus. ID- und Zugriffstoken sind erforderlich, da diese App Benutzer anmeldet und eine API aufruft.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren der Anwendung im Azure-Portal
> Damit das Codebeispiel für diesen Schnellstart funktioniert, müssen Sie den Umleitungs-URI **http://localhost:4200/** hinzufügen und **Implizite Genehmigung** aktivieren.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]() (Diese Änderungen für mich vornehmen)
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-javascript/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-code-sample"></a>Schritt 2: Herunterladen des Codebeispiels
>[!div renderon="docs"]
>Wenn Sie das Projekt mit einem Webserver unter Verwendung von Node.js ausführen möchten, [klonen Sie das Beispielrepository](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular), oder [laden Sie die Kernprojektdateien herunter](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Öffnen Sie die Dateien mit einem Editor (beispielsweise Visual Studio Code).

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip).

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Schritt 3: Konfigurieren der JavaScript-App
>
> Bearbeiten Sie im Ordner *src/app* die Datei *app.module.ts*, und legen Sie unter `MsalModule.forRoot` die Werte `clientId` und `authority` fest.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> Ersetzen Sie die folgenden Werte:
>
>|Wertname|BESCHREIBUNG|
>|---------|---------|
>|Enter_the_Application_Id_Here|Der Wert für **Anwendungs-ID (Client)** auf der Seite **Übersicht** Ihrer Anwendungsregistrierung |
>|Enter_the_Cloud_Instance_Id_Here|Die Instanz der Azure-Cloud. Geben Sie für die Azure-Hauptcloud oder für die globale Azure-Cloud **https://login.microsoftonline.com** ein. Informationen zu nationalen Clouds (z. B. für China) finden Sie unter [Nationale Clouds](./authentication-national-cloud.md).|
>|Enter_the_Tenant_Info_Here| Legen Sie eine der folgenden Optionen fest: Wenn Ihre Anwendung *Konten in diesem Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch die Verzeichnis-ID (Mandant) oder den Mandantennamen (z. B. **contoso.microsoft.com**). Wenn Ihre Anwendung *Konten in einem beliebigen Organisationsverzeichnis* unterstützt, ersetzen Sie diesen Wert durch **Organisationen**. Wenn Ihre Anwendung *Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten* unterstützt, ersetzen Sie diesen Wert durch **common**. Wenn Sie die Unterstützung *ausschließlich auf persönliche Microsoft-Konten* beschränken möchten, ersetzen Sie diesen Wert durch **consumers**. |
>|Enter_the_Redirect_Uri_Here|Ersetzen Sie diesen Wert durch **http://localhost:4200** .|
>|cacheLocation  | (Optional) Legen Sie den Browserspeicher für den Authentifizierungsstatus fest. Der Standardwert lautet **sessionStorage**.   |
>|storeAuthStateInCookie  | (Optional) Identifizieren Sie die Bibliothek, in der der Authentifizierungsanforderungsstatus gespeichert wird. Dieser Status ist für die Überprüfung der Authentifizierungsflows in den Browsercookies erforderlich. Dieses Cookie wird für Internet Explorer und Microsoft Edge festgelegt, um diese beiden Browser zu unterstützen. Weitere Informationen finden Sie im Artikel zu [bekannten Problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > Die Werte für **Anwendungs-ID (Client)** , **Verzeichnis-ID (Mandant)** und **Unterstützte Kontotypen** finden Sie im Azure-Portal auf der Seite **Übersicht** der App.

Weitere Informationen zu den verfügbaren konfigurierbaren Optionen finden Sie unter [Initialisieren von Clientanwendungen](msal-js-initializing-client-applications.md).

Den Quellcode für die MSAL.js-Bibliothek finden Sie auf GitHub im Repository [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Schritt 3: Ausführen des Projekts

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Schritt 4: Ausführen des Projekts

Wenn Sie Node.js verwenden, gehen Sie wie folgt vor:

1. Starten Sie den Server, indem Sie im Projektverzeichnis die folgenden Befehle ausführen:

   ```console
   npm install
   npm start
   ```

1. Navigieren Sie zu **http://localhost:4200/** .
1. Wählen Sie **Anmelden**.
1. Wählen Sie **Profil** aus, um Microsoft Graph aufzurufen.

Wählen Sie **Anmelden** aus, wenn die Anwendung im Browser geladen wurde. Bei der ersten Anmeldung werden Sie aufgefordert, einzuwilligen, dass die Anwendung auf Ihr Profil zugreifen und Sie anmelden darf. Wählen Sie nach erfolgreicher Anmeldung **Profil** aus. Daraufhin werden Ihre Benutzerprofilinformationen auf der Seite angezeigt.

## <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

![Diagramm, das die Funktionsweise der Beispiel-App in diesem Schnellstart demonstriert](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)


## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich als Nächstes im Angular-Tutorial darüber, wie Sie einen Benutzer anmelden und Token abrufen:

> [!div class="nextstepaction"]
> [Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer Angular-SPA (Single-Page-Webanwendung)](./tutorial-v2-angular.md)