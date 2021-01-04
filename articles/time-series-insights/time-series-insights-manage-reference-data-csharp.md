---
title: Verwalten von Verweisdaten in allgemein verfügbaren Umgebungen mithilfe von C# – Azure Time Series Insights | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Verweisdaten für Ihre allgemein verfügbare Umgebung verwalten, indem Sie eine in C# geschriebene benutzerdefinierte Anwendung erstellen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: d6f0e5230fb3c59cab690620e837f476f3392a48
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020077"
---
# <a name="manage-reference-data-for-an-azure-time-series-insights-gen-1-environment-using-c-sharp"></a>Verwalten von Verweisdaten für eine Azure Time Series Insights Gen1-Umgebung mithilfe von C#

> [!CAUTION]
> Dies ist ein Artikel zu Azure Time Series Insights Gen1.

In diesem Artikel wird veranschaulicht, wie Sie C#, [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) und Azure Active Directory kombinieren, um programmgesteuert API-Anforderungen an die [Verweisdatenverwaltungs-API](/rest/api/time-series-insights/gen1-reference-data-api) von Azure Time Series Insights Gen1 auszuführen.

> [!TIP]
> C#-Codebeispiele mit allgemeiner Verfügbarkeit finden Sie unter [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample).

## <a name="summary"></a>Zusammenfassung

Im unten stehenden Beispielcode werden die folgenden Funktionen veranschaulicht:

* Abrufen eines Zugriffstokens mit **PublicClientApplication** für [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)
* Sequenzielle CREATE-, READ-, UPDATE- und DELETE-Vorgänge für die Gen1-[Verweisdatenverwaltungs-API](/rest/api/time-series-insights/gen1-reference-data-api)
* Allgemeine Antwortcodes, einschließlich [allgemeiner Fehlercodes](/rest/api/time-series-insights/gen1-reference-data-api#validation-and-error-handling)

    Die Verweisdatenverwaltungs-API verarbeitet jedes Element einzeln. Wenn bei einem Element ein Fehler auftritt, verhindert dies nicht den erfolgreichen Abschluss der anderen. Wenn Ihre Anforderung z. B. 100 Elemente umfasst und bei einem Element ein Fehler auftritt, werden 99 Elemente geschrieben, und eines wird zurückgewiesen.

## <a name="prerequisites-and-setup"></a>Voraussetzungen und Setup

Führen Sie vor dem Kompilieren und Ausführen des Beispielcodes die folgenden Schritte aus:

1. [Stellen Sie eine Gen1 Azure Time Series Insights](./time-series-insights-get-started.md
)-Umgebung bereit.

1. [Erstellen Sie ein Verweisdataset](time-series-insights-add-reference-data-set.md) in Ihrer Umgebung. Verwenden Sie das folgende Verweisdatenschema:

   | Schlüsselname | type |
   | --- | --- |
   | uuid | String |

1. Konfigurieren Sie Ihre Azure Time Series Insights-Umgebung für Azure Active Directory wie unter [Authentifizierung und Autorisierung](time-series-insights-authentication-and-authorization.md) beschrieben. Verwenden Sie `http://localhost:8080/` als **Umleitungs-URI**.

1. Installieren Sie die erforderlichen Projektabhängigkeiten.

1. Bearbeiten Sie den unten stehenden Beispielcode, indem Sie alle Vorkommen von **#PLACEHOLDER#** durch den entsprechenden Umgebungsbezeichner ersetzen.

1. Führen Sie `dotnet run` im Stammverzeichnis Ihres Projekts aus. Wenn Sie dazu aufgefordert werden, verwenden Sie Ihr Benutzerprofil für die Anmeldung bei Azure.

## <a name="project-dependencies"></a>Projektabhängigkeiten

Es wird empfohlen, die neueste Version von Visual Studio und **NETCore.app** zu verwenden:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ab Version 16.4.2
* [NETCore.app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) Version 2.2.8

Der Beispielcode weist zwei erforderliche Abhängigkeiten auf:

* MSAL.NET [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) – Paket 4.7.1
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) – Paket 12.0.3

Fügen Sie die Pakete mit [NuGet ab Version 2.12](https://www.nuget.org/) hinzu:

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

Oder:

1. Deklarieren Sie eine Datei `csharp-tsi-msal-ga-sample.csproj`:

    ```XML
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <LangVersion>latest</LangVersion>
        <RootNamespace>csharp-tsi-msal-ga-sample</RootNamespace>
        <RunWorkingDirectory>$(MSBuildThisFileDirectory)</RunWorkingDirectory>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Identity.Client" Version="4.7.1.0" Culture="neutral" PublicKeyToken="0a613f4dd989e8ae" />
        <PackageReference Include="Newtonsoft.Json" Version="12.0.3" />
      </ItemGroup>
    </Project>
    ```

1. Führen Sie dann `dotnet restore` aus.

## <a name="c-sample-code"></a>C#-Beispielcode

```csharp
// Copyright (c) Microsoft Corporation.  All rights reserved.

namespace CsharpTsiMsalGaSample
{
    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    public static class Program
    {
        /**
         * Review the product documentation for detailed configuration steps or skip ahead and configure your environment settings.
         *
         * https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization
         */

        // Azure Time Series Insights environment configuration
        internal static string EnvironmentFqdn = "#PLACEHOLDER#.env.timeseries.azure.com";
        internal static string EnvironmentReferenceDataSetName = "#PLACEHOLDER#";

        // Azure Active Directory application configuration
        internal static string AadClientApplicationId = "#PLACEHOLDER#";
        internal static string[] AadScopes = new string[] { "https://api.timeseries.azure.com//user_impersonation" };
        internal static string AadRedirectUri = "http://localhost:8080/";
        internal static string AadTenantName = "#PLACEHOLDER#";
        internal static string AadAuthenticationAuthority = "https://login.microsoftonline.com/" + AadTenantName + ".onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/";

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (AadClientApplicationId == "#PLACEHOLDER#" || AadScopes.Length == 0 || AadRedirectUri == "#PLACEHOLDER#" || AadTenantName.StartsWith("#PLACEHOLDER#"))
            {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started"} to update the values of 'AadClientApplicationId', 'AadScopes', 'AadRedirectUri', and 'AadAuthenticationAuthority'.");
            }

            /**
             * MSAL.NET configuration. Review the product documentation for more information about MSAL.NET authentication options.
             *
             * https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/
             */

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(AadClientApplicationId)
                .WithRedirectUri(AadRedirectUri)
                .WithAuthority(AadAuthenticationAuthority)
                .Build();

            AuthenticationResult result = await app
                .AcquireTokenInteractive(AadScopes)
                .ExecuteAsync();

            Console.WriteLine("MSAL Authentication Token Acquired: {0}", result.AccessToken);
            Console.WriteLine("");
            return result.AccessToken;
        }

        // System.Net.HttpClient helper to wrap HTTP POST made to the GA Reference Data API
        private static async Task<HttpResponseMessage> AsyncHttpPostRequestHelper(HttpClient httpClient, string input)
        {
             if (EnvironmentFqdn.StartsWith("#PLACEHOLDER#") || EnvironmentReferenceDataSetName == "#PLACEHOLDER#")
             {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'EnvironmentFqdn' and 'EnvironmentReferenceDataSetName'.");
             }

             Console.WriteLine("HTTP JSON Request Body: {0}", input);
             Console.WriteLine("");
             HttpContent requestBody = new StringContent(input, Encoding.UTF8, "application/json");

             Uri uri = new UriBuilder("https", EnvironmentFqdn)
             {
                Path = $"referencedatasets/{EnvironmentReferenceDataSetName}/$batch",
                Query = "api-version=2016-12-12"
             }.Uri;

             Console.WriteLine("Making HTTP POST to URI: {0}", uri);
             Console.WriteLine("");

             HttpResponseMessage response = await httpClient.PostAsync(uri, requestBody);
             if (response.IsSuccessStatusCode)
             {
                var jsonString = await response.Content.ReadAsStringAsync();
                var jsonStringTransferObject = JsonConvert.DeserializeObject<object>(jsonString);
                Console.WriteLine("HTTP JSON Response Body: {0}", jsonStringTransferObject);
                Console.WriteLine("");
                return response;
             }

             return null;
        }

        private static async Task TsiMsalGaSample()
        {
            Console.WriteLine("Beginning demo...");
            Console.WriteLine("");
            Console.WriteLine("The following samples assume a single Key Name (uuid) with String type...");
            Console.WriteLine("");

            string accessToken = await AcquireAccessTokenAsync();
            var httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + accessToken);

            {
                // CREATE reference data
                Console.WriteLine("CREATE reference data example...");
                Console.WriteLine("");
                string createInput = @"
                    {
                        ""put"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""floor"": 2
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";


                var createResponse = await AsyncHttpPostRequestHelper(httpClient, createInput);

                // READ reference data
                Console.WriteLine("READ reference data example...");
                Console.WriteLine("");
                string readInput = @"
                    {
                        ""get"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""desc"": ""example""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";

                var readResponse = await AsyncHttpPostRequestHelper(httpClient, readInput);

                // UPDATE reference data
                Console.WriteLine("UPDATE reference data example...");
                Console.WriteLine("");
                string updateInput = @"
                    {
                        ""patch"": [
                            {
                                ""uuid"": ""Fan1"",
                                ""color"": ""Red""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""desc"": ""Example""
                            }
                        ]
                    }";

                var inputResponse = await AsyncHttpPostRequestHelper(httpClient, updateInput);

                // DELETE reference data
                Console.WriteLine("DELETE reference data example...");
                Console.WriteLine("");
                string deleteInput = @"
                    {
                        ""delete"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""desc"": ""Blue""
                            }
                        ]
                    }";

                var deleteResponse = await AsyncHttpPostRequestHelper(httpClient, deleteInput);
            }
        }

        internal static void Main(string[] args)
        {
            Task.Run(async () => await TsiMsalGaSample()).Wait();
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die Referenzdokumentation zur Gen1-[Verweisdatenverwaltungs-API](/rest/api/time-series-insights/gen1-reference-data-api).