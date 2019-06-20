---
title: Web-API, die Downstream-Web-APIs aufruft (Codekonfiguration der App) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Web-API erstellen, die Web-APIs aufruft (Codekonfiguration der App)
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
ms.openlocfilehash: f62cf65e275d8a9b909bf60103ccbd84e91e4574
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65785056"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Web-API, die Web-APIs aufruft – Codekonfiguration

Nachdem Sie Ihre Web-API registriert haben, können Sie den Code für die Anwendung konfigurieren.

Der Code zum Konfigurieren Ihrer Web-API, sodass diese Downstream-Web-APIs aufruft, die auf dem zum Schutz einer Web-API verwendeten Code basieren. Weitere Informationen finden Sie unter [Geschützte Web-API – App-Konfiguration](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Von OnTokenValidated abonnierter Code

Über die Codekonfiguration für alle geschützten Web-APIs hinaus müssen Sie die Validierung des Bearertokens abonnieren, das beim Aufruf Ihrer API empfangen wird:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>„Im Auftrag von“-Ablauf

Für die AddAccountToCacheFromJwt()-Methode ist Folgendes erforderlich:

- Instanziieren einer vertraulichen MSAL-Clientanwendung.
- Rufen Sie `AcquireTokenOnBehalf` auf, um das Bearertoken, das vom Client für die Web-API abgerufen wurde, gegen ein Bearertoken des selben Benutzers auszutauschen, wohingegen für unsere API eine Downstream-API aufgerufen werden soll.

### <a name="instantiate-a-confidential-client-application"></a>Instanziieren einer vertraulichen Clientanwendung

Dieser Flow ist nur im vertraulichen Clientflow verfügbar, sodass die geschützte Web-API der [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)-Klasse jeweils über die Methode `WithClientSecret` oder `WithCertificate` Clientanmeldeinformationen (Clientgeheimnisse oder -zertifikate) bereitstellt.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

### <a name="how-to-call-on-behalf-of"></a>Aufrufen von „Im Auftrag von“

Der Aufruf „Im Auftrag von (on behalf of, OBO)“ erfolgt durch das Aufrufen der [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder)-Methode auf der `IConfidentialClientApplication`-Schnittstelle.

`ClientAssertion` basiert auf dem Bearertoken, das von der Web-API der eigenen Clients empfangen wurde. Es gibt [zwei Konstruktoren](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet): Ein Konstruktor verwendet ein JWT-Bearertoken und der andere verwendet jegliche Art von Benutzerassertion (eine andere Art von Sicherheitstoken, dessen Typ dann in einem zusätzlichen Parameter namens `assertionType` angegeben wird).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

In der Praxis wird der OBO-Fluss häufig zum Abrufen eines Token für eine Downstream-API verwendet und im MSAL.NET-Benutzertokencache gespeichert, sodass andere Teile der Web-API später die [Außerkraftsetzung](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) von ``AcquireTokenOnSilent`` aufrufen kann, um die Downstream-APIs aufzurufen. Dies hat zur Folge, dass die Token aktualisiert werden, wenn dies erforderlich ist.

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
 try
 {
  UserAssertion userAssertion;
  IEnumerable<string> requestedScopes;
  if (jwtToken != null)
  {
   userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
   requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
  }
  else
  {
   throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
  }

  // Create the application
  var application = BuildConfidentialClientApplication(httpContext, principal);

  // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
  var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                  userAssertion)
                                        .ExecuteAsync()
                                        .GetAwaiter().GetResult();
 }
 catch (MsalException ex)
 {
  Debug.WriteLine(ex.Message);
  throw;
 }
}
```

## <a name="protocol"></a>Protocol

Weitere Informationen zum „Im Auftrag von“-Protokoll finden Sie unter [Microsoft Identity Platform und der On-Behalf-Of-Fluss von OAuth2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Abrufen eines Tokens für die App](scenario-web-api-call-api-acquire-token.md)
