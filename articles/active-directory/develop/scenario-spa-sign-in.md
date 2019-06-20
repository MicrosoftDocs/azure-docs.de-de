---
title: Single-Page-Webanwendung (Anmeldung) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Single-Page-Webanwendung (Anmeldung) erstellen
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138799"
---
# <a name="single-page-application---sign-in"></a>Single-Page-Webanwendung – Anmeldung

Erfahren Sie, wie Sie die Anmeldung zum Code Ihrer Single-Page-Webanwendung hinzufügen.

Bevor Sie die Token für den Zugriff auf APIs in Ihrer Anwendung abrufen können, benötigen Sie einen authentifizierten Benutzerkontext. Sie können Benutzer in MSAL.js auf zwei Arten bei Ihrer Anwendung anmelden:

* [Anmelden mit einem Popupfenster](#sign-in-with-a-pop-up-window) mit der `loginPopup`-Methode
* [Anmelden mittels einer Umleitung](#sign-in-with-redirect) mit der `loginRedirect`-Methode

Sie können auch optional diejenigen Bereiche der APIs übergeben, für die der Benutzer zum Zeitpunkt der Anmeldung seine Zustimmung erteilen muss.

> [!NOTE]
> Wenn Ihre Anwendung bereits Zugriff auf einen authentifizierten Benutzerkontext oder das ID-Token hat, können Sie den Anmeldeschritt überspringen und Token direkt abrufen. Weitere Informationen hierzu finden Sie unter [sso without msal.js login (SSO-Anmeldung ohne msal.js)](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Auswählen zwischen einem Popupfenster oder einer Umleitung

Sie können in Ihrer Anwendung keine Kombination aus einem Popupfenster und den Umleitungsmethoden verwenden. Die Entscheidung für ein Popupfenster oder eine Umleitung hängt von Ihrem Anwendungsfluss ab.

* Wenn Sie nicht möchten, dass der Benutzer während der Authentifizierung Ihre Hauptanmeldungsseite verlässt, wird empfohlen, die Popupmethoden zu verwenden. Da die Authentifizierungsumleitung in einem Popupfenster erfolgt, wird der Status der Hauptanwendung beibehalten.

* Es gibt bestimmte Fälle, in denen Sie möglicherweise die Umleitungsmethoden verwenden müssen. Wenn für Benutzer Ihrer Anwendung Browsereinschränkungen oder Richtlinien gelten, bei denen Popupfenster deaktiviert sind, können Sie die Umleitungsmethoden verwenden. Verwenden Sie die Umleitungsmethoden mit dem Internet Explorer-Browser, da es bestimmte [bekannte Probleme mit Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) bei der Verarbeitung von Popupfenstern gibt.

## <a name="sign-in-with-a-pop-up-window"></a>Anmelden mit einem Popupfenster

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

Mit dem MSAL Angular-Wrapper können Sie bestimmte Routen in Ihrer Anwendung sichern, indem Sie einfach `MsalGuard` zu der Routendefinition hinzufügen. Dieser Wächter ruft die Methode für die Anmeldung auf, wenn auf diese Route zugegriffen wird.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Aktivieren Sie für ein Popupfenster die Konfigurationsoption `popUp`. Sie können wie folgt auch die Bereiche übergeben, die eine Zustimmung erfordern:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Anmelden mit einer Umleitung

### <a name="javascript"></a>JavaScript

Die Umleitungsmethoden geben keine Zusage zurück, was in der Navigation weg von der Hauptanwendung begründet liegt. Um die zurückgegebenen Token zu verarbeiten und auf sie zuzugreifen, müssen Sie Erfolgs- und Fehlerrückrufe registrieren, bevor die Umleitungsmethoden aufgerufen werden.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Hier entspricht der Code dem im Abschnitt „Anmelden mit einem Popupfenster“ beschriebenen Code. Der Standardfluss wird umgeleitet.

> [!NOTE]
> Das ID-Token enthält nicht die Bereiche, zu denen Zustimmung erteilt wurde, und repräsentiert nur den authentifizierten Benutzer. Die Bereiche mit Zustimmung werden im Zugriffstoken zurückgegeben, das Sie im nächsten Schritt abrufen.

## <a name="sign-out"></a>Abmelden

Die MSAL-Bibliothek bietet eine `logout`-Methode, die den Cache im Speicher des Browsers löscht und eine Abmeldungsanforderung an Azure AD sendet. Nach der Abmeldung erfolgt standardmäßig die Umleitung zurück zur Startseite der Anwendung.

Durch Festlegen von `postLogoutRedirectUri` können Sie den URI konfigurieren, zu dem nach der Anmeldung umgeleitet werden soll. Dieser URI sollte auch als Abmeldungs-URI in Ihrer Anwendungsregistrierung registriert sein.

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Abrufen eines Tokens für die App](scenario-spa-acquire-token.md)
