---
title: Hinzufügen von Geplauder zu einer QnA Maker-Wissensdatenbank
titleSuffix: Azure Cognitive Services
description: Das Hinzufügen von persönlichem Geplauder zu Ihrem Bot macht ihn unterhaltsamer und interessanter, wenn Sie eine Wissensdatenbank erstellen. Mit QnA Maker können Sie auf einfache Weise ein vordefiniertes Dataset mit wichtigen Geplauderelementen in Ihrer Wissensdatenbank hinzufügen.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 5d410e1015b751743c171adabda1d5bcbe68b491
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65541002"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Hinzufügen von Geplauder zu einer Wissensdatenbank

Das Hinzufügen von Geplauder zu Ihrem Bot macht ihn unterhaltsamer und interessanter. Das Geplauderfeature von QnA Maker ermöglicht Ihnen, auf einfache Weise ein vordefiniertes Dataset mit wichtigen Geplauderelementen in Ihrer Wissensdatenbank (KB) hinzufügen. Dies kann ein Ausgangspunkt für die Persönlichkeit Ihres Bots sein und Ihnen die Zeit und die Kosten ersparen, diese Elemente von Grund auf neu zu schreiben.  

Dieses Dataset enthält rund 100 Geplauderszenarios mit den Stimmen mehrerer Personen wie „Professionell“, „Freundlich“ oder „Witzig“. Wählen Sie die Persona aus, die dem Sprachstil Ihres Bots am nächsten kommt. Bei einer Benutzerabfrage versucht QnA Maker, sie mit der nächsten bekannten Geplauder-QnA abzugleichen.  

Einige Beispiele für die verschiedenen Persönlichkeiten finden Sie unten. Sie können alle Persönlichkeiten-[Datasets](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) einschließlich Details der Persönlichkeiten anzeigen.

Für die Benutzerabfrage von `When is your birthday?` hat jede Persönlichkeit eine stilisierte Antwort:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Persönlichkeit|Beispiel|
|--|--|
|Professionell|Das Alter trifft auf mich nicht wirklich zu.|
|Freundlich|Ich habe nicht wirklich ein Lebensalter.|
|Witzig|Ich bin alterslos.|
|Mitfühlend|Ich habe kein Alter.|
|Begeistert|Ich bin ein Bot, also habe ich kein Alter.|
||

> [!NOTE]
> Unterstützung für Geplauder ist zurzeit nur in englischer Sprache verfügbar. 

## <a name="add-chit-chat-during-kb-creation"></a>Hinzufügen von Geplauder während der Erstellung der Wissensdatenbank
Während der Erstellung der Wissensdatenbank besteht nach dem Hinzufügen Ihrer Quell-URLs und Dateien eine Option zum Hinzufügen von Geplauder. Wählen Sie die Persönlichkeit aus, die Sie als Grundlage für das Geplauder verwenden möchten. Wenn Sie kein Geplauder hinzufügen möchten oder bereits Unterstützung für Geplauder in Ihren Datenquellen verwenden, wählen Sie **Keine** aus. 
   
![Hinzufügen von Geplauder während der Erstellung](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>Hinzufügen von Geplauder zu einer vorhandenen Wissensdatenbank
Wählen Sie Ihre Wissensdatenbank aus, und navigieren Sie zur Seite **Einstellungen**. Dort finden Sie einen Link zu allen Geplauderdatasets im entsprechenden **TSV**-Format. Laden Sie die gewünschte Persönlichkeit herunter, und laden Sie sie dann als Dateiquelle hoch. Achten Sie darauf, dass Sie das Format oder die Metadaten nicht bearbeiten, wenn Sie die Datei herunterladen und hochladen. 
  
![Hinzufügen von Geplauder zu einer vorhandenen Wissensdatenbank](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Bearbeiten von Geplauderfragen und -antworten
Beim Bearbeiten Ihrer Wissensdatenbank wird eine neue Quelle für Geplauder basierend auf der Persönlichkeit angezeigt, die Sie ausgewählt haben. Sie können jetzt geänderte Fragen hinzufügen oder die Antworten wie bei jeder anderen Quelle bearbeiten. 

![Bearbeiten von Geplauder-QnAs](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Wählen Sie zum Anzeigen der Metadaten auf der Symbolleiste **Ansichtsoptionen** aus, und wählen Sie dann **Metadaten anzeigen** aus.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Hinzufügen zusätzlicher Geplauderfragen und -antworten
Sie können neue Geplauder-QnAs hinzufügen, die nicht im vordefinierten Dataset enthalten sind. Stellen Sie sicher, dass Sie kein QnA-Paar duplizieren, das bereits im Geplauderdataset enthalten ist. Wenn Sie neue Geplauder-QnAs hinzufügen, werden diese Ihrer **redaktionellen** Quelle hinzugefügt. Um sicherzustellen, dass der Ranker versteht, dass es sich um Geplauder handelt, fügen Sie das Metadaten-Schlüssel-Wertpaar „Editorial: chit-chat“ hinzu, wie in der folgenden Abbildung gezeigt:
   
![![Add chit-chat QnAs](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Löschen von Geplauder aus einer vorhandenen Wissensdatenbank
Wählen Sie Ihre Wissensdatenbank aus, und navigieren Sie zur Seite **Einstellungen**. Ihre spezifische Geplauderquelle wird als Datei mit dem ausgewählten Persönlichkeitsnamen aufgelistet. Sie können diese als Quelldatei löschen.

![Löschen von Geplauder aus der Wissensdatenbank](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Importieren einer Knowledge Base](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Weitere Informationen 

[Übersicht über QnA Maker](../Overview/overview.md)
