---
title: Bildmoderation – Content Moderator
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die computergestützte Bildmoderation und das Tool für die Überprüfung mit menschlicher Beteiligung von Content Moderator, um Bilder mit nicht jugendfreien und freizügigen Inhalten zu moderieren.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 9f1df23d1f0f24787bb9267064ffd647eda2cb74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699272"
---
# <a name="learn-image-moderation-concepts"></a>Lernen von Bildmoderationskonzepten

Verwenden Sie die computergestützte Bildmoderation und das [Tool für die Überprüfung mit menschlicher Beteiligung](Review-Tool-User-Guide/human-in-the-loop.md) von Content Moderator, um Bilder mit nicht jugendfreien und freizügigen Inhalten zu moderieren. Scannen Sie Bilder auf Textinhalte, extrahieren Sie diesen Text, und nutzen Sie die Gesichtserkennung. Sie können Bilder mit benutzerdefinierten Listen vergleichen und weitere Maßnahmen ergreifen.

## <a name="evaluating-for-adult-and-racy-content"></a>Auswerten von nicht jugendfreien und freizügigen Inhalten

Der **Auswertungsvorgang** gibt eine Zuverlässigkeitsbewertung zwischen 0 und 1 zurück. Außerdem werden boolesche Daten zurückgegeben, die TRUE oder FALSE entsprechen. Diese Werte sagen vorher, ob das Bild potenziell nicht jugendfreie oder freizügige Inhalte enthält. Wenn Sie die API mit Ihrem Bild (Datei oder URL) aufrufen, enthält die zurückgegebene Antwort die folgenden Informationen:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - `isImageAdultClassified` gibt an, dass möglicherweise Bilder vorhanden sind, die in bestimmten Situationen als explizit sexuell oder nicht jugendfrei betrachtet werden können.
> - `isImageRacyClassified` gibt an, dass möglicherweise Bilder vorhanden sind, die in bestimmten Situationen als zweideutig bzw. freizügig oder als für Erwachsene bestimmt betrachtet werden können.
> - Die Bewertung liegt zwischen 0 und 1. Je höher der Wert, desto höher die vom Modell ermittelte Wahrscheinlichkeit, dass die Kategorie zutreffend ist. Diese Vorschauversion basiert nicht auf manuell programmierten Ergebnissen, sondern auf einem statistischen Modell. Es wird empfohlen, anhand Ihrer eigenen Inhalte zu testen, wie die jeweiligen Kategorien zu Ihren Anforderungen passen.
> - Die booleschen Werte sind abhängig von den internen Bewertungsschwellenwerten entweder TRUE oder FALSE. Kunden müssen entscheiden, ob sie diesen Wert verwenden oder auf der Grundlage ihrer Inhaltsrichtlinien eigene Schwellenwerte festlegen möchten.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Erkennen von Text mit der optischen Zeichenerkennung (OCR)

Der **OCR-Vorgang** sagt die Anwesenheit von Textinhalten in einem Bild vorher und extrahiert diese für die Textmoderation sowie andere Zwecke. Sie können die Sprache festlegen. Wenn Sie keine Sprache angeben, wird die Erkennung automatisch auf Englisch festgelegt.

Die Antwort enthält folgende Informationen:
- Den ursprünglichen Text
- Die ermittelten Textelemente mit der zugehörigen Zuverlässigkeitsbewertung

Beispiel für die Extraktion:

    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      .........
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },


## <a name="detecting-faces"></a>Erkennen von Gesichtern

Das Erkennen von Gesichtern hilft dabei, persönliche Daten wie Gesichter in den Bildern zu erkennen. Sie können potenzielle Gesichter und die Anzahl potenzieller Gesichter in jedem Bild ermitteln.

Eine Antwort enthält die folgenden Informationen:

- Anzahl der Gesichter
- Liste der Positionen der erkannten Gesichter

Beispiel für die Extraktion:


    "FaceDetection": {
       ......
      "result": true,
      "count": 2,
      "advancedInfo": [
      .....
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        }
      ]
    }

## <a name="creating-and-managing-custom-lists"></a>Erstellen und Verwalten von benutzerdefinierten Listen

In vielen Onlinecommunitys kommt es oft dazu, dass anstößige Elemente mehrmals über Tage, Wochen und Monate hinweg geteilt werden, nachdem Benutzer Bilder oder andere Inhalte hochgeladen haben. Die Kosten für wiederholtes Scannen und Überprüfen des gleichen Bildes oder von geringfügig geänderten Versionen des Bildes von mehreren Orten kann teuer und fehleranfällig sein.

Anstelle dasselbe Bild mehrmals zu moderieren, können Sie die anstößigen Bilder Ihrer benutzerdefinierten Liste blockierter Inhalte hinzufügen. Auf diese Weise vergleicht Ihr System für die Moderation von Inhalten eingehende Bilder mit der benutzerdefinierten Liste und stoppt weitere Verarbeitung.

> [!NOTE]
> Die Obergrenze liegt bei **fünf Bilderlisten**, wobei jede Liste **maximal 10.000 Bilder** enthalten kann.
>

Der Content Moderator bietet eine vollständige [API zur Verwaltung von Bilderlisten](try-image-list-api.md) mit Vorgängen zum Verwalten von Listen benutzerdefinierter Bilder. Beginnen Sie mit der [API-Konsole für Bilderlisten](try-image-list-api.md), und verwenden Sie die REST-API-Codebeispiele. Falls Sie mit Visual Studio und C# vertraut sind, können Sie sich auch die [.NET-Schnellstartanleitung für Bilderlisten](image-lists-quickstart-dotnet.md) ansehen.

## <a name="matching-against-your-custom-lists"></a>Abgleichen mit Ihren benutzerdefinierten Listen

Der Vergleichsvorgang lässt Fuzzyübereinstimmungen bei eingehenden Bildern im Vergleich mit Ihren benutzerdefinierten Listen zu, die mithilfe der Listenvorgänge erstellt und verwaltet werden.

Wenn eine Übereinstimmung gefunden wird, gibt der Vorgang den Bezeichner und die Moderationstags des übereinstimmenden Bilds zurück. Die Antwort enthält die folgenden Informationen:

- Bewertung der Übereinstimmung (zwischen 0 und 1)
- Das übereinstimmende Bild
- Bildtags (während der vorherigen Moderation zugewiesen)
- Bildbezeichnungen

Beispiel für die Extraktion:

    {
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
    }

## <a name="human-review-tool"></a>Tool für die manuelle Überprüfung

Verwenden Sie für nuancierte Fälle das [Überprüfungstool](Review-Tool-User-Guide/human-in-the-loop.md) und die API des Content Moderator, um die Ergebnisse und Inhalte der Moderation für die Überprüfung durch menschliche Moderatoren zum Vorschein zu bringen. Diese überprüfen die vom Computer zugewiesenen Tags und bestätigen die endgültigen Entscheidungen.

![Bildüberprüfung für menschliche Moderatoren](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Nächste Schritte

Testen Sie die [API-Konsole für die Bildmoderation](try-image-api.md), und verwenden Sie die REST-API-Codebeispiele. Falls Sie mit Visual Studio und C# vertraut sind, können Sie sich auch die [.NET-Schnellstartanleitung für die Bildmoderation](image-moderation-quickstart-dotnet.md) ansehen.
