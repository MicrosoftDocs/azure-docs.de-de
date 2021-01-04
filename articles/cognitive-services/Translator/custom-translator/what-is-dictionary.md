---
title: 'Definition: Wörterbuch – Custom Translator'
titleSuffix: Azure Cognitive Services
description: Ein Wörterbuch ist ein Dokument mit Zuordnungen, das Ausdrücke oder Sätze – samt Übersetzung – enthält, die Microsoft Translator immer identisch übersetzen soll. Wörterbücher werden auch als Glossare oder Terminologiedatenbanken bezeichnet.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 14ff76e090e628a880290bb0e9f90026260de0c7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368983"
---
# <a name="what-is-a-dictionary"></a>Definition: Wörterbuch

Ein Wörterbuch umfasst Dokumente, die paarweise zugeordnet sind, und gibt Ausdrücke oder Sätze mit ihren entsprechenden Übersetzungen an. Verwenden Sie ein Wörterbuch in Ihrem Training, wenn Sie Microsoft Translator für alle Instanzen des Ausgangssatzes bzw. -ausdrucks immer die Übersetzung verwenden soll, die Sie im Wörterbuch angegeben haben. Wörterbücher werden auch als Glossare oder Terminologiedatenbanken bezeichnet. Sie können sich das so vorstellen, dass das Wörterbuch für alle Begriffe, die Sie angeben, den Ansatz „Kopieren und Ersetzen“ verfolgt. Darüber hinaus erstellt der Microsoft Custom Translator-Dienst eigene Universalwörterbücher und verwendet sie, um die Qualität seiner Übersetzung zu steigern. Ein vom Kunden bereitgestelltes Wörterbuch hat jedoch Vorrang und wird zuerst nach Wörtern oder Sätzen durchsucht.

Wörterbücher eignen sich nur für Projekte mit Sprachpaaren, die auf einem vollständig unterstützten allgemeinen neuronalen Netzwerkmodell von Microsoft basieren. [Vollständige Liste der Sprachen anzeigen](../language-support.md#customization).

## <a name="phrase-dictionary"></a>Ausdruckswörterbuch
Beim Ausdruckswörterbuch wird die Groß-/Kleinschreibung unterschieden. Es handelt sich um einen exakten Such- und Ersetzungsvorgang. Wenn Sie ein Ausdruckswörterbuch für das Training Ihres Modells verwenden, wird jedes aufgeführte Wort bzw. jeder aufgeführte Ausdruck wie von Ihnen angegeben übersetzt. Der Rest des Satzes wird wie gewohnt übersetzt. Sie können mithilfe eines Ausdruckswörterbuchs Ausdrücke angeben, die nicht mit dem gleichen unübersetzten Ausdruck in der Quell- und Zieldatei im Wörterbuch übersetzt werden sollen.

## <a name="sentence-dictionary"></a>Satzwörterbuch
Beim Satzwörterbuch wird die Groß-/Kleinschreibung nicht unterschieden. Im Satzwörterbuch können Sie eine genaue zielsprachliche Übersetzung für einen Ausgangssatz festlegen. Damit eine Übereinstimmung im Satzwörterbuch gefunden wird, muss der gesamte übermittelte Satz dem Wörterbucheintrag in der Ausgangssprache entsprechen. Wenn der Quellwörterbucheintrag mit Interpunktionszeichen endet, wird er während des Abgleichs ignoriert. Wenn die Übereinstimmung nur für einen Teil des Satzes gilt, zählt der Eintrag nicht als Entsprechung.  Wenn eine Übereinstimmung erkannt wird, wird der zielsprachliche Eintrag des Satzwörterbuchs zurückgegeben.

## <a name="dictionary-only-trainings"></a>Training nur mit Wörterbüchern
Sie können ein Modell nur mit Wörterbuchdaten trainieren. Wählen Sie dazu nur das Wörterbuchdokument (oder mehrere Wörterbuchdokumente) aus, das Sie einbinden möchten, und tippen Sie auf „Modell erstellen“. Da es sich um ein Training handelt, das ausschließlich auf Wörterbuchdaten basiert, ist keine Mindestanzahl von Trainingssätzen erforderlich. Ihr Modell schließt das Training in der Regel viel schneller als das Standardtraining ab.  Die resultierenden Modelle verwenden die Microsoft-Grundmodelle zum Übersetzen samt der von Ihnen hinzugefügten Wörterbücher.  Sie erhalten keinen Testbericht.

>[!Note]
>Custom Translator ordnet Wörterbuchdateien nicht zu. Deswegen ist es wichtig, dass Ihre Wörterbuchdokumente gleich viele Sätze/Ausdrücke in der Ausgangs- und in der Zielsprache haben, und dass diese einander genau zugeordnet sind.

## <a name="recommendations"></a>Empfehlungen

- Wörterbücher sind kein Ersatz für das Trainieren eines Modells mit Trainingsdaten. Es empfiehlt sich, sie zu vermeiden und das System aus Ihren Trainingsdaten lernen zu lassen. Wenn jedoch Sätze oder zusammengesetzte Substantive unverändert angewendet werden müssen, verwenden Sie ein Wörterbuch.
- Verwenden Sie das Ausdruckswörterbuch sparsam. Machen Sie sich bewusst, dass beim Ersetzen eines Ausdrucks in einem Satz der Kontext dieses Satzes verloren geht oder für die Übersetzung des restlichen Satzes nicht mehr ausreicht. Das heißt, wenn der Ausdruck oder das Wort im Satzzusammenhang gemäß dem bereitgestellten Wörterbuch übersetzt wird, leidet oft die allgemeine Übersetzungsqualität des Satzes darunter.
- Das Ausdruckswörterbuch eignet sich gut für zusammengesetzte Substantive wie Produktnamen („Microsoft SQL Server“), Eigennamen („City of Hamburg“) oder Produktmerkmale („Pivottabelle“). Weniger geeignet ist es für Verben oder Adjektive, da diese in der Regel in der Ausgangs- oder Zielsprache flektiert werden. Die bewährte Methode ist das ausschließliche Verwenden von Ausdruckswörterbucheinträgen für zusammengesetzte Substantive.
- Beim Verwenden eines Ausdruckswörterbuchs sind Groß-/Kleinschreibung und Interpunktion wichtig. Wörterbucheinträge stimmen nur mit Wörtern und Ausdrücken im Eingangssatz überein, die genau die gleiche Groß- und Kleinschreibung verwenden, die in der Quell-Wörterbuchdatei angeben ist. Außerdem geben die Übersetzungen die Groß-/Kleinschreibung und Interpunktion wieder, die in der Ziel-Wörterbuchdatei angegeben ist. Nehmen wir beispielsweise an, Sie haben ein System für die Übersetzung von Englisch in Spanisch trainiert, das „US“ in der Quelldatei und „EE.UU.“ in der Zieldatei angibt. Wenn Sie die Übersetzung eines Satzes anfordern, der das Wort „us“ (nicht groß geschrieben) enthält, stimmt dies NICHT mit dem Wörterbuch überein. Wenn Sie jedoch die Übersetzung eines Satzes anfordern, der das Wort „US“ (groß geschrieben) enthält, stimmte es mit dem Wörterbuch überein, und die Übersetzung würde „EE.UU.“ enthalten. Beachten Sie, dass die Groß-/Kleinschreibung und Interpunktion in der Übersetzung sich von der Angabe in der Wörterbuch-Zieldatei unterscheiden kann und ebenfalls von der Groß-/Kleinschreibung und Interpunktion in der Quelle. Sie folgt den Regeln der Zielsprache.
- Bei Verwendung eines Satzwörterbuchs wird das Satzzeichen am Ende des Satzes ignoriert. Wenn das Quellwörterbuch beispielsweise „Dieser Satz endet mit einem Satzzeichen!“ enthält, stimmen alle Übersetzungsanforderungen überein, die „Dieser Satz endet mit einem Satzzeichen“ enthalten.
- Wenn ein Wort mehrmals in einer Wörterbuchdatei vorkommt, verwendet das System stets den zuletzt angegebenen Eintrag. Daher sollte Ihr Wörterbuch jeweils nur eine Übersetzung für ein Wort enthalten.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Richtlinien zu Dokumentformaten](document-formats-naming-convention.md).