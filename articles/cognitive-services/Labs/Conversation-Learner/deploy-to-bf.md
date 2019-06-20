---
title: Bereitstellen eines Unterhaltungslernmodul-Bots – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Erfahren Sie, wie Sie einen Unterhaltungslernmodul-Bot bereitstellen.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 5522f762f3893f1d67cd3755b1e022f0118cc004
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385315"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>Bereitstellen eines Unterhaltungslernmodul-Bots

In diesem Dokument wird erläutert, wie Sie einen Unterhaltungslernmodul-Bot – entweder lokal oder in Azure – bereitstellen.

## <a name="prerequisite-determine-the-model-id"></a>Voraussetzung: Ermitteln der Modell-ID 

Damit Sie einen Bot außerhalb der Benutzeroberfläche des Unterhaltungslernmodells ausführen können, müssen Sie die vom Bot verwendete Anwendungs-ID des Unterhaltungslernmoduls, also die ID des Modells für maschinelles Lernen in der Unterhaltungslernmodul-Cloud festlegen.  (Bei der Ausführung des Bots über die Benutzeroberfläche des Unterhaltungslernmodells wird die zu verwendende Anwendungs-ID dagegen von der Benutzeroberfläche ausgewählt.)  

Nachfolgend wird beschrieben, wie Sie die Modell-ID abrufen:

1. Starten Sie Ihren Bot und die Benutzeroberfläche des Unterhaltungslernmoduls.  Vollständige Anweisungen finden Sie im Leitfaden für den Schnellstart. Hier folgt eine Zusammenfassung:

    In einem Befehlsfenster:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    In einem anderen Befehlsfenster

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. Öffnen Sie den Browser, und navigieren Sie zu `http://localhost:5050`. 

3. Klicken Sie auf das Unterhaltungslernmodell, dessen ID Sie abrufen möchten.

4. Klicken Sie auf der linken Navigationsleiste auf „Einstellungen“.

5. Der GUID „Modell-ID“ wird am oberen Rand der Seite angezeigt.

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>Option 1: Bereitstellen eines Conversation Learner-Bots zur lokalen Ausführung

Hier wird ein Bot auf dem lokalen Computer bereitgestellt und gezeigt, wie Sie mit dem Bot Framework-Emulator darauf zugreifen können.

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>Konfigurieren Ihres Bots für den Zugriff außerhalb der Benutzeroberfläche des Unterhaltungslernmoduls

Fügen Sie bei lokaler Ausführung eines Bots die Anwendungs-ID zur `.env`-Datei des Bots hinzu:

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

Starten Sie dann Ihren Bot:

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

Der Bot wird jetzt lokal ausgeführt.  Sie können mit dem Bot Framework-Emulator darauf zugreifen.

### <a name="download-and-install-the-emulator"></a>Herunterladen und Installieren des Emulators

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>Verbinden des Emulators mit Ihrem Bot

1. Geben Sie in der oberen linken Ecke des Emulators im Feld für die Eingabe der Endpunkt-URL die Zeichenfolge `http://127.0.0.1:3978/api/messages` ein.  Lassen Sie die anderen Felder leer, und klicken Sie auf „Verbinden“.

2. Sie können sich jetzt mit Ihrem Bot unterhalten.

## <a name="option-2-deploy-to-azure"></a>Option 2: Bereitstellen in Azure

Sie können Ihren Unterhaltungslernmodul-Bot auf ähnliche Weise veröffentlichen wie jeden anderen Bot. Sie laden auf einer hohen Ebene Ihren Code auf eine gehostete Website hoch, legen die entsprechenden Konfigurationswerte fest und registrieren dann den Bot für verschiedene Kanäle. Ausführliche Anweisungen finden Sie in diesem Video, in dem gezeigt wird, wie Sie einen Bot mit Azure Bot Service veröffentlichen.

Sobald der Bot bereitgestellt wurde und ausgeführt wird, können Sie verschiedene Kanäle wie Facebook, Teams, Skype usw. über eine Azure Bot-Kanalregistrierung mit dem Bot verbinden. Eine Dokumentation zu diesem Prozess finden Sie unter: https://docs.microsoft.com/bot-framework/bot-service-quickstart-registration

Im Folgenden finden Sie Schrittanleitungen zum Bereitstellen eines Unterhaltungslernmodul-Bots in Azure.  Bei diesen Anweisungen wird davon ausgegangen, dass Ihre Botquelle über eine cloudbasierte Quelle wie Azure DevOps Services, GitHub, BitBucket oder OneDrive verfügbar ist und Ihr Bot für die dauerhafte Bereitstellung (Continuous Deployment) konfiguriert wird.

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

2. Erstellen Sie eine neue „Web-App-Bot“-Ressource. 

    1. Weisen Sie dem Bot einen Namen zu.
    2. Klicken Sie auf „Botvorlage“, wählen Sie „Node.js“ aus, wählen Sie „Standard“ aus, und klicken Sie dann auf die Schaltfläche „Auswählen“.
    3. Klicken Sie auf „Erstellen“, um den Web-App-Bot zu erstellen.
    4. Warten Sie, bis die Web-App-Bot-Ressource erstellt wurde.

3. Bearbeiten Sie im Azure-Portal die Web-App-Bot-Ressource, die Sie eben erstellt haben.

   1. Klicken Sie auf der linken Seite auf das Navigationselement „Anwendungseinstellungen“.
   1. Scrollen Sie nach unten zum Abschnitt „App-Einstellungen“.
   2. Fügen Sie die folgenden Einstellungen hinzu:

       Umgebungsvariable | value
       --- | --- 
       CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
       CONVERSATION_LEARNER_MODEL_ID      | Die über die Benutzeroberfläche des Unterhaltungslernmoduls unter den „Einstellungen“ für das Modell abgerufene ID (GUID)
       LUIS_AUTHORING_KEY               | LUIS-Erstellungsschlüssel für dieses Modell
       LUIS_SUBSCRIPTION_KEY            | Nicht erforderlich, aber empfohlen für veröffentlichte Bots, um die Verwendung Ihres Kontingents für die Erstellung zu vermeiden.
    
   4. Klicken Sie am oberen Rand der Seite auf „Speichern“.
   5. Öffnen Sie auf der linken Seite das Navigationselement „Build“.
   6. Klicken Sie auf „Continuous Deployment konfigurieren“. 
   7. Klicken Sie unter „Bereitstellungen“ auf das Symbol „Setup“.
   8. Klicken Sie auf „Erforderliche Einstellungen“.
   9. Wählen Sie die Quelle aus, in der Ihr Botcode verfügbar ist, und konfigurieren Sie die Quelle.
