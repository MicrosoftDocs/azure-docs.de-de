---
title: Indizieren von Mediendateien mit Azure Media Indexer
description: Mit dem Azure Media Indexer können Sie die Inhalte Ihrer Mediendateien durchsuchbar machen und eine Volltext-Aufzeichnung für Untertitel und Schlüsselwörter generieren. In diesem Thema wird die Verwendung von Media Indexer erläutert.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.reviewer: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 12a4099a6a3f0d06dfbb40171970c7386c3e4d22
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518839"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indizieren von Mediendateien mit Azure Media Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Kunden wird empfohlen, von Indexer v1 und Indexer v2 auf die Verwendung des [Media Services v3 AudioAnalyzerPreset Basic-Modus](../latest/analyzing-video-audio-files-concept.md) umsteigen. Die Medienprozessoren [Azure Media Indexer](media-services-index-content.md) und [Azure Media Indexer 2 Preview](./legacy-components.md) Medienprozessoren werden außer Betrieb genommen. Die Daten zur Einstellung finden Sie unter dem Thema [Legacykomponenten](legacy-components.md).

Mit dem Azure Media Indexer können Sie die Inhalte Ihrer Mediendateien durchsuchbar machen und eine Volltext-Aufzeichnung für Untertitel und Schlüsselwörter generieren. Sie können eine einzelne Mediendatei oder mehrere Mediendateien in einem Batch verarbeiten.  

Stellen Sie beim Indizieren von Inhalten sicher, dass Mediendateien verwendet werden, die klare Sprache enthalten (ohne Hintergrundmusik, Lärm, Effekte oder Mikrofonrauschen). Die folgenden Beispiele sind geeignete Inhalte: aufgezeichnete Besprechungen, Vorträge oder Präsentationen. Folgende Inhalte sind für die Indizierung ggf. nicht geeignet: Filme, Fernsehsendungen, Material mit gemischten Audio- und Soundeffekten, schlecht aufgezeichnete Inhalte mit Hintergrundgeräuschen (Rauschen).

Ein Indizierungsauftrag kann die folgenden Ausgaben generieren:

* Untertiteldateien in den folgenden Formaten: **TTML** und **WebVTT**.
  
    Eine Untertiteldatei enthält ein Tag namens „Recognizability“, das einen Indizierungsauftrag basierend darauf bewertet, wie gut die Sprache im Quellvideo erkennbar ist.  Sie können den Wert von Recognizability nutzen, um Dateien auf ihre Verwendbarkeit zu prüfen. Eine niedrige Bewertung steht für schlechte Indizierungsergebnisse aufgrund der Audioqualität.
* Schlüsselwortdatei (XML).

In diesem Artikel wird das Erstellen von Indizierungsaufträgen zum **Indizieren eines Medienobjekts** und zum **Indizieren mehrerer Dateien** beschrieben.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Verwenden von Konfigurations- und Manifestdateien für Indizierungsaufgaben
Sie können weitere Details für Ihre Indizierungsaufgaben mithilfe einer Aufgabenkonfiguration angeben. Beispielsweise können Sie angeben, welche Metadaten für Ihre Mediendatei verwendet werden sollen. Diese Metadaten werden durch die Sprach-Engine zum Erweitern des Vokabulars verwendet und verbessern erheblich die Spracherkennungsgenauigkeit.  Sie können zudem die gewünschten Ausgabedateien angeben.

Sie können auch mehrere Mediendateien gleichzeitig mithilfe einer Manifestdatei verarbeiten.

Weitere Informationen finden Sie unter [Aufgabenvoreinstellung für Azure Media Indexer](./legacy-components.md).

## <a name="index-an-asset"></a>Indizieren eines Medienobjekts
Mit der folgenden Methode werden eine Mediendatei als Medienobjekt hochgeladen und ein Auftrag zum Indizieren des Medienobjekts erstellt.

Die Mediendatei wird mit allen Standardeinstellungen indiziert, wenn keine Konfigurationsdatei angegeben ist.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a name="output-files"></a><a id="output_files"></a>Ausgabedateien
Ein Indizierungsauftrag generiert standardmäßig die folgenden Ausgabedateien. Die Dateien werden im ersten Ausgabemedienobjekt gespeichert.

Wenn mehrere Eingabemediendateien vorhanden sind, generiert Indexer die Manifestdatei „JobResult.txt“ für die Auftragsausgaben. Die sich daraus ergebenden TTML-, WebVTT- und Stichwortdateien werden für jede Eingabemediendatei fortlaufend nummeriert und mithilfe des „Alias“ benannt.

| Dateiname | BESCHREIBUNG |
| --- | --- |
| **InputFileName.ttml**<br/>**InputFileName.vtt** |Untertiteldateien (Closed Caption, CC) im TTML-, und WebVTT-Format.<br/><br/>Sie können verwendet werden, um Hörgeschädigten Audio- und Videodateien zugänglich zu machen.<br/><br/>Eine Untertiteldatei enthält ein Tag namens <b>Recognizability</b>, das einen Indizierungsauftrag basierend darauf bewertet, wie gut die Sprache im Quellvideo erkennbar ist.  Sie können den Wert von <b>Recognizability</b> nutzen, um Ausgabedateien auf ihre Verwendbarkeit zu prüfen. Eine niedrige Bewertung steht für schlechte Indizierungsergebnisse aufgrund der Audioqualität. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Stichwort- und Informationsdateien. <br/><br/>Die Stichwortdatei ist eine XML-Datei, die Stichwörter enthält, welche aus dem Sprachinhalt mit Frequenz- und Abstandsinformationen extrahiert wurden. <br/><br/>Die Informationsdatei ist eine Nur-Text-Datei und enthält detaillierte Informationen zu jedem erkannten Begriff. Die erste Zeile unterscheidet sich von den anderen Zeilen und enthält die Bewertung für „Recognizability“. Jede nachfolgende Zeile ist eine tabstoppgetrennte Auflistung der folgenden Daten: Startzeit, Endzeit, Wort/Ausdruck, Konfidenz. Die Zeiten sind in Sekunden und die Konfidenzwerte als Zahl zwischen 0 und 1 angegeben. <br/><br/>Beispielzeile: „1.20    1.45    word    0.67“ <br/><br/>Diese Dateien können für unterschiedliche Zwecke verwendet werden, beispielsweise zum Ausführen einer Sprachanalyse. Zudem können sie für Suchmaschinen wie Bing, Google oder Microsoft SharePoint zur Verfügung gestellt werden, damit die Mediendateien leichter auffindbar sind, oder auch zum Bereitstellen wirkungsvollerer Werbeanzeigen verwendet werden. |
| **JobResult.txt** |Ausgabemanifest, das nur beim Indizieren mehrerer Dateien vorhanden ist und die folgenden Informationen enthält:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Fehler</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Wenn nicht alle Eingabemediendateien erfolgreich indiziert werden, verursacht der Indizierungsauftrag einen Fehler mit dem Fehlercode 4000. Weitere Informationen finden Sie unter [Fehlercodes](#error_codes).

## <a name="index-multiple-files"></a>Indizieren mehrerer Dateien
Mit der folgenden Methode werden mehrere Mediendateien als Medienobjekt hochgeladen und ein Auftrag zum Indizieren all dieser Dateien in einem Batch erstellt.

Eine Manifestdatei mit der Erweiterung LST wird erstellt und in das Medienobjekt hochgeladen. Die Manifestdatei enthält die Liste sämtlicher Medienobjektdateien. Weitere Informationen finden Sie unter [Aufgabenvoreinstellung für Azure Media Indexer](./legacy-components.md).

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Teilweise erfolgreicher Auftrag
Wenn nicht alle Eingabemediendateien erfolgreich indiziert werden, verursacht der Indizierungsauftrag einen Fehler mit dem Fehlercode 4000. Weitere Informationen finden Sie unter [Fehlercodes](#error_codes).

Es werden die gleichen Ausgaben (wie bei erfolgreichen Aufträgen) generiert. Sie können anhand der Ausgabemanifestdatei feststellen, welche Eingabedateien gemäß den Werten in der Fehlerspalte fehlerhaft sind. Bei fehlerhaften Eingabedateien werden KEINE TTML-, WebVTT- und Stichwortdateien generiert.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a> Aufgabenvoreinstellungen für Azure Media Indexer
Die Verarbeitung von Azure Media Indexer kann angepasst werden, indem zusätzlich zur Aufgabe eine optionale Aufgabenvoreinstellung bereitgestellt wird.  Im Folgenden wird das Format dieser XML-Konfigurationsdatei beschrieben.

| Name | Anforderung | BESCHREIBUNG |
| --- | --- | --- |
| **input** |false |Medienobjekte, die Sie indizieren möchten.</p><p>Azure Media Indexer unterstützt die folgenden Mediendateiformate: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Sie können die Dateinamen im Attribut **name** oder **list** des Elements **input** angeben (wie nachfolgend gezeigt). Wenn Sie nicht angeben, welche Medienobjektdatei indiziert werden soll, wird die primäre Datei ausgewählt. Wenn keine primäre Medienobjektdatei festgelegt ist, wird die erste Datei im Eingabemedienobjekt indiziert.</p><p>So legen Sie den Namen der Medienobjektdatei explizit fest:<br/>`<input name="TestFile.wmv">`<br/><br/>Sie können auch in einem Arbeitsschritt mehrere Medienobjektdateien indizieren (bis zu 10 Dateien). Gehen Sie dazu folgendermaßen vor:<br/><br/><ol class="ordered"><li><p>Erstellen Sie eine Textdatei (Manifestdatei), und geben Sie ihr die Erweiterung .LST. </p></li><li><p>Fügen Sie dieser Manifestdatei eine Liste aller Medienobjektdateinamen im Eingabemedienobjekt hinzu. </p></li><li><p>Fügen Sie die Manifestdatei dem Medienobjekt hinzu (laden Sie sie hoch).  </p></li><li><p>Geben Sie den Namen der Manifestdatei im list-Attribut der Eingabe an.<br/>`<input list="input.lst">`</li></ol><br/><br/>Hinweis: Wenn Sie der Manifestdatei mehr als zehn Dateien hinzufügen, tritt bei der Indizierung ein Fehler auf (Fehlercode 2006). |
| **metadata** |false |Metadaten für die festgelegten Medienobjektdateien zur Vokabularanpassung.  So kann der Indexer nicht standardmäßiges Vokabular wie z.B. Eigennamen erkennen.<br/>`<metadata key="..." value="..."/>` <br/><br/>Sie können Werte ( **values** ) für vordefinierte Schlüssel ( **keys** ) angeben. Derzeit werden die folgenden Schlüssel unterstützt:<br/><br/>„title“ und „description“: Diese dienen der Vokabularanpassung zum Optimieren des Sprachmodells für Ihren Auftrag und zum Verbessern der Spracherkennungsgenauigkeit.  Über Internetsuchen finden die Werte kontextrelevante Textdokumente und nutzen deren Inhalte zur Vergrößern des internen Wörterbuchs während der Indizierungsaufgabe.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **features** <br/><br/> Wurde in Version 1.2 hinzugefügt. Das einzige unterstützte Feature ist derzeit die Spracherkennung („ASR“). |false |Die Spracherkennungsfunktion umfasst die folgenden Einstellungsschlüssel:<table><tr><th><p>Schlüssel</p></th>        <th><p>BESCHREIBUNG</p></th><th><p>Beispielwert</p></th></tr><tr><td><p>Sprache</p></td><td><p>Die natürliche Sprache, die in der Multimediadatei erkannt werden soll.</p></td><td><p>English, Spanish</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>Eine mit Semikolon getrennte Liste der gewünschten Ausgabeuntertitelformate (sofern vorhanden)</p></td><td><p>ttml;webvtt</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>True; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Ein boolesches Flag, das angibt, ob eine XML-Datei mit Schlüsselwörtern erforderlich ist oder nicht.</p></td><td><p>True; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Ein boolesches Flag, das angibt, ob vollständige Untertitel erzwungen werden sollen (unabhängig vom Grad der Zuverlässigkeit).  </p><p>Der Standardwert lautet „false“. Bei dieser Einstellung werden Wörter und Sätze, deren Zuverlässigkeit unter 50 % liegt, in den final ausgegebenen Untertiteln durch Auslassungspunkte („...“) ersetzt.  Die Auslassungspunkte eignen sich für die Qualitätskontrolle der Untertitel und für die Überwachung.</p></td><td><p>True; False. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Fehlercodes
Wenn ein Fehler auftritt, gibt Azure Media Indexer einen der folgenden Fehlercodes aus:

| Code | Name | Mögliche Ursachen |
| --- | --- | --- |
| 2000 |Ungültige Konfiguration |Ungültige Konfiguration |
| 2001 |Ungültige Eingabemedienobjekte |Fehlende Eingabemedienobjekte oder leeres Medienobjekt |
| 2002 |Ungültiges Manifest |Manifest ist leer oder enthält ungültige Elemente |
| 2003 |Fehler beim Herunterladen der Mediendatei |Ungültige URL in Manifestdatei |
| 2004 |Nicht unterstütztes Protokoll |Protokoll der Medien-URL wird nicht unterstützt |
| 2005 |Nicht unterstützter Dateityp |Typ der Eingabemediendatei wird nicht unterstützt |
| 2006 |Zu viele Eingabedateien |Es sind mehr als 10 Dateien im Eingabemanifest vorhanden |
| 3000 |Fehler beim Decodieren der Mediendatei |Nicht unterstützter Mediencodec <br/>oder<br/> Beschädigte Mediendatei <br/>oder<br/> Kein Audiostream in den Eingabemedien |
| 4000 |Batchindizierung teilweise erfolgreich |Bei einigen Eingabemediendateien ist beim Indizieren ein Fehler aufgetreten Weitere Informationen finden Sie unter <a href="#output_files">Ausgabedateien</a>. |
| Sonstige |Interne Fehler |Wenden Sie sich an das Supportteam. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Unterstützte Sprachen
Derzeit werden die Sprachen Englisch und Spanisch unterstützt.  

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwandte Links
[Azure Media Services Analytics – Übersicht](./legacy-components.md)

[Indizieren von Mediendateien mit der Vorschauversion von Azure Media Indexer 2](./legacy-components.md)