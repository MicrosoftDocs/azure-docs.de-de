---
title: Verwenden von .NET SDK, um Kanäle zu erstellen, von denen eine Livecodierung von Single-Bitrate- zu Multi-Bitrate-Datenströmen vorgenommen wird | Microsoft Docs
description: In diesem Lernprogramm werden Sie durch die Schritte zum Erstellen eines Kanals mithilfe von .NET SDK geführt, von dem ein Single-Bitrate-Livedatenstrom empfangen und in einen Multi-Bitrate-Datenstrom codiert wird.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 4df5e690-ff63-47cc-879b-9c57cb8ec240
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: juliako;anilmur
ms.openlocfilehash: b2ff6caa4c1b3626e4be1684976441be6ecbfe30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61231402"
---
# <a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-net"></a>Verwenden von .NET SDK, um Kanäle zu erstellen, von denen eine Livecodierung von Single-Bitrate- zu Multi-Bitrate-Datenströmen vorgenommen wird
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST-API](https://docs.microsoft.com/rest/api/media/operations/channel)
> 
> [!NOTE]
> Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
> 
> 

## <a name="overview"></a>Übersicht
In diesem Lernprogramm werden Sie durch die Schritte zum Erstellen eines **Kanals** geführt, von dem ein Single-Bitrate-Livedatenstrom empfangen und in einen Multi-Bitrate-Datenstrom codiert wird.

Weitere grundlegende Informationen zu Kanälen, die für Livecodierung aktiviert sind, finden Sie unter [Arbeiten mit Kanälen, die zum Ausführen von Livecodierung mit Azure Media Services aktiviert wurden](media-services-manage-live-encoder-enabled-channels.md).

## <a name="common-live-streaming-scenario"></a>Allgemeines Livestreamingszenario
Die folgenden Schritte beschreiben die Aufgaben zum Erstellen von gebräuchlichen Livestreaminganwendungen.

> [!NOTE]
> Die maximal empfohlene Dauer eines Liveereignisses beträgt derzeit 8 Stunden. Wenden Sie sich an amslived@microsoft.com, falls Sie einen Kanal über längere Zeiträume ausführen müssen.

1. Schließen Sie eine Videokamera an einen Computer an. Starten und konfigurieren Sie einen lokalen Liveencoder, von dem ein Einzelbitraten-Datenstrom in einem der folgenden Protokolle ausgegeben wird: RTMP oder Smooth Streaming. Weitere Informationen finden Sie unter [Microsoft Azure Media Services RTMP-Support und Liveencoder](https://go.microsoft.com/fwlink/?LinkId=532824).

    Dieser Schritt kann auch nach der Erstellung des Kanals ausgeführt werden.

2. Erstellen Sie einen Kanal, und starten Sie ihn.
3. Rufen Sie die Erfassungs-URL des Kanals ab.

    Die Erfassungs-URL wird vom Liveencoder verwendet, um den Datenstrom an den Kanal zu senden.

4. Rufen Sie die Vorschau-URL des Kanals ab.

    Verwenden Sie diese URL, um sicherzustellen, dass der Livestream ordnungsgemäß vom Kanal empfangen wird.

5. Erstellen Sie ein Medienobjekt.
6. Wenn das Medienobjekt während der Wiedergabe dynamisch verschlüsselt werden soll, führen Sie folgende Schritte aus:
7. Erstellen Sie einen Inhaltsschlüssel.
8. Konfigurieren Sie eine Autorisierungsrichtlinie für Inhaltsschlüssel.
9. Konfigurieren Sie Übermittlungsrichtlinien für Medienobjekte (wird zur dynamischen Paketerstellung und zur dynamischen Verschlüsselung verwendet).
10. Erstellen Sie ein Programm, und legen Sie fest, dass das erstellte Medienobjekt verwendet werden soll.
11. Veröffentlichen Sie das dem Programm zugeordnete Medienobjekt, indem Sie einen OnDemand-Locator erstellen.

    >[!NOTE]
    >Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Der Streamingendpunkt, von dem aus Sie die Inhalte streamen möchten, muss sich im Status **Wird ausgeführt** befinden. 

12. Wenn Sie zum Starten von Streaming und Archivierung bereit sind, starten Sie das Programm.
13. Optional kann vom Liveencoder eine Ankündigung gestartet werden. Die Ankündigung wird in den Ausgabedatenstrom eingefügt.
14. Sie können das Programm und damit das Streaming und die Archivierung des Ereignisses jederzeit beenden.
15. Löschen Sie das Programm (und optional das Medienobjekt).

## <a name="what-youll-learn"></a>Sie lernen Folgendes
In diesem Artikel wird erläutert, wie mithilfe von Media Services .NET SDK verschiedene Vorgänge bei Kanälen und Programmen ausgeführt werden. Da viele Vorgänge langfristiger Art sind, werden .NET APIs verwendet, von denen langfristige Vorgänge verwaltet werden.

In diesem Artikel erfahren Sie, wie Sie die folgenden Aufgaben ausführen:

1. Erstellen Sie einen Kanal, und starten Sie ihn. Verwenden Sie langfristige APIs.
2. Erfassen Sie den Eingabeendpunkt der Kanäle. Dieser Endpunkt muss dem Encoder bereitgestellt werden, von dem ein Single-Bitrate-Livedatenstrom gesendet wird.
3. Rufen Sie den Vorschauendpunkt ab. Dieser Endpunkt wird verwendet, um Ihren Datenstrom in der Vorschau anzuzeigen.
4. Erstellen Sie ein Medienobjekt, das zum Speichern Ihrer Inhalte verwendet wird. Konfigurieren Sie außerdem Übermittlungsrichtlinien für Medienobjekte wie in diesem Beispiel gezeigt.
5. Erstellen Sie ein Programm, und legen Sie fest, dass das zuvor erstellte Medienobjekt verwendet werden soll. Starten Sie das Programm. Verwenden Sie langfristige APIs.
6. Erstellen Sie einen Locator für das Medienobjekt, damit der Inhalt veröffentlicht wird und an Ihre Kunden gestreamt werden kann.
7. Blenden Sie Slates ein und aus. Starten und Beenden Sie Werbespots. Verwenden Sie langfristige APIs.
8. Bereinigen Sie den Kanal und alle zugehörigen Ressourcen.

## <a name="prerequisites"></a>Voraussetzungen
Zum Abschließen dieses Lernprogramms müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Sie erhalten ein Guthaben, mit dem Sie andere kostenpflichtige Azure-Dienste ausprobieren können. Selbst, nachdem Sie dieses Guthaben aufgebraucht haben, können Sie das Konto behalten und kostenlose Azure-Dienste und -Features nutzen, z. B. das Web-Apps-Feature in Azure App Service.
* Media Services-Konto. Informationen zum Erstellen eines Media Services-Kontos finden Sie unter [Erstellen eines Kontos](media-services-portal-create-account.md).
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate oder Express) oder höhere Versionen.
* Sie müssen das Media Services .NET SDK, Version 3.2.0.0 oder höher, verwenden.
* Sie benötigen eine Webcam und einen Encoder, von dem ein Single-Bitrate-Livedatenstrom gesendet wird.

## <a name="considerations"></a>Überlegungen
* Die maximal empfohlene Dauer eines Liveereignisses beträgt derzeit 8 Stunden. Wenden Sie sich an amslived@Microsoft.com, falls Sie einen Kanal über längere Zeiträume ausführen müssen.
* Es gilt ein Grenzwert von 1.000.000 Richtlinien für verschiedene AMS-Richtlinien (z.B. für die Locator-Richtlinie oder für ContentKeyAuthorizationPolicy). Wenn Sie immer die gleichen Tage/Zugriffsberechtigungen verwenden, z.B. Richtlinien für Locator, die für einen längeren Zeitraum vorgesehen sind (Richtlinien ohne Upload), sollten Sie dieselbe Richtlinien-ID verwenden. Weitere Informationen dazu finden Sie in [diesem Artikel](media-services-dotnet-manage-entities.md#limit-access-policies).

## <a name="download-sample"></a>Beispiel herunterladen

Sie können das Beispiel, das in diesem Artikel beschrieben wird, [hier](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/) herunterladen.

## <a name="set-up-for-development-with-media-services-sdk-for-net"></a>Vorbereiten der Entwicklung mit Media Services SDK für .NET

Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben. 

## <a name="code-example"></a>Codebeispiel

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Net;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

namespace EncodeLiveStreamWithAmsClear
{
    class Program
    {
        private const string ChannelName = "channel001";
        private const string AssetName = "asset001";
        private const string ProgramName = "program001";

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

            IChannel channel = CreateAndStartChannel();

            // The channel's input endpoint:
            string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();

            Console.WriteLine("Intest URL: {0}", ingestUrl);


            // Use the previewEndpoint to preview and verify 
            // that the input from the encoder is actually reaching the Channel. 
            string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();

            Console.WriteLine("Preview URL: {0}", previewEndpoint);

            // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
            // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
            // The thumbnail is exposed via the same end-point as the Channel Preview URL.
            string thumbnailUri = new UriBuilder
            {
                Scheme = Uri.UriSchemeHttps,
                Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                Path = "thumbnails/input.jpg"
            }.Uri.ToString();

            Console.WriteLine("Thumbain URL: {0}", thumbnailUri);

            // Once you previewed your stream and verified that it is flowing into your Channel, 
            // you can create an event by creating an Asset, Program, and Streaming Locator. 
            IAsset asset = CreateAndConfigureAsset();

            IProgram program = CreateAndStartProgram(channel, asset);

            ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);

            // You can use slates and ads only if the channel type is Standard.  
            StartStopAdsSlates(channel);

            // Once you are done streaming, clean up your resources.
            Cleanup(channel);
        }

        public static IChannel CreateAndStartChannel()
        {
            var channelInput = CreateChannelInput();
            var channelPreview = CreateChannelPreview();
            var channelEncoding = CreateChannelEncoding();

            ChannelCreationOptions options = new ChannelCreationOptions
            {
                EncodingType = ChannelEncodingType.Standard,
                Name = ChannelName,
                Input = channelInput,
                Preview = channelPreview,
                Encoding = channelEncoding
            };

            Log("Creating channel");
            IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
            string channelId = TrackOperation(channelCreateOperation, "Channel create");

            IChannel channel = _context.Channels.FirstOrDefault(c => c.Id == channelId);

            Log("Starting channel");
            var channelStartOperation = channel.SendStartOperation();
            TrackOperation(channelStartOperation, "Channel start");

            return channel;
        }

        /// <summary>
        /// Create channel input, used in channel creation options. 
        /// </summary>
        /// <returns></returns>
        private static ChannelInput CreateChannelInput()
        {
            // When creating a Channel, you can specify allowed IP addresses in one of the following formats: 
            // IpV4 address with 4 numbers
            // CIDR address range

            return new ChannelInput
            {
                StreamingProtocol = StreamingProtocol.FragmentedMP4,
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                        Name = "TestChannelInput001",
                        Address = IPAddress.Parse("0.0.0.0"),
                        SubnetPrefixLength = 0
                        }
                    }
                }
            };
        }

        /// <summary>
        /// Create channel preview, used in channel creation options. 
        /// </summary>
        /// <returns></returns>
        private static ChannelPreview CreateChannelPreview()
        {
            // When creating a Channel, you can specify allowed IP addresses in one of the following formats: 
            // IpV4 address with 4 numbers
            // CIDR address range

            return new ChannelPreview
            {
                AccessControl = new ChannelAccessControl
                {
                    IPAllowList = new List<IPRange>
                {
                    new IPRange
                    {
                    Name = "TestChannelPreview001",
                    Address = IPAddress.Parse("0.0.0.0"),
                    SubnetPrefixLength = 0
                    }
                }
                }
            };
        }

        /// <summary>
        /// Create channel encoding, used in channel creation options. 
        /// </summary>
        /// <returns></returns>
        private static ChannelEncoding CreateChannelEncoding()
        {
            return new ChannelEncoding
            {
                SystemPreset = "Default720p",
                IgnoreCea708ClosedCaptions = false,
                AdMarkerSource = AdMarkerSource.Api
            };
        }

        /// <summary>
        /// Create an asset and configure asset delivery policies.
        /// </summary>
        /// <returns></returns>
        public static IAsset CreateAndConfigureAsset()
        {
            IAsset asset = _context.Assets.Create(AssetName, AssetCreationOptions.None);

            IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption,
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

            asset.DeliveryPolicies.Add(policy);

            return asset;
        }

        /// <summary>
        /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
        /// however each Program must have a unique name within your Media Services account.
        /// </summary>
        /// <param name="channel"></param>
        /// <param name="asset"></param>
        /// <returns></returns>
        public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
        {
            IProgram program = channel.Programs.Create(ProgramName, TimeSpan.FromHours(3), asset.Id);
            Log("Program created", program.Id);

            Log("Starting program");
            var programStartOperation = program.SendStartOperation();
            TrackOperation(programStartOperation, "Program start");

            return program;
        }

        /// <summary>
        /// Create locators in order to be able to publish and stream the video.
        /// </summary>
        /// <param name="asset"></param>
        /// <param name="ArchiveWindowLength"></param>
        /// <returns></returns>
        public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
        {
            // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
            var locator = _context.Locators.CreateLocator
            (
                LocatorType.OnDemandOrigin,
                asset,
                _context.AccessPolicies.Create
                (
                    "Live Stream Policy",
                    ArchiveWindowLength,
                    AccessPermissions.Read
                )
            );

            return locator;
        }

        /// <summary>
        /// Perform operations on slates.
        /// </summary>
        /// <param name="channel"></param>
        public static void StartStopAdsSlates(IChannel channel)
        {
            int cueId = new Random().Next(int.MaxValue);
            var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));

            Log("Creating asset");
            var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
            Log("Slate asset created", slateAsset.Id);

            Log("Uploading file");
            var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
            assetFile.Upload(path);
            assetFile.IsPrimary = true;
            assetFile.Update();

            Log("Showing slate");
            var showSlateOperation = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
            TrackOperation(showSlateOperation, "Show slate");

            Log("Hiding slate");
            var hideSlateOperation = channel.SendHideSlateOperation();
            TrackOperation(hideSlateOperation, "Hide slate");

            Log("Starting ad");
            var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
            TrackOperation(startAdOperation, "Start ad");

            Log("Ending ad");
            var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
            TrackOperation(endAdOperation, "End ad");

            Log("Deleting slate asset");
            slateAsset.Delete();
        }

        /// <summary>
        /// Clean up resources associated with the channel.
        /// </summary>
        /// <param name="channel"></param>
        public static void Cleanup(IChannel channel)
        {
            IAsset asset;
            if (channel != null)
            {
                foreach (var program in channel.Programs)
                {
                    asset = _context.Assets.FirstOrDefault(se => se.Id == program.AssetId);

                    Log("Stopping program");
                    var programStopOperation = program.SendStopOperation();
                    TrackOperation(programStopOperation, "Program stop");

                    program.Delete();

                    if (asset != null)
                    {
                        Log("Deleting locators");
                        foreach (var l in asset.Locators)
                            l.Delete();

                        Log("Deleting asset");
                        asset.Delete();
                    }
                }

                Log("Stopping channel");
                var channelStopOperation = channel.SendStopOperation();
                TrackOperation(channelStopOperation, "Channel stop");

                Log("Deleting channel");
                var channelDeleteOperation = channel.SendDeleteOperation();
                TrackOperation(channelDeleteOperation, "Channel delete");
            }
        }

        /// <summary>
        /// Track long running operations.
        /// </summary>
        /// <param name="operation"></param>
        /// <param name="description"></param>
        /// <returns></returns>
        public static string TrackOperation(IOperation operation, string description)
        {
            string entityId = null;
            bool isCompleted = false;

            Log("starting to track ", null, operation.Id);
            while (isCompleted == false)
            {
                operation = _context.Operations.GetOperation(operation.Id);
                isCompleted = IsCompleted(operation, out entityId);
                System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
            }
            // If we got here, the operation succeeded.
            Log(description + " in completed", operation.TargetEntityId, operation.Id);

            return entityId;
        }

        /// <summary> 
        /// Checks if the operation has been completed. 
        /// If the operation succeeded, the created entity Id is returned in the out parameter.
        /// </summary> 
        /// <param name="operationId">The operation Id.</param> 
        /// <param name="channel">
        /// If the operation succeeded, 
        /// the entity Id associated with the successful operation is returned in the out parameter.</param>
        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
        private static bool IsCompleted(IOperation operation, out string entityId)
        {
            bool completed = false;

            entityId = null;

            switch (operation.State)
            {
                case OperationState.Failed:
                    // Handle the failure. 
                    // For example, throw an exception. 
                    // Use the following information in the exception: operationId, operation.ErrorMessage.
                    Log("operation failed", operation.TargetEntityId, operation.Id);
                    break;
                case OperationState.Succeeded:
                    completed = true;
                    entityId = operation.TargetEntityId;
                    break;
                case OperationState.InProgress:
                    completed = false;
                    Log("operation in progress", operation.TargetEntityId, operation.Id);
                    break;
            }
            return completed;
        }

        private static void Log(string action, string entityId = null, string operationId = null)
        {
            Console.WriteLine(
            "{0,-21}{1,-51}{2,-51}{3,-51}",
            DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
            action,
            entityId ?? string.Empty,
            operationId ?? string.Empty);
        }
    }
}
```

## <a name="next-step"></a>Nächster Schritt
Überprüfen Sie die Media Services-Lernpfade.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


