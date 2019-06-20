---
title: Videomoderation mit Überprüfung durch Personen – Content Moderator
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die computergestützte Videomoderation und Tools für die Überprüfung durch Personen, um unangemessene Inhalte zu moderieren.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: sajagtap
ms.openlocfilehash: a6c467d3153400815e37a5d461766140abd1fa32
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65228128"
---
# <a name="video-moderation-with-human-review"></a>Videomoderation mit Überprüfung durch Personen

Nutzen Sie die computergestützte [Videomoderation](video-moderation-api.md) von Content Moderator und [Tools zur Überprüfung durch Personen](Review-Tool-User-Guide/human-in-the-loop.md) zum Moderieren von Videos und Transkripts auf anstößige und zweideutige Inhalte, um optimale Ergebnisse für Ihr Unternehmen zu erhalten.

## <a name="video-trained-classifier-preview"></a>Durch Videos trainierte Klassifizierung (Preview)

Die computergestützte Videoklassifizierung erfolgt entweder mit durch Bilder oder Videos trainierte Modelle. Im Gegensatz zu durch Bilder trainierte Klassifizierungen wird die Klassifizierung für Inhalte für Erwachsene und freizügige Inhalte von Microsoft mit Videos trainiert. Dies führt zu besseren Übereinstimmungen.

## <a name="shot-detection"></a>Szenenwechselerkennung

Bei der Ausgabe der Klassifizierungsdetails ermöglicht zusätzliche Videointelligenz die flexiblere Analyse von Videos. Anstatt nur die Frames auszugeben, bietet der Video Moderation Service von Microsoft auch Informationen auf Szenenebene. Sie haben nun die Möglichkeit, Ihre Videos auf der Ebene der Szenen und der Frames zu analysieren.

## <a name="key-frame-detection"></a>Keyframeerkennung

Anstatt in regelmäßigen Abständen Frames auszugeben, identifiziert der Video Moderation Service nur potenziell vollständige (gute) Frames und gibt nur diese aus. Diese Funktion ermöglicht eine effiziente Frame-Generierung für Analyse von Inhalten für Erwachsene und von freizügigen Inhalten auf Frameebene.

Der folgende Auszug zeigt eine Teilantwort mit potentiellen Aufnahmen, Keyframes, sowie Bewertungen von Inhalten für Erwachsene und von freizügigen Inhalten:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Visualisierung für Überprüfungen durch Personen

Für differenzierte Fälle benötigen Unternehmen eine Lösung für Überprüfungen durch Personne für das Rendern des Videos, seiner Frames und der von Computern zugewiesenen Tags. Die menschlichen Moderatoren, die Videos und Frames überprüfen, erhalten einen vollständigen Überblick über die Einsichten, ändern Tags und reichen ihre Entscheidungen ein.

![Videoprüfungstool – Standardansicht](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Playeransicht zur Überprüfung auf Videoebene

Binäre Entscheidungen auf Videoebene werden durch eine Videoplayeransicht ermöglicht, die potenzielle Frames für Erwachsene und freizügige Frames zeigt. Der menschliche Prüfer kann mit verschiedenen Geschwindigkeitsoptionen im Video navigieren und die Szenen untersuchen. Um ihre Entscheidungen zu bestätigen, schalten Sie die Tags um.

![Videoprüfungstool – Playeransicht](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Framesansicht für detaillierte Überprüfungen

Die framebasierte Ansicht ermöglicht eine detaillierte Videoüberprüfung für die Frame-für-Frame-Analyse. Die menschlichen Prüfer überprüfen und wählen einen oder mehrere Frames und schalten die Tags um, um ihre Entscheidungen zu bestätigen. Ein optionaler nächsten Schritt ist die Bearbeitung der anstößigen Frames oder Inhalte.

![Videoprüfungstool – Framesansicht](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Transkriptmoderation

Videos haben in der Regel einen Begleitkommentar, der ebenfalls hinsichtlich anstößiger Sprache moderiert werden muss. Sie verwenden den Azure Media Indexer-Dienst, um Sprache in Text umzuwandeln, und übermitteln das Transkript zur Textmoderation innerhalb des Prüfungstools an die Überprüfungs-API von Content Moderator.

![Videoprüfungstool – Transkriptansicht](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Nächste Schritte

- Beginnen Sie mit dem [Schnellstart für die Videomoderation](video-moderation-api.md).
- Erfahren Sie mehr über das Generieren von [Videoüberprüfungen](video-reviews-quickstart-dotnet.md) für Ihre menschlichen Prüfer aus Ihrer moderierten Ausgabe.
- Fügen Sie [Überprüfungen von Videotranskripten](video-transcript-reviews-quickstart-dotnet.md) zu Ihren Videoüberprüfungen hinzu.
- Sehen Sie sich das ausführliche Tutorial zur Entwicklung einer [vollständigen Lösung für die Videomoderation an](video-transcript-moderation-review-tutorial-dotnet.md).