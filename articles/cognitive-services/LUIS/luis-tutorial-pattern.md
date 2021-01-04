---
title: 'Tutorial: Muster: LUIS'
description: In diesem Tutorial werden Muster verwendet, um die Vorhersage von Absichten und Entitäten zu verbessern und zugleich weniger Beispieläußerungen anzugeben. Das Muster wird als Beispiel für eine Vorlagenäußerung bereitgestellt, die die Syntax zum Identifizieren von Entitäten und ignorierbarem Text enthält.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 9814304aed4d7a5f307fb2179491b0fa9635fd68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324653"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Tutorial: Hinzufügen von Formaten für allgemeine Mustervorlagen, um Vorhersagen zu verbessern

In diesem Tutorial verwenden Sie Muster, um die Vorhersage von Absichten und Entitäten zu verbessern, was Ihnen ermöglicht, weniger Beispieläußerungen anzugeben. Bei dem Muster handelt es sich um eine Vorlagenäußerung, die einer Absicht zugewiesen ist und eine Syntax zum Identifizieren von Entitäten und ignorierbarem Text enthält.

**In diesem Tutorial lernen Sie Folgendes:**

> [!div class="checklist"]
> * Erstellen eines Musters
> * Überprüfen der Verbesserungen bei Vorhersagen durch Muster
> * Kennzeichnen von Text als ignorierbar und Verschachteln innerhalb von Mustern
> * Verwenden des Testpanels zum Überprüfen des Erfolgs der Muster

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Äußerungen in Absicht und Muster

In der LUIS-APP sind zwei Arten von Äußerungen gespeichert:

* Beispieläußerungen in der Absicht
* Vorlagenäußerungen in der Absicht

Das Hinzufügen von Vorlagenäußerungen als Muster ermöglicht Ihnen, insgesamt weniger Beispieläußerungen für eine Absicht bereitzustellen.

Ein Muster wird als Kombination aus der Suche nach Übereinstimmungen mit Text und maschinellem Lernen angewendet.  Durch die Vorlagenäußerung innerhalb des Musters und die Beispieläußerungen in der Absicht erhält LUIS ein besseres Verständnis davon, welche Äußerungen zur Absicht passen.

## <a name="import-example-app-and-clone-to-new-version"></a>Importieren der Beispiel-App und Klonen in neue Version

Führen Sie die folgenden Schritte durch:

1.  Laden Sie die [App-JSON-Datei](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true) herunter, und speichern Sie sie.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie Ihr **Abonnement** und Ihre **Erstellungsressource** aus, um die Apps anzuzeigen, die dieser Erstellungsressource zugewiesen sind.
1. Importieren Sie die JSON-Datei im [LUIS-Portal](https://www.luis.ai) in eine neue App. Wählen Sie auf der Seite **Meine Apps** die Option **+ Neue App für Unterhaltung** und dann **Als JSON importieren** aus. Wählen Sie die Datei aus, die Sie im vorherigen Schritt heruntergeladen haben, und nennen Sie die App `Patterns tutorial`.

## <a name="create-new-intents-and-their-utterances"></a>Erstellen neuer Absichten samt Äußerungen

Die beiden Absichten ermitteln basierend auf dem Äußerungstext den Verwalter oder die direkten Berichte des Verwalters. Die Schwierigkeit besteht darin, dass die beiden Absichten verschiedene Dinge _bedeuten_, die meisten Wörter jedoch identisch sind. Lediglich die Wortreihenfolge ist anders. Damit die Absicht richtig vorhergesagt werden kann, müssten viele Beispiele enthalten sein.

1. Wählen Sie in der Navigationsleiste **Erstellen** aus.

1. Wählen Sie auf der Seite **Absichten** die Option **+ Erstellen** aus, um eine neue Absicht zu erstellen.

1. Geben Sie im Popupdialogfeld die Zeichenfolge `OrgChart-Manager` ein, und klicken Sie anschließend auf **Fertig**.

    ![Popupfenster zum Erstellen einer neuen Absicht](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Fügen Sie der Absicht Beispieläußerungen hinzu. Diese Äußerungen sind nicht _genau_ gleich, verfügen aber über ein Muster, das extrahiert werden kann.

    |Beispiele für Äußerungen|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

1. Klicken Sie im linken Navigationsbereich auf **Absichten**.

1. Wählen Sie **+ Erstellen** aus, um eine neue Absicht zu erstellen. Geben Sie im Popupdialogfeld die Zeichenfolge `OrgChart-Reports` ein, und klicken Sie anschließend auf **Fertig**.

1. Fügen Sie der Absicht Beispieläußerungen hinzu.

    |Beispiele für Äußerungen|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Wichtiger Hinweis zur Menge der Beispieläußerungen

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Trainieren der App vor dem Testen oder Veröffentlichen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Veröffentlichen der App, um sie vom Endpunkt aus abzufragen

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Abrufen von Absicht und Entitäten von einem Endpunkt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ersetzen Sie in der Adressleiste am Ende der URL _IHRE_ABFRAGE_ durch: `Who is the boss of Jill Jones?`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Es wurde zwar die korrekte höchste Absicht (`OrgChart-Manager`) vorhergesagt, das Ergebnis liegt jedoch nicht über 70 Prozent und somit nicht hoch genug über der nächsthöchsten Absicht. Verwenden Sie Muster, um die Bewertung der richtigen Absicht deutlich zu erhöhen (in Prozent) und den Abstand zur nächst höheren Bewertung zu vergrößern.

Lassen Sie das zweite Browserfenster geöffnet. Es wird später im Tutorial erneut benötigt.

## <a name="template-utterances"></a>Vorlagenäußerungen
Im Themenbereich Personalwesen gibt es einige häufig verwendete Methoden, um Mitarbeiterbeziehungen in Organisationen zu erfragen. Beispiel:

|Äußerungen|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Diese Äußerungen sind zu ähnlich, um jeweils ihre kontextbezogene Eindeutigkeit zu ermitteln, ohne _zahlreiche_ Beispieläußerungen anzugeben. Durch Hinzufügen eines Musters für eine Absicht lernt LUIS häufige Muster von Äußerungen für eine Absicht, ohne dass dafür zahlreiche weitere Beispieläußerungen angegeben werden müssen.

Zu den Beispielen für Vorlagenäußerungen zu dieser Absicht gehören:

|Beispiele für Vorlagenäußerungen|Bedeutung der Syntax|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|austauschbar `{EmployeeListEntity}`<br>ignorieren `[?]`|
|`Who reports to {EmployeeListEntity}[?]`|austauschbar `{EmployeeListEntity}`<br>ignorieren `[?]`|

Die Syntax `{EmployeeListEntity}` markiert die Position der Entität innerhalb der Vorlagenäußerung sowie die Entität selbst. Die optionale Syntax (`[?]`) markiert Wörter oder [Satzzeichen](luis-reference-application-settings.md#punctuation-normalization), die optional sind. LUIS gleicht die Äußerung ab und ignoriert dabei den optionalen Text in den Klammern.

Zwar sieht die Syntax nach einem regulären Ausdruck aus, es handelt sich aber nicht um einen regulären Ausdruck. Es wird nur die Syntax mit geschweiften `{}` und eckigen `[]` Klammern unterstützt. Sie können bis zu zwei Ebenen tief geschachtelt werden.

Damit ein Muster mit einer Äußerung übereinstimmt, müssen _zunächst_ die Entitäten innerhalb der Äußerung mit den Entitäten in der Vorlagenäußerung übereinstimmen. Dies bedeutet, dass die Entitäten genügend Beispiele in Beispieläußerungen mit einem hohen Vorhersagegrad aufweisen müssen, bevor Muster mit Entitäten erfolgreich sind. Allerdings hilft die Vorlage nicht dabei, Entitäten vorherzusagen, sondern nur Absichten.

**Muster ermöglichen Ihnen, weniger Beispieläußerungen bereitzustellen. Doch wenn die Entitäten nicht erkannt werden, stimmt das Muster nicht überein.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Hinzufügen der Muster für die Absicht „OrgChart-Manager“

1. Wählen Sie oben im Menü **Erstellen** aus.

1. Wählen Sie im linken Navigationsbereich unter **Improve app performance** (App-Leistung verbessern) die Option **Muster** aus.

1. Wählen Sie die Absicht **OrgChart-Manager** aus, und geben Sie dann die folgende Vorlagenäußerung ein:

    |Vorlagenäußerungen|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    Diese Vorlagenäußerungen enthalten die Entität **EmployeeListEntity** in geschweiften Klammern.

1. Wählen Sie auf der Seite „Muster“ die Absicht **OrgChart-Reports** aus, und geben Sie dann die folgenden Vorlagenäußerungen ein:

    |Vorlagenäußerungen|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Abfragen des Endpunkts mit Mustern

Nachdem die Muster der App nun hinzugefügt wurden, können Sie die App auf dem Vorhersagelaufzeit-Endpunkt trainieren, veröffentlichen und abfragen.

1. Wählen Sie **Trainieren** aus. Wählen Sie nach Abschluss des Trainings **Veröffentlichen**, dann den Slot **Produktion** und schließlich **Fertig** aus.

1. Wechseln Sie nach Abschluss der Veröffentlichung in den Browserregisterkarten wieder zur Endpunkt-URL-Registerkarte.

1. Vergewissern Sie sich am Ende der URL in der Adressleiste, dass Ihre Abfrage immer noch `Who is the boss of Jill Jones?` lautet, und übermitteln Sie die URL anschließend für eine neue Vorhersage.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Die Absichtsvorhersage ist nun deutlich zuverlässiger und die Bewertung der nächsthöchsten Absicht deutlich niedriger. Zwischen diesen beiden Absichten tritt beim Training kein Flip-Flop-Effekt auf.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Arbeiten mit optionalem Text und vordefinierten Entitäten

Die vorherigen Mustervorlagenäußerungen in diesem Tutorial enthielten einige Beispiele für optionalen Text wie die possesive Verwendung des Buchstabens s, `'s`, und die Verwendung des Fragezeichens, `?`. Angenommen, Sie müssen im Text der Äußerung die Angabe eines aktuellen und zukünftigen Datums abdecken.

Beispieläußerungen sind:

|Intent|Beispieläußerungen mit optionalem Text und vordefinierten Entitäten|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Jedes dieser Beispiele verwendet ein Verb, `was`, `is`, `will be`, sowie ein Datum, `March 3`, `now` und `in a month`, das LUIS korrekt voraussagen muss. Beachten Sie, dass die letzten beiden Beispiele in der Tabelle fast den gleichen Text verwenden, mit Ausnahme von `in` und `on`.

Beispielvorlagenäußerungen, die diese optionalen Informationen ermöglichen:

|Intent|Beispieläußerungen mit optionalem Text und vordefinierten Entitäten|
|:--|:--|
|OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


Durch die Verwendung der optionalen Syntax von eckigen Klammern, `[]`, kann dieser optionale Text zur Vorlagenäußerung hinzugefügt und bis zu einer zweiten Ebene, `[[]]`, verschachtelt werden und Entitäten oder Text enthalten.


**Frage: Warum ist der Buchstabe `w` am Anfang jeder Vorlagenäußerung jeweils ein Kleinbuchstabe? Sollten dies nicht optional Groß- oder Kleinbuchstaben sein?** Die von der Clientanwendung an den Abfrageendpunkt übermittelte Äußerung wird in Kleinbuchstaben konvertiert. Die Vorlagenäußerung kann in Groß- oder Kleinbuchstaben geschrieben sein, bei der Endpunktäußerung besteht diese Möglichkeit ebenfalls. Der Vergleich erfolgt immer nach der Konvertierung in Kleinbuchstaben.

**Frage: Warum ist die vordefinierte Zahl nicht Teil der Vorlagenäußerung, wenn der 3. März sowohl als Zahl `3` als auch als Datum `March 3` vorhergesagt wird?** Die Vorlagenäußerung verwendet kontextuell ein Datum, entweder wörtlich wie in `March 3` oder abstrahiert wie `in a month`. Ein Datum kann eine Zahl enthalten, aber eine Zahl muss nicht unbedingt als Datum angesehen werden. Verwenden Sie immer die Entität, die den Typ am besten repräsentiert, den Sie in den JSON-Vorhersageergebnissen zurückgeben möchten.

**Frage: Wie ist es bei schlecht formulierten Äußerungen, z.B. `Who will {EmployeeListEntity}['s] manager be on March 3?`** Grammatisch unterschiedliche Verbformen wie diese, bei denen `will` und `be` getrennt sind, müssen eine neue Vorlagenäußerung sein. Die vorhandene Vorlagenäußerung wird nicht übereinstimmen. Die Absicht der Äußerung nicht sich zwar nicht geändert, die Wortstellung aber schon. Diese Änderung wirkt sich auf die Vorhersage in LUIS aus. Sie können [Gruppen und den OR-Operator](#use-the-or-operator-and-groups) mit den Verbzeitformen verwenden, um diese Äußerungen zu kombinieren.

> [!CAUTION]
> **Beachten Sie: Entitäten werden zuerst gefunden, dann wird das Muster verglichen.**

### <a name="add-new-pattern-template-utterances"></a>Hinzufügen von neuen Mustervorlagenäußerungen

1. Fügen Sie im Abschnitt **Muster** unter **Erstellen** verschiedene neue Mustervorlagenäußerungen hinzu. Wählen Sie **OrgChart-Manager** im Dropdownmenü „Absicht“, und geben Sie jede der folgenden Vorlagenäußerungen ein:

    |Intent|Beispieläußerungen mit optionalem Text und vordefinierten Entitäten|
    |--|--|
    |OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. Wählen Sie auf der Navigationsleiste **Trainieren** aus, um die App zu trainieren.

3. Wählen Sie nach Abschluss des Trainings im oberen Bereich **Test** aus, um den Testbereich zu öffnen.

4. Geben Sie mehrere Testäußerungen ein, um zu überprüfen, ob dieses Muster abgeglichen wird und die Absichtsbewertung hoch genug ist.

    Nachdem Sie die erste Äußerung eingegeben haben, wählen Sie in den Ergebnissen **Untersuchen**, damit alle Vorhersageergebnisse angezeigt werden. Jede Äußerung sollte über die Absicht **OrgChart-Manager** verfügen und die Werte für die Entitäten `EmployeeListEntity` und `datetimeV2` extrahieren.

    |Äußerung|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Alle diese Äußerungen beinhalten Entitäten, daher entsprechen sie dem gleichen Muster und haben einen hohen Vorhersagewert. Sie haben einige Muster hinzugefügt, die vielen Variationen von Äußerungen entsprechen. Sie mussten der Absicht keine Beispieläußerungen hinzufügen, damit die Vorlagenäußerung im Muster funktioniert.

Diese Verwendung von Mustern hat für Folgendes gesorgt:
* Bessere Vorhersageergebnisse
* mit den gleichen Beispieläußerungen in der Absicht
* mit einigen wenigen gut konzipierten Vorlagenäußerungen im Muster

### <a name="use-the-or-operator-and-groups"></a>Verwenden des OR-Operators und von Gruppen

Einige der obigen Vorlagenäußerungen sind sich sehr ähnlich. Nutzen Sie die Syntax **group** `()` und **OR** `|`, um die Anzahl von Vorlagenäußerungen zu reduzieren.

Die beiden folgenden Muster können mithilfe der Syntax für „Gruppieren“ (`()`) und „ODER“ (`|`) kombiniert werden.

|Intent|Beispieläußerungen mit optionalem Text und vordefinierten Entitäten|
|--|--|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

Die neue Vorlagenäußerung lautet dann:

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

Hier wird **group** für die erforderlichen Verbzeitformen und die optionale Angabe `in` bzw. `on` mit einem **OR**-Pipezeichen dazwischen verwendet.

> [!NOTE]
> Bei Verwendung des Pipezeichens für _ODER_ (`|`) muss sich in der Beispielvorlage sowohl vor als auch nach dem Symbol ein Leerzeichen befinden.

1. Wählen Sie auf der Seite **Muster** den Filter **OrgChart-Manager** aus. Grenzen Sie die Liste ein, indem Sie nach `manager` suchen.

1. Behalten Sie eine Version der Vorlagenäußerung bei (zur Bearbeitung im nächsten Schritt), und löschen Sie die anderen Varianten.

1. Ändern Sie die Vorlagenäußerung in:

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. Wählen Sie auf der Navigationsleiste **Trainieren** aus, um die App zu trainieren.

3. Wählen Sie nach Abschluss des Trainings im oberen Bereich **Test** aus, um den Testbereich zu öffnen.

    Verwenden Sie den Testbereich, um Versionen der Äußerung zu testen:

    |Äußerungen für die Eingabe im Testbereich|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Wenn Sie eine umfangreichere Mustersyntax verwenden, verringern Sie die Anzahl von Vorlagenäußerungen, die Sie in Ihrer App verwalten müssen, während Sie immer noch ein gutes Vorhersageergebnis erhalten.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Verwenden von Anfangs- und Endankern für Äußerungen

Die Mustersyntax verfügt über Anfangs- und Endankersyntax für Äußerungen in Form eines Caretzeichens (`^`). Die Anfangs- und Endanker für Äußerungen können zusammen für sehr spezifische und ggf. wörtliche Äußerungen oder einzeln für Absichten verwendet werden.

## <a name="using-patternany-entity"></a>Verwenden der Entität „pattern.any“

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Hinzufügen von Beispieläußerungen mit „Pattern.any“

1. Wählen Sie **Erstellen** im oberen Navigationsbereich und dann **Absichten** im linken Navigationsbereich aus.

1. Wählen Sie **FindForm** in der Absichtenliste aus.

1. Fügen Sie einige Beispieläußerungen hinzu. Der Text, der als Pattern.any vorhergesagt werden soll, ist **fett formatiert**. Der Formularname lässt sich anhand der übrigen umgebenden Begriffe in der Äußerung nur schwer erkennen. Mit Pattern.any können die Begrenzungen der Entität markiert werden.

    |Beispieläußerung|Formularname|
    |--|--|
    |Wo ist das Formular **What to do when a fire breaks out in the Lab** (Vorgehensweise bei einem Brand im Labor), und wer muss es unterzeichnen, nachdem ich es gelesen habe?|Vorgehensweise bei einem Brand im Labor
    |Wo ist **Request relocation from employee new to the company** (Umzugsanfrage für neuen Mitarbeiter) auf dem Server?|Umzugsanfrage für neuen Mitarbeiter|
    |Wer hat **Health and wellness requests on the main campus** (Gesundheits- und Wellnessanforderungen auf dem Hauptcampus) erstellt, und was ist die aktuelle Version?|Gesundheits- und Wellnessanforderungen auf dem Hauptcampus|
    |Ich suche das Formular mit dem Namen **Office move request including physical assets** (Anfrage für Büroumzug einschließlich physischer Ressourcen). |Office move request including physical assets (Anfrage für Büroumzug einschließlich physischer Ressourcen)|

    Ohne eine Pattern.any-Entität wäre es für LUIS schwierig, zu verstehen, wo der Titel des Formulars endet, weil viele Variationen des Formularnamens vorliegen.

### <a name="create-a-patternany-entity"></a>Erstellen einer Pattern.any-Entität
Die Pattern.any-Entität extrahiert Entitäten unterschiedlicher Länge. Sie funktioniert nur in einem Muster, da das Muster Anfang und Ende der Entität mit Syntax kennzeichnet.

1. Wählen Sie im linken Navigationsbereich **Entitäten** aus.

1. Wählen Sie **+ Erstellen** aus, geben Sie den Namen `FormName` ein, und wählen Sie **Pattern.any** als Typ aus. Klicken Sie auf **Erstellen**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Hinzufügen eines Muster, das Pattern.any verwendet

1. Wählen Sie im linken Navigationsbereich **Muster** aus.

1. Wählen Sie die Absicht **FindForm** aus.

1. Geben Sie die folgenden Vorlagenäußerungen ein, die die neue Entität verwenden:

    |Vorlagenäußerungen|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Trainieren Sie die App.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testen des neuen Musters für das Extrahieren von Freiformdaten
1. Wählen Sie auf der oberen Leiste **Test** aus, um den Testbereich zu öffnen.

1. Geben Sie die folgende Äußerung ein:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Wählen Sie unter dem Ergebnis **Überprüfen** aus, um die Testergebnisse für die Entität und die Absicht anzuzeigen.

    Zuerst wird die Entität `FormName` gefunden, und danach das Muster, das die Absicht angibt. Wenn bei einem Testergebnis die Entitäten nicht erkannt wurden und damit das Muster nicht gefunden wurde, müssen Sie der Absicht (nicht dem Muster) weitere Beispieläußerungen hinzufügen.

1. Schließen Sie den Testbereich durch Auswählen der Schaltfläche **Test** auf der oberen Navigationsleiste.

### <a name="using-an-explicit-list"></a>Verwenden einer expliziten Liste

Wenn Ihr Muster eine Pattern.any-Entität enthält und Entitäten falsch extrahiert, verwenden Sie eine [explizite Liste](reference-pattern-syntax.md#explicit-lists), um dieses Problem zu beheben.

## <a name="what-did-this-tutorial-accomplish"></a>Was wurde in diesem Tutorial erreicht?

In diesem Tutorial wurden Muster hinzugefügt, mit denen LUIS die Absicht mit einer deutlich höheren Bewertung vorhersagen konnte, ohne dass weitere Beispieläußerungen hinzugefügt werden mussten. Das Kennzeichnen von Entitäten und ignorierbarem Text ermöglichte LUIS die Anwendung des Musters auf eine größere Vielfalt von Äußerungen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte


> [!div class="nextstepaction"]
> [Verwenden von Rollen mit einem Muster](luis-tutorial-pattern.md)
