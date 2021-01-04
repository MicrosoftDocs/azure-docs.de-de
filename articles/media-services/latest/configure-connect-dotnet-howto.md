---
title: Verbinden mit der Azure Media Services v3-API – .NET
description: In diesem Artikel wird demonstriert, wie Sie mit .NET eine Verbindung mit der Media Services v3-API herstellen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/17/2020
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 3d4f232d87209a3a5676cac22e67a38b17af6917
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844317"
---
# <a name="connect-to-media-services-v3-api---net"></a>Verbinden mit der Media Services v3-API – .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieser Artikel zeigt Ihnen, wie Sie mit der Methode der Dienstprinzipalanmeldung eine Verbindung zum Azure Media Services v3.NET SDK herstellen.

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md) Merken Sie sich den Namen der Ressourcengruppe und den Namen des Media Services-Kontos.
- Installieren Sie ein Tool, das Sie für die .NET-Entwicklung verwenden möchten. Die Schritte in diesem Artikel zeigen, wie Sie die [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) verwenden. Informationen zu Visual Studio Code finden Sie unter [Arbeiten mit C# ](https://code.visualstudio.com/docs/languages/csharp). Sie können auch einen anderen Code-Editor verwenden.

> [!IMPORTANT]
> Informieren Sie sich über die [Namenskonventionen](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Erstellen einer Konsolenanwendung

1. Starten Sie Visual Studio. 
1. Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**. 
1. Erstellen Sie eine **.NET Core**-Konsolenanwendung.

Die Beispiel-App in diesem Thema ist auf `netcoreapp2.0` ausgerichtet. Der Code verwendet 'async main', der ab C# 7.1 verfügbar ist. Weitere Einzelheiten hierzu finden Sie in diesem [Blog](/archive/blogs/benwilli/async-main-is-available-but-hidden).

## <a name="add-required-nuget-packagesassemblies"></a>Hinzufügen erforderlicher NuGet-Pakete/Assemblys

1. Klicken Sie in Visual Studio auf **Tools** > **NuGet-Paket-Manager** > **NuGet-Manager-Konsole**.
2. Verwenden Sie im Fenster **Paket-Manager-Konsole** den Befehl `Install-Package`, um die folgenden NuGet-Pakete hinzuzufügen. Beispiel: `Install-Package Microsoft.Azure.Management.Media`.

|Paket|BESCHREIBUNG|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK <br/>Um sicherzustellen, dass Sie das neueste Azure Media Services-Paket verwenden, überprüfen Sie [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|

### <a name="other-required-assemblies"></a>Weitere erforderliche Assemblys

- Azure.Storage.Blobs
- Microsoft.Extensions.Configuration
- Microsoft.Extensions.Configuration.EnvironmentVariables
- Microsoft.Extensions.Configuration.Json
- Microsoft.Rest.ClientRuntime.Azure.Authentication

## <a name="create-and-configure-the-app-settings-file"></a>Erstellen und Konfigurieren der App-Einstellungsdatei

### <a name="create-appsettingsjson"></a>Erstellen der appsettings.json

1. Wechseln Sie **Allgemein** > **Textdatei**.
1. Benennen Sie die Datei „appsettings.json“.
1. Setzen Sie die Eigenschaft „Copy to Output Directory“ der .json-Datei auf „Copy if newer“ (damit die Anwendung bei Veröffentlichung darauf zugreifen kann).

### <a name="set-values-in-appsettingsjson"></a>Festlegen der Werte in „appSettings.json“

Führen Sie den Befehl `az ams account sp create` wie unter [Zugreifen auf APIs](./access-api-howto.md) beschrieben aus. Der Befehl gibt json-Werte zurück, das Sie in Ihre „appsettings.json“ kopieren sollten.
 
## <a name="add-configuration-file"></a>Konfigurationsdatei hinzufügen

Fügen Sie aus Gründen der Übersichtlichkeit eine Konfigurationsdatei hinzu, die für das Lesen von Werten aus „appsettings.json“ zuständig ist.

1. Fügen Sie eine neue .cs-Klasse zu Ihrem Projekt hinzu. Vergeben Sie den Namen `ConfigWrapper`. 
1. Fügen Sie den folgenden Code in diese Datei ein (in diesem Beispiel wird davon ausgegangen, dass der Namespace `ConsoleApp1` ist).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Location
        {
            get { return _config["Location"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Verbinden mit dem .NET-Client

Um mit der Verwendung von Media Services-APIs in .NET zu beginnen, müssen Sie ein **AzureMediaServicesClient**-Objekt erstellen. Zum Erstellen des Objekts müssen Sie Anmeldeinformationen bereitstellen, die für den Client zum Herstellen einer Verbindung mit Azure mithilfe von Azure AD erforderlich sind. In dem Code unten erstellt die Funktion „GetCredentialsAsync“ das ServiceClientCredentials-Objekt basierend auf den in der lokalen Konfigurationsdatei angegebenen Anmeldeinformationen.

1. Öffnen Sie `Program.cs`.
1. Fügen Sie folgenden Code ein:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Hochladen, Codieren und Streamen von Videos mithilfe von .NET](stream-files-tutorial-with-api.md) 
- [Tutorial: Livestreaming mit Media Services v3 unter Verwendung von .NET](stream-live-tutorial-with-api.md)
- [Tutorial: Analysieren von Videos mit Media Services v3 mithilfe von .NET](analyze-videos-tutorial-with-api.md)
- [Erstellen einer Auftragseingabe aus einer lokalen Datei](job-input-from-local-file-how-to.md)
- [Erstellen einer Auftragseingabe aus einer HTTPS-URL](job-input-from-http-how-to.md)
- [Codieren einer benutzerdefinierten Transformation](customize-encoder-presets-how-to.md)
- [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts](protect-with-aes128.md)
- [Verwenden der dynamischen DRM-Verschlüsselung und des Lizenzbereitstellungsdiensts](protect-with-drm.md)
- [Abrufen eines Signaturschlüssels aus der vorhandenen Richtlinie](get-content-key-policy-dotnet-howto.md)
- [Erstellen von Filtern mit dem Media Services .NET SDK](filters-dynamic-manifest-dotnet-howto.md)
- [Erweiterte Video-on-Demand-Beispiele von Azure Functions v2 mit Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Weitere Informationen

* [.NET-Referenz](/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet&preserve-view=true)
* Weitere Codebeispiele finden Sie im Repository für [.NET SDK-Beispiele](https://github.com/Azure-Samples/media-services-v3-dotnet).
