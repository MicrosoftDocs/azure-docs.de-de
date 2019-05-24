---
title: Fehler und Ausnahmen (MSAL) | Azure
description: Erfahren Sie, wie Sie Fehler und Ausnahmen, den bedingten Zugriff und Anspruchsaufforderungen in MSAL-Anwendungen behandeln.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f1972a870ac15e1ca8dde963eef6cf7f1caf3039
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544182"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Behandeln von Ausnahmen und Fehlern mit MSAL
Ausnahmen in der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) sind für App-Entwickler vorgesehen, die eine Problembehandlung durchführen, und nicht für Endbenutzer. Ausnahmemeldungen sind nicht lokalisiert.

Bei der Verarbeitung von Ausnahmen und Fehlern können Sie den Ausnahmetyp selbst und den Fehlercode verwenden, um zwischen Ausnahmen zu unterscheiden.  Eine Liste der Fehlercodes finden Sie unter [Authentifizierungs- und Autorisierungsfehlercodes](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>.NET-Ausnahmen
Bei der Verarbeitung von Ausnahmen können Sie den Ausnahmetyp selbst und das `ErrorCode`-Element verwenden, um zwischen Ausnahmen zu unterscheiden. Die Werte von `ErrorCode` sind Konstanten des Typs [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet#fields).

Sie können auch die Felder von [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet#fields), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) und [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet#fields) überprüfen.

Wenn [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) ausgelöst wird, enthält der Fehlercode möglicherweise einen Code, den Sie unter [Authentifizierungs- und Autorisierungsfehlercodes](reference-aadsts-error-codes.md) finden können.

### <a name="common-exceptions"></a>Allgemeine Ausnahmen
Im Folgenden allgemeine Ausnahmen, die ausgelöst werden können, und mögliche Lösungen.

| Ausnahme | Fehlercode | Lösung|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: The user or administrator has not consented to use the application with ID '{appId}' named '{appName}'. (Der Benutzer oder Administrator hat nicht zugestimmt, die Anwendung mit der ID '{appId}' und dem Namen '{appName}' zu verwenden.) Send an interactive authorization request for this user and resource. (Senden Sie eine interaktive Autorisierungsanforderung für diesen Benutzer und die Ressource.)| Sie müssen zuerst die Zustimmung des Benutzers einholen. Wenn Sie nicht .NET Core verwenden (das keine Webbenutzeroberfläche aufweist), rufen Sie (einmalig) `AcquireTokeninteractive` auf. Wenn Sie .NET Core verwenden oder `AcquireTokenInteractive` nicht ausführen möchten, kann der Benutzer zu einer URL navigieren, um seine Zustimmung zu geben: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. So rufen Sie `AcquireTokenInteractive` auf: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: The user is required to use multi-factor authentication. (Der Benutzer muss eine mehrstufige Authentifizierung durchführen.)| Es gibt keine Lösung. Wenn MFA für Ihren Mandanten konfiguriert ist und AAD entscheidet, die Methode durchzusetzen, müssen Sie auf einen interaktiven Flow wie `AcquireTokenInteractive` oder `AcquireTokenByDeviceCode` ausweichen.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) |AADSTS90010: The grant type is not supported over the */common* or */consumers* endpoints. (Der Gewährungstyp wird für den /common- oder /consumers-Endpunkt nicht unterstützt.) Use the */organizations* or tenant-specific endpoint. (Verwenden Sie den /organizations-Endpunkt oder mandantenspezifischen Endpunkt.) You used */common*. (Sie haben /common verwendet.)| Wie in der Azure AD-Meldung erläutert, muss die Autorität über einen Mandanten oder ansonsten über einen */organizations*-Endpunkt verfügen.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) | AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'. (Der Anforderungstext muss den folgenden Parameter enthalten: 'client_secret or client_assertion'.)| Dies kann vorkommen, wenn die Anwendung in Azure AD nicht als öffentliche Clientanwendung registriert wurde. Bearbeiten Sie im Azure-Portal das Manifest für Ihre Anwendung, und legen Sie `allowPublicClient` auf `true` fest. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user Message: (unknown_user-Meldung:) Could not identify logged in user. (Der angemeldete Benutzer konnte nicht identifiziert werden.)| Die Bibliothek war nicht in der Lage, den aktuellen, in Windows angemeldeten Benutzer abzufragen, oder der Benutzer ist nicht in AD oder AAD eingebunden (Benutzer, die über den Arbeitsplatz eingebunden sind, werden nicht unterstützt). Lösung 1: Vergewissern Sie sich auf der UWP, dass die Anwendung die folgenden Funktionen aufweist: Unternehmensauthentifizierung, private Netzwerke (Client und Server), Benutzerkonteninformationen. Lösung 2: Implementieren Sie eigene Logik zum Abrufen des Benutzernamens (z. B. john@contoso.com), und verwenden Sie die Methode in der Form `AcquireTokenByIntegratedWindowsAuth`, die den Benutzernamen annimmt.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Diese Methode basiert auf einem von Active Directory (AD) verfügbar gemachten Protokoll. Wenn ein Benutzer in Azure Active Directory ohne AD-Unterstützung („verwalteter Benutzer“) erstellt wurde, schlägt diese Methode fehl. Benutzer, die in AD erstellt und von AAD unterstützt werden („Partnerbenutzer“), können von dieser nicht interaktiven Authentifizierungsmethode profitieren. Lösung: Verwenden Sie die interaktive Authentifizierung.|

## <a name="javascript-errors"></a>JavaScript-Fehler

MSAL.js bietet Error-Objekte, die die verschiedenen allgemeinen Fehlertypen abstrahieren und klassifizieren und über eine Schnittstelle verfügen, die den Zugriff auf bestimmte Fehlerdetails wie Fehlermeldungen ermöglichen, damit diese ordnungsgemäß behandelt werden können.

**Error-Objekt**

```javascript                                
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```                
Durch die Erweiterung der Fehlerklasse haben Sie Zugriff auf die folgenden Eigenschaften:
* **AuthError.message:** Entspricht errorMessage.
* **AuthError.stack:** Die Stapelüberwachung für ausgelöste Fehler. Sie ermöglicht die Ablaufverfolgung zum Ursprung des Fehlers.

**Fehlertypen**

Folgende Fehlertypen sind verfügbar:

* *AuthError:* Die grundlegende Fehlerklasse für die MSAL.js-Bibliothek. Sie wird auch für unerwartete Fehler verwendet.

* *ClientAuthError:* Die Fehlerklasse, die auf ein Problem mit der Clientauthentifizierung hinweist. Bei den meisten Fehlern, die von der Bibliothek gemeldet werden, handelt es sich um ClientAuthErrors. Beispielsweise kann eine Anmeldemethode aufgerufen werden, während die Anmeldung bereits ausgeführt wird, Benutzer können die Anmeldung abbrechen usw.

* *ClientConfigurationError:* Fehlerklasse zur Erweiterung von ClientAuthError, der ausgelöst wird, bevor Anforderungen ausgeführt werden, wenn der angegebene Benutzerkonfigurationsparameter falsch formatiert oder nicht vorhanden ist.

* *ServerError:* Fehlerklasse, die die vom Authentifizierungsserver gesendeten Fehlerzeichenfolgen darstellt. Bei den Fehlern kann es sich um ungültige Anforderungsformate oder -parameter sowie um andere Fehler handeln, die den Server an der Authentifizierung oder Autorisierung des Benutzers hindern.

* *InteractionRequiredAuthError:* Fehlerklasse, die ServerError erweitert und Serverfehler darstellt, die einen interaktiven Aufruf erfordern. Der Fehler wird durch `acquireTokenSilent` ausgelöst, wenn der Benutzer mit dem Server interagieren muss, um Anmeldeinformationen anzugeben oder der Authentifizierung/Autorisierung zuzustimmen. Die Fehlercodes umfassen „interaction_required“, „login_required“ und „consent_required“.

Zur Fehlerbehandlung in Authentifizierungsflows mit Umleitungsmethoden (`loginRedirect`, `acquireTokenRedirect`) müssen Sie den Rückruf, der nach der Umleitung erfolgreich oder mit einem Fehler aufgerufen wird, mithilfe der `handleRedirectCallback()`-Methode wie folgt registrieren:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Die Methoden für Popupfunktionen (`loginPopup`, `acquireTokenPopup`) geben Zusagen zurück. Auf diese Weise können Sie das Zusagemuster („.then“ und „.catch“) verwenden, um die Methoden zu behandeln, wie im Folgenden dargestellt:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Fehler, die eine Interaktion erfordern

Ein Fehler wird zurückgegeben, wenn eine Interaktion mit der Benutzeroberfläche erforderlich ist. Das bedeutet, dass Sie versucht haben, eine nicht interaktive Methode (z. B. `acquireTokenSilent`) für den Tokenabruf zu verwenden, MSAL das Token aber nicht automatisch abrufen konnte. Mögliche Gründe:

* Sie müssen sich anmelden.
* Sie müssen Ihre Zustimmung geben.
* Sie müssen eine mehrstufige Authentifizierung ausführen.

Die Lösung besteht darin, eine interaktive Methode wie `acquireTokenPopup` oder `acquireTokenRedirect` aufzurufen:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="conditional-access-and-claims-challenges"></a>Bedingter Zugriff und Anspruchsaufforderungen
Beim automatischen Abruf von Token kann Ihre Anwendung Fehler empfangen, wenn für eine API, auf die Sie zugreifen möchten, eine [Anspruchsaufforderung für bedingten Zugriff](conditional-access-dev-guide.md) wie z. B. eine MFA-Richtlinie erforderlich ist.

Sie behandeln diesen Fehler in der Regel dadurch, dass Sie mithilfe von MSAL ein Token interaktiv abrufen. Beim interaktiven Abruf eines Tokens erhält der Benutzer in Form einer Aufforderung die Möglichkeit, die erforderliche Richtlinie für bedingten Zugriff zu erfüllen.

In bestimmten Fällen können Sie beim Aufruf einer API, die bedingten Zugriff erfordert, in dem von der API ausgegebenen Fehler eine Anspruchsaufforderung erhalten. Wenn die Richtlinie für bedingten Zugriff beispielsweise ein verwaltetes Gerät (Intune) vorsieht, könnte eine mit der folgenden vergleichbare Fehlermeldung ausgegeben werden – [AADSTS53000: Für den Zugriff auf diese Ressource müssen Sie ein verwaltetes Gerät verwenden.](reference-aadsts-error-codes.md) In diesem Fall können Sie die Ansprüche im Aufruf für den Tokenabruf übergeben, damit der Benutzer aufgefordert wird, die entsprechende Richtlinie zu erfüllen.

### <a name="net"></a>.NET
Wenn Sie eine API, die den bedingten Zugriff erfordert, von MSAL.NET aufrufen, muss Ihre Anwendung Ausnahmen bei Anspruchsaufforderungen behandeln. Die Ausnahme tritt in Form einer [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) auf, deren [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet)-Eigenschaft nicht leer ist.

Um die Anspruchsaufforderung zu behandeln, verwenden Sie die `.WithClaim()`-Methode der `PublicClientApplicationBuilder`-Klasse.

### <a name="javascript"></a>JavaScript
Beim automatischen Abruf von Token mit MSAL.js (und `acquireTokenSilent`) kann Ihre Anwendung Fehler empfangen, wenn für eine API, auf die Sie zugreifen möchten, eine [Anspruchsaufforderung für bedingten Zugriff](conditional-access-dev-guide.md) wie z. B. eine MFA-Richtlinie erforderlich ist.

Sie behandeln diesen Fehler in der Regel dadurch, dass Sie einen interaktiven Aufruf ausführen, um ein Token in MSAL.js abzurufen, z. B. mit `acquireTokenPopup` oder `acquireTokenRedirect`. Siehe folgendes Beispiel:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

Beim interaktiven Abruf des Tokens erhält der Benutzer in Form einer Aufforderung die Möglichkeit, die erforderliche Richtlinie für bedingten Zugriff zu erfüllen.

Beim Aufruf einer API, die bedingten Zugriff erfordert, können Sie in dem von der API ausgegebenen Fehler eine Anspruchsaufforderung erhalten. In diesem Fall können Sie die im Fehler zurückgegebenen Ansprüche im Aufruf für den Tokenabruf als `extraQueryParameters` übergeben, damit der Benutzer aufgefordert wird, die entsprechende Richtlinie zu erfüllen:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Wiederholung nach Fehlern und Ausnahmen

### <a name="http-error-codes-500-600"></a>HTTP-Fehlercodes 500–600
MSAL.NET implementiert einen einfachen einmaligen Wiederholungsmechanismus für Fehler mit den HTTP-Fehlercodes 500–600.

### <a name="http-429"></a>HTTP 429
Wenn der Service Token Server (STS) aufgrund „zu vieler Anforderungen“ ausgelastet ist, gibt er einen HTTP-Fehler 429 mit einem Hinweis (dem Retry-After-Antwortfeld) zurück, der angibt, nach wie vielen Sekunden oder zu welchem Datum der Versuch wiederholt werden kann.

#### <a name="net"></a>.NET
Die [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)-Ausnahme gibt `System.Net.Http.Headers.HttpResponseHeaders` als `namedHeaders`-Eigenschaft aus. Daher können Sie zusätzliche Informationen im Fehlercode nutzen, um die Zuverlässigkeit Ihrer Anwendungen zu verbessern. Im gerade beschriebenen Fall können Sie die `RetryAfterproperty` (vom Typ `RetryConditionHeaderValue`) nutzen, um den für die Wiederholung geeigneten Zeitpunkt zu berechnen.

Im Folgenden ein Beispiel für eine Daemonanwendung (unter Verwendung des Clientanmeldeinformations-Flows). Das Beispiel ist jedoch auf alle Methoden für den Tokenabruf anwendbar.

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
