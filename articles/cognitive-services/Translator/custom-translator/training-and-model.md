---
title: 'Definition: Training und Modelle – Custom Translator'
titleSuffix: Azure Cognitive Services
description: 'Ein Modell ist das System, das die Übersetzung für ein bestimmtes Sprachenpaar bereitstellt. Das Ergebnis eines erfolgreichen Trainings ist ein Modell. Zum Trainieren eines Modells werden drei sich gegenseitig ausschließende Datasets benötigt: Trainingsdataset, Optimierungsdataset und Testdataset.'
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: swmachan
ms.openlocfilehash: 1765aaed3c0562da13c539845bfe19f6f85ed4ef
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369017"
---
# <a name="what-are-trainings-and-models"></a>Definition: Training und Modelle

Ein Modell ist das System, das die Übersetzung für ein bestimmtes Sprachenpaar bereitstellt.
Das Ergebnis eines erfolgreichen Trainings ist ein Modell. Beim Trainieren eines Modells sind drei sich gegenseitig ausschließende Dokumenttypen erforderlich: Training, Optimieren und Testen. Auch der Dokumenttyp „Wörterbuch“ kann bereitgestellt werden. Weitere Informationen finden Sie unter [Satzzuordnung](./sentence-alignment.md#suggested-minimum-number-of-sentences).

Wenn beim Queuing eines Trainings nur ein Trainingsdataset angegeben wird, erstellt Benutzerdefinierter Translator automatisch Optimierungs- und Testdaten. Er verwendet eine zufällige Teilmenge von Sätzen aus den Trainingsdokumenten und schließt diese Sätze aus den eigentlichen Trainingsdaten aus.

## <a name="training-document-type-for-custom-translator"></a>Trainingsdokumenttyp für Benutzerdefinierter Translator

Die im Trainingsset enthaltenen Dokumente werden von Custom Translator als Grundlage zum Erstellen Ihres Modells verwendet. Beim Trainieren werden die in diesen Dokumenten vorhandenen Sätze einander zugeordnet (bzw. miteinander verbunden). Für das Erstellen Ihrer Trainingsdokumentenmappe gelten keine Einschränkungen. Sie können alle Dokumente, die Sie für relevant halten, in ein Modell aufnehmen. Schließen Sie sie in einem anderen Modell aus, um die Folgen anhand des [BLEU-Scores (Bilingual Evaluation Understudy)](what-is-bleu-score.md) anzuzeigen. Wenn das Optimierungs- und Testset konstant bleiben, können Sie mit der Zusammensetzung des Trainingssets experimentieren. Dies ist ein effektiver Ansatz, um die Qualität Ihres Übersetzungssystems zu verbessern.

Sie können mehrere Trainings in einem Projekt ausführen und den [BLEU-Score](what-is-bleu-score.md) für alle Trainingsläufe vergleichen. Wenn Sie vergleichsweise mehrere Trainings ausführen, achten Sie darauf, dass stets dieselben Optimierungs-/Testdaten angegeben werden. Überprüfen Sie auch manuell die Ergebnisse auf der Registerkarte [Test](how-to-view-system-test-results.md).

## <a name="tuning-document-type-for-custom-translator"></a>Optimierungsdokumenttyp für Benutzerdefinierter Translator

Custom Translator verwendet die in diesem Set enthaltenen parallelen Dokumente zum Optimieren der idealen Ergebnisse im Übersetzungssystem.

Mit den Optimierungsdaten werden beim Trainieren alle Parameter und Gewichtungen des Übersetzungssystems gemäß den optimalen Werten eingestellt. Wählen Sie Ihre Optimierungsdaten sorgfältig aus: Die Optimierungsdaten sollten repräsentativ für den Inhalt der Dokumente sein, die Sie künftig übersetzen möchten. Die Optimierungsdaten haben einen großen Einfluss auf die Übersetzungsqualität. Durch Optimierung kann das Übersetzungssystem Übersetzungen bereitstellen, die den Beispielen in den Optimierungsdaten am nächsten kommen. Sie benötigen nicht mehr als 2.500 Sätze in Ihren Optimierungsdaten. Für eine optimale Übersetzungsqualität wird empfohlen, das Optimierungsset manuell zu bestimmen, indem Sie die repräsentativsten Sätzen auswählen.

Wählen Sie für Ihr Optimierungsset Sätze aus, deren Länge sinnvoll und repräsentativ in Hinblick auf die künftig übersetzten Sätze ist. Zudem sollten Sie Sätze mit Wörtern und Ausdrücken auswählen, die Sie etwa auch in dem Umfang in künftigen Übersetzungen erwarten. In der Praxis liefern Sätze mit 7 bis 10 Wörtern die besten Ergebnisse, weil sie genug Kontext bieten, um Flexion zu erkennen, und weil diese Satzlänge aussagekräftig jedoch nicht zu komplex ist.

Die im Optimierungsset verwendeten Sätze müssen fehlerfrei sein und dem tatsächlichen Sprachgebrauch entsprechen. Dazu gehören keine Tabellenzellen, Gedichte, Auflistungen oder Sätze, die nur aus Satzzeichen oder Zahlen bestehen.

Wenn Sie Ihre Optimierungsdaten manuell auswählen, sollten sie nicht die gleichen Sätze wie Ihre Trainings- und Testdaten enthalten. Die Optimierungsdaten haben einen wesentlichen Einfluss auf die Übersetzungsqualität. Wählen Sie die Sätze also sorgfältig aus.

Wenn Sie sich über die Zusammenstellung der Optimierungsdaten nicht sicher sind, wählen Sie einfach die Trainingsdaten aus, und lassen Sie Benutzerdefinierter Translator die Optimierungsdaten für Sie auswählen. Wenn Benutzerdefinierter Translator die Optimierungsdaten automatisch festlegt, wird eine zufällige Teilmenge von Sätzen aus Ihren zweisprachigen Trainingsdokumenten ausgewählt und aus dem Trainingsmaterial ausgeschlossen.

## <a name="testing-dataset-for-custom-translator"></a>Testdataset für Custom Translator

Im Testset enthaltene parallele Dokumente werden zur Berechnung des BLEU-Scores verwendet. Dieser Wert gibt die Qualität Ihres Übersetzungssystems an. Er gibt Auskunft darüber, wie exakt die Übersetzungen, die das Übersetzungssystem anhand dieses Trainings anfertigt, mit den Referenzsätzen im Testdataset übereinstimmen.

Der BLEU-Score ist ein Maß für das Delta zwischen der automatischen Übersetzung und der Referenzübersetzung. Der Wert liegt zwischen 0 und 100. Der Wert 0 zeigt an, dass kein einziges Wort des Referenzmaterials in der Übersetzung vorkommt. Der Wert 100 bedeutet, dass die automatische Übersetzung genau mit der Referenz übereinstimmt: Das gleiche Wort befindet sich an genau derselben Stelle. Der zurückgegebene Wert ist der Durchschnitt des BLEU-Scores für alle Sätze der Testdaten.

Die Testdaten sollten im Quelle-Ziel-Paar parallele Dokumente mit paarweisen Entsprechungen enthalten: Sätze in der Ausgangssprache und ihre idealen Übersetzungen in der Zielsprache. Sie können die gleichen Kriterien verwenden, die Sie beim Zusammenstellen der Optimierungsdaten genutzt haben. Die Testdaten haben jedoch keinen Einfluss auf die Qualität des Übersetzungssystems. Sie werden ausschließlich zum Generieren des BLEU-Scores verwendet.

Sie benötigen nicht mehr als 2.500 Sätze als Testdaten. Wenn das Testsystem das Testset automatisch festlegt, wird eine zufällige Teilmenge von Sätzen aus Ihren zweisprachigen Trainingsdokumenten ausgewählt und aus dem Trainingsmaterial ausgeschlossen.

Sie können die benutzerdefinierten Übersetzungen des Testsets anzeigen und mit den in Ihrem Testset bereitgestellten Übersetzungen vergleichen, indem Sie im Modells zur Registerkarte „Test“ navigieren.