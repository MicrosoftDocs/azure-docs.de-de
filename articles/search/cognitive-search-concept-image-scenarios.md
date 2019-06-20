---
title: Verarbeiten und Extrahieren von Text aus Bildern in der kognitiven Suche – Azure Search
description: Verarbeiten und extrahieren Sie Text und andere Informationen aus Bildern in kognitiven Suchpipelines in Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: ca9b3607041f75b1c866aa2813308312ad5d1017
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023748"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Verarbeiten und Extrahieren von Text aus Bildern in kognitiven Suchszenarien

Die kognitive Suche bietet mehrere Funktionen für die Verarbeitung von Bildern und Bilddateien. Im Rahmen der Dokumentaufschlüsselung können Sie den Parameter *imageAction* verwenden, um alphanumerischen Text (beispielsweise das Wort „Stopp“ eines Stopp-Schilds) aus Fotos oder Bildern zu extrahieren. Ein anderes Szenario wäre etwa die Generierung einer Textdarstellung eines Bilds – etwa „Löwenzahn“ (oder die Farbe „Gelb“) für ein Foto eines Löwenzahns. Des Weiteren können Sie Metadaten des Bilds extrahieren (beispielsweise die Größe).

Dieser Artikel geht etwas ausführlicher auf die Bildverarbeitung ein und bietet einen Leitfaden für die Verwendung von Bildern in einer kognitiven Suchpipeline.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Generieren normalisierter Bilder

Im Rahmen der Dokumentaufschlüsselung steht ein neuer Satz von Indexerkonfigurationsparametern für die Behandlung von Bilddateien oder von Bildern zur Verfügung, die in Dateien eingebettet sind. Diese Parameter dienen zur Normalisierung von Bildern für die Weiterverarbeitung. Die Normalisierung dient zur Generierung einheitlicherer Bilder. Große Bilder werden auf eine maximale Höhe und Breite festgelegt, um sie nutzbar zu machen. Bei Bildern mit Metadaten zur Ausrichtung wird die Drehung angepasst, um vertikales Laden zu ermöglichen. Metadatenanpassungen werden in einem komplexen Typ erfasst, der für jedes Bild erstellt wird. 

Die Bildnormalisierung kann nicht deaktiviert werden. Qualifikationen, die Bilder durchlaufen, erwarten normalisierte Bilder.

| Konfigurationsparameter | BESCHREIBUNG |
|--------------------|-------------|
| imageAction   | Legen Sie diese Eigenschaft auf „none“ fest, falls bei der Erkennung von eingebetteten Bildern oder Bilddateien keine Aktion erfolgen soll. <br/>Wenn Sie die Eigenschaft auf „generateNormalizedImages“ festlegen, wird im Rahmen der Dokumentaufschlüsselung ein Array mit normalisierten Bildern generiert.<br/>Legen Sie diese Option auf „generateNormalizedImagePerPage“ fest, um ein Array von normalisierten Bildern zu erzeugen, bei dem für PDFs in Ihrer Datenquelle jede Seite in ein Ausgabebild gerendert wird.  Die Funktionalität ist die gleiche wie bei „generateNormalizedImages“ für Nicht-PDF-Dateitypen.<br/>Für alle anderen Optionen als „none“ werden diese Bilder im Feld *normalized_images* verfügbar gemacht. <br/>Der Standardwert ist „none“. Diese Konfiguration ist nur für Blobdatenquellen relevant, wenn „dataToExtract" auf „contentAndMetadata“ festgelegt ist. <br/>Maximal 1000 Bilder werden aus einem angegebenen Dokument extrahiert. Wenn in einem Dokument mehr als 1000 Bilder vorhanden sind, werden die ersten 1000 extrahiert, und eine Warnung wird generiert. |
|  normalizedImageMaxWidth | Die maximale Breite (in Pixel) für generierte normalisierte Bilder. Der Standardwert ist „2000“.|
|  normalizedImageMaxHeight | Die maximale Höhe (in Pixel) für generierte normalisierte Bilder. Der Standardwert ist „2000“.|

> [!NOTE]
> Wenn Sie die Eigenschaft *imageAction* auf einen anderen Wert als „none“ festlegen, kann die Eigenschaft *parsingMode* nur auf „default“ festgelegt werden.  In der Indexerkonfiguration kann immer nur eine dieser beiden Eigenschaften auf einen standardfremden Wert festgelegt werden.

Legen Sie den Parameter **parsingMode** auf `json` (zum Indizieren jedes Blob als einzelnes Dokument) oder `jsonArray` (wenn die Blobs JSON-Arrays enthalten und jedes Element eines Arrays als separates Dokument behandelt werden soll) fest.

Der Standardwert von 2.000 Pixeln für die maximale Breite und Höhe der normalisierten Bilder basiert auf der maximal unterstützten Größe der [OCR-Qualifikation](cognitive-search-skill-ocr.md) und der [Bildanalysequalifikation](cognitive-search-skill-image-analysis.md). Wenn Sie die maximalen Grenzwerte erhöhen, können größere Bilder möglicherweise nicht erfolgreich verarbeitet werden.


Die imageAction-Eigenschaft wird in der [Indexerdefinition](https://docs.microsoft.com/rest/api/searchservice/create-indexer) wie folgt angegeben:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Wenn *imageAction* auf einen anderen Wert als „none“ festgelegt wird, enthält das neue Feld *normalized_images* ein Array von Bildern. Jedes Bild ist ein komplexer Typ mit folgenden Elementen:

| Bildelement       | BESCHREIBUNG                             |
|--------------------|-----------------------------------------|
| data               | Base64-codierte Zeichenfolge mit dem normalisierten Bild im JPEG-Format.   |
| width              | Breite des normalisierten Bilds (in Pixel). |
| height             | Höhe des normalisierten Bilds (in Pixel). |
| originalWidth      | Ursprüngliche Breite des Bilds vor der Normalisierung. |
| originalHeight      | Ursprüngliche Höhe des Bilds vor der Normalisierung. |
| rotationFromOriginal |  Drehung gegen den Uhrzeigersinn zur Erstellung des normalisierten Bilds (in Grad). Der Wert muss zwischen 0 und 360 Grad liegen. In diesem Schritt werden die durch eine Kamera oder einen Scanner generierten Metadaten aus dem Bild gelesen. Der Wert ist in der Regel ein Vielfaches von 90 Grad. |
| contentOffset |Das Zeichenoffset in dem Inhaltsfeld, aus dem das Bild extrahiert wurde. Dieses Feld ist nur für Dateien mit eingebetteten Bildern relevant. |

 Beispielwert von *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500  
  }
]
```

## <a name="image-related-skills"></a>Bildbezogene Qualifikationen

Es gibt zwei integrierte kognitive Qualifikationen, die Bilder als Eingabe akzeptieren: [OCR](cognitive-search-skill-ocr.md) und [Bildanalyse](cognitive-search-skill-image-analysis.md). 

Momentan können diese Qualifikationen nur für Bilder verwendet werden, die durch den Dokumentaufschlüsselungsschritt generiert wurden. Daher wird als Eingabe ausschließlich `"/document/normalized_images"` unterstützt.

### <a name="image-analysis-skill"></a>Bildanalysequalifikation

Die [Bildanalysequalifikation](cognitive-search-skill-image-analysis.md) extrahiert anhand des Bildinhalts einen umfangreichen Satz von optischen Merkmalen. So können Sie beispielsweise anhand eines Bilds eine Beschriftung erstellen, Tags generieren oder Prominente und Wahrzeichen identifizieren.

### <a name="ocr-skill"></a>OCR-Qualifikation

Die [OCR-Qualifikation](cognitive-search-skill-ocr.md) extrahiert Text aus Bilddateien – etwa aus JPG-, PNG- und Bitmap-Dateien. Sie kann sowohl Text als auch Layoutinformationen extrahieren. Die Layoutinformationen bieten umgebende Rechtecke für die jeweils erkannten Zeichenfolgen.

Mit der OCR-Qualifikation können Sie den Algorithmus für die Texterkennung in Ihren Bildern auswählen. Aktuell werden zwei Algorithmen unterstützt: einer für gedruckten Text und einer für handschriftlichen Text.

## <a name="embedded-image-scenario"></a>Szenario mit eingebettetem Bild

Ein gängiges Szenario ist die Erstellung einer einzelnen Zeichenfolge mit sämtlichen Dateiinhalten (sowohl Text als auch Bildursprungstext):  

1. [Extrahieren Sie „normalized_images“.](#get-normalized-images)
1. Führen Sie die OCR-Qualifikation mit `"/document/normalized_images"` als Eingabe aus.
1. Führen Sie die Textdarstellung dieser Bilder mit dem unformatierten Text aus der Datei zusammen. Die beiden Textblöcke können mithilfe der [Textzusammenführungsqualifikation](cognitive-search-skill-textmerger.md) in einer einzelnen umfangreichen Zeichenfolge konsolidiert werden.

Die folgende Beispielqualifikationsgruppe erstellt ein Feld namens *merged_text* mit dem Textinhalt Ihres Dokuments. Darüber hinaus enthält sie den per OCR erkannten Text aus den einzelnen eingebetteten Bildern. 

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

Das Feld „merged_text“ kann in der Indexerdefinition als durchsuchbares Feld zugeordnet werden. Der gesamte Inhalt Ihrer Dateien – einschließlich des Texts der Bilder – wird somit durchsuchbar.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualisieren von umgebenden Rechtecken für extrahierten Text

Ein weiteres gängiges Szenario ist die Visualisierung der Layoutinformationen von Suchergebnissen. So können Sie beispielsweise in Ihren Suchergebnissen den Ort in einem Bild hervorheben, an dem ein Textelement gefunden wurde.

Da der OCR-Schritt auf der Grundlage der normalisierten Bilder erfolgt, befinden sich die Layoutkoordinaten im normalisierten Bildraum. Bei der Anzeige des normalisierten Bilds ist das Vorhandensein von Koordinaten in der Regel kein Problem. In bestimmten Situationen möchten Sie jedoch möglicherweise das Originalbild anzeigen. Konvertieren Sie in diesem Fall die einzelnen Koordinatenpunkte aus dem Layout in das Koordinatensystem des Originalbilds. 

Um die normalisierten Koordinaten auf den ursprünglichen Koordinatenraum zu übertragen, können Sie den folgenden Algorithmus verwenden:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Weitere Informationen
+ [Create Indexer (Azure Search Service REST api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) (Erstellen eines Indexers (REST-API für den Azure Search-Dienst: Version 2017-11-11-Preview))
+ [Image Analysis cognitive skill](cognitive-search-skill-image-analysis.md) (Kognitive Qualifikation: Bildanalyse)
+ [OCR cognitive skill](cognitive-search-skill-ocr.md) (Kognitive Qualifikation: OCR)
+ [Text Merge cognitive skill](cognitive-search-skill-textmerger.md) (Kognitive Qualifikation: Textzusammenführung)
+ [How to create a skillset in an enrichment pipeline](cognitive-search-defining-skillset.md) (Erstellen einer Qualifikationsgruppe in einer Anreicherungspipeline)
+ [How to map enriched fields to a searchable index](cognitive-search-output-field-mapping.md) (Zuordnen angereicherter Felder zu einem durchsuchbaren Index)
