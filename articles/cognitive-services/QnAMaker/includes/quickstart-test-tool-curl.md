---
title: include file
description: include file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: cbabfc1ae0c54c4ef20d3c689506e486bf4b6a66
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386559"
---
In diesem Schnellstart auf cURL-Basis wird Schritt für Schritt erläutert, wie Sie eine Antwort aus einer Wissensdatenbank abrufen.

## <a name="prerequisites"></a>Voraussetzungen

* Neueste Version von [**cURL**](https://curl.haxx.se/).
* Sie benötigen Folgendes:
    * Einen [QnA Maker-Dienst](../How-To/set-up-qnamaker-service-azure.md).
    * Eine trainierte und veröffentlichte Wissensdatenbank mit Fragen und Antworten, die in der [vorherigen Schnellstartanleitung](../Quickstarts/add-question-metadata-portal.md) erstellt und mit Metadaten und Smalltalk konfiguriert wurde.

> [!NOTE]
> Wenn Sie bereit sind, eine Antwort auf eine Frage aus Ihrer Wissensdatenbank zu generieren, müssen Sie Ihre Wissensdatenbank [trainieren](../Quickstarts/create-publish-knowledge-base.md#save-and-train) und [veröffentlichen](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base). Wenn Ihre Wissensdatenbank veröffentlicht wird, werden auf der Seite **Veröffentlichen** die HTTP-Anforderungseinstellungen zum Generieren einer Antwort angezeigt. Die Registerkarte **cURL** enthält die Einstellungen, die zum Generieren einer Antwort über das Befehlszeilentool erforderlich sind.

## <a name="use-metadata-to-filter-answer"></a>Filtern von Antworten mithilfe von Metadaten

Verwenden Sie die Wissensdatenbank aus der vorherigen Schnellstartanleitung, um eine Antwort auf der Grundlage von Metadaten abzufragen.

1. Wählen Sie auf der Seite **Einstellungen** der Wissensdatenbank die Registerkarte **cURL** aus, um einen cURL-Beispielbefehl anzuzeigen, mit dem eine Antwort auf der Grundlage der Wissensdatenbank generiert wird.
1. Kopieren Sie den Befehl in eine Bearbeitungsumgebung (beispielsweise eine Textdatei), um ihn zu bearbeiten. Bearbeiten Sie den Fragewert so, dass die Metadaten von `service:qna_maker` als Filter für die Frage-Antwort-Paare verwendet werden.

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)
    
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H   "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```
    ---

    Die Frage ist nur ein einzelnes Wort (`size`), wodurch jedes der beiden Frage-Antwort-Paare zurückgegeben werden kann. Das Array `strictFilters` gibt die Antwort aus und reduziert sie ausschließlich auf die `qna_maker`-Antworten.

1. Die Ausgabe enthält nur die Antwort, die den Filterkriterien entspricht. Die folgende cURL-Antwort wurde aus Gründen der Lesbarkeit formatiert:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Wenn ein Frage-Antwort-Paar nicht mit dem Suchbegriff, aber mit dem Filter übereinstimmt, wird es nicht zurückgegeben. Stattdessen wird die allgemeine Antwort `No good match found in KB.` zurückgegeben.

## <a name="use-debug-query-property"></a>Verwenden der Abfrageeigenschaft „debug“
> [!NOTE]
>Es wird nicht empfohlen, die debug-Eigenschaft für Abhängigkeiten zu verwenden. Diese Eigenschaft wurde hinzugefügt, um dem Produktteam bei der Problembehandlung zu helfen.

Anhand von Debuginformationen können Sie besser nachvollziehen, wie die zurückgegebene Antwort ermittelt wurde. Dies ist zwar hilfreich, aber nicht unbedingt erforderlich. Wenn Sie eine Antwort mit Debuginformationen generieren möchten, fügen Sie die Eigenschaft `debug` hinzu:

```json
Debug: {Enable:true}
```

1. Bearbeiten Sie den cURL-Befehl, um die debug-Eigenschaft einzuschließen und weitere Informationen anzuzeigen.

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```
    ---

1. Die Antwort enthält die relevanten Informationen über die Antwort. In der folgenden JSON-Ausgabe wurden einige Debugdetails durch Auslassungspunkte ersetzt, um die Ausgabe zu verkürzen.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) to learn how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Verwenden der Testversion der Wissensdatenbank

Wenn Sie eine Antwort von der Testversion der Wissensdatenbank erhalten möchten, verwenden Sie die Texteigenschaft `isTest`.

Die Eigenschaft ist ein boolescher Wert.

```json
isTest:true
```

Der cURL-Befehl sieht wie folgt aus:
# <a name="qna-maker-ga"></a>[QnA Maker, allgemeine Verfügbarkeit](#tab/v1)
```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```
# <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)
```bash
curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

---
In der JSON-Antwort wird das gleiche Schema verwendet wie in der Abfrage der veröffentlichten Wissensdatenbank.

> [!NOTE]
> Da der Testindex von allen Wissensdatenbanken in der Ressource genutzt wird, kann es zu geringfügigen Abweichungen kommen, selbst wenn die Testversion und die veröffentlichte Version der Wissensdatenbank exakt gleich sind.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Abfragen einer Smalltalk-Antwort mithilfe von cURL

1. Verwenden Sie im cURL-fähigen Terminal eine Benutzeraussage zur Beendigung der Bot-Konversation (etwa `Thank you` als Frage). Es müssen keine weiteren Eigenschaften festgelegt werden.
    
   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```
    ---

1. Führen Sie den cURL-Befehl aus, um die JSON-Antwort mit der Bewertung und der Antwort auf die Frage zu erhalten.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Da die Frage `Thank you` exakt einer Smalltalk-Frage entspricht, ist QnA Maker zu 100 Prozent überzeugt. QnA Maker hat auch alle dazugehörigen Fragen sowie die Metadateneigenschaft mit den Informationen zum Metadatentag für Smalltalk zurückgegeben.

## <a name="use-threshold-and-default-answer"></a>Verwenden von Schwellenwert und Standardantwort

Sie können einen Mindestschwellenwert für die Antwort anfordern. Sollte der Schwellenwert nicht erreicht werden, wird die Standardantwort zurückgegeben.

1. Fügen Sie die Eigenschaft `threshold` hinzu, um für `size` eine Antwort mit einem Schwellenwert von mindestens 80 Prozent anzufordern. Diese Antwort sollte von der Wissensdatenbank nicht gefunden werden, da die Frage mit 71 Prozent bewertet wurde. Stattdessen wird die Standardantwort zurückgeben, die Sie beim Erstellen der Wissensdatenbank angegeben haben.

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```
    ---

1. Führen Sie den cURL-Befehl aus, um die JSON-Antwort zu erhalten.

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker hat als Bewertung den Wert `0` (nicht überzeugt) zurückgegeben. Darüber hinaus wurde die Standardantwort zurückgegeben.

1. Ändern Sie den Schwellenwert in 60 Prozent, und wiederholen Sie die Abfrage:
    
   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Mit QnA Maker verwaltet (Vorschauversion)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```
    ---

    Die Antwort wurde vom zurückgegebenen JSON-Code gefunden.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```