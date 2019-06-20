---
title: Untersuchen der von der v2-API erstellten Video Indexer-Ausgabe von Azure Media Services
titlesuffix: Azure Media Services
description: In diesem Thema wird die Video Indexer-Ausgabe untersucht, die von der v2-API erstellt wird.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 205dc7d9e69788ea29a48ff342844a4b74e143bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799083"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>Untersuchen der von der API erstellten Video Indexer-Ausgabe

Wenn Sie die API zum Abrufen des Videoindex (**Get Video Index**) aufrufen und der Antwortstatus „OK“ lautet, erhalten Sie eine ausführliche JSON-Ausgabe als Inhalt der Antwort. Die JSON-Daten enthalten Details zu den angegebenen Erkenntnissen aus Videos. Zu diesen Erkenntnissen gehören z. B. folgende Dimensionen: Transkripts, OCRs, Gesichter, Themen, Blöcke usw. Die Dimensionen verfügen über Instanzen von Zeitbereichen, die angezeigt werden, wenn die einzelnen Dimensionen im Video angezeigt werden.  

Sie können die zusammengefassten Erkenntnisse des Videos auch visuell untersuchen, indem Sie auf der [Video Indexer-Website](https://www.videoindexer.ai/) auf die Schaltfläche **Wiedergabe** klicken. Weitere Informationen finden Sie unter [View and edit video insights](video-indexer-view-edit.md) (Anzeigen und Bearbeiten von Videoinformationen).

![Einblicke](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

In diesem Artikel wird der JSON-Inhalt untersucht, der von der API zum Abrufen des Videoindex (**Get Video Index**) zurückgegeben wird. 

> [!NOTE]
> Der Zeitraum bis zum Ablauf aller Zugriffstoken in Video Indexer beträgt eine Stunde.


## <a name="root-elements"></a>Stammelemente

|NAME|BESCHREIBUNG|
|---|---|
|accountId|Die VI-Konto-ID der Wiedergabeliste.|
|id|Die ID der Wiedergabeliste.|
|name|Der Name der Wiedergabeliste.|
|description|Die Beschreibung der Wiedergabeliste.|
|userName|Der Name des Benutzers, der die Wiedergabeliste erstellt hat.|
|created|Die Erstellungszeit der Wiedergabeliste.|
|privacyMode|Der Datenschutzmodus der Wiedergabeliste (privat/öffentlich).|
|state|Der Status der Wiedergabeliste (uploaded (Hochgeladen), processing (Wird verarbeitet), processed (Verarbeitet), failed (Fehler), quarantined (Quarantäne)).|
|isOwned|Gibt an, ob die Wiedergabeliste vom aktuellen Benutzer erstellt wurde.|
|isEditable|Gibt an, ob der aktuelle Benutzer zum Bearbeiten der Wiedergabeliste berechtigt ist.|
|isBase|Gibt an, ob die Wiedergabeliste eine Basiswiedergabeliste (ein Video) oder eine Wiedergabeliste ist, die aus anderen Videos zusammengestellt (abgeleitet) wurde.|
|durationInSeconds|Die Gesamtdauer der Wiedergabeliste.|
|summarizedInsights|Enthält ein [summarizedInsights](#summarizedinsights)-Element.
|videos|Eine Liste mit [Videos](#videos), aus denen die Wiedergabeliste besteht.<br/>Wenn diese Wiedergabeliste aus den Zeitbereichen anderer Videos zusammengestellt (abgeleitet) ist, enthalten die Videos in dieser Liste nur Daten aus den einbezogenen Zeitbereichen.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

In diesem Abschnitt wird die Zusammenfassung der Erkenntnisse angezeigt.

|Attribut | BESCHREIBUNG|
|---|---|
|name|Der Name des Videos. Beispiel: Azure Monitor.|
|id|Die ID des Videos. Beispiel: 63c6d532ff.|
|privacyMode|Für Ihre Aufschlüsselung kann einer der folgenden Modi verwendet werden: **Private** oder **Public**. **Public**: Das Video ist für alle Benutzer Ihres Kontos und alle Personen sichtbar, die über einen Link zum Video verfügen. **Private**: Das Video ist für alle Benutzer Ihres Kontos sichtbar.|
|duration|Enthält eine Dauer, mit der der Zeitbereich beschrieben wird, in dem eine Erkenntnis gewonnen wurde. Die Dauer wird in Sekunden angegeben.|
|thumbnailVideoId|Die ID des Videos, aus dem die Miniaturansicht entnommen wurde.
|thumbnailId|Die Miniaturansicht-ID des Videos. Rufen Sie [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) auf, und übergeben Sie „thumbnailVideoId“ und „thumbnailId“, um die eigentliche Miniaturansicht zu erhalten.|
|faces|Kann null oder mehr Gesichter enthalten. Ausführlichere Informationen finden Sie unter [faces](#faces).|
|keywords|Kann null oder mehr Schlüsselwörter enthalten. Ausführlichere Informationen finden Sie unter [keywords](#keywords).|
|sentiments|Kann null oder mehr Stimmungen enthalten. Ausführlichere Informationen finden Sie unter [sentiments](#sentiments).|
|audioEffects| Kann null oder mehr Audioeffekte (audioEffects) enthalten. Ausführlichere Informationen finden Sie unter [audioEffects](#audioEffects).|
|Bezeichnungen| Kann null oder mehr Bezeichnungen enthalten. Weitere Informationen finden Sie unter [labels](#labels).|
|brands| Kann null oder mehr Marken enthalten. Ausführlichere Informationen finden Sie unter [brands](#brands).|
|statistics | Ausführlichere Informationen finden Sie unter [statistics](#statistics).|
|emotions| Kann null oder mehrere Emotionen enthalten. Ausführlichere Informationen finden Sie unter [emotions](#emotions).|
|topics|Kann null oder mehrere Themen enthalten. Die Dimension [topics](#topics).|

## <a name="videos"></a>videos

|NAME|BESCHREIBUNG|
|---|---|
|accountId|Die VI-Konto-ID des Videos.|
|id|Die ID des Videos.|
|name|Der Name des Videos.
|state|Der Status des Videos (uploaded (Hochgeladen), processing (Wird verarbeitet), processed (Verarbeitet), failed (Fehler), quarantined (Quarantäne)).|
|processingProgress|Der Status der Verarbeitung während des Verarbeitungsprozesses (z.B. 20%).|
|failureCode|Der Fehlercode, wenn bei der Verarbeitung ein Fehler aufgetreten ist (z.B. „UnsupportedFileType“).|
|failureMessage|Die Fehlermeldung, wenn bei der Verarbeitung ein Fehler aufgetreten ist.|
|externalId|Die externe ID des Videos (falls vom Benutzer angegeben).|
|externalUrl|Die externe URL des Videos (falls vom Benutzer angegeben).|
|metadata|Die externen Metadaten des Videos (falls vom Benutzer angegeben).|
|isAdult|Gibt an, ob das Video manuell geprüft und als nur für Erwachsene geeignetes Video eingestuft wurde.|
|insights|Das insights-Objekt. Weitere Informationen finden Sie unter [insights](#insights).|
|thumbnailId|Die Miniaturansicht-ID des Videos. Rufen Sie [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) auf, und übergeben Sie die Video-ID und „thumbnailId“, um die eigentliche Miniaturansicht zu erhalten.|
|publishedUrl|Eine URL zum Streamen des Videos.|
|publishedUrlProxy|Eine URL, über die das Video gestreamt werden kann (für Apple-Geräte).|
|viewToken|Ein kurzlebiges Anzeigetoken für das Streamen des Videos.|
|sourceLanguage|Die Quellsprache des Videos.|
|language|Die tatsächliche Sprache des Videos (Übersetzung).|
|indexingPreset|Die Voreinstellung, die zum Indizieren des Videos verwendet wird.|
|streamingPreset|Die Voreinstellung, die zum Veröffentlichen des Videos verwendet wird.|
|linguisticModelId|Das CRIS-Modell, das zum Transkribieren des Videos verwendet wird.|
|statistics | Weitere Informationen finden Sie unter [statistics](#statistics).|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>insights

Bei „insights“ (Erkenntnissen) handelt es sich um eine Reihe von Dimensionen (z.B. Transkriptzeilen, Gesichter, Marken usw.), wobei jede Dimension eine Liste mit eindeutigen Elementen umfasst (z.B. face1, face2, face3). Außerdem verfügt jedes Element über eigene Metadaten und eine Liste mit den zugehörigen Instanzen (Zeitbereiche mit zusätzlichen optionalen Metadaten).

Ein Gesicht kann eine ID, einen Namen, eine Miniaturansicht, andere Metadaten und eine Liste mit seinen temporalen Instanzen aufweisen (Beispiele: 00:00:05 – 00:00:10, 00:01:00 – 00:02:30 und 00:41:21 – 00:41:49). Jede temporale Instanz kann über zusätzliche Metadaten verfügen. Ein Beispiel hierfür sind die Rechteckkoordinaten des Gesichts (20,230,60,60).

|Version|Codeversion|
|---|---|
|sourceLanguage|Die Quellsprache des Videos (Annahme: eine Hauptsprache). Diese Angabe hat die Form einer [BCP-47](https://tools.ietf.org/html/bcp47)-Zeichenfolge.|
|language|Die Sprache der Erkenntnisse (Übersetzung aus der Quellsprache). Diese Angabe hat die Form einer [BCP-47](https://tools.ietf.org/html/bcp47)-Zeichenfolge.|
|Transkript|Die Dimension [transcript](#transcript).|
|ocr|Die Dimension [OCR](#ocr).|
|keywords|Die Dimension [keywords](#keywords).|
|blocks|Kann einen oder mehrere Blöcke ([blocks](#blocks)) enthalten.|
|faces|Die Dimension [faces](#faces).|
|Bezeichnungen|Die Dimension [labels](#labels).|
|shots|Die Dimension [shots](#shots).|
|brands|Die Dimension [brands](#brands).|
|audioEffects|Die Dimension [audioEffects](#audioEffects).|
|sentiments|Die Dimension [sentiments](#sentiments).|
|visualContentModeration|Die Dimension [visualContentModeration](#visualcontentmoderation).|
|textualContentModeration|Die Dimension [textualConentModeration](#textualcontentmoderation).|
|emotions| Die Dimension [emotions](#emotions).|
|topics|Die Dimension [topics](#topics).|

Beispiel:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blocks

Attribut | BESCHREIBUNG
---|---
id|Die ID des Blocks.|
instances|Eine Liste mit Zeitbereichen dieses Blocks.|

#### <a name="transcript"></a>Transkript

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

#### <a name="ocr"></a>ocr

|NAME|BESCHREIBUNG|
|---|---|
|id|Die OCR-Zeilen-ID.|
|text|Der OCR-Text.|
|confidence|Die Zuverlässigkeit der Erkennung.|
|language|Die OCR-Sprache.|
|instances|Eine Liste der Zeitbereiche, in denen diese OCR angezeigt wurde (die gleiche OCR kann mehrfach vorkommen).|
|height|Die Höhe des OCR-Rechtecks.|
|top|Die oberste Position in „px“.|
|Linker Join| Die linke Position in „px“.|
|width|Die Breite des OCR-Rechtecks.|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>keywords

|NAME|BESCHREIBUNG|
|---|---|
|id|Die Stichwort-ID.|
|text|Der Stichworttext.|
|confidence|Die Zuverlässigkeit der Erkennung des Stichworts.|
|language|Die Sprache des Stichworts (sofern übersetzt).|
|instances|Eine Liste der Zeitbereiche, in denen dieses Stichwort angezeigt wurde (ein Stichwort kann mehrfach vorkommen).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="faces"></a>faces

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

#### <a name="labels"></a>Bezeichnungen

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

#### <a name="scenes"></a>scenes

|NAME|BESCHREIBUNG|
|---|---|
|id|Die Szenen-ID.|
|instances|Eine Liste der Zeitbereiche dieser Szene (eine Szene kann nur eine Instanz aufweisen).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>shots

|NAME|BESCHREIBUNG|
|---|---|
|id|Die ID der Aufnahme.|
|keyFrames|Eine Liste mit Keyframes innerhalb der Aufnahme (jede verfügt über eine ID und eine Liste der Zeitbereiche der Vorkommen). Jede keyFrame-Instanz verfügt über ein „thumbnailId“-Feld, das die Miniaturbild-ID von keyFrame enthält.|
|instances|Eine Liste der Zeitbereiche dieser Aufnahme (eine Aufnahme kann nur eine Instanz aufweisen).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>brands

Markennamen von Unternehmen oder Produkten, die im Transkript der Spracherkennung bzw. in den Video-OCR-Daten erkannt werden. Die visuelle Erkennung von Marken oder die Logoerkennung sind hierin nicht enthalten.

|NAME|BESCHREIBUNG|
|---|---|
|id|Die Marken-ID.|
|name|Der Name der Marke.|
|referenceId | Das Suffix der Wikipedia-URL für die Marke. Beispielsweise ist „Target_Corporation“ das Suffix von [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | Die Wikipedia-URL der Marke, falls vorhanden. Beispiel: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)
|description|Die Beschreibung der Marke.|
|tags|Eine Liste mit vordefinierten Tags, die dieser Marke zugeordnet wurden.|
|confidence|Der Zuverlässigkeitswert der Video Indexer-Markenerkennung (0 - 1).|
|instances|Eine Liste mit Zeitbereichen dieser Marke. Jede Instanz verfügt über ein brandType-Element, mit dem angegeben wird, ob diese Marke im Transkript oder in den OCR-Daten angezeigt wird.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statistics

|NAME|BESCHREIBUNG|
|---|---|
|CorrespondenceCount|Anzahl von Korrespondenzen im Video.|
|SpeakerWordCount|Die Anzahl von Wörtern pro Sprecher.|
|SpeakerNumberOfFragments|Die Anzahl von Fragmenten, über die ein Sprecher im Video verfügt.|
|SpeakerLongestMonolog|Der längste Monolog des Sprechers. Falls der Sprecher bei seinem Monolog Sprechpausen einlegt, ist diese Zeit mit enthalten. Die Ruhephasen am Anfang und Ende des Monologs werden entfernt.| 
|SpeakerTalkToListenRatio|Die Berechnung basiert auf der Zeit für den Monolog des Sprechers (ohne Sprechpausen) geteilt durch die Gesamtzeit des Videos. Der Zeitwert wird auf die dritte Dezimalstelle gerundet.|

#### <a name="a-idaudioeffectsaudioeffects"></a><a id="audioEffects"/>audioEffects

|NAME|BESCHREIBUNG|
|---|---|
|id|Die ID des Audioeffekts.|
|type|Der Typ des Audioeffekts (z. B. Geklatsche, Sprache, Stille).|
|instances|Eine Liste der Zeitbereiche, in denen dieser Audioeffekt vorkam.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>sentiments

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

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|NAME|BESCHREIBUNG|
|---|---|
|id|Die ID für die Textinhaltsmoderation.|
|bannedWordsCount |Die Anzahl von gesperrten Wörtern.|
|bannedWordsRatio |Das Verhältnis der gesperrten Wörter zu den Gesamtwörtern.|

#### <a name="emotions"></a>emotions

Video Indexer erkennt Emotionen basierend auf Sprache und Audiosignalen. Bei den erkannten Emotionen kann es sich um Freude, Trauer, Wut oder Angst handeln.

|NAME|BESCHREIBUNG|
|---|---|
|id|Die ID der Emotion.|
|type|Der Moment der Emotion, der basierend auf Sprach- und Audiosignalen erkannt wurde. Bei den Emotionen kann es sich um Freude, Trauer, Wut oder Angst handeln.|
|instances|Eine Liste der Zeitbereiche, in denen diese Emotion aufgetaucht ist.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>topics

Video Indexer zieht in den Transkripten einen Rückschluss auf Hauptthemen. Falls möglich, ist eine [IPTC](https://iptc.org/standards/media-topics/)-Taxonomie erster Ebene eingeschlossen. 

|NAME|BESCHREIBUNG|
|---|---|
|id|Die ID des Themas.|
|name|Der Name des Themas, z.B.: „Pharmazeutika“.|
|referenceId|Brotkrümel, die die Hierarchie der Themen reflektieren. Beispiel:  „Gesundheit und Wohlbefinden/Medizin und Gesundheitswesen/Pharmazeutika“.|
|confidence|Die Zuverlässigkeitsbewertung im Bereich [0,1]. Je höher, desto zuverlässiger.|
|language|Die im Thema verwendete Sprache.|
|iptcName|Falls erkannt, der Codename von IPTC-Medien.|
|instances |Derzeit indiziert Video Indexer ein Thema nicht in Zeitintervallen, weshalb das gesamte Video als Intervall verwendet wird.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>Nächste Schritte

[Entwicklerportal für Video Indexer](https://api-portal.videoindexer.ai)

Informationen dazu, wie Sie Widgets in Ihre Anwendung einbetten, finden Sie unter [Embed Video Indexer widgets into your applications](video-indexer-embed-widgets.md) (Einbetten von Video Indexer-Widgets in Ihre Anwendungen). 

