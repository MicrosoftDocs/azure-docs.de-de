---
title: 'Definition: Training und Modelle – Custom Translator'
titleSuffix: Azure Cognitive Services
description: 'Ein Modell ist das System, das die Übersetzung für ein bestimmtes Sprachenpaar bereitstellt. Das Ergebnis eines erfolgreichen Trainings ist ein Modell. Zum Trainieren eines Modells werden drei sich gegenseitig ausschließende Datasets benötigt: Trainingsdataset, Optimierungsdataset und Testdataset.'
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 530e87a84899b9659acd19f90c7c30ad3da3e7ba
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382305"
---
# <a name="what-are-trainings-and-models"></a>Definition: Training und Modelle

Ein Modell ist das System, das die Übersetzung für ein bestimmtes Sprachenpaar bereitstellt.
Das Ergebnis eines erfolgreichen Trainings ist ein Modell. Zum Trainieren eines Modells werden drei sich gegenseitig ausschließende Datasets benötigt: Trainingsdataset, Optimierungsdataset und Testdataset. Auch Wörterbuchdaten können bereitgestellt werden.

Wenn beim Queuing eines Trainings nur ein Trainingsdataset angegeben wird, erstellt Custom Translator automatisch Optimierungs- und Testdatasets. Er schließt 5.000 Sätze aus Ihren Trainingsdaten aus und verwendet jeweils 2.500 Sätze, um ein Optimierungs- und Testset zusammenzustellen.

## <a name="training-dataset-for-custom-translator"></a>Trainingsdataset für Custom Translator

Die im Trainingsset enthaltenen Dokumente werden von Custom Translator als Grundlage zum Erstellen Ihres Modells verwendet. Beim Trainieren werden die in diesen Dokumenten vorhandenen Sätze einander zugeordnet (bzw. miteinander verbunden). Für das Erstellen Ihrer Trainingsdokumentenmappe gelten keine Einschränkungen. Sie können alle Dokumente, die Sie für relevant halten, in ein Modell aufnehmen. Schließen Sie sie in einem anderen Modell aus, um die Folgen anhand des [BLEU-Scores (Bilingual Evaluation Understudy)](what-is-bleu-score.md) anzuzeigen. Wenn das Optimierungs- und Testset konstant bleiben, können Sie mit der Zusammensetzung des Trainingssets experimentieren. Dies ist ein effektiver Ansatz, um die Qualität Ihres Übersetzungssystems zu verbessern.

Sie können mehrere Trainings in einem Projekt ausführen und den [BLEU-Score](what-is-bleu-score.md) für alle Trainingsläufe vergleichen. Wenn Sie vergleichsweise mehrere Trainings ausführen, achten Sie darauf, dass stets dieselben Optimierungs-/Testdaten angegeben werden. Überprüfen Sie auch manuell die Ergebnisse auf der Registerkarte [Test](how-to-view-system-test-results.md).

## <a name="tuning-dataset-for-custom-translator"></a>Optimierungsdataset für Custom Translator

Custom Translator verwendet die in diesem Set enthaltenen parallelen Dokumente zum Optimieren der idealen Ergebnisse im Übersetzungssystem.

Mit dem Optimierungsset werden beim Trainieren alle Parameter und Gewichtungen des Übersetzungssystems gemäß den optimalen Werten eingestellt. Wählen Sie Ihr Optimierungsset sorgfältig aus: Das Optimierungsset sollte repräsentativ für den Inhalt der Dokumente sein, die Sie künftig übersetzen möchten. Das Optimierungsset hat einen großen Einfluss auf die Übersetzungsqualität. Durch Optimierung kann das Übersetzungssystem Übersetzungen bereitstellen, die den Beispielen im Optimierungsdataset am nächsten kommen. Ein Optimierungsset muss über 2.500 Sätze umfassen. Für eine optimale Übersetzungsqualität wird empfohlen, das Optimierungsset manuell zu bestimmen, indem Sie die repräsentativsten Sätzen auswählen.

Wählen Sie für Ihr Optimierungsset Sätze aus, deren Länge sinnvoll und repräsentativ in Hinblick auf die künftig übersetzten Sätze ist. Zudem sollten Sie Sätze mit Wörtern und Ausdrücken auswählen, die Sie etwa auch in dem Umfang in künftigen Übersetzungen erwarten. In der Praxis liefern Sätze mit 8 bis 18 Wörtern die besten Ergebnissen, weil sie genug Kontext bieten, um Flexion zu erkennen, und weil diese Satzlänge aussagekräftig jedoch nicht zu komplex ist.

Die im Optimierungsset verwendeten Sätze müssen fehlerfrei sein und dem tatsächlichen Sprachgebrauch entsprechen. Dazu gehören keine Tabellenzellen, Gedichte, Auflistungen oder Sätze, die nur aus Satzzeichen oder Zahlen bestehen.

Wenn Sie Ihr Optimierungsdataset manuell auswählen, sollte es nicht die gleichen Sätze wie Ihre Optimierungs- und Testdaten enthalten. Das Optimierungsset hat einen wesentlichen Einfluss auf die Übersetzungsqualität. Wählen Sie die Sätze also sorgfältig aus.

Wenn Sie sich über die Zusammenstellung des Optimierungssets nicht sicher sind, wählen Sie einfach das Trainingsset aus, und lassen Sie Custom Translator Ihr Optimierungsset für Sie auswählen. Wenn Custom Translator das Optimierungsset automatisch festlegt, wird eine zufällige Teilmenge von Sätzen aus Ihren zweisprachigen Trainingsdokumenten ausgewählt und aus dem Trainingsmaterial ausgeschlossen.

## <a name="testing-dataset-for-custom-translator"></a>Testdataset für Custom Translator

Im Testset enthaltene parallele Dokumente werden zur Berechnung des BLEU-Scores verwendet. Dieser Wert gibt die Qualität Ihres Übersetzungssystems an. Er gibt Auskunft darüber, wie exakt die Übersetzungen, die das Übersetzungssystem anhand dieses Trainings anfertigt, mit den Referenzsätzen im Testdataset übereinstimmen.

Der BLEU-Score ist ein Maß für das Delta zwischen der automatischen Übersetzung und der Referenzübersetzung. Der Wert liegt zwischen 0 und 100. Der Wert 0 zeigt an, dass kein einziges Wort des Referenzmaterials in der Übersetzung vorkommt. Der Wert 100 bedeutet, dass die automatische Übersetzung genau mit der Referenz übereinstimmt: Das gleiche Wort befindet sich an genau derselben Stelle. Der zurückgegebene Wert ist der Durchschnitt des BLEU-Scores für alle Sätze des Testsets.

Das Testset muss parallele Dokumente enthalten mit paarweisen Entsprechungen: Sätze in der Zielsprache und ihre idealen Übersetzungen in der Ausgangssprache. Sie können die gleichen Kriterien verwenden, die Sie beim Zusammenstellen des Optimierungssets genutzt haben. Das Testset hat jedoch keinen Einfluss auf die Qualität des Übersetzungssystems. Es wird ausschließlich zum Generieren des BLEU-Scores verwendet.

Ein Testset benötigt bis zu 2.500 Sätze. Wenn das Testsystem das Testset automatisch festlegt, wird eine zufällige Teilmenge von Sätzen aus Ihren zweisprachigen Trainingsdokumenten ausgewählt und aus dem Trainingsmaterial ausgeschlossen.

Sie können die benutzerdefinierten Übersetzungen des Testsets anzeigen und mit den in Ihrem Testset bereitgestellten Übersetzungen vergleichen, indem Sie im Modells zur Registerkarte „Test“ navigieren.
