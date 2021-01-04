---
title: Analysieren von Video- und Audiodateien
titleSuffix: Azure Media Services
description: Informationen zum Analysieren von Audio- und Videoinhalten mit AudioAnalyzerPreset und VideoAnalyzerPreset in Azure Media Services
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: f4784cc2e1b0276caf8326df8fad93b12f0d551d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490073"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analysieren von Video- und Audiodateien mit Azure Media Services

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Mit Azure Media Services, Version 3 können Sie Inhalte aus Video- und Audiodateien mit Video Indexer extrahieren. In diesem Artikel werden die Voreinstellungen der Version 3 des Media Services-Analysetools beschrieben, mit denen diese Inhalte extrahiert werden. Wenn Sie detailliertere Erkenntnisse möchten, verwenden Sie Video Indexer direkt. Was für die Verwendung von Video Indexer anstelle von Voreinstellungen des Media Services-Analysetools spricht, erfahren Sie im [Vergleichsdokument](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Es gibt zwei Modi für die Audioanalysevoreinstellung, Basic und Standard. Weitere Informationen finden Sie in der Beschreibung der Unterschiede unten.

Um Ihren Inhalt mit Media Services v3 zu analysieren, erstellen Sie eine **Transformation**, und senden Sie einen **Auftrag**, der eine der folgenden Voreinstellungen verwendet: [VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) oder **AudioAnalyzerPreset**. Ein Tutorial zur Verwendung von **VideoAnalyzerPreset** finden Sie unter [Analysieren von Videos mit Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Wenn Sie Voreinstellungen eines Video- oder Audioanalysetools verwenden, legen Sie über das Azure-Portal für Ihr Konto zehn reservierte Einheiten für Medien (S3) fest. Dieser Schritt ist nicht erforderlich. Sie können entweder S1 oder S2 für Audiovoreinstellungen verwenden. Weitere Informationen finden Sie unter [Skalieren der Medienverarbeitung](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Compliance, Datenschutz und Sicherheit

Wichtig: Ihre Nutzung von Video Indexer darf nicht gegen geltende Gesetze verstoßen, und weder Video Indexer noch ein anderer Azure-Dienst darf auf eine Weise verwendet werden, die die Rechte Dritter verletzt oder für Dritte schädlich ist. Bevor Sie ein Video mit biometrischen Daten zur Verarbeitung und Speicherung in den Video Indexer-Dienst hochladen, müssen Sie über sämtliche erforderlichen Rechte verfügen (einschließlich entsprechender Einwilligungen von den Personen im Video). Informationen zu Compliance, Datenschutz und Sicherheit in Video Indexer finden Sie in den [Cognitive Services-Bestimmungen](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) von Microsoft. Informationen zu den Datenschutzauflagen und zur Behandlung Ihrer Daten durch Microsoft finden Sie in den [Datenschutzbestimmungen](https://privacy.microsoft.com/PrivacyStatement), in den [Lizenzbedingungen für Onlinedienste](https://www.microsoft.com/licensing/product-licensing/products) (Online Services Terms, OST) und im [Nachtrag zur Datenverarbeitung](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (Data Processing Addendum, DPA). Weitere Datenschutzinformationen (unter anderem zu Datenaufbewahrung und Datenlöschung/-vernichtung) finden Sie in den Lizenzbedingungen für Onlinedienste und [hier](../video-indexer/faq.md). Durch die Nutzung von Video Indexer akzeptieren Sie die Cognitive Services-Bestimmungen, die OST, den DPA und die Datenschutzbestimmungen.

## <a name="built-in-presets"></a>Integrierte Voreinstellungen

Media Services unterstützt derzeit die folgenden integrierten Analysevoreinstellungen:  

|**Name der Voreinstellung**|**Szenario**|**Details**|
|---|---|---|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Audioanalyse Standard|Die Voreinstellung wendet einen vordefinierten Satz von AI-basierten Analysevorgängen, einschließlich Sprachtranskription, an. Die Voreinstellung unterstützt derzeit die Verarbeitung von Inhalten mit einer einzelnen, einsprachigen Audiospur. Sie können die Sprache für die Audionutzlast in der Eingabe angeben. Verwenden Sie dazu das BCP-47-Format des Tags zur Identifizierung von Sprache und Region. Folgende Sprachen werden unterstützt: Englisch („en-US“ und „en-GB“), Spanisch („es-ES“ und „es-MX“), Französisch („fr-FR“), Italienisch („it-IT“), Japanisch („ja-JP“), Portugiesisch („pt-BR“), Chinesisch („zh-CN“), Deutsch („de-DE“), Arabisch („ar-EG“ und „ar-SY“), Russisch („ru-RU“), Hindi („hi-IN“) und Koreanisch („ko-KR“).<br/><br/> Wenn keine Sprache angegeben oder NULL festgelegt ist, wählt die automatische Spracherkennung die erste erkannte Sprache aus und verwendet diese für die gesamte Datei. Die automatische Spracherkennung unterstützt derzeit Englisch, Chinesisch, Französisch, Deutsch, Italienisch, Japanisch, Spanisch, Russisch und Portugiesisch. Sie unterstützt nicht das dynamische Wechseln zwischen Sprachen, nachdem die erste Sprache erkannt wurde. Die automatische Spracherkennung funktioniert am besten mit Audioaufnahmen mit deutlicher Sprache. Kann die Sprache nicht automatisch erkannt werden, wird standardmäßig Englisch für die Transkription verwendet.|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Audioanalyse Basic|In diesem Modus wird Sprache-in-Text-Transkription und die Generierung einer VTT-Datei mit Untertiteln/Untertiteln für Hörgeschädigte ausgeführt. Die Ausgabe in diesem Modus beinhaltet eine Insights JSON-Datei, die nur die Stichwörter, die Transkription und Zeitinformationen enthält. Die automatische Spracherkennung und Sprecherdiarisierung sind nicht Bestandteil dieses Modus. Die Liste der unterstützten Sprachen finden Sie [hier](#built-in-presets).|
|[VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analysieren von Audio und Video|Extrahiert Erkenntnisse (umfangreiche Metadaten) von Audio- und Videoinhalten und gibt eine Datei im JSON-Format aus. Beim Verarbeiten einer Videodatei können Sie angeben, ob Sie nur Audioerkenntnisse erhalten möchten. Weitere Informationen finden Sie unter [Analysieren von Videos](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset)|Erkennen von Gesichtern in Videos|Beschreibt die Einstellungen, die bei der Analyse eines Videos verwendet werden, um alle erscheinenden Gesichter zu erkennen|

### <a name="audioanalyzerpreset-standard-mode"></a>AudioAnalyzerPreset-Standardmodus

Die Voreinstellung ermöglicht Ihnen, mehrere Audioinformationen aus einer Audio- oder Videodatei zu extrahieren.

Die Ausgabe enthält eine JSON-Datei (mit allen Informationen) und eine VTT-Datei für die Audiotranskription. Diese Voreinstellung akzeptiert eine Eigenschaft, die die Sprache der Eingabedatei in Form einer [BCP47](https://tools.ietf.org/html/bcp47)-Zeichenfolge angibt. Die Audioinformationen umfassen Folgendes:

* **Audiotranskription:** ein Transkript des gesprochenen Texts mit Zeitstempeln. Es werden mehrere Sprachen unterstützt.
* **Sprecherindizierung:** Zuordnung der Sprecher und des jeweils gesprochenen Texts
* **Sprachstimmungsanalyse:** die Ausgabe der auf die Audiotranskription angewendeten Sprachstimmung
* **Schlüsselwörter:** aus der Audiotranskription extrahierte Schlüsselbegriffe

### <a name="audioanalyzerpreset-basic-mode"></a>AudioAnalyzerPreset-Basic-Modus

Die Voreinstellung ermöglicht Ihnen, mehrere Audioinformationen aus einer Audio- oder Videodatei zu extrahieren.

Die Ausgabe enthält eine JSON-Datei und eine VTT-Datei für die Audiotranskription. Diese Voreinstellung akzeptiert eine Eigenschaft, die die Sprache der Eingabedatei in Form einer [BCP47](https://tools.ietf.org/html/bcp47)-Zeichenfolge angibt. Die Ausgabe umfasst Folgendes:

* **Audiotranskription:** ein Transkript des gesprochenen Texts mit Zeitstempeln. Es werden mehrere Sprachen unterstützt, die automatische Spracherkennung und Sprecherdiarisierung sind aber nicht Bestandteil des Modus.
* **Schlüsselwörter:** aus der Audiotranskription extrahierte Schlüsselbegriffe

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Die Voreinstellung ermöglicht Ihnen, mehrere Audio- und Videoinformationen aus einer Videodatei zu extrahieren. Die Ausgabe enthält eine JSON-Datei (mit allen Informationen), eine VTT-Datei für die Videotranskription und eine Sammlung von Miniaturbildern. Diese Voreinstellung akzeptiert auch eine [BCP47](https://tools.ietf.org/html/bcp47)-Zeichenfolge (die die Sprache des Videos darstellt) als Eigenschaft. Die Videoinformationen umfassen alle oben genannten Audioinformationen sowie die folgenden zusätzlichen Elemente:

* **Gesichtsverfolgung:** die Zeit, während der Gesichter im Video zu sehen sind. Jedes Gesicht weist eine Gesichts-ID und eine entsprechende Sammlung von Miniaturbildern auf.
* **Sicherbarer Text:** der Text, der über optische Zeichenerkennung ermittelt wird. Der Text ist mit einem Zeitstempel versehen und wird auch zum Extrahieren von Stichwörtern verwendet (zusätzlich zum Audiotranskript).
* **Keyframes:** eine Sammlung von Keyframes, die aus dem Video extrahiert werden
* **Moderation visueller Inhalte:** der Teil der Videos, der als nicht jugendfrei oder anstößig gekennzeichnet wird
* **Anmerkung:** das Ergebnis der Kommentierung der Videos anhand eines vordefinierten Objektmodells

## <a name="insightsjson-elements"></a>Elemente in „insights.json“

Die Ausgabe umfasst eine JSON-Datei (insights.json) mit allen Informationen, die in der Video- oder Audiodatei gefunden wurden. Die JSON-Datei kann die folgenden Elemente enthalten:

### <a name="transcript"></a>Transkript

|Name|BESCHREIBUNG|
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

|Name|BESCHREIBUNG|
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

|Name|BESCHREIBUNG|
|---|---|
|id|Die Gesichts-ID.|
|name|Der Name des Gesichts. Möglich sind „Unknown #0“, ein identifizierter Prominenter oder eine vom Kunden trainierte Person.|
|confidence|Die Zuverlässigkeit der Gesichtsidentifikation.|
|description|Eine Beschreibung des Prominenten. |
|thumbnailId|Die ID der Miniaturansicht dieses Gesichts.|
|knownPersonId|Die interne ID (bei einer bekannten Person)|
|referenceId|Die Bing-ID (wenn es sich um einen Bing-Prominenten handelt)|
|referenceType|Zurzeit nur Bing.|
|title|Der Titel (z. B. „CEO von Microsoft“ bei einem Prominenten)|
|imageUrl|Die Bild-URL, wenn es sich um einen Prominenten handelt|
|instances|Instanzen, in denen das Gesicht in einem bestimmten Zeitraum zu sehen war Jedes Vorkommen hat auch eine Miniaturbild-ID. |

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

|Name|BESCHREIBUNG|
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

|Name|BESCHREIBUNG|
|---|---|
|CorrespondenceCount|Anzahl von Korrespondenzen im Video.|
|WordCount|Die Anzahl von Wörtern pro Sprecher.|
|SpeakerNumberOfFragments|Die Anzahl von Fragmenten, über die ein Sprecher im Video verfügt.|
|SpeakerLongestMonolog|Der längste Monolog des Sprechers. Falls der Sprecher bei seinem Monolog Sprechpausen einlegt, ist diese Zeit mit enthalten. Die Ruhephasen am Anfang und Ende des Monologs werden entfernt.|
|SpeakerTalkToListenRatio|Die Berechnung basiert auf der Zeit für den Monolog des Sprechers (ohne Sprechpausen) geteilt durch die Gesamtzeit des Videos. Der Zeitwert wird auf die dritte Dezimalstelle gerundet.|


### <a name="sentiments"></a>sentiments

Stimmungen werden anhand ihres Felds „SentimentType“ (neutral/positiv/negativ) aggregiert. Beispiel: 0-0,1, 0,1-0,2.

|Name|BESCHREIBUNG|
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

|Name|BESCHREIBUNG|
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

|Name|BESCHREIBUNG|
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

Der visualContentModeration-Block enthält Zeitbereiche, für die von Video Indexer Inhalt ermittelt wurde, der unter Umständen nur für Erwachsene geeignet ist. Wenn „visualContentModeration“ leer ist, wurde kein Inhalt als nicht jugendfrei identifiziert.

Videos, für die nicht jugendfreier bzw. freizügiger Inhalt ermittelt wird, sind unter Umständen nur für die private Wiedergabe verfügbar. Benutzer können die Überprüfung des Inhalts durch einen Menschen anfordern. In diesem Fall enthält das Attribut `IsAdult` das Ergebnis der Überprüfung durch den Menschen.

|Name|BESCHREIBUNG|
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