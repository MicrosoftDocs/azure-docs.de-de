---
title: Überprüfen der Containerinstanz für die Sprachenerkennung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Containerinstanz für die Sprachenerkennung überprüfen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 543a4d85982adadc86435819679351c8ffaa9814
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009896"
---
### <a name="verify-the-language-detection-container-instance"></a>Überprüfen der Containerinstanz für die Sprachenerkennung

1. Wählen Sie die Registerkarte **Übersicht** aus, und kopieren Sie die IP-Adresse.
1. Öffnen Sie eine neue Registerkarte im Browser, und geben Sie die IP-Adresse ein. Geben Sie zum Beispiel `http://<IP-address>:5000 (http://55.55.55.55:5000`) ein. Die Homepage des Containers wird angezeigt, in der Sie erkennen, dass der Container ausgeführt wird.

    ![Anzeigen der Homepage des Containers, um sicherzustellen, dass er ausgeführt wird](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Klicken Sie auf den Link **Dienst-API-Beschreibung**, um die Swagger-Seite des Containers aufzurufen.

1. Wählen Sie eine der **POST**-APIs und dann **Jetzt ausprobieren** aus. Die Parameter werden angezeigt, einschließlich der folgenden Beispieleingabe:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Legen Sie **showStats** auf `true` fest.

1. Wählen Sie **Ausführen** aus, um die Stimmung des Texts zu bestimmen.

    Das im Container enthaltene Modell erzeugt einen Bewertungsbereich von 0 bis 1, wobei 0 eine negative und 1 eine positive Stimmung ist.

    Die zurückgegebene JSON-Antwort enthält die Stimmung für die aktualisierte Texteingabe:

    ```json
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
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Das Dokument der in der Antwort enthaltenen JSON-Daten kann nun mit dem ursprünglichen Dokument durch seine entsprechende `id` in Korrelation gebracht werden. Jedes Dokument wird unabhängig voneinander behandelt und enthält verschiedene Statistiken wie `characterCount` und `transactionCount`. Zusätzlich weist jedes resultierende Dokument das `detectedLanguages`-Array mit `name`, `iso6391Name` und `score` für jede erkannte Sprache auf. Wenn mehrere Sprachen erkannt werden, wird die `score` verwendet, um die wahrscheinlichste Sprache zu bestimmen.