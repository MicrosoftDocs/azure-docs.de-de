---
title: Analysieren von Video- und Audiodateien mit Azure Media Services | Microsoft-Dokumentation
description: Wenn Sie Azure Media Services verwenden, können Sie Ihre Audio- und Videoinhalte mit AudioAnalyzerPreset und VideoAnalyzerPreset analysieren.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 9154e5d58a36bde1827d63d11d57a77b4289a781
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64689372"
---
# <a name="analyzing-video-and-audio-files"></a>Analysieren von Video- und Audiodateien

Mit Azure Media Services (AMS) v3 können Sie Inhalte aus Video- und Audiodateien mit Video Indexer über die Voreinstellungen des AMS v3-Analysetools extrahieren. Die genaue Vorgehensweise beschreibt dieser Artikel. Wenn Sie detailliertere Erkenntnisse möchten, verwenden Sie Video Indexer direkt. Was für die Verwendung von Video Indexer anstelle von Voreinstellungen des Media Services-Analysetools spricht, erfahren Sie im [Vergleichsdokument](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Um Ihren Inhalt mit Media Services v3 zu analysieren, erstellen Sie eine **Transformation**, und senden Sie einen **Auftrag**, der eine der folgenden Voreinstellungen verwendet: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) oder **AudioAnalyzerPreset**. Im folgenden Artikel wird die Verwendung von **VideoAnalyzerPreset** veranschaulicht: [Tutorial: Analysieren von Videos mit Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Wenn Sie Voreinstellungen eines Video- oder Audioanalysetools verwenden, legen Sie über das Azure-Portal für Ihr Konto zehn reservierte Einheiten für Medien (S3) fest. Weitere Informationen finden Sie unter [Skalieren der Medienverarbeitung](media-reserved-units-cli-how-to.md).

## <a name="built-in-presets"></a>Integrierte Voreinstellungen

Media Services unterstützt derzeit die folgenden integrierten Analysevoreinstellungen:  

|**Name der Voreinstellung**|**Szenario**|**Details**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analysieren von Audioinhalten|Die Voreinstellung wendet einen vordefinierten Satz von AI-basierten Analysevorgängen, einschließlich Sprachtranskription, an. Die Voreinstellung unterstützt derzeit die Verarbeitung von Inhalten mit einer einzelnen, einsprachigen Audiospur. Sie können die Sprache für die Audionutzlast in der Eingabe angeben. Verwenden Sie dazu das BCP-47-Format des Tags zur Identifizierung von Sprache und Region. Folgende Sprachen werden unterstützt: Englisch („en-US“ und „en-GB“), Spanisch („es-ES“ und „es-MX“), Französisch („fr-FR“), Italienisch („it-IT“), Japanisch („ja-JP“), Portugiesisch („pt-BR“), Chinesisch („zh-CN“), Deutsch („de-DE“), Arabisch („ar-EG“), Russisch („ru-RU“), Hindi („hi-IN“) und Koreanisch („ko-KR“).<br/><br/> Wenn die Sprache nicht angegeben oder auf NULL festgelegt ist, wählt die automatische Spracherkennung die erste erkannte Sprache aus und führt die Verarbeitung für die ausgewählte Sprache für die Länge der Datei durch. Die automatische Spracherkennung unterstützt derzeit Englisch, Chinesisch, Französisch, Deutsch, Italienisch, Japanisch, Spanisch, Russisch und Portugiesisch. Sie unterstützt derzeit keine dynamische Umschaltung zwischen Sprachen, nachdem die erste Sprache erkannt wurde. Die automatische Spracherkennung funktioniert am besten mit Audioaufnahmen mit deutlicher Sprache. Kann die Sprache nicht automatisch erkannt werden, wird standardmäßig Englisch verwendet.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analysieren von Audio und Video|Extrahiert Erkenntnisse (umfangreiche Metadaten) von Audio- und Videoinhalten und gibt eine Datei im JSON-Format aus. Beim Verarbeiten einer Videodatei können Sie angeben, ob Sie nur Audioerkenntnisse erhalten möchten. Weitere Informationen finden Sie unter [Analysieren von Videos](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Erkennen aller im Video vorhandenen Gesichter|Beschreibt die Einstellungen, die bei der Analyse eines Videos verwendet werden, um alle vorhandenen Gesichter zu erkennen.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

Die Voreinstellung ermöglicht Ihnen, mehrere Audioinformationen aus einer Audio- oder Videodatei zu extrahieren. Die Ausgabe enthält eine JSON-Datei (mit allen Informationen) und eine VTT-Datei für die Audiotranskription. Diese Voreinstellung akzeptiert eine Eigenschaft, die die Sprache der Eingabedatei in Form einer [BCP47](https://tools.ietf.org/html/bcp47)-Zeichenfolge angibt. Die Audioinformationen umfassen Folgendes:

* Audiotranskription: Ein Transkript der gesprochenen Worte mit Zeitstempeln. Mehrere Sprachen werden unterstützt.
* Sprecherindizierung: Eine Zuordnung der Sprecher und der entsprechenden gesprochenen Worte
* Standpunktanalyse der Sprache: Die Ausgabe der auf die Audiotranskription angewendeten Standpunktanalyse.
* Stichwörter: Aus der Audiotranskription extrahierte Schlüsselbegriffe.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Die Voreinstellung ermöglicht Ihnen, mehrere Audio- und Videoinformationen aus einer Videodatei zu extrahieren. Die Ausgabe enthält eine JSON-Datei (mit allen Informationen), eine VTT-Datei für die Videotranskription und eine Sammlung von Miniaturbildern. Diese Voreinstellung akzeptiert auch eine [BCP47](https://tools.ietf.org/html/bcp47)-Zeichenfolge (die die Sprache des Videos darstellt) als Eigenschaft. Die Videoinformationen umfassen alle oben genannten Audioinformationen sowie die folgenden zusätzlichen Elemente:

* Gesichtsverfolgung: Die Zeit, während der Gesichter im Video zu sehen sind. Jedes Gesicht weist eine Gesichts-ID und eine entsprechende Sammlung von Miniaturbildern auf.
* Sichtbarer Text: Der Text, der über optische Zeichenerkennung erkannt wird. Der Text ist mit einem Zeitstempel versehen und wird auch zum Extrahieren von Stichwörtern verwendet (zusätzlich zum Audiotranskript).
* Keyframes: Eine Sammlung von Keyframes, die aus dem Video extrahiert werden.
* Moderation von Videoinhalten: Die Teile der Videos, die als nicht für Jugendliche geeignet eingestuft wurden.
* Anmerkung: Das Ergebnis der Kommentierung der Videos anhand eines vordefinierten Objektmodells.

##  <a name="insightsjson-elements"></a>Elemente in „insights.json“

Die Ausgabe umfasst eine JSON-Datei (insights.json) mit allen Informationen, die im Video oder Audio gefunden wurden. Die JSON-Datei kann die folgenden Elemente enthalten:

### <a name="transcript"></a>Transkript

|NAME|BESCHREIBUNG|
|---|---|
|id|Die Zeilen-ID.|
|text|Das Transkript selbst.|
|language|Die Sprache des Transkripts. Vorgesehen zur Unterstützung von Transkripts, bei denen jede Zeile eine andere Sprache enthalten kann.|
|instances|Eine Liste der Zeitbereiche, in denen diese Zeile angezeigt wurde. Wenn die Instanz „transcript“ lautet, ist nur eine Instanz vorhanden.|

Beispiel:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|NAME|BESCHREIBUNG|
|---|---|
|id|Die OCR-Zeilen-ID.|
|text|Der OCR-Text.|
|confidence|Die Zuverlässigkeit der Erkennung.|
|language|Die OCR-Sprache.|
|instances|Eine Liste der Zeitbereiche, in denen diese OCR angezeigt wurde (die gleiche OCR kann mehrfach vorkommen).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>faces

|NAME|BESCHREIBUNG|
|---|---|
|id|Die Gesichts-ID.|
|name|Der Name des Gesichts. Möglich sind „Unknown #0“, ein identifizierter Prominenter oder eine vom Kunden trainierte Person.|
|confidence|Die Zuverlässigkeit der Gesichtsidentifikation.|
|description|Eine Beschreibung des Prominenten. |
|thumbnailId|Die ID der Miniaturansicht dieses Gesichts.|
|knownPersonId|Bei einer bekannten Person die interne ID.|
|referenceId|Bei einem Bing-Prominenten die Bing-ID.|
|referenceType|Zurzeit nur Bing.|
|title|Bei einem Prominenten der Titel (z.B. „CEO von Microsoft“).|
|imageUrl|Bei einem Prominenten die Bild-URL.|
|instances|Dies sind Vorkommen des Gesichts in einem bestimmten Zeitbereich. Jedes Vorkommen hat auch eine Miniaturbild-ID. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>shots

|NAME|BESCHREIBUNG|
|---|---|
|id|Die ID der Aufnahme.|
|keyFrames|Eine Liste mit Keyframes innerhalb der Aufnahme (jede verfügt über eine ID und eine Liste der Zeitbereiche der Vorkommen). Keyframeinstanzen weisen ein Feld „thumbnailId“ mit der keyFrame-Miniaturansicht-ID auf.|
|instances|Eine Liste der Zeitbereiche dieser Aufnahme (Aufnahmen kommen nur einmal vor).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statistics

|NAME|BESCHREIBUNG|
|---|---|
|CorrespondenceCount|Anzahl von Korrespondenzen im Video.|
|WordCount|Die Anzahl von Wörtern pro Sprecher.|
|SpeakerNumberOfFragments|Die Anzahl von Fragmenten, über die ein Sprecher im Video verfügt.|
|SpeakerLongestMonolog|Der längste Monolog des Sprechers. Falls der Sprecher bei seinem Monolog Sprechpausen einlegt, ist diese Zeit mit enthalten. Die Ruhephasen am Anfang und Ende des Monologs werden entfernt.| 
|SpeakerTalkToListenRatio|Die Berechnung basiert auf der Zeit für den Monolog des Sprechers (ohne Sprechpausen) geteilt durch die Gesamtzeit des Videos. Der Zeitwert wird auf die dritte Dezimalstelle gerundet.|


### <a name="sentiments"></a>sentiments

Stimmungen werden anhand ihres Felds „SentimentType“ (neutral/positiv/negativ) aggregiert. Beispiel: 0-0,1, 0,1-0,2.

|NAME|BESCHREIBUNG|
|---|---|
|id|Die Stimmungs-ID.|
|averageScore |Der Durchschnitt aller Bewertungen aller Vorkommen dieses Stimmungstyps: neutral/positiv/negativ|
|instances|Eine Liste der Zeitbereiche, in denen diese Stimmung vorkam.|
|sentimentType |Der Typ kann „Positive“ (Positiv), „Neutral“ (Neutral) oder „Negative“ (Negativ) lauten.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>Bezeichnungen

|NAME|BESCHREIBUNG|
|---|---|
|id|Die Bezeichnungs-ID.|
|name|Der Bezeichnungsname (z. B. „Computer“, „TV“).|
|language|Die Sprache des Bezeichnungsnamens (sofern übersetzt). BCP-47|
|instances|Eine Liste der Zeitbereiche, in denen diese Bezeichnung angezeigt wurde (eine Bezeichnung kann mehrfach vorkommen). Jedes Vorkommen weist ein Zuverlässigkeitsfeld auf. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>keywords

|NAME|BESCHREIBUNG|
|---|---|
|id|Die Stichwort-ID.|
|text|Der Stichworttext.|
|confidence|Die Zuverlässigkeit der Erkennung des Stichworts.|
|language|Die Sprache des Stichworts (sofern übersetzt).|
|instances|Eine Liste der Zeitbereiche, in denen dieses Stichwort angezeigt wurde (ein Stichwort kann mehrfach vorkommen).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

Der visualContentModeration-Block enthält Zeitbereiche, für die von Video Indexer Inhalt ermittelt wurde, der unter Umständen nur für Erwachsene geeignet ist. Wenn „visualContentModeration“ leer ist, wurde kein Inhalt identifiziert, der ggf. nur für Erwachsene geeignet ist.

Videos, für die nicht jugendfreier bzw. freizügiger Inhalt ermittelt wird, sind unter Umständen nur für die private Wiedergabe verfügbar. Benutzer haben die Möglichkeit, für den Inhalt eine Überprüfung durch einen Menschen anzufordern. In diesem Fall enthält das IsAdult-Attribut das Ergebnis der Überprüfung durch den Menschen.

|NAME|BESCHREIBUNG|
|---|---|
|id|Die ID für die Moderation des visuellen Inhalts.|
|adultScore|Die Bewertung für nicht jugendfreien Inhalt (von Content Moderator).|
|racyScore|Die Bewertung für Freizügigkeit (von Content Moderator).|
|instances|Eine Liste mit Zeitbereichen, in denen diese visuelle Inhaltsmoderation durchgeführt wurde.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Analysieren von Videos mit Azure Media Services](analyze-videos-tutorial-with-api.md)
