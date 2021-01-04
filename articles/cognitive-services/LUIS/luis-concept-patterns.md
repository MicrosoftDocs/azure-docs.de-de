---
title: Unterstützen der Vorhersage durch Muster – LUIS
titleSuffix: Azure Cognitive Services
description: Ein Muster ermöglicht es Ihnen, größere Genauigkeit für eine Absicht zu erreichen, ohne viele weitere Äußerungen anzugeben.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: cda71c6e825ff27ba9b03e1306ccb287663e8613
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025958"
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

<a name="patterns-do-not-improve-entity-detection"></a>

## <a name="patterns-do-not-improve-machine-learning-entity-detection"></a>Muster verbessern nicht die Erkennung von Machine Learning-Entitäten.

Ein Muster ist in erster Linie zur Unterstützung der Vorhersage bei Absichten und Rollen vorgesehen. Die Entität _pattern.any_ wird zum Extrahieren von Freiformentitäten verwendet. Zwar verwenden Muster Entitäten, ein Muster hilft jedoch nicht beim Erkennen einer Machine Learning-Entität.

Erwarten Sie keine verbesserte Vorhersage von Entitäten, wenn Sie mehrere Äußerungen in einem einzelnen Muster zusammenfassen. Damit einfache Entitäten ausgelöst werden, müssen Sie Äußerungen hinzufügen oder Listenentitäten verwenden, da Ihr Muster andernfalls nicht ausgelöst wird.

## <a name="patterns-use-entity-roles"></a>Muster verwenden Entitätsrollen
Wenn zwei oder mehr Entitäten in einem Muster kontextverwandt sind, setzen Muster [Entitätsrollen](./luis-concept-entity-types.md) ein, um Kontextinformationen zu den Entitäten zu extrahieren.

## <a name="prediction-scores-with-and-without-patterns"></a>Vorhersagebewertungen mit und ohne Muster
Eine ausreichende Anzahl von Beispieläußerungen vorausgesetzt, wäre LUIS imstande, die Vorhersagezuverlässigkeit ohne Muster zu steigern. Mithilfe von Mustern lässt sich die Zuverlässigkeitsbewertung steigern, ohne so viele Äußerungen zur Verfügung stellen zu müssen.

## <a name="pattern-matching"></a>Musterabgleich
Ein Musterabgleich erfolgt so, dass zuerst die Entitäten innerhalb des Musters erkannt und dann der Rest der Wörter und die Wortstellung bewertet werden. Entitäten sind im Muster erforderlich, damit ein Musterabgleich erfolgen kann. Das Muster wird auf Tokenebene, nicht auf Zeichenebene angewandt.

## <a name="pattern-only-apps"></a>Apps, die nur auf Mustern beruhen
Sie können eine App mit Absichten erstellen, die über keine Beispieläußerungen verfügen, solange es für jede Absicht ein Muster gibt. Bei Apps, die nur auf Mustern beruhen, sollte das Muster keine Machine Learning-Entitäten enthalten, da für diese Beispieläußerungen erforderlich sind.

## <a name="patternany-entity"></a>Entität „Pattern.any“

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

## <a name="best-practices"></a>Bewährte Methoden
Informationen zu [Best Practices](luis-concept-best-practices.md).

## <a name="pattern-syntax"></a>Mustersyntax

Erhalten Sie Informationen zur Mustersyntax aus der [Mustersyntaxreferenz](reference-pattern-syntax.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Mustern:

* [Hinzufügen von Mustern](luis-how-to-model-intent-pattern.md)
* [Hinzufügen einer pattern.any-Entität](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Mustersyntax](reference-pattern-syntax.md)

> [!div class="nextstepaction"]
> [In diesem Tutorial ](luis-tutorial-pattern.md) erfahren Sie mehr über das Implementieren von Mustern.
