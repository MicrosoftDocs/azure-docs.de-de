---
title: Definieren und Verwenden von Inhaltsworkflows über das Prüfungstool – Content Moderator
titleSuffix: Azure Cognitive Services
description: Sie können den Workflow-Designer von Azure Content Moderator verwenden, um benutzerdefinierte Workflows und Schwellenwerte basierend auf Ihren Inhaltsrichtlinien zu definieren.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 6eb2a2d2762b60a12bb9a24b92e2edae4b846cd1
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904141"
---
# <a name="define-and-use-moderation-workflows"></a>Definieren und Verwenden von Moderationsworkflows

In diesem Artikel wird erläutert, wie Sie [Workflows](../review-api.md#workflows) auf der Website des [Prüfungstools](https://contentmoderator.cognitive.microsoft.com) einrichten und verwenden. Workflows sind cloudbasierte benutzerdefinierte Filter, mit denen Sie Inhalte effizienter verwalten können. Workflows lassen sich mit einer Vielzahl von Diensten verbinden, sodass Inhalte auf unterschiedliche Weise gefiltert und dann entsprechende Aktionen ausgeführt werden können. In diesem Artikel wird erläutert, wie Sie mithilfe des Content Moderator-Konnektors (standardmäßig enthalten) in einem typischen Moderationsszenario Inhalte filtern und Überprüfungen durch Personen einrichten können.

## <a name="create-a-new-workflow"></a>Erstellen eines neuen Workflows

Navigieren Sie zur Website des [Content Moderator-Prüfungstools](https://contentmoderator.cognitive.microsoft.com/), und melden Sie sich an. Wählen Sie auf der Registerkarte **Einstellungen** die Option **Workflows**.

![Workflow-Einstellung](images/2-workflows-0.png)

Wählen Sie auf dem nächsten Bildschirm die Option **Workflow hinzufügen** aus.

![Workflow hinzufügen](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Zuweisen von Name und Beschreibung

Benennen Sie den Workflow, geben Sie eine Beschreibung ein, und wählen Sie aus, ob der Workflow Bilder oder Text verarbeitet.

![Workflowname und -beschreibung](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definieren von Auswertungskriterien

Navigieren Sie auf dem nächsten Bildschirm zum Abschnitt **Wenn**. Wählen Sie im oberen Dropdownmenü den Eintrag **Bedingung** aus. Dadurch können Sie die Bedingung konfigurieren, für die der Workflow Aktionen ausführt. Wenn Sie mehrere Bedingungen verwenden möchten, wählen Sie stattdessen **Kombination** aus. 

Wählen Sie als Nächstes einen Konnektor aus. Dieses Beispiel verwendet **Content Moderator**. Je nach ausgewähltem Konnektor werden unterschiedliche Optionen für die Datenausgabe angezeigt. Informationen zum Einrichten weiterer Konnektoren finden Sie im Abschnitt [Konnektoren](./configure.md#connectors) des Leitfadens zu den Einstellungen des Prüfungstools.

![Auswählen des Workflowkonnektors](images/image-workflow-connect-to.PNG)

Wählen Sie die gewünschte zu verwendende Ausgabe aus, und legen Sie die entsprechenden Bedingungen für die Überprüfung fest.

![Workflowbedingung definieren](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definieren der Aktion

Wechseln Sie zum Abschnitt **Then** (Dann), und wählen Sie dort eine Aktion aus. Im folgenden Beispiel wird eine Bildüberprüfung erstellt und eine Markierung zugewiesen. Optional können Sie einen alternativen Pfad („Else“, Sonst) hinzufügen und auch dafür eine Aktion festlegen.

![Workflowaktion definieren](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Speichern des Workflows

Notieren Sie sich den Workflownamen. Sie benötigen ihn zum Starten eines Moderationsauftrags mit der Workflow-API (siehe unten). Speichern Sie den Workflow schließlich über die Schaltfläche **Speichern** oben auf der Seite.

## <a name="test-the-workflow"></a>Testen des Workflows

Nachdem Sie nun einen benutzerdefinierten Workflow definiert haben, testen Sie ihn mit Beispielinhalten. Navigieren Sie zu **Workflows**, und wählen Sie die entsprechende Schaltfläche **Workflow ausführen** aus.

![Workflowtest](images/image-workflow-execute.PNG)

Speichern Sie dieses [Beispielbild](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) auf Ihrem lokalen Laufwerk. Wählen Sie dann **Datei(en) auswählen** aus, und laden Sie das Bild in den Workflow hoch.

![Screenshot: Bild einer Läuferin und ein Zitat im Vordergrund auf dem Bild](images/sample-text.jpg)

### <a name="track-progress"></a>Nachverfolgen des Status

Sie können den Status des Workflows im nächsten Popupfenster anzeigen.

![Workflowausführung nachverfolgen](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Überprüfen der Workflowaktion

Wechseln Sie zur Registerkarte **Bild**  unter **Überprüfung**, und vergewissern Sie sich, dass eine neu erstellte Bildüberprüfung vorhanden ist.

![Überprüfen von Bildern](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde erläutert, wie Sie Moderationsworkflows über das [Prüfungstool](https://contentmoderator.cognitive.microsoft.com) von Content Moderator einrichten und verwenden. Sehen Sie sich als Nächstes die [Anleitung für die API-Konsole](../try-review-api-workflow.md) an, um zu erfahren, wie Workflows programmgesteuert erstellt werden.
