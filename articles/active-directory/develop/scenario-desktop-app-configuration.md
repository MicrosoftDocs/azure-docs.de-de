---
title: Desktop-App, die Web-APIs aufruft (Codekonfiguration) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Desktop-App erstellen, die Web-APIs aufruft (Codekonfiguration der App)
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
ms.openlocfilehash: bc0042d6392891e8282c563afea2212031a0f49a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121883"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Desktop-App, die Web-APIs aufruft – Codekonfiguration

Nachdem Sie Ihre Anwendung nun erstellt haben, erfahren Sie, wie Sie den Code mit den Koordinaten der Anwendung konfigurieren.

## <a name="msal-libraries"></a>MSAL-Bibliotheken

MSAL.NET ist momentan die einzige MSAL-Bibliothek, die Desktopanwendungen unterstützt.

## <a name="public-client-application"></a>Öffentliche Clientanwendung

Aus Codesicht handelt es sich bei Desktopanwendungen um öffentliche Clientanwendungen. Daher erstellen und bearbeiten Sie MSAL.NET-`IPublicClientApplication`. Dabei unterscheidet sich die Vorgehensweise wieder je nachdem, ob Sie die interaktive Authentifizierung verwenden oder nicht.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Ausschließlich durch Code

Mit dem folgenden Code wird eine öffentliche Clientanwendung instanziiert, über die Benutzer mit einem Geschäfts-, Schul- oder Unikonto oder einem persönlichen Microsoft-Konto bei der öffentlichen Microsoft Azure-Cloud angemeldet werden.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Wenn Sie planen, wie oben dargestellt die interaktive Authentifizierung zu verwenden, möchten Sie den Modifizierer `.WithRedirectUri` verwenden:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .Build();
```

### <a name="using-configuration-files"></a>Verwenden von Konfigurationsdateien

Der folgende Code instanziiert eine öffentliche Clientanwendung aus einem Konfigurationsobjekt, das programmgesteuert ausgefüllt oder von einer Konfigurationsdatei gelesen werden könnte.

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .Build();
```

### <a name="more-elaborated-configuration"></a>Ausführlichere Konfiguration

Sie können die Anwendungserstellung ausführlicher gestalten, indem Sie Modifizierer hinzufügen. Wenn Sie beispielsweise möchten, dass Ihre Anwendung eine mehrinstanzenfähige Anwendung in einer nationalen Cloud (in diesem Fall US Government) ist, könnten Sie Folgendes schreiben:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET enthält auch einen Modifizierer für ADFS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Wenn Sie schließlich Token für einen Azure AD B2C-Mandanten abrufen möchten, können Sie Ihren Mandanten wie im folgenden Codeausschnitt dargestellt angeben:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Weitere Informationen

So erhalten Sie weitere Informationen zur Konfiguration einer MSAL.NET-Desktopanwendung:

- Eine Liste aller bei `PublicClientApplicationBuilder` verfügbaren Modifizierer finden Sie in der Referenzdokumentation [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Die Beschreibung aller bei `PublicClientApplicationOptions` verfügbar gemachten Optionen finden Sie in der Referenzdokumentation [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="complete-example-with-configuration-options"></a>Vollständiges Beispiel mit Konfigurationsoptionen

Stellen Sie sich eine .NET Core-Konsolenanwendung mit folgender `appsettings.json`-Konfigurationsdatei vor:

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Sie verfügen über wenig Code, um diese Datei mithilfe des von .NET bereitgestellten Konfigurationsframework zu lesen:

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Sie müssen nun einfach folgenden Code schreiben, um Ihre Anwendung zu erstellen:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
           .Build();
```

Vor dem Aufruf der `.Build()`-Methode können Sie Ihre Konfiguration wie zuvor dargestellt durch Aufrufe von `.WithXXX`-Methoden überschreiben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Abrufen eines Tokens für eine Desktop-App](scenario-desktop-acquire-token.md)
