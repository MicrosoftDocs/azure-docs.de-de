---
title: Worum handelt es sich bei Language Understanding (LUIS)?
description: Language Understanding (LUIS) ist ein cloudbasierter API-Dienst, der maschinelles Lernen auf Konversationstext in natürlicher Sprache anwendet, um die Bedeutung vorherzusagen und Informationen zu extrahieren.
keywords: Azure, künstliche Intelligenz, KI, Verarbeitung natürlicher Sprache, NLP, Verstehen natürlicher Sprache, NLU, LUIS, Konversations-KI, KI Chatbot, NLP KI, Azure LUIS
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/23/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: fb36ab8a86a89d6383f93ad58c23956472841de2
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95539259"
---
# <a name="what-is-language-understanding-luis"></a>Worum handelt es sich bei Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) ist ein cloudbasierter Konversations-KI-Dienst, der benutzerdefinierte Machine Learning-Intelligenz auf natürliche Konversationssprachtexte eines Benutzers anwendet, um die allgemeine Bedeutung vorherzusagen sowie relevante und detaillierte Informationen abzurufen.

Bei einer Clientanwendung für LUIS handelt es sich um eine beliebige Konversationsanwendung, die zum Durchführen einer Aufgabe mit einem Benutzer in natürlicher Sprache kommuniziert. Beispiele für Clientanwendungen sind Social Media-Apps, KI-Chatbots und sprachfähige Desktopanwendungen.

![Konzeptionelle Abbildung dreier Clientanwendungen, die mit Cognitive Services Language Understanding (LUIS) arbeiten](./media/luis-overview/luis-entry-point.png "Konzeptionelle Abbildung dreier Clientanwendungen, die mit Cognitive Services Language Understanding (LUIS) arbeiten")

## <a name="use-luis-in-a-chat-bot"></a>Verwendung von LUIS in einen Chatbot

<a name="Accessing-LUIS"></a>

Sobald die Azure LUIS-App veröffentlicht ist, sendet eine Clientanwendung Äußerungen (Text) an die LUIS-Endpunkt-[API][endpoint-apis] für die Verarbeitung von natürlicher Sprache und empfängt die Ergebnisse als JSON-Antworten. Eine gängige Clientanwendung für LUIS ist ein Chatbot.


![Konzeptionelle Abbildungen von LUIS in Zusammenarbeit mit dem Chatbot zur Vorhersage von Benutzertext mithilfe des Verstehens natürlicher Sprache](./media/luis-overview/LUIS-chat-bot-request-response.svg "Konzeptionelle Abbildungen von LUIS in Zusammenarbeit mit dem Chatbot zur Vorhersage von Benutzertext mithilfe des Verstehens natürlicher Sprache")

|Schritt|Aktion|
|:--|:--|
|1|Die Clientanwendung sendet die _Benutzeräußerung_ (Text in den Worten des Benutzers) „Ich möchte meinen Personalverantwortlichen anrufen“ als HTTP-Anforderung an den LUIS-Endpunkt.|
|2|LUIS ermöglicht Ihnen das Erstellen eigener Sprachmodelle, um Ihrer Anwendung Intelligenz zu verleihen. Maschinell erlernte Sprachmodelle verwenden den unstrukturierten Eingabetext des Benutzers und geben eine Antwort im JSON-Format mit der wichtigsten Absicht, `HRContact`, zurück. Die JSON-Endpunktantwort enthält mindestens die Abfrageäußerung und die Absicht mit der höchsten Bewertung. Sie kann auch Daten wie die Entität _Kontakttyp_ extrahieren.|
|3|Die Clientanwendung verwendet die JSON-Antwort für Entscheidungen darüber, wie die Anforderungen des Benutzer erfüllt werden. Diese Entscheidungen können eine Entscheidungsstruktur im Frameworkcode des Bots und Aufrufe anderer Dienste umfassen. |

Die LUIS-App stellt Intelligenz bereit, damit die Clientanwendung intelligente Entscheidungen treffen kann. Die Auswahlmöglichkeiten werden nicht von LUIS bereitgestellt.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>Verstehen natürlicher Sprache (Natural Language Understanding, NLU)

[LUIS bietet künstliche Intelligenz (KI)](artificial-intelligence.md "LUIS bietet künstliche Intelligenz (KI)") in Form von NLU, einem Teil der KI für die Verarbeitung natürlicher Sprache.

Ihre LUIS-App enthält ein domänenspezifisches Modell für natürliche Sprache. Sie können die LUIS-App mit einem vordefinierten Domänenmodell beginnen, ein eigenes Modell erstellen oder Teile einer vordefinierten Domäne mit Ihren eigenen benutzerdefinierten Informationen mischen.

* **Vordefiniertes Modell**: LUIS verfügt über eine Vielzahl vordefinierter Domänenmodelle, einschließlich Absichten, Äußerungen und vordefinierter Entitäten. Sie können die vordefinierten Entitäten nutzen, ohne die Absichten und Äußerungen des vordefinierten Modells verwenden zu müssen. [Vordefinierte Domänenmodelle](./howto-add-prebuilt-models.md "Vordefinierte Domänenmodelle") enthalten den gesamten Entwurf und bieten eine hervorragende Möglichkeit für ein schnelle Verwendung von LUIS.

* **Benutzerdefiniertes Modell** LUIS bietet Ihnen mehrere Möglichkeiten, eigene benutzerdefinierte Modelle zu definieren, einschließlich Absichten und Entitäten. Entitäten umfassen Machine Learning-Entitäten, bestimmte oder literale Entitäten sowie eine Kombination von literalen und Machine Learning-Entitäten.

Informieren Sie sich ausführlicher über [KI für NLP](artificial-intelligence.md "NLP") und den LUIS-spezifischen Bereich NLU.

## <a name="step-1-design-and-build-your-model"></a>Schritt 1: Entwerfen und Erstellen des Modells

Entwerfen Sie Ihr Modell mit Kategorien von Benutzerabsichten, die als **[Absichten](luis-concept-intent.md "Absichten")** bezeichnet werden. Jede Absicht erfordert Beispiele für **[Äußerungen](luis-concept-utterance.md "Äußerungen")** von Benutzern. Jede Äußerung kann Daten enthalten, die mit [Machine Learning-Entitäten](luis-concept-entity-types.md#effective-machine-learned-entities "Machine Learning-Entitäten") extrahiert werden müssen.

|Beispieläußerung eines Benutzers|Intent|Extrahierte Daten|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|FlugBuchen|Seattle|
|`When does your store open?`|GeschäftszeitenUndStandorte|öffnen|
|`Schedule a meeting at 1pm with Bob in Distribution`|BesprechungPlanen|13 Uhr, Robert|

Erstellen Sie das Modell mit den [Dokumenterstellungs](https://go.microsoft.com/fwlink/?linkid=2092087 "authoring")-APIs, dem **[LUIS-Portal](https://www.luis.ai "LUIS-Portal")** oder mit beidem. Erfahren Sie mehr über das Erstellen mit dem [Portal](get-started-portal-build-app.md "Portal") und den [SDK-Clientbibliotheken](azure-sdk-quickstart.md "SDK-Clientbibliotheken").

## <a name="step-2-get-the-query-prediction"></a>Schritt 2: Abrufen der Abfragevorhersage

Nachdem Ihr App-Modell trainiert und für den Endpunkt veröffentlicht wurde, sendet eine Clientanwendung (etwa ein Chatbot) Äußerungen an die API des [Vorhersageendpunkts](https://go.microsoft.com/fwlink/?linkid=2092356 "endpoint"). Die API wendet zu Analysezwecken das Modell auf die Äußerung an und antwortet mit Vorhersageergebnissen im JSON-Format.

Die JSON-Endpunktantwort enthält mindestens die Abfrageäußerung und die Absicht mit der höchsten Bewertung. Sie kann auch Daten wie die folgende Entität **Kontakttyp** und die allgemeine Stimmung extrahieren.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Schritt 3: Verbessern der Modellvorhersage

Nachdem die LUIS-App veröffentlicht wurde und echte Benutzeräußerungen empfängt, ermöglicht LUIS zum Verbessern der Vorhersagegenauigkeit [aktives Lernen](luis-concept-review-endpoint-utterances.md "Aktives Lernen") anhand von Endpunktäußerungen. Überprüfen Sie diese Vorschläge im Rahmen der regelmäßigen Wartungsarbeiten in Ihrem Entwicklungslebenszyklus.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Entwicklungslebenszyklus und -tools
LUIS bietet Tools, eine Versionsverwaltung und die Zusammenarbeit mit anderen LUIS-Autoren für die Integration in den vollständigen [Entwicklungslebenszyklus](luis-concept-app-iteration.md "Lebenszyklus der Entwicklung").

LUIS (Language Understanding ) kann als REST-API mithilfe einer HTTP-Anforderung mit allen Produkten, Diensten oder Frameworks eingesetzt werden. LUIS bietet außerdem Clientbibliotheken (SDKs) für verschiedene Programmiersprachen. Informieren Sie sich ausführlicher über die bereitgestellten [Entwicklerressourcen](developer-reference-resource.md "Entwicklerressourcen").

Tools zum schnellen und einfachen Nutzen von LUIS mit einem Bot:
* [LUIS-CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "LUIS CLI") Mit dem NPM-Paket sind Erstellungen und Vorhersagen entweder mit einem eigenständigen Befehlszeilentool oder als Import möglich.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen") LUISGen ist ein Tool zum Generieren von stark typisiertem C#- und TypeScript-Quellcode aus einem exportierten LUIS-Modell.
* [Dispatch](https://aka.ms/dispatch-tool "Dispatch") ermöglicht die Verwendung mehrerer LUIS- und QnA Maker-Apps aus einer übergeordneten App mithilfe eines Verteilermodells.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown") LUDown ist ein Befehlszeilentool, mit dem Sprachmodelle für Ihren Bot verwaltet werden können.

## <a name="integrate-with-a-bot"></a>Integrieren in einen Bot

Verwenden Sie [Azure Bot Service](/azure/bot-service/ "Azure Bot Service") mit [Microsoft Bot Framework](https://dev.botframework.com/ "Microsoft Bot Framework"), um einen Chatbot zu erstellen und bereitzustellen. Verwenden Sie zum Entwerfen und Entwickeln das GUI-Tool [Composer](/composer/ "Composer") oder [funktionierende Botbeispiele](https://github.com/microsoft/BotBuilder-Samples "Funktionierende Botbeispiele") für die wichtigsten Botszenarien.

## <a name="integrate-with-other-cognitive-services"></a>Integrieren in andere Cognitive Services-Dienste

Weitere Cognitive Services, die mit LUIS verwendet werden:
* [QnA Maker](../QnAMaker/overview/overview.md "QnA Maker") ermöglicht die Kombination mehrerer Texttypen in einer Wissensdatenbank für Fragen und Antworten.
* Der [Spracherkennungsdienst](../Speech-Service/overview.md "Speech-Dienst") konvertiert Anforderungen in gesprochener Sprache in Text.

LUIS stellt Funktionen der Textanalyse als Teil Ihrer vorhandenen LUIS-Ressourcen bereit. Diese Funktionen umfassen [Stimmungsanalyse](luis-how-to-publish-app.md#configuring-publish-settings "Stimmungsanalyse") und [Schlüsselbegriffserkennung](luis-reference-prebuilt-keyphrase.md "oder Entitätserkennung") mit der vorgefertigten Entität „keyPhrase“.

## <a name="learn-with-the-quickstarts"></a>Lernen mit den Schnellstartanleitungen

Erfahren Sie mit praktischen Schnellstarts, in denen das [Portal](get-started-portal-build-app.md "Portal") und die [SDK-Clientbibliotheken](azure-sdk-quickstart.md "SDK-Clientbibliotheken") verwendet werden, mehr über LUIS.


## <a name="deploy-on-premises-using-docker-containers"></a>Lokales Bereitstellen unter Verwendung von Docker-Containern

[Verwenden Sie LUIS-Container](luis-container-howto.md), um API-Features lokal bereitzustellen. Mithilfe dieser Docker-Container können Sie den Dienst näher an Ihre Daten heranbringen, um Compliance- oder Sicherheitsanforderungen zu erfüllen oder anderen betrieblichen Anforderungen gerecht zu werden.

## <a name="next-steps"></a>Nächste Schritte

* [Neuerungen](whats-new.md "Neues") beim Dienst und bei der Dokumentation
* [Planen Ihrer App](luis-how-plan-your-app.md "Planen der App") mit [Absichten](luis-concept-intent.md "Absichten") und [Entitäten](luis-concept-entity-types.md "entities")
* [Abfragen des Vorhersageendpunkts](luis-get-started-get-intent-from-browser.md "Abfragen des Vorhersageendpunkts")
* [Entwicklerressourcen](developer-reference-resource.md "Entwicklerressourcen") für LUIS.

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/