---
title: Die Qualifikation „Schlüsselbegriffserkennung“ der kognitiven Suche – Azure Search | Microsoft-Dokumentation
description: Bewertet unstrukturierten Text und gibt für jeden Datensatz eine Liste mit Schlüsselbegriffen in einer Azure Search-Anreicherungspipeline zurück.
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
ms.openlocfilehash: 1d221e3bcdfd781da79c73e8f228b9e449a7f5bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65021815"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Der kognitive Skill „Schlüsselbegriffserkennung“

Der Skill **Schlüsselbegriffserkennung** bewertet unstrukturierten Text und gibt für jeden Datensatz eine Liste mit Schlüsselbegriffen zurück. Bei dieser Qualifikation werden die Machine Learning-Modelle verwendet, die in Cognitive Services über die [Textanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) bereitgestellt werden.

Diese Funktion ist nützlich, wenn Sie die wichtigsten Gesprächspunkte in dem Datensatz schnell identifizieren müssen. Zum Beispiel, wenn der eingegebene Text „Das Essen war köstlich, und es gab hervorragendes Personal“ lautet, gibt der Dienst „Essen“ und „hervorragendes Personal“ zurück.

> [!NOTE]
> Wenn Sie den Umfang erweitern, indem Sie die Verarbeitungsfrequenz erhöhen oder weitere Dokumente oder KI-Algorithmen hinzufügen, müssen Sie [eine kostenpflichtige Cognitive Services-Ressource anfügen](cognitive-search-attach-cognitive-services.md). Gebühren fallen beim Aufrufen von APIs in Cognitive Services sowie für die Bildextraktion im Rahmen der Dokumentaufschlüsselungsphase in Azure Search an. Für die Textextraktion aus Dokumenten fallen keine Gebühren an.
>
> Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion werden auf der [Preisseite von Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) beschrieben.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Datengrenzwerte
Die maximale Größe eines Datensatzes ist 50.000 Zeichen, gemessen durch `String.Length`. Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an die Schlüsselbegriffserkennung senden, denken Sie daran, den [Skill „Text teilen“](cognitive-search-skill-textsplit.md) zu verwenden.

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Eingaben                | BESCHREIBUNG |
|---------------------|-------------|
| defaultLanguageCode | (Optional) Der Sprachcode, der auf Dokumente angewendet wird, in denen die Sprache nicht explizit angegeben ist.  Wenn kein Standardsprachcode festgelegt ist, wird Englisch (en) als Standardsprachcode verwendet. <br/> Siehe die [vollständige Liste der unterstützten Sprachen](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Optional) Die maximale Anzahl der Schlüsselbegriffe, die erzeugt werden. |

## <a name="skill-inputs"></a>Skilleingaben

| Eingaben     | BESCHREIBUNG |
|--------------------|-------------|
| text | Der zu analysierende Text|
| languageCode  |  Eine Zeichenfolge, die die Sprache der Datensätze angibt. Wenn dieser Parameter nicht angegeben ist, wird der Standardsprachcode zur Analyse der Datensätze verwendet. <br/>Siehe die [vollständige Liste der unterstützten Sprachen](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).|

##  <a name="sample-definition"></a>Beispieldefinition

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
           }
      }
    ]
```


##  <a name="sample-output"></a>Beispielausgabe

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Fehler und Warnungen
Wenn Sie einen nicht unterstützte Sprachcode bereitstellen, tritt ein Fehler auf, und es werden keine Schlüsselbegriffe extrahiert.
Wenn Ihr Text leer ist, wird eine Warnung angezeigt.
Wenn Ihre Text mehr als 50.000 Zeichen umfasst, werden nur die ersten 50.000 Zeichen analysiert und eine Warnung ausgegeben.

## <a name="see-also"></a>Weitere Informationen

+ [Vordefinierte Skills](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
