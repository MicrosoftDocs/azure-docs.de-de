---
title: Konstituentenanalyse – API für linguistische Analyse
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie mit der Konstituentenanalyse, auch als „Phrasenstrukturanalyse“ bezeichnet, Phrasen im Text identifiziert werden.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 7611f5f16111b5d8b0d2d293750f658125e50837
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60535433"
---
# <a name="constituency-parsing"></a>Konstituentenanalyse

> [!IMPORTANT]
> Die Vorschauversion für die linguistische Analyse wurde am 9. August 2018 außer Betrieb genommen. Es wird empfohlen, [Azure Machine Learning-Textanalysemodule](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) für die Textverarbeitung und -analyse zu verwenden.

Erfahren Sie, wie mit der Konstituentenanalyse (auch als „Phrasenstrukturanalyse“ bezeichnet) die Ausdrücke im Text identifiziert werden.
Dies kann beim Extrahieren von Informationen aus Texten nützlich sein.
Kunden sollten nach in einem großen Textkörper Funktionsnamen oder Schlüsselphrasen suchen und die Modifizierer und Aktionen untersuchen, die diese einzelnen Phrasen umgeben.

## <a name="phrases"></a>Phrasen

Für einen Linguisten stellt eine *Phrase* mehr als nur eine Abfolge von Wörtern dar.
Eine Phrase besteht aus einer Gruppe von Wörtern und spielt eine bestimmte Rolle im Satz.
Diese Wortgruppe kann zusammengesetzt oder vollständig ersetzt werden, und der Satz sollte trotzdem flüssig und grammatikalisch korrekt bleiben.

Sehen Sie sich den folgenden Satz an:

> I want to find a new hybrid automobile with Bluetooth. (Ich suche ein neues Hybridauto mit Bluetooth-Funktionalität.)

Dieser Satz enthält die Nominalphrase: „a new hybrid automobile with Bluetooth“ (ein neues Hybridauto mit Bluetooth-Funktionalität).
Wodurch zeichnet sich diese Phrase aus?
Der Satz kann (wenn auch etwas poetisch anmutend) umgeschrieben werden, indem die gesamte Phrase an den Anfang verschoben wird:

> A new hybrid automobile with Bluetooth I want to find. (Ein neues Hybridauto mit Bluetooth-Funktionalität suche ich.)

Alternativ kann diese Phrase durch eine Phrase mit ähnlicher Funktion und Bedeutung ersetzt werden, z.B. durch „a fancy new car“ (ein schickes, neues Auto):

> I want to find a fancy new car. (Ich suche ein schickes neues Auto.)

Würde stattdessen eine andere Untergruppe von Wörtern ersetzt werden, kämen ungewöhnliche oder unverständliche Sätze zustande.
Werfen wir einen Blick darauf, was geschieht, wenn der Teil „find a new“ (ein neues suchen) an den Anfang verschoben wird:

> Find a new I want to hybrid automobile with Bluetooth. (Ein neues suchen ich nach einem Hybridauto mit Bluetooth-Funktionalität.)

Das Ergebnis: Ein sehr sperriger und unverständlicher Satz.

Das Ziel eines Parsers besteht darin, alle diese Phrasen ausfindig zu machen.
Interessanterweise sind die Phrasen in der natürlichen Sprache oftmals ineinander geschachtelt.
Eine natürliche Darstellung dieser Phrasen ist eine Struktur wie etwa im folgenden Beispiel:

![Struktur](./Images/tree.png)

In dieser Struktur handelt es sich bei den mit „NP“ gekennzeichneten Verzweigungen um Nominalphrasen.
Von diesen Phrasen gibt es mehrere: *I* (ich), *a new hybrid automobile* (ein neues Hybridauto), *Bluetooth* (Bluetooth-Funktionalität) und *a new hybrid automobile with Bluetooth* (ein neues Hybridauto mit Bluetooth-Funktionalität).

## <a name="phrase-types"></a>Arten von Phrasen

| Bezeichnung | BESCHREIBUNG | Beispiel |
|-------|-------------|---------|
|ADJP   | Adjektivphrase | „so rude“ |
|ADVP   | Adverbialphrase | „clear through“ |
|CONJP  | Konjunktionalphrase | „as well as“ |
|FRAG   | Fragment, das für unvollständige oder lückenhafte Eingaben verwendet wird | „Highly recommended...“ |
|INTJ   | Interjection, Ausruf | „Hooray“ |
|LST    | Listenmarkierung, einschließlich Interpunktion | „#4)“ |
|NAC    | Not A Constituent, kein Konstituent: Wird verwendet, um die Eingrenzung einer nicht konstituierenden Phrase anzugeben |  „and for a good deal“ in „you get things and for a good deal“ |
|NP | Nominalphrase | „a tasty potato pancake“ |
|NX | Wird in bestimmten komplexen NPs verwendet, um den Hauptteil zu kennzeichnen| |
|PP | Präpositionalphrase| „in the pool“ |
|PRN    | Parenthetical, Einklammerung| „(so called)“ |
|PRT    | Partikel| „out“ in „ripped out“ |
|QP | Quantity Phrase, Mengenausdruck (z.B. komplexe/r Maßeinheit/Betrag) in einer Nominalphrase| „around $75“ |
|RRC    | Reduced Relative Clause, verkürzter Relativsatz| „still unresolved“ in „many issues still unresolved“ |
|S  | Satz | „This is a sentence.“
|SBAR   | Subordinate Clause, Nebensatz: Wird häufig durch eine subordinierende Konjunktion eingeleitet. | „as I left“ in „I looked around as I left.“|
|SBARQ  | Direkte Frage, die durch ein W-Wort oder eine W-Phrase eingeleitet wird | „What was the point?“ |
|SINV   | Inverted Declarative Sentence, Ausagesatz in umgekehrter Reihenfolge | „At no time were they aware.“ (Beachten Sie, dass das Subjekt „they“ hinter das Verb „were“ verschoben wurde.) |
|SQ | Ja/Nein-Frage in umgekehrter Reihenfolge oder Hauptsatz einer W-Frage | „Did they get the car?“ |
|UCP    | Unlike Coordinated Phrase, ungleichartige koordinierte Phrase| „small and with bugs“ (Beachten Sie, dass eine Adjektiv- und eine Präpositionalphrase durch ein „and“ verbunden werden.)|
|VP | Verbalphrase | „ran into the woods“ |
|WHADJP | Wh-Adjective Phrase, Adjektivphrase mit W-Wort | „how uncomfortable“ |
|WHADVP | Wh-Adverb Phrase, Adverbialphrase mit W-Wort| „when“ |
|WHNP   | Wh-Noun Phrase, Nominalphrase mit W-Wort| „which potato“, „how much soup“|
|WHPP   | Wh-Prepositional Phrase, Präpositionalphrase mit W-Wort| „in which country“|
|X  | Unbekannt, nicht eindeutig oder nicht einklammerbar.| Das erste „the“ in „the... the soup“ |


## <a name="specification"></a>Spezifikation

Bei den hier aufgeführten Strukturen werden S-Phrasen aus der [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42) verwendet.
