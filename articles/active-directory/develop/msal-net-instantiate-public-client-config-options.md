---
title: Instanziieren einer öffentlichen Clientanwendung mit Optionen (Microsoft Authentication Library für .NET) | Azure
description: Erfahren Sie, wie Sie unter Verwendung der Microsoft Authentication Library für .NET (MSAL.NET) eine öffentliche Clientanwendung mit Konfigurationsoptionen instanziieren.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 125bbf9aed54fb00f039aeffddd5cc1aad3360a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544402"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Instanziieren einer öffentlichen Clientanwendung mit Konfigurationsoptionen unter Verwendung von MSAL.NET

In diesem Artikel wird beschrieben, wie Sie eine [öffentliche Clientanwendung](msal-client-applications.md) unter Verwendung der Microsoft Authentication Library für .NET (MSAL.NET) instanziieren.  Die Anwendung wird mit Konfigurationsoptionen instanziiert, die in einer Einstellungsdatei definiert werden.

Vor der Initialisierung einer Anwendung müssen Sie diese zunächst [registrieren](quickstart-register-app.md), damit Ihre App in Microsoft Identity Platform integriert werden kann. Nach der Registrierung benötigen Sie unter Umständen die folgenden Informationen (die Sie im Azure-Portal finden können):

- Die Client-ID (eine Zeichenfolge, die eine GUID darstellt)
- Die URL des Identitätsanbieters (Namensgeber der Instanz) und die Anmeldezielgruppe für Ihre Anwendung. Diese beiden Parameter werden zusammen als Autorität bezeichnet.
- Die Mandanten-ID, wenn Sie eine Geschäftsanwendung ausschließlich für Ihre Organisation schreiben (auch als Einzelmandantenanwendung bezeichnet).
- Für Web-Apps und gelegentlich auch für öffentliche Clientanwendungen (insbesondere, wenn Ihre App einen Broker verwenden muss) müssen Sie auch den Umleitungs-URI festlegen, mit dem der Identitätsanbieter Ihrer Anwendung die Sicherheitstoken sendet.


Eine .NET Core-Konsolenanwendung könnte beispielsweise die folgende *appsettings.json*-Konfigurationsdatei aufweisen:

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

Der folgende Code liest diese Datei mit dem .NET-Konfigurationsframework:

```csharp
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
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Der folgende Code erstellt Ihre Anwendung und verwendet dabei die Konfiguration aus der Einstellungsdatei:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

