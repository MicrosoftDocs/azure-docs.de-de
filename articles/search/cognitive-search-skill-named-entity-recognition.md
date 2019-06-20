---
title: Die Qualifikation „Benannte Entität erkennen“ der kognitiven Suche – Azure Search
description: Extrahieren Sie benannte Entitäten für Personen, Orte und Organisationen aus Text in einer Pipeline der kognitiven Suche in Azure Search.
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
ms.openlocfilehash: b7af4d0a48f002f7523def971a306d1fa2077c70
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952034"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Der kognitive Skill „Benannte Entität erkennen“

Der Skill **Benannte Entitäten erkennen** extrahiert benannte Entitäten aus Text. Zu den verfügbaren Entitäten gehören die Typen `person`, `location` und `organization`.

> [!IMPORTANT]
> Die Qualifikation zur Erkennung benannter Entitäten wurde eingestellt und durch [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) ersetzt. Die Unterstützung endete am 15. Februar 2019, und die API wurde am 2. Mai 2019 aus dem Produkt entfernt. Führen Sie unter Berücksichtigung der Empfehlungen unter [Veraltete Qualifikationen für die kognitive Suche](cognitive-search-skill-deprecated.md) eine Migration zu einer unterstützten Qualifikation durch.

> [!NOTE]
> Wenn Sie den Umfang erweitern, indem Sie die Verarbeitungsfrequenz erhöhen oder weitere Dokumente oder KI-Algorithmen hinzufügen, müssen Sie [eine kostenpflichtige Cognitive Services-Ressource anfügen](cognitive-search-attach-cognitive-services.md). Gebühren fallen beim Aufrufen von APIs in Cognitive Services sowie für die Bildextraktion im Rahmen der Dokumentaufschlüsselungsphase in Azure Search an. Für die Textextraktion aus Dokumenten fallen keine Gebühren an.
>
> Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion werden auf der [Preisseite von Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) beschrieben.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Datengrenzwerte
Die maximale Größe eines Datensatzes ist 50.000 Zeichen, gemessen durch `String.Length`. Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an die Schlüsselbegriffserkennung senden, denken Sie daran, den [Skill „Text teilen“](cognitive-search-skill-textsplit.md) zu verwenden.

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| categories    | Array von zu extrahierenden Kategorien.  Mögliche Kategorietypen: `"Person"`, `"Location"`, `"Organization"`. Wenn keine Kategorie angegeben ist, werden alle Typen zurückgegeben.|
|defaultLanguageCode |  Sprachcode des Eingabetexts. Die folgenden Sprachen werden unterstützt: `de, en, es, fr, it`|
| minimumPrecision  | Eine Zahl zwischen 0 und 1. Wenn die Genauigkeit unter diesem Wert liegt, wird die Entität nicht zurückgegeben. Der Standardwert ist 0.|

## <a name="skill-inputs"></a>Skilleingaben

| Eingabename      | BESCHREIBUNG                   |
|---------------|-------------------------------|
| languageCode  | Optional. Der Standardwert ist `"en"`.  |
| text          | Der zu analysierende Text          |

## <a name="skill-outputs"></a>Skillausgaben

| Ausgabename     | BESCHREIBUNG                   |
|---------------|-------------------------------|
| persons      | Ein Array von Zeichenfolgen, wobei jede Zeichenfolge den Namen einer Person darstellt. |
| locations  | Ein Array von Zeichenfolgen, wobei jede Zeichenfolge einen Ort darstellt. |
| organizations  | Ein Array von Zeichenfolgen, wobei jede Zeichenfolge eine Organisation darstellt. |
| entities | Ein Array komplexer Typen. Jeder komplexe Typ umfasst die folgenden Felder: <ul><li>category (`"person"`, `"organization"` oder `"location"`)</li> <li>value (der tatsächliche Entitätsname)</li><li>offset (die Fundstelle im Text)</li><li>confidence (Ein Wert zwischen 0 und 1, der das Vertrauen darstellt, dass der Wert eine tatsächliche Entität ist.)</li></ul> |

##  <a name="sample-definition"></a>Beispieldefinition

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
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
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
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
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Auftretende Fehler
Wird der Sprachcode für das Dokument nicht unterstützt, wird ein Fehler zurückgegeben, und es werden keine Entitäten extrahiert.

## <a name="see-also"></a>Weitere Informationen

+ [Vordefinierte Skills](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Die kognitive Qualifikation „Entitätserkennung“](cognitive-search-skill-entity-recognition.md)
