---
title: Sprachenerkennung mit der Textanalyse-REST-API | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe der Textanalyse-REST-API von Azure Cognitive Services Sprachen erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: 481f7a7589a58baac922001d230f95198ed45eb7
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417945"
---
# <a name="example-how-to-detect-language-with-text-analytics"></a>Beispiel: Sprachenerkennung mithilfe der Textanalyse

Das Feature für die [Sprachenerkennung](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) der API wertet Texteingaben aus und gibt für jedes Dokument Sprachen-IDs mit einer Punktzahl zurück, die die Stärke der Analyse angibt.

Diese Funktion ist hilfreich für Inhaltsspeicher, die willkürliche Texte mit unbekannter Sprache sammeln. Sie können die Analyseergebnisse analysieren, um die Sprache des Eingabedokuments zu bestimmen. Die Antwort gibt außerdem eine Punktzahl zwischen 0 und 1 zurück, um die Zuverlässigkeit des Modells anzugeben.

Für dieses Feature wird keine genaue Liste mit Sprachen veröffentlicht. Es kann jedoch eine Vielzahl von Sprachen, Varianten und Dialekten sowie einige Regional- und Kultursprachen erkennen. 

Bei Inhalten in einer seltener verwendeten Sprache können Sie die Sprachenerkennung ausprobieren, um zu sehen, ob sie einen Code zurückgibt. Die Antwort bei Sprachen, die nicht erkannt werden können, lautet `unknown`.

> [!TIP]
> Die Textanalyse bietet darüber hinaus ein Linux-basiertes Docker-Containerimage für die Spracherkennung, damit Sie [den Textanalysecontainer nah bei Ihren Daten installieren und ausführen können](text-analytics-how-to-install-containers.md).

## <a name="preparation"></a>Vorbereitung

Sie benötigen JSON-Dokumente im folgenden Format: ID, Text

Die Dokumentgröße darf 5.120 Zeichen pro Dokument nicht übersteigen, und pro Sammlung sind bis zu 1.000 Elemente (IDs) zulässig. Die Sammlung wird im Hauptteil der Anforderung übermittelt. Das folgende Beispiel zeigt Inhalte, die Sie ggf. für die Sprachenerkennung übermitteln können:

   ```
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },                
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Schritt 1: Strukturieren der Anforderung

Details zur Anforderungsdefinition finden Sie unter [Aufrufen der Textanalyse-REST-API](text-analytics-how-to-call-api.md). Der Einfachheit halber sind hier noch einmal einige Punkte aufgeführt:

+ Erstellen Sie eine Anforderung vom Typ **POST**. Lesen Sie die API-Dokumentation für diese Anforderung: [Sprachenerkennungs-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)

+ Legen Sie den HTTP-Endpunkt für die Spracherkennung entweder mithilfe einer Textanalyseressource in Azure oder mithilfe eines instanziierten [Textanalysecontainers](text-analytics-how-to-install-containers.md) fest. Er muss die Ressource `/languages` enthalten: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`.

+ Legen Sie einen Anforderungsheader fest, der den Zugriffsschlüssel für Textanalysevorgänge enthält. Weitere Informationen finden Sie unter [Ermitteln von Endpunkten und Zugriffsschlüsseln](text-analytics-how-to-access-key.md).

+ Geben Sie im Anforderungstext die JSON-Dokumentsammlung an, die Sie für diese Analyse vorbereitet haben.

> [!Tip]
> Verwenden Sie [Postman](text-analytics-how-to-call-api.md), oder öffnen Sie die **API-Testkonsole** in der [Dokumentation](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7), um eine Anforderung zu strukturieren und mittels POST an den Dienst zu übermitteln.

## <a name="step-2-post-the-request"></a>Schritt 2: Übermitteln der Anforderung

Die Analyse erfolgt, wenn die Anforderung eingeht. Der Dienst akzeptiert bis zu 100 Anforderungen pro Minute. Jede Anforderung darf maximal 1 MB groß sein.

Vergessen Sie nicht, dass der Dienst zustandslos ist. In Ihrem Konto werden keine Daten gespeichert. Die Ergebnisse werden direkt in der Antwort zurückgegeben.


## <a name="step-3-view-results"></a>Schritt 3: Anzeigen der Ergebnisse

Alle POST-Anforderungen geben eine Antwort im JSON-Format mit den IDs und erkannten Eigenschaften zurück.

Die Ausgabe wird umgehend zurückgegeben. Sie können die Ergebnisse an eine Anwendung streamen, die JSON akzeptiert, oder die Ausgabe in einer Datei auf dem lokalen System speichern und sie anschließend in eine Anwendung importieren, in der Sie die Daten sortieren, durchsuchen und bearbeiten können.

Ergebnisse für die Beispielanforderung sollten wie im folgenden JSON-Code aussehen. Beachten Sie, dass es sich hierbei um ein einzelnes Dokument mit mehreren Elementen handelt. Die Ausgabe erfolgt in englischer Sprache. Sprachen-IDs enthalten einen Anzeigenamen und einen Sprachcode im Format [ISO 639-1](https://www.iso.org/standard/22109.html).

Das positive Ergebnis 1,0 steht für die höchstmögliche Zuverlässigkeit der Analyse.



```
{
    "documents": [
        {
            "id": "1",
            "detectedLanguages": [
                {
                    "name": "English",
                    "iso6391Name": "en",
                    "score": 1
                }
            ]
        },
        {
            "id": "2",
            "detectedLanguages": [
                {
                    "name": "Spanish",
                    "iso6391Name": "es",
                    "score": 1
                }
            ]
        },
        {
            "id": "3",
            "detectedLanguages": [
                {
                    "name": "French",
                    "iso6391Name": "fr",
                    "score": 1
                }
            ]
        },
        {
            "id": "4",
            "detectedLanguages": [
                {
                    "name": "Chinese_Simplified",
                    "iso6391Name": "zh_chs",
                    "score": 1
                }
            ]
        },
        {
            "id": "5",
            "detectedLanguages": [
                {
                    "name": "Russian",
                    "iso6391Name": "ru",
                    "score": 1
                }
            ]
        }
    ],
```

### <a name="ambiguous-content"></a>Mehrdeutige Inhalte

Falls das Analysetool die Eingabe nicht analysieren kann (beispielsweise im Falle eines Textblocks, der ausschließlich arabische Ziffern enthält), wird `(Unknown)` zurückgegeben.

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>Inhalte in verschiedenen Sprachen

Wenn in einem Dokument Inhalte in verschiedenen Sprachen enthalten sind, wird die Sprache zurückgegeben, die in den Inhalten überwiegt – allerdings mit einer niedrigeren positiven Bewertung, die die marginale Stärke dieser Bewertung widerspiegelt. Im folgenden Beispiel enthält die Eingabe eine Mischung aus Englisch, Spanisch und Französisch. Das Analysetool zählt die Zeichen der einzelnen Segmente, um die vorherrschende Sprache zu bestimmen.

**Input** (Eingabe)

```
{
  "documents": [
    {
      "id": "1",
      "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
    }
  ]
}
```

**Ausgabe**

Die resultierende Ausgabe enthält die vorherrschende Sprache mit einer Punktzahl von weniger als 1,0, um eine niedrigere Zuverlässigkeit anzugeben.

```
{
  "documents": [
    {
      "id": "1",
      "detectedLanguages": [
        {
          "name": "Spanish",
          "iso6391Name": "es",
          "score": 0.9375
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie sich mit Konzepten und mit dem Workflow für die Sprachenerkennung unter Verwendung der Textanalyse in Cognitive Services vertraut gemacht. Im Anschluss finden Sie eine kurze Zusammenfassung der wichtigsten Punkte:

+ Die [Sprachenerkennung](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) ist für eine Vielzahl von Sprachen, Varianten und Dialekten sowie einige Regional- und Kultursprachen verfügbar.
+ JSON-Dokumente im Anforderungstext umfassen eine ID und Text.
+ Die POST-Anforderung wird an einen Endpunkt vom Typ `/languages` gesendet. Dabei werden ein personalisierter [Zugriffsschlüssel und ein Endpunkt](text-analytics-how-to-access-key.md) verwendet, der für Ihr Abonnement gültig ist.
+ Bei der Antwortausgabe handelt es sich um Sprachen-IDs für die jeweilige Dokument-ID. Sie kann an eine beliebige JSON-fähige App gestreamt werden (beispielsweise an Excel oder Power BI).

## <a name="see-also"></a>Weitere Informationen 

 [Übersicht über die Textanalyse](../overview.md)  
 [Häufig gestellte Fragen (FAQ)](../text-analytics-resource-faq.md)</br>
 [Textanalysen (Produktseite)](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Analysieren von Stimmungen](text-analytics-how-to-sentiment-analysis.md)
