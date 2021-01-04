---
title: Konfigurieren von Desktop-Apps, die Web-APIs aufrufen – Microsoft Identity Platform | Azure
description: Erfahren Sie, wie Sie den Code einer Desktop-App konfigurieren, die Web-APIs aufruft
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 27c9f2ab8b02c96d016fea66acd2b3840d79d09d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444122"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Desktop-App, die Web-APIs aufruft: Codekonfiguration

Nachdem Sie Ihre Anwendung nun erstellt haben, erfahren Sie, wie Sie den Code mit den Koordinaten der Anwendung konfigurieren.

## <a name="microsoft-authentication-libraries"></a>Microsoft-Authentifizierungsbibliotheken

Die folgenden Microsoft-Authentifizierungsbibliotheken (MSALs) unterstützen Desktopanwendungen.

  Microsoft Authentication Library (MSAL) | BESCHREIBUNG
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Unterstützt das Erstellen einer Desktopanwendung auf mehreren Plattformen – Linux, Windows und macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Unterstützt das Erstellen einer Desktopanwendung unter mehreren Plattformen.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Unterstützt das Erstellen einer Desktopanwendung unter mehreren Plattformen.
  ![iOS für MSAL](media/sample-v2-code/logo_iOS.png) <br/> iOS für MSAL | Unterstützt ausschließlich Desktopanwendungen, die unter macOS ausgeführt werden.

## <a name="public-client-application"></a>Öffentliche Clientanwendung

Aus Codesicht sind Desktopanwendungen öffentliche Clientanwendungen. Die Konfiguration weicht ein wenig ab, je nachdem, ob Sie die interaktive Authentifizierung verwenden oder nicht.

# <a name="net"></a>[.NET](#tab/dotnet)

Sie müssen MSAL.NET `IPublicClientApplication` erstellen und bearbeiten.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Ausschließlich durch Code

Mit dem folgenden Code wird eine öffentliche Clientanwendung instanziiert, und Benutzer werden mit einem Geschäfts-, Schul- oder Unikonto oder einem persönlichen Microsoft-Konto bei der öffentlichen Microsoft Azure-Cloud angemeldet.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Wenn Sie planen, wie zuvor dargestellt, die interaktive Authentifizierung oder den Gerätecodeflow zu verwenden, sollten Sie den Modifizierer `.WithRedirectUri` verwenden.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Verwenden von Konfigurationsdateien

Der folgende Code instanziiert eine öffentliche Clientanwendung über ein Konfigurationsobjekt, das programmgesteuert ausgefüllt oder aus einer Konfigurationsdatei gelesen werden kann.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Ausführlichere Konfiguration

Sie können die Anwendungserstellung ausführlicher gestalten, indem Sie Modifizierer hinzufügen. Wenn Sie beispielsweise möchten, dass Ihre Anwendung eine mehrinstanzenfähige Anwendung in einer nationalen Cloud (in diesem Fall US Government) ist, könnten Sie Folgendes schreiben:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET enthält außerdem einen Modifizierer für Active Directory-Verbunddienste (AD FS) 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Wenn Sie schließlich Token für einen Azure Active Directory (Azure AD) B2C-Mandanten abrufen möchten, können Sie Ihren Mandanten wie im folgenden Codeausschnitt dargestellt angeben:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Weitere Informationen

So erhalten Sie weitere Informationen zur Konfiguration einer MSAL.NET-Desktopanwendung

- Eine Liste aller bei `PublicClientApplicationBuilder` verfügbaren Modifizierer finden Sie in der Referenzdokumentation [PublicClientApplicationBuilder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Eine Beschreibung aller bei `PublicClientApplicationOptions` verfügbar gemachten Optionen finden Sie unter [PublicClientApplicationOptions](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) in der Referenzdokumentation.

### <a name="complete-example-with-configuration-options"></a>Vollständiges Beispiel mit Konfigurationsoptionen

Stellen Sie sich eine .NET Core-Konsolenanwendung mit folgender `appsettings.json`-Konfigurationsdatei vor:

```json
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

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
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

  // Read the auth and graph endpoint configuration
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

Schreiben Sie nun den folgenden Code, um Ihre Anwendung zu erstellen:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Vor dem Aufruf der `.Build()`-Methode können Sie Ihre Konfiguration wie zuvor dargestellt durch Aufrufe von `.WithXXX`-Methoden überschreiben.

# <a name="java"></a>[Java](#tab/java)

Dies ist die Klasse, mit der die Beispiele in den MSAL Java-Entwicklungsbeispielen konfiguriert werden: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[MacOS](#tab/macOS)

Mit dem folgenden Code wird eine öffentliche Clientanwendung instanziiert, und Benutzer werden mit einem Geschäfts-, Schul- oder Unikonto oder einem persönlichen Microsoft-Konto bei der öffentlichen Microsoft Azure-Cloud angemeldet.

### <a name="quick-configuration"></a>Schnellkonfiguration

Objective-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Ausführlichere Konfiguration

Sie können die Anwendungserstellung ausführlicher gestalten, indem Sie Modifizierer hinzufügen. Wenn Ihre Anwendung beispielsweise eine mehrinstanzenfähige Anwendung in einer nationalen Cloud (in diesem Fall US Government) sein soll, könnten Sie Folgendes schreiben:

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Abrufen eines Tokens für die Desktop-App](scenario-desktop-acquire-token.md).