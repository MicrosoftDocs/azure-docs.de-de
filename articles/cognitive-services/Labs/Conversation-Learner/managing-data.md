---
title: Verwalten von Benutzerdaten mit dem Unterhaltungslernmodul – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Erfahren Sie, wie Sie Benutzerdaten mit dem Unterhaltungslernmodul verwalten.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 7ea0b246a16ff196a4160d9822b5db15cd39a4a6
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385200"
---
# <a name="managing-user-data"></a>Verwalten von Benutzerdaten

Auf dieser Seite wird beschrieben, was vom Conversation Learner-Clouddienst protokolliert wird, wenn Dialoge mit Endbenutzern geführt werden.  Außerdem wird beschrieben, wie Sie Protokollen des Unterhaltungslernmoduls Benutzer-IDs zuordnen, sodass Sie alle einem bestimmten Benutzer zugeordneten Protokolle abrufen oder löschen können.

## <a name="overview-of-end-user-data-logging"></a>Übersicht über die Protokollierung von Endbenutzerdaten

Standardmäßig protokolliert der Conversation Learner-Clouddienst Interaktionen zwischen Endbenutzern und Ihrem Bot.  Diese Protokolle sind für die Verbesserung Ihres Bots wichtig. Dadurch können Sie Fälle identifizieren, in denen Ihr Bot die falsche Entität extrahiert oder die falsche Aktion ausgewählt hat.  Diese Fehler können dann korrigiert werden, indem Sie auf der Benutzeroberfläche zur Seite „Dialoge protokollieren“ wechseln, die Korrekturen vornehmen und diesen korrigierten Dialog als neuen Trainingsdialog speichern. Weitere Informationen finden Sie im Tutorial zum Protokollieren von Dialogen.

## <a name="how-to-disable-logging"></a>So deaktivieren Sie die Protokollierung

Sie können steuern, ob Unterhaltungen mit Endbenutzern auf der Seite „Einstellungen“ für Ihr Unterhaltungslernmodell aktiviert sind.  Es gibt ein Kontrollkästchen für „Unterhaltungen protokollieren“.  Wenn Sie dieses Kontrollkästchen deaktivieren, werden die Unterhaltungen mit Endbenutzern nicht protokolliert.

## <a name="what-is-logged"></a>Protokollierte Inhalte 

In den protokollierten Dialogen speichert das Unterhaltungslernmodul Benutzereingaben, Entitätswerte, ausgewählte Aktionen und Zeitstempel für jede Runde.  Diese Protokolle werden für einen bestimmte Zeitraum gespeichert und danach verworfen (Einzelheiten finden Sie auf der Hilfeseite unter „Standardwert und Grenzen“).  

Das Unterhaltungslernmodul erstellt eine eindeutige ID für jeden protokollierten Dialog.  Das Unterhaltungslernmodul speichert jedoch *keine* Benutzer-ID mit den protokollierten Dialogen.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Zuordnen einer Benutzer-ID zu protokollierten Dialogen

Es ist oft wichtig, den protokollierten Dialogen die ID des Benutzers zuordnen zu können, um beispielsweise in der Lage zu sein, protokollierte Dialoge eines bestimmten Benutzers abzurufen oder zu löschen.  Weil das Unterhaltungslernmodul keine Benutzer-ID speichert, muss diese Zuordnung durch den Code des Entwicklers verwaltet werden.  

Um diese Zuordnung zu erstellen, rufen Sie die ID des protokollierten Dialogs in `EntityDetectionCallback` ab, und speichern Sie dann die Zuordnung zwischen der Benutzer-ID und diesem protokollierten Dialog im Speicher Ihres Bots.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific data store, store an association
    // between your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>Header für HTTP-Aufrufe

Fügen Sie in allen nachstehenden HTTP-Aufrufen den folgenden Header hinzu:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

Dabei ist `<LUIS_AUTHORING_KEY>` der LUIS-Erstellungsschlüssel, der für den Zugriff auf Ihre Unterhaltungslernanwendungen verwendet wird.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>So rufen Sie Rohdaten für einen protokollierten Dialog ab

Zum Abrufen der Rohdaten für einen protokollierten Dialog können Sie den folgenden HTTP-Aufruf verwenden:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Dabei ist `<appId>` der globale eindeutige Bezeichner (Globally Unique Identifier, GUID) für dieses Unterhaltungslernmodell, und `<logDialgoId>` ist die ID des protokollierten Dialogs, den Sie abrufen möchten.  

> [!NOTE]
> Protokollierte Dialoge können vom Entwickler bearbeitet und dann als Trainingsdialoge gespeichert werden.  Wenn dies erfolgt, speichert das Unterhaltungslernmodul die ID des protokollierten Quelldialogs mit dem Trainingsdialog.  Darüber hinaus kann ein Trainingsdialog auf der Benutzeroberfläche „verzweigt“ werden. Wenn einem Trainingsdialog die ID des protokollierten Quelldialogs zugeordnet ist, werden Verzweigungen dieses Trainingsdialogs mit der gleichen ID des protokollierten Dialogs gekennzeichnet.

Führen Sie die folgenden Schritte aus, um alle von einem protokollierten Dialog abgeleiteten Trainingsdialoge abzurufen.

Rufen Sie zuerst alle Trainingsdialoge ab:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Dabei ist `<appId>` der GUID für dieses Unterhaltungslernmodell.  

Dadurch werden alle Trainingsdialoge zurückgegeben.  Suchen Sie in dieser Liste nach der zugeordneten `sourceLogDialogId`, und notieren Sie sich die zugeordnete `trainDialogId`. 

Um einen einzelnen Trainingsdialog nach ID abzurufen, gehen Sie wie folgt vor:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Dabei ist `<appId>` der globale eindeutige Bezeichner (Globally Unique Identifier, GUID) für dieses Unterhaltungslernmodell, und `<trainDialogId>` ist die ID des Trainingsdialogs, den Sie abrufen möchten.  

## <a name="how-to-delete-a-logged-dialog"></a>So löschen Sie einen protokollierten Dialog

Wenn Sie einen protokollierten Dialog anhand seiner ID löschen möchten, können Sie den folgenden HTTP-Aufruf verwenden:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Dabei ist `<appId>` der globale eindeutige Bezeichner (Globally Unique Identifier, GUID) für dieses Unterhaltungslernmodell, und `<logDialogId>` ist die ID des protokollierten Dialogs, den Sie löschen möchten. 

Wenn Sie einen Trainingsdialog anhand seiner ID löschen möchten, können Sie den folgenden HTTP-Aufruf verwenden:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Dabei ist `<appId>` der globale eindeutige Bezeichner (Globally Unique Identifier, GUID) für dieses Unterhaltungslernmodell, und `<trainDialogId>` ist die ID des Trainingsdialogs, den Sie löschen möchten. 
