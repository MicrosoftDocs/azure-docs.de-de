---
title: 'Definition: Wörterbuch – Custom Translator'
titleSuffix: Azure Cognitive Services
description: Ein Wörterbuch ist ein Dokument mit Zuordnungen, das Ausdrücke oder Sätze – samt Übersetzung – enthält, die Microsoft Translator immer identisch übersetzen soll. Wörterbücher werden auch als Glossare oder Terminologiedatenbanken bezeichnet.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 4bf112974befd7063b3da8e2b1c1dcbb7ad55608
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385238"
---
# <a name="what-is-a-dictionary"></a>Definition: Wörterbuch

Ein Wörterbuch umfasst Dokumente, die paarweise zugeordnet sind, und gibt Ausdrücke oder Sätze mit ihren entsprechenden Übersetzungen an. Verwenden Sie ein Wörterbuch in Ihrem Training, wenn Sie Microsoft Translator für alle Instanzen des Ausgangssatzes bzw. -ausdrucks immer die Übersetzung verwenden soll, die Sie im Wörterbuch angegeben haben. Wörterbücher werden auch als Glossare oder Terminologiedatenbanken bezeichnet. Sie können sich das so vorstellen, dass das Wörterbuch für alle Begriffe, die Sie angeben, den Ansatz „Kopieren und Ersetzen“ verfolgt.

Wörterbücher eignen sich nur für Projekte mit Sprachenpaaren, die auf einem vollständig unterstützten neuronalen maschinellen Microsoft-Übersetzungssystem basieren. [Vollständige Liste der Sprachen anzeigen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Ausdruckswörterbuch
Wenn Sie ein Ausdruckswörterbuch für das Training Ihres Modells verwenden, wird jedes aufgeführte Wort bzw. jeder aufgeführte Ausdruck wie von Ihnen angegeben übersetzt. Der Rest des Satzes wird wie gewohnt übersetzt. Sie können mithilfe eines Ausdruckswörterbuchs Ausdrücke angeben, die nicht mit dem gleichen unübersetzten Ausdruck in der Quell- und Zieldatei im Wörterbuch übersetzt werden sollen.

## <a name="sentence-dictionary"></a>Satzwörterbuch
Im Satzwörterbuch können Sie eine genaue zielsprachliche Übersetzung für einen Ausgangssatz festlegen. Damit eine Übereinstimmung im Satzwörterbuch gefunden wird, muss der gesamte übermittelte Satz dem Wörterbucheintrag in der Ausgangssprache entsprechen.  Wenn die Übereinstimmung nur für einen Teil des Satzes gilt, zählt der Eintrag nicht als Entsprechung.  Wenn eine Übereinstimmung erkannt wird, wird der zielsprachliche Eintrag des Satzwörterbuchs zurückgegeben.

## <a name="dictionary-only-trainings"></a>Training nur mit Wörterbüchern
Sie können ein Modell nur mit Wörterbuchdaten trainieren. Wählen Sie dazu nur das Wörterbuchdokument (oder mehrere Wörterbuchdokumente) aus, das Sie einbinden möchten, und tippen Sie auf „Modell erstellen“. Da es sich um ein Training handelt, das ausschließlich auf Wörterbuchdaten basiert, ist keine Mindestanzahl von Trainingssätzen erforderlich. Ihr Modell schließt das Training in der Regel viel schneller als das Standardtraining ab.  Die resultierenden Modelle verwenden die Microsoft-Grundmodelle zum Übersetzen samt der von Ihnen hinzugefügten Wörterbücher.  Sie erhalten keinen Testbericht.

>[!Note]
>Custom Translator ordnet Wörterbuchdateien nicht zu. Deswegen ist es wichtig, dass Ihre Wörterbuchdokumente gleich viele Sätze/Ausdrücke in der Ausgangs- und in der Zielsprache haben, und dass diese einander genau zugeordnet sind.

## <a name="recommendations"></a>Empfehlungen

- Wörterbücher sind kein Ersatz für ein trainiertes Modell mit Trainingsdaten.  Sie finden und ersetzen im Grunde Wörter oder Sätze.  In der Regel ist es besser, wenn das System aus Ihrem Trainingsmaterial ganze Sätze lernt, als dass Sie ein Wörterbuch verwenden.
- Verwenden Sie das Ausdruckswörterbuch sparsam. Wenn ein Ausdruck in einem Satz ersetzt wird, geht der Kontext dieses Satzes verloren oder reicht für die Übersetzung des restlichen Satzes nicht mehr aus. Das heißt, wenn der Ausdruck oder das Wort im Satzzusammenhang gemäß dem Ausdruckswörterbuch übersetzt wird, leidet oft die allgemeine Übersetzungsqualität des Satzes darunter.
- Das Ausdruckswörterbuch eignet sich gut für zusammengesetzte Substantive wie Produktnamen („Microsoft SQL Server“), Eigennamen („City of Hamburg“) oder Produktmerkmale („Pivottabelle“). Weniger geeignet ist es für Verben oder Adjektive, da diese in der Regel in der Ausgangs- oder Zielsprache flektiert werden. Verwenden Sie Ausdruckswörterbucheinträge ausschließlich für zusammengesetzte Substantive.
- Beim Verwenden eines Wörterbuchs entspricht die Großschreibung in der Übersetzung der Großschreibung in der Zieldatei. Großschreibung und Interpunktion werden ignoriert, wenn Sie versuchen, Übereinstimmungen zwischen dem eingegebenen Satz und den ausgangssprachlichen Sätzen in Ihrer Wörterbuchdatei zu identifizieren. Angenommen, Sie haben ein System für die Übersetzung aus dem Englischen ins Spanische trainiert. Im Wörterbuch des Systems entspricht der Quelldateieintrag „City of Hamburg“ dem Zieldateieintrag „Ciudad de hamburg“. Wenn die Übersetzung eines Satzes mit dem Ausdruck „City of Hamburg“ angefordert wird, stimmt „City of Hamburg“ mit der Wörterbuchdatei mit dem Eintrag „City of Hamburg“ überein und wird in der endgültigen Übersetzung „Ciudad de hamburg“ zugeordnet.
- Wenn ein Wort mehrmals in einer Wörterbuchdatei vorkommt, verwendet das System stets den zuletzt angegebenen Eintrag. Ihr Wörterbuch sollte jeweils nur eine Übersetzung für ein Wort enthalten.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Richtlinien zu Dokumentformaten](document-formats-naming-convention.md).
