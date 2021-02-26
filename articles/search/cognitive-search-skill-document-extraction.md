---
title: Kognitive Qualifikation „Dokumentextrahierung“ (Vorschau)
titleSuffix: Azure Cognitive Search
description: Extrahiert Inhalt aus einer Datei innerhalb der Anreicherungspipeline. Diese Qualifikation ist zurzeit als öffentliche Vorschauversion verfügbar.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: chalton
ms.openlocfilehash: d6dd2b891cb3bf9ebb5442f541021c20a34ce1a1
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99474845"
---
# <a name="document-extraction-cognitive-skill"></a>Kognitive Qualifikation „Dokumentextrahierung“

> [!IMPORTANT] 
> Diese Qualifikation ist zurzeit als öffentliche Vorschauversion verfügbar. Die Vorschaufunktion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Derzeit werden weder das Portal noch das .NET SDK unterstützt.

Die Qualifikation **Dokumentextrahierung** extrahiert Inhalt aus einer Datei innerhalb der Anreicherungspipeline. Auf diese Weise können Sie die Vorteile des Schritts der Dokumentextrahierung nutzen, der normalerweise vor der Ausführung des Skillsets mit Dateien erfolgt, die möglicherweise durch andere Qualifikationen generiert werden.

> [!NOTE]
> Wenn Sie den Umfang erweitern, indem Sie die Verarbeitungsfrequenz erhöhen oder weitere Dokumente oder KI-Algorithmen hinzufügen, müssen Sie [eine kostenpflichtige Cognitive Services-Ressource anfügen](cognitive-search-attach-cognitive-services.md). Gebühren fallen beim Aufrufen von APIs in Cognitive Services sowie für die Bildextraktion im Rahmen der Dokumentaufschlüsselungsphase bei der Indizierung an. Für die Textextraktion aus Dokumenten fallen keine Gebühren an.
>
> Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion werden auf der [Preisseite](https://azure.microsoft.com/pricing/details/search/) beschrieben.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Eingaben | Zulässige Werte | BESCHREIBUNG |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Legen Sie diesen Parameter auf `default` fest, um die Dokumentextrahierung aus Dateien durchzuführen, die keine reinen Text- oder JSON-Dateien sind. Legen Sie diesen Parameter auf `text` fest, um die Leistung von Nur-Text-Dateien zu verbessern. Legen Sie diesen Parameter auf `json` fest, um strukturierte Inhalte aus JSON-Dateien zu extrahieren. Wenn der Parameter `parsingMode` nicht explizit definiert ist, wird er auf `default` festgelegt. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Legen Sie diesen Parameter auf `contentAndMetadata` fest, um aus jeder Datei alle Metadaten und Textinhalte zu extrahieren. Legen Sie diesen Parameter auf `allMetadata` fest, um nur die [für den Inhaltstyp spezifischen Metadaten](search-blob-metadata-properties.md) zu extrahieren (z. B. Metadaten, die nur für PNG-Dateien eindeutig sind). Wenn der Parameter `dataToExtract` nicht explizit definiert ist, wird er auf `contentAndMetadata` festgelegt. |
| `configuration` | Siehe unten. | Ein Wörterbuch mit optionalen Parametern zur Anpassung der Durchführung der Dokumentextrahierung. In der folgenden Tabelle finden Sie Beschreibungen der unterstützten Konfigurationseigenschaften. |

| Konfigurationsparameter   | Zulässige Werte | BESCHREIBUNG |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Legen Sie diesen Parameter auf `none` fest, um eingebettete Bilder oder Bilddateien im Dataset zu ignorieren. Dies ist die Standardoption. <br/>Legen Sie diesen Parameter für die [Bildanalyse mithilfe von kognitiven Qualifikationen](cognitive-search-concept-image-scenarios.md) auf `generateNormalizedImages` fest, damit die Qualifikation bei der Dokumententschlüsselung ein Array von normalisierten Bildern erstellt. Für diese Aktion ist es erforderlich, `parsingMode` auf `default` und `dataToExtract` auf `contentAndMetadata` festzulegen. Ein normalisiertes Bild bezieht sich auf eine zusätzliche Verarbeitung, die zu einer einheitlichen Bildausgabe führt. Für die Ausgabe wird die Größe angepasst, und sie wird gedreht, um das einheitliche Rendern zu fördern, wenn Sie Bilder in visuelle Suchergebnisse einbinden (z.B. Fotos gleicher Größe für ein Graphsteuerelement wie in der [JFK-Demo](https://github.com/Microsoft/AzureSearch_JFK_Files)). Diese Informationen werden bei Verwendung dieser Option für jedes Bild generiert.  <br/>Wenn Sie diesen Parameter auf `generateNormalizedImagePerPage` festlegen, werden PDF-Dateien anders behandelt. Anstatt eingebettete Bilder zu extrahieren, wird jede Seite als Bild gerendert und entsprechend normalisiert.  Nicht-PDF-Dateitypen werden genauso behandelt, als ob der Parameter auf `generateNormalizedImages` festgelegt worden wäre.
| `normalizedImageMaxWidth` | Eine beliebige ganze Zahl zwischen 50-10000 | Die maximale Breite (in Pixel) für generierte normalisierte Bilder. Der Standardwert ist „2000“. | 
| `normalizedImageMaxHeight` | Eine beliebige ganze Zahl zwischen 50-10000 | Die maximale Höhe (in Pixel) für generierte normalisierte Bilder. Der Standardwert ist „2000“. |

> [!NOTE]
> Der Standardwert von 2.000 Pixeln für die maximale Breite und Höhe der normalisierten Bilder basiert auf der maximal unterstützten Größe der [OCR-Qualifikation](cognitive-search-skill-ocr.md) und der [Bildanalysequalifikation](cognitive-search-skill-image-analysis.md). Die [OCR-Qualifikation](cognitive-search-skill-ocr.md) unterstützt eine maximale Breite und Höhe von 4.200 für nicht englische Sprachen und 10.000 für Englisch.  Wenn Sie die maximalen Grenzwerte erhöhen, können bei größeren Images je nach Skillsetdefinition und Sprache der Dokumente Fehler bei der Verarbeitung auftreten. 
## <a name="skill-inputs"></a>Skilleingaben

| Eingabename     | BESCHREIBUNG |
|--------------------|-------------|
| `file_data` | Die Datei, aus der Inhalt extrahiert werden soll. |

Die Eingabe „file_data“ muss ein Objekt sein, das wie folgt definiert wurde:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Dieses Dateiverweisobjekt kann auf eine von drei Arten generiert werden:

 - Indem Sie den `allowSkillsetToReadFileData`-Parameters in Ihrer Indexerdefinition auf „true“ festlegen.  Dadurch wird der Pfad `/document/file_data` erstellt, bei dem es sich um ein Objekt handelt, das die aus der Blobdatenquelle heruntergeladenen ursprünglichen Dateidaten darstellt. Dieser Parameter gilt nur für Daten in Blobspeicher.

 - Indem Sie den `imageAction`-Parameters in Ihrer Indexerdefinition auf einen anderen Wert als `none` festlegen.  Dadurch wird ein Array von Bildern erstellt, die die erforderliche Konvention für die Eingaben für diese Qualifikation erfüllen, wenn sie einzeln übermittelt werden (d. h. `/document/normalized_images/*`).

 - Indem Sie eine benutzerdefinierte Qualifikation ein JSON-Objekt zurückgeben lassen, das EXAKT wie oben definiert ist.  Der `$type`-Parameter muss exakt auf `file` festgelegt werden, und der `data`-Parameter muss die Per Base-64 verschlüsselte Bytearraydaten des Dateiinhalts enthalten.

## <a name="skill-outputs"></a>Skillausgaben

| Ausgabename    | BESCHREIBUNG |
|--------------|-------------|
| `content` | Der Textinhalt des Dokuments. |
| `normalized_images`   | Wenn `imageAction` auf einen anderen Wert als `none` festgelegt wird, enthält das neue Feld *normalized_images* ein Array von Bildern. Weitere Informationen zum Ausgabeformat der einzelnen Bilder finden Sie in der [Dokumentation für die Bildextraktion](cognitive-search-concept-image-scenarios.md). |

##  <a name="sample-definition"></a>Beispieldefinition

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Beispieleingabe

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Beispielausgabe

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Weitere Informationen

+ [Integrierte Qualifikationen](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Verarbeiten und Extrahieren von Text aus Bildern in kognitiven Suchszenarien](cognitive-search-concept-image-scenarios.md)