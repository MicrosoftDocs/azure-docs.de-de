---
title: Unterstützen der Vorhersage durch Muster
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ein Muster ermöglicht es Ihnen, größere Genauigkeit für eine Absicht zu erreichen, ohne viele weitere Äußerungen anzugeben.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 2a160ab7447304dc6eb14f76a723df4e8a4d9f46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813574"
---
# <a name="patterns-improve-prediction-accuracy"></a>Verbessern der Vorhersagegenauigkeit mit Mustern
Muster werden entworfen, um die Genauigkeit zu erhöhen, wenn mehrere Äußerungen sehr ähnlich sind.  Ein Muster ermöglicht es Ihnen, größere Genauigkeit für eine Absicht zu erreichen, ohne viele weitere Äußerungen anzugeben. 

## <a name="patterns-solve-low-intent-confidence"></a>Muster als Weg bei wenig vertrauenswürdigen Absichten
Stellen Sie sich eine Personalwesen-App vor, die das Organigramm bezogen auf einen Mitarbeiter auswertet. Bei Angabe des Namens und der Beziehungen eines Mitarbeiters gibt LUIS die beteiligten Mitarbeiter zurück. Sehen wir uns einen Mitarbeiter Tom mit einer Vorgesetzten Alice und einem Team von ihm Unterstellten mit den Namen Michael, Rebecca und Carl an.

![Bild des Organigramms](./media/luis-concept-patterns/org-chart.png)

|Äußerungen|Vorhergesagte Absicht|Absichtsbewertung|
|--|--|--|
|Wer sind Toms Untergebene?|GetOrgChart|0,30|
|Wer ist der Untergebene von Tom?|GetOrgChart|0,30|

Wenn einer App zwischen 10 und 20 Äußerungen mit unterschiedlicher Satzlänge, verschiedener Wortstellung und sogar verschiedenen Wörtern (Synonymen von „Untergebener“, „Verwalten“, „Vorgesetzter“) vorliegen, gibt LUIS möglicherweise eine geringe Zuverlässigkeitsbewertung zurück. Erstellen Sie ein Muster, um LUIS zu helfen, die Bedeutung der Wortstellung zu erfassen. 

Muster lösen Probleme in den folgenden Situationen: 

* die Absichtsbewertung ist gering
* die richtige Absicht hat nicht die höchste Punktzahl, kommt der höchsten Punktzahl aber sehr nahe 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Muster stellen eine Garantie für Absicht dar
Muster verwenden eine Kombination von Vorhersagetechnologien. Das Festlegen einer Absicht für eine Vorlagenäußerung in einem Muster stellt keine Garantie für die Absichtsvorhersage dar, liefert aber ein starkes Signal. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Muster verbessern nicht die Erkennung von Entitäten, die mit Machine Learning trainiert wurden

Ein Muster ist in erster Linie zur Unterstützung der Vorhersage bei Absichten und Rollen vorgesehen. Die Entität „pattern.any“ wird zum Extrahieren von Freiformentitäten verwendet. Zwar verwenden Muster Entitäten, ein Muster hilft jedoch nicht beim Erkennen einer mit Machine Learning trainierten Entität.  

Erwarten Sie keine verbesserte Vorhersage von Entitäten, wenn Sie mehrere Äußerungen in einem einzelnen Muster zusammenfassen. Damit einfache Entitäten ausgelöst werden, müssen Sie Äußerungen hinzufügen oder Listenentitäten verwenden, da Ihr Muster andernfalls nicht ausgelöst wird.

## <a name="patterns-use-entity-roles"></a>Muster verwenden Entitätsrollen
Wenn zwei oder mehr Entitäten in einem Muster kontextverwandt sind, setzen Muster [Entitätsrollen](luis-concept-roles.md) ein, um Kontextinformationen zu den Entitäten zu extrahieren.  

## <a name="prediction-scores-with-and-without-patterns"></a>Vorhersagebewertungen mit und ohne Muster
Eine ausreichende Anzahl von Beispieläußerungen vorausgesetzt, wäre LUIS imstande, die Vorhersagezuverlässigkeit ohne Muster zu steigern. Mithilfe von Mustern lässt sich die Zuverlässigkeitsbewertung steigern, ohne so viele Äußerungen zur Verfügung stellen zu müssen.  

## <a name="pattern-matching"></a>Musterabgleich
Ein Musterabgleich erfolgt so, dass zuerst die Entitäten innerhalb des Musters erkannt und dann der Rest der Wörter und die Wortstellung bewertet werden. Entitäten sind im Muster erforderlich, damit ein Musterabgleich erfolgen kann. Das Muster wird auf Tokenebene, nicht auf Zeichenebene angewandt. 

## <a name="pattern-syntax"></a>Mustersyntax
Die Mustersyntax ist eine Vorlage für eine Äußerung. Die Vorlage sollte sowohl Wörter und Entitäten enthalten, die abgeglichen werden sollen, als auch Wörter und Interpunktion, die ignoriert werden sollen. Sie ist **kein** regulärer Ausdruck. 

Entitäten in Mustern sind in geschweifte Klammern, `{}`, eingeschlossen. Muster können Entitäten und Entitäten mit Rollen enthalten. [Pattern.any](luis-concept-entity-types.md#patternany-entity) ist eine Entität, die ausschließlich in Mustern verwendet wird. 

Die Mustersyntax unterstützt folgende Syntax:

|Funktion|Syntax|Schachtelungsebene|Beispiel|
|--|--|--|--|
|Entität| {} – geschweifte Klammern|2|Where is form {entity-name}? (Wo befindet sich Formular {Entitätsname}?)|
|optional|[] – eckige Klammern<BR><BR>Auf Schachtelungsebenen jeder beliebigen Kombination von optionaler und Gruppierungssyntax sind diese auf 3 begrenzt. |2|The question mark is optional [?] (Das Fragezeichen ist optional [?])|
|Gruppierung|() – Klammern|2|is (a \| b) (ist (a | b))|
|oder| \| – senkrechter Strich (Pipe)<br><br>Die senkrechten Striche („oder“) sind pro Gruppe auf 2 begrenzt. |-|Where is form ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number}) (Wo ist Formular ({kurzer Formularname} &#x7c; {langer Formularname} &#x7c; {Formularnummer}))| 
|Anfang und/oder Ende der Äußerung|^ – Caretzeichen|-|^begin the utterance (Anfang der Äußerung)<br>the utterance is done^ (Ende der Äußerung)<br>^strict literal match of entire utterance with {number} entity^ (^strikte Literalübereinstimmung der gesamten Äußerung mit {Anzahl} Entität(en))|

## <a name="nesting-syntax-in-patterns"></a>Schachteln von Syntax in Mustern

Die **optionale** Syntax mit eckigen Klammern kann in zwei Ebenen geschachtelt werden. Beispiel: `[[this]is] a new form`. Dieses Beispiel ermöglicht die folgenden Äußerungen: 

|Beispiel für geschachtelte optionale Äußerung|Erklärung|
|--|--|
|this is a new form (dies ist ein neues Formular)|stimmt mit allen Wörtern im Muster überein|
|is a new form (ist ein neues Formular)|stimmt mit dem äußeren optionalen Wort und nicht optionalen Wörtern im Muster überein|
|a new form (ein neues Formular)|stimmt nur mit den erforderlichen Wörtern überein|

Die **Gruppierungssyntax** mit Klammern kann in zwei Ebenen geschachtelt werden. Beispiel: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Durch dieses Feature kann jede der drei Entitäten verglichen werden. 

Wenn es sich bei „Entity1“ um einen Standort mit Rollen wie „origin“ (Ursprung, Seattle) und „destination“ (Ziel, Kairo) und bei „Entity2“ um einen bekannten Gebäudenamen aus einer Listenentität (RedWest-C) handelt, stimmen die folgenden Äußerungen mit diesem Muster überein:

|Beispiel für geschachtelte Gruppierungsäußerung|Erklärung|
|--|--|
|RedWest-C|stimmt mit der äußeren Gruppierungsentität überein|
|Seattle|stimmt mit einer der inneren Gruppierungsentitäten überein|
|Kairo|stimmt mit einer der inneren Gruppierungsentitäten überein|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Schachtelungsbegrenzungen für Gruppen mit optionaler Syntax

Bei der Kombination von **Gruppierungssyntax** und **optionaler** Syntax gilt ein Grenzwert von 3 Schachtelungsebenen.

|Zulässig|Beispiel|
|--|--|
|Ja|( [ ( test1 &#x7c; test2 ) ] &#x7c; test3 )|
|Nein|( [ ( [ test1 ] &#x7c; test2 ) ] &#x7c; test3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Schachtelungsbegrenzungen für eine Gruppierung mit ODER-Syntax (|)

Bei der Kombination von **Gruppierungssyntax** mit **or-ing**-Syntax gilt ein Grenzwert von 2 senkrechten Strichen.

|Zulässig|Beispiel|
|--|--|
|Ja|( test1 &#x7c; test2 &#x7c; ( test3 &#x7c; test4 ) )|
|Nein|( test1 &#x7c; test2 &#x7c; test3 &#x7c; ( test4 &#x7c; test5 ) ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntax zum Hinzufügen einer Entität zu einer Mustervorlage
Um der Mustervorlage eine Entität hinzuzufügen, schließen Sie den Namen der Entität in geschweiften Klammern ein, wie hier: `Who does {Employee} manage?`. 

|Muster mit Entität|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntax zum Hinzufügen einer Entität und einer Rolle zu einer Mustervorlage
Eine Entitätsrolle wird als `{entity:role}` notiert, wobei ein Doppelpunkt und dann der Name der Rolle auf den Namen der Entität folgen. Um der Mustervorlage eine Entität mit einer Rolle hinzuzufügen, schließen Sie den Namen der Entität und den Namen der Rolle in geschweiften Klammern ein, wie hier: `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Muster mit Entitätsrollen|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntax zum Hinzufügen eines „pattern.any“ zu einer Mustervorlage
Die Pattern.any-Entität ermöglicht Ihnen das Hinzufügen einer Entität mit variabler Länge zum Muster. Sofern die Mustervorlage befolgt wird, kann „pattern.any“ jede beliebige Länge aufweisen. 

Um der Mustervorlage eine **Pattern.any**-Entität hinzuzufügen, schließen Sie die Pattern.any-Entität in geschweifte Klammern ein, wie hier: `How much does {Booktitle} cost and what format is it available in?`.  

|Muster mit Entität „Pattern.any“|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Buchtitel im Muster|
|--|
|Wie viel kostet **stiehl dieses Buch**, und in welchen Formaten ist es verfügbar?|
|Wie viel kostet **fragen**, und in welchen Formaten ist es verfügbar?|
|Wie viel kostet **Supergute Tage oder die sonderbare Welt des Christopher Boone**, und in welchen Formaten ist es verfügbar?| 

Die Wörter des Buchtitels sind für LUIS nicht verwirrend, da LUIS basierend auf der „Pattern.any“-Entität weiß, wo der Buchtitel endet.

## <a name="explicit-lists"></a>Explizite Listen

Erstellen Sie eine [explizite Liste](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) durch die Erstellungs-API, um die Ausnahme zu ermöglichen, wenn Folgendes zutrifft:

* Ihr Muster enthält eine [Pattern.any](luis-concept-entity-types.md#patternany-entity)-Entität.
* Diese Mustersyntax lässt die Möglichkeit einer falschen Entitätsextraktion auf Grundlage der Äußerung zu. 

Nehmen wir beispielsweise an, Sie haben ein Muster, das sowohl optionale Syntax `[]` als auch Entitätssyntax `{}` in einer Kombination enthält, die zu einer fehlerhaften Extraktion der Daten führt.

Betrachten Sie das Muster ‚[Suche] E-Mail zu {Betreff} [von {Person}]‘.

In den folgenden Äußerungen werden die Entitäten **Betreff** und **Person** richtig und falsch extrahiert:

|Äußerung|Entität|Richtige Extraktion|
|--|--|:--:|
|E-Mail über Hunde von Chris|Betreff=Hunde<br>Person=Chris|✔|
|E-Mail über den Mann aus La Mancha|Betreff=der Mann<br>Person=La Mancha|X|

In der vorstehenden Tabelle sollte der Betreff `the man from La Mancha` (ein Buchtitel) sein. Da der Betreff jedoch das optionale Wort `from` enthält, wird der Titel falsch vorhergesagt. 

Um diese Ausnahme im Muster zu beheben, fügen Sie mithilfe der [Verfasser-API für explizite Listen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) `the man from la mancha` als expliziten Listentreffer für die {Betreff}-Entität hinzu.

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntax zum Kennzeichnen von optionalem Text in einer Vorlagenäußerung
Kennzeichnen Sie optionalen Text in der Äußerung mithilfe der Syntax für eckige Klammern in regulären Ausdrücken, `[]`. In optionalem Text können eckige Klammern nur bis zu einer Tiefe von zwei Klammern verschachtelt werden.

|Muster mit optionalem Text|Bedeutung|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` und `from {person}` sind optional.|
|`Can you help me[?] (Können Sie mir weiterhelfen[?])|Das Satzzeichen ist optional|

Satzzeichen (`?`, `!`, `.`) sollten ignoriert werden. Dies erreichen Sie, indem Sie die Syntax mit eckigen Klammern in Mustern verwenden. 

## <a name="pattern-only-apps"></a>Apps, die nur auf Mustern beruhen
Sie können eine App mit Absichten erstellen, die über keine Beispieläußerungen verfügen, solange es für jede Absicht ein Muster gibt. Bei Apps, die nur auf Mustern beruhen, sollte das Muster keine mit Machine Learning trainierten Entitäten enthalten, da für diese Beispieläußerungen erforderlich sind. 

## <a name="best-practices"></a>Bewährte Methoden
Informationen zu [Best Practices](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [In diesem Tutorial ](luis-tutorial-pattern.md) erfahren Sie mehr über das Implementieren von Mustern.
