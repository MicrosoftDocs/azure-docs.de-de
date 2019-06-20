---
title: Benennungsstruktur im Analysetool – API für die linguistische Analyse
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie die Benennungsstruktur des Analysetools der API für die linguistische Analyse sowohl Flexibilität als auch Genauigkeit ermöglicht.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: c989f1115bc5a85bf09270c553ac1cb51bb4f170
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954708"
---
# <a name="analyzer-names"></a>Analysetoolnamen

> [!IMPORTANT]
> Die Vorschauversion für die linguistische Analyse wurde am 9. August 2018 außer Betrieb genommen. Es wird empfohlen, [Azure Machine Learning-Textanalysemodule](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) für die Textverarbeitung und -analyse zu verwenden.

Wir verwenden eine etwas komplizierte Benennungsstruktur für Analysetools, um sowohl Flexibilität bei den Analysetools als auch Genauigkeit beim Verstehen der Bedeutung der Benennung zu ermöglichen.
Namen für Analysetools bestehen aus vier Teilen: eine ID, eine Variante, eine Spezifikation und eine Implementierung.
Die Rolle der einzelnen Komponenten wird unten definiert.

## <a name="id"></a>ID
Zunächst hat ein Analysetool eine eindeutige ID: eine GUID.
Diese GUIDs sollten sich relativ selten ändern, sind aber die einzige Möglichkeit, ein bestimmtes Analysetool eindeutig zu beschreiben.

## <a name="kind"></a>Variante
Als Nächstes ist jedes Analysetool eine **Variante**.
Diese definiert ganz allgemein die Art der zurückgegebenen Analyse und sollte die Datenstruktur, die zur Darstellung dieser Analyse verwendet wird, eindeutig definieren.
Derzeit gibt es drei verschiedene Varianten:
 - [Token](Sentences-and-Tokens.md)
 - [POS-Tags](Pos-Tagging.md)
 - [Konstituentenstruktur](constituency-parsing.md)

## <a name="specification"></a>Spezifikation
Innerhalb einer bestimmten Variante können sich jedoch verschiedene Experten uneinig darüber sein, wie ein bestimmtes Phänomen analysiert werden soll.
Im Gegensatz zu Programmiersprachen gibt es keine klare und genaue Definition, wie dies geschehen soll.

Stellen Sie sich zum Beispiel vor, wir würden versuchen, die Token im englischen Satz „They didn't go.“ zu finden.
Beachten Sie insbesondere die Zeichenfolge „didn‘t“.
Eine mögliche Interpretation ist, die Zeichenfolge in zwei Token aufzuteilen: „did“ und „not“.
Der alternative Satz „They did not go“ würde dann denselben Satz an Token enthalten.
Eine andere Möglichkeit wäre es, dass sie in die Token „did“ und „n't“ aufgeteilt werden könnte.
Letzteres Token wird normalerweise nicht als Wort betrachtet, aber dieser Ansatz enthält mehr Informationen über die Oberflächenzeichenfolge, was manchmal nützlich sein kann.
Oder diese Kontraktion kann als einzelnes Wort betrachtet werden.

Egal, für welche Möglichkeit Sie sich entscheiden, sollten Sie diese konsistent beibehalten.
Dies ist genau die Rolle einer **Spezifikation**: zu entscheiden, was eine korrekte Darstellung werden soll.

Ausgaben von Analysetools können nur ordnungsgemäß mit Daten verglichen werden, die der gleichen Spezifikation entsprechen.

## <a name="implementation"></a>Implementierung

Oft gibt es mehrere Modelle, die versuchen, die gleichen Ergebnisse zu erzielen, aber mit unterschiedlichen Leistungsmerkmalen.
Ein Modell ist vielleicht schneller, dafür aber weniger genau; ein anderes könnte einen anderen Kompromiss eingehen.

Die Teil der **Implementierung** eines Analysetoolnamens wird verwendet, um diese Art von Informationen zu identifizieren, sodass der Benutzer das für seine Bedürfnisse am besten geeignete Analysetool auswählen kann.
