---
title: Konfigurieren einer Web-App, die Web-APIs aufruft | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie den Code einer Web-App konfigurieren, die Web-APIs aufruft
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 2109705116c323fd3632b7230a81ccd9158c1a64
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582316"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Web-App, die Web-APIs aufruft: Codekonfiguration

Wie im Szenario [Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md) gezeigt, verwendet die Web-App den [OAuth 2.0-Autorisierungscodeflow](v2-oauth2-auth-code-flow.md), um den Benutzer anzumelden. Dieser Flow besteht aus zwei Schritten:

1. Anfordern eines Autorisierungscodes. Dieser Teil delegiert einen privaten Dialog mit dem Benutzer an Microsoft Identity Platform. Während des Dialogs meldet sich der Benutzer an und stimmt der Verwendung von Web-APIs zu. Wenn dieser private Dialog erfolgreich beendet wird, erhält die Web-App einen Autorisierungscode an ihren Umleitungs-URI.
1. Anfordern eines Zugriffstokens für die API durch Einlösen des Autorisierungscodes.

In den Szenarien [Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md) wurde nur der erste Schritt behandelt. Hier erfahren Sie, wie Sie Ihre Web-App so ändern, dass sie nicht nur Benutzer anmeldet, sondern auch Web-APIs aufruft.

## <a name="libraries-that-support-web-app-scenarios"></a>Bibliotheken mit Unterstützung für Web-App-Szenarien

Die folgenden Bibliotheken in der Microsoft-Authentifizierungsbibliothek (Microsoft Authentification Library, MSAL) unterstützen den Autorisierungscodeflow für Web-Apps:

| MSAL-Bibliothek | BESCHREIBUNG |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Unterstützung für .NET Framework- und .NET Core-Plattformen. Nicht unterstützt werden UWP (Universal Windows Platform), Xamarin.iOS und Xamarin.Android, da diese Plattformen zum Erstellen öffentlicher Clientanwendungen verwendet werden. <br/><br/>Für ASP.NET Core-Web-Apps und Web-APIs ist MSAL.NET in einer Bibliothek höherer Ebene namens [Microsoft.Identity.Web](https://aka.ms/ms-identity-web) gekapselt. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL für Python | Unterstützung für Python-Webanwendungen. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL für Java | Unterstützung für Java-Webanwendungen. |

Wählen Sie die Registerkarte für die Plattform aus, die Sie interessiert:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Geheime Clientschlüssel oder Clientzertifikate

Wenn Ihre Web-App jetzt eine Downstream-Web-API aufruft, geben Sie in der Datei *appsettings.json* einen geheimen Clientschlüssel oder ein Clientzertifikat an. Sie können auch einen Abschnitt hinzufügen, in dem Folgendes angegeben wird:

- Die URL der Downstream-Web-API
- Die zum Aufrufen der API erforderlichen Bereiche

Im folgenden Beispiel sind diese Einstellungen im Abschnitt `GraphBeta` angegeben.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
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
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
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

*Microsoft.Identity.Web* bietet verschiedene Möglichkeiten, Zertifikate zu beschreiben, sowohl durch Konfiguration als auch durch Code. Weitere Informationen finden Sie unter [Microsoft.Identity.Web: Verwenden von Zertifikaten](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) auf GitHub.

## <a name="startupcs"></a>Startup.cs

Ihre Web-App muss ein Token für die Downstream-API abrufen. Die Angabe erfolgt durch Hinzufügen der Zeile `.EnableTokenAcquisitionToCallDownstreamApi()` nach `.AddMicrosoftIdentityWebApi(Configuration)`. Diese Zeile macht den `ITokenAcquisition`-Dienst verfügbar, den Sie in Ihren Controller- und Seitenaktionen verwenden können. Wie jedoch aus den folgenden beiden Optionen hervorgeht, kann es einfacher gemacht werden. Sie müssen auch eine Tokencacheimplementierung (z. B. `.AddInMemoryTokenCaches()`) in *Startup.cs* auswählen:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

Die an `EnableTokenAcquisitionToCallDownstreamApi` übergebenen Bereiche sind optional und ermöglichen Ihrer Web-App das Anfordern der Bereiche und der Zustimmung des Benutzers zu diesen Bereichen bei der Anmeldung. Wenn Sie die Bereiche nicht angeben, aktiviert *Microsoft.Identity.Web* einen inkrementellen Zustimmungsprozess.

Wenn Sie das Token nicht selbst abrufen möchten, bietet *Microsoft.Identity.Web* zwei Mechanismen zum Aufrufen einer Web-API über eine Web-App. Welche Option Sie auswählen, hängt davon ab, ob Sie Microsoft Graph oder eine andere API aufrufen möchten.

### <a name="option-1-call-microsoft-graph"></a>Option 1: Aufrufen von Microsoft Graph

Wenn Sie Microsoft Graph aufrufen möchten, bietet *Microsoft.Identity.Web* die Möglichkeit, den (über das Microsoft Graph SDK verfügbar gemachten) `GraphServiceClient` in Ihren API-Aktionen direkt zu verwenden. Gehen Sie wie folgt vor, um Microsoft Graph verfügbar zu machen:

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
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Option 2: Aufrufen einer anderen Downstream-Web-API als Microsoft Graph

Zum Aufrufen einer anderen Web-API als Microsoft Graph stellt *Microsoft.Identity.Web* die Methode `.AddDownstreamWebApi()` bereit, die Token anfordert und die Downstream-Web-API aufruft.

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>Zusammenfassung

Wie bei Web-APIs können Sie verschiedene Tokencacheimplementierungen auswählen. Weitere Informationen finden Sie unter [Microsoft.Identity.Web: Tokencacheserialisierung](https://aka.ms/ms-id-web/token-cache-serialization) auf GitHub.

In der folgenden Abbildung sind die verschiedenen Möglichkeiten von *Microsoft.Identity.Web* und die Auswirkungen auf die Datei *Startup.cs* dargestellt:

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Blockdiagramm mit Dienstkonfigurationsoptionen in der Datei „Startup.cs“ zum Aufrufen einer Web-API und zum Angeben einer Tokencacheimplementierung":::

> [!NOTE]
> Um die hier aufgeführten Codebeispiele in vollem Umfang zu verstehen, müssen Sie mit den [ASP.NET Core-Grundlagen](/aspnet/core/fundamentals) und insbesondere mit der [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) und den [Optionen](/aspnet/core/fundamentals/configuration/options) vertraut sein.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Weil die Benutzeranmeldung an die Middleware von OpenID Connect (OIDC) delegiert wird, müssen Sie mit dem OIDC-Prozess interagieren. Diese Interaktion hängt von dem Framework ab, das Sie verwenden.

Bei ASP.NET Core abonnieren Sie OIDC-Middlewareereignisse:

- Sie lassen ASP.NET Core einen Autorisierungscode über die OpenID Connect-Middleware anfordern. ASP.NET bzw. ASP.NET Core ermöglicht die Anmeldung und Einwilligung des Benutzers.
- Sie abonnieren die Web-App, um den Autorisierungscode zu erhalten. Dieses Abonnement erfolgt mittels eines C#-Delegaten.
- Nach dem Empfang des Autorisierungscodes lösen Sie den Code mithilfe von MSAL-Bibliotheken ein. Die daraus resultierenden Zugriffstoken und Aktualisierungstoken werden im Tokencache gespeichert. Der Cache kann in anderen Teilen der Anwendung (z. B. in Controllern) verwendet werden, um im Hintergrund weitere Token abzurufen.

Die Codebeispiele in diesem und dem folgenden Artikel stammen aus dem [ASP.NET-Web-App-Beispiel](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Dieses Beispiel enthält auch ausführliche Informationen zur Implementierung.

# <a name="java"></a>[Java](#tab/java)

Die Codebeispiele in diesem und dem folgenden Artikel stammen aus dem Web-App-Beispiel [Java-Webanwendung für den Aufruf von Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), bei dem MSAL für Java verwendet wird.
Im Beispiel wird aktuell von MSAL für Java die Autorisierungscode-URL erzeugt und die Navigation zum Autorisierungsendpunkt für Microsoft Identity Platform gesteuert. Es ist auch möglich, die Sprint-Sicherheit für die Anmeldung des Benutzers zu verwenden. Das Beispiel enthält auch ausführliche Informationen zur Implementierung.

# <a name="python"></a>[Python](#tab/python)

Die Codebeispiele in diesem und dem folgenden Artikel stammen aus dem Web-App-Beispiel [Python-Webanwendung für den Aufruf von Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), bei dem MSAL.Python verwendet wird.
Im Beispiel wird derzeit von MSAL.Python die Autorisierungscode-URL erzeugt und die Navigation zum Autorisierungsendpunkt für Microsoft Identity Platform gesteuert. Das Beispiel enthält auch ausführliche Informationen zur Implementierung.

---

## <a name="code-that-redeems-the-authorization-code"></a>Code, der den Autorisierungscode einlöst

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web vereinfacht Ihren Code, indem es die richtigen OpenID-Verbindungseinstellungen festlegt, das Ereignis „Code erhalten“ abonniert und den Code einlöst. Zum Einlösen des Autorisierungscodes ist kein weiterer Code erforderlich. Ausführliche Informationen zur Funktionsweise finden Sie im [Microsoft.Identity.Web-Quellcode](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Die Verarbeitung durch ASP.NET ist mit der von ASP.NET Core vergleichbar, mit der Ausnahme, dass die Konfiguration von OpenID Connect und das Abonnement des `OnAuthorizationCodeReceived`-Ereignisses in der Datei [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) erfolgen. Die Konzepte sind ebenfalls mit denen von ASP.NET Core vergleichbar, mit der Ausnahme, dass Sie in ASP.NET den `RedirectUri` in [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15) angeben müssen. Diese Konfiguration ist etwas weniger stabil als die in ASP.NET Core, da Sie beim Bereitstellen Ihrer Anwendung Änderungen vornehmen müssen.

Der Code für „Startup.Auth.cs“ lautet wie folgt:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Lesen Sie [Web-App, die Benutzer anmeldet: Codekonfiguration](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code), um zu verstehen, wie das Java-Beispiel den Autorisierungscode abruft. Nachdem die App den Code erhalten hat, geht [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) wie folgt vor:

1. Delegierung an die `AuthHelper.processAuthenticationCodeRedirect`-Methode in [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Aufruf von `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

Die `getAuthResultByAuthCode`-Methode ist in [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176) definiert. Sie erstellt eine MSAL-`ConfidentialClientApplication` und ruft `acquireToken()` mit den aus dem Autorisierungscode erstellten `AuthorizationCodeParameters` auf.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

Der Authorisierungscodeflow wird angefordert, wie in [Web-App, die Benutzer anmeldet: Codekonfiguration](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code) gezeigt. Der Empfang des Codes erfolgt dann über die Funktion `authorized` über Flask-Routen von der `/getAToken`-URL. Den vollständigen Kontext dieses Codes finden Sie unter [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44):

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Die vertrauliche Clientanwendung kann ihre Identität statt mit einem geheimen Clientschlüssel auch mithilfe eines Clientzertifikats oder einer Clientassertion nachweisen.
Die Verwendung von Clientassertionen ist ein erweitertes Szenario, das unter [Clientassertionen](msal-net-client-assertions.md) detailliert beschrieben ist.

## <a name="token-cache"></a>Tokencache

> [!IMPORTANT]
> Die Tokencache-Implementierung für Web-Apps oder Web-APIs unterscheidet sich von der Implementierung für Desktopanwendungen, die häufig [dateibasiert](scenario-desktop-acquire-token.md#file-based-token-cache) ist.
> Aus Sicherheits- und Leistungsgründen ist es wichtig, sicherzustellen, dass es für Web-Apps und Web-APIs ein Tokencache pro Benutzerkonto gibt. Sie müssen den Tokencache für jedes Konto serialisieren.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Im ASP.NET Core-Tutorial wird die Abhängigkeitsinjektion verwendet, um Ihnen die Entscheidung über die Tokencache-Implementierung in der Datei „Startup.cs“ für Ihre Anwendung zu ermöglichen. Microsoft.Identity.Web enthält eine Reihe vordefinierter Tokencache-Serialisierungsmodule, die unter [Tokencacheserialisierung](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application) beschrieben sind. Eine interessante Möglichkeit besteht darin, [verteilte Arbeitsspeichercaches](/aspnet/core/performance/caching/distributed#distributed-memory-cache) von ASP.NET Core auszuwählen:

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Ausführliche Informationen zu den Tokencacheanbietern finden Sie im Artikel [Tokencacheserialisierung](https://aka.ms/ms-id-web/token-cache-serialization) von Microsoft.Identity.Web sowie in den [Tutorials zu ASP.NET Core-Web-Apps | Tokencaches](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) in der entsprechenden Phase des Web-App-Tutorials.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Die Tokencache-Implementierung für Web-Apps oder Web-APIs unterscheidet sich von der Implementierung für Desktopanwendungen, die häufig [dateibasiert](scenario-desktop-acquire-token.md#file-based-token-cache) ist.

Die Web-App-Implementierung kann die ASP.NET-Sitzung oder den Serverarbeitsspeicher verwenden. Unter [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51) können Sie beispielsweise sehen, wie die Implementierung des Caches nach der Erstellung der MSAL.NET-Anwendung verknüpft wird:

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java stellt Methoden zum Serialisieren und Deserialisieren des Tokencaches bereit. Das Java-Beispiel behandelt die Serialisierung aus der Sitzung, wie in der `getAuthResultBySilentFlow`-Methode in [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122) gezeigt:

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Die Details der `SessionManagementHelper`-Klasse werden im [MSAL-Beispiel für Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java) bereitgestellt.

# <a name="python"></a>[Python](#tab/python)

Im Python-Beispiel wird die Vorgabe „ein Cache pro Konto“ durch erneutes Erstellen einer vertraulichen Clientanwendung für jede Anforderung und anschließende Serialisierung im Flask-Sitzungscache sichergestellt:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Nächste Schritte

Wenn sich der Benutzer anmeldet, wird zu diesem Zeitpunkt ein Token im Tokencache gespeichert. Sehen wir uns an, wie dieses dann in anderen Teilen der Web-App verwendet wird.

[Entfernen von Konten aus dem Cache bei der globalen Abmeldung](scenario-web-app-call-api-sign-in.md)
