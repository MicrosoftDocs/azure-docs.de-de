---
title: Single-Page-Webanwendung (Erwerben eines Tokens zum Aufrufen einer API) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Single-Page-Webanwendung erstellen (Erwerben eines Tokens zum Aufrufen einer API).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138811"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Single-Page-Anwendung – Erwerben eines Tokens zum Aufrufen einer API

Das Muster für das Erwerben von Token für APIs mit MSAL.js besteht darin, zunächst eine automatische Tokenanforderung mit der `acquireTokenSilent`-Methode zu versuchen. Wenn diese Methode aufgerufen wird, überprüft die Bibliothek zuerst den Cache im Browserspeicher, um festzustellen, ob ein gültiges Token vorhanden ist, und gibt es zurück. Wenn kein gültiges Token im Cache vorhanden ist, sendet sie aus einem ausgeblendeten IFrame eine automatische Tokenanforderung an Azure Active Directory (Azure AD). Diese Methode ermöglicht der Bibliothek auch, Token zu erneuern. Weitere Informationen zu SSO-Sitzung und Tokengültigkeitsdauer-Werten in Azure AD finden Sie unter [Konfigurierbare Tokengültigkeitsdauern in Azure Active Directory (Vorschau)](active-directory-configurable-token-lifetimes.md).

Bei den automatischen Tokenanforderungen für Azure AD können möglicherweise aus beliebigem Grund Fehler auftreten, z.B. wg. einer abgelaufenen Azure AD-Sitzung oder einer Kennwortänderung. In diesem Fall können Sie zum Abrufen von Token eine der interaktiven Methoden aufrufen (die den Benutzer zur Eingabe auffordert).

* [Erwerben eines Tokens mit einem Popupfenster](#acquire-token-with-a-pop-up-window) mit `acquireTokenPopup`
* [Erwerben eines Tokens mit Umleitung](#acquire-token-with-redirect) mit `acquireTokenRedirect`

**Auswählen zwischen einem Popupfenster oder einer Umleitung**

 Sie können in Ihrer Anwendung keine Kombination aus einer Popupfenster- und einer Umleitungsmethode verwenden. Die Entscheidung für ein Popupfenster oder eine Umleitung hängt von Ihrem Anwendungsfluss ab.

* Wenn Sie nicht möchten, dass der Benutzer während der Authentifizierung Ihre Hauptanmeldungsseite verlässt, wird empfohlen, die Popupmethoden zu verwenden. Da die Authentifizierungsumleitung in einem Popupfenster erfolgt, wird der Status der Hauptanwendung beibehalten.

* Es gibt bestimmte Fälle, in denen Sie möglicherweise die Umleitungsmethoden verwenden müssen. Wenn für Benutzer Ihrer Anwendung Browsereinschränkungen oder Richtlinien gelten, bei denen Popupfenster deaktiviert sind, können Sie die Umleitungsmethoden verwenden. Sie sollten auch die Umleitungsmethoden mit dem Internet Explorer-Browser verwenden, da es bestimmte [bekannte Probleme mit Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) bei der Verarbeitung von Popupfenstern gibt.

Sie können beim Erstellen der Anforderung eines Zugriffstokens die API-Bereiche festlegen, die das Zugriffstoken einbeziehen soll. Beachten Sie, dass möglicherweise nicht alle angeforderten Bereiche im Zugriffstoken gewährt werden und dies von der Zustimmung des Benutzers abhängt.

## <a name="acquire-token-with-a-pop-up-window"></a>Erwerben eines Tokens mit einem Popupfenster

### <a name="javascript"></a>JavaScript

Das oben genannten Muster mithilfe der Methoden für eine Popup-Benutzeroberfläche:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

### <a name="angular"></a>Angular

Der MSAL Angular Wrapper bietet die Vorteile des Hinzufügens des HTTP-Interceptors `MsalInterceptor`, der automatisch Zugriffstoken im Hintergrund erwirbt und sie den HTTP-Anforderungen von APIs anfügt.

Sie können die Bereiche für APIs in der Konfigurationsoption `protectedResourceMap` angeben, die der MsalInterceptor anfordert, wenn Token automatisch erworben werden.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

Für das erfolgreiche und nicht erfolgreiche automatische Erwerben von Token stellt MSAL Angular Rückrufe bereit, die Sie abonnieren können. Sie müssen auch daran denken, das Abonnement zu kündigen.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternativ können Sie Token auch explizit mit der in der MSAL.js-Kernbibliothek beschriebenen Methode erwerben.

## <a name="acquire-token-with-redirect"></a>Erwerben eines Tokens mit Umleitung

### <a name="javascript"></a>JavaScript

Das Muster ist wie oben beschrieben, wird jedoch mit einer Umleitungsmethode zum interaktiven Erwerben von Token gezeigt. Beachten Sie, dass Sie den Umleitungsrückruf wie bereits erwähnt registrieren müssen.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

### <a name="angular"></a>Angular

Dies entspricht der obigen Beschreibung.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aufrufen einer Web-API](scenario-spa-call-api.md)
