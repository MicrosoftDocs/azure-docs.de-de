---
title: Schnellstart für die Gesichtserkennungs-REST-API
description: Verwenden Sie die Gesichtserkennungs-REST-API mit cURL, um Gesichter zu erkennen, ähnliche Gesichter zu finden (Gesichtssuche anhand von Bildern), Gesichter zu identifizieren (Gesichtserkennungssuche) und Ihre Gesichtserkennungsdaten zu migrieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: be942f73ee0a3d5a8850141c937754bad330db90
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912266"
---
Erste Schritte mit der Gesichtserkennung unter Verwendung der Gesichtserkennungs-REST-API. Über den Gesichtserkennungsdienst haben Sie Zugriff auf erweiterte Algorithmen für die Erkennung von menschlichen Gesichtern in Bildern.

Verwenden Sie die Gesichtserkennungs-REST-API für Folgendes:

* [Gesichtserkennung in einem Bild](#detect-faces-in-an-image)
* [Suchen ähnlicher Gesichter](#find-similar-faces)

> [!NOTE]
> In dieser Schnellstartanleitung wird die REST-API mithilfe von cURL-Befehlen aufgerufen. Die REST-API kann aber auch mit einer Programmiersprache aufgerufen werden. Auf GitHub stehen Beispiele für [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest) und [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest) zur Verfügung.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Erstellen einer Gesichtserkennungsressource"  target="_blank"> eine Gesichtserkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Gesichtserkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.


## <a name="detect-faces-in-an-image"></a>Gesichtserkennung in einem Bild

Der Befehl zum Aufrufen der Gesichtserkennungs-API sowie zum Abrufen von Gesichtsattributdaten aus einem Bild sieht in etwa wie folgt aus. Kopieren Sie den Code zunächst in einen Text-Editor. Sie müssen bestimmte Teile des Befehls ändern, bevor Sie ihn ausführen können.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

Nehmen Sie die folgenden Änderungen vor:
1. Weisen Sie `Ocp-Apim-Subscription-Key` Ihrem gültigen Abonnementschlüssel für die Gesichtserkennung zu.
1. Ändern Sie den ersten Teil der Abfrage-URL so, dass er dem Endpunkt für Ihren Abonnementschlüssel entspricht.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Ändern Sie optional die URL im Anforderungstext, um auf ein anderes Bild zu verweisen.

Wenn Sie die gewünschten Änderungen vorgenommen haben, öffnen Sie eine Eingabeaufforderung, und geben Sie den neuen Befehl ein. 

### <a name="examine-the-results"></a>Untersuchen der Ergebnisse

Die Gesichtserkennungsinformationen sollten daraufhin als JSON-Daten im Konsolenfenster angezeigt werden. Beispiel:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>Abrufen von Gesichtsattributen
 
Rufen Sie zum Extrahieren von Gesichtsattributen erneut die Erkennungs-API auf, aber legen Sie diesmal `detectionModel` auf `detection_01` fest. Fügen Sie außerdem den Abfrageparameter `returnFaceAttributes` hinzu. Der Befehl sollte jetzt wie in der folgenden Abbildung aussehen. Fügen Sie wie zuvor Ihren Abonnementschlüssel und den Endpunkt für die Gesichtserkennung ein.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>Untersuchen der Ergebnisse

Die zurückgegebenen Gesichtserkennungsinformationen enthalten jetzt Gesichtsattribute. Beispiel:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>Suchen ähnlicher Gesichter

Bei diesem Vorgang wird ein einzelnes erkanntes Gesicht (Quelle) verwendet und eine Reihe anderer Gesichter (Ziel) auf Übereinstimmungen (Gesichtssuche anhand von Bildern) durchsucht. Bei einer Übereinstimmung wird die ID des entsprechenden Gesichts in der Konsole ausgegeben.

### <a name="detect-faces-for-comparison"></a>Erkennen von Gesichtern zum Vergleich

Damit Gesichter in Bildern verglichen werden können, müssen sie zunächst erkannt werden. Führen Sie den folgenden Befehl auf die gleiche Weise aus wie im Abschnitt [Erkennen von Gesichtern](#detect-faces-in-an-image). Diese Erkennungsmethode ist für Vergleichsvorgänge optimiert. Sie extrahiert keine detaillierten Gesichtsattribute wie im obigen Abschnitt und verwendet ein anderes Erkennungsmodell.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

Suchen Sie in der JSON-Antwort nach dem Wert `"faceId"`, und speichern Sie ihn an einem temporären Speicherort. Rufen Sie anschließend den obigen Befehl für die folgenden Bild-URLs auf, und speichern Sie auch deren Gesichts-IDs. Diese IDs werden als die Zielgruppe von Gesichtern verwendet, unter denen nach einem ähnlichen Gesicht gesucht werden soll.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

Erkennen Sie schließlich das einzelne Quellgesicht für den Abgleich, und speichern Sie dessen ID. Halten Sie diese ID von den anderen IDs getrennt.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>Suchen von Übereinstimmungen

Kopieren Sie den folgenden Befehl in einen Text-Editor:

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

Nehmen Sie dann folgende Änderungen vor:
1. Weisen Sie `Ocp-Apim-Subscription-Key` Ihrem gültigen Abonnementschlüssel für die Gesichtserkennung zu.
1. Ändern Sie den ersten Teil der Abfrage-URL so, dass er dem Endpunkt für Ihren Abonnementschlüssel entspricht.

Verwenden Sie für den Wert `body` den folgenden JSON-Inhalt:

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. Verwenden Sie für `"faceId"` die ID des Quellgesichts.
1. Fügen Sie die anderen Gesichts-IDs als Begriffe in das Array `"faceIds"` ein.

### <a name="examine-the-results"></a>Untersuchen der Ergebnisse

Sie erhalten eine JSON-Antwort mit einer Liste der IDs von Gesichtern, die zu Ihrem Abfragegesicht passen.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie die Gesichtserkennungs-REST-API für einfache Gesichtserkennungsaufgaben verwenden. In der Referenzdokumentation finden Sie weitere Informationen zur Bibliothek.

> [!div class="nextstepaction"]
> [Referenz zur Gesichtserkennungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [Was ist der Gesichtserkennungsdienst?](../../overview.md)