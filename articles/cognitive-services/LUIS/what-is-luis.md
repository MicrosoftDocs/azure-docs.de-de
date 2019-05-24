---
title: Was ist Language Understanding (LUIS) – Azure Cognitive Services | Microsoft-Dokumentation
description: Language Understanding (LUIS) ist ein cloudbasierter API-Dienst, der benutzerdefinierte Machine Learning-Intelligenz auf natürliche Konversationssprachtexte eines Benutzers anwendet, um die allgemeine Bedeutung vorherzusagen sowie relevante und detaillierte Informationen abzurufen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 2bb50f985b99a3fb62e7b8a61bb0f9ce2dd402e1
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522007"
---
# <a name="what-is-language-understanding-luis"></a>Worum handelt es sich bei Language Understanding (LUIS)?

Language Understanding (LUIS) ist ein cloudbasierter API-Dienst, der benutzerdefinierte Machine Learning-Intelligenz auf natürliche Konversationssprachtexte eines Benutzers anwendet, um die allgemeine Bedeutung vorherzusagen sowie relevante und detaillierte Informationen abzurufen. 

Bei einer Clientanwendung für LUIS handelt es sich um eine beliebige Konversationsanwendung, die zum Durchführen einer Aufgabe mit einem Benutzer in natürlicher Sprache kommuniziert. Beispiele für Clientanwendungen sind Social Media Apps, Chatbots und sprachfähige Desktopanwendungen.  

![Schematische Darstellung von 3 Clientanwendungen, die mit Cognitive Services Language Understanding (LUIS) arbeiten](./media/luis-overview/luis-entry-point.png "Schematische Darstellung von 3 Clientanwendungen, die mit Cognitive Services Language Understanding (LUIS) arbeiten")

## <a name="use-luis-in-a-chat-bot"></a>Verwendung von LUIS in einen Chatbot

<a name="Accessing-LUIS"></a>

Sobald die LUIS-App veröffentlicht ist, sendet eine Clientanwendung Äußerungen (Text) an die LUIS-Endpunkt-[API][endpoint-apis] für die Verarbeitung von natürlicher Sprache und empfängt die Ergebnisse als JSON-Antworten. Eine gängige Clientanwendung für LUIS ist ein Chatbot.


![Schematische Darstellung von LUIS in Verwendung mit einem Chatbot zum Vorhersagen von Benutzertext mit Verstehen natürlicher Sprache (Natural Language Understanding, NLP)](./media/luis-overview/luis-overview-process-2.png "Schematische Darstellung von LUIS in Verwendung mit einem Chatbot zum Vorhersagen von Benutzertext mit Verstehen natürlicher Sprache (Natural Language Understanding, NLP)")

|Schritt|Aktion|
|:--|:--|
|1|Die Clientanwendung sendet die _Benutzeräußerung_ (Text in den Worten des Benutzers) „Ich möchte meinen Personalverantwortlichen anrufen“ als HTTP-Anforderung an den LUIS-Endpunkt.|
|2|LUIS wendet das erlernte Modell auf den Text in natürlicher Sprache an, um ein intelligentes Verständnis der Benutzereingabe bereitzustellen. LUIS gibt eine Antwort in JSON-Formatierung mit der am höchsten eingestuften Absicht „HRContact“ zurück. Die JSON-Endpunktantwort enthält mindestens die Abfrageäußerung und die Absicht mit der höchsten Bewertung. Sie kann auch Daten wie die Entität „Kontakttyp“ extrahieren.|
|3|Die Clientanwendung verwendet die JSON-Antwort für Entscheidungen darüber, wie die Anforderungen des Benutzer erfüllt werden. Diese Entscheidungen können eine Entscheidungsstruktur im Frameworkcode des Bots und Aufrufe von anderen Diensten umfassen. |

Die LUIS-App stellt Intelligenz bereit, damit die Clientanwendung intelligente Entscheidungen treffen kann. Die Auswahlmöglichkeiten werden nicht von LUIS bereitgestellt. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Verarbeitung natürlicher Sprache

Eine LUIS-App enthält ein domänenspezifisches Modell für natürliche Sprache. Sie können die LUIS-App mit einem vordefinierten Domänenmodell beginnen, ein eigenes Modell erstellen oder Teile einer vordefinierten Domäne mit Ihren eigenen benutzerdefinierten Informationen mischen.

* **Vordefiniertes Modell**: LUIS verfügt über eine Vielzahl vordefinierter Domänenmodelle, einschließlich Absichten, Äußerungen und vordefinierter Entitäten. Sie können die vordefinierten Entitäten nutzen, ohne die Absichten und Äußerungen des vordefinierten Modells verwenden zu müssen. [Vordefinierte Domänenmodelle](luis-how-to-use-prebuilt-domains.md) enthalten den gesamten Entwurf und bieten eine hervorragende Möglichkeit für ein schnelle Verwendung von LUIS.

* **Benutzerdefinierte Entitäten**: LUIS bietet Ihnen mehrere Möglichkeiten zum Festlegen eigener benutzerdefinierter Absichten und Entitäten, einschließlich auf maschinellem Lernen basierender Entitäten, bestimmter oder literaler Entitäten sowie einer Kombination von literalen und auf maschinellem Lernen basierenden Entitäten.

## <a name="build-the-luis-model"></a>Erstellen des LUIS-Modells
Erstellen Sie das Modell mit den [Dokumenterstellungs](https://go.microsoft.com/fwlink/?linkid=2092087)-APIs oder über das LUIS-Portal.

Das LUIS-Modell beginnt mit Kategorien von Benutzerabsichten, die als **[Absichten](luis-concept-intent.md)** bezeichnet werden. Jede Absicht erfordert Beispiele für **[Äußerungen](luis-concept-utterance.md)** von Benutzern. Jede Äußerung kann eine Vielzahl von Daten enthalten, die mit **[Entitäten](luis-concept-entity-types.md)** extrahiert werden müssen. 

|Beispieläußerung eines Benutzers|Absicht|Entitäten|
|-----------|-----------|-----------|
|„Einen Flug nach __Seattle__ buchen?“|FlugBuchen|Seattle|
|„Wann __öffnet__ Ihr Geschäft?“|GeschäftszeitenUndStandorte|öffnet|
|„Eine Besprechung mit __Robert__ im Vertrieb um __13 Uhr__ planen“|BesprechungPlanen|13 Uhr, Robert|

## <a name="query-prediction-endpoint"></a>Abfragen eines Vorhersageendpunkts

Nachdem das Modell erstellt und am Endpunkt veröffentlicht wurde, sendet die Clientanwendung Äußerungen an die API des veröffentlichten [Vorhersageendpunkts](https://go.microsoft.com/fwlink/?linkid=2092356). Die API wendet das Modell zur Analyse auf den Text an. Die API gibt die Vorhersageergebnisse in einem JSON-Format zurück.  

Die JSON-Endpunktantwort enthält mindestens die Abfrageäußerung und die Absicht mit der höchsten Bewertung. Sie kann auch Daten wie die folgende **Kontakttyp**-Entität extrahieren. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Verbessern der Modellvorhersage

Sobald ein LUIS-Modell veröffentlicht wurde und echte Benutzeräußerungen empfängt, bietet LUIS mehrere Methoden zum Verbessern der Vorhersagegenauigkeit: [Aktives Lernen](luis-concept-review-endpoint-utterances.md) von Endpunktäußerungen, [Begriffslisten](luis-concept-feature.md) für die Einbeziehung von Domänenwörtern und [Muster](luis-concept-patterns.md) zum Reduzieren der Anzahl erforderlicher Äußerungen.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Lebenszyklus der Entwicklung
LUIS verfügt über Tools, Versionsverwaltung und Kollaboration mit anderen LUIS-Autoren, und diese Elemente werden auf Ebene der Clientanwendung und des Sprachmodells in den vollständigen Entwicklungslebenszyklus integriert. 

## <a name="implementing-luis"></a>Implementieren von LUIS
LUIS kann als REST-API mit allen Produkten, Diensten oder Frameworks eingesetzt werden, die HTTP-Anforderungen stellen. Die folgende Liste enthält die am häufigsten mit LUIS verwendeten Microsoft-Produkte und -Dienste.

Die am häufigsten verwendete Clientanwendung für LUIS ist:
* [Web-App-Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0) erstellt schnell einen LUIS-fähigen Chatbot für die Kommunikation mit einem Benutzer über die Texteingabe. Verwendet [Bot Framework][bot-framework] Version [3.x](https://github.com/Microsoft/BotBuilder) oder [4.x](https://github.com/Microsoft/botbuilder-dotnet) für eine vollständige Bot-Oberfläche.

Tools zum schnellen und einfachen Nutzen von LUIS mit einem Bot:
* [LUIS-CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) Mit dem NPM-Paket sind Erstellungen und Vorhersagen entweder mit einem eigenständigen Befehlszeilentool oder als Import möglich. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) LUISGen ist ein Tool zum Generieren von stark typisiertem C#- und TypeScript-Quellcode aus einem exportierten LUIS-Modell.
* [Dispatch](https://aka.ms/dispatch-tool) ermöglicht die Verwendung mehrerer LUIS- und QnA Maker-Apps aus einer übergeordneten App mithilfe eines Verteilermodells.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown ist ein Befehlszeilentool, mit dem Sprachmodelle für Ihren Bot verwaltet werden können.

Weitere Cognitive Services, die mit LUIS verwendet werden:
* [QnA Maker][qnamaker] ermöglicht die Kombination mehrerer Texttypen in einer Wissensdatenbank basierend auf Fragen und Antworten.
* [Bing-Rechtschreibprüfungs-API](../bing-spell-check/proof-text.md) bietet Textkorrektur vor der Vorhersage. 
* Der [Spracherkennungsdienst](../Speech-Service/overview.md) konvertiert Anforderungen in gesprochener Sprache in Text. 
* Das [Unterhaltungslernmodul](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) ermöglicht Ihnen mit LUIS ein schnelleres Erstellen von Bot-Konversationen.
* [Projekt: Chat mit Profil](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) für Smalltalk mit dem Bot.

Beispiele mit LUIS:
* GitHub-Repository zu [Konversations-KI](https://github.com/Microsoft/AI)
* Azure-Beispiele zu [Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding)

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine neue LUIS-App mit einer [vordefinierten](luis-get-started-create-app.md) oder [benutzerdefinierten](luis-quickstart-intents-only.md) Domäne. [Fragen Sie den Endpunkt der Vorhersage](luis-get-started-cs-get-intent.md) einer öffentlichen IoT-App ab.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
