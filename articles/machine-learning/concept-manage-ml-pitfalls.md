---
title: Vermeiden von Überanpassung und unausgeglichenen Daten mit AutoML
titleSuffix: Azure Machine Learning
description: Identifizieren und beheben Sie mit den automatisierten Lösungen für automatisiertes maschinelles Lernen von Azure Machine Learning häufige Fehler bei ML-Modellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: ba85bda1e322d3efd467527b48bd4cd90eb7ce8c
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922629"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Verhindern von Überanpassung und unausgeglichenen Daten durch automatisiertes maschinelles Lernen

Häufige Fehler bei der Erstellung von Machine Learning-Modellen sind Überanpassung und unausgeglichene Daten. Standardmäßig bietet das automatisierte maschinelle Lernen von Azure Machine Learning Diagramme und Metriken, die Ihnen helfen, diese Risiken zu identifizieren, und implementiert bewährte Methoden, um sie zu mindern. 

## <a name="identify-over-fitting"></a>Erkennen von Überanpassung

Überanpassung bei maschinellem Lernen tritt auf, wenn ein Modell zu gut zu den Trainingsdaten passt und daher nicht in der Lage ist, eine genaue Vorhersage für unbekannte Testdaten zu bieten. Mit anderen Worten, das Modell hat sich schlicht bestimmte Muster und bestimmtes Rauschen in den Trainingsdaten gemerkt, ist aber nicht flexibel genug, um Vorhersagen zu echten Daten zu treffen.

Sehen Sie sich die folgenden trainierten Modelle und deren entsprechende Trainings- und Testgenauigkeiten an.

| Modell | Trainingsgenauigkeit | Testgenauigkeit |
|-------|----------------|---------------|
| Ein | 99,9 % | 95 % |
| B | 87 % | 87 % |
| C | 99,9 % | 45 % |

Bei Betrachtung von Modell **A** gibt es ein gängiges Missverständnis: Wenn die Testgenauigkeit bei unbekannten Daten niedriger ist als die Trainingsgenauigkeit, ist das Modell überangepasst. Die Testgenauigkeit sollte jedoch immer kleiner als die Trainingsgenauigkeit sein, und die Unterscheidung zwischen „überangepasst“ und „angemessen angepasst“ wird heruntergebrochen auf *wie viel* ungenauer. 

Wenn die Modelle **A** und **B** verglichen werden, ist das Modell **A** ein besseres Modell, da es eine höhere Testgenauigkeit hat, und obwohl die Testgenauigkeit geringfügig niedriger bei 95 % liegt, ist dies kein bedeutender Unterschied, der nahelegt, dass eine Überanpassung vorliegt. Modell **B** würde einfach deshalb nicht ausgewählt, weil die Trainings- und die Testgenauigkeit näher beieinander liegen.

Model **C** repräsentiert einen eindeutigen Fall von Überanpassung. Die Trainingsgenauigkeit ist sehr hoch, aber die Testgenauigkeit ist nirgends annähernd so hoch. Diese Unterscheidung ist subjektiv, aber sie ergibt sich aus dem Wissen über Ihr Problem und Ihre Daten und darüber, welches Ausmaß an Fehlern akzeptabel ist.

## <a name="prevent-over-fitting"></a>Verhindern von Überanpassung

In den gravierendsten Fällen führt ein überangepasstes Modell zu der Annahme, dass die beim Training verwendeten Featurewertkombinationen immer die exakt selbe Ausgabe für das Ziel bringen.

Die beste Möglichkeit, eine über Anpassung zu verhindern, besteht darin, die bewährten Methoden für maschinelles Lernen (ML) zu befolgen. Dazu gehören:

* Verwenden von möglichst vielen Trainingsdaten und Eliminieren statistischer systematischer Abweichungen
* Verhindern von Zielungenauigkeit (target leakage)
* Verwenden weniger Features
* **Regularisierung und Hyperparameteroptimierung**
* **Einschränkungen der Modellkomplexität**
* **Kreuzvalidierung**

Im Kontext von automatisiertem ML sind die ersten drei dieser Aspekte **bewährte Methoden, die Sie implementieren**. Die letzten drei fett formatierten Aspekte sind **bewährte Methoden, die standardmäßig von automatischem ML implementiert werden**, um Überanpassung zu vermeiden. In Konfigurationen ohne automatisiertes ML sollten alle sechs bewährten Methoden beachtet werden, um Überanpassung von Modellen zu verhindern.

### <a name="best-practices-you-implement"></a>Bewährte Methoden, die Sie implementieren

Die Verwendung von **mehr Daten** ist die einfachste und beste Möglichkeit, eine Überanpassung zu verhindern, und erhöht als Zusatzbonus in den meisten Fällen die Genauigkeit. Wenn Sie mehr Daten verwenden, wird es für das Modell schwieriger, sich genaue Muster zu merken, und es ist gezwungen, Lösungen zu finden, die flexibler sind, um mehr Bedingungen zu erfüllen. Außerdem ist es wichtig, **statistische systematische Abweichungen** zu erkennen, um sicherzustellen, dass die Trainingsdaten keine isolierten Muster enthalten, die in echten Vorhersagedaten nicht vorhanden sind. Ein Auflösen dieses Szenarios kann schwierig sein, da möglicherweise keine Überanpassung zwischen Ihren Trainings- und Testsätzen vorliegt, beim Vergleich mit echten Testdaten aber Überanpassung vorhanden ist.

**Zielungenauigkeit** (target leakage) ist ein ähnliches Problem, bei dem Sie möglicherweise keine Überanpassung zwischen den Trainings- und Testsätzen feststellen, aber Überanpassung zur Vorhersagezeit auftritt. Zielungenauigkeit tritt auf, wenn Ihr Modell während des Trainings „betrügt“, indem es Zugriff auf Daten hat, auf die es zur Vorhersagezeit normalerweise keinen Zugriff haben sollte. Angenommen, Ihr Problem darin besteht, am Montag den Preis vorherzusagen, den eine Ware am Freitag haben wird, aber eines Ihrer Features enthielt versehentlich Daten von Donnerstagen, und dies wären Daten, die das Modell zur Vorhersagezeit nicht zur Verfügung hat, weil es nicht in die Zukunft schauen kann. Zielungenauigkeit ist ein Fehler, der sich leicht übersehen lässt, ist aber häufig durch eine ungewöhnlich hohe Genauigkeit für Ihr Problem gekennzeichnet. Wenn Sie versuchen, den Aktienkurs vorherzusagen und ein Modell mit einer Genauigkeit von 95 % trainiert haben, gibt es wahrscheinlich eine Zielungenauigkeit in ihren Features.

Ein **Entfernen von Features** kann ebenfalls gegen Überanpassung helfen, indem verhindert wird, dass das Modell zu viele Felder verwendet, um sich bestimmte Muster zu merken. Dies führt dazu, dass das Modell flexibler wird. Ein quantitatives Messen ist möglicherweise schwierig, aber wenn Sie Features entfernen und dieselbe Genauigkeit beibehalten können, haben Sie das Modell wahrscheinlich flexibler gestaltet und das Risiko einer Überanpassung verringert.

### <a name="best-practices-automated-ml-implements"></a>Bewährte Methoden, die von automatisiertem ML implementiert werden

**Regularisierung** ist der Prozess, bei dem eine Kostenfunktion minimiert wird, um komplexe und überangepasste Modelle zu pönalisieren. Es gibt unterschiedliche Arten von Regularisierungsfunktionen, aber normalerweise pönalisieren diese alle die Modellkoeffizientengröße, -varianz und -komplexität. Automatisiertes ML verwendet L1 (Lasso), L2 (Ridge) und ElasticNet (L1 und L2 gleichzeitig) in verschiedenen Kombinationen mit unterschiedlichen Modellhyperparametereinstellungen, die Überanpassung steuern. In einfachen Worten, automatisiertes ML variiert, wie stark ein Modell reguliert wird, und wählt das beste Ergebnis aus.

Automatisiertes ML implementiert auch explizite **Einschränkungen der Modellkomplexität**, um Überanpassung zu verhindern. In den meisten Fällen erfolgt diese Implementierung speziell für Entscheidungsstruktur- oder Gesamtstrukturalgorithmen, bei denen die maximale Tiefe der einzelnen Strukturen und die Gesamtzahl der in der Gesamtstruktur oder in den Kombinationstechniken verwendeten Strukturen begrenzt sind.

**Kreuzvalidierung** ist der Prozess, bei dem viele Teilmengen Ihrer vollständigen Trainingsdaten erstellt werden und ein Modell mit jeder Teilmenge trainiert wird. Die Idee ist, dass ein Modell „glücklich“ werden und eine große Genauigkeit mit einer Teilmenge erzielen könnte, aber durch Verwenden vieler Teilmengen wird das Modell diese hohe Genauigkeit nicht jedes Mal erzielen. Wenn Sie eine Kreuzvalidierung vornehmen, stellen Sie ein Validierungsdataset mit zurückgehaltenen Daten bereit, und geben Sie Ihre Kreuzvalidierungsfalten (Anzahl der Teilmengen) an. Automatisiertes ML trainiert daraufhin Ihr Modell und optimiert Hyperparameter, um den Fehler für Ihren Validierungssatz zu minimieren. Eine Kreuzvalidierungsfalte könnte überangepasst sein, aber dadurch, dass viele von ihnen verwendet werden, wird die Wahrscheinlichkeit verringert, dass das endgültige Modell überangepasst ist. Der Nachteil ist, dass Kreuzvalidierung längere Trainingszeiten und somit höhere Kosten verursacht, denn anstatt ein Modell einmal zu trainieren, trainieren Sie es einmal mit jeder der *n* Kreuzvalidierungsteilmengen. 

> [!NOTE]
> Kreuzvalidierung ist nicht standardmäßig aktiviert. Sie muss in den Einstellungen für automatisiertes ML konfiguriert werden. Nachdem Kreuzvalidierung konfiguriert und ein Validierungsdataset bereitgestellt wurde, wird der Prozess jedoch für Sie automatisiert. [Weitere Informationen zur Konfiguration der Kreuzvalidierung in AutoML](how-to-configure-cross-validation-data-splits.md)

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identifizieren von Modellen mit unausgeglichenen Daten

Unausgeglichene Daten finden sich häufig in Daten für Klassifizierungsszenarien des maschinellen Lernens und beziehen sich auf Daten, die ein überproportionales Verhältnis von Beobachtungen in den einzelnen Klassen enthalten. Diese Unausgeglichenheit kann zu einem falsch wahrgenommenen positiven Effekt der Genauigkeit eines Modells führen, da die Eingabedaten eine Abweichung zu einer Klasse aufweisen, was dazu führt, dass das trainierte Modell diese Abweichung imitiert. 

Darüber hinaus generieren Durchläufe des automatisierten maschinellen Lernens automatisch die folgenden Diagramme, die Ihnen helfen können, die Richtigkeit der Klassifizierungen Ihres Modells zu verstehen und Modelle zu identifizieren, die möglicherweise von nicht ausgeglichenen Daten beeinflusst werden.

Diagramm| BESCHREIBUNG
---|---
[Konfusionsmatrix](how-to-understand-automated-ml.md#confusion-matrix)| Bewertet die ordnungsgemäß klassifizierten Bezeichnungen anhand der tatsächlichen Bezeichnungen der Daten. 
[Genauigkeit-Trefferquote](how-to-understand-automated-ml.md#precision-recall-curve)| Bewertet das Verhältnis der richtigen Bezeichnungen anhand des Verhältnisses der gefundenen Bezeichnungsinstanzen der Daten. 
[ROC-Kurven](how-to-understand-automated-ml.md#roc-curve)| Bewertet das Verhältnis von richtigen Bezeichnungen anhand des Verhältnisses von falsch positiven Bezeichnungen.

## <a name="handle-imbalanced-data"></a>Behandeln von unausgeglichenen Daten 

Als Teil seines Ziels, den Workflow des maschinellen Lernens zu vereinfachen, verfügt das **automatisierte maschinelle Lernen über integrierte Funktionen**, die bei der Verarbeitung unausgeglichener Daten helfen. Beispiel: 

- Eine **Gewichtungsspalte**: Das automatisierte maschinelle Lernen unterstützt eine gewichtete Spalte als Eingabe, wodurch Zeilen in den Daten eine höhere oder niedrigere Gewichtung erhalten. Auf diese Weise kann eine Klasse mehr oder weniger „wichtig“ festgelegt werden.

- Die vom automatisierten maschinellen Lernen verwendeten Algorithmen erkennen eine Unausgeglichenheit, wenn die Anzahl der Stichproben in der Minderheitsklasse nicht mehr als 20 % der Anzahl der Stichproben in der Mehrheitsklasse beträgt, wobei als Minderheitsklasse die Klasse mit den wenigsten Stichproben und als Mehrheitsklasse die Klasse mit den meisten Stichproben bezeichnet wird. In der Folge führt AutoML ein Experiment mit Teilprobendaten aus, um zu überprüfen, ob sich das Problem mit Klassengewichtungen lösen und die Leistung so verbessern lässt. Wenn bei diesem Experiment eine bessere Leistung erzielt wurde, wird die Problemlösung übernommen.

- Verwenden Sie eine Leistungsmetrik, die besser mit unausgeglichenen Daten umgeht. Beispielsweise ist AUC_weighted eine primäre Metrik, die den Beitrag jeder Klasse basierend auf der relativen Anzahl von Stichproben berechnet, die diese Klasse darstellen. Daher ist der Wert gegenüber einem Ungleichgewicht robuster.

Die folgenden Verfahren sind zusätzliche Optionen zum Behandeln von unausgeglichenen Daten **außerhalb des automatisierten maschinellen Lernens**. 

- Erneutes Sampling zum Ausgleichen der Unausgeglichenheit von Klassen, entweder durch Upsampling der kleineren Klassen oder durch Downsampling der größeren Klassen. Diese Methoden erfordern Fachwissen, um sie zu verarbeiten und zu analysieren.

- Überprüfen Sie Leistungsmetriken für unausgeglichene Daten. Beispielsweise ist der F1-Score das harmonische Mittel aus Genauigkeit und Abruf. Die Genauigkeit misst die Präzision eines Klassifizierers. Eine höhere Genauigkeit weist auf eine geringere Anzahl von False Positives hin. Der Abruf misst hingegen die Vollständigkeit eines Klassifizierers. Ein höherer Abrufwert weist auf eine geringere Anzahl von False Negatives hin.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Modelle mithilfe des automatisierten maschinellen Lernens erstellt werden, und sehen Sie sich Beispiele dazu an:

+ [Tutorial: Automatisches Trainieren eines Regressionsmodell mithilfe von Azure Machine Learning](tutorial-auto-train-models.md)

+ Konfigurieren Sie die Einstellungen für ein automatisches Trainingsexperiment:
  + Verwenden Sie in Azure Machine Learning Studio [diese Schritte](how-to-use-automated-ml-for-ml-models.md).
  + Mit dem Python SDK [verwenden Sie diese Schritte](how-to-configure-auto-train.md).


