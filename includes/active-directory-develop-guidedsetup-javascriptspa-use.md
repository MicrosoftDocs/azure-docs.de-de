---
title: Includedatei
description: Includedatei
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8785f89a335f0ae7d983f267176da1656aee57a0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121747"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Verwenden der MSAL (Microsoft Authentication Library) für die Benutzeranmeldung

1. Fügen Sie der Datei `index.html` in den Tags `<script></script>` den folgenden Code hinzu:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };

    var graphConfig = {
        graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
    };

    // this can be used for login or token request, however in more complex situations
    // this can have diverging options
    var requestObj = {
        scopes: ["user.read"]
    };

    var myMSALObj = new Msal.UserAgentApplication(msalConfig);
    // Register Callbacks for redirect flow
    myMSALObj.handleRedirectCallback(authRedirectCallBack);


    function signIn() {

        myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
            //Login Success
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }).catch(function (error) {
            console.log(error);
        });
    }

    function acquireTokenPopupAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
        });
    }


    function graphAPICallback(data) {
        document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
    }


    function showWelcomeMessage() {
        var divWelcome = document.getElementById('WelcomeMessage');
        divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
        var loginbutton = document.getElementById('SignIn');
        loginbutton.innerHTML = 'Sign Out';
        loginbutton.setAttribute('onclick', 'signOut();');
    }


    //This function can be removed if you do not need to support IE
    function acquireTokenRedirectAndCallMSGraph() {
         //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
         myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
             callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
         }).catch(function (error) {
             console.log(error);
             // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
             // Call acquireTokenRedirect
             if (requiresInteraction(error.errorCode)) {
                 myMSALObj.acquireTokenRedirect(requestObj);
             }
         });
     }


    function authRedirectCallBack(error, response) {
        if (error) {
            console.log(error);
        }
        else {
            if (response.tokenType === "access_token") {
                callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
            } else {
                console.log("token type is:" + response.tokenType);
            }
        }
    }

    function requiresInteraction(errorCode) {
        if (!errorCode || !errorCode.length) {
            return false;
        }
        return errorCode === "consent_required" ||
            errorCode === "interaction_required" ||
            errorCode === "login_required";
    }

    // Browser check variables
    var ua = window.navigator.userAgent;
    var msie = ua.indexOf('MSIE ');
    var msie11 = ua.indexOf('Trident/');
    var msedge = ua.indexOf('Edge/');
    var isIE = msie > 0 || msie11 > 0;
    var isEdge = msedge > 0;
    //If you support IE, our recommendation is that you sign-in using Redirect APIs
    //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
    // can change this to default an experience outside browser use
    var loginType = isIE ? "REDIRECT" : "POPUP";

    if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
    }
    else if (loginType === 'REDIRECT') {
        document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
        };
        if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
    } else {
        console.error('Please set a valid login type');
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Weitere Informationen

Wenn ein Benutzer das erste Mal auf die Schaltfläche **Anmelden** klickt, ruft die `signIn`-Methode `loginPopup` auf, um den Benutzer anzumelden. Durch diese Methode wird ein Popupfenster mit dem *Microsoft Identity Platform-Endpunkt* geöffnet, um die Anmeldeinformationen des Benutzers anzufordern und zu überprüfen. Als Ergebnis einer erfolgreichen Anmeldung wird der Benutzer zurück zur ursprünglichen Seite *index.html* umgeleitet, und es wird ein Token empfangen, das von `msal.js` verarbeitet wird. Die im Token enthaltenen Informationen werden zwischengespeichert. Dieses Token ist das sogenannte *ID-Token*, das grundlegende Informationen zum Benutzer enthält, beispielsweise den Benutzeranzeigenamen. Wenn Sie von diesem Token bereitgestellte Daten nutzen möchten, müssen Sie sicherstellen, dass das Token durch Ihren Back-End-Server überprüft wird. So wird garantiert, dass das Token für einen gültigen Benutzer Ihrer Anwendung ausgestellt wurde.

Die in diesem Leitfaden generierte SPA ruft `acquireTokenSilent` bzw. `acquireTokenPopup` auf, um ein *Zugriffstoken* zu beziehen, das zum Abfragen von Informationen zum Benutzerprofil von der Microsoft Graph-API verwendet wird. Wenn Sie ein Beispiel zum Überprüfen des ID-Tokens suchen, sehen Sie sich [diese](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 sample")-Beispielanwendung in GitHub an. Dieses Beispiel verwendet eine ASP.NET-Web-API für die Tokenüberprüfung.

#### <a name="getting-a-user-token-interactively"></a>Interaktives Abrufen eines Benutzertokens

Nach der ersten Anmeldung möchten Sie die Benutzer nicht jedes Mal, wenn sie ein Token für den Zugriff auf eine Ressource anfordern, zur erneuten Authentifizierung auffordern – deshalb sollte in den meisten Fällen *acquireTokenSilent* für das Abrufen von Token verwendet werden. Es gibt jedoch Situationen, in denen Sie Benutzer zur Interaktion mit dem Microsoft Identity Platform-Endpunkt auffordern müssen. Beispiele:

- Benutzer müssen möglicherweise ihre Anmeldeinformationen erneut eingeben, da das Kennwort abgelaufen ist.
- Ihre Anwendung fordert Zugriff auf eine Ressource, der der Benutzer zustimmen muss.
- Die zweistufige Authentifizierung ist erforderlich.

Der Aufruf von *acquireTokenPopup* führt zu einem Popupfenster. (*acquireTokenRedirect* führt zum Umleiten von Benutzern an den Microsoft Identity Platform-Endpunkt.) Darin müssen Benutzer ihre Anmeldeinformationen bestätigen, ihre Zustimmung für die angeforderte Ressource geben oder die zweistufige Authentifizierung durchführen.

#### <a name="getting-a-user-token-silently"></a>Automatisches Abrufen eines Benutzertokens

Die Methode `acquireTokenSilent` verwaltet das Abrufen und Erneuern von Token ohne Eingreifen des Benutzers. Nachdem `loginPopup` (oder `loginRedirect`) zum ersten Mal ausgeführt wurde, ist `acquireTokenSilent` die übliche Methode zum Abrufen von Token, die für den Zugriff auf geschützte Ressourcen bei nachfolgenden Aufrufen verwendet werden, da Aufrufe zum Anfordern oder Verlängern von Token automatisch erfolgen.
`acquireTokenSilent` führt in einigen Fällen möglicherweise zu Fehlern – z.B. wenn das Kennwort des Benutzers abgelaufen ist. Ihre Anwendung kann diese Ausnahme auf zwei Arten handhaben:

1. Sofortiges Aufrufen von `acquireTokenPopup`, was dazu führt, dass der Benutzer zur Anmeldung aufgefordert wird. Dieses Muster wird in der Regel in Onlineanwendungen verwendet, in denen kein nicht authentifizierter Inhalt in der Anwendung für den Benutzer verfügbar ist. Die in diesem Leitfaden generierte Beispielanwendung verwendet dieses Muster.

2. Anwendungen können dem Benutzer auch visuell zu verstehen geben, dass eine interaktive Anmeldung erforderlich ist, damit der Benutzer den richtigen Zeitpunkt zum Anmelden wählen oder die Anwendung `acquireTokenSilent` zu einem späteren Zeitpunkt wiederholen kann. Dies wird normalerweise verwendet, wenn der Benutzer andere Funktionen der Anwendung nutzen kann, ohne unterbrochen zu werden, z.B. wenn nicht authentifizierter Inhalt in der Anwendung verfügbar ist. In diesem Fall kann der Benutzer entscheiden, ob er sich für den Zugriff auf die geschützte Ressource anmelden oder die veralteten Informationen aktualisieren möchte.

> [!NOTE]
> In diesem Schnellstart werden die Methoden `loginRedirect` und `acquireTokenRedirect` verwendet, wenn Internet Explorer als Browser genutzt wird. Dies liegt an einem [bekannten Problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) mit der Verarbeitung von Popupfenstern durch den Browser Internet Explorer.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Aufrufen der Microsoft Graph-API mit dem zuvor bezogenen Token

Fügen Sie der Datei `index.html` in den Tags `<script></script>` den folgenden Code hinzu:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Weitere Informationen zum Richten eines REST-Aufrufs an eine geschützte API

In der in diesem Leitfaden erstellten Beispielanwendung wird die `callMSGraph()`-Methode dazu verwendet, um eine HTTP `GET`-Anforderung an eine geschützte Ressource zu senden, die ein Token erfordert, und anschließend den Inhalt an den Aufrufer zurückzugeben. Diese Methode fügt das abgerufene Token in den *HTTP-Autorisierungsheader* ein. In der über diesen Leitfaden erstellten Beispielanwendung ist die Ressource der Endpunkt *me* der Microsoft Graph-API, der die Profilinformationen des Benutzers anzeigt.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Hinzufügen einer Methode zum Abmelden des Benutzers

Fügen Sie der Datei `index.html` in den Tags `<script></script>` den folgenden Code hinzu:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```
