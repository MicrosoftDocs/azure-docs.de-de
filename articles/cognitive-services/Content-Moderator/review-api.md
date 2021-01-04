---
title: 'Konzepte: Überprüfungen, Workflows und Aufträge – Content Moderator'
titleSuffix: Azure Cognitive Services
description: 'In diesem Artikel erhalten Sie Informationen zu den wichtigsten Konzepten des Prüfungstools: Überprüfungen, Workflows und Aufträge.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 43c39c40af6e02861211a8666fefa57c34072f32
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905195"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Inhaltsmoderationsüberprüfungen, -workflows und -aufträge

Durch die Kombination von computergestützter Moderation und Human-in-the-Loop-Funktionen kann mit Content Moderator ein optimaler Moderationsprozess für reale Szenarien erstellt werden. Dies erfolgt durch das cloudbasierte [Prüfungstool](https://contentmoderator.cognitive.microsoft.com). In diesem Artikel erhalten Sie Informationen zu den wichtigsten Konzepten des Prüfungstools: Überprüfungen, Workflows und Aufträge.

## <a name="reviews"></a>Überprüfungen

Bei einer Überprüfung wird der Inhalt in das Überprüfungstool hochgeladen. Sie können es anzeigen, indem Sie auf seinen Inhaltstyp unter der Registerkarte **Review** (Überprüfung) auf dem Dashboard klicken. Vom Überprüfungsbildschirm aus können Sie die angewendeten Tags ändern und ggf. Ihre eigenen benutzerdefinierten Tags anwenden. Wenn Sie eine Überprüfung übermitteln, werden die Ergebnisse an einen angegebenen Rückrufendpunkt gesendet und die Inhalte auf der Website entfernt.

> [!div class="mx-imgBorder"]
> ![Hervorgehobenes Dropdownmenü „Review“ (Überprüfen). Diese Inhaltstypen werden angezeigt: Bild, Text und Video.](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>Verwalten von Überprüfungen

Navigieren Sie vom Dashboard aus zu **Admin** -> **Manage Reviews** (Administrator – Überprüfungen verwalten), um den Administratorbildschirm anzuzeigen. Hier sehen Sie eine Liste aller Überprüfungen (ausstehende und abgeschlossene).

Mit der **Aktionsschaltfläche** (die drei Punkte anzeigt) für die einzelnen Überprüfungen können Sie zum Überprüfungsbildschirm wechseln oder den Verlauf dieser Überprüfung anzeigen.

> [!div class="mx-imgBorder"]
> ![Website zum Überprüfungstool auf dem Bildschirm „Review“ (Überprüfung)](./Review-Tool-user-Guide/images/manage-reviews.png)

Verwenden Sie die Symbolleiste für die **Suche**, um die Überprüfungen nach einer Vielzahl von Kategorien wie Überprüfungsstatus, Tags, Inhaltstyp, untergeordneten Teams, zugewiesenen Benutzern und Erstellungs-/Änderungsdatum zu sortieren.

> [!div class="mx-imgBorder"]
> ![Die Suchsymbolleiste wird angezeigt. Darin enthalten: verschiedene Kombinationsfelder zum Eingeben von Suchkriterien wie Überprüfungsstatus und Tags](./Review-Tool-user-Guide/images/review-search.png)

Informationen zum Erstellen von Überprüfungen finden Sie im [Leitfaden für das Prüfungstool](./review-tool-user-guide/review-moderated-images.md), Informationen zur programmgesteuerten Erstellung finden Sie in der [Anleitung für die API-Konsole](./try-review-api-review.md).

## <a name="workflows"></a>Workflows

Ein Workflow ist ein cloudbasierter benutzerdefinierter Filter für Inhalte. Workflows lassen sich mit einer Vielzahl von Diensten verbinden, sodass Inhalte auf unterschiedliche Weise gefiltert und dann entsprechende Aktionen ausgeführt werden können. Über den Content Moderator-Konnektor kann ein Workflow automatisch Moderationsmarkierungen anwenden und Überprüfungen mit übermittelten Inhalten erstellen.

### <a name="view-workflows"></a>Anzeigen von Workflows

Navigieren Sie zum Anzeigen Ihrer vorhandenen Workflows zum [Überprüfungstool](https://contentmoderator.cognitive.microsoft.com/), und wählen Sie **Admin** > **Workflows** (Administrator – Workflows) aus.

> [!div class="mx-imgBorder"]
> ![Standardworkflow](images/default-workflow-list.png)

Workflows werden als JSON-Zeichenfolgen definiert, wodurch sie programmgesteuert zugänglich sind. Wenn Sie die Option **Bearbeiten** für Ihren Workflow und dann die Registerkarte **JSON** auswählen, wird ein JSON-Ausdruck ähnlich dem folgenden angezeigt:

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

Informationen zum Erstellen und Verwenden von Workflows finden Sie im [Leitfaden für das Prüfungstool](./review-tool-user-guide/workflows.md), Informationen zur programmgesteuerten Erstellung und Verwendung finden Sie in der [Anleitung für die API-Konsole](./try-review-api-workflow.md).

## <a name="jobs"></a>Aufträge

Ein Moderationsauftrag dient als eine Art Wrapper für die Funktionen der Inhaltsmoderation und von Workflows und Überprüfungen. Der Auftrag durchsucht Ihre Inhalte mithilfe der Bildmoderations-API oder der Textmoderations-API in Content Moderator und vergleicht sie dann mit dem festgelegten Workflow. Basierend auf den Workflowergebnissen wird für die Inhalte im [Prüfungstool](./review-tool-user-guide/human-in-the-loop.md) eine Überprüfung erstellt oder nicht. Während Überprüfungen und Workflows über die jeweils zugehörige API erstellt und konfiguriert werden können, können Sie über die Auftrags-API einen detaillierten Bericht des gesamten Prozesses abrufen (der an einen angegebenen Rückrufendpunkt gesendet werden kann).

Informationen zu den ersten Schritten mit Aufträgen finden Sie im [Leitfaden für die API-Konsole](./try-review-api-job.md).

## <a name="next-steps"></a>Nächste Schritte

* Testen Sie die [API-Konsole für Aufträge](try-review-api-job.md), und verwenden Sie die REST-API-Codebeispiele. Falls Sie mit Visual Studio und C# vertraut sind, können Sie sich auch die [.NET-Schnellstartanleitung für Aufträge](moderation-jobs-quickstart-dotnet.md) ansehen. 
* Machen Sie in Bezug auf Überprüfungen mit der [API-Konsole für Überprüfungen](try-review-api-review.md) vertraut, und verwenden Sie die REST-API-Codebeispiele. Gehen Sie anschließend den Abschnitt „Überprüfungen“ des [.NET-Schnellstarts](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) durch.
* Verwenden Sie für Videoüberprüfungen den [Schnellstart für Videoüberprüfungen](video-reviews-quickstart-dotnet.md), und erfahren Sie, wie Sie [Transkripte zur Videoüberprüfung hinzufügen](video-transcript-reviews-quickstart-dotnet.md).