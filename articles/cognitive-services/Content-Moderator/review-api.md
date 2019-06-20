---
title: 'Konzepte: Überprüfungen, Workflows und Aufträge – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Informationen zu Überprüfungen, Workflows und Aufträgen
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607253"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Inhaltsmoderationsüberprüfungen, -workflows und -aufträge

Durch die Kombination von computergestützter Moderation und Human-in-the-Loop-Funktionen kann mit Content Moderator ein optimaler Moderationsprozess für reale Szenarien erstellt werden. Dies erfolgt durch das cloudbasierte [Prüfungstool](https://contentmoderator.cognitive.microsoft.com). In diesem Artikel erhalten Sie Informationen zu den wichtigsten Konzepten des Prüfungstools: Überprüfungen, Workflows und Aufträge.

## <a name="reviews"></a>Überprüfungen

Bei einer Überprüfung werden Inhalte in das Prüfungstool hochgeladen und auf der Registerkarte **Überprüfen** angezeigt. Hier können Benutzer die angewandten Markierungen ändern und nach Bedarf eigene benutzerdefinierte Markierungen anwenden. Wenn ein Benutzer eine Überprüfung übermittelt, werden die Ergebnisse an einen angegebenen Rückrufendpunkt gesendet und die Inhalte auf der Website entfernt.

![Website des Prüfungstools in einem Browser mit geöffneter Registerkarte „Überprüfen“](./Review-Tool-user-Guide/images/image-workflow-review.png)

Informationen zum Erstellen von Überprüfungen finden Sie im [Leitfaden für das Prüfungstool](./review-tool-user-guide/review-moderated-images.md), Informationen zur programmgesteuerten Erstellung finden Sie in der [Anleitung für die REST-API](./try-review-api-review.md).

## <a name="workflows"></a>Workflows

Ein Workflow ist ein cloudbasierter benutzerdefinierter Filter für Inhalte. Workflows lassen sich mit einer Vielzahl von Diensten verbinden, sodass Inhalte auf unterschiedliche Weise gefiltert und dann entsprechende Aktionen ausgeführt werden können. Über den Content Moderator-Konnektor kann ein Workflow automatisch Moderationsmarkierungen anwenden und Überprüfungen mit übermittelten Inhalten erstellen.

### <a name="view-workflows"></a>Anzeigen von Workflows

Navigieren Sie zum Anzeigen Ihrer vorhandenen Workflows zum [Prüfungstool](https://contentmoderator.cognitive.microsoft.com/), und wählen Sie **Einstellungen** > **Workflows** aus.

![Standardworkflow](images/default-workflow-listed.PNG)

Workflows können vollständig als JSON-Zeichenfolgen beschrieben werden, sodass programmgesteuert auf sie zugegriffen werden kann. Wenn Sie die Option **Bearbeiten** für Ihren Workflow und dann die Registerkarte **JSON** auswählen, wird ein JSON-Ausdruck ähnlich dem folgenden angezeigt:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Informationen zum Erstellen und Verwenden von Workflows finden Sie im [Leitfaden für das Prüfungstool](./review-tool-user-guide/workflows.md), Informationen zur programmgesteuerten Erstellung und Verwendung finden Sie in der [Anleitung für die REST-API](./try-review-api-workflow.md).

## <a name="jobs"></a>Aufträge

Ein Moderationsauftrag dient als eine Art Wrapper für die Funktionen der Inhaltsmoderation und von Workflows und Überprüfungen. Der Auftrag durchsucht Ihre Inhalte mithilfe der Bildmoderations-API oder der Textmoderations-API in Content Moderator und vergleicht sie dann mit dem festgelegten Workflow. Basierend auf den Workflowergebnissen wird für die Inhalte im [Prüfungstool](./review-tool-user-guide/human-in-the-loop.md) eine Überprüfung erstellt oder nicht. Während Überprüfungen und Workflows über die jeweils zugehörige API erstellt und konfiguriert werden können, können Sie über die Auftrags-API einen detaillierten Bericht des gesamten Prozesses abrufen (der an einen angegebenen Rückrufendpunkt gesendet werden kann).

Informationen zur Verwendung von Aufträgen finden Sie in der [Anleitung für die REST-API](./try-review-api-job.md).

## <a name="next-steps"></a>Nächste Schritte

* Testen Sie die [API-Konsole für Aufträge](try-review-api-job.md), und verwenden Sie die REST-API-Codebeispiele. Falls Sie mit Visual Studio und C# vertraut sind, können Sie sich auch die [.NET-Schnellstartanleitung für Aufträge](moderation-jobs-quickstart-dotnet.md) ansehen. 
* Machen Sie in Bezug auf Überprüfungen mit der [API-Konsole für Überprüfungen](try-review-api-review.md) vertraut, und verwenden Sie die REST-API-Codebeispiele. Gehen Sie anschließend den [.NET-Schnellstart für Überprüfungen](moderation-reviews-quickstart-dotnet.md) durch.
* Verwenden Sie für Videoüberprüfungen den [Schnellstart für Videoüberprüfungen](video-reviews-quickstart-dotnet.md), und erfahren Sie, wie Sie [Transkripte zur Videoüberprüfung hinzufügen](video-transcript-reviews-quickstart-dotnet.md).
