---
title: Verwenden von Versionsmarkierungen in einem Conversation Learner-Modell – Azure Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Hier erfahren Sie, wie Sie Versionsverwaltung und Markierungen in einem Unterhaltungslernmodell verwenden.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 4067c7fb43cc200b8f49dbc14151c69a188e4e8e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475709"
---
# <a name="how-to-use-version-tagging"></a>Verwenden von Versionsmarkierungen

Dieses Tutorial veranschaulicht das Markieren von Versionen Ihres Conversation Learner-Modells und das Festlegen der Live-Version.  

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial ist die Verwendung von Bot Framework Emulator zum Erstellen von Protokolldialogen erforderlich, nicht die Protokolldialog-Webbenutzeroberfläche.  

Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden:

    npm run tutorial-general

## <a name="details"></a>Details

Markierte Versionen des Modells sind statisch. Sie können nicht bearbeitet oder geändert werden. Wenn Sie Ihr Modell bearbeiten, bearbeiten Sie immer die Masterversion. Wenn Sie ein neues Tag hinzufügen, erfasst Conversation Learner eine Momentaufnahme zum jeweiligen Zeitpunkt. 

Ihr Bot verwendet die Version des Modells, die Sie als die Live-Version ausgewählt haben. Darin enthaltene Konversationen können jedoch nur angezeigt werden, wenn das Bearbeitungstag auf „Master“ festgelegt ist. Wenn die Eigenschaft „Editing Tag“ (Bearbeitungstag) des Modells nicht auf „Master“ festgelegt ist, können Sie die Momentaufnahme des Modells zwar anzeigen, aber nicht ändern.

## <a name="steps"></a>Schritte

### <a name="install-the-bot-framework-emulator"></a>Installieren von Bot Framework Emulator

1. Navigieren Sie zu [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
2. Laden Sie den Emulator herunter, und installieren Sie ihn.

### <a name="create-a-model"></a>Modellerstellung

1. Klicken Sie auf der Startseite in der Modellliste auf die Schaltfläche `New Model`.
2. Geben Sie Feld `Name` den Text „Tutorial-18-Versioning“ ein, und drücken Sie die EINGABETASTE.
4. Klicken Sie im linken Bereich auf „Einstellungen“.
5. Kopieren Sie den Inhalt des Felds „CONVERSATION_LEARNER_MODEL_ID“ in die Zwischenablage.

### <a name="configure-the-emulator"></a>Konfigurieren des Emulators

1. Öffnen Sie im Stammordner von Conversation Learner die ENV-Datei.
2. Fügen Sie der ENV-Datei wie folgt eine Zeile hinzu:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Starten Sie den Unterhaltungslerndienst neu, indem Sie die Eingabeaufforderung schließen und diesen Befehl noch einmal ausführen:
    - `npm run tutorial-general`
4. Erstellen Sie in Bot Framework-Emulator eine neue Botkonfiguration, und legen Sie die Endpunkt-URL auf `http://localhost:3978/api/messages` fest.

### <a name="version-1"></a>Version 1

Wir erstellen eine einzelne Aktion für Version 1.

1. Klicken Sie im linken Bereich der Web-Benutzeroberfläche auf „Aktionen“ und dann auf die `New Action` Schaltfläche.
2. Geben Sie im Feld „Bot's response...“ (Antwort des Bots...) die Zeichenfolge „hi there (version 1)“ (Hallo Ihr (Version 1)) ein.
3. Klicken Sie auf die Schaltfläche `Save`.

Jetzt markieren wir dies als „Version 1“ des Modells.

1. Klicken Sie im linken Bereich auf „Einstellungen“ und dann auf das Symbol ![](../media/tutorial18_version_tags.PNG)„Versionstags“, damit die Schaltfläche `New Tag` angezeigt wird, auf die Sie klicken müssen.
    - Nennen Sie sie „Version 1“.
1. Wählen Sie in der Dropdownliste „Live Tag“ (Live-Tag) die Option „Version 1“ aus.  
    - Jetzt verwenden Kanäle, die diesen Bot nutzen, „Version 1“ des Modells.
    - Die Entitäten, Aktionen und Trainingsdialoge dieses Modells der Version 1 können nicht mehr geändert werden.
    - Wenn Sie „Version 1“ als Bearbeitungstag auswählen, können Sie dieses Modell NUR anzeigen, aber nicht bearbeiten.
    - Behalten Sie „Master“ als Bearbeitungstag bei, da es sich dabei um die einzige Version des Modells handelt, die bearbeitet werden kann.

Jetzt wird „Version 1“ im Raster „Version Tags“ (Versionstags) angezeigt.

### <a name="version-2"></a>Version 2

Jetzt bearbeiten wir unser Modell, um es von Version 1 zu unterscheiden.

1. Klicken Sie im linken Bereich auf „Aktionen“.
2. Klicken Sie im Raster „Aktionen“ auf „hi there (version 1)“ (Hallo Ihr (Version 1)).
3. Ändern Sie das Feld „Bot's response...“ (Antwort des Bots...) zu „hi there (version 2)“ (Hallo Ihr (Version 2)).
4. Klicken Sie auf die Schaltfläche `Save`.
5. Klicken Sie auf die Schaltfläche `New Action`.
6. Ändern Sie das Feld „Bot's response...“ (Antwort des Bots...) zu „Bye-bye (Version 2)“.

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Vergewissern Sie sich, dass Bot Framework Emulator „Version 1“ verwendet.

1. Geben Sie in Bot Framework Emulator die Nachricht „Hey there“ (Hallo) ein.
2. Sie sehen, dass der Bot mit „hi there (version 1)“ (Hallo Ihr (Version 1)) antwortet.
    - Dies bestätigt, dass Version 1 die Live-Version ist.

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Anzeigen der Gesprächsprotokolle auf der Webbenutzeroberfläche von Conversation Learner

1. Klicken Sie im linken Bereich auf „Log Dialogs“ (Protokolldialoge).
    - Wenn keine Dialoge angezeigt werden, klicken Sie auf die Schaltfläche zum Aktualisieren.
2. Beachten Sie das Tag „Version 1“ im Raster.
3. Klicken Sie im Raster auf „hi there (version 1)“ (Hallo Ihr (Version 1)).

> [!NOTE]
> Wir können Korrekturen vornehmen, indem wie aus allen derzeit verfügbaren Conversation Learner-Funktionen wählen, aber diese Bearbeitungen erfolgen für „Master“ und nicht für „Version 1“.

Sie haben jetzt gesehen, wie die Versionsverwaltung funktioniert und wie Sie mithilfe von Bot Framework Emulator mit dem Bot interagieren können.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Enum-Entitäten und Set_Entity-Aktionen](./tutorial-enum-set-entity.md)
