---
title: Protokollieren von Dialogen in einem Unterhaltungslernmodell – Microsoft Cognitive Services | Microsoft-Dokumentation
titleSuffix: Azure
description: Erfahren Sie, wie Sie Dialoge in einem Unterhaltungslernmodell protokollieren.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: f1e161fa05a77682d0b5eb1c6c21975ac87028a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66387660"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Protokollieren von Dialogen in einem Unterhaltungslernmodell

In diesem Tutorial erfahren Sie, wie Sie Protokolldialoge verwenden, um Conversation Learner-Modelle auf der Grundlage aufgezeichneter Interaktionen mit echten Benutzern besser zu trainieren.

## <a name="video"></a>Video

[![Protokolldialoge: Tutorial (Vorschauversion)](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Requirements (Anforderungen)
Für dieses Tutorial muss der allgemeine Tutorialbot ausgeführt werden.

    npm run tutorial-general

Außerdem benötigen Sie das Wettermodell, das in vorherigen Tutorials erstellt wurde.

## <a name="details"></a>Details
Protokolldialoge sind Protokolle der Interaktion Ihres Bots mit Endbenutzern. Mithilfe dieser Protokolldialoge können Sie Entitätsbezeichnungen und die Aktionsauswahl korrigieren, um die Leistung des Modells und des gesamten Systems zu verbessern.

## <a name="steps"></a>Schritte

Klicken Sie auf der Webbenutzeroberfläche auf „Tutorials importieren“, und wählen Sie das Modell „Tutorial-11-LogDialogs“ aus.

Dieses Modell enthält eine Entität namens „City“ (Stadt) sowie Aktionen zur Beantwortung von Fragen hinsichtlich des Wetters in der entsprechenden Stadt. Das Modell wurde mithilfe von zwei Trainingsdialogen trainiert. Die Leistungserwartungen sind daher nicht besonders hoch. Das Modell ließe sich durch zusätzliches Training sowie durch Interaktionen mit echten Benutzern verbessern.

### <a name="create-a-new-conversation"></a>Erstellen einer neuen Unterhaltung

1. Klicken Sie im linken Bereich auf „Log Dialogs“ (Protokolldialoge) und anschließend auf die Schaltfläche „New Log Dialog“ (Neuer Protokolldialog).
2. Geben Sie im Chatbereich unter „Type your message...“ (Nachricht eingeben...) die Zeichenfolge „Austin weather forecast“ (Wettervorhersage für Austin) ein.
3. Klicken Sie auf die Schaltfläche „Done Testing“ (Tests abgeschlossen).
4. Klicken Sie in der Liste auf den Protokolldialog „Austin weather forecast“ (Wettervorhersage für Austin).
5. Klicken Sie im Chatbereich auf die Äußerung „Austin weather forecast“ (Wettervorhersage für Austin).
6. Klicken Sie auf „Austin“ und anschließend in der Entitätsliste auf „City“ (Stadt).
7. Klicken Sie auf die Schaltfläche „Änderung übermitteln“.
    - Diese Änderung des Entitätswerts hat weitere Änderungen der Unterhaltung zur Folge, da sich ein neuer Entitätswert im Speicher befindet. Nachfolgende Aktionen werden dadurch wahrscheinlich ungültig. Das betrifft insbesondere Aktionen im Zusammenhang mit der Entität „City“ (Stadt).
8. Klicken Sie auf die Äußerung „Which city?“ (Welche Stadt?) im Chatbereich.
9. Wählen Sie die Antwort „The weather in Austin is probably sunny.“ (Das Wetter in Austin ist wahrscheinlich sonnig.) aus.
10. Klicken Sie auf die Schaltfläche „Save As Train Dialog“ (Als Trainingsdialog speichern).
    - Das Training wird sofort gestartet.

![](../media/T11_logdialog.png)

Ein letzter Hinweis: Das Feature für die Unterhaltungsprotokollierung kann bei Bedarf deaktiviert werden. Deaktivieren Sie hierzu in den Einstellungen das Kontrollkästchen „Log Conversations“ (Unterhaltungen protokollieren).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Rückruf zur Entitätserkennung](./12-entity-detection-callback.md)
