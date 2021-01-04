---
title: Erste Schritte zum Bereitstellen von Inhalten nach Bedarf mithilfe von .NET | Microsoft Docs
description: Dieses Lernprogramm führt Sie durch die Schritte zum Implementieren einer Anwendung zur Übermittlung von Inhalten nach Bedarf mit Azure Media Services mithilfe von .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: bbbf924ea950c5ba281be430190370ae77734b7a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184398"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Erste Schritte zum Bereitstellen von Inhalten nach Bedarf mit dem .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie mit dem Azure Media Services .NET SDK einen einfachen Dienst zur Übermittlung von VoD-Inhalten (Video-on-Demand) mit der AMS-Anwendung (Azure Media Services) implementieren.

## <a name="prerequisites"></a>Voraussetzungen

Zum Abschließen dieses Lernprogramms müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Konto. Ausführliche Informationen finden Sie unter [Einen Monat kostenlos testen](https://azure.microsoft.com/pricing/free-trial/).
* Media Services-Konto. Informationen zum Erstellen eines Media Services-Kontos finden Sie unter [Gewusst wie: Erstellen eines Media Services Kontos](media-services-portal-create-account.md).
* .NET Framework 4.0 oder höher.
* Visual Studio.

Dieses Lernprogramm enthält die folgenden Aufgaben:

1. Starten von Streamingendpunkten (mithilfe des Azure-Portals)
2. Erstellen und Konfigurieren eines Visual Studio-Projekts
3. Herstellen einer Verbindung mit dem Media Services-Konto
2. Hochladen einer Videodatei
3. Codieren der Quelldatei in einen Satz von MP4-Dateien mit adaptiver Bitrate
4. Veröffentlichen des Medienobjekts und Abrufen von URLs für Streaming und progressiven Download  
5. Wiedergeben Ihrer Inhalte

## <a name="overview"></a>Übersicht
Dieses Lernprogramm führt Sie durch die Schritte zum Implementieren einer Anwendung zur Video-on-Demand (VoD)-Inhaltsübermittlung mithilfe des Azure Media Services (AMS) SDK für .NET.

Das Lernprogramm stellt den Media Services-Arbeitsfluss und die gängigsten Programmierobjekte und Aufgaben für die Media Services-Entwicklung vor. Nach Abschluss des Lernprogramms sind Sie in der Lage, eine einfache Mediendatei zu streamen oder progressiv herunterzuladen, die Sie hochgeladen, codiert und heruntergeladen haben.

### <a name="ams-model"></a>AMS-Modell

Die folgende Abbildung zeigt einige der am häufigsten verwendeten Objekte beim Entwickeln von VoD-Anwendungen mit dem Media Services OData-Modell.

Klicken Sie auf Bild, um es in voller Größe anzeigen.  

[![Diagramm mit einigen der am häufigsten verwendeten Objekte im Objektdatenmodell von Azure Media Services zur Entwicklung von Video on Demand-Anwendungen.](./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png)](./media/media-services-dotnet-get-started/media-services-overview-object-model.png#lightbox)

Sie können das gesamte Modell [hier](https://media.windows.net/API/$metadata?api-version=2.15) anzeigen.  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Starten von Streamingendpunkten mithilfe des Azure-Portals

Wenn Sie mit Azure Media Services arbeiten, besteht eines der häufigsten Szenarien darin, Videos per Adaptive Bitrate Streaming zu übermitteln. Media Services bietet dynamische Paketerstellung für die Just-in-Time-Übermittlung von Daten vom Typ „MP4-codierte Inhalte mit adaptiver Bitrate“ in Streamingformaten, die von Media Services unterstützt werden (MPEG DASH, HLS, Smooth Streaming), ohne dass Sie jeweils vorab verpackte Versionen dieser Streamingformate speichern müssen.

>[!NOTE]
>Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen.

Führen Sie folgende Schritte aus, um den Streamingendpunkt zu starten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie im Fenster „Einstellungen“ auf „Streamingendpunkte“.
3. Klicken Sie auf den standardmäßigen Streamingendpunkt.

    Das Fenster DEFAULT STREAMING ENDPOINT DETAILS (DETAILS ZUM STANDARD-STREAMINGENDPUNKT) wird angezeigt.

4. Klicken Sie auf das Symbol „Start“.
5. Klicken Sie auf die Schaltfläche „Speichern“, um die Änderungen zu speichern.

## <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts

1. Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben. 
2. Erstellen Sie (unter einem beliebigen Pfad auf Ihrem lokalen Laufwerk) einen neuen Ordner, und kopieren Sie eine MP4-Datei, die Sie codieren und streamen oder progressiv herunterladen möchten. In diesem Beispiel wird der Pfad "C:\VideoFiles" verwendet.

## <a name="connect-to-the-media-services-account"></a>Herstellen einer Verbindung mit dem Media Services-Konto

Wenn Sie Media Services mit .NET verwenden, müssen Sie die **CloudMediaContext**-Klasse für die meisten Media Services-Programmieraufgaben verwenden, z.B. für das Herstellen einer Verbindung mit dem Media Services-Konto, das Erstellen, Aktualisieren, Zugreifen auf und Löschen der folgenden Objekte: Medienobjekte, Objektdateien, Aufträge, Zugriffsrichtlinien, Locators usw.

Überschreiben Sie die standardmäßige Program-Klasse mit dem folgenden Code: Im Code wird veranschaulicht, wie die Werte aus der Datei "App.config" gelesen werden und das **CloudMediaContext** -Objekt erstellt wird, um eine Verbindung zu Media Services herzustellen. Weitere Informationen finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md).

Achten Sie darauf, den Dateinamen und den Pfad zu aktualisieren, unter dem sich Ihre Mediendatei befindet.

Die **Main** -Funktion ruft Methoden auf, die weiter unten in diesem Abschnitt definiert werden.

> [!NOTE]
> Sie erhalten so lange Kompilierungsfehler, bis Sie Definitionen für alle Funktionen hinzufügen, die weiter unten in diesem Artikel definiert sind.

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
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Erstellen eines neuen Medienobjekts und Hochladen einer Videodatei

In Media Services laden Sie Ihre digitalen Dateien in ein Medienobjekt hoch oder erfassen sie auf diese Weise. Die **Asset**-Entität kann Videos, Audiodateien, Bilder, Miniaturansichtssammlungen, Texttitel und Untertiteldateien (und die Metadaten zu diesen Dateien) enthalten.  Nachdem die Dateien hochgeladen wurden, werden Ihre Inhalte zur weiteren Verarbeitung und zum Streaming sicher in der Cloud gespeichert. Die Dateien im Medienobjekt heißen **Medienobjektdateien**.

Die unten definierte **UploadFile**-Methode ruft **CreateFromFile** auf (in den .NET SDK-Erweiterungen definiert). **CreateFromFile** erstellt ein neues Medienobjekt, in das die angegebene Quelldatei hochgeladen wird.

Die **CreateFromFile**-Methode übernimmt **AssetCreationOptions**, sodass Sie eine der folgenden Erstellungsoptionen für Medienobjekte angeben können:

* **None:** Es wird keine Verschlüsselung verwendet. Dies ist der Standardwert. Beachten Sie, dass bei Verwendung dieser Option Ihre Inhalte während der Übertragung oder des Verbleibs im Speicher nicht geschützt sind.
  Wenn Sie planen, eine MP4-Datei über progressives Herunterladen zu übermitteln, verwenden Sie diese Option.
* **StorageEncrypted:** Verwenden Sie diese Option, um Ihre unverschlüsselten Inhalte lokal mithilfe der AES-256-Bit-Verschlüsselung (Advanced Encryption Standard) zu verschlüsseln. Anschließend werden sie in Azure Storage hochgeladen, wo sie verschlüsselt im Ruhezustand gespeichert werden. Medienobjekte, die durch Storage Encryption geschützt sind, werden automatisch entschlüsselt, vor der Codierung in einem verschlüsselten Dateisystem platziert und optional vor dem Hochladen als neues Ausgabemedienobjekt erneut verschlüsselt. Der primäre Anwendungsfall für Storage Encryption ist, wenn Sie Ihre qualitativ hochwertigen Eingabemediendateien mit starker Verschlüsselung beim Speichern im Ruhezustand auf dem Datenträger sichern möchten.
* **CommonEncryptionProtected:** Verwenden Sie diese Option, wenn Sie Inhalte hochladen, die bereits verschlüsselt wurden und durch allgemeine Verschlüsselung oder PlayReady-DRM geschützt sind (z.B. mit PlayReady-DRM geschütztem Smooth Streaming).
* **EnvelopeEncryptionProtected** : Verwenden Sie diese Option, wenn Sie mit AES verschlüsseltes HLS hochladen. Beachten Sie, dass die Dateien durch Transform Manager codiert und verschlüsselt sein müssen.

Mit der **CreateFromFile** -Methode können Sie auch einen Rückruf angeben, um den Fortschritt des Dateiuploads zu melden.

Im folgenden Beispiel wird **Kein** für die Medienobjektoptionen angegeben.

Fügen Sie die folgende Methode zur Program-Klasse hinzu.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codieren der Quelldatei in einen Satz von MP4-Dateien mit adaptiver Bitrate
Nach dem Erfassen der Medienobjekte in Media Services können die Medien u. a. codiert, transcodiert/multiplexiert und mit einem Wasserzeichen versehen werden, bevor sie an die Clients übermittelt werden. Diese Aktivitäten werden geplant und für mehrere Hintergrundrolleninstanzen ausgeführt, um hohe Leistung und Verfügbarkeit zu gewährleisten. Diese Aktivitäten werden als Aufträge bezeichnet, und jeder Auftrag besteht aus atomaren Tasks, welche die eigentliche Arbeit für die Medienobjektdatei leisten.

Wie bereits erwähnt, besteht beim Arbeiten mit Azure Media Services eines der häufigsten Szenarios darin, Adaptive Bitrate Streaming an Ihre Clients zu übermitteln. Media Services kann eine Reihe von MP4-Dateien mit adaptiver Bitrate dynamisch in eines der folgenden Formate verpacken: HTTP Live Streaming (HLS), Smooth Streaming und MPEG DASH.

Um die dynamische Paketerstellung nutzen zu können, müssen Sie Ihre Zwischendatei (Quelldatei) in einen Satz von MP4-Dateien oder Smooth Streaming-Dateien mit adaptiver Bitrate codieren oder transcodieren.  

Der folgende Code zeigt, wie Sie einen Codierungsauftrag senden. Der Auftrag enthält eine Aufgabe zum Transcodieren der Zwischendatei in eine Reihe von MP4-Dateien mit adaptiver Bitrate mithilfe von **Media Encoder Standard**. Der Code sendet den Auftrag und wartet, bis er abgeschlossen ist.

Wenn der Auftrag abgeschlossen ist, sind Sie in der Lage, das Medienobjekt zu streamen oder MP4-Dateien, die als Ergebnis der Transcodierung erstellt wurden, progressiv herunterzuladen.

Fügen Sie die folgende Methode zur Program-Klasse hinzu.

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Veröffentlichen des Medienobjekts und Abrufen von URLs für Streaming und progressiven Download

Um ein Medienobjekt zu streamen oder herunterzuladen, müssen Sie es zunächst durch Erstellen eines Locators "veröffentlichen". Locators ermöglichen den Zugriff auf Dateien im Medienobjekt. Media Services unterstützt zwei Arten von Locatorobjekten: OnDemandOrigin-Locator zum Streamen von Medien (z. B. MPEG DASH, HLS oder Smooth Streaming) und Access Signature-Locator (SAS) zum Herunterladen von Mediendateien.

### <a name="some-details-about-url-formats"></a>Einige Details zu URL-Formaten

Nachdem Sie die Locator erstellt haben, können Sie die URLs erstellen, mit denen Sie die Dateien streamen oder herunterladen möchten. Das Beispiel in diesem Tutorial gibt URLs aus, die Sie in den entsprechenden Browsern einfügen können. Dieser Abschnitt enthält kurze Beispiele für verschiedene Formate.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>Eine Streaming-URL für MPEG DASH hat das folgende Format:

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest **(Format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>Eine Streaming-URL für HLS hat das folgende Format:

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest **(Format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Eine URL für Smooth Streaming hat das folgende Format:

{Streamingendpunktname-Media Services-Kontoname}.streaming.mediaservices.windows.net/{Locator-ID}/{Dateiname}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>Eine SAS-URL zum Herunterladen von Dateien hat das folgende Format:

{Blob-Containername}/{Ressourcenname}/{Dateiname}/{SAS-Signatur}

Media Services .NET SDK-Erweiterungen bieten praktische Hilfsmethoden, die formatierte URLs für das veröffentlichte Medienobjekt zurückgeben.

Der folgende Code verwendet .NET SDK-Erweiterungen, um Locators zu erstellen sowie Streaming-URLs und URLs für progressive Downloads abzurufen. Der Code zeigt auch das Herunterladen von Dateien in einen lokalen Ordner.

Fügen Sie die folgende Methode zur Program-Klasse hinzu.

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>Testen der Funktionalität durch Wiedergabe von Inhalten

Nachdem Sie das im vorherigen Abschnitt definierte Programm ausgeführt haben, werden ähnliche URLs wie die folgenden im Konsolenfenster angezeigt.

Adaptive Streaming-URLs:

Smooth Streaming

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

HLS

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

MPEG DASH

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

URLs für progressiven Download (Audio und Video).

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

`https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`


Zum Streamen von Videos fügen Sie die URL im Textfeld „URL“ des [Azure Media Services-Players](https://aka.ms/azuremediaplayer) ein.

Fügen Sie zum Testen des progressiven Downloads eine URL in einen Browser ein (z. B. Internet Explorer, Chrome oder Safari).

Weitere Informationen finden Sie in den folgenden Themen:

- [Wiedergabe Ihrer Inhalte mit vorhandenen Playern](media-services-playback-content-with-existing-players.md)
- [Einbetten eines Videos mit adaptivem Streaming im MPEG-DASH-Format in eine HTML5-Anwendung mit DASH.js](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Beispiel herunterladen
Das folgende Codebeispiel enthält den Code, den Sie in diesem Tutorial erstellt haben: [Beispiel](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: https://go.microsoft.com/fwlink/?linkid=255386
[Portal]: https://portal.azure.com/
