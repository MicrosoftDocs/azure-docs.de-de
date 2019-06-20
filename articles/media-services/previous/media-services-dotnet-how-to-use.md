---
title: Einrichten des Computers für die Media Services-Entwicklung mit .NET
description: Lernen Sie die Voraussetzungen für Media Services bei Verwendung des Media Services SDK für .NET kennen. Erfahren Sie zudem, wie Sie eine Visual Studio-App erstellen.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 51fffbd170daecfec6fcea95caa0526e6d881407
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724121"
---
# <a name="media-services-development-with-net"></a>Media Services-Entwicklung mit .NET 

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

In diesem Artikel wird das Entwickeln von Media Services-Anwendungen mit .NET erläutert.

Die Bibliothek für das **Azure Media Services .NET SDK** ermöglicht es Ihnen, unter Verwendung von .NET für Media Services zu programmieren. Um die Entwicklung mit .NET weiter zu vereinfachen, steht die Bibliothek für **Azure Media Services .NET SDK-Erweiterungen** zur Verfügung. Diese Bibliothek enthält eine Reihe von Erweiterungsmethoden und Hilfsfunktionen, die Ihren .NET-Code vereinfachen. Beide Bibliotheken sind über **NuGet** und **GitHub** verfügbar.

## <a name="prerequisites"></a>Voraussetzungen
* Ein Mediendienstekonto in einem neuen oder existierenden Azure-Abonnement. Weitere Informationen finden Sie unter [Erstellen eines Media Services-Kontos](media-services-portal-create-account.md).
* Betriebssysteme: Windows 10, Windows 7, Windows 2008 R2 oder Windows 8.
* .NET Framework 4.5 oder höher.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts
Dieser Abschnitt beschreibt, wie Sie ein Projekt in Visual Studio erstellen und für die Mediendienste-Entwicklung einrichten.  Es handelt sich um eine C# Windows-Konsolenanwendung, allerdings gelten für andere Typen von Projekten für Media Services-Anwendungen (z.B. eine Windows Forms-Anwendung oder ASP.NET-Webanwendung) dieselben Schritte.

In diesem Abschnitt wird beschrieben, wie Sie mithilfe von **NuGet** Media Services .NET SDK-Erweiterungen und andere abhängige Bibliotheken hinzufügen.

Sie können auch die neuesten Media Services .NET SDK-Komponenten von GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) oder [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)) herunterladen, die Projektmappe erstellen und die Verweise dem Clientprojekt hinzufügen. Alle erforderlichen Abhängigkeiten werden automatisch heruntergeladen und extrahiert.

1. Erstellen Sie eine neue C#-Konsolenanwendung in Visual Studio. Geben Sie **Name**, **Speicherort** und **Projektmappenname** ein und klicken Sie auf „OK“.
2. Erstellen Sie die Projektmappe.
3. Verwenden Sie **NuGet**, um die **Azure Media Services .NET SDK-Erweiterungen** (**windowsazure.mediaservices.extensions**) zu installieren. Durch Installieren dieses Pakets werden auch das **Media Services .NET SDK** installiert und alle anderen erforderlichen Abhängigkeiten hinzugefügt.
   
    Stellen Sie sicher, dass Sie die neueste Version von NuGet installiert haben. Weitere Informationen und Installationsanweisungen finden Sie unter [NuGet](https://nuget.codeplex.com/).

    1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Namen des Projekts, und wählen Sie **NuGet-Pakete verwalten** aus.

    2. Das Dialogfeld NuGet-Pakete verwalten wird geöffnet.

    3. Suchen Sie im Onlinekatalog nach den Azure Media Services-Erweiterungen, wählen Sie die **Azure Media Services .NET SDK-Erweiterungen** (**windowsazure.mediaservices.extensions**) aus, und klicken Sie dann auf die Schaltfläche **Installieren**.
   
    4. Das Projekt wird geändert, und Verweise auf die Media Services .NET SDK-Erweiterungen, das Media Services .NET-SDK und andere abhängige Assemblys werden hinzugefügt.
4. Um die Entwicklungsumgebung übersichtlicher zu halten, sollten Sie die Verwendung von NuGet Package Restore in Erwägung ziehen. Weitere Informationen finden Sie unter [NuGet Package Restore](https://docs.nuget.org/consume/package-restore).
5. Fügen Sie einen Verweis auf die Assembly **System.Configuration** hinzu. Diese Assembly enthält die **System.Configuration.ConfigurationManager**-Klasse, die zum Zugriff auf die Konfigurationsdateien (z.B. „App.config“) verwendet wird.
   
    1. Wenn Sie Verweise mit dem Dialogfeld zur Verweisverwaltung hinzufügen möchten, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen. Klicken Sie auf **Hinzufügen** und anschließend auf **Verweis...** .
   
    2. Das Dialogfeld „Verweise verwalten“ wird angezeigt.
    3. Suchen Sie unter den .NET Framework-Assemblys die Assembly „System.Configuration“, und wählen Sie sie aus. Klicken Sie anschließend auf **OK**.
6. Öffnen Sie die Datei „App.config“, und fügen Sie der Datei einen **appSettings**-Abschnitt hinzu. Legen Sie die Werte fest, die zur Verbindung mit der Media Services-API erforderlich sind. Weitere Informationen finden Sie unter [Access the Azure Media Services API with Azure AD authentication (Zugriff auf die Azure Media Services-API mit der Azure AD-Authentifizierung)](media-services-use-aad-auth-to-access-ams-api.md). 

    Legen Sie die Werte fest, die zum Herstellen der Verbindung mit der Authentifizierungsmethode **Dienstprinzipal** benötigt werden.

        ```csharp
                <configuration>
                ...
                    <appSettings>
                        <add key="AMSAADTenantDomain" value="tenant"/>
                        <add key="AMSRESTAPIEndpoint" value="endpoint"/>
                        <add key="AMSClientId" value="id"/>
                        <add key="AMSClientSecret" value="secret"/>
                    </appSettings>
                </configuration>
        ```

7. Fügen Sie den Verweis **System.Configuration** zu Ihrem Projekt hinzu.
8. Überschreiben Sie die existierenden **using** -Anweisungen am Anfang der Datei „Program.cs“ durch den folgenden Code:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    Nun können Sie mit der Entwicklung einer Mediendienste-Anwendung beginnen.    

## <a name="example"></a>Beispiel

Hier ist ein kurzes Beispiel, bei dem eine Verbindung mit der AMS-API hergestellt wird und alle verfügbaren Medienprozessoren aufgelistet werden.

```csharp
        class Program
        {
            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];
        
            private static CloudMediaContext _context = null;
            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
                // List all available Media Processors
                foreach (var mp in _context.MediaProcessors)
                {
                    Console.WriteLine(mp.Name);
                }
        
            }
 ```

## <a name="next-steps"></a>Nächste Schritte

Jetzt [können Sie sich mit der API AMS verbinden](media-services-use-aad-auth-to-access-ams-api.md) und mit dem [Entwickeln](media-services-dotnet-get-started.md) beginnen.


## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

