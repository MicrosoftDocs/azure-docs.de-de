---
title: Überprüfen der Containerinstanz für die Schlüsselbegriffserkennung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Containerinstanz für die Schlüsselbegriffserkennung überprüfen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5dca4828a5c1127133461ddf9fc06099fc176b68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009914"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>Überprüfen der Containerinstanz für die Schlüsselbegriffserkennung

1. Wählen Sie die Registerkarte **Übersicht** aus, und kopieren Sie die IP-Adresse.
1. Öffnen Sie eine neue Registerkarte im Browser, und geben Sie die IP-Adresse ein. Geben Sie zum Beispiel `http://<IP-address>:5000 (http://55.55.55.55:5000`) ein. Die Homepage des Containers wird angezeigt, in der Sie erkennen, dass der Container ausgeführt wird.

    ![Anzeigen der Homepage des Containers, um sicherzustellen, dass er ausgeführt wird](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Klicken Sie auf den Link **Dienst-API-Beschreibung**, um die Swagger-Seite des Containers aufzurufen.

1. Wählen Sie eine der **POST**-APIs und dann **Jetzt ausprobieren** aus. Die Parameter werden angezeigt, einschließlich der folgenden Beispieleingabe:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. Ersetzen Sie die Eingabe durch folgenden JSON-Code:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Legen Sie **showStats** auf `true` fest.

1. Wählen Sie **Ausführen** aus, um die Stimmung des Texts zu bestimmen.

    Das im Container enthaltene Modell erzeugt einen Bewertungsbereich von 0 bis 1, wobei 0 negativ und 1 positiv ist.

    Die zurückgegebene JSON-Antwort enthält die Stimmung für die aktualisierte Texteingabe:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Das Dokument `id` der in der Antwort enthaltenen JSON-Daten kann nun mit dem ursprünglichen Dokument `id` in Korrelation gebracht werden. Das resultierende Dokument weist ein `keyPhrases`-Array auf, das die Liste der Schlüsselbegriffe enthält, die aus dem entsprechenden Eingabedokument extrahiert wurden. Zusätzlich gibt es verschiedene Statistiken wie `characterCount` und `transactionCount` für jedes resultierende Dokument.