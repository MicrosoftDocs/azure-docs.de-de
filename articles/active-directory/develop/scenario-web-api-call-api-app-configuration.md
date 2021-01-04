---
title: Konfigurieren einer Web-API, die Web-APIs aufruft | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine Web-API erstellen, die Web-APIs aufruft (Codekonfiguration der App)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4a888c3ad771e4a7edbd7110ba584050fe68e810
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443788"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Web-API, die Web-APIs aufruft: Codekonfiguration

Nachdem Sie Ihre Web-API registriert haben, können Sie den Code für die Anwendung konfigurieren.

Der Code, den Sie zum Konfigurieren der Web-API verwenden, sodass diese Downstream-Web-APIs aufruft, baut auf dem zum Schutz einer Web-API verwendeten Code auf. Weitere Informationen finden Sie unter [Geschützte Web-API: App-Konfiguration](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft.Identity.Web

Microsoft empfiehlt die Verwendung des NuGet-Pakets [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) beim Entwickeln einer geschützten ASP.NET Core-API, die Downstream-Web-APIs aufruft. Weitere Informationen finden Sie unter [Geschützte Web-API: Codekonfiguration | Microsoft.Identity.Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) finden Sie eine kurze Präsentation dieser Bibliothek im Kontext einer Web-API.

## <a name="client-secrets-or-client-certificates"></a>Geheime Clientschlüssel oder Clientzertifikate

Wenn Ihre Web-API jetzt eine Downstream-Web-API aufruft, müssen Sie in der Datei *appsettings.json* einen geheimen Clientschlüssel oder ein Clientzertifikat angeben. Sie können auch einen Abschnitt hinzufügen, in dem Folgendes angegeben wird:

- Die URL der Downstream-Web-API
- Die zum Aufrufen der API erforderlichen Bereiche

Im folgenden Beispiel sind diese Einstellungen im Abschnitt `GraphBeta` angegeben.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Statt eines geheimen Clientschlüssels können Sie ein Clientzertifikat angeben. Im folgenden Codeausschnitt ist die Verwendung eines in Azure Key Vault gespeicherten Zertifikats dargestellt.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft.Identity.Web bietet verschiedene Möglichkeiten, Zertifikate zu beschreiben, sowohl durch Konfiguration als auch durch Code. Weitere Informationen finden Sie unter [Microsoft.Identity.Web-Wiki: Verwenden von Zertifikaten](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) auf GitHub.

## <a name="startupcs"></a>Startup.cs

Ihre Web-API muss ein Token für die Downstream-API abrufen. Die Angabe erfolgt durch Hinzufügen der Zeile `.EnableTokenAcquisitionToCallDownstreamApi()` nach `.AddMicrosoftIdentityWebApi(Configuration)`. Diese Zeile macht den `ITokenAcquisition`-Dienst verfügbar, den Sie in Ihren Controller-/Seitenaktionen verwenden können. Wie jedoch aus den nächsten zwei Aufzählungspunkten hervorgeht, können Sie noch einfacher vorgehen. Sie müssen auch eine Tokencacheimplementierung (z. B. `.AddInMemoryTokenCaches()`) in *Startup.cs* auswählen:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Wenn Sie das Token nicht selbst abrufen möchten, bietet *Microsoft.Identity.Web* zwei Mechanismen zum Aufrufen einer Downstream-Web-API über eine andere API. Welche Option Sie auswählen, hängt davon ab, ob Sie Microsoft Graph oder eine andere API aufrufen möchten.

### <a name="option-1-call-microsoft-graph"></a>Option 1: Aufrufen von Microsoft Graph

Wenn Sie Microsoft Graph aufrufen möchten, bietet „Microsoft.Identity.Web“ die Möglichkeit, den (über das Microsoft Graph SDK verfügbar gemachten) `GraphServiceClient` in Ihren API-Aktionen direkt zu verwenden. Gehen Sie wie folgt vor, um Microsoft Graph verfügbar zu machen:

1. Fügen Sie Ihrem Projekt das NuGet-Paket [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) hinzu.
1. Fügen Sie in der Datei *Startup.cs* nach `.EnableTokenAcquisitionToCallDownstreamApi()` den Eintrag `.AddMicrosoftGraph()` hinzu. `.AddMicrosoftGraph()` verfügt über mehrere Überschreibungen. Wenn Sie die Überschreibung verwenden, die einen Konfigurationsabschnitt als Parameter annimmt, sieht der Code wie folgt aus:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Option 2: Aufrufen einer anderen Downstream-Web-API als Microsoft Graph

Zum Aufrufen einer anderen Downstream-API als Microsoft Graph stellt *Microsoft.Identity.Web* die Methode `.AddDownstreamWebApi()` bereit, die Token anfordert und die Downstream-Web-API aufruft.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Wie bei Web-Apps können Sie verschiedene Tokencacheimplementierungen auswählen. Weitere Informationen finden Sie unter [Microsoft.Identity.Web: Tokencacheserialisierung](https://aka.ms/ms-id-web/token-cache-serialization) auf GitHub.

In der folgenden Abbildung sind die verschiedenen Möglichkeiten von *Microsoft.Identity.Web* und die Auswirkungen auf die Datei *Startup.cs* dargestellt:

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Blockdiagramm mit Dienstkonfigurationsoptionen in der Datei „Startup.cs“ zum Aufrufen einer Web-API und zum Angeben einer Tokencacheimplementierung":::

> [!NOTE]
> Um die hier aufgeführten Codebeispiele in vollem Umfang zu verstehen, müssen Sie mit den [ASP.NET Core-Grundlagen](/aspnet/core/fundamentals) und insbesondere mit der [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) und den [Optionen](/aspnet/core/fundamentals/configuration/options) vertraut sein.

# <a name="java"></a>[Java](#tab/java)

Der On-Behalf-Of-Fluss (OBO) wird verwendet, um ein Token zum Aufrufen der Downstream-Web-API abzurufen. In diesem Flow empfängt Ihre Web-API ein Bearertoken mit den vom Benutzer delegierten Berechtigungen von der Clientanwendung und tauscht dieses Token dann mit einem anderen Zugriffstoken aus, um die Downstream-Web-API aufzurufen.

Der folgende Code verwendet den `SecurityContextHolder` des Spring Security-Frameworks in der Web-API, um das überprüfte Bearertoken abzurufen. Anschließend wird mithilfe der MSAL-Java-Bibliothek ein Token für die Downstream-API über einen `acquireToken`-Aufruf mit `OnBehalfOfParameters` abgerufen. MSAL speichert das Token zwischen, damit für nachfolgende Aufrufe der API `acquireTokenSilently` verwendet werden kann, um das zwischengespeicherte Token abzurufen.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Der On-Behalf-Of-Fluss (OBO) wird verwendet, um ein Token zum Aufrufen der Downstream-Web-API abzurufen. In diesem Flow empfängt Ihre Web-API ein Bearertoken mit den vom Benutzer delegierten Berechtigungen von der Clientanwendung und tauscht dieses Token dann mit einem anderen Zugriffstoken aus, um die Downstream-Web-API aufzurufen.

Eine Python-Web-API muss eine Middleware verwenden, um das vom Client empfangene Bearertoken zu überprüfen. Die Web-API kann dann das Zugriffstoken für die Downstream-API mithilfe der MSAL-Python-Bibliothek über einen Aufruf der [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of)-Methode abrufen. Ein Beispiel zur Verwendung dieser API finden Sie im [Testcode für microsoft-authentication-library-for-python in GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Siehe auch die Erörterung von [Issue 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) im selben Repository zum Ansatz, der die Notwendigkeit einer Anwendung auf mittlerer Ebene umgeht.

---

Ein Beispiel für die Implementierung des OBO-Flusses finden Sie auch unter [Node.js und Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol

Weitere Informationen zum OBO-Protokoll finden Sie unter [Microsoft Identity Platform und der On-Behalf-Of-Fluss von OAuth 2.0](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Abrufen eines Tokens für die App](scenario-web-api-call-api-acquire-token.md).
