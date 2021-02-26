---
title: Instanziieren einer vertraulichen Client-App (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie mithilfe der Microsoft Authentication Library für .NET (MSAL.NET) eine vertrauliche Clientanwendung mit Konfigurationsoptionen instanziieren.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: d477c419bb677a6b8f24a3aae26c403e47cc96cb
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583941"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Instanziieren einer vertraulichen Clientanwendung mit Konfigurationsoptionen unter Verwendung von MSAL.NET

In diesem Artikel wird beschrieben, wie Sie mithilfe der Microsoft-Authentifizierungsbibliothek für .NET (Microsoft Authentication Library für .NET, MSAL.NET) eine [vertrauliche Clientanwendung](msal-client-applications.md) instanziieren.  Die Anwendung wird mit Konfigurationsoptionen instanziiert, die in einer Einstellungsdatei definiert werden.

Vor der Initialisierung einer Anwendung müssen Sie diese zunächst [registrieren](quickstart-register-app.md), damit Ihre App in Microsoft Identity Platform integriert werden kann. Nach der Registrierung benötigen Sie unter Umständen die folgenden Informationen (die Sie im Azure-Portal finden können):

- Die Client-ID (eine Zeichenfolge, die eine GUID darstellt)
- Die URL des Identitätsanbieters (Namensgeber der Instanz) und die Anmeldezielgruppe für Ihre Anwendung. Diese beiden Parameter werden zusammen als Autorität bezeichnet.
- Die Mandanten-ID, wenn Sie eine Branchenanwendung ausschließlich für Ihre Organisation schreiben (auch Einzelmandantenanwendung genannt).
- Den geheimen Anwendungsschlüssel (geheime Clientzeichenfolge) oder das Zertifikat (vom Typ „X509Certificate2“), wenn es sich um eine vertrauliche Client-App handelt.
- Für Web-Apps und gelegentlich auch für öffentliche Clientanwendungen (insbesondere, wenn Ihre App einen Broker verwenden muss) müssen Sie auch den Umleitungs-URI festlegen, mit dem der Identitätsanbieter Ihrer Anwendung die Sicherheitstoken sendet.

## <a name="configure-the-application-from-the-config-file"></a>Konfigurieren der Anwendung aus der Konfigurationsdatei
Der Eigenschaftenname der Optionen in MSAL.NET entspricht dem Eigenschaftennamen von `AzureADOptions` in ASP.NET Core, Sie müssen daher keinen Verbindungscode schreiben.

Die Konfiguration einer ASP.NET Core-Anwendung wird in einer *appsettings.json*-Datei beschrieben:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Ab MSAL.NET v3.x können Sie Ihre vertrauliche Clientanwendung aus der Konfigurationsdatei konfigurieren.

Deklarieren Sie in der Klasse, in der Sie Ihre Anwendung konfigurieren und instanziieren möchten, ein `ConfidentialClientApplicationOptions`-Objekt.  Binden Sie die aus der Quelle gelesene Konfiguration (einschließlich der Datei „appconfig.json“) mit der Methode `IConfigurationRoot.Bind()` aus dem NuGet-Paket [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) an die Instanz der Anwendungsoptionen:

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Dadurch kann der Inhalt des Abschnitts „AzureAD“ der Datei *appsettings.json* an die entsprechenden Eigenschaften des `ConfidentialClientApplicationOptions`-Objekts gebunden werden.  Erstellen Sie als Nächstes ein `ConfidentialClientApplication`-Objekt:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Hinzufügen der Runtime-Konfiguration
In einer vertraulichen Clientanwendung haben Sie in der Regel einen Cache pro Benutzer. Daher müssen Sie den Cache dem Benutzer zuordnen und den Anwendungsersteller darüber informieren, dass Sie diesen verwenden möchten. Auf die gleiche Weise verfügen Sie möglicherweise über einen dynamisch berechneten Umleitungs-URI. In diesem Fall ist der Code wie folgt:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

