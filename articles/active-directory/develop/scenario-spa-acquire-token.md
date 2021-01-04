---
title: Abrufen eines Tokens zum Aufrufen einer Web-API (Single-Page-Apps) – Microsoft Identity Platform | Azure
description: Informationen zum Erstellen einer Single-Page-Webanwendung (Abrufen eines Tokens zum Aufrufen einer API)
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 3d91e69d7d9eac9f8cae1a1a122a13afd19ef631
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443873"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Single-Page-Webanwendung: Abrufen eines Tokens zum Aufrufen einer API

Das Muster für das Abrufen von Token für APIs mit MSAL.js besteht darin, zuerst zu versuchen, eine automatische Tokenanforderung mithilfe der `acquireTokenSilent`-Methode zu senden. Wenn diese Methode aufgerufen wird, überprüft die Bibliothek zuerst den Cache im Browserspeicher, um festzustellen, ob ein gültiges Token vorhanden ist, und dann wird das Token zurückgegeben. Wenn kein gültiges Token im Cache vorhanden ist, sendet sie aus einem ausgeblendeten IFrame eine automatische Tokenanforderung an Azure Active Directory (Azure AD). Diese Methode ermöglicht der Bibliothek auch, Token zu erneuern. Weitere Informationen zu SSO-Sitzungen und Werten für die Tokengültigkeitsdauer in Azure AD finden Sie unter [Konfigurierbare Tokengültigkeitsdauern in Azure Active Directory (Vorschau)](active-directory-configurable-token-lifetimes.md).

Beim Senden von automatischen Tokenanforderungen an Azure AD können beispielsweise aufgrund einer abgelaufenen Azure AD-Sitzung oder einer Kennwortänderung Fehler auftreten. In diesem Fall können Sie eine der interaktiven Methoden aufrufen (die den Benutzer zur Eingabe auffordern), um Token abzurufen:

* [Popupfenster](#acquire-a-token-with-a-pop-up-window) mithilfe von `acquireTokenPopup`
* [Umleitung](#acquire-a-token-with-a-redirect) mithilfe von `acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Auswählen zwischen einem Popupfenster oder einer Umleitung

 Sie können in Ihrer Anwendung nicht die Popupfenster- und die Umleitungsmethode verwenden. Die Entscheidung für ein Popupfenster oder eine Umleitung hängt von Ihrem Anwendungsfluss ab:

* Wenn Sie nicht möchten, dass Benutzer während der Authentifizierung Ihre Hauptanwendungsseite verlassen, empfiehlt sich die Verwendung der Popupmethode. Da die Authentifizierungsumleitung in einem Popupfenster erfolgt, wird der Status der Hauptanwendung beibehalten.

* Wenn für Benutzer Browsereinschränkungen oder Richtlinien gelten, bei denen Popupfenster deaktiviert sind, können Sie die Umleitungsmethode verwenden. Wenn Sie Internet Explorer als Browser nutzen, verwenden Sie die Umleitungsmethode, weil [in Internet Explorer bekannte Probleme mit Popupfenstern bestehen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Sie können beim Erstellen der Zugriffstokenanforderung die API-Bereiche festlegen, die das Zugriffstoken einschließen soll. Beachten Sie, dass möglicherweise nicht alle angeforderten Bereiche im Zugriffstoken gewährt werden. Das ist von der Zustimmung des Benutzers abhängig.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Abrufen eines Tokens mit einem Popupfenster

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Im folgenden Code wird das zuvor beschriebene Muster mit den Methoden für ein Popupelement kombiniert:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
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

# <a name="angular"></a>[Angular](#tab/angular)

Der MSAL Angular-Wrapper nutzt den HTTP-Interceptor, der Zugriffstoken automatisch im Hintergrund abruft und an die HTTP-Anforderungen für APIs anfügt.

Sie können die Bereiche für APIs in der Konfigurationsoption `protectedResourceMap` angeben. `MsalInterceptor` fordert diese Bereiche beim automatischen Abrufen von Token an.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Für den erfolgreichen und den fehlerhaften automatischen Tokenabruf stellt MSAL Angular Rückrufe bereit, die Sie abonnieren können. Sie müssen auch daran denken, das Abonnement zu kündigen.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternativ können Sie Token auch explizit mithilfe der in der MSAL.js-Kernbibliothek beschriebenen „acquire-token“-Methoden abrufen.

---

## <a name="acquire-a-token-with-a-redirect"></a>Abrufen eines Tokens mit einer Umleitung

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Das folgende Muster entspricht dem zuvor beschriebenen Muster, wird jedoch mit einer Umleitungsmethode zum interaktiven Abrufen von Token dargestellt. Wie bereits erwähnt, müssen Sie den Umleitungsrückruf registrieren.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Anfordern optionaler Ansprüche

Sie können optionale Ansprüche zu folgenden Zwecken verwenden:

- Einbinden zusätzlicher Ansprüche in Token für Ihre Anwendung
- Ändern des Verhaltens bestimmter Ansprüche, die von Azure AD in Token zurückgegeben werden
- Hinzufügen und Zugreifen auf benutzerdefinierte Ansprüche für Ihre Anwendung

Zum Anfordern optionaler Ansprüche in `IdToken` können Sie ein als Zeichenfolge dargestelltes Anspruchsobjekt an das Feld `claimsRequest` der `AuthenticationParameters.ts`-Klasse senden.

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

Weitere Informationen finden Sie unter [Optionale Ansprüche](active-directory-optional-claims.md).

# <a name="angular"></a>[Angular](#tab/angular)

Dieser Code entspricht dem zuvor beschriebenen Code.

---

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Aufrufen einer Web-API](scenario-spa-call-api.md).