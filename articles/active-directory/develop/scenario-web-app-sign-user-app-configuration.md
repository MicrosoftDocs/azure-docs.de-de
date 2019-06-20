---
title: Web-App, die Benutzer anmeldet (Codekonfiguration) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Benutzer anmeldet (Codekonfiguration)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
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
ms.openlocfilehash: 82e6cbcd01c87ddffb7eac8d0ea0faef85f41a13
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254005"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Web-App, die Benutzer anmeldet – Codekonfiguration

Erfahren Sie, wie Sie den Code für Ihre Web-App konfigurieren, die Benutzer anmeldet.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotheken zum Schutz von Web-Apps

<!-- This section can be in an include for Web App and Web APIs -->
Die folgenden Bibliotheken werden zum Schützen einer Web-App (und einer Web-API) verwendet:

| Plattform | Bibliothek | BESCHREIBUNG |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identitätsmodellerweiterungen für .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Die Microsoft Identity-Erweiterung für .NET schlägt bei der direkten Verwendung durch ASP.NET und ASP.NET Core eine Reihe von DLLs vor, die sowohl unter .NET Framework und .NET Core ausgeführt werden. Über eine ASP.NET-/ASP.NET Core-Webanwendung können Sie mithilfe der **TokenValidationParameters**-Klasse die Tokenüberprüfung steuern (insbesondere in ISV-Szenarios). |

## <a name="aspnet-core-configuration"></a>ASP.NET Core-Konfiguration

### <a name="application-configuration-files"></a>Anwendungskonfigurationsdatei

In ASP.NET Core wird eine Webanwendung, die Benutzer mit der Microsoft Identity Platform anmeldet, über die `appsettings.json`-Datei konfiguriert. Folgende Einstellungen sind einzugeben:

- Cloud-`Instance`, wenn Ihre App in nationalen Clouds ausgeführt werden soll
- Zielgruppe in `tenantId`
- `clientId` für Ihre Anwendung, die aus dem Azure-Portal kopiert wird.

```JSon
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

In ASP.NET Core ist eine weitere Datei enthalten, die die URL (`applicationUrl`) und den SSL-Port (`sslPort`) für Ihre Anwendung sowie verschiedene Profile enthält.

```JSon
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

Im Azure-Portal müssen die Antwort-URLs, die Sie auf der Seite **Authentifizierung** für Ihre Anwendung registrieren müssen, diesen URLs entsprechen. Für die beiden zuvor genannten Konfigurationsdateien wären diese beispielsweise `https://localhost:44321/signin-oidc`, da die applicationUrl `http://localhost:3110` ist, die `sslPort`-Eigenschaft aber angegeben ist (44321) und die `CallbackPath`-Eigenschaft in der `appsettings.json`-Konfiguration `/signin-oidc` ist.
  
Auf die gleiche Weise würde die Abmelde-URL auf `https://localhost:44321/signout-callback-oidc` festgelegt werden.

### <a name="initialization-code"></a>Initialisierungscode

In ASP.NET Core-Web-Apps (und -Web-APIs) befindet sich der Code für die Anwendungsinitialisierung in der `Startup.cs`-Datei. Zum Hinzufügen der Authentifizierung mit der Microsoft Identity Platform v2.0 (ehemals Azure AD) müssen Sie den folgenden Code hinzufügen. Die Kommentare im Code sollten selbsterklärend sein.

  > [!NOTE]
  > Falls Sie Ihr Projekt mit dem ASP.NET Core-Standardwebprojekt in Visual Studio oder mit `dotnet new mvc` starten, ist die Methode `AddAzureAD` standardmäßig verfügbar, da die zugehörigen Pakete automatisch geladen werden. Wenn Sie jedoch ein Projekt von Grund auf neu erstellen und den unten angegebenen Code verwenden, empfiehlt es sich, Ihrem Projekt das NuGet-Paket **Microsoft.AspNetCore.Authentication.AzureAD.UI** hinzuzufügen, um die Methode `AddAzureAD` verfügbar zu machen.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>ASP.NET-Konfiguration

In ASP.NET wird die Anwendung über die `Web.Config`-Datei konfiguriert.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
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

Der Code für die Authentifizierung in ASP.NET-Web-Apps/-Web-APIs befindet sich in der `App_Start/Startup.Auth.cs`-Datei.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [An- und Abmelden](scenario-web-app-sign-user-sign-in.md)
