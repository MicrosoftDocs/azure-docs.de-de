---
title: Die Qualifikation „OCR“ der kognitiven Suche – Azure Search
description: Extrahieren Sie Text aus Bilddateien mithilfe der optischen Zeichenerkennung (OCR) in einer Azure Search-Anreicherungspipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 6d9b68bda2a6cff533286d9ee944abf1c92cc2bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523238"
---
# <a name="ocr-cognitive-skill"></a>Der Skill „OCR“

Mit der Qualifikation für die optische Zeichenerkennung (OCR) wird gedruckter und handschriftlicher Text in Bilddateien erkannt. Diese Qualifikation verwendet die durch [Maschinelles Sehen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) in Cognitive Services bereitgestellten Machine Learning-Modelle. Die Qualifikation **OCR** ist den folgenden Funktionen zugeordnet:

+ Wenn „textExtractionAlgorithmus“ auf „handwritten“ festgelegt ist, wird die Funktion [„RecognizeText“](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) verwendet.
+ Wenn „textExtractionAlgorithmus“ auf „printed“ festgelegt ist, wird die Funktion [„OCR“](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) für andere Sprachen als Englisch verwendet. Für Englisch wird die neue Funktion [„Texterkennung“](../cognitive-services/computer-vision/concept-recognizing-text.md) für gedruckten Text verwendet.

Der Skill **OCR** extrahiert Text aus Bilddateien. Folgende Dateiformate werden unterstützt:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> Wenn Sie den Umfang erweitern, indem Sie die Verarbeitungsfrequenz erhöhen oder weitere Dokumente oder KI-Algorithmen hinzufügen, müssen Sie [eine kostenpflichtige Cognitive Services-Ressource anfügen](cognitive-search-attach-cognitive-services.md). Gebühren fallen beim Aufrufen von APIs in Cognitive Services sowie für die Bildextraktion im Rahmen der Dokumentaufschlüsselungsphase in Azure Search an. Für die Textextraktion aus Dokumenten fallen keine Gebühren an.
>
> Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion werden auf der [Preisseite von Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) beschrieben.


## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| detectOrientation | Aktiviert die automatische Erkennung der Bildausrichtung. <br/> Gültige Werte: „true“ und „false“|
|defaultLanguageCode | <p>  Sprachcode des Eingabetexts. Unterstützte Sprachen: <br/> zh-Hans (Vereinfachtes Chinesisch) <br/> zh-Hant (Traditionelles Chinesisch) <br/>cs (Tschechisch) <br/>da (Dänisch) <br/>nl (Niederländisch) <br/>en (Englisch) <br/>fi (Finnisch)  <br/>fr (Französisch) <br/>  de (Deutsch) <br/>el (Griechisch) <br/> hu (Ungarisch) <br/> it (Italienisch) <br/>  ja (Japanisch) <br/> ko (Koreanisch) <br/> nb (Norwegisch) <br/>   pl (Polnisch) <br/> pt (Portugiesisch) <br/>  ru (Russisch) <br/>  es (Spanisch) <br/>  sv (Schwedisch) <br/>  tr (Türkisch) <br/> ar (Arabisch) <br/> ro (Rumänisch) <br/> sr-Cyrl (Serbisch, kyrillisch) <br/> sr-Latn (Serbisch, lateinisch) <br/>  sk (Slowakisch) <br/>  unk (Unbekannt) <br/><br/> Wenn der Sprachcode nicht angegeben oder Null ist, wird als Sprache automatisch Englisch festgelegt. Wenn die Sprache explizit auf „unk“ festgelegt ist, wird die Sprache automatisch erkannt. </p> |
| textExtractionAlgorithm | „printed“ (gedruckt) oder „handwritten“ (handgeschrieben) Der OCR-Algorithmus für die Erkennung von „handgeschriebenem“ Text befindet sich derzeit in der Vorschau und ist nur in englischer Sprache verfügbar. |
|lineEnding | Der Wert, der zwischen jeder erkannten Zeile verwendet werden soll. Mögliche Werte: 'Space','CarriageReturn','LineFeed'.  Der Standardwert ist 'Space'. |

## <a name="skill-inputs"></a>Skilleingaben

| Eingabename      | BESCHREIBUNG                                          |
|---------------|------------------------------------------------------|
| image         | Komplexer Typ. Arbeitet derzeit mit dem Feld „/document/normalized_images“, das vom Azure Blob-Indexer generiert wird, wenn ```imageAction``` auf einen anderen Wert als ```none``` gesetzt ist. Weitere Informationen finden Sie im [Beispiel](#sample-output).|


## <a name="skill-outputs"></a>Skillausgaben
| Ausgabename     | BESCHREIBUNG                   |
|---------------|-------------------------------|
| text          | Aus dem Bild extrahierter Nur-Text-Inhalt.   |
| layoutText    | Komplexer Typ, der den extrahierten Text und die Fundstelle beschreibt.|


## <a name="sample-definition"></a>Beispieldefinition

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Beispieltext und „layoutText“-Ausgabe

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Beispiel: Text, der aus eingebetteten Bildern extrahiert wurde, wird mit dem Inhalt des Dokuments zusammengeführt.

Ein häufiger Anwendungsfall für die Textzusammenführung ist die Möglichkeit, die Textdarstellung von Bildern (Text aus einem OCR-Skill oder der Titel eines Bildes) in das Inhaltsfeld eines Dokuments einzubinden.

Mit der folgenden Beispielqualifikationsgruppe wird das Feld mit der Bezeichnung *merged_text* erstellt. Dieses Feld enthält den Textinhalt des Dokuments und den per OCR erkannten Text aus den einzelnen im Dokument eingebetteten Bildern.

#### <a name="request-body-syntax"></a>Syntax des Anforderungstexts
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
Im oben gezeigten Beispiel für das Skillset wird davon ausgegangen, dass ein Feld mit normalisierten Bildern vorhanden ist. Um ein Feld zu erhalten, legen Sie die Konfiguration *imageAction* in Ihrer Indexerdefinition auf *generateNormalizedImages* fest, wie unten gezeigt:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Weitere Informationen
+ [Vordefinierte Skills](cognitive-search-predefined-skills.md)
+ [Der Skill „Text zusammenführen“](cognitive-search-skill-textmerger.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Erstellen eines Indexers (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
