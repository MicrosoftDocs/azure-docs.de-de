---
title: Definieren von Moderationsworkflows über die REST-API-Konsole – Content Moderator
titleSuffix: Azure Cognitive Services
description: Sie können die Überprüfungs-APIs von Azure Content Moderator verwenden, um benutzerdefinierte Workflows und Schwellenwerte basierend auf Ihren Inhaltsrichtlinien zu definieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 79749533d636f4b73ff3bef6b12d9e842ac485ea
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905158"
---
# <a name="define-and-use-moderation-workflows-api-console"></a>Definieren und Verwenden von Moderationsworkflows (API-Konsole)

Workflows sind cloudbasierte benutzerdefinierte Filter, mit denen Sie Inhalte effizienter verwalten können. Workflows lassen sich mit einer Vielzahl von Diensten verbinden, sodass Inhalte auf unterschiedliche Weise gefiltert und dann entsprechende Aktionen ausgeführt werden können. In dieser Anleitung wird erläutert, wie Sie mithilfe der REST-APIs für Workflows über die API-Konsole Workflows erstellen und verwenden können. Nachdem Sie sich mit der Struktur der APIs vertraut gemacht haben, können Sie diese Aufrufe bequem zu jeder REST-kompatiblen Plattform portieren.

## <a name="prerequisites"></a>Voraussetzungen

- Melden Sie sich auf der Website des [Content Moderator-Prüfungstools](https://contentmoderator.cognitive.microsoft.com/) an, oder erstellen Sie dort ein Konto.

## <a name="create-a-workflow"></a>Erstellen eines Workflows

Um einen Workflow zu erstellen oder zu aktualisieren, wechseln Sie zur API-Referenzseite **[Workflow – Erstellen oder Aktualisieren](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** , und wählen Sie die Schaltfläche für Ihre Schlüsselregion aus. Sie können Ihre Region in der Endpunkt-URL auf der Seite **Anmeldeinformationen** des [Prüfungstools](https://contentmoderator.cognitive.microsoft.com/) finden. Dadurch wird die API-Konsole gestartet, über die Sie ganz einfach REST-API-Aufrufe erstellen und ausführen können.

![Regionsauswahl auf der Seite „Workflow – Erstellen oder aktualisieren“](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Eingeben von REST-Aufrufparametern

Geben Sie Werte für **team**, **workflowname** und **Ocp-Apim-Subscription-Key** ein:

- **team**: Die Team-ID, die Sie beim Einrichten Ihres Kontos für das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com/) erstellt haben (zu finden im Feld **ID** auf der Seite „Anmeldeinformationen“ des Prüfungstools).
- **workflowname**: Der Name eines neuen hinzuzufügenden Workflows (oder ein vorhandener Name, wenn Sie einen vorhandenen Workflow aktualisieren möchten).
- **Ocp-Apim-Subscription-Key**: Ihr Content Moderator-Schlüssel. Diesen Schlüssel finden Sie auf der Registerkarte **Einstellungen** des [Prüfungstools](https://contentmoderator.cognitive.microsoft.com).

![Abfrageparameter und Header in der Konsole „Workflow – Erstellen oder aktualisieren“](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Eingeben einer Workflowdefinition

1. Geben Sie im Feld **Anforderungstext** die JSON-Anforderung mit Angaben zu **Description** und **Type** (entweder `Image` oder `Text`) ein.
2. Kopieren Sie für **Expression** den JSON-Ausdruck für den Standardworkflow. Die endgültige JSON-Zeichenfolge sollte wie folgt aussehen:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Mit dieser API können Sie einfache, komplexe und sogar geschachtelte Ausdrücke für Ihre Workflows definieren. In der Dokumentation zu [Workflow – Create Or Update](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) (Workflow – erstellen oder aktualisieren) finden Sie Beispiele mit komplexerer Logik.

### <a name="submit-your-request"></a>Senden der Anforderung
  
Wählen Sie **Send** (Senden) aus. Wenn der Vorgang erfolgreich ist, lautet der **Antwortstatus**`200 OK`, und im Feld **Antwortinhalt** wird `true` angezeigt.

### <a name="examine-the-new-workflow"></a>Prüfen des neuen Workflows

Wählen Sie im [Prüfungstool](https://contentmoderator.cognitive.microsoft.com/) die Optionen **Einstellungen** > **Workflows** aus. Der neue Workflow sollte in der Liste angezeigt werden.

![Liste von Workflows im Prüfungstool](images/workflow-console-new-workflow.PNG)

Wählen Sie für Ihren Workflow die Option **Bearbeiten** aus, und navigieren Sie zu **Designer**. Hier sehen Sie eine intuitive Darstellung der JSON-Logik.

![Registerkarte „Designer“ für einen ausgewählten Workflow](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Abrufen von Workflowdetails

Um Details zu einem vorhandenen Workflow abzurufen, navigieren Sie zur API-Referenzseite, und wählen Sie unter **[Workflow – Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** (Workflow – abrufen) die Schaltfläche für Ihre Region aus (die Region, in der Ihr Schlüssel verwaltet wird).

![Regionsauswahl auf der Seite „Workflow – Abrufen“](images/test-drive-region.png)

Geben Sie die REST-Aufrufparameter wie im obigen Abschnitt ein. Stellen Sie sicher, dass **Workflowname** diesmal der Name eines vorhandenen Workflows ist.

![Abrufen allgemeiner Parameter und Header](images/workflow-get-default.PNG)

Wählen Sie **Send** (Senden) aus. Wenn der Vorgang erfolgreich ist, lautet der **Antwortstatus**`200 OK`, und im Feld **Antwortinhalt** wird der Workflow im JSON-Format wie im folgenden Beispiel angezeigt:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Workflows mit [Inhaltsmoderationsaufträgen](try-review-api-job.md) verwendet werden.