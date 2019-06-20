---
title: 'Datenfilterung: Benutzerdefinierter Translator'
titleSuffix: Azure Cognitive Services
description: Wenn Sie Dokumente übermitteln, die für das Trainieren eines benutzerdefinierten Systems verwendet werden sollen, durchlaufen die Dokumente eine Reihe von Verarbeitungs- und Filterschritten als Vorbereitung für das Training.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: ba6d1ed5bc26f121f32fad548e7a17c1f4998851
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389354"
---
# <a name="data-filtering"></a>Datenfilterung

Wenn Sie Dokumente übermitteln, die für das Trainieren eines benutzerdefinierten Systems verwendet werden sollen, durchlaufen die Dokumente eine Reihe von Verarbeitungs- und Filterschritten als Vorbereitung für das Training. Diese Schritte werden hier erläutert. Die Wissen der Filterung kann Ihnen helfen, die im benutzerdefinierten Translator angezeigte Satzanzahl sowie die Schritte zu verstehen, die Sie selbst durchführen können, um die Dokumente für das Training mit dem benutzerdefinierten Translator vorzubereiten.

## <a name="sentence-alignment"></a>Satzausrichtung
Wenn das Dokument nicht im XLIFF-, TMX- oder ALIGN-Format vorliegt, richtet der benutzerdefinierte Translator die Sätze Ihrer Quell- und Zieldokumente Satz für Satz aneinander aus. Der Translator führt keine Dokumentausrichtung durch. Er folgt Ihrer Benennung der Dokumente, um das entsprechende Dokument in der anderen Sprache zu finden. Innerhalb des Dokuments versucht der benutzerdefinierte Translator, den entsprechenden Satz in der anderen Sprache zu finden. Er verwendet Dokumentmarkups wie eingebettete HTML-Tags, um die Ausrichtung zu unterstützen.  

Wenn Sie eine erhebliche Abweichung zwischen der Anzahl der Sätze in den Quell- und Zieldokumenten feststellen, war das Dokument möglicherweise von Anfang an nicht parallel oder aus anderen Gründen nicht problemlos ausrichtbar. Die Dokumentpaare mit einen großen Unterschied (> 10%) zwischen den Sätzen auf beiden Seiten sollten erneut überprüft werden, um sicherzustellen, dass sie tatsächlich parallel sind. Der benutzerdefinierte Translator zeigt eine Warnung neben dem Dokument an, wenn sich die Anzahl der Sätze verdächtig unterscheidet.  


## <a name="deduplication"></a>Deduplizierung
Der benutzerdefinierte Translator entfernt die Sätze, die in Test- und Optimierungsdokumenten vorhanden sind, aus Trainingsdaten. Die Entfernung erfolgt dynamisch innerhalb der Trainingsausführung, nicht beim Datenverarbeitungsschritt. Vor einer solchen Entfernung gibt der benutzerdefinierte Translator die Anzahl der Sätze in der Projektübersicht an.  

## <a name="length-filter"></a>Längenfilter
* Entfernen Sie Sätze mit nur einem Wort auf jeder Seite.
* Entfernen Sie Sätze mit mehr als 100 Wörtern auf jeder Seite.  Chinesisch, Japanisch, Koreanisch sind ausgenommen.
* Entfernen Sie Sätze mit weniger als 3 Zeichen. Chinesisch, Japanisch, Koreanisch sind ausgenommen.
* Entfernen Sie Sätze mit mehr als 2000 Zeichen für Chinesisch, Japanisch und Koreanisch.
* Entfernen Sie Sätze mit weniger als 1% Alphazeichen.
* Entfernen Sie Wörterbucheinträge, die mehr als 50 Wörter enthalten.

## <a name="white-space"></a>Leerzeichen
* Ersetzen Sie eine beliebige Sequenz von Leerzeichen einschließlich Tabstopps und CR/LF-Sequenzen durch ein einzelnes Leerzeichen.
* Entfernen von voran- oder nachstehenden Leerzeichen im Satz

## <a name="sentence-end-punctuation"></a>Interpunktion am Satzende
Ersetzen Sie mehrere Satzendzeichen durch ein einzelnes.  

## <a name="japanese-character-normalization"></a>Normalisierung von japanischen Zeichen
Konvertieren von Buchstaben und Ziffern normaler Breite in Zeichen halber Breite.

## <a name="unescaped-xml-tags"></a>Nicht durch Escapezeichen formatierte XML-Tags
Durch Filtern werden Tags ohne Escapezeichen in Tags mit Escapezeichen umgewandelt:
* `&lt;` wird zu `&amp;lt;`
* `&gt;` wird zu `&amp;gt;`
* `&amp;` wird zu `&amp;amp;`

## <a name="invalid-characters"></a>Ungültige Zeichen
Der benutzerdefinierte Translator entfernt Sätze, die das Unicode-Zeichen U+FFFD enthalten. Das Zeichen U+FFFD gibt an, dass eine Codierungskonvertierung fehlgeschlagen ist.

## <a name="next-steps"></a>Nächste Schritte

- [Trainieren Sie ein Modell](how-to-train-model.md) im benutzerdefinierten Translator.
