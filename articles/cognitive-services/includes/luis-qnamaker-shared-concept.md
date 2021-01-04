---
title: include file
description: include file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.openlocfilehash: 78b93c05621754ae499e4ae8ca6b66c5bbfaad1b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028547"
---
Mit Cognitive Services werden zwei Dienste für die Verarbeitung natürlicher Sprache (Natural Language Processing, NLP) bereitgestellt: [Language Understanding](../luis/what-is-luis.md) und [QnA Maker](../qnamaker/overview/overview.md). Beide dienen jeweils einem anderen Zweck. Machen Sie sich damit vertraut, wann Sie diese Dienste einsetzen sollten und wie sie sich gegenseitig ergänzen.

Bei der Verarbeitung natürlicher Sprache (NLP) kann Ihre Clientanwendung, z. B. ein Chatbot, in natürlicher Sprache mit Ihren Benutzern zusammenarbeiten. Ein Benutzer gibt einen Satz oder einige Wörter ein. Der Text des Benutzers weist unter Umständen Fehler in Bezug auf Grammatik, Rechtschreibung und Satzzeichen auf. Der Cognitive Service kann den Satz des Benutzers trotzdem verarbeiten und Informationen zurückgeben, die vom Chatbot benötigt werden, um dem Benutzer zu weiterzuhelfen.

## <a name="cognitive-services-with-nlp"></a>Cognitive Services mit NLP

Language Understanding (LUIS) und QnA Maker ermöglichen die Verarbeitung natürlicher Sprache. Die Clientanwendung übermittelt Text in natürlicher Sprache. Der Dienst verarbeitet den Text und gibt ein Ergebnis zurück.

## <a name="when-to-use-each-service"></a>Einsatz der einzelnen Dienste

Mit Language Understanding (LUIS) und QnA Maker werden jeweils unterschiedliche Probleme gelöst. Mit LUIS wird die Absicht eines Benutzertexts ermittelt (als „Äußerung“ bezeichnet), und mit QnA Maker die Antwort auf einen Benutzertext (als „Abfrage“ bezeichnet).

Zum Auswählen des richtigen Diensts müssen Sie verstehen, welcher Benutzertext von der Clientanwendung bereitgestellt wird und welche Informationen die Clientanwendung vom Cognitive Service benötigt.

Wenn Ihr Chatbot den Text `How do I get to the Human Resources building on the Seattle North campus?` empfängt, können Sie anhand des unten angegebenen Diagramms ermitteln, wie der Text von den Diensten jeweils verarbeitet wird.

|Dienst|Aktion der Clientanwendung|
|--|--|
|LUIS|**Ermittelt die Absicht des Benutzers**, die im Text enthalten ist. Der Dienst gibt keine Antwort auf die Frage zurück. Dieser Text wird beispielsweise so klassifiziert, dass er mit der Absicht `FindLocation` übereinstimmt.<br>|
|QnA Maker|**Gibt die Antwort auf die Frage zurück** (aus einer benutzerdefinierten Wissensdatenbank). Für diesen Text wird beispielsweise ermittelt, dass es sich um eine Frage handelt, die mit der statischen Textantwort `Get on the #9 bus and get off at Franklin street` beantwortet werden kann.|
|||

> [!div class="mx-imgBorder"]
> ![Infografik für die Bestimmung, wann LUIS und wann QnA Maker verwendet werden sollte](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>Verwenden von LUIS

Verwenden Sie LUIS, wenn Sie im Rahmen eines Prozesses im Chatbot die Absicht der Äußerung ermitteln müssen. Wir fahren mit dem Beispieltext `How do I get to the Human Resources building on the Seattle North campus?` fort. Nachdem Sie wissen, dass der Benutzer nach einem Ort sucht, können Sie Details zur Äußerung (mit den jeweiligen Entitäten) an einen anderen Dienst übergeben, z. B. einen Transportserver, um die Antwort zu erhalten.

Sie müssen LUIS und QnA Maker nicht unbedingt kombinieren, um die Absicht zu ermitteln.

Sie können die beiden Dienste für diese Äußerung aber kombinieren, falls der Chatbot den Text basierend auf Absichten und Entitäten (über LUIS) verarbeiten und die spezifische statische Textantwort finden muss (über QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Verwenden von QnA Maker

Verwenden Sie QnA Maker, wenn Sie über eine statische Wissensdatenbank mit Antworten verfügen. Diese Wissensdatenbank ist speziell auf Ihre Anforderungen zugeschnitten und wurde anhand von Dokumenten wie PDFs und URLs erstellt.

Wir fahren mit der Beispieläußerung `How do I get to the Human Resources building on the Seattle North campus?` fort. Der Text soll als Abfrage an Ihren veröffentlichten QnA Maker-Dienst gesendet werden, und die beste Antwort soll empfangen werden.

Sie müssen LUIS und QnA Maker nicht kombinieren, um die Antwort auf die Frage zu ermitteln.

Sie können die beiden Dienste für diese Äußerung kombinieren, falls der Chatbot den Text basierend auf Absichten und Entitäten (über LUIS) verarbeiten und die Antwort finden muss (über QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Verwenden beider Dienste bei einer unvollständigen Wissensdatenbank

Wenn Sie Ihre QnA Maker-Wissensdatenbank erstellen und wissen, dass sich das Thema ändert (z. B. zeitnahe Informationen), können Sie die Dienste LUIS und QnA Maker kombinieren. So können Sie die Informationen in Ihrer Wissensdatenbank und zusätzlich LUIS nutzen, um die Absicht eines Benutzers zu ermitteln. Wenn die Clientanwendung über die Absicht verfügt, kann sie relevante Informationen von einer anderen Quelle anfordern.

Ihre Clientanwendung muss sowohl LUIS- als auch QnA Maker-Antworten auf Bewertungen überwachen. Falls die Bewertung von QnA Maker unterhalb eines beliebigen Schwellenwerts liegt, sollten Sie die von LUIS zurückgegebenen Absichts- und Entitätsinformationen verwenden, um die Informationen an einen Drittanbieterdienst zu übergeben.

Wir fahren mit dem Beispieltext `How do I get to the Human Resources building on the Seattle North campus?` fort. Angenommen, QnA Maker gibt eine niedrige Zuverlässigkeitsbewertung zurück. Verwenden Sie die von LUIS zurückgegebene Absicht (`FindLocation`) sowie alle extrahierten Entitäten, z. B. `Human Resources building` und `Seattle North campus`, um diese Informationen an einen Karten- oder Suchdienst zu senden und darüber eine andere Antwort zu erhalten.

Sie können diese Antwort des Drittanbieters für den Benutzer zur Überprüfung bereitstellen. Nachdem Sie die Genehmigung des Benutzers erhalten haben, können Sie diese Informationen QnA Maker hinzufügen, um Ihr Wissen zu erweitern.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Verwenden beider Dienste, wenn Ihr Chatbot weitere Informationen benötigt

Falls Ihr Chatbot mehr Informationen benötigt, als von den Diensten bereitgestellt werden können, können Sie mit einer Entscheidungsstruktur fortfahren. Verwenden Sie hierfür beide Dienste, und verarbeiten Sie beide Antworten in der Clientanwendung.

Nutzen Sie das Bot Framework-Tool **[Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** , um einen Prozess zu erstellen, bei dem beide Dienste verwendet werden. Mit diesem Tool wird eine übergeordnete LUIS-App für Absichten erstellt, für die LUIS und QnA Maker als untergeordnete Apps genutzt werden. [Erfahren Sie mehr](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0) zur Integration mit LUIS, QnA Maker und Bot Framework.

Verwenden Sie das Bot Builder-Beispiel **NLP mit Dispatch** in [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) oder [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), um diese Art von Chatbot zu implementieren.

## <a name="best-practices"></a>Bewährte Methoden

Implementieren der bewährten Methoden für jeden Dienst:

* Bewährte Methoden für [LUIS](../luis/luis-concept-best-practices.md)
* Bewährte Methoden für [QnA Maker](../qnamaker/concepts/best-practices.md)

## <a name="see-also"></a>Weitere Informationen

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Bot Framework-Beispiele](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure Bot Service](/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Bot-Emulator von Azure](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot Framework-Webchat](https://github.com/microsoft/BotFramework-WebChat)