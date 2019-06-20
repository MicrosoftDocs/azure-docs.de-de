---
title: Hinzufügen von Entitäten
titleSuffix: Language Understanding - Azure Cognitive Services
description: Erstellen Sie Entitäten, um die Schlüsseldaten aus Benutzeräußerungen in Language Understanding-Apps (LUIS) zu extrahieren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 241e89ac7fa78184e7c55f9e8065e1534cea9143
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148723"
---
# <a name="create-entities-without-utterances"></a>Erstellen von Entitäten ohne Äußerungen

Die Entität stellt ein Wort oder einen Ausdruck innerhalb der Äußerung dar, die extrahiert werden soll. Eine Entität stellt eine Klasse dar, einschließlich einer Sammlung ähnlicher Objekte (Orte, Dinge, Personen, Ereignisse oder Konzepte). Entitäten beschreiben Informationen mit Bezug zur Absicht. Manchmal sind sie auch für die Ausführung der Aufgabe Ihrer App wichtig. Sie können Entitäten erstellen, wenn Sie einer Absicht eine Äußerung hinzufügen, oder auch getrennt vom Hinzufügen einer Äußerung zu einer Absicht (vor oder nach dem Hinzufügen).

Sie können Entitäten in Ihrer LUIS-App über die **Entitätenlisten** auf der Seite **Entitäten** hinzufügen, bearbeiten oder löschen. LUIS verfügt über zwei Haupttypen von Entitäten: [vordefinierte Entitäten](luis-reference-prebuilt-entities.md) und Ihre eigenen [benutzerdefinierten Entitäten](luis-concept-entity-types.md#types-of-entities).

Sobald eine maschinell erlernte Entität erstellt wurde, müssen Sie diese Entität in der gesamten Beispieläußerung aller Absichten, in denen sie sich befindet, markieren.

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Hinzufügen einer vordefinierten Entität zu Ihrer App

Einer Anwendung hinzugefügte allgemeine vordefinierte Entitäten sind *number* und *datetimeV2*. 

1. Wählen Sie in Ihrer App im Abschnitt **Erstellen** im linken Bereich die Option **Entitäten**.
 
1. Wählen Sie auf der Seite **Entitäten** die Option **Vordefinierte Entitäten hinzufügen**.

1. Wählen Sie im Dialogfeld **Vordefinierte Entitäten hinzufügen** die vordefinierten Entitäten **number** und **datetimeV2** aus. Wählen Sie dann **Fertig** aus.

    ![Screenshot des Dialogfelds „Vordefinierte Entität hinzufügen“](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Hinzufügen einfacher Entitäten für einzelne Konzepte

Eine einfache Entität beschreibt ein einzelnes Konzept. Verwenden Sie das folgende Verfahren zum Erstellen einer Entität, mit der die Namen von Unternehmensabteilungen extrahiert werden, z.B. *Personalwesen* oder *Operativer Betrieb*.   

1. Wählen Sie in Ihrer App den Abschnitt **Build**, im linken Bereich die Option **Entitäten** und dann die Option **Neue Entität erstellen**.

1. Geben Sie im Popupdialogfeld das Wort `Location` in das Feld **Entitätsname** ein, und wählen Sie in der Liste **Entitätstyp** die Option **Einfach** aus. Wählen Sie anschließend **Fertig**.

    Navigieren Sie nach dem Erstellen dieser Entität zu allen Absichten mit Beispieläußerungen, die die Entität enthalten. Wählen Sie den Text in der Beispieläußerung aus, und kennzeichnen Sie ihn als Entität. 

    Normalerweise wird eine [Ausdrucksliste](luis-concept-feature.md) verwendet, um das Signal einer einfachen Entität zu verstärken.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Hinzufügen von Entitäten mit regulären Ausdrücken für hochgradig strukturierte Konzepte

Eine Entität des Typs „Regulärer Ausdruck“ dient zum Abrufen von Daten aus der Äußerung basierend auf einem regulären Ausdruck, den Sie bereitstellen. 

1. Wählen Sie in Ihrer App im linken Navigationsbereich **Entitäten** und dann **Neue Entität erstellen** aus.

1. Geben Sie im Popupdialogfeld `Human resources form name` in das Feld **Entitätsname** ein, wählen Sie **Regulärer Ausdruck** in der Liste **Entitätstyp** aus, geben Sie den regulären Ausdruck `hrf-[0-9]{6}` ein, und wählen Sie dann **Fertig** aus. 

    Dieser reguläre Ausdruck stimmt mit dem Literal `hrf-` überein und enthält dann sechs Ziffern, um eine Formularnummer für ein Formular aus dem Personalwesen darzustellen.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Hinzufügen von zusammengesetzten Entitäten zum Gruppieren in einer Beziehung von über- und untergeordneten Elementen

Sie können Beziehungen zwischen Entitäten unterschiedlichen Typs durch das Erstellen einer zusammengesetzten Entität definieren. Im folgenden Beispiel enthält die Entität einen regulären Ausdruck und eine vordefinierte Entität mit einem Namen.  

In der Äußerung `Send hrf-123456 to John Smith` wird die Zeichenfolge `hrf-123456` mit einem [regulären Ausdruck](#add-regular-expression-entities) des Personalwesens abgeglichen, und `John Smith` wird mit der vordefinierten Entität „personName“ extrahiert. Jede Entität ist Teil einer größeren übergeordneten Entität. 

1. Wählen Sie in Ihrer App im linken Navigationsbereich des Abschnitts **Build** die Option **Entitäten** und dann **Vordefinierte Entität hinzufügen**.

1. Fügen Sie die vordefinierte Entität **PersonName** hinzu. Anweisungen hierzu finden Sie unter [Hinzufügen einer vordefinierten Entität](#add-prebuilt-entity). 

1. Wählen Sie im linken Navigationsbereich **Entitäten** und dann **Neue Entität erstellen** aus.

1. Geben Sie im Popupdialogfeld die Zeichenfolge `SendHrForm` in das Feld **Entitätsname** ein, und wählen Sie in der Liste **Entitätstyp** dann die Option **Zusammengesetzt** aus.

1. Wählen Sie **Untergeordnetes Element hinzufügen** aus, um ein neues untergeordnetes Element hinzuzufügen.

1. Wählen Sie im ersten Feld **Untergeordnete Entität** die Entität **number** in der Liste aus.

1. Wählen Sie im zweiten Feld **Untergeordnete Entität** in der Liste die Entität **Human resources form name** (Name für Personalwesenformular) aus. 

1. Wählen Sie **Fertig**aus.

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Hinzufügen von Pattern.any-Entitäten zum Erfassen von Freiformentitäten

[Pattern.Any](luis-concept-entity-types.md)-Entitäten sind nur in [Mustern](luis-how-to-model-intent-pattern.md) gültig, nicht in Absichten. Mit diesem Entitätstyp kann LUIS das Ende von Entitäten unterschiedlicher Länge und Wortwahl finden. Da diese Entität in einem Muster verwendet wird, weiß LUIS, wo sich das Ende der Entität in der Äußerungsvorlage befindet.

Wenn eine App eine `FindHumanResourcesForm`-Absicht hat, kann ein Konflikt zwischen dem extrahierten Formulartitel und der Absichtsvorhersage auftreten. Um zu verdeutlichen, welche Wörter der Formulartitel enthält, verwenden Sie eine Pattern.any-Entität in einem Muster. Die LUIS-Vorhersage beginnt mit der Äußerung. Zunächst wird die Äußerung überprüft und mit Entitäten abgeglichen. Wenn Entitäten gefunden werden, wird das Muster überprüft und abgeglichen. 

In der Äußerung `Where is Request relocation from employee new to the company on the server?` ist der Formulartitel problematisch, da aus dem Kontext nicht hervorgeht, wo der Titel endet und wo der restliche Teil der Äußerung beginnt. Titel können eine beliebigen Wortreihenfolge aufweisen und beispielsweise ein einzelnes Wort, komplexe Ausdrücke mit Satzzeichen und eine unsinnige Reihenfolge von Wörtern enthalten. Ein Muster ermöglicht Ihnen, eine Entität zu erstellen, wobei die vollständige und genaue Entität extrahiert werden kann. Sobald der Titel gefunden wurde, wird die `FindHumanResourcesForm`-Absicht vorhergesagt, da dies die Absicht für das Muster ist.

1. Wählen Sie im Abschnitt **Erstellen** im linken Bereich die Option **Entitäten** und dann **Neue Entität erstellen**.

1. Geben Sie im Dialogfeld **Entität hinzufügen** in das Feld **Entitätsname** den Namen `HumanResourcesFormTitle` ein, und wählen Sie **Pattern.any** als **Entitätstyp** aus.

    Um die pattern.any-Entität zu verwenden, fügen Sie auf der Seite **Muster** im Abschnitt **Improve app performance** (App-Leistung verbessern) mit der richtigen Syntax mit geschweiften Klammern ein Muster ein, z.B. `Where is **{HumanResourcesFormTitle}** on the server?`.

    Wenn Ihr Muster eine Pattern.any-Entität enthält und Entitäten falsch extrahiert, verwenden Sie eine [explizite Liste](luis-concept-patterns.md#explicit-lists), um dieses Problem zu beheben. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Hinzufügen einer Rolle zum Unterscheiden verschiedener Kontexte

Eine Rolle ist ein benannter Untertyp, der auf Kontext basiert. Er ist in allen Entitäten verfügbar, einschließlich vorkonfigurierter und nicht durch maschinelles Lernen erworbener Entitäten. 

Die Syntax für eine Rolle ist **`{Entityname:Rolename}`** , wobei auf den Entitätsnamen ein Doppelpunkt und dann der Rollenname folgt. Beispiel: `Move {personName} from {LocationUsingRoles:Origin} to {LocationUsingRoles:Destination}`.

1. Wählen Sie im Abschnitt **Erstellen** im linken Bereich die Option **Entitäten**.

1. Wählen Sie **Neue Entität erstellen** aus. Geben Sie den Namen `LocationUsingRoles` ein. Wählen Sie den Typ **Einfach** und dann **Fertig** aus. 

1. Wählen Sie im linken Bereich die Option **Entitäten** und dann die neue Entität **LocationUsingRoles** aus, die im vorherigen Schritt erstellt wurde.

1. Geben Sie im Textfeld **Rollenname** den Namen der Rolle `Origin` ein, und drücken Sie die EINGABETASTE. Fügen Sie eine zweite Rolle namens `Destination` hinzu. 

    ![Screenshot vom Hinzufügen der Rolle „Origin“ zur Entität „Location“](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Hinzufügen von Listenentitäten für genaue Übereinstimmungen

Listenentitäten stellen eine feste, abgeschlossene Gruppe mit verwandten Wörtern dar. 

Für eine Personal-App können Sie eine Liste mit allen Abteilungen und den Synonymen für die Abteilungen verwenden. Sie müssen nicht alle Werte kennen, wenn Sie die Entität erstellen. Sie können weitere Werte hinzufügen, nachdem Sie wirkliche Benutzeräußerungen mit Synonymen überprüft haben.

1. Wählen Sie im Abschnitt **Erstellen** im linken Bereich die Option **Entitäten** und dann **Neue Entität erstellen**.

1. Geben Sie im Dialogfeld **Entität hinzufügen** `Department` in das Feld **Entitätsname** ein, und wählen Sie **Liste** als **Entitätstyp** aus. Wählen Sie **Fertig**aus.
  
1. Auf der Listenentitätsseite können Sie normalisierte Namen hinzufügen. Geben Sie im Textfeld **Werte** einen Abteilungsnamen für eine Liste ein, z.B. `HumanResources`, und drücken Sie anschließend auf der Tastatur die EINGABETASTE. 

1. Geben Sie rechts neben dem normalisierten Wert die Synonyme ein, und drücken Sie nach jedem Element die EINGABETASTE.

1. Wenn Sie weitere normalisierte Elemente für die Liste wünschen, wählen Sie **Empfehlen** aus, um Optionen aus dem [semantischen Wörterbuch](luis-glossary.md#semantic-dictionary) anzuzeigen.

    ![Screenshot der Auswahl der Empfehlen-Funktion zum Anzeigen der Möglichkeiten](./media/add-entities/hr-list-2.png)


1. Wählen Sie ein Element in der empfohlenen Liste aus, um es als normalisierten Wert hinzuzufügen, oder wählen Sie **Alle hinzufügen** aus, um alle Elemente hinzuzufügen. 
    Sie können Werte in eine vorhandene Listenentität importieren, indem Sie das folgende JSON-Format verwenden:

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>Der Entitätstyp kann nicht geändert werden

LUIS erlaubt Ihnen nicht, den Entitätstyps zu ändern, da die Anwendung nicht weiß, was zum Erstellen der Entität hinzugefügt oder entfernt werden muss. Um den Typ zu ändern, sollten Sie besser eine neue Entität des richtigen Typs mit einem etwas anderen Namen erstellen. Sobald die Entität erstellt ist, entfernen Sie in jeder Äußerung den alten bezeichnenden Entitätsnamen, und fügen Sie den neuen Entitätsnamen hinzu. Sobald alle Äußerungen ihre neuen Bezeichnungen erhalten haben, löschen Sie die alte Entität. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Erstellen eines Musters aus einer Beispieläußerung

Weitere Informationen finden Sie unter [Hinzufügen eines Musters aus einer vorhandenen Äußerung auf der Seite der Absicht oder der Entität](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Trainieren Ihrer App nach dem Ändern des Modells mit Entitäten

Nachdem Sie Entitäten hinzugefügt, bearbeitet oder entfernt haben, [trainieren](luis-how-to-train.md) und [veröffentlichen](luis-how-to-publish-app.md) Sie Ihre App, damit die Änderungen auf Endpunktabfragen angewandt werden. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu vordefinierten Entitäten finden Sie unter dem Projekt [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). 

Weitere Informationen zur Anzeige der Entität in der Antwort auf die JSON-Endpunktabfrage finden Sie unter [Extrahieren von Daten](luis-concept-data-extraction.md).

Wenn Sie Absichten, Äußerungen und Entitäten hinzugefügt haben, verfügen Sie über eine einfache LUIS-App. Erfahren Sie, wie Sie Ihre App [trainieren](luis-how-to-train.md), [testen](luis-interactive-test.md) und [veröffentlichen](luis-how-to-publish-app.md) können.
 
