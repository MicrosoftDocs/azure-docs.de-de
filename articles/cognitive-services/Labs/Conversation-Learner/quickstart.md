---
title: Erstellen eines Unterhaltungslernmodells mithilfe von Node.js – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie Sie ein Unterhaltungslernmodell mithilfe von Node.js erstellen.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: cc071d59a387c8ae4982eacbce6812526f447788
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388766"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Erstellen eines Unterhaltungslernmodells mithilfe von Node.js

Ein Unterhaltungslernmodul vereinfacht das Erstellen von Bots. Es ermöglicht einen hybriden Entwicklungsworkflow mit selbst geschriebenem Code und maschinellem Lernen, um die Codemenge zu reduzieren, die für das Schreiben von Bots erforderlich ist. Bestimmte feste Teile Ihres Modells, z.B. das Überprüfen, ob der Benutzer angemeldet ist, oder das Durchführen einer API-Anforderung zur Überprüfung des Lagerbestands, können weiterhin programmiert werden. Andere Änderungen an der Status- und Aktionsauswahl können jedoch von Beispieldialogen gelernt werden, die vom Domänenexperten oder Entwickler bereitgestellt werden.

## <a name="invitation-required"></a>Erforderliche Einladung

*An invitation is required to access Project Conversation Learner.* (Eine Einladung ist erforderlich, um auf das Projekt „Unterhaltungslernmodul“ zuzugreifen.)

Das Projekt „Unterhaltungslernmodul“ besteht aus einem SDK, das Sie zu Ihrem Bot hinzufügen, und aus einem Clouddienst, auf den die SDK für das maschinelle Lernen zugreift.  Derzeit erfordert der Zugriff auf den Clouddienst für das Projekt „Unterhaltungslernmodul“ eine Einladung.  Wenn Sie noch nicht eingeladen wurden, können Sie [eine Einladung anfordern](https://aka.ms/conversation-learner-request-invite).  Wenn Sie keine Einladung erhalten haben, können Sie nicht auf die Cloud-API zugreifen.

## <a name="prerequisites"></a>Voraussetzungen

- Node 8.5.0 oder höher und npm 5.3.0 oder höher. Installieren Sie diese über [https://nodejs.org](https://nodejs.org).
  
- LUIS-Erstellungsschlüssel:

  1. Melden Sie sich bei [https://www.luis.ai](https://www.luis.ai) an.

  2. Klicken Sie auf Ihren Namen in der oberen rechten Ecke, und klicken Sie dann auf „Einstellungen“.

  3. Der Erstellungsschlüssel wird auf der Ergebnisseite angezeigt.

  (Ihr LUIS-Erstellungsschlüssel erfüllt zwei Funktionen.  Einerseits dient er als Erstellungsschlüssel für das Unterhaltungslernmodul.  Andererseits verwendet das Unterhaltungslernmodul LUIS für das Extrahieren von Entitäten, und der LUIS-Erstellungsschlüssel wird verwendet, um LUIS-Modelle in Ihrem Auftrag zu erstellen.)

- Google Chrome-Webbrowser: Installieren Sie diesen über [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html).

- Git: Installieren Sie dieses Feature über [https://git-scm.com/downloads](https://git-scm.com/downloads).

- VSCode: Installieren Sie dieses Feature über [https://code.visualstudio.com/](https://code.visualstudio.com/). Beachten Sie, dass es empfohlen wird, jedoch nicht erforderlich ist.

## <a name="quick-start"></a>Schnellstart 

1. Installieren und Erstellen:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Während `npm install` können Sie ignorieren, wenn der Fehler `gyp ERR! stack Error: Can't find Python executable` auftritt.

2. Konfigurieren:

   Erstellen Sie im Verzeichnis `cl-bot-01` eine Datei namens `.env`.  Der Datei sollte Folgendes beinhalten:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Starten des Bots:

    ```
    npm start
    ```

    Dadurch wird der generische leere Bot in `cl-bot-01/src/app.ts` ausgeführt.

3. Öffnen Sie den Browser, und navigieren Sie zu `http://localhost:3978`.

Sie verwenden nun das Unterhaltungslernmodul und können ein Unterhaltungslernmodell erstellen und trainieren.  

> [!NOTE]
> Am Anfang ist das Projekt „Unterhaltungslernmodul“ nur über eine Einladung verfügbar.  Wenn `http://localhost:3978/ui` den HTTP-Fehler `403` anzeigt, bedeutet das, dass Ihr Konto nicht eingeladen wurde.  [Fordern Sie eine Einladung an](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Tutorials, Demos und Wechseln zwischen Bots

Durch die obigen Anweisungen wurde der generische leere Bot gestartet.  So führen Sie stattdessen ein Tutorial oder einen Demobot aus:

1. Wenn Sie die Webbenutzeroberfläche des Unterhaltungslernmoduls geöffnet haben, kehren Sie zur Modellliste unter `http://localhost:3978/ui/home` zurück.
    
2. Wenn ein anderer Bot ausgeführt wird (wie `npm start` oder `npm run demo-pizza`), beenden Sie diesen.  Sie müssen den Benutzeroberflächenprozess nicht beenden oder den Webbrowser schließen.

3. Führen Sie einen Demobot über die Befehlszeile aus (siehe Schritt 2).  Folgende Demos sind verfügbar:

   ```bash
   npm run tutorial-general
   npm run tutorial-entity-detection
   npm run tutorial-session-callbacks
   npm run tutorial-api-calls
   npm run tutorial-hybrid
   npm run demo-password
   npm run demo-pizza
   npm run demo-storage
   ```

4. Wenn Sie dies nicht bereits getan haben, wechseln Sie zur Webbenutzeroberfläche des Unterhaltungslernmoduls in Chrome, indem Sie `http://localhost:3978/ui/home` laden. 

5. Klicken Sie auf „Tutorials importieren“, und wählen Sie auf der Conversation Learner-Benutzeroberfläche das Demomodell aus, das der von Ihnen gestarteten Demo entspricht.

Die Quelldateien für die Demos befinden sich unter `cl-bot-01/src/demos`.

## <a name="create-a-bot-which-includes-back-end-code"></a>Erstellen eines Bots mit Back-End-Code

1. Wenn Sie die Webbenutzeroberfläche des Unterhaltungslernmoduls geöffnet haben, kehren Sie zur Modellliste unter `http://localhost:3978/ui/home` zurück.
    
2. Wenn ein Bot ausgeführt wird (z.B. `npm run demo-pizza`), beenden Sie diesen.  Sie müssen den Benutzeroberflächenprozess nicht beenden oder den Webbrowser schließen.

3. Bearbeiten Sie bei Bedarf den Code in `cl-bot-01/src/app.ts`.

4. Erstellen und starten Sie den Bot neu:

    ```bash    
    npm run build
    npm start
    ```

5. Wenn Sie dies nicht bereits getan haben, wechseln Sie zur Webbenutzeroberfläche des Unterhaltungslernmoduls in Chrome, indem Sie `http://localhost:3978/ui/home` laden. 

6. Erstellen Sie ein neues Unterhaltungslernmodell auf der Benutzeroberfläche, und beginnen Sie mit dem Training.

7. Wiederholen Sie die obigen Schritte ab Schritt 2, um Codeänderungen in `cl-bot-01/src/app.ts` vorzunehmen.

## <a name="vscode"></a>VSCode

In VSCode gibt es Ausführungskonfigurationen für jede Demo und für den „leeren“ Bot unter `cl-bot-01/src/app.ts`.  Öffnen Sie den Ordner `cl-bot-01` in VSCode.

## <a name="advanced-configuration"></a>Erweiterte Konfiguration

Es gibt eine `.env.example`-Vorlagendatei, die darstellt, welche Umgebungsvariablen Sie festlegen sollten, um die Beispiele zu konfigurieren.

Sie können diese Ports anpassen, um Konflikte zwischen anderen Diensten zu vermeiden, die auf Ihrem Computer ausgeführt werden, indem Sie eine `.env`-Datei zum Stamm des Projekts hinzufügen:

```bash
cp .env.example .env
```

Dadurch wird die Standardkonfiguration verwendet, mit der der Bot lokal ausgeführt werden kann. Nun können Sie das Unterhaltungslernmodul verwenden.  (Später müssen einige Änderungen an dieser Datei vorgenommen werden, um Ihren Bot in Bot Framework bereitzustellen.)

## <a name="support"></a>Support

- Verwenden Sie für Fragen zu [Stack Overflow](https://stackoverflow.com) das Tag „microsoft cognitive“.
- Fordern Sie ein Feature auf der [User Voice-Seite](https://aka.ms/conversation-learner-uservoice) an.
- Öffnen Sie ein Problem in unserem [GitHub-Repository](https://github.com/Microsoft/ConversationLearner-Samples).

## <a name="contributing"></a>Mitwirken

Für dieses Projekt wird der [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Verhaltenskodex für Microsoft Open Source) beachtet. Weitere Informationen finden Sie in den [häufig gestellten Fragen zum Verhaltenskodex](https://opensource.microsoft.com/codeofconduct/faq/). Sie können sich auch an [opencode@microsoft.com](mailto:opencode@microsoft.com) wenden, wenn Sie weitere Fragen oder Kommentare haben.

## <a name="source-repositories"></a>Quellrepositorys

- [conversationlearner-samples](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hallo Welt](./tutorials/01-hello-world.md)
