---
title: Behandeln von Fehlern und Ausnahmen in MSAL.NET
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie Fehler und Ausnahmen, Anspruchsaufforderungen für den bedingten Zugriff und Wiederholversuche in MSAL.NET verarbeiten.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584043"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>Behandeln von Fehlern und Ausnahmen in MSAL.NET

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>Fehlerbehandlung in MSAL.NET

Bei der Verarbeitung von .NET-Ausnahmen können Sie den Ausnahmetyp selbst und das `ErrorCode`-Element verwenden, um zwischen Ausnahmen zu unterscheiden. Die `ErrorCode`-Werte sind Konstanten des Typs [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

Sie können auch die Felder von [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) und [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) überprüfen.

Wenn [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) ausgelöst wird, überprüfen Sie die [Authentifizierungs- und Autorisierungsfehlercodes](reference-aadsts-error-codes.md), um festzustellen, ob der Code dort aufgeführt wird.

### <a name="common-net-exceptions"></a>Häufige .NET-Ausnahmen

Dies sind die häufigsten Ausnahmen, die ausgelöst werden könnten, und einige mögliche Abhilfemaßnahmen:  

| Ausnahme | Fehlercode | Minderung|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: The user or administrator has not consented to use the application with ID '{appId}' named '{appName}'. (Der Benutzer oder Administrator hat nicht zugestimmt, die Anwendung mit der ID '{appId}' und dem Namen '{appName}' zu verwenden.) Send an interactive authorization request for this user and resource. (Senden Sie eine interaktive Autorisierungsanforderung für diesen Benutzer und die Ressource.)| Holen Sie zuvor die Benutzereinwilligung ein. Wenn Sie nicht .NET Core verwenden (das keine Webbenutzeroberfläche aufweist), rufen Sie (einmalig) `AcquireTokeninteractive` auf. Wenn Sie .NET Core verwenden oder `AcquireTokenInteractive` nicht ausführen möchten, kann der Benutzer zu einer URL navigieren, um seine Zustimmung zu geben: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`. Aufrufen von `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: Der Benutzer muss die [mehrstufige Authentifizierung (Multi-Factor Authentication, MFA)](../authentication/concept-mfa-howitworks.md) verwenden.| Es gibt keine Abhilfemaßnahme. Wenn MFA für Ihren Mandanten konfiguriert ist und Azure Active Directory (AAD) entscheidet, MFA zu erzwingen, greifen Sie auf einen interaktiven Flow wie `AcquireTokenInteractive` oder `AcquireTokenByDeviceCode` zurück.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: The grant type is not supported over the */common* or */consumers* endpoints. (Der Gewährungstyp wird für den /common- oder /consumers-Endpunkt nicht unterstützt.) Use the */organizations* or tenant-specific endpoint. (Verwenden Sie den /organizations-Endpunkt oder mandantenspezifischen Endpunkt.) You used */common*. (Sie haben /common verwendet.)| Wie in der Azure AD-Meldung erläutert, muss die Autorität über einen Mandanten oder ansonsten über einen */organizations*-Endpunkt verfügen.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: The request body must contain the following parameter: `client_secret or client_assertion`. (Der Anforderungstext muss den folgenden Parameter enthalten:)| Diese Ausnahme kann ausgelöst werden, wenn die Anwendung in Azure AD nicht als öffentliche Clientanwendung registriert wurde. Bearbeiten Sie im Azure-Portal das Manifest für Ihre Anwendung, und legen Sie `allowPublicClient` auf `true` fest. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Could not identify logged in user. (Der angemeldete Benutzer konnte nicht identifiziert werden.)| Die Bibliothek konnte den aktuellen, in Windows angemeldeten Benutzer nicht abfragen, oder der Benutzer ist nicht in AD oder Azure AD eingebunden (Benutzer, die über den Arbeitsplatz eingebunden sind, werden nicht unterstützt). Lösung 1: Vergewissern Sie sich auf der UWP, dass die Anwendung die folgenden Funktionen aufweist: Unternehmensauthentifizierung, private Netzwerke (Client und Server), Benutzerkonteninformationen. Lösung 2: Implementieren Sie eigene Logik zum Abrufen des Benutzernamens (z. B. john@contoso.com), und verwenden Sie die Methode in der Form `AcquireTokenByIntegratedWindowsAuth`, die den Benutzernamen annimmt.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Diese Methode basiert auf einem von Active Directory (AD) verfügbar gemachten Protokoll. Wenn ein Benutzer in Azure AD ohne AD-Unterstützung („verwalteter Benutzer“) erstellt wurde, schlägt diese Methode fehl. Benutzer, die in AD erstellt und von Azure AD unterstützt werden („Verbundbenutzer“), können von dieser nicht interaktiven Authentifizierungsmethode profitieren. Abhilfe: Verwenden Sie die interaktive Authentifizierung.|

### `MsalUiRequiredException`

Einer der allgemeinen Statuscodes, die von MSAL.NET beim Aufrufen von `AcquireTokenSilent()` zurückgegeben werden, ist`MsalError.InvalidGrantError`. Dieser Statuscode bedeutet, dass die Anwendung die Authentifizierungsbibliothek erneut, allerdings im interaktiven Modus, aufrufen muss (für AcquireTokenInteractive oder AcquireTokenByDeviceCodeFlow für öffentliche Clientanwendungen gibt es eine Abfrage in Web-Apps). Dies liegt daran, dass eine zusätzliche Benutzerinteraktion erforderlich ist, bevor ein Authentifizierungstoken ausgegeben werden kann.

Wenn bei `AcquireTokenSilent` ein Fehler auftritt, liegt dies in den meisten Fällen daran, dass der Tokencache keine Token für Ihre Anforderung hat. Zugriffstoken laufen innerhalb 1 Stunde ab, und `AcquireTokenSilent` wird versuchen, ein neues auf der Grundlage eines Aktualisierungstokens abzurufen (in OAuth2-Begriffen ist dies der „Tokenaktualisierungsflow“). Bei diesem Flow kann auch aus verschiedenen Gründen ein Fehler auftreten, wenn z. B. ein Mandantenadministrator strengere Anmelderichtlinien konfiguriert. 

Die Interaktion zielt darauf ab, dass der Benutzer eine Aktion durchführt. Einige dieser Bedingungen können von Benutzern leicht aufgelöst werden (z. B. Akzeptieren der Nutzungsbedingungen mit einem einzigen Mausklick), und einige dieser Bedingungen können mit der aktuellen Konfiguration nicht aufgelöst werden (z. B. muss der betreffende Computer eine Verbindung mit einem bestimmten Unternehmensnetzwerk herstellen). Einige helfen dem Benutzer beim Einrichten der mehrstufigen Authentifizierung oder Installieren von Microsoft Authenticator auf dem Gerät.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` Klassifizierungsenumeration

MSAL macht ein Feld `Classification` verfügbar, das Sie lesen können, um eine bessere Benutzerumgebung bereitzustellen. Mit diesem Feld kann dem Benutzer beispielsweise mitgeteilt werden, dass sein Kennwort abgelaufen ist, oder dass er der Verwendung einiger Ressourcen zustimmen muss. Die unterstützten Werte sind Teil der `UiRequiredExceptionClassification`-Enumeration:

| Klassifizierung    | Bedeutung           | Empfohlene Behandlung |
|-------------------|-------------------|----------------------|
| BasicAction | Die Bedingung kann während des interaktiven Authentifizierungsflows durch Benutzerinteraktion aufgelöst werden. | AcquireTokenInteractively() aufrufen. |
| AdditionalAction | Die Bedingung kann durch eine zusätzliche Abhilfemaßnahme mit dem System außerhalb des interaktiven Authentifizierungsflows aufgelöst werden. | AcquireTokenInteractively() aufrufen, um eine Nachricht anzuzeigen, die die Abhilfemaßnahme erklärt. Beim Aufrufen der Anwendung können Flows ausgeblendet werden, für die „additional_action“ erforderlich ist, wenn der Benutzer die Abhilfemaßnahme wahrscheinlich nicht ausführen kann. |
| MessageOnly      | Die Bedingung kann zu diesem Zeitpunkt nicht aufgelöst werden. Beim Starten des interaktiven Authentifizierungsflows wird eine Meldung angezeigt, in der die Bedingung erläutert wird. | AcquireTokenInteractively() aufrufen, um eine Nachricht anzuzeigen, die die Bedingung erklärt. AcquireTokenInteractively() gibt den UserCanceled-Fehler zurück, nachdem der Benutzer die Meldung gelesen und das Fenster geschlossen hat. Beim Aufrufen der Anwendung können Flows ausgeblendet werden, die zu „message_only“ führen, wenn der Benutzer von der Nachricht wahrscheinlich keinen Nutzen hat.|
| ConsentRequired  | Die Benutzerzustimmung ist nicht vorhanden oder wurde widerrufen. | AcquireTokenInteractively() aufrufen, damit der Benutzer seine Zustimmung erteilt. |
| UserPasswordExpired | Das Kennwort des Benutzers ist abgelaufen. | AcquireTokenInteractively() aufrufen, damit der Benutzer sein Kennwort zurücksetzen kann. |
| PromptNeverFailed| Die interaktive Authentifizierung wurde mit dem Parameter „prompt=never“ aufgerufen, wodurch MSAL gezwungen wird, sich auf Browsercookies zu verlassen und den Browser nicht anzuzeigen. Dabei ist ein Fehler aufgetreten. | AcquireTokenInteractively() ohne „Prompt.None“ aufrufen. |
| AcquireTokenSilentFailed | Das MSAL-SDK verfügt nicht über genügend Informationen, um ein Token aus dem Cache abzurufen. Dies kann daran liegen, dass keine Token im Cache vorhanden sind oder ein Konto nicht gefunden wurde. Weitere Informationen finden Sie in der Fehlermeldung.  | AcquireTokenInteractively() aufrufen. |
| Keine    | Es sind keine weiteren Informationen vorhanden. Die Bedingung könnte während des interaktiven Authentifizierungsflows durch Benutzerinteraktion aufgelöst werden. | AcquireTokenInteractively() aufrufen. |

## <a name="net-code-example"></a>Codebeispiel für .NET

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Wenn Sie eine API, die den bedingten Zugriff erfordert, von MSAL.NET aufrufen, muss Ihre Anwendung Ausnahmen bei Anspruchsaufforderungen behandeln. Die Ausnahme tritt in Form einer [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) auf, deren [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims)-Eigenschaft nicht leer ist.

Um die Anspruchsaufforderung zu behandeln, verwenden Sie die `.WithClaim()`-Methode der `PublicClientApplicationBuilder`-Klasse.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>HTTP-Fehlercodes 500–600

MSAL.NET implementiert einen einfachen einmaligen Wiederholungsmechanismus für Fehler mit den HTTP-Fehlercodes 500–600.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) gibt `System.Net.Http.Headers.HttpResponseHeaders` als `namedHeaders`-Eigenschaft aus. Sie können zusätzliche Informationen im Fehlercode nutzen, um die Zuverlässigkeit Ihrer Anwendungen zu verbessern. Im beschriebenen Fall können Sie die `RetryAfterproperty` (vom Typ `RetryConditionHeaderValue`) nutzen, um den für die Wiederholung geeigneten Zeitpunkt zu berechnen.

Im folgenden finden Sie ein Beispiel für eine Daemonanwendung, die den Clientanmeldeinformationenflow verwendet. Sie können dies an eine beliebige Methode zum Abrufen eines Tokens anpassen.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
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
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>Nächste Schritte

Ziehen Sie die Aktivierung der [Protokollierung in MSAL.NET](msal-logging-dotnet.md) in Betracht, um Probleme besser diagnostizieren und debuggen zu können.
