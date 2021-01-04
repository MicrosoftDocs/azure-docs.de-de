---
title: Schlüsselbegriffsextraktion mit der Textanalyse-REST-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mithilfe der Textanalyse-REST-API von Azure Cognitive Services Schlüsselbegriffe extrahieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/15/2020
ms.author: aahi
ms.openlocfilehash: e5d25e71e4700f3f327319e4f444d2060c7ab5f6
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561884"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Beispiel: Erkennen von Schlüsselbegriffen mithilfe der Textanalyse

Die [Schlüsselbegriffserkennungs-API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) bewertet unstrukturierten Text und gibt für jedes JSON-Dokument eine Liste mit Schlüsselbegriffen zurück.

Diese Funktion ist nützlich, wenn Sie die wichtigsten Punkte in einer Sammlung von Dokumenten schnell identifizieren müssen. Wenn der eingegebene Text beispielsweise „Das Essen war köstlich, und es gab hervorragendes Personal“ lautet, gibt der Dienst die Kernpunkte „Essen“ und „hervorragendes Personal“ zurück.

Weitere Informationen finden Sie unter [Unterstützte Sprachen](../language-support.md).

> [!TIP]
> * Die Textanalyse bietet darüber hinaus ein Linux-basiertes Docker-Containerimage für die Schlüsselbegriffserkennung, damit Sie [den Textanalysecontainer nah bei Ihren Daten installieren und ausführen können](text-analytics-how-to-install-containers.md).
> * Sie können dieses Feature auch [asynchron](text-analytics-how-to-call-api.md) mithilfe des Endpunkts vom Typ `/analyze` verwenden.

## <a name="preparation"></a>Vorbereitung

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

Die Schlüsselbegriffserkennung funktioniert am besten, wenn Sie ihr größere Texte zur Verarbeitung übergeben. Dies steht im Gegensatz zur Standpunktanalyse, die mit kleineren Texten besser funktioniert. Um für beide Vorgänge optimale Ergebnisse zu erzielen, empfiehlt es sich ggf., die Eingaben entsprechend umzustrukturieren.

Sie benötigen JSON-Dokumente im folgenden Format: ID, Text, Sprache.

Die Dokumentgröße darf 5.120 Zeichen pro Dokument nicht übersteigen, und pro Sammlung sind bis zu 1.000 Elemente (IDs) zulässig. Die Sammlung wird im Hauptteil der Anforderung übermittelt. Das folgende Beispiel ist eine Abbildung von Inhalten, die Sie zur Schlüsselbegriffserkennung übermitteln könnten. 

Unter [Aufrufen der Textanalyse-REST-API](text-analytics-how-to-call-api.md) erhalten Sie weitere Informationen zu Anforderungen und Antwortobjekten.  

### <a name="example-synchronous-request-object"></a>Beispiel eines synchronen Anforderungsobjekts


```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

### <a name="example-asynchronous-request-object"></a>Beispiel eines asynchronen Anforderungsobjekts

Ab `v3.1-preview.3` können Sie NER-Anforderungen mithilfe des `/analyze`-Endpunkts asynchron senden.


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>Schritt 1: Strukturieren der Anforderung

Weitere Informationen zur Anforderungsdefinition finden Sie unter [Aufrufen der Textanalyse-API](text-analytics-how-to-call-api.md). Der Einfachheit halber sind hier noch einmal einige Punkte aufgeführt:

+ Erstellen Sie eine Anforderung vom Typ **POST**. Lesen Sie die API-Dokumentation für diese Anforderung: [Schlüsselbegriffs-API:](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases)

+ Legen Sie den HTTP-Endpunkt für die Schlüsselbegriffsextraktion entweder mithilfe einer Textanalyseressource in Azure oder mithilfe eines instanziierten [Textanalysecontainers](text-analytics-how-to-install-containers.md) fest. Wenn Sie die API synchron verwenden, müssen Sie `/text/analytics/v3.0/keyPhrases` in der URL einschließen. Beispiel: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Legen Sie einen Anforderungsheader fest, der den [Zugriffsschlüssel](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) für Textanalysevorgänge enthält.

+ Geben Sie im Anforderungstext die JSON-Dokumentsammlung an, die Sie für diese Analyse vorbereitet haben.

> [!Tip]
> Verwenden Sie [Postman](text-analytics-how-to-call-api.md), oder öffnen Sie die **API-Testkonsole** in der [Dokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases), um eine Anforderung zu strukturieren und mittels POST an den Dienst zu übermitteln.

## <a name="step-2-post-the-request"></a>Schritt 2: Übermitteln der Anforderung

Die Analyse erfolgt, wenn die Anforderung eingeht. Informationen zur Größe und Anzahl von Anforderungen, die Sie pro Minute und Sekunde senden können, finden Sie in der Übersicht im Abschnitt [Datengrenzwerte](../overview.md#data-limits).

Vergessen Sie nicht, dass der Dienst zustandslos ist. In Ihrem Konto werden keine Daten gespeichert. Die Ergebnisse werden direkt in der Antwort zurückgegeben.

## <a name="step-3-view-results"></a>Schritt 3: Anzeigen der Ergebnisse

Alle POST-Anforderungen geben eine Antwort im JSON-Format mit den IDs und erkannten Eigenschaften zurück. Die Reihenfolge der zurückgegebenen Schlüsselbegriffe wird vom Modell intern bestimmt.

Die Ausgabe wird umgehend zurückgegeben. Sie können die Ergebnisse an eine Anwendung streamen, die JSON akzeptiert, oder die Ausgabe in einer Datei auf dem lokalen System speichern und sie anschließend in eine Anwendung importieren, in der Sie die Daten sortieren, durchsuchen und bearbeiten können.

Ein Beispiel für die Ausgabe zur Schlüsselbegriffserkennung vom Endpunkt „v3.1-preview.2“ wird hier gezeigt:

### <a name="synchronous-result"></a>Synchrone Ergebnisse

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
Wie bereits erwähnt, sucht das Analysetool nach unbedeutenden Wörter, verwirft sie und behält einzelne Begriffe oder Ausdrücke bei, die offenbar Subjekt oder Objekt eines Satzes sind.

### <a name="asynchronous-result"></a>Asynchrone Ergebnisse

Wenn Sie den `/analyze`-Endpunkt für asynchrone Vorgänge verwenden, erhalten Sie eine Antwort, die die Aufgaben enthält, die Sie an die API gesendet haben.

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie sich mit Konzepten und dem Workflow für die Extraktion von Schlüsselbegriffen unter Verwendung der Textanalyse in Cognitive Services vertraut gemacht. Zusammenfassung:

+ Die [Schlüsselbegriffserkennungs-API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) ist für ausgewählte Sprachen verfügbar.
+ JSON-Dokumente im Anforderungstext umfassen eine ID, Text und einen Sprachcode.
+ Die POST-Anforderung wird an einen Endpunkt vom Typ `/keyphrases` oder `/analyze` gesendet. Dabei werden ein personalisierter [Zugriffsschlüssel und ein Endpunkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) verwendet, der für Ihr Abonnement gültig ist.
+ Bei der Antwortausgabe handelt es sich um Schlüsselwörter und -begriffe für die jeweilige Dokument-ID. Sie kann an eine beliebige JSON-fähige App gestreamt werden – beispielsweise an Microsoft Office Excel oder Power BI.

## <a name="see-also"></a>Weitere Informationen

 [Übersicht über die Textanalyse](../overview.md) [Häufig gestellte Fragen (FAQ)](../text-analytics-resource-faq.md)</br>
 [Textanalysen (Produktseite)](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die Textanalyse](../overview.md)
* [Verwenden der Textanalyse-Clientbibliothek](../quickstarts/client-libraries-rest-api.md)
* [Neuigkeiten](../whats-new.md)
