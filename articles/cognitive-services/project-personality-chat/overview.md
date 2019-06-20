---
title: 'Was ist „Projekt: Chat mit Profil“?'
titlesuffix: Azure Cognitive Services
description: Dieser Artikel bietet einen Überblick über das Azure-Projekt „Individualisierter Chat“, eine cloudbasierte API zur Erweiterung der Konversationsmöglichkeiten Ihres Bots.
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: c7f7a8c65717acd5a19e92b7e0437dc4b8628909
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "62103615"
---
# <a name="what-is-project-personality-chat"></a>Was ist „Projekt: Chat mit Profil“?

Das Projekt „Individualisierter Chat“ verbessert die Konversationsfähigkeiten Ihres Bots durch den Umgang mit Smalltalk im Einklang mit einer ausgeprägten, ausgewählten Persönlichkeit. Der individualisierte Chat verwendet Absichtsklassifizierer, um häufige Smalltalkabsichten zu identifizieren, und generiert Antworten, die zu einer Persönlichkeit passen. Basierend auf der Absicht und den Zuverlässigkeitswerten wählt der Bot entweder die beste Antwort aus einer bereitgestellten redaktionellen Basis aus oder generiert sie in Echtzeit mithilfe von Deep Neural Networks (DNNs). Sie können unter drei Standardpersonas wählen. Das Personamodell gibt Antworten zurück, die der ausgewählten Persönlichkeit am ähnlichsten sind.

Ein anpassbares Redaktionssystem für gängige Smalltalkabfragen ist verfügbar. Es kann ganz einfach mit dem Microsoft Bot Framework SDK integriert werden. Dieses SDK erspart Ihnen den Zeit- und Kostenaufwand, der mit dem Neuerstellen solcher Abfragen verbunden ist.

## <a name="getting-started-with-project-personality-chat"></a>Erste Schritte mit dem Projekt „Individualisierter Chat“

Sie können die Labseite zum Projekt „Individualisierter Chat“ besuchen und mit der verfügbaren Demo chatten sowie frühzeitigen Zugriff beantragen, wenn der Dienst verfügbar ist.
Schon heute können Sie die anpassbare nur redaktionelle Bibliothek auch über das Microsoft Bot Framework SDK in Ihre Bots integrieren. <br>
[Beispiele: Integrieren des individualisierten Chats in einen Bot](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Testen der Bibliothek für individualisierten Chat](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Generieren von Antworten mithilfe neuronaler Netzwerke

Der individualisierte Chat verwendet Deep Learning-Modelle, um allgemeine Konversationsmuster zu erlernen und Antworten zu generieren. Das Antwortgenerierungsmodell ist rekurrentes neuronales Netz (RNN). Dieses Modell wird mit Millionen von Gesprächen trainiert, wodurch es Muster menschlicher Interaktionen versteht und lernt. Mit den erlernten Satzmustern werden neue Abfragen gebildet und Antworten generiert. Beim Generieren dieser neuen Antwort durchsucht das Modell ein "Schreibvokabular" von Wörtern und wählt n-beste erste Wörter für die Antwort aus. Mit der Strahlsuche wird weiterhin nach den n-besten zweiten Wörtern für jedes der generierten ersten Wörter gesucht. Eine Antwort gilt als vollständig, wenn das Modell das Wort für das „Satzende“ (End of Sentence, EOS) auswählt. Sobald alle Antworten vorliegen, wählt es die n-besten Antworten basierend auf dem Wahrscheinlichkeitswert für die vollständige Antwort aus.

Das Modell lernt, kontextuell passende Wendungen zu erzeugen, die die richtige „Struktur“ aufweisen. Eine Frage wie „Möchten Sie jetzt sprechen?“ sagt z.B. recht viel über die Struktur einer plausiblen Antwort aus: Sie wird wahrscheinlich mit einer Paraphrase wie „ja“ oder „nein“ beginnen, und das Pronomen lautet wahrscheinlich „ich“. Eine Antwort mit „nein“ beinhaltet wahrscheinlich eher eine höfliche Erklärung usw.

Das System versucht, ein Sprachmodell für die grundlegende Struktur der Konversation zu lernen. Diese Struktur sollte es ermöglichen, dass die Antworten teilweise durch externe Einschränkungen wie Themen, Persönlichkeit usw. beeinflusst werden können.  Da diese Einschränkungen aus breiten Mustern gelernt werden, eignen sie sich für die Anwendung für Smalltalk (aber nicht ausschließlich).

![Sequenz-zu-Sequenz-Modell für Antwortgenerierung](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Persönlichkeitsmodellierung

 Bei jedem datengesteuerten Gesprächsmodell besteht die Herausforderung darin, konsistent eine stimmige „Persönlichkeit“ bereitzustellen. PERSONA ist definiert als die Persönlichkeit, die während der Gesprächsinteraktion erlebt wird. Eine Persona kann als eine aus Elementen der Identität, des Sprachverhaltens und des Interaktionsstils bestehende Persönlichkeit angesehen werden. In der aktuellen Version des individualisierten Chats liegt der Fokus auf dem Sprachverhalten und Interaktionsstil.

Dieses Modell stellt jeden einzelnen Sprecher als einen Vektor oder eine Einbettung dar. Es codiert Informationen, die es vom Sprecher erhält, die den Inhalt und die Art der jeweiligen Antwort beeinflussen. Das Modell lernt dann Sprecherdarstellungen basierend auf dem Gesprächsinhalt, der von verschiedenen Sprechern beigesteuert wird. Die Sprecher mit ähnlichen Antworten neigen dazu, ähnliche Einbettungen aufzuweisen, die benachbarte Positionen im Vektorraum einnehmen. Auf diese Weise tragen die Trainingsdaten von Sprechern in der Nähe im Vektorraum dazu bei, die Generalisierungsfähigkeit des Sprechermodells zu erhöhen. Das Modell bündelt effektiv Sprecher, die ähnliche Darstellungen im Vektorraum haben, zu einem Personacluster mit einer „Persona-ID“.

Für die Standardpersonas werden Attribute und bereitgestellte Antworten verwendet, um den am besten übereinstimmenden Sprechercluster zu ermitteln. Dieser Cluster wird dann als Persona-ID für jede dieser Standardpersönlichkeiten ausgewählt. Eine kontinuierliche Anpassung kann für jede Art von Persönlichkeit erfolgen, indem eine Reihe von Bot-/Markenantworten verwendet werden. Es werden dann Gespräche geführt, die die Persona des einzelnen Sprechers wie sprachliches Antwortverhalten und andere Hauptmerkmale genau emulieren.

![Personamodellierung mit Sprecherclustern](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>Verstehen der Smalltalkabsicht

Der individualisierte Chat verwendet Absichtsklassifizierer, um eine Antwort für Smalltalkabsichten sicherzustellen. Diese Klassifizierer verursachen keinen Konflikt mit Aufgaben oder Informationsabfragen. Ein allgemeiner Chatklassifizierer bestimmt, ob die Abfrage eine Smalltalk- oder Plauderabsicht verfolgt. Sie entscheidet, indem sie lexikalische und semantische Klassifizierer verwendet und deren Bewertungen kombiniert. Diese Absichten werden anhand von Gesprächsdaten (als positive Absichtsproben) und Such-/Aufgabenabfragen (für negative Absichtsproben) trainiert.

Andere Klassifizierer für Unterabsichten werden verwendet, um die Unterklassen von Smalltalk zu identifizieren und die Arten von Smalltalk einzuschränken, auf die der Dienst reagiert, z.B. Begrüßungen, Komplimente, Meinungen usw. Diese Deep Learning-Klassifizierer konvertieren mit CDSSM (Convolutional Deep Structure Semantic Model) alle Abfragen in Vektoren. Sie werden mithilfe von Zehntausenden von bereitgestellten Abfragen für die Unterabsichten trainiert. Der Klassifizierer gleicht dann eine Abfrage mit vorhandenen, identifizierten Absichtsklassen ab, indem er die nächstliegende Übereinstimmung im Vektorraum ermittelt.

Mehrere Steuerelement wurden verwendet, um unpassende Antworten zu vermeiden. Diese basieren auf den Erfahrungen mit intelligenten Agents wie Zo. Standardmäßig ist das Projekt „Personality Chat“ darauf festgelegt, nur auf erkannte Absichten des Benutzers zu antworten. Sie sollten testen, ob das Projekt „Personality Chat“ für Ihre Situation geeignet ist. Wir freuen uns auf Ihr Feedback, wenn Sie feststellen, dass Verbesserungen vorgenommen werden können.
