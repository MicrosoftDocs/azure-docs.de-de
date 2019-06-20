---
title: Die Qualifikation „Stimmung“ der kognitiven Suche – Azure Search
description: Extrahieren Sie einen Positiv-Negativ-Stimmungswert aus Text in einer Azure Search-Anreicherungspipeline.
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
ms.openlocfilehash: f52f5200f33d11db44d94b5a5f26d246f711e224
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023802"
---
#   <a name="sentiment-cognitive-skill"></a>Der kognitive Skill „Stimmung“

Der Skill **Stimmung** bewertet unstrukturierten Text anhand eines Positiv-Negativ-Kontinuums und gibt für jeden Datensatz einen numerischen Wert zwischen 0 und 1 zurück. Dabei weisen Werte nahe 1 auf eine positive Stimmung und Werte nahe 0 auf eine negative Stimmung hin. Bei dieser Qualifikation werden die Machine Learning-Modelle verwendet, die in Cognitive Services über die [Textanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) bereitgestellt werden.

> [!NOTE]
> Wenn Sie den Umfang erweitern, indem Sie die Verarbeitungsfrequenz erhöhen oder weitere Dokumente oder KI-Algorithmen hinzufügen, müssen Sie [eine kostenpflichtige Cognitive Services-Ressource anfügen](cognitive-search-attach-cognitive-services.md). Gebühren fallen beim Aufrufen von APIs in Cognitive Services sowie für die Bildextraktion im Rahmen der Dokumentaufschlüsselungsphase in Azure Search an. Für die Textextraktion aus Dokumenten fallen keine Gebühren an.
>
> Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion werden auf der [Preisseite von Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) beschrieben.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Datengrenzwerte
Die maximale Größe eines Datensatzes sollte 5000 Zeichen betragen, wie mit `String.Length` gemessen. Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an das Stimmungsanalysetool senden, verwenden Sie den [Skill „Text teilen“](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername |                      |
|----------------|----------------------|
| defaultLanguageCode | (Optional) Der Sprachcode, der für Dokumente gilt, die nicht explizit eine Sprache angeben. <br/> Siehe die [vollständige Liste der unterstützten Sprachen](../cognitive-services/text-analytics/text-analytics-supported-languages.md). |

## <a name="skill-inputs"></a>Skilleingaben 

| Eingabename | BESCHREIBUNG |
|--------------------|-------------|
| text | Der zu analysierende Text|
| languageCode  |  (Optional) Eine Zeichenfolge, die die Sprache der Datensätze angibt. Wenn dieser Parameter nicht festgelegt ist, wird der Standardwert „en“ verwendet. <br/>Siehe die [vollständige Liste der unterstützten Sprachen](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Skillausgaben

| Ausgabename | BESCHREIBUNG |
|--------------------|-------------|
| Ergebnis Ihrer App | Ein Wert zwischen 0 und 1, der die Stimmung des analysierten Textes repräsentiert. Werte nahe 0 stehen für eine negative, nahe 0,5 für eine neutrale und nahe 1 für eine positive Stimmung.|


##  <a name="sample-definition"></a>Beispieldefinition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
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
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Notizen
Wenn der Wert leer ist, wird für diese Datensätze kein Stimmungswert zurückgegeben.

## <a name="error-cases"></a>Auftretende Fehler
Wenn eine Sprache nicht unterstützt wird, wird eine Fehlermeldung generiert und kein Stimmungswert zurückgegeben.

## <a name="see-also"></a>Weitere Informationen

+ [Vordefinierte Skills](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
