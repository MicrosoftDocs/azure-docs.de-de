---
title: Daemon-App, die Web-APIs aufruft (Aufrufen von Token für die App) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Daemon-App erstellen, die Web-APIs aufruft (beim Aufrufen von Token)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080166"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon-App, die Web-APIs aufruft – Aufruf eines Tokens

Sobald die vertrauliche Clientanwendung erstellt wurde, können Sie ein Token für die App abrufen, indem Sie ``AcquireTokenForClient`` aufrufen, den Bereich übergeben und ggf. die Aktualisierung des Tokens erzwingen.

## <a name="scopes-to-request"></a>Anzufordernde Bereiche

Der anzufordernde Bereich für einen Anmeldeinformationsfluss für Clients ist der Name der Ressource, gefolgt von `/.default`. Durch diese Notation weiß Azure AD, dass die **Berechtigungen auf Anwendungsebene** verwendet werden sollen, die im Zuge der Anwendungsregistrierung statisch deklariert wurden. Zudem müssen diese API-Berechtigungen, wie zuvor festgestellt, von einem Mandantenadministrator gewährt werden.

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

In MSAL.Python sähe die Konfigurationsdatei wie der folgende Codeausschnitt aus:

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>Alle

Der für die Clientanmeldeinformationen verwendete Bereich muss immer resourceId+"/.default" sein.

### <a name="case-of-v10-resources"></a>Anwendungsfall v1.0-Ressourcen

> [!IMPORTANT]
> Wenn MSAL (v2.0-Endpunkt) bei einem Zugriffstoken nach einer Ressource fragt, die ein v1.0-Zugriffstoken akzeptiert, analysiert Azure AD die gewünschte Zielgruppe aus dem angeforderten Bereich, indem alles vor dem letzten Schrägstrich als Ressourcenbezeichner verwendet wird.
> Wenn die Ressource wie Azure SQL ( **https://database.windows.net** ) daher eine Zielgruppe mit Schrägstrich am Ende erwartet (bei Azure SQL: `https://database.windows.net/` ), müssen Sie den Bereich `https://database.windows.net//.default` anfordern (beachten Sie den doppelten Schrägstrich). Weitere Informationen finden Sie unter dem MSAL.NET-Issue [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Resource url's trailing slash is omitted, which caused sql auth failure (Bei der URL der Ressource wurde der nachgestellte Schrägstrich entfernt, wodurch bei der SQL-Authentifizierung ein Fehler aufgetreten ist).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient-API

### <a name="net"></a>.NET

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

#### <a name="application-token-cache"></a>Anwendungstokencache

In MSAL.NET verwendet `AcquireTokenForClient` den **Anwendungstokencache** (alle anderen AcquireTokenXX-Methoden verwenden den Benutzertokencache). Rufen Sie `AcquireTokenSilent` nicht vor `AcquireTokenForClient` auf, da `AcquireTokenSilent` den **Benutzertokencache** verwendet. `AcquireTokenForClient` überprüft den **Anwendungstokencache** selbst und aktualisiert diesen.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

Wenn Sie noch über keine Bibliothek für die Programmiersprache Ihrer Wahl verfügen, möchten Sie das Protokoll möglicherweise direkt verwenden:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Erster Fall: Zugriffstokenanforderung mit einem gemeinsamen Geheimnis

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Zweiter Fall: Zugriffstokenanforderung mit einem Zertifikat

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

### <a name="learn-more-about-the-protocol"></a>Weitere Informationen zum Protokoll

Weitere Informationen finden Sie in der Protokolldokumentation: [Microsoft identity platform and the OAuth 2.0 client credentials flow (Microsoft Identity Platform und der OAuth 2.0-Fluss mit Clientanmeldeinformationen)](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="did-you-use-the-resourcedefault-scope"></a>Haben Sie den Bereich „resource/.default“ verwendet?

Wenn Ihnen eine Fehlermeldung angezeigt wird, nach der Sie einen ungültigen Bereich verwendet haben, haben Sie wahrscheinlich nicht den Bereich `resource/.default` verwendet.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Haben Sie die Administratoreinwilligung vergessen? Diese wird von Daemon-Apps benötigt!

Wenn Ihnen beim Aufruf der API die Fehlermeldung **Nicht genügend Berechtigungen zum Abschließen des Vorgangs** angezeigt wird, muss der Mandantenadministrator der Anwendung Berechtigungen gewähren. Weitere Informationen finden Sie weiter oben unter Schritt 6 des Abschnitts über die Registrierung der Client-App.
Ihnen wird in der Regel eine Fehlermeldung wie in der folgenden Beschreibung angezeigt:

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Daemon-App – Aufrufen einer Web-API](scenario-daemon-call-api.md)