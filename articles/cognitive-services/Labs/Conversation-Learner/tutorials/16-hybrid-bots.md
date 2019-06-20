---
title: Verwenden eines Unterhaltungslernmoduls mit anderen Botentwicklungstechnologien – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie Sie ein Unterhaltungslernmodul mit anderen Botentwicklungstechnologien verwenden.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: nitinme
ms.openlocfilehash: d6af927e395532e43c7cc51c39665e2e42ac6781
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389977"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>So verwenden Sie ein Unterhaltungslernmodul mit anderen Botentwicklungstechnologien.

In diesem Tutorial erfahren Sie, wie Sie Conversation Learner mit anderen Botentwicklungstechnologien verwenden und wie Speicher (oder der Zustand) von diesen Technologien gemeinsam genutzt werden kann. 

## <a name="video"></a>Video

[![Hybrid-Bots: Tutorial (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial ist die Verwendung des Botemulators zum Erstellen von Protokolldialogen erforderlich, nicht die Protokolldialog-Webbenutzeroberfläche. Weitere Informationen zum Einrichten von Bot Framework Emulator finden Sie [hier](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

Für dieses Tutorial muss der Hybrid-Tutorialbot ausgeführt werden.

    npm run tutorial-hybrid

## <a name="details"></a>Details

Während das Unterhaltungslernmodul die Kontrolle hat, müssen alle Zustände bezüglich der Sitzung des Unterhaltungslernmodells im Speicher-Manager des Unterhaltungslernmodells gespeichert werden. Dies ist notwendig, da das maschinelle Lernen den Zustand verwendet, um zu bestimmen, wie das Gespräch geführt werden soll. Der externe Zustand kann an das Unterhaltungslernmodul über OnSessionStartCallback weitergegeben werden, das zu Beginn der Sitzung aufgerufen wird. Der interne Zustand kann am Ende der Sitzung über „OnSessionEndCallback“ zurückgegeben werden.

Sie können sich das Unterhaltungslernmodul fast wie einen Funktionsaufruf vorstellen, der einen Anfangszustand einnimmt und Werte zurückgibt.

In diesem Beispiel erstellen Sie einen Hybrid-Bot mithilfe von zwei verschiedenen Systemen:
1. Ein Unterhaltungslernmodell <br/>
    Verwendet ein Conversation Learner-Modell, um auf der Grundlage der aktuellen Sitzung die nächste Aktion des Bots zu bestimmen. Dieser Teil des Bots akzeptiert einen Teil des Ausgangszustands (`isOpen`: gibt an, ob ein Store geöffnet oder geschlossen ist) und gibt einen anderen Zustand (`purchaseItem`: der Name eines Artikels, den der Benutzer kauft) zurück.

2. Textabgleich <br />
    Durchsucht eingehenden Text nach bestimmten Zeichenfolgen und reagiert entsprechend. Dieser Teil des Bots verwaltet die anderen Speichermechanismen der Bots und ist für den Start der CL-Sitzung verantwortlich. Insbesondere verwaltet er drei Variablen: `usingConversationLearner`, `storeIsOpen` und `purchaseItem`.

Schauen wir uns zunächst das in dieser Demo verwendete Modell an.

### <a name="open-the-demo"></a>Öffnen der Demo

Klicken Sie auf der Webbenutzeroberfläche auf „Tutorials importieren“, und wählen Sie das Modell „Tutorial-16-HybridBot“ aus.

## <a name="entities"></a>Entitäten

Öffnen Sie die Entitätenseite und beachten Sie zwei Entitäten: `isOpen` und `purchaseItem`.

Um zu verstehen, wie diese Entitäten verwendet werden, öffnen Sie die Datei: `C:\<installedpath>\src\demos\tutorialHybrid.ts`, um die Rückrufe anzusehen.

Beachten Sie, dass der Code in `OnSessionStartCallback` den Wert von `storeIsOpen` aus dem BotBuilder-Konversationsspeicher als Wert der Entität `isOpen` kopiert, sodass er für das Unterhaltungslernmodul verfügbar ist. Schauen Sie sich den folgenden Code an:

![](../media/tutorial17_sessionstart.PNG)

Ebenso kopiert der Code in `OnSessionEndCallback` (wenn die Sitzung aufgrund einer gelernten Aktivität und nicht nur aufgrund eines Timeout beendet wurde) den Wert der Entität `purchaseItem` in den BotBuilder-Speicher `purchaseItem`. Schauen Sie sich den folgenden Code an:

![](../media/tutorial17_sessionend.PNG)

Betrachten wir nun die Aktionen.

## <a name="actions"></a>Aktionen

Beachten Sie, dass das Modell über vier Aktionen verfügt.

Die gewünschten Regeln für die Aktionen sehen wie folgt aus:

- Wenn die `isOpen`-Entität festgelegt ist, wird der Bot „What would you like to buy?“ fragen. Die Antwort wird dann im `puchaseItem`-Slot gespeichert.
- Ist `isOpen` nicht festgelegt, antwortet der Bot mit „I’m sorry we’re closed“ (Wir haben leider geschlossen.).
- Die anderen beiden Aktionen sind vom Typ `END_SESSION`.
- Die Aktion END_SESSION zeigt dem Unterhaltungslernmodul an, dass die Konversation abgeschlossen wurde.

### <a name="overall-bot-logic"></a>Allgemeine Botlogik

Zuerst sehen Sie, dass wir die Kontrolle an das Unterhaltungslernmodul übergeben, wenn das Flag `usingConversationLearner` des Botzustands gesetzt wurde. Wenn dies nicht der Fall ist, übergeben wir die Kontrolle an ein anderes Element.  In diesem Beispiel zeigen wir einen einfachen Textabgleich, das könnte aber auch jede andere Bottechnologie sein, wie z.B LUIS, QnA Maker und sogar eine weitere Instanz des Unterhaltungslernmoduls.

Wir benötigen eine Möglichkeit für den Benutzer, den Store zu öffnen und zu schließen, also vergleichen wir einen String mit „open store“ und „close store“ und setzen das Flag „storeIsOpen“.

Als Nächstes benötigen wir eine Möglichkeit, um die Übergabe der Kontrolle an unser Unterhaltungslernmodell auszulösen. Beim Abgleich mit der Zeichenfolge „shop“ gehen wir wie folgt vor:
- Legen Sie das Flag `usingConversationLearner` im Speicher des Bots fest.
- Rufen Sie die Methode „StartSession“ in unserem Unterhaltungslernmodell auf.  Dadurch wird „onSessionStartCallback“ ausgelöst, wodurch der `isOpen`-Entitätswert initialisiert wird.

Siehe unten:

![](../media/tutorial17_useConversationLearner.PNG)

Wir führen auch einen Textabgleich mit „history“ durch, sodass der zuletzt gekaufte Artikel angezeigt wird.
Wenn etwas anderes eingegeben wird, werden die verfügbaren Benutzerbefehle angezeigt.

## <a name="train-dialog"></a>Trainingsdialog

Für dieses Tutorial wurde das Modell bereits vorab trainiert.  Wir werden den kompletten Bot testen, um die Auswirkung der Rückrufe zum Starten und Beenden der Sitzung in der Praxis zu sehen.

## <a name="testing-the-bot"></a>Testen des Bots

Im Gegensatz zu einzelnen Unterhaltungslernmodell-Bots können Sie dies nicht in der Benutzeroberfläche des Unterhaltungslernmoduls testen, da nur angezeigt werden kann, was vom Unterhaltungslernmodell verarbeitet wird.

### <a name="install-the-bot-framework-emulator"></a>Installieren Sie den Bot Framework-Emulator

- Navigieren Sie zu [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Laden Sie den Emulator herunter, und installieren Sie ihn.

### <a name="configure-the-emulator"></a>Konfigurieren des Emulators

- Öffnen Sie den Emulator und stellen Sie sicher, dass die URL auf denselben Port zeigt, auf dem Ihr Bot läuft. Wie: `http://localhost:3978/api/messages`

### <a name="test"></a>Test 

#### <a name="scenario-1-store-is-closed"></a>Szenario 1: Store geschlossen
1. Geben Sie „shop“ ein. Dies wird durch den Textabgleich verarbeitet und übergibt die Kontrolle an das Unterhaltungslernmodell.
2. Geben Sie „Hallo“ ein.  Das der Wert `isOpen` nicht festgelegt ist, wird der Bot „I’m sorry we’re closed“ sagen und die Sitzung beenden.

#### <a name="scenario-2-store-is-open"></a>Szenario 2: Store geöffnet
1. Geben Sie „open store“ ein.  Dadurch wird `isOpen` auf „true“ festgelegt.
1. Geben Sie „shop“ ein.
1. Geben Sie „Hallo“ ein.  Da der Wert `isOpen` auf „true“ festgelegt ist, wird der Bot „What would you like to buy?“ sagen.
1. Geben Sie „chair“ ein. „Chair“ wird im CL-Speicher als Entität `purchaseItem` gespeichert. Es wird der Rückruf zum Beenden der Sitzung aufgerufen, der diesen Wert in den Konversationsspeicher kopiert.
1. Geben Sie „history“ ein.  Der Bot sagt „You bought chair“, da dies Ihr letzter `purchaseItem` war.

## <a name="conclusion"></a>Zusammenfassung

Sie haben gelernt, wie Sie Conversation Learner mit beliebigen anderen Botentwicklungstechnologien kombinieren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Branchen und Rückgängigmachen](./17-branch-undo.md)
