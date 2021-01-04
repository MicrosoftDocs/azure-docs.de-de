---
title: Erstellen von Moderationsüberprüfungen über die REST-API-Konsole – Content Moderator
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Überprüfungs-API von Azure Content Moderator, um Bild- oder Textüberprüfungen für die Moderation durch Personen zu erstellen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: 479c7c455f07d098edd327196803e85df24dfb6d
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905131"
---
# <a name="create-human-reviews-api-console"></a>Erstellen von Überprüfungen durch Personen (API-Konsole)

In [Überprüfungen](./review-api.md#reviews) werden Inhalte gespeichert und angezeigt, damit Moderatoren auf sie zugreifen können. Wenn ein Benutzer eine Überprüfung abgeschlossen hat, werden die Ergebnisse an einen angegebenen Rückrufendpunkt gesendet. In diesem Artikel erfahren Sie, wie Sie Überprüfungen mithilfe der Überprüfungs-REST-APIs über die API-Konsole einrichten. Nachdem Sie sich mit der Struktur der APIs vertraut gemacht haben, können Sie diese Aufrufe bequem zu jeder REST-kompatiblen Plattform portieren.

## <a name="prerequisites"></a>Voraussetzungen

- Melden Sie sich auf der Website des [Content Moderator-Prüfungstools](https://contentmoderator.cognitive.microsoft.com/) an, oder erstellen Sie dort ein Konto.

## <a name="create-a-review"></a>Erstellen einer Überprüfung

Navigieren Sie zum Erstellen einer Überprüfung zur API-Referenzseite, und wählen Sie unter **[Review – Create](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** (Überprüfung – Erstellen) die Schaltfläche für Ihre Schlüsselregion aus (diese finden Sie in der Endpunkt-URL auf der Seite **Anmeldeinformationen** des [Prüfungstools](https://contentmoderator.cognitive.microsoft.com/)). Dadurch wird die API-Konsole gestartet, über die Sie ganz einfach REST-API-Aufrufe erstellen und ausführen können.

![Regionsauswahl auf der Seite „Review – Get“ (Überprüfung – abrufen)](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Eingeben von REST-Aufrufparametern

Geben Sie Werte für **teamName** und **Ocp-Apim-Subscription-Key** ein:

- **teamName**: Die Team-ID, die Sie beim Einrichten Ihres Kontos für das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com/) erstellt haben (zu finden im Feld **ID** auf der Seite „Anmeldeinformationen“ des Prüfungstools).
- **Ocp-Apim-Subscription-Key**: Ihr Content Moderator-Schlüssel. Diesen finden Sie auf der Registerkarte **Einstellungen** des [Prüfungstools](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Eingeben einer Überprüfungsdefinition

Geben Sie im Feld **Anforderungstext** die JSON-Anforderung mit den folgenden Feldern ein:

- **Metadaten**: Die an Ihren Rückrufendpunkt zurückzugebenden benutzerdefinierten Schlüssel-Wert-Paare. Wenn dieser Schlüssel ein im [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) definierter kurzer Code ist, wird er als Tag angezeigt.
- **Content:** Bei Bild- und Videoinhalten ist dies eine URL-Zeichenfolge, die auf die Inhalte verweist. Bei Textinhalten ist dies die eigentliche Textzeichenfolge.
- **ContentId**: Eine benutzerdefinierte Bezeichnerzeichenfolge. Diese Zeichenfolge wird an die API übergeben und durch den Rückruf zurückgegeben. Sie ist nützlich, um interne Bezeichner oder Metadaten mit den Ergebnissen eines Moderationsauftrags zu verknüpfen.
- **CallbackEndpoint:** (Optional:) Die URL zum Empfangen von Rückrufinformationen, wenn die Überprüfung abgeschlossen ist.

Im Standardanforderungstext werden Beispiele für die verschiedenen Überprüfungen angezeigt, die Sie erstellen können:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Senden der Anforderung
  
Wählen Sie **Senden** aus. Wenn der Vorgang erfolgreich ist, lautet der **Antwortstatus**`200 OK`, und im Feld **Antwortinhalt** wird eine ID für die Überprüfung angezeigt. Kopieren Sie diese ID für die folgenden Schritte.

![Feld „Antwortinhalt“ mit Überprüfungs-ID in der Konsole „Überprüfen – Erstellen“](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Untersuchen der neuen Überprüfung

Wählen Sie im [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) die Optionen **Überprüfung** > **Bild**/**Text**/**Video** aus (abhängig vom verwendeten Inhalt). Der hochgeladene Inhalt sollte angezeigt werden und kann durch Personen überprüft werden.

![Prüfungstool-Bild eines Fußballs](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Abrufen von Bewertungsdetails

Um Details zu einer vorhandenen Überprüfung abzurufen, navigieren Sie zur API-Referenzseite, und wählen Sie unter [Review – Get](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) (Überprüfung – Abrufen) die Schaltfläche für Ihre Region aus (die Region, in der Ihr Schlüssel verwaltet wird).

![Regionsauswahl auf der Seite „Workflow – Abrufen“](images/test-drive-region.png)

Geben Sie die REST-Aufrufparameter wie im obigen Abschnitt ein. In diesem Schritt haben Sie beim Erstellen der Überprüfung **reviewId** als eindeutige ID-Zeichenfolge erhalten.

![Abrufergebnisse in der Konsole „Überprüfen – Erstellen“](images/test-drive-review-3.PNG)
  
Wählen Sie **Senden** aus. Wenn der Vorgang erfolgreich ist, lautet der **Antwortstatus**`200 OK`, und im Feld **Antwortinhalt** werden die Überprüfungsdetails im JSON-Format wie folgt angezeigt:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Beachten Sie folgende Felder in der Antwort:

- **status**
- **reviewerResultTags:** Wird angezeigt, wenn vom Überprüfungsteam (angegeben im Feld **createdBy**) manuell Markierungen hinzugefügt wurden.
- **metadata:** Hier werden die ursprünglich in der Überprüfung hinzugefügten Markierungen angezeigt, bevor das Überprüfungsteam Änderungen vorgenommen hat.

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie erfahren, wie Inhaltsmoderationsüberprüfungen mithilfe der REST-API erstellt werden. Als Nächstes integrieren Sie Überprüfungen in ein End-to-End-Moderationsszenario, z. B. das im Tutorial [E-Commerce-Moderation](./ecommerce-retail-catalog-moderation.md).