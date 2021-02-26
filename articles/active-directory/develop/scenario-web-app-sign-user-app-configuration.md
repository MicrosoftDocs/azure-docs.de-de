---
title: Konfigurieren einer Web-App, die Benutzer anmeldet | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Benutzer anmeldet (Codekonfiguration)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 54caea62feed6ae7c082a979901999a5dcb3bd71
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582246"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Web-App für Benutzeranmeldungen: Codekonfiguration

Erfahren Sie, wie Sie den Code für Ihre Web-App zur Benutzeranmeldung konfigurieren.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotheken zum Schützen von Web-Apps

<!-- This section can be in an include for web app and web APIs -->
Die folgenden Bibliotheken werden zum Schützen einer Web-App (und einer Web-API) verwendet:

| Plattform | Bibliothek | BESCHREIBUNG |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_NET.png) | [Identitätsmodellerweiterungen für .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Die Microsoft Identity-Modellerweiterung für .NET schlägt bei der direkten Verwendung durch ASP.NET und ASP.NET Core eine Reihe von DLLs vor, die unter .NET Framework und .NET Core ausgeführt werden. Über eine ASP.NET-/ASP.NET Core-Web-App können Sie mithilfe der **TokenValidationParameters**-Klasse die Tokenvalidierung steuern (insbesondere in einigen Partnerszenarien). In der Praxis ist die Komplexität in der [Microsoft.Identity.Web](https://aka.ms/ms-identity-web)-Bibliothek gekapselt. |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Unterstützung für Java-Webanwendungen |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Unterstützung für Python-Webanwendungen |

Wählen Sie die Registerkarte aus, die der gewünschten Plattform entspricht:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Die Codeausschnitte in diesem und den folgenden Artikeln stammen aus [Kapitel 1 des inkrementellen Tutorials zu ASP.NET Core-Web-Apps](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

In diesem Tutorial finden Sie auch ausführliche Informationen zur Implementierung.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Die Codeausschnitte in diesem und den folgenden Artikeln stammen aus dem [ASP.NET-Web-App-Beispiel](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Dieses Beispiel enthält auch ausführliche Informationen zur Implementierung.

# <a name="java"></a>[Java](#tab/java)

Die Codeausschnitte in diesem und den folgenden Artikeln stammen aus dem MSAL-Java-Beispiel [Java-Webanwendung für den Aufruf von Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp).

Dieses Beispiel enthält auch ausführliche Informationen zur Implementierung.

# <a name="python"></a>[Python](#tab/python)

Die Codeausschnitte in diesem und den folgenden Artikeln stammen aus dem MSAL-Python-Beispiel [Python-Webanwendung für den Aufruf von Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp).

Dieses Beispiel enthält auch ausführliche Informationen zur Implementierung.

---

## <a name="configuration-files"></a>Konfigurationsdateien

Webanwendungen, die Benutzer mithilfe der Microsoft Identity Platform anmelden, werden mithilfe von Konfigurationsdateien konfiguriert. In der Konfiguration müssen Sie die folgenden Werte angeben:

- Die Cloudinstanz (`Instance`), wenn Ihre App beispielsweise in nationalen Clouds ausgeführt werden soll
- Die Zielgruppe in der Mandanten-ID (`TenantId`)
- Die Client-ID (`ClientId`) für Ihre Anwendung, die aus dem Azure-Portal kopiert wird

Möglicherweise werden auch Verweise auf die `Authority` angezeigt. Der Wert `Authority` wird aus der Verkettung der Werte `Instance` und `TenantId` gebildet.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core befinden sich diese Einstellungen in der Datei [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) im Abschnitt „AzureAd“.

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

In ASP.NET Core gibt es eine weitere Datei ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)), die die URL (`applicationUrl`) und den TLS-/SSL-Port (`sslPort`) für Ihre Anwendung und verschiedenen Profile enthält.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Im Azure-Portal müssen die Umleitungs-URIs, die Sie auf der Seite **Authentifizierung** für Ihre Anwendung registrieren, mit diesen URLs übereinstimmen. Für die beiden obigen Konfigurationsdateien wäre dies `https://localhost:44321/signin-oidc`. Der Grund hierfür ist, dass `applicationUrl` zwar `http://localhost:3110` ist, aber `sslPort` angegeben wird (44321). `CallbackPath` ist `/signin-oidc`, wie in `appsettings.json`definiert.

Auf die gleiche Weise wird der Abmelde-URI auf `https://localhost:44321/signout-oidc` festgelegt.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET wird die Anwendung über die Zeilen 12 bis 15 der Datei [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) konfiguriert.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Im Azure-Portal müssen die Antwort-URIs, die Sie auf der Seite **Authentifizierung** für Ihre Anwendung registrieren, mit diesen URLs übereinstimmen. Das heißt, sie sollten `https://localhost:44326/` sein.

# <a name="java"></a>[Java](#tab/java)

In Java befindet sich die Konfiguration in der Datei [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) unter `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Im Azure-Portal müssen die Antwort-URIs, die Sie auf der Seite **Authentifizierung** für Ihre Anwendung registrieren, mit den `redirectUri`-Instanzen übereinstimmen, die von der Anwendung definiert werden. Das heißt, sie sollten `http://localhost:8080/msal4jsample/secure/aad` und `http://localhost:8080/msal4jsample/graph/me` sein.

# <a name="python"></a>[Python](#tab/python)

Hier finden Sie die Python-Konfigurationsdatei in [app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> In dieser Schnellstartanleitung wird vorgeschlagen, den geheimen Clientschlüssel der Einfachheit halber in der Konfigurationsdatei zu speichern. In Ihrer Produktions-App sollten Sie andere Möglichkeiten zum Speichern Ihres Geheimnisses, z. B. einen Schlüsseltresor oder eine Umgebungsvariable, verwenden, wie in der [Flask-Dokumentation](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables) beschrieben.
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Initialisierungscode

Der Initialisierungscode ist je nach Plattform unterschiedlich. Für ASP.NET Core und ASP.NET wird die Anmeldung von Benutzern an die OpenID Connect-Middleware delegiert. Die ASP.NET-/ASP.NET Core-Vorlage generiert Webanwendungen für den Azure AD v1.0-Endpunkt (Azure Active Directory). Es müssen Konfigurationsänderungen vorgenommen werden, um diese an Microsoft Identity Platform anzupassen. Bei Java erfolgt dies von Spring in Zusammenarbeit mit der Anwendung.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core-Web-Apps (und Web-APIs) ist die Anwendung geschützt, da es das Attribut `[Authorize]` für die Controller bzw. Controlleraktionen gibt. Mit diesem Attribut wird geprüft, ob der Benutzer authentifiziert ist. Der Code für die Anwendungsinitialisierung befindet sich in der Datei *Startup.cs*.

Zum Hinzufügen der Authentifizierung mit der Microsoft Identity Platform (ehemals Azure AD v2.0) müssen Sie den folgenden Code hinzufügen. Die Kommentare im Code sollten selbsterklärend sein.

> [!NOTE]
> Wenn Sie direkt mit den neuen ASP.NET Core-Vorlagen für Microsoft Identity Platform, die Microsoft.Identity.Web nutzen, beginnen möchten, können Sie ein NuGet-Vorschaupaket mit Projektvorlagen für .NET Core 3.1 und .NET 5.0 herunterladen. Nach der Installation können Sie dann direkt ASP.NET Core-Webanwendungen (MVC oder Blazor) instanziieren. Weitere Informationen finden Sie unter [Microsoft.Identity.Web – Web-App-Projektvorlage](https://aka.ms/ms-id-web/webapp-project-templates). Dies ist der einfachste Ansatz, da er alle folgenden Schritte für Sie ausführt.
>
> Wenn Sie Ihr Projekt lieber mit dem aktuellen ASP.NET Core-Standardwebprojekt in Visual Studio oder mithilfe von `dotnet new mvc --auth SingleAuth` oder `dotnet new webapp --auth SingleAuth` starten möchten, wird Code ähnlich dem folgenden angezeigt:
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> In diesem Code wird das ältere NuGet-Paket **Microsoft.AspNetCore.Authentication.AzureAD.UI** verwendet, das zum Erstellen einer Azure AD v 1.0-Anwendung verwendet wird. In diesem Artikel wird erläutert, wie Sie eine Microsoft Identity Platform-Anwendung (Azure AD v2.0) erstellen, die diesen Code ersetzt.
>

1. Fügen Sie dem Projekt die NuGet-Pakete [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) und [Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) hinzu. Entfernen Sie, sofern vorhanden, das NuGet-Paket „Microsoft.AspNetCore.Authentication.AzureAD.UI“.

2. Aktualisieren Sie den Code in `ConfigureServices`, sodass er die Methoden `AddMicrosoftIdentityWebAppAuthentication` und `AddMicrosoftIdentityUI` verwendet.

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. Aktivieren Sie in der `Configure`-Methode in der Datei *Startup.cs* die Authentifizierung mit einem Aufruf von `app.UseAuthentication();`.

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

Im obigen Code gilt Folgendes:
- Die `AddMicrosoftIdentityWebAppAuthentication`-Erweiterungsmethode ist im Paket **Microsoft.Identity.Web** definiert. Sie hat folgende Aufgaben:
  - Hinzufügen des Authentifizierungsdiensts
  - Konfigurieren von Optionen zum Lesen der Konfigurationsdatei (hier aus dem Abschnitt „AzureAD“)
  - Konfigurieren der OpenID Connect-Optionen, damit Microsoft Identity Platform die verwendete Autorität ist
  - Validieren des Ausstellers des Tokens
  - Sicherstellen der Zuordnung der dem Namen entsprechenden Ansprüche aus dem Anspruch `preferred_username` im ID-Token

- Neben dem Konfigurationsobjekt können Sie beim Aufruf von `AddMicrosoftIdentityWebAppAuthentication` auch den Namen des Konfigurationsabschnitts angeben. Dieser lautet standardmäßig `AzureAd`.

- `AddMicrosoftIdentityWebAppAuthentication` weist andere Parameter für erweiterte Szenarien auf. Das Verfolgen von Ereignissen der OpenID Connect-Middleware beispielsweise kann bei der Behebung von Fehlern bei Ihrer Webanwendung helfen, wenn die Authentifizierung nicht funktioniert. Wenn Sie den optionalen Parameter `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` auf `true` festlegen, können Sie sehen, wie Informationen beim Fortschritt von der HTTP-Antwort zur Identität des Benutzers in `HttpContext.User` von der ASP.NET Core-Middleware verarbeitet werden.

- Die `AddMicrosoftIdentityUI`-Erweiterungsmethode ist im Paket **Microsoft.Identity.Web.UI** definiert. Sie stellt einen Standardcontroller zum Behandeln der An- und Abmeldung bereit.

Weitere Details zum Erstellen von Web-Apps mit Microsoft.Identity.Web finden Sie unter <https://aka.ms/ms-id-web/webapp>.

> [!WARNING]
> Derzeit wird von Microsoft.Identity.Web das Szenario **Einzelne Benutzerkonten** (Speichern von Benutzerkonten in der App) nicht unterstützt, wenn Azure AD als externer Anmeldeanbieter verwendet wird. Einzelheiten dazu finden Sie unter: [AzureAD/microsoft-identity-web#133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Der Code für die Authentifizierung in ASP.NET-Web-Apps und -Web-APIs befindet sich in der Datei [App_Start/Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61).

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

Das Java-Beispiel verwendet das Spring-Framework. Die Anwendung ist geschützt, da Sie einen Filter implementieren, der jede HTTP-Antwort abfängt. Im Schnellstart für Java-Web-Apps handelt es sich dabei um den Filter `AuthFilter` in `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`.

Der Filter verarbeitet den OAuth 2.0-Autorisierungscodeflow und überprüft, ob der Benutzer authentifiziert ist (`isAuthenticated()`-Methode). Wenn der Benutzer nicht authentifiziert ist, wird die URL der Azure AD-Autorisierungsendpunkte berechnet und der Browser zu diesem URI umgeleitet.

Wenn die Antwort mit dem Autorisierungscode eingeht, wird mit MSAL für Java das Token abgerufen. Wenn das Token schließlich vom Tokenendpunkt (im Umleitungs-URI) empfangen wird, erfolgt die Benutzeranmeldung.

Weitere Informationen finden Sie unter der `doFilter()`-Methode in [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> Der Code von `doFilter()` wird in einer etwas anderen Reihenfolge geschrieben, aber der Flow entspricht dem beschriebenen.

Unter [Microsoft Identity Platform und der OAuth 2.0-Autorisierungscodeflow](v2-oauth2-auth-code-flow.md) finden Sie Einzelheiten zu dem durch diese Methode ausgelösten Autorisierungscodeflow.

# <a name="python"></a>[Python](#tab/python)

Das Python-Beispiel verwendet Flask. Die Initialisierung von Flask und MSAL für Python erfolgt in [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie An- und Abmeldung ausgelöst werden.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Anmelden und Abmelden](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Anmelden und Abmelden](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Anmelden und Abmelden](./scenario-web-app-sign-user-sign-in.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Anmelden und Abmelden](./scenario-web-app-sign-user-sign-in.md?tabs=python).

---
