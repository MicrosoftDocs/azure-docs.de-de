---
title: Abrufen eines Tokens zum Aufrufen einer Web-API (Daemon-App) – Microsoft Identity Platform | Azure
description: Erfahren Sie, wie Sie eine Daemon-App erstellen, die Web-APIs aufruft (beim Aufrufen von Token)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c13b6ed991403e65c4c4d71c964f1f7f4d1ffe7b
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443312"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon-App, die Web-APIs aufruft – Aufruf eines Tokens

Nachdem die vertrauliche Clientanwendung erstellt wurde, können Sie ein Token für die App abrufen, indem Sie `AcquireTokenForClient` aufrufen, den Bereich übergeben und ggf. die Aktualisierung des Tokens erzwingen.

## <a name="scopes-to-request"></a>Anzufordernde Bereiche

Der anzufordernde Bereich für einen Anmeldeinformationsfluss für Clients ist der Name der Ressource, gefolgt von `/.default`. Durch diese Notation weiß Azure Active Directory (Azure AD), dass die *Berechtigungen auf Anwendungsebene* verwendet werden sollen, die im Zuge der Anwendungsregistrierung statisch deklariert wurden. Außerdem müssen diese API-Berechtigungen von einem Mandantenadministrator gewährt werden.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

In MSAL Python sieht die Konfigurationsdatei wie der folgende Codeausschnitt aus:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD-Ressourcen (v1.0)

Der für die Clientanmeldeinformationen verwendete Bereich muss immer die Ressourcen-ID gefolgt von `/.default` sein.

> [!IMPORTANT]
> Wenn MSAL ein Zugriffstoken für eine Ressource anfordert, die ein Version 1.0-Zugriffstoken akzeptiert, analysiert Azure AD die gewünschte Zielgruppe aus dem angeforderten Bereich, indem alles vor dem letzten Schrägstrich als Ressourcenbezeichner verwendet wird.
> Wenn also die Ressource wie z. B. bei Azure SQL-Datenbank (**https:\//database.windows.net**) eine Zielgruppe erwartet, die mit einem Schrägstrich endet (für Azure SQL-Datenbank `https://database.windows.net/`), müssen Sie als Bereich `https://database.windows.net//.default` anfordern. (Beachten Sie den doppelten Schrägstrich.) Weitere Informationen finden Sie unter dem MSAL.NET-Problem [747: Resource url's trailing slash is omitted, which caused sql auth failure](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747) (Bei der URL der Ressource wurde der nachgestellte Schrägstrich ausgelassen, wodurch bei der SQL-Authentifizierung ein Fehler aufgetreten ist).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient-API

Zum Abrufen eines Tokens für die App wird `AcquireTokenForClient` oder die Entsprechung für die jeweilige Plattform verwendet.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

Dies ist ein Codeauszug aus den [MSAL Java-Entwicklungsbeispielen](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Protocol

Wenn Sie noch nicht über eine Bibliothek für die Programmiersprache Ihrer Wahl verfügen, können Sie das Protokoll auch direkt verwenden:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Erster Fall: Zugreifen auf die Tokenanforderung mit einem freigegebenen Geheimnis

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Zweiter Fall: Zugreifen auf die Tokenanforderung mit einem Zertifikat

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Weitere Informationen finden Sie in der Protokolldokumentation: [Microsoft Identity Platform und der Fluss von OAuth 2.0-Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Anwendungstokencache

In MSAL.NET verwendet `AcquireTokenForClient` den Anwendungstokencache. (Alle anderen AcquireToken *XX*-Methoden verwenden den Benutzertokencache.) Rufen Sie `AcquireTokenSilent` nicht vor `AcquireTokenForClient` auf, da `AcquireTokenSilent` den *Benutzertokencache* verwendet. `AcquireTokenForClient` überprüft den *Anwendungstokencache* selbst und aktualisiert diesen.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="did-you-use-the-resourcedefault-scope"></a>Haben Sie den Bereich „resource/.default“ verwendet?

Wenn Ihnen eine Fehlermeldung angezeigt wird, nach der Sie einen ungültigen Bereich verwendet haben, haben Sie wahrscheinlich nicht den Bereich `resource/.default` verwendet.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Haben Sie die Administratoreinwilligung vergessen? Diese wird von Daemon-Apps benötigt!

Wenn Ihnen beim Aufruf der API die Fehlermeldung **Nicht genügend Berechtigungen zum Abschließen des Vorgangs** angezeigt wird, muss der Mandantenadministrator der Anwendung Berechtigungen gewähren. Weitere Informationen finden Sie weiter oben unter Schritt 6 des Abschnitts über die Registrierung der Client-App.
In der Regel wird ein Fehler ähnlich dem folgenden angezeigt:

```json
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

# <a name="net"></a>[.NET](#tab/dotnet)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Aufrufen einer Web-API](./scenario-daemon-call-api.md?tabs=dotnet).

# <a name="python"></a>[Python](#tab/python)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Aufrufen einer Web-API](./scenario-daemon-call-api.md?tabs=python).

# <a name="java"></a>[Java](#tab/java)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Aufrufen einer Web-API](./scenario-daemon-call-api.md?tabs=java).

---