---
title: Konfigurieren von geschützten Web-API-Apps | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine geschützte Web-API erstellen und den Code Ihrer Anwendung konfigurieren.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bdf74a6d8b6798de8aba33baea5afdf987ce1c34
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582484"
---
# <a name="protected-web-api-code-configuration"></a>Geschützte Web-API: Codekonfiguration

Beim Konfigurieren Ihrer geschützten Web-API müssen Sie Folgendes wissen:

- Wodurch werden APIs als geschützt definiert?
- Wie wird ein Bearertoken konfiguriert?
- Wie wird das Token überprüft?

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Wodurch werden ASP.NET- und ASP.NET Core-APIs als geschützt definiert?

Genau wie Webanwendungen sind auch die ASP.NET- und die ASP.NET Core-Web-APIs geschützt, da ihren Controlleraktionen das Attribut **[Authorize]** vorangestellt ist. Die Controlleraktionen können nur aufgerufen werden, wenn die API mit einer autorisierten Identität aufgerufen wird.

Stellen Sie sich die folgenden Fragen:

- Eine Web-API kann nur von einer App aufgerufen werden. Woher kennt die API die Identität der aufrufenden App?
- Falls die App die API im Namen eines Benutzers aufruft: Welche Identität hat der Benutzer?

## <a name="bearer-token"></a>Bearertoken

Das Bearertoken, das beim Aufrufen der App im Header festgelegt wird, enthält Informationen zur App-Identität. Außerdem enthält es Informationen zum Benutzer. (Es sei denn, die Web-App akzeptiert Dienst-zu-Dienst-Aufrufe von einer Daemon-App).

Das folgende C#-Codebeispiel zeigt, wie ein Client die API aufruft, nachdem er ein Token für die Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) abgerufen hat:

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Eine Clientanwendung fordert das Bearertoken für Microsoft Identity Platform *für die Web-API* an. Die Web-API ist die einzige Anwendung, die das Token verifizieren und die darin enthaltenen Ansprüche anzeigen sollte. Client-Apps dürfen niemals versuchen, die Ansprüche in Token zu überprüfen.
>
> Die Web-API kann später ggf. die Verschlüsselung des Tokens erforderlich machen. Diese Anforderung würde den Zugriff durch Client-Apps verhindern, die Zugriffstoken anzeigen können.

## <a name="jwtbearer-configuration"></a>JwtBearer-Konfiguration

In diesem Abschnitt wird beschrieben, wie Sie ein Bearertoken konfigurieren.

### <a name="config-file"></a>Konfigurationsdatei

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>Verwendung eines benutzerdefinierten App-ID-URIs für Ihre Web-API

Wenn Sie den vom App-Registrierungsportal vorgeschlagenen App-ID-URI akzeptiert haben, müssen Sie die Zielgruppe nicht angeben (siehe [Anwendungs-ID-URI und-Bereiche](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)). Andernfalls sollten Sie eine `Audience`-Eigenschaft hinzufügen, deren Wert der App-ID-URI für Ihre Web-API ist.

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>Codeinitialisierung

Wenn eine App in einer Controlleraktion aufgerufen wird, die ein Attribut vom Typ **[Authorize]** enthält, extrahieren ASP.NET und ASP.NET Core das Zugriffstoken aus dem Bearertoken des Autorisierungsheaders. Das Zugriffstoken wird dann an die JwtBearer-Middleware weitergeleitet, die die Microsoft-Identitätsmodellerweiterungen für .NET aufruft.

#### <a name="microsoftidentityweb"></a>Microsoft.Identity.Web

Microsoft empfiehlt die Verwendung des NuGet-Pakets [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) beim Entwickeln einer Web-API mit ASP.NET Core.

_Microsoft.Identity.Web_ ist die verbindende Komponente zwischen ASP.NET Core, der Authentifizierungsmiddleware und der [Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL)](msal-overview.md) für .NET. Das Paket ermöglicht eine übersichtlichere, stabilere Entwicklerumgebung und nutzt die Leistungsfähigkeit von Microsoft Identity Platform und Azure AD B2C.

#### <a name="using-microsoftidentityweb-templates"></a>Verwenden von Microsoft.Identity.Web-Vorlagen

Mithilfe von Microsoft.Identity.Web-Projektvorlagen können Sie eine Web-API von Grund auf neu erstellen. Weitere Informationen finden Sie unter [Microsoft.Identity.Web – Web-API-Projektvorlage](https://aka.ms/ms-id-web/webapi-project-templates).

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>Verwenden einer vorhandenen ASP.NET Core 3.1-Anwendung

ASP.NET Core 3.1 verwendet die Microsoft.AspNetCore.AzureAD.UI-Bibliothek. Die Middleware wird in der Datei „Startup.cs“ initialisiert.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Die Middleware wird der Web-API durch die folgende Anweisung hinzugefügt:

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 Aktuell erstellen die ASP.NET Core-Vorlagen Azure Active Directory-Web-APIs, die Benutzer innerhalb Ihrer Organisation oder einer anderen Organisation anmelden. Benutzer werden nicht mit persönlichen Konten angemeldet. Sie können jedoch die Vorlagen so ändern, dass Microsoft Identity Platform verwendet wird, indem Sie [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) verwenden. Ersetzen Sie dabei den Code in der Datei *Startup.cs*:

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

Sie können auch Folgendes schreiben (was gleichwertig ist):

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> Wenn Sie Microsoft.Identity.Web verwenden und die `Audience` nicht in der Datei *appsettings.json* festlegen, wird Folgendes verwendet:
> -  `$"{ClientId}"`: Wenn Sie die [akzeptierte Zugriffstokenversion](scenario-protected-web-api-app-registration.md#accepted-token-version) auf `2` festgelegt haben oder für Azure AD B2C-Web-APIs.
> - `$"api://{ClientId}` in allen anderen Fällen (für v1.0-[Zugriffstoken](access-tokens.md)).
> Die Details finden Sie im Microsoft.Identity.Web-[Quellcode](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83).

Der vorstehende Codeausschnitt wurde aus dem [inkrementellen Tutorial zu ASP.NET Core-Web-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28) extrahiert. Die Details zu **AddMicrosoftIdentityWebApiAuthentication** sind in [Microsoft.Identity.Web](microsoft-identity-web.md) verfügbar. Diese Methode ruft die [AddMicrosoftIdentityWebAPI](/dotnet/api/microsoft.identity.web.microsoftidentitywebapiauthenticationbuilderextensions.addmicrosoftidentitywebapi?preserve-view=true&view=azure-dotnet-preview) auf, die wiederum die Middleware anweist, wie das Token überprüft werden soll.

## <a name="token-validation"></a>Tokenüberprüfung

Im obigen Codeausschnitt überprüft die JwtBearer-Middleware das Token auf der Grundlage des Werts von `TokenValidationParameters` (genau wie die OpenID Connect-Middleware in Web-Apps). Der Token wird bei Bedarf entschlüsselt, die Ansprüche werden extrahiert, und die Signatur wird überprüft. Die Middleware validiert dann das Token, indem sie auf diese Daten überprüft:

- Audience: Das Ziel des Tokens ist die Web-API.
- Antragsteller: Das Token wurde für eine App ausgestellt, die zum Aufrufen der Web-API berechtigt ist.
- Aussteller: Das Token wurde von einem vertrauenswürdigen Sicherheitstokendienst (Security Token Service, STS) ausgestellt.
- Ablauf: Die Tokenlebensdauer liegt innerhalb des zulässigen Bereichs.
- Signatur: Das Token wurde nicht manipuliert.

Es können auch spezielle Validierungen vorliegen. So kann beispielsweise sichergestellt werden, dass die Signaturschlüssel (bei Einbettung in ein Token) vertrauenswürdig sind und das Token nicht wiedergegeben wird. Für einige Protokolle sind bestimmte Validierungen erforderlich.

### <a name="validators"></a>Validierungssteuerelemente

Die Schritte zur Überprüfung werden in Validierungssteuerelementen erfasst, die durch die Open-Source-Bibliothek [Microsoft-Identitätsmodellerweiterungen für .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) bereitgestellt werden. Die Validierungssteuerelemente sind in der Quelldatei [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs) der Bibliothek definiert.

Die folgende Tabelle enthält eine Beschreibung der Validierungssteuerelemente:

| Validierungssteuerelement | BESCHREIBUNG |
|---------|---------|
| **ValidateAudience** | Stellt sicher, dass das Token für die Anwendung bestimmt ist, die das Token für Sie überprüft. |
| **ValidateIssuer** | Stellt sicher, dass das Token von einem vertrauenswürdigen Sicherheitstokendienst ausgestellt wurde und somit von jemandem stammt, dem Sie vertrauen. |
| **ValidateIssuerSigningKey** | Stellt sicher, dass die Anwendung, die das Token validiert, dem Schlüssel vertraut, mit dem das Token signiert wurde. Es gibt einen Sonderfall, bei dem der Schlüssel in das Token eingebettet ist. Dies ist jedoch in der Regel nicht der Fall. |
| **ValidateLifetime** | Stellt sicher, dass das Token noch oder bereits gültig ist. Das Validierungssteuerelement überprüft, ob die Lebensdauer des Tokens innerhalb des durch die Ansprüche **notbefore** und **expires** angegebenen Bereichs liegt. |
| **ValidateSignature** | Stellt sicher, dass das Token nicht manipuliert wurde. |
| **ValidateTokenReplay** | Stellt sicher, dass das Token nicht wiedergegeben wird. Es gibt einen Sonderfall für einige Protokolle mit einmaliger Verwendung. |

#### <a name="customizing-token-validation"></a>Anpassen der Tokenüberprüfung

Die Validierungssteuerelemente werden Eigenschaften der Klasse **TokenValidationParameters** zugeordnet. Die Eigenschaften werden von der ASP.NET- und ASP.NET Core-Konfiguration initialisiert.

In den meisten Fällen müssen die Parameter nicht geändert werden. Ausnahmen sind Apps, bei denen es sich nicht um Einzelmandanten handelt. Diese Web-Apps akzeptieren Benutzer aus einer beliebigen Organisation oder von persönlichen Microsoft-Konten. In diesem Fall ist eine Überprüfung der Aussteller erforderlich. Microsoft.Identity.Web übernimmt auch die Überprüfung des Ausstellers. Die Details finden Sie unter [Microsoft.Identity.Web – AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

Wenn Sie in ASP.NET Core die Parameter für die Tokenüberprüfung anpassen möchten, verwenden Sie in der Datei *Startup.cs* den folgenden Codeausschnitt:

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  }
});
```

Für ASP.NET MVC veranschaulicht das folgende Codebeispiel die Ausführung einer benutzerdefinierten Tokenüberprüfung:

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Tokenüberprüfung in Azure Functions

Eingehende Zugriffstoken können auch in Azure Functions überprüft werden. Beispiele für eine solche Überprüfung finden Sie in den folgenden Codebeispielen auf GitHub:

- .NET: [Azure-Samples/ms-identity-dotnet-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js: [Azure-Samples/ms-identity-nodejs-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-Samples/ms-identity-python-webapi-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Überprüfen der Geltungsbereiche und App-Rollen in Ihrem Code](scenario-protected-web-api-verification-scope-app-roles.md).