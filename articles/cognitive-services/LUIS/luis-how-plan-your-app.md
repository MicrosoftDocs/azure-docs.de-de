---
title: Planen Ihrer App – LUIS
description: Beschreiben Sie kurz relevante App-Absichten und -Entitäten, und erstellen Sie dann Ihre Anwendungspläne in Language Understanding Intelligent Service (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 66df23466694fe8b9caea4e56565cde6d8fd7416
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018955"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planen Ihres LUIS-App-Schemas mit Motivdomäne und Datenextraktion

Ein LUIS-App-Schema enthält [Absichten](luis-glossary.md#intent) und [Entitäten](luis-glossary.md#entity), die für Ihre [Motivdomäne](luis-glossary.md#domain) relevant sind. Die Absichten klassifizieren [Benutzeräußerungen](luis-glossary.md#utterance), und die Entitäten extrahieren Daten aus den Benutzeräußerungen.

## <a name="identify-your-domain"></a>Identifizieren Ihrer Domäne

Eine LUIS-App baut auf einer Motivdomäne auf. Beispielsweise könnten Sie eine Reise-App erstellen, die Buchungen von Tickets, Flügen, Hotels und Mietwagen ausführt. Eine andere App könnte Inhalte im Zusammenhang mit Sportaktivitäten, dem Nachverfolgen von Fitnessübungen und dem Festlegen von Zielen bereitstellen. Durch die Identifizierung der Domäne können Sie Wörter oder Ausdrücke finden, die für Ihre Domäne relevant sind.

> [!TIP]
> LUIS bietet [vordefinierte Domänen](./howto-add-prebuilt-models.md) für viele häufige Szenarien. Überprüfen Sie, ob Sie eine vordefinierte Domäne als Ausgangspunkt für Ihre App verwenden können.

## <a name="identify-your-intents"></a>Identifizieren Ihrer Absichten

Überlegen Sie sich, welche [Absichten](luis-concept-intent.md) wichtig für die Aufgabe Ihrer Anwendung sind.

Beispiel: eine Reise-App mit Funktionen zum Buchen eines Flugs und Überprüfen des Wetters am Ziel des Benutzers. Sie können für diese Aktionen die Absichten `BookFlight` und `GetWeather` definieren.

In einer komplexeren App mit umfassenderen Funktionen gibt es weitere Absichten, und Sie sollten diese sorgfältig definieren, damit sie nicht zu spezifisch sind. Beispielsweise müssen möglicherweise `BookFlight` und `BookHotel` separate Absichten sein, aber `BookInternationalFlight` und `BookDomesticFlight` können zu ähnlich sein.

> [!NOTE]
> Als bewährte Methode sollten Sie nur so viele Absichten verwenden, wie zum Ausführen der Funktionen Ihrer App erforderlich sind. Wenn Sie zu viele Absichten definieren, wird es für LUIS schwieriger, Äußerungen richtig zu klassifizieren. Wenn Sie zu wenige definieren, sind sie möglicherweise so allgemein gehalten, dass sie sich überschneiden.

Wenn Sie nicht die Gesamtabsicht identifizieren müssen, fügen Sie alle Beispielbenutzeräußerungen zur Absicht `None` hinzu. Wenn sich bei Ihrer App die Notwendigkeit weiterer Absichten herausstellt, können Sie diese später erstellen.

## <a name="create-example-utterances-for-each-intent"></a>Erstellen von Beispieläußerungen für jede Absicht

Vermeiden Sie zunächst, zu viele Äußerungen für jede Absicht zu erstellen. Nachdem Sie die Absichten festgelegt haben, können Sie pro Absicht jeweils 15 bis 30 Beispieläußerungen erstellen. Jede Äußerung sollte sich von den zuvor bereitgestellten Äußerungen unterscheiden. Eine gute Vielfalt in den Äußerungen umfasst die Gesamtzahl der Wörter, die Wortwahl, die Verbform und die [Interpunktion](luis-reference-application-settings.md#punctuation-normalization).

Weitere Informationen finden Sie unter [Grundlegendes zu guten Äußerungen für LUIS-Apps](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identifizieren Ihrer Entitäten

Identifizieren Sie in den Beispieläußerungen die Entitäten, die extrahiert werden sollen. Um einen Flug zu buchen, benötigen Sie Informationen wie Ziel, Datum, Fluggesellschaft, Ticketkategorie und Reiseklasse. Erstellen Sie Entitäten für diese Datentypen, und markieren Sie dann die [Entitäten](luis-concept-entity-types.md) in den Beispieläußerungen. Entitäten sind wichtig, um eine Absicht umzusetzen.

Wenn Sie festlegen, welche Entitäten in Ihrer App verwendet werden sollen, bedenken Sie, dass es verschiedene Typen von Entitäten zum Aufzeichnen der Beziehungen zwischen Objekttypen gibt. Unter [Entitäten in LUIS](luis-concept-entity-types.md) finden Sie weitere Details zu den verschiedenen Typen.

> [!TIP]
> LUIS bietet [vordefinierte Entitäten](./howto-add-prebuilt-models.md) für gängige, interaktive Benutzerszenarios. Verwenden Sie vorgefertigte Entitäten als Ausgangspunkt für die Anwendungsentwicklung.

## <a name="resolution-with-intent-or-entity"></a>Absicht oder Entität?

In vielen Fälle, insbesondere bei natürlicher Sprache, enthalten die Äußerungen der Benutzer mehr als eine Funktion oder Absicht. Hierfür gilt die Faustregel, dass die Darstellung der Ausgabe in Absichten und Entitäten erfolgen kann. Die Darstellung sollte den Aktionen der Clientanwendung zuordenbar sein und muss nicht auf die Absichten beschränkt sein.

Das Konzept, dass Aktionen (in der Regel als Absichten bezeichnet) auch als Entitäten erfasst und in diesem Format in die JSON-Ausgabe eingefügt werden, sodass sie einer bestimmten Aktion zugeordnet werden können, wird als **Intentities** (Absichtsentitäten) bezeichnet. Die _Negation_ wird häufig verwendet, um diese Abhängigkeit von Absicht und Entität für die vollständige Extraktion zu nutzen.

Sehen Sie sich die folgenden beiden Äußerungen an, deren Wortwahl sich zwar ähnelt, aber die unterschiedliche Ergebnisse hervorbringen:

|Äußerung|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

Anstelle von zwei separaten Absichten können Sie eine Absicht mit einer `FlightAction`-Entität für Machine Learning erstellen. Die Machine-Learning-Entität sollte die Einzelheiten der Aktion für eine Buchungs- und Stornierungsanfrage sowie für einen Start- und einen Zielort extrahieren.

Die Entität `FlightAction` wäre nach folgendem Pseudoschema für Entitäten und Subentitäten für maschinelles Lernen strukturiert:

* FlightAction
    * Aktion
    * Origin
    * Destination

Fügen Sie den Subentitäten einige Eigenschaften hinzu, um die Extraktion zu optimieren. Wählen Sie Eigenschaften anhand des zu erwartenden Vokabulars in Benutzeräußerungen und anhand der Werte aus, die in der Vorhersageantwort zurückgegeben werden sollen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einführung in den Entwicklungslebenszyklus von LUIS](luis-concept-app-iteration.md)