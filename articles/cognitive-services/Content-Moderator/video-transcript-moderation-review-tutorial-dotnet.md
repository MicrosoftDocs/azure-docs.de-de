---
title: 'Tutorial: Moderieren von Videos und Transkripts in .NET – Content Moderator'
titlesuffix: Azure Cognitive Services
description: In diesem ausführlichen Tutorial erfahren Sie, wie Sie eine vollständige Lösung für die Video- und Transkriptmoderation mit computergestützter Moderation und Erstellung von Überprüfungen mit menschlicher Beteiligung entwickeln.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 4a28e9f1f911e274f9a53275afa577024405b336
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474683"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Tutorial: Video- und Transkriptmoderation

In diesem Tutorial erfahren Sie, wie Sie eine vollständige Lösung für die Video- und Transkriptmoderation mit computergestützter Moderation und Erstellung von Überprüfungen mit menschlicher Beteiligung entwickeln.

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> - Komprimieren der Eingabevideos zur schnelleren Verarbeitung
> - Moderieren des Videos, um Shots und Frames mit Erkenntnissen zu erhalten
> - Verwenden der Framezeitstempel zum Erstellen von Miniaturansichten (Bilder)
> - Übermitteln von Zeitstempeln und Miniaturansichten zum Erstellen von Videoüberprüfungen
> - Konvertieren der Videosprache in Text (Transkript) mit der Media Indexer-API
> - Moderieren des Transkripts mit dem Dienst für die Textmoderation
> - Hinzufügen des moderierten Transkripts zur Videoüberprüfung

## <a name="prerequisites"></a>Voraussetzungen

- Registrieren Sie sich für das [Content Moderator-Prüfungstool](https://contentmoderator.cognitive.microsoft.com/), und erstellen Sie benutzerdefinierte Tags. Weitere Informationen zu diesem Schritt finden Sie bei Bedarf unter [Erstellen und Verwenden von Moderationstags](Review-Tool-User-Guide/tags.md).

    ![Screenshot: Benutzerdefinierte Tags für die Videomoderation](images/video-tutorial-custom-tags.png)
- Um die Beispielanwendung ausführen zu können, benötigen Sie ein Azure-Konto, eine Azure Media Services-Ressource, eine Azure Content Moderator-Ressource sowie Azure Active Directory-Anmeldeinformationen. Wie Sie diese Komponenten erhalten, erfahren Sie im [Leitfaden für die Videomoderations-API](video-moderation-api.md).
- Laden Sie die App [VideoReviewConsoleApp](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) aus GitHub herunter.

## <a name="enter-credentials"></a>Eingeben von Anmeldeinformationen

Bearbeiten Sie die Datei `App.config`, und fügen Sie den Active Directory-Mandantennamen, Dienstendpunkte und Abonnementschlüssel (mit `#####` gekennzeichnet) hinzu. Sie benötigen die folgenden Informationen:

|Schlüssel|BESCHREIBUNG|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Endpunkt für die AMS-API (Azure Media Services)|
|`ClientSecret`|Abonnementschlüssel für Azure Media Services|
|`ClientId`|Client-ID für Azure Media Services|
|`AzureAdTenantName`|Active Directory-Mandantenname für Ihre Organisation|
|`ContentModeratorReviewApiSubscriptionKey`|Abonnementschlüssel für die Content Moderator-Überprüfungs-API|
|`ContentModeratorApiEndpoint`|Endpunkt für die Content Moderator-API|
|`ContentModeratorTeamId`|Content Moderator-Team-ID|

## <a name="examine-the-main-code"></a>Untersuchen des Hauptcodes

Die `Program`-Klasse in `Program.cs` ist der Haupteinstiegspunkt in die Anwendung für die Videomoderation.

### <a name="methods-of-program-class"></a>Methoden der Program-Klasse

|Methode|BESCHREIBUNG|
|-|-|
|`Main`|Dient zum Analysieren die Befehlszeile, Erfassen der Benutzereingabe und Starten der Verarbeitung.|
|`ProcessVideo`|Dient zum Komprimieren, Hochladen, Moderieren und Erstellen von Videoüberprüfungen.|
|`CreateVideoStreamingRequest`|Dient zum Erstellen eines Datenstroms zum Hochladen eines Videos.|
|`GetUserInputs`|Dient zum Erfassen der Benutzereingabe. Wird verwendet, wenn keine Befehlszeilenoptionen vorhanden sind.|
|`Initialize`|Dient zum Initialisieren der Objekte, die für den Moderationsprozess erforderlich sind.|

### <a name="the-main-method"></a>Main-Methode

Mit `Main()` beginnt die Ausführung. Dies ist also der Ausgangspunkt für das Verständnis des Prozesses für die Videomoderation.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()` verarbeitet die folgenden Befehlszeilenargumente:

- Den Pfad zu einem Verzeichnis mit MPEG-4-Videodateien, die für die Moderation übermittelt werden sollen. Alle `*.mp4`-Dateien in diesem Verzeichnis und den Unterverzeichnissen werden für die Moderation übermittelt.
- Optional ein boolesches Flag (true/false), um anzugeben, ob Texttranskripts zum Moderieren von Audiodaten generiert werden sollten.

Wenn keine Befehlszeilenargumente vorhanden sind, ruft `Main()` die `GetUserInputs()`-Methode auf. Mit dieser Methode wird der Benutzer aufgefordert, den Pfad zu einer einzelnen Videodatei einzugeben und anzugeben, ob ein Texttranskript generiert werden soll.

> [!NOTE]
> Die Konsolenanwendung nutzt die [Azure Media Indexer-API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2), um aus dem Audiotitel des hochgeladenen Videos Transkripts zu generieren. Die Ergebnisse werden im WebVTT-Format bereitgestellt. Weitere Informationen zu diesem Format finden Sie unter [Web Video Text Tracks Format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) (Webvideo-Texttitelformat).

### <a name="initialize-and-processvideo-methods"></a>Initialize- und ProcessVideo-Methode

Unabhängig davon, ob die Optionen des Programms von der Befehlszeile oder aus der interaktiven Benutzereingabe stammen, ruft `Main()` als Nächstes `Initialize()` auf, um die folgenden Instanzen zu erstellen:

|Klasse|BESCHREIBUNG|
|-|-|
|`AMSComponent`|Dient zum Komprimieren von Videodateien, bevor diese zur Moderation übermittelt werden.|
|`AMSconfigurations`|Dient als Schnittstelle zu den Konfigurationsdaten der Anwendung (in `App.config`).|
|`VideoModerator`| Dient zum Hochladen, Codieren, Verschlüsseln und Moderieren per AMS-SDK.|
|`VideoReviewApi`|Dient zum Verwalten von Videoüberprüfungen im Content Moderator-Dienst.|

Auf diese Klassen wird in den folgenden Abschnitten dieses Tutorials näher eingegangen (mit Ausnahme von `AMSConfigurations`, da diese Klasse unkompliziert ist).

Abschließend werden die Videodateien einzeln nacheinander verarbeitet, indem jeweils `ProcessVideo()` aufgerufen wird.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

Die `ProcessVideo()`-Methode ist relativ einfach. Sie führt die folgenden Vorgänge in dieser Reihenfolge durch:

- Komprimieren des Videos
- Hochladen des Videos in ein Azure Media Services-Asset
- Erstellen eines AMS-Auftrags zum Moderieren des Videos
- Erstellen einer Videoüberprüfung in Content Moderator

In den folgenden Abschnitten werden einige individuelle Prozesse, die von `ProcessVideo()` aufgerufen werden, ausführlicher behandelt. 

## <a name="compress-the-video"></a>Komprimieren des Videos

Zur Reduzierung des Netzwerkdatenverkehrs konvertiert die Anwendung Videodateien in das Format „H.264 (MPEG-4 AVC)“ und skaliert sie auf eine maximale Breite von 640 Pixel. Die Verwendung des H.264-Codecs wird aufgrund seiner hohen Effizienz (Komprimierungsrate) empfohlen. Die Komprimierung wird mit dem kostenlosen Befehlszeilentool `ffmpeg` durchgeführt, das im Ordner `Lib` der Visual Studio-Lösung enthalten ist. Die Eingabedateien können ein beliebiges Format haben, das von `ffmpeg` unterstützt wird, z.B. die gängigsten Videodateiformate und -codecs.

> [!NOTE]
> Wenn Sie das Programm mit Befehlszeilenoptionen starten, geben Sie ein Verzeichnis mit den Videodateien an, die für die Moderation übermittelt werden sollen. Alle Dateien in diesem Verzeichnis, die über die Dateinamenerweiterung `.mp4` verfügen, werden verarbeitet. Aktualisieren Sie zum Verarbeiten von anderen Dateinamenerweiterungen die `Main()`-Methode in der Datei `Program.cs`, damit sie die gewünschten Erweiterungen enthält.

Der Code, mit dem eine einzelne Videodatei komprimiert wird, ist die `AmsComponent`-Klasse in `AMSComponent.cs`. Die für diese Funktionalität zuständige Methode ist `CompressVideo()` (hier dargestellt).

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

Mit diesem Code werden die folgenden Schritte ausgeführt:

- Es wird überprüft, ob die Konfiguration in `App.config` alle erforderlichen Daten enthält.
- Es wird überprüft, ob die `ffmpeg`-Binärdaten vorhanden sind.
- Der Name der Ausgabedatei wird erstellt, indem `_c.mp4` an den Basisnamen der Datei angefügt wird (z.B. `Example.mp4` -> `Example_c.mp4`).
- Erstellt eine Befehlszeilen-Zeichenfolge zum Durchführen der Konvertierung.
- Startet einen `ffmpeg`-Prozess über die Befehlszeile.
- Es wird gewartet, bis das Video verarbeitet wurde.

> [!NOTE]
> Wenn Sie wissen, dass Ihre Videos bereits per H.264 komprimiert wurden und über die richtigen Dimensionen verfügen, können Sie `CompressVideo()` umschreiben, um die Komprimierung zu überspringen.

Die Methode gibt den Dateinamen der komprimierten Ausgabedatei zurück.

## <a name="upload-and-moderate-the-video"></a>Hochladen und Moderieren des Videos

Das Video muss in Azure Media Services gespeichert werden, bevor es vom Content Moderation-Dienst verarbeitet werden kann. Die `Program`-Klasse in `Program.cs` verfügt über eine kleine `CreateVideoStreamingRequest()`-Methode, mit der ein Objekt zurückgegeben wird, das für die Streaminganforderung zum Hochladen des Videos steht.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

Das sich ergebende `UploadVideoStreamRequest`-Objekt ist unter `UploadVideoStreamRequest.cs` (und dem übergeordneten `UploadVideoRequest`-Element in `UploadVideoRequest.cs`) definiert. Diese Klassen werden hier nicht angezeigt. Sie sind kurz und haben nur den Zweck, die komprimierten Videodaten und die zugehörigen Informationen zu enthalten. Eine andere Nur-Daten-Klasse, nämlich `UploadAssetResult` (`UploadAssetResult.cs`), wird für die Ergebnisse des Uploadprozesses verwendet. Jetzt sind diese Zeilen in `ProcessVideo()` verständlich:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Mit diesen Zeilen werden die folgenden Aufgaben durchgeführt:

- Erstellen eines `UploadVideoStreamRequest`-Elements zum Hochladen der komprimierten Videodaten
- Festlegen des `GenerateVTT`-Flags der Anforderung, wenn der Benutzer ein Texttranskript angefordert hat
- Aufrufen von `CreateAzureMediaServicesJobToModerateVideo()` zum Durchführen des Uploads und Empfangen des Ergebnisses

## <a name="examine-video-moderation-code"></a>Untersuchen des Videomoderationscodes

Die `CreateAzureMediaServicesJobToModerateVideo()`-Methode befindet sich in der Datei `VideoModerator.cs`, die den Großteil des Codes für die Interaktion mit Azure Media Services enthält. Der Quellcode der Methode ist im folgenden Auszug dargestellt.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Mit diesem Code werden die folgenden Aufgaben durchgeführt:

- Erstellen eines AMS-Auftrags für die erforderlichen Verarbeitungsschritte
- Hinzufügen von Aufgaben zum Codieren und Moderieren der Videodatei und Generieren eines Texttranskripts
- Übermitteln des Auftrags, Hochladen der Datei und Starten der Verarbeitung
- Abrufen der Moderationsergebnisse, des Texttranskripts (falls angefordert) und anderer Informationen

## <a name="sample-video-moderation-output"></a>Beispielausgabe der Videomoderation

Das Ergebnis des Auftrags für die Videomoderation (siehe [Schnellstart zur Videomoderation](video-moderation-api.md)) ist eine JSON-Datenstruktur mit den Moderationsergebnissen. Diese Ergebnisse umfassen eine Aufschlüsselung der Fragmente (Shots) des Videos, die jeweils Ereignisse (Clips) mit Keyframes enthalten, die zur Überprüfung gekennzeichnet wurden. Jeder Keyframe wird anhand der Wahrscheinlichkeit bewertet, dass er nur für Erwachsene geeignete bzw. anzügliche Inhalte aufweist. Im folgenden Beispiel ist eine JSON-Antwort dargestellt:

```json
{
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
        "start": 0,
        "duration": 18000
    },
    {
        "start": 18000,
        "duration": 3600,
        "interval": 3600,
        "events": [
        [
        {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
        }
        ]
    ]
    },
    {
        "start": 18386372,
        "duration": 119149,
        "interval": 119149,
        "events": [
        [
        {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
        }
    ]
    ]
    }
]
}
```

Eine Transkription der Audiodaten aus dem Video wird auch produziert, wenn das `GenerateVTT`-Flag festgelegt wird.

> [!NOTE]
> Die Konsolenanwendung nutzt die [Azure Media Indexer-API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2), um aus dem Audiotitel des hochgeladenen Videos Transkripts zu generieren. Die Ergebnisse werden im WebVTT-Format bereitgestellt. Weitere Informationen zu diesem Format finden Sie unter [Web Video Text Tracks Format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) (Webvideo-Texttitelformat).

## <a name="create-a-the-human-in-the-loop-review"></a>Erstellen einer Überprüfung mit menschlicher Beteiligung

Der Moderationsprozess gibt eine Liste mit Keyframes aus dem Video zurück – zusammen mit einem Transkript der Audiotitel. Der nächste Schritt ist das Erstellen einer Überprüfung im Content Moderator-Prüfungstool für menschliche Moderatoren. Wenn Sie zurück zur `ProcessVideo()`-Methode in `Program.cs` navigieren, sehen Sie den Aufruf der `CreateVideoReviewInContentModerator()`-Methode. Diese Methode befindet sich in der `videoReviewApi`-Klasse, die in `VideoReviewAPI.cs` enthalten ist. Sie ist hier dargestellt.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

Mit `CreateVideoReviewInContentModerator()` werden mehrere andere Methoden aufgerufen, um die folgenden Aufgaben durchzuführen:

> [!NOTE]
> Die Konsolenanwendung nutzt die [FFmpeg](https://ffmpeg.org/)-Bibliothek zum Generieren von Miniaturansichten. Die Miniaturansichten (Bilder) entsprechen den Framezeitstempeln in der Ausgabe der Videomoderation.

|Aufgabe|Methoden|Datei|
|-|-|-|
|Extrahieren der Keyframes aus dem Video und Erstellen von entsprechenden Miniaturansichten|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Scannen des Texttranskripts, falls verfügbar, zum Ermitteln von Audiodaten, die nur für Erwachsene geeignet bzw. anzüglich sind|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Vorbereiten und Übermitteln einer Videoüberprüfungsanforderung zur Prüfung durch einen Menschen|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

Im folgenden Screenshot sind die Ergebnisse der obigen Schritte dargestellt.

![Videoüberprüfung – Standardansicht](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Verarbeiten des Transkripts

Bisher ging es im Code dieses Tutorials vor allem um den visuellen Inhalt. Die Überprüfung des Sprachinhalts ist ein separater und optionaler Prozess, für den wie erwähnt ein aus den Audiodaten generiertes Transkript verwendet wird. Jetzt ist der Zeitpunkt gekommen, einen Blick darauf zu werfen, wie Texttranskripte erstellt und im Überprüfungsprozess verwendet werden. Die Aufgabe zur Generierung des Transkripts obliegt dem [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content)-Dienst.

Die Anwendung führt die folgenden Aufgaben durch:

|Aufgabe|Methoden|Datei|
|-|-|-|
|Ermitteln, ob Texttranskripte generiert werden sollen|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Wenn ja: Übermitteln eines Transkriptionsauftrags im Rahmen der Moderation|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Abrufen einer lokalen Kopie des Transkripts|`GenerateTranscript()`|`VideoModerator.cs`|
|Kennzeichnen der Frames des Videos, die ungeeignete Audiodaten enthalten|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Hinzufügen der Ergebnisse zur Überprüfung|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Aufgabenkonfiguration

Wir beginnen gleich mit der Übermittlung des Transkriptionsauftrags. Mit `CreateAzureMediaServicesJobToModerateVideo()` (bereits beschrieben) wird `ConfigureTranscriptTask()` aufgerufen.

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

Die Konfiguration für die Transkriptaufgabe wird aus der Datei `MediaIndexerConfig.json` im Ordner `Lib` der Lösung gelesen. AMS-Medienobjekte werden für die Konfigurationsdatei und für die Ausgabe des Transkriptionsprozesses erstellt. Bei Ausführung des AMS-Auftrags wird mit dieser Aufgabe ein Texttranskript aus dem Audiotitel der Videodatei erstellt.

> [!NOTE]
> Die Beispielanwendung erkennt nur Sprache auf Englisch (USA).

### <a name="transcript-generation"></a>Transkriptgenerierung

Das Transkript wird als AMS-Medienobjekt veröffentlicht. Zum Scannen des Transkripts auf unzulässigen Inhalt lädt die Anwendung das Medienobjekt aus Azure Media Services herunter. `CreateAzureMediaServicesJobToModerateVideo()` ruft `GenerateTranscript()` auf (hier gezeigt), um die Datei abzurufen.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Nach dem erforderlichen AMS-Setup wird der eigentliche Download durchgeführt, indem die generische Funktion `DownloadAssetToLocal()` aufgerufen wird, mit der ein AMS-Medienobjekt in eine lokale Datei kopiert wird.

## <a name="moderate-the-transcript"></a>Moderieren des Transkripts

Das Transkript ist nun verfügbar und wird gescannt und für die Überprüfung verwendet. Für die Erstellung der Überprüfung ist das `CreateVideoReviewInContentModerator()`-Element zuständig, mit dem `GenerateTextScreenProfanity()` für die Durchführung des Auftrags aufgerufen wird. Mit dieser Methode wird wiederum das `TextScreen()`-Element aufgerufen, das den Großteil der Funktionalität enthält.

`TextScreen()` führt die folgenden Aufgaben durch:

- Analysieren des Transkripts auf Zeitstempel und Untertitel
- Übermitteln der einzelnen Untertitel für die Textmoderation
- Kennzeichnen aller Frames, die ggf. unzulässigen Sprachinhalt aufweisen

Wir sehen uns diese Aufgaben nun genauer an:

### <a name="initialize-the-code"></a>Initialisieren des Codes

Initialisieren Sie zuerst alle Variablen und Sammlungen.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Analysieren des Transkripts auf Untertitel

Analysieren Sie als Nächstes das Transkript im VTT-Format auf Untertitel und Zeitstempel. Im Prüfungstool werden diese Untertitel im Fenster für die Videoüberprüfung auf der Registerkarte „Transkript“ angezeigt. Die Zeitstempel werden verwendet, um die Untertitel mit den entsprechenden Videoframes zu synchronisieren.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderieren von Untertiteln mit dem Dienst für die Textmoderation

Als Nächstes untersuchen wir die analysierten Textuntertitel mit der Text-API von Content Moderator.

> [!NOTE]
> Für Ihren Content Moderator-Dienstschlüssel gilt ein Ratenlimit vom Typ „Anforderungen pro Sekunde“ (Requests Per Second, RPS). Wenn Sie das Limit überschreiten, löst das SDK eine Ausnahme mit dem Fehlercode 429 aus.
>
> Bei einem Schlüssel des Free-Tarifs ist die Anforderungsrate auf eine einzelne Anforderung pro Sekunde beschränkt.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Aufschlüsselung der Textmoderation

Da `TextScreen()` eine etwas umfangreichere Methode ist, schlüsseln wir sie hier auf.

1. Zuerst liest die Methode die Transkriptdatei Zeile für Zeile. Leere Zeilen und Zeilen mit einer Anmerkung (`NOTE`) und zugehöriger Zuverlässigkeitsbewertung werden ignoriert. Die Zeitstempel und Textelemente werden aus den Hinweisen (*Cues*) in der Datei extrahiert. Ein „Cue“ steht für Text aus dem Audiotitel und enthält die Start- und Endzeit. Der „Cue“ beginnt mit der Zeitstempelzeile mit der Zeichenfolge `-->`. Es folgen eine oder mehrere Textzeilen.

1. Instanzen von `CaptionScreentextResult` (in `TranscriptProfanity.cs` definiert) enthalten die Informationen, die für die einzelnen „Cues“ analysiert werden.  Wenn eine neue Zeitstempelzeile erkannt oder eine maximale Textlänge von 1.024 Zeichen erreicht wird, wird der `csrList` ein neues `CaptionScreentextResult`-Element hinzugefügt. 

1. Mit der nächsten Methode werden die einzelnen „Cues“ an die Textmoderations-API übermittelt. Sie ruft die Elemente `ContentModeratorClient.TextModeration.DetectLanguageAsync()` und `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()` auf, die in der `Microsoft.Azure.CognitiveServices.ContentModerator`-Assembly definiert sind. Um nicht unter das Ratenlimit zu fallen, wartet die Methode eine Sekunde lang, bevor die „Cues“ übermittelt werden.

1. Nachdem die Ergebnisse des Diensts für die Textmoderation empfangen wurden, werden sie von der Methode analysiert, um zu ermitteln, ob sie die Schwellenwerte für die Zuverlässigkeit erfüllen. Diese Werte liegen in `App.config` unter `OffensiveTextThreshold`, `RacyTextThreshold` und `AdultTextThreshold` vor. Abschließend werden auch die unzulässigen Begriffe gespeichert. Alle Frames innerhalb des Zeitbereichs eines „Cues“ werden gekennzeichnet, um anzugeben, dass sie anstößigen, anzüglichen bzw. nur für Erwachsene geeigneten Text enthalten.

1. `TextScreen()` gibt eine `TranscriptScreenTextResult`-Instanz zurück, die das Ergebnis der Textmoderation für das gesamte Video enthält. Dieses Objekt enthält Flags und Bewertungen für die unterschiedlichen Arten von unzulässigem Inhalt sowie eine Liste mit allen unzulässigen Begriffen. Der Aufrufer `CreateVideoReviewInContentModerator()` ruft `UploadScreenTextResult()` auf, um diese Informationen an die Überprüfung anzufügen, damit sie für menschliche Prüfer verfügbar sind.

Der folgende Screenshot enthält das Ergebnis der Schritte für die Transkriptgenerierung und der Moderation.

![Videomoderation – Transkriptansicht](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Programmausgabe

In der folgenden Befehlszeilenausgabe des Programms sind die unterschiedlichen Aufgaben und ihre Durchführung zu sehen. Darüber hinaus sind das Moderationsergebnis (im JSON-Format) und das Sprachtranskript in demselben Verzeichnis wie die ursprünglichen Videodateien verfügbar.

```console
Microsoft.ContentModerator.AMSComponentClient
Enter the fully qualified local path for Uploading the video :
"Your File Name.MP4"
Generate Video Transcript? [y/n] : y

Video compression process started...
Video compression process completed...

Video moderation process started...
Video moderation process completed...

Video review process started...
Video Frames Creation inprogress...
Frames(83) created successfully.
Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
Video review successfully completed...

Total Elapsed Time: 00:05:56.8420355
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Anwendung eingerichtet, die Videoinhalte (einschließlich Transkriptinhalte) moderiert und im Prüfungstool Überprüfungen erstellt. Machen Sie sich als Nächstes mit den Details der Videomoderation vertraut.

> [!div class="nextstepaction"]
> [Videomoderation](./video-moderation-human-review.md)
