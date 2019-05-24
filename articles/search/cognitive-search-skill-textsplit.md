---
title: Die Qualifikation „Text teilen“ der kognitiven Suche – Azure Search
description: Teilen Sie Text in mehrere Blöcke oder Seiten anhand der Länge in einer Azure Search-Anreicherungspipeline auf.
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
ms.openlocfilehash: c7f5fda405ca0e5ba9cf1dd0ed44c47cd3ee74b1
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949862"
---
#   <a name="text-split-cognitive-skill"></a>Der Skill „Text teilen“

Der Skill **Text teilen** unterteilt den Text in Blöcke. Sie können festlegen, ob Sie den Text in Sätze oder in Seiten einer bestimmten Länge aufteilen möchten. Dieser Skill ist besonders nützlich, wenn Sie eine maximale Textlänge in nachfolgenden Skills einhalten müssen. 

> [!NOTE]
> Dieser Skill ist nicht an eine Cognitive Services-API gebunden, und Ihnen entstehen für seine Nutzung keine Kosten. Es wird jedoch empfohlen, dennoch eine [Cognitive Services-Ressource anzufügen](cognitive-search-attach-cognitive-services.md), um die Ressourcenoption **Free** außer Kraft zu setzen, durch die Sie auf eine geringe Anzahl von Anreicherungen pro Tag beschränkt werden.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| textSplitMode      | Entweder „Seiten“ oder „Sätze“ | 
| maximumPageLength | Wenn „textSplitMode“ auf „Seiten“ gesetzt ist, bezieht sich dies auf die maximale Seitenlänge, gemessen durch `String.Length`. Der Mindestwert ist 100.  Wenn textSplitMode auf „pages“ festgelegt ist, versucht der Algorithmus, den Text in Blöcke aufzuteilen, deren Größe höchstens „maximumPageLenth“ beträgt. Dabei versucht der Algorithmus, Sätze an Satzgrenzen zu teilen, sodass die Größe der Blöcke etwas kleiner als „maximumPageLength“ sein kann. | 
| defaultLanguageCode   | (Optional) Einer der folgenden Sprachcodes: `da, de, en, es, fi, fr, it, ko, pt`. Die Standardsprache ist Englisch (en). Zu beachtende Aspekte:<ul><li>Wenn Sie ein Sprachcode-Ländercode-Format übergeben, wird nur der Sprachcodeteil des Formats verwendet.</li><li>Ist die Sprache nicht in der obigen Liste aufgeführt, wird der Text durch der Skill „Text teilen“ an Zeichengrenzen getrennt.</li><li>Die Angabe eines Sprachcodes ist sinnvoll, um bei Sprachen ohne Leerzeichen wie Chinesisch, Japanisch oder Koreanisch zu vermeiden, dass ein Wort in der Mitte getrennt wird.</li></ul>  |


## <a name="skill-inputs"></a>Skilleingaben

| Parametername       | BESCHREIBUNG      |
|----------------------|------------------|
| text  | Der Text, der in Teilzeichenfolgen aufgeteilt werden soll. |
| languageCode  | (Optional) Der Sprachcode für das Dokument.  |

## <a name="skill-outputs"></a>Skillausgaben 

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| textItems | Ein Array von Teilzeichenfolgen, die extrahiert wurden. |


##  <a name="sample-definition"></a>Beispieldefinition

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
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
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
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
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Auftretende Fehler
Wird eine Sprache nicht unterstützt, wird eine Warnung generiert und der Text an den Zeichengrenzen geteilt.

## <a name="see-also"></a>Weitere Informationen

+ [Vordefinierte Skills](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
