---
title: Signalisieren beschreibender Audiospuren mit Media Services v3
description: Führen Sie die Schritte in diesem Tutorial aus, um eine Datei hochzuladen, das Video zu codieren, beschreibende Audiospuren hinzuzufügen und Ihre Inhalte mit Media Services v3 zu streamen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3d029f23a094646d20dd6ae8cb6560aeef4aed54
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954511"
---
# <a name="signal-descriptive-audio-tracks"></a>Signalisieren beschreibender Audiospuren

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Sie können dem Video eine Audiokommentarspur hinzufügen, damit Benutzer mit Sehbehinderung dem Video folgen können, indem sie sich den Audiokommentar anhören. In Media Services v3 signalisieren Sie beschreibende Audiospuren, indem Sie die Audiospur in der Manifestdatei kommentieren.

Dieser Artikel erläutert, wie Sie ein Video codieren, eine nur Audio enthaltende MP4-Datei (AAC-Codec) mit beschreibenden Audioinhalten in das Ausgabemedienobjekt hochladen und die ISM-Datei so bearbeiten, dass sie die beschreibende Audiospur enthält.

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md)
- Führen Sie die Schritte unter [Zugreifen auf die Azure Media Services-API mit der Azure CLI](./access-api-howto.md) aus, und speichern Sie die Anmeldeinformationen. Sie benötigen sie für den Zugriff auf die API.
- Lesen Sie [Dynamische Paketerstellung](dynamic-packaging-overview.md).
- Arbeiten Sie das Tutorial [Hochladen, Codieren und Streamen von Videos](stream-files-tutorial-with-api.md) durch.

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Erstellen eines Eingabeobjekts und Hochladen einer lokalen Datei in dieses 

Die **CreateInputAsset**-Funktion erstellt ein neues [Eingabeobjekt](/rest/api/media/assets) und lädt die angegebene lokale Videodatei in dieses hoch. Dieses **Objekt** wird als Eingabe für Ihren Codierungsauftrag verwendet. In Media Services v3 kann die Eingabe in einen **Auftrag** ein **Objekt** sein, oder es kann sich um Inhalte handeln, die Sie für Ihr Media Services-Konto über HTTPS-URLs zur Verfügung stellen. 

Wenn Sie erfahren möchten, wie die Codierung aus einer HTTPS-URL erfolgt, lesen Sie [diesen](job-input-from-http-how-to.md) Artikel.  

In Media Services v3 verwenden Sie Azure Storage-APIs zum Hochladen von Dateien. Im folgenden Codeausschnitt wird gezeigt, wie dies funktioniert.

Die folgende Funktion führt diese Aktionen aus:

* Erstellen eines **Objekts** 
* Abrufen einer nicht schreibgeschützten [SAS-URL](../../storage/common/storage-sas-overview.md) für den [Container](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) des Objekts im Speicher
* Die Datei in den Container im Speicher mithilfe der SAS-URL hochladen

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Wenn Sie den Namen des erstellten Eingabemedienobjekts an andere Methoden übergeben müssen, achten Sie darauf, die `Name`-Eigenschaft des von `CreateInputAssetAsync` zurückgegebenen Medienobjekts zu verwenden, beispielsweise „inputAsset.Name“. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Erstellen eines Ausgabemedienobjekts zum Speichern des Ergebnisses des Codierungsauftrags

Das [Ausgabeobjekt](/rest/api/media/assets) speichert das Ergebnis Ihres Codierungsauftrags. Die folgende Funktion zeigt das Erstellen eines Ausgabemedienobjekts.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Wenn Sie den Namen des erstellten Ausgabemedienobjekts an andere Methoden übergeben müssen, achten Sie darauf, die `Name`-Eigenschaft des von `CreateIOutputAssetAsync` zurückgegebenen Medienobjekts zu verwenden, beispielsweise „outputAsset.Name“. 

Übergeben Sie im Rahmen dieses Artikels den Wert `outputAsset.Name` an die Funktionen `SubmitJobAsync` und `UploadAudioIntoOutputAsset`.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Erstellen einer Transformation und eines Auftrags, der die hochgeladene Datei codiert

Bei der Codierung oder Verarbeitung von Inhalten in Media Services besteht ein allgemeines Muster darin, die Codierungseinstellungen als eine Anleitung einzurichten. Anschließend übermitteln Sie einen **Auftrag**, um diese Anleitung auf ein Video anzuwenden. Durch die Übermittlung von neuen Aufträgen für jedes neue Video wenden Sie diese Anleitung auf alle Videos in Ihrer Bibliothek an. Eine Anleitung wird in Media Services als eine **Transformation** aufgerufen. Weitere Informationen finden Sie unter [Transformationen und Aufträge](./transforms-jobs-concept.md). Das in diesem Tutorial beschriebene Beispiel definiert eine Anleitung, die das Video codiert, damit es auf eine Vielzahl von iOS- und Android-Geräte gestreamt werden kann. 

Im folgenden Beispiel wird eine Transformation erstellt (falls keine vorhanden ist).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

Mit der folgenden Funktion wird ein Auftrag übermittelt.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Warten auf den Abschluss des Auftrags

Der Abschluss des Auftrags nimmt einige Zeit in Anspruch. Wenn er erfolgt ist, möchten Sie benachrichtigt werden. Es empfiehlt sich, Event Grid zu verwenden, um den Abschluss des Auftrags abzuwarten.

Der Auftrag durchläuft in der Regel die folgenden Zustände: **Geplant**, **In Warteschlange**, **Wird verarbeitet**, **Abgeschlossen** (Endzustand). Wenn für den Auftrag ein Fehler aufgetreten ist, erhalten Sie den Zustand **Fehler**. Wenn der Auftrag aktuell abgebrochen wird, erhalten Sie **Abbrechen** und **Abgebrochen**, wenn dies geschehen ist.

Weitere Informationen finden Sie unter [Behandeln von Event Grid-Ereignissen](reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Laden Sie die nur das Audiosignal enthaltende MP4-Datei hoch

Laden Sie die zusätzliche, nur das beschreibende Audiosignal enthaltende MP4-Datei (AAC-Codec) in das Ausgabemedienobjekt hoch.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Dies ist ein Beispiel für einen Aufruf der `UpoadAudioIntoOutputAsset`-Funktion:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Bearbeiten der ISM-Datei

Wenn Ihr Codierungsauftrag abgeschlossen ist, enthält das Ausgabemedienobjekt die vom Codierungsauftrag generierten Dateien. 

1. Navigieren Sie im Azure-Portal zu dem Speicherkonto, das ihrem Media Services-Konto zugeordnet ist. 
1. Suchen Sie den Container mit dem Namen Ihres Ausgabemedienobjekts. 
1. Suchen Sie im Container die ISM-Datei, und klicken Sie auf **Blob bearbeiten** (im rechten Fenster). 
1. Bearbeiten Sie die ISM-Datei, indem Sie die Informationen über die hochgeladene MP4-Datei (AAC-Codec) hinzufügen, die nur das beschreibende Audiosignal enthält, und drücken Sie **Speichern**, wenn Sie fertig sind.

    Um die beschreibenden Audiospuren zu signalisieren, müssen Sie der ISM-Datei die Parameter „accessibility“ und „role“ hinzufügen. Sie sind dafür verantwortlich, diese Parameter korrekt festzulegen, um eine Audiospur als Audiobeschreibung zu signalisieren. Fügen Sie beispielsweise `<param name="accessibility" value="description" />` und `<param name="role" value="alternate" />` zur ISM-Datei für eine bestimmte Audiospur hinzu, wie im folgenden Beispiel dargestellt.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Abrufen eines Streaminglocators

Nachdem die Codierung abgeschlossen ist, besteht der nächste Schritt darin, das Video im Ausgabeobjekt Clients für die Wiedergabe zur Verfügung zu stellen. Sie können dies in zwei Schritten bewerkstelligen: Erstellen Sie zunächst einen [Streaminglocator](/rest/api/media/streaminglocators) und dann die Streaming-URLs, die Clients verwenden können. 

Der Vorgang zum Erstellen eines **Streaminglocators** wird als „Veröffentlichen“ bezeichnet. Standardmäßig ist der **Streaminglocator** sofort nach dem Vornehmen der API-Aufrufe gültig und bleibt es auch, bis er gelöscht wird (es sei denn, Sie konfigurieren die optionalen Start- und Endzeiten). 

Beim Erstellen eines [StreamingLocator](/rest/api/media/streaminglocators) müssen Sie den gewünschten **StreamingPolicyName** angeben. In diesem Beispiel streamen Sie klare (oder unverschlüsselte) Inhalte, daher wird die vordefinierte Richtlinie für unverschlüsseltes Streaming (**PredefinedStreamingPolicy.ClearStreamingOnly**) verwendet.

> [!IMPORTANT]
> Wenn Sie eine benutzerdefinierte [Streamingrichtlinie](/rest/api/media/streamingpolicies) verwenden, sollten Sie eine begrenzte Sammlung solcher Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre Streaminglocators wiederverwenden, wenn dieselben Verschlüsselungsoptionen und Protokolle benötigt werden. Ihr Media Services-Konto weist ein Kontingent für die Anzahl von Streamingrichtlinieneinträgen auf. Sie sollten nicht für jeden Streaminglocator eine neue Streamingrichtlinie erstellen.

Im folgende Code wird davon ausgegangen, dass Sie die Funktion mit einem eindeutigen locatorName aufrufen.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Das Beispiel in diesem Thema beschäftigt sich mit Streaming. Sie können den gleichen Aufruf jedoch auch verwenden, um einen Streaminglocator für die Übermittlung von Video über progressiven Download zu erstellen.

### <a name="get-streaming-urls"></a>Abrufen von Streaming-URLs

Nachdem der **Streaminglocator** erstellt wurde, können Sie die Streaming-URLs wie unter [GetStreamingURLs](/rest/api/media/streaminglocators) gezeigt abrufen. Zum Erstellen einer URL müssen Sie den Hostnamen des [Streamingendpunkts](/rest/api/media/streamingendpoints) und den Pfad des **Streaminglocators** miteinander verketten. In diesem Beispiel wird der *standardmäßige* **Streamingendpunkt** verwendet. Beim ersten Erstellen eines Media Services-Kontos weist dieser *standardmäßige* **Streamingendpunkt** den Status „Beendet“ auf. Sie müssen daher **Start** aufrufen.

> [!NOTE]
> In dieser Methode benötigen Sie den LocatorName, der beim Erstellen des **Streaminglocators** für das Ausgabeobjekt verwendet wurde.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Testen mit Azure Media Player

Um den Stream zu testen, wird in diesem Artikel Azure Media Player verwendet. 

> [!NOTE]
> Wenn ein Player auf einer HTTPS-Website gehostet wird, stellen Sie sicher, die URL mit „https“ zu aktualisieren.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Fügen Sie im Feld **URL:** einen der URL-Streamingwerte ein, die Sie von Ihrer Anwendung erhalten haben. 
 
     Sie können die URL im HLS-, Dash- oder Smooth-Format einfügen. Der Azure Media Player wechselt auf Ihrem Gerät automatisch zu einem geeigneten Streamingprotokoll für die Wiedergabe.
3. Drücken Sie **Player aktualisieren**.

Azure Media Player kann zum Testen verwendet werden, sollte jedoch nicht in einer Produktionsumgebung zum Einsatz kommen. 

## <a name="next-steps"></a>Nächste Schritte

[Analysieren von Videos](analyze-videos-tutorial-with-api.md)
