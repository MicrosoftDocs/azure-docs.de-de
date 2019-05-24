---
title: Bewährte Methoden bei der Fehlerbehandlung von ADAL-Clients (Azure Active Directory Authentication Library)
description: Stellt Anleitungen für die Fehlerbehandlung und bewährten Methoden für ADAL-Clientanwendungen bereit.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 652175e99c800b8e4aa69c639f0bdb9aba838987
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544662"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Bewährte Methoden bei der Fehlerbehandlung von ADAL-Clients (Azure Active Directory Authentication Library)

Dieser Artikel enthält Anleitungen zum Typ von Fehlern, auf die Entwickler bei der Verwendung von ADAL für die Authentifizierung von Benutzern stoßen können. Bei der Verwendung von ADAL gibt es mehrere Fälle, in denen ein Entwickler eingreifen und Fehler behandeln muss. Die richtige Fehlerbehandlung sorgt für ein großartiges Endbenutzererlebnis und schränkt die Anzahl der Anmeldungen des Endbenutzers ein.

In diesem Artikel untersuchen wir die spezifischen Fälle für jede von ADAL unterstützte Plattform und wie Ihre Anwendung jeden Fall ordnungsgemäß handhaben kann. Der Fehlerleitfaden ist in zwei größere Kategorien unterteilt, die auf den Tokenabrufmustern basieren, die ADAL-APIs bereitstellen:

- **AcquireTokenSilent**: Der Client versucht, automatisch ein Token zu erhalten (ohne Benutzeroberfläche). Es tritt ggf. ein Fehler auf, wenn ADAL nicht erfolgreich ist. 
- **AcquireToken**: Der Client kann einen automatischen Abruf versuchen, aber auch interaktive Anforderungen ausführen, die eine Anmeldung erfordern.

> [!TIP]
> Es ist eine gute Idee, alle Fehler und Ausnahmen bei der Verwendung von ADAL und Azure AD zu protokollieren. Protokolle sind nicht nur hilfreich, um die allgemeine Integrität Ihrer Anwendung zu verstehen, sondern sind auch wichtig, wenn es darum geht, umfassendere Probleme zu beheben. Ihre Anwendung kann sich zwar von bestimmten Fehlern erholen, aber diese können auf umfassendere Entwurfsprobleme hinweisen, die Codeänderungen erfordern, um sie zu beheben. 
> 
> Wenn Sie die in diesem Dokument behandelten Fehlerbedingungen implementieren, sollten Sie den Fehlercode und die Beschreibung aus den oben genannten Gründen protokollieren. Beispiele für die Protokollierung von Code finden Sie unter [Fehler- und Protokollierungsreferenz](#error-and-logging-reference). 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent versucht, ein Token mit der Garantie abzurufen, dass dem Endbenutzer kein Benutzeroberfläche (UI) angezeigt wird. Es gibt mehrere Fälle, in denen der automatische Abruf fehlschlägt und die Abwicklung über interaktive Anforderungen oder einen Standardhandler erfolgen muss. In den folgenden Abschnitten gehen wir auf die Einzelheiten ein, wann und wie die einzelnen Fälle anzuwenden sind.

Es gibt eine Reihe von Fehlern, die vom Betriebssystem generiert werden und die eine anwendungsspezifische Fehlerbehandlung erfordern können. Weitere Informationen finden Sie im Abschnitt „Betriebssystemfehler“ unter [Fehler- und Protokollierungsreferenz](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Anwendungsszenarien

- [Native Clientanwendungen](developer-glossary.md#native-client) (iOS, Android, .NET Desktop oder Xamarin)
- [Webclientanwendungen](developer-glossary.md#web-client), die eine [Ressource](developer-glossary.md#resource-server) aufrufen (.NET)

### <a name="error-cases-and-actionable-steps"></a>Fehlerfälle und umsetzbare Schritte

Grundsätzlich gibt es zwei Fälle von AcquireTokenSilent-Fehlern:

| Fall | BESCHREIBUNG |
|------|-------------|
| **Fall 1**: Fehler ist mit einer interaktiven Anmeldung auflösbar | Bei Fehlern, die durch das Fehlen gültiger Token verursacht werden, ist eine interaktive Anforderung erforderlich. Insbesondere für die Cachesuche und ein ungültiges/abgelaufenes Aktualisierungstoken ist ein AcquireToken-Aufruf erforderlich.<br><br>In diesen Fällen muss der Endbenutzer aufgefordert werden, sich anzumelden. Die Anwendung kann wählen, ob sie eine interaktive Anforderung sofort, nach der Interaktion mit dem Endbenutzer (z.B. durch Drücken einer Anmeldeschaltfläche) oder später ausführen möchte. Die Auswahl hängt vom gewünschten Verhalten der Anwendung ab.<br><br>Weitere Informationen finden Sie im Code im folgenden Abschnitt für diesen speziellen Fall und die Fehler, die ihn diagnostizieren.|
| **Fall 2**: Fehler ist nicht mit einer interaktiven Anmeldung auflösbar | Bei Netzwerk- und vorübergehenden/temporären Fehlern oder anderen Fehlern löst die Ausführung einer interaktiven AcquireToken-Anforderung das Problem nicht. Unnötige interaktive Anmeldeaufforderungen können Endbenutzer auch stören. ADAL versucht automatisch einen einzigen Wiederholungsversuch für die meisten Fehler bei AcquireTokenSilent-Fehlern.<br><br>Die Clientanwendung kann auch zu einem späteren Zeitpunkt einen Wiederholungsversuch ausführen, aber wann und wie dieser auszuführen ist, hängt vom Anwendungsverhalten und der gewünschten Endbenutzererfahrung ab. Beispielsweise kann die Anwendung nach einigen Minuten oder als Reaktion auf eine Aktion des Endbenutzers einen Wiederholungsversuch von AcquireTokenSilent ausführen. Ein sofortiger Wiederholungsversuch führt dazu, dass die Anwendung gedrosselt wird und sollte nicht unternommen werden.<br><br>Ein nachfolgender Wiederholungsversuch, der mit dem gleichen Fehler fehlschlägt, bedeutet nicht, dass der Client eine interaktive Anforderung mit AcquireToken ausführen sollte, da dies den Fehler nicht behebt.<br><br>Weitere Informationen finden Sie im Code im folgenden Abschnitt für diesen speziellen Fall und die Fehler, die ihn diagnostizieren. |

### <a name="net"></a>.NET

Die folgende Anleitung enthält Beispiele für die Fehlerbehandlung in Verbindung mit ADAL-Methoden: 

- acquireTokenSilentAsync(…)
- acquireTokenSilentSync(…) 
- [Veraltet] acquireTokenSilent(…)
- [Veraltet] acquireTokenByRefreshToken(…) 

Der Code würde wie folgt implementiert werden:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

Die folgende Anleitung enthält Beispiele für die Fehlerbehandlung in Verbindung mit ADAL-Methoden: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- [Veraltet] acquireTokenSilent(…)

Der Code würde wie folgt implementiert werden:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

Die folgende Anleitung enthält Beispiele für die Fehlerbehandlung in Verbindung mit ADAL-Methoden: 

- acquireTokenSilentWithResource(…)

Der Code würde wie folgt implementiert werden:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken ist die ADAL Standardmethode, die zum Abrufen von Token verwendet wird. In den Fällen, in denen eine Benutzeridentität erforderlich ist, versucht AcquireToken zuerst, automatisch ein Token zu erhalten und zeigt dann ggf. die Benutzeroberfläche an (es sei denn, PromptBehavior.Never wird übergeben). In Fällen, in denen eine Anwendungsidentität erforderlich ist, versucht AcquireToken, ein Token abzurufen, zeigt aber keine Benutzeroberfläche an, da es keinen Endbenutzer gibt. 

Bei der Behandlung von AcquireToken-Fehlern hängt die Fehlerbehandlung von der Plattform und dem Szenario ab, das die Anwendung zu erreichen versucht. 

Das Betriebssystem kann auch eine Reihe von Fehlern generieren, die abhängig von der jeweiligen Anwendung eine Fehlerbehandlung erfordern. Weitere Informationen finden Sie unter „Betriebssystemfehler“ in der [Fehler- und Protokollierungsreferenz](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Anwendungsszenarien

- Native Clientanwendungen (iOS, Android, .NET Desktop oder Xamarin)
- Webanwendungen, die eine Ressourcen-API aufrufen (.NET)
- Single-Page-Webanwendungen (JavaScript)
- Service-to-Service-Anwendungen (.NET, Java)
  - Alle Szenarien einschließlich „Im Auftrag von“
  - „Im Auftrag von“-spezifische Szenarien

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Fehlerfälle und umsetzbare Schritte: Native Clientanwendungen

Wenn Sie eine native Clientanwendung erstellen, müssen Sie einige Fehlerbehandlungsfälle berücksichtigen, die sich auf Netzwerkprobleme, vorübergehende Fehler und andere plattformspezifische Fehler beziehen. In den meisten Fällen sollte eine Anwendung keine sofortigen Wiederholungsversuche ausführen, sondern auf die Interaktion mit dem Endbenutzer warten, die zu einer Anmeldung auffordert. 

Es gibt einige Sonderfälle, in denen ein einziger Wiederholungsversuch das Problem lösen kann. Beispielsweise, wenn ein Benutzer Daten auf einem Gerät aktivieren muss oder den Download des Azure AD-Brokers nach dem anfänglichen Fehler abgeschlossen hat. 

Im Fehlerfall kann eine Anwendung eine Benutzeroberfläche anzeigen, die es dem Endbenutzer ermöglicht, eine Interaktion auszuführen, die zu einem Wiederholungsversuch auffordert. Wenn auf einem Gerät z.B. ein Offlinefehler aufgetreten ist, wird über die Schaltfläche „Versuchen Sie, sich erneut anzumelden“ ein AcquireToken-Wiederholungsversuch gestartet, anstatt den Fehler sofort zu wiederholen. 

Die Fehlerbehandlung in nativen Anwendungen kann durch zwei Fälle definiert werden:

|  |  |
|------|-------------|
| **Fall 1**:<br>Nicht wiederholbarer Fehler (Mehrzahl der Fälle) | 1. Versuchen Sie keinen sofortigen Wiederholungsversuch. Zeigen Sie die Benutzeroberfläche des Endbenutzers auf der Grundlage des spezifischen Fehlers an, der einen Wiederholungsversuch auslöst („Versuchen Sie, sich erneut anzumelden“, „Azure AD-Brokeranwendung herunterladen“ usw.). |
| **Fall 2**:<br>Wiederholbarer Fehler | 1. Führen Sie einen einzelnen Wiederholungsversuch aus. Möglicherweise hat der Endbenutzer einen Status eingegeben, der zu einem Erfolg führt.<br><br>2. Wenn der Wiederholungsversuch fehlschlägt, zeigen Sie die Benutzeroberfläche des Endbenutzers auf der Grundlage des spezifischen Fehlers an, der einen Wiederholungsversuch auslöst („Versuchen Sie, sich erneut anzumelden“, „Azure AD-Brokeranwendung herunterladen“ usw.). |

> [!IMPORTANT]
> Wenn ein Benutzerkonto in einem automatischen Aufruf an ADAL übergeben wird und fehlschlägt, ermöglicht die anschließende interaktive Anforderung dem Endbenutzer, sich mit einem anderen Konto anzumelden. Nach einem erfolgreichen AcquireToken mit einem Benutzerkonto muss die Anwendung überprüfen, ob der angemeldete Benutzer mit dem lokalen Benutzerobjekt der Anwendung übereinstimmt. Eine Nichtübereinstimmung generiert keine Ausnahme (außer in Objective-C), sollte aber in Fällen berücksichtigt werden, in denen ein Benutzer lokal vor den Authentifizierungsanforderungen bekannt ist (wie bei einem fehlgeschlagenen automatischen Aufruf).
>

#### <a name="net"></a>.NET

Die folgende Anleitung enthält Beispiele für die Fehlerbehandlung in Verbindung mit allen nicht automatischen ADAL-Methoden vom Typ AcquireToken(...), *ausgenommen*: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion,…)   

Der Code würde wie folgt implementiert werden:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> Für ADAL.NET gibt es eine zusätzliche Überlegung, da PromptBehavior.Never mit einem Verhalten wie AcquireTokenSilent unterstützt wird.
>

Die folgende Anleitung enthält Beispiele für die Fehlerbehandlung in Verbindung mit ADAL-Methoden: 

- acquireToken(…, PromptBehavior.Never)

Der Code würde wie folgt implementiert werden:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Die folgende Anleitung enthält Beispiele für die Fehlerbehandlung in Verbindung mit allen nicht automatischen ADAL-Methoden vom Typ AcquireToken(...). 

Der Code würde wie folgt implementiert werden:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Die folgende Anleitung enthält Beispiele für die Fehlerbehandlung in Verbindung mit allen nicht automatischen ADAL-Methoden vom Typ AcquireToken(...). 

Der Code würde wie folgt implementiert werden:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Fehlerfälle und umsetzbare Schritte: Webanwendungen, die eine Ressourcen-API aufrufen (.NET)

Wenn Sie eine .NET-Webanwendung erstellen, die ein Token mit einem Autorisierungscode für eine Ressource abruft, ist der einzige Code, der erforderlich ist, ein Standardhandler für den generischen Fall. 

Die folgende Anleitung enthält Beispiele für die Fehlerbehandlung in Verbindung mit ADAL-Methoden: 

- AcquireTokenByAuthorizationCodeAsync(…)

Der Code würde wie folgt implementiert werden:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Fehlerfälle und umsetzbare Schritte: Single-Page-Webanwendungen (adal.js)

Wenn Sie eine Single-Page-Webanwendung mit „adal.js“ mit AcquireToken erstellen, ist der Fehlerbehandlungscode ähnlich dem eines typischen automatischen Aufrufs. Insbesondere in „adal.js“ zeigt AcquireToken nie eine Benutzeroberfläche an. 

Für ein fehlgeschlagenes AcquireToken gelten die folgenden Fällen:

|  |  |
|------|-------------|
| **Fall 1**:<br>Mit einer interaktiven Anforderung auflösbar | 1. Wenn bei login() ein Fehler auftritt, führen Sie keinen sofortigen Wiederholungsversuch aus. Wiederholen Sie den Aufruf erst, nachdem eine Benutzeraktion zu einem Wiederholungsversuch auffordert.|
| **Fall 2**:<br>Nicht mit einer interaktiven Anforderung auflösbar. Fehler ist wiederholbar. | 1. Führen Sie einen einzelnen Wiederholungsversuch aus. Möglicherweise hat der Endbenutzer einen Status eingegeben, der zu einem Erfolg führt.<br><br>2. Wenn der Wiederholungsversuch fehlschlägt, zeigen Sie dem Endbenutzer eine Aktion auf der Grundlage des spezifischen Fehlers an, der einen Wiederholungsversuch auslösen kann („Versuchen Sie, sich erneut anzumelden“). |
| **Fall 3**:<br>Nicht mit einer interaktiven Anforderung auflösbar. Fehler ist nicht wiederholbar. | 1. Versuchen Sie keinen sofortigen Wiederholungsversuch. Zeigen Sie dem Endbenutzer eine Aktion an, die auf dem spezifischen Fehler basiert, der einen Wiederholungsversuch auslösen kann („Versuchen Sie, sich erneut anzumelden“"). |

Der Code würde wie folgt implementiert werden:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Fehlerfälle und umsetzbare Schritte: Service-to-Service-Anwendungen (nur .NET)

Wenn Sie eine Service-to-Service-Anwendung erstellen, die AcquireToken verwendet, gibt es einige wichtige Fehler, die Ihr Code behandeln muss. Die einzige Möglichkeit, den Fehler aufzulösen, besteht darin, den Fehler an die aufrufende Anwendung zurückzugeben (für Fälle des Typs „Im Auftrag von“) oder eine Wiederholungsstrategie anzuwenden. 

#### <a name="all-scenarios"></a>Alle Szenarien

Für *alle* Service-to-Service-Anwendungsszenarien, einschließlich „Im Auftrag von“:

- Versuchen Sie keinen sofortigen Wiederholungsversuch. ADAL versucht einen einzelnen Wiederholungsversuch für bestimmte fehlgeschlagene Anforderungen. 
- Setzen Sie den Wiederholungsversuch erst dann fort, wenn eine Benutzer- oder App-Aktion zu einer Wiederholung auffordert. Beispielsweise sollte eine Daemonanwendung, die mit einem bestimmten Intervall arbeitet, bis zum nächsten Intervall warten, bevor ein erneuter Versuch unternommen wird.

Die folgende Anleitung enthält Beispiele für die Fehlerbehandlung in Verbindung mit ADAL-Methoden: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion, …)

Der Code würde wie folgt implementiert werden:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>„Im Auftrag von“-Szenarien

Für *Im Auftrag von*-Szenarien von Service-to-Service-Anwendungen.

Die folgende Anleitung enthält Beispiele für die Fehlerbehandlung in Verbindung mit ADAL-Methoden: 

- AcquireTokenAsync(…, UserAssertion, …)

Der Code würde wie folgt implementiert werden:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Wir haben ein [vollständiges Beispiel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) erstellt, das dieses Szenario veranschaulicht.

## <a name="error-and-logging-reference"></a>Fehler- und Protokollierungsreferenz

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Protokollieren von personenbezogenen Informationen (PII) und organisationsbezogenen Informationen (OII)
Standardmäßig werden bei der ADAL-Protokollierung keine personen- oder organisationsbezogenen Informationen erfasst. Die Bibliothek erlaubt App-Entwicklern aber, dies über einen Setter in der Protokollierungsklasse zu aktivieren. Nach der Aktivierung von personen- und organisationsbezogenen Daten ist die App für die sichere Verarbeitung hochgradig sensibler Daten und die Einhaltung von gesetzlichen Anforderungen verantwortlich.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Fehler der ADAL-Bibliothek

Um spezifische ADAL-Fehler zu untersuchen, stellt der Quellcode im [azure-activedirectory-library-for-dotnet-Repository](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) die beste Fehlerreferenz dar.

#### <a name="guidance-for-error-logging-code"></a>Leitfaden für Fehlerprotokollierungscode

Die ADAL .NET-Protokollierung ändert sich abhängig von der Plattform, auf der gearbeitet wird. Code zum Aktivieren der Protokollierung finden Sie im [Protokollierungs-Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net).

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Fehler der ADAL-Bibliothek

Um spezifische ADAL-Fehler zu untersuchen, stellt der Quellcode im [azure-activedirectory-library-for-dotnet-Repository](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) die beste Fehlerreferenz dar.

#### <a name="operating-system-errors"></a>Betriebssystemfehler

Android-Betriebssystemfehler werden durch AuthenticationException in ADAL verfügbar gemacht, sind als "SERVER_INVALID_REQUEST" identifizierbar und können durch die Fehlerbeschreibungen weiter differenziert werden. 

Eine vollständige Liste häufiger Fehler und die Schritte, die beim Auftreten dieser Fehler in Ihrer App oder beim Endbenutzer ausgeführt werden sollten, finden Sie im [ADAL-Android-Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Leitfaden für Fehlerprotokollierungscode

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>Fehler der ADAL-Bibliothek

Um spezifische ADAL-Fehler zu untersuchen, stellt der Quellcode im [azure-activedirectory-library-for-obj-Repository](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) die beste Fehlerreferenz dar.

#### <a name="operating-system-errors"></a>Betriebssystemfehler

iOS-Fehler können während der Anmeldung auftreten, wenn Benutzer Webansichten verwenden, oder durch die Art der Authentifizierung. Dies kann durch Bedingungen wie SSL-Fehler, Timeouts oder Netzwerkfehler verursacht werden:

- Bei der Berechtigungsfreigabe sind die Anmeldungen nicht persistent, und der Cache erscheint leer. Sie können das Problem beheben, indem Sie der Keychain die folgende Codezeile hinzufügen: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Bei der NsUrlDomain-Fehlermenge ändert sich die Aktion in Abhängigkeit von der Anwendungslogik. Spezifische Instanzen, die verarbeitet werden können, finden Sie in der [NSURLErrorDomain-Referenzdokumentation](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations).
- Die Liste der häufigsten Fehler, die vom ADAL Objective-C-Team verwaltet werden, finden Sie unter [Häufige ADAL Objective-C-Probleme](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror).

#### <a name="guidance-for-error-logging-code"></a>Leitfaden für Fehlerprotokollierungscode

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Leitfaden für Fehlerprotokollierungscode: JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```
## <a name="related-content"></a>Verwandte Inhalte

* [Entwicklerhandbuch zu Azure AD][AAD-Dev-Guide]
* [Azure AD-Authentifizierungsbibliotheken][AAD-Auth-Libraries]
* [Azure AD-Authentifizierungsszenarien][AAD-Auth-Scenarios]
* [Integrieren von Anwendungen in Azure Active Directory][AAD-Integrating-Apps]

Nutzen Sie den folgenden Kommentarabschnitt, um uns Feedback zu geben und uns dabei zu helfen, unsere Inhalte zu optimieren und zu gestalten.

[![Anmeldungsschaltfläche][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

