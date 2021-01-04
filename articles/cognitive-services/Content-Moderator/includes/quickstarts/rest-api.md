---
title: Schnellstart für die Content Moderator-REST-API
titleSuffix: Azure Cognitive Services
description: Diese Schnellstartanleitung enthält Informationen zu den ersten Schritten mit der Azure Content Moderator-REST-API. Integrieren Sie Software zur Inhaltsfilterung in Ihre App, um gesetzliche Vorschriften einzuhalten oder Benutzern die für sie beabsichtigte Umgebung zu bieten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: ebef33072b802ffc35b8c011d974dbcd203fa6e1
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561476"
---
Hier finden Sie Informationen zu den ersten Schritten mit der Azure Content Moderator-REST-API. 

Content Moderator ist ein KI-Dienst für die Behandlung potenziell anstößiger, riskanter oder anderweitig unerwünschter Inhalte. Verwenden Sie den KI-gestützten Dienst zur Inhaltsmoderation, um Text, Bilder und Videos zu scannen und Inhaltsflags automatisch anzuwenden. Integrieren Sie dann das Review-Tool in Ihre App, eine Online-Moderatorumgebung für ein Team aus menschlichen Prüfern. Integrieren Sie Software zur Inhaltsfilterung in Ihre App, um gesetzliche Vorschriften einzuhalten oder Benutzern die für sie beabsichtigte Umgebung zu bieten.

Verwenden Sie die Content Moderator-REST-API für Folgendes:

* Moderieren von Text
* Moderieren von Bildern

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Wenn Sie über ein Azure-Abonnement verfügen, können Sie im Azure-Portal <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Erstellen einer Content Moderator-Ressource"  target="_blank">eine Content Moderator-Ressource erstellen <span class="docon docon-navigate-external x-hidden-focus"></span></a>, um Ihren Schlüssel und Endpunkt abzurufen. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um für Ihre Anwendung eine Verbindung mit Content Moderator herzustellen. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.


## <a name="moderate-text"></a>Moderieren von Text

Die Content Moderator-API kann mit einem Befehl wie dem folgenden aufgerufen werden, um einen Text zu analysieren und die Ergebnisse in der Konsole auszugeben:

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

Kopieren Sie den Befehl in einen Text-Editor, und nehmen Sie folgende Änderungen vor:

1. Weisen Sie `Ocp-Apim-Subscription-Key` Ihrem gültigen Abonnementschlüssel für die Gesichtserkennung zu.
1. Ändern Sie den ersten Teil der Abfrage-URL so, dass er dem Endpunkt für Ihren Abonnementschlüssel entspricht.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Ändern Sie optional den Text der Anforderung in eine beliebige Textzeichenfolge, die Sie analysieren möchten.

Wenn Sie die gewünschten Änderungen vorgenommen haben, öffnen Sie eine Eingabeaufforderung, und geben Sie den neuen Befehl ein. 

### <a name="examine-the-results"></a>Untersuchen der Ergebnisse

Die Ergebnisse der Textmoderation sollten als JSON-Daten im Konsolenfenster angezeigt werden. Beispiel:

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

Weitere Informationen zu den von Content Moderator untersuchten Textattributen finden Sie im [Leitfaden zu Textmoderationskonzepten](../../text-moderation-api.md).

## <a name="moderate-images"></a>Moderieren von Bildern

Die Content Moderator-API kann mit einem Befehl wie dem folgenden aufgerufen werden, um ein Remotebild zu moderieren und die Ergebnisse in der Konsole auszugeben:

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

Kopieren Sie den Befehl in einen Text-Editor, und nehmen Sie folgende Änderungen vor:

1. Weisen Sie `Ocp-Apim-Subscription-Key` Ihrem gültigen Abonnementschlüssel für die Gesichtserkennung zu.
1. Ändern Sie den ersten Teil der Abfrage-URL so, dass er dem Endpunkt für Ihren Abonnementschlüssel entspricht.
1. Ändern Sie optional die URL `"Value"` im Anforderungstext in das gewünschte Remotebild für die Moderation.

> [!TIP]
> Sie können auch lokale Bilder moderieren, indem Sie deren Bytedaten an den Anforderungstext übergeben. Eine entsprechende Anleitung finden Sie in der [Referenzdokumentation](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

Wenn Sie die gewünschten Änderungen vorgenommen haben, öffnen Sie eine Eingabeaufforderung, und geben Sie den neuen Befehl ein. 

### <a name="examine-the-results"></a>Untersuchen der Ergebnisse

Die Ergebnisse der Bildmoderation sollten als JSON-Daten im Konsolenfenster angezeigt werden. 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

Weitere Informationen zu den Bildattributen, die von Content Moderator untersucht werden, finden Sie im Leitfaden zu [Bildmoderationskonzepten](../../image-moderation-api.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-Befehlszeilenschnittstelle](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie die Content Moderator-REST-API für Moderationsaufgaben verwenden. Im Anschluss können Sie sich im konzeptionellen Leitfaden ausführlicher über die Moderation von Bildern oder anderen Medien informieren.

* [Lernen von Bildmoderationskonzepten](../../image-moderation-api.md)
* [Lernen von Textmoderationskonzepten](../../text-moderation-api.md)
* [Was ist Azure Content Moderator?](../../overview.md)