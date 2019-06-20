---
title: 'Klassifizierung: Vorhersagen des Kreditrisikos (kostensensibel)'
titleSuffix: Azure Machine Learning service
description: In diesem Artikel wird gezeigt, wie über die grafische Benutzeroberfläche ein komplexes Experiment für maschinelles Lernen erstellt werden kann. Sie erfahren, wie benutzerdefinierte Python-Skripts implementiert und mehrere Modelle verglichen werden, um die beste Option auszuwählen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: d714756c19b94eafc40cc0dbeffbc07704e8f94e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787819"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Beispiel 4 – Klassifizierung: Vorhersagen des Kreditrisikos (kostensensibel)

In diesem Artikel wird gezeigt, wie über die grafische Benutzeroberfläche ein komplexes Experiment für maschinelles Lernen erstellt werden kann. Sie erfahren, wie benutzerdefinierte Logik mit Python-Skripts implementiert wird und mehrere Modelle verglichen werden, um die beste Option auszuwählen.

In diesem Beispiel wird eine Klassifizierung trainiert, um das Kreditrisiko anhand von Kreditantragsinformationen wie Kreditgeschichte, Alter und Anzahl der Kreditkarten vorherzusagen. Sie können die Konzepte in diesem Artikel aber auch anwenden, um Ihre eigenen Probleme durch maschinelles Lernen zu lösen.

Wenn Sie gerade erst mit dem Thema „Maschinelles Lernen“ beginnen, sollten Sie sich zuerst das [Basisbeispiel für Klassifizierung](ui-sample-classification-predict-credit-risk-basic.md) ansehen.

Das vollständige Diagramm für dieses Experiment sieht wie folgt aus:

[![Graph des Experiments](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wählen Sie die Schaltfläche **Öffnen** für das Experiment „Beispiel 4“ aus:

    ![Öffnen des Experiments](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Daten

Wir verwenden das Dataset „German Credit Card“ aus dem UC Irvine-Repository. Dieses Dataset enthält 1.000 Stichproben mit 20 Features und 1 Bezeichnung. Jede Stichprobe stellt eine Person dar. Die 20 Features enthalten numerische und kategorische Features. Weitere Informationen zu diesem Dataset finden Sie auf der [UCI-Website](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29). Die letzte Spalte ist der Bezeichnung, die das Kreditrisiko bezeichnet und nur über zwei mögliche Werte verfügt: hohes Kreditrisiko = 2 und niedriges Kreditrisiko = 1.

## <a name="experiment-summary"></a>Experimentzusammenfassung

In diesem Experiment vergleichen wir zwei verschiedene Ansätze für die Generierung von Modellen zur Lösung dieses Problems:

- Training mit dem Originaldataset.
- Training mit einem replizierten Dataset.

Bei beiden Ansätzen werten wir die Modelle aus, indem wir das Testdataset mit Replikation verwenden, um sicherzustellen, dass die Ergebnisse auf die Kostenfunktion abgestimmt sind. Wir testen zwei Klassifizierer mit beiden Ansätzen: **Two-Class Support Vector Machine** und **Two-Class Boosted Decision Tree**.

Die Kosten für die Fehlklassifizierung einer Stichprobe mit geringem Risiko betragen 1, die Kosten für die Fehlklassifizierung einer Stichprobe mit hohem Risiko betragen 5. Wir verwenden ein Modul **Execute Python Script** (Python-Skript ausführen), um diese Fehlklassifizierungskosten zu berücksichtigen.

So sieht das Diagramm des Experiments aus:

[![Graph des Experiments](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Datenverarbeitung

Wir beginnen mit der Verwendung des Moduls **Metadata Editor** (Metadaten-Editor), um Spaltennamen hinzuzufügen und die standardmäßigen Spaltennamen durch aussagekräftigere Namen zu ersetzen, die aus der Datasetbeschreibung auf der UCI-Website entnommen wurden. Wir stellen die neuen Spaltennamen als durch Trennzeichen getrennte Werte im Namensfeld **New column** (Neue Spalte) des **Metadaten-Editors** zur Verfügung.

Als Nächstes generieren wir die Trainings- und Testdatasets, die zum Entwickeln des Risikovorhersagemodells verwendet werden. Wir teilen das ursprüngliche Dataset mit dem Modul **Split Data** (Daten aufteilen) in Trainings- und Testdatasets mit derselben Größe auf. Um Datasets gleicher Größe zu erstellen, legen wir die Option **Fraction of rows in the first output dataset** (Bruchteil der Zeilen im ersten Ausgabedataset) auf 0,5 fest.

### <a name="generate-the-new-dataset"></a>Generieren des neuen Datasets

Da die Kosten für die Unterschätzung des Risikos hoch sind, legen wir die Kosten der Fehlklassifizierung wie folgt fest:

- Für Fälle mit hohem Risiko, die fälschlicherweise als risikoarm eingestuft werden: 5
- Für Fälle mit geringem Risiko, die fälschlicherweise als hohes Risiko eingestuft werden: 1

Um diese Kostenfunktion abzubilden, generieren wir ein neues Dataset. Im neuen Dataset wird jede Stichprobe mit hohem Risiko fünf Mal repliziert, aber die Anzahl der Stichproben mit niedrigem Risiko ändert sich nicht. Wir teilen die Daten vor der Replikation in Trainings- und Testdatasets auf, um zu verhindern, dass die gleiche Zeile in beiden Datasets enthalten ist.

Um die Hochrisikodaten zu replizieren, fügen wir diesen Python-Code in ein Modul **Execute Python Script** (Python-Skript ausführen) ein:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

Das Modul **Execute Python Script** repliziert sowohl die Trainings-als auch die Testdatasets.

### <a name="feature-engineering"></a>Featureentwicklung

Der Algorithmus **Two-Class Support Vector Machine** erfordert normalisierte Daten. Daher verwenden wir das Modul **Normalize Data** (Daten normalisieren), um die Bereiche aller numerischen Features mit einer `tanh`-Transformation zu normalisieren. Eine `tanh`-Transformation konvertiert alle numerischen Features in Werte innerhalb eines Bereichs von 0 bis 1 unter Beibehaltung der Gesamtverteilung der Werte.

Das Modul **Two-Class Support Vector Machine** verarbeitet Zeichenfolgenfeatures und konvertiert sie in kategorische Merkmale und dann in binäre Merkmale mit einem Wert von 0 oder 1. Wir müssen diese Features also nicht normalisieren.

## <a name="models"></a>Modelle

Da wir zwei Klassifizierer anwenden (**Two-Class Support Vector Machine** (SVM) und **Two-Class Boosted Decision Tree**) und auch zwei Datasets verwenden, generieren wir insgesamt vier Modelle:

- SVM trainiert mit den ursprünglichen Daten.
- SVM trainiert mit den replizierten Daten.
- Boosted Decision Tree trainiert mit den ursprünglichen Daten.
- Boosted Decision Tree trainiert mit den replizierten Daten.

Wir verwenden den experimentellen Standardworkflow, um die Modelle zu erstellen, zu trainieren und zu testen:

1. Initialisieren Sie die Lernalgorithmen mit **Two-Class Support Vector Machine** und **Two-Class Boosted Decision Tree**.
1. Verwenden Sie **Train Model** (Modell trainieren), um den Algorithmus auf die Daten anzuwenden und das eigentliche Modell zu erstellen.
1. Verwenden Sie **Score Model** (Modell bewerten), um Bewertungen anhand der Testbeispiele zu generieren.

Die folgende Abbildung zeigt einen Ausschnitt aus diesem Experiment, in dem die ursprünglichen und replizierten Trainingsdatasets zum Trainieren von zwei verschiedenen SVM-Modellen verwendet werden. **Train Model** ist mit dem Trainingsdataset verbunden, **Score Model** mit dem Testdataset.

![Abbildung des Experiments](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

In der Auswertungsphase des Experiments berechnen wir die Genauigkeit jedes der vier Modelle. Für diese Experiment verwenden wir **Evaluate Model**, um Stichproben zu vergleichen, die die gleichen Fehlklassifizierungskosten aufweisen.

Das Modul **Evaluate Model** kann die Leistungsmetriken für zwei bewertete Modelle berechnen. Daher verwenden wir eine Instanz von **Evaluate Model**, um die beiden SVM-Modelle auszuwerten, und eine weitere Instanz von **Evaluate Model**, um die beiden Boosted Decision Tree-Modelle auszuwerten.

Beachten Sie, dass das replizierte Testdataset als Eingabe für **Score Model** verwendet wird. Mit anderen Worten: Die endgültigen Genauigkeitsbewertungen beinhalten die Kosten für die falsche Bewertung von Bezeichnungen.

## <a name="combine-multiple-results"></a>Kombinieren mehrerer Ergebnisse

Das Modul **Evaluate Model** generiert eine Tabelle mit einer einzelnen Zeile, die verschiedene Metriken enthält. Um einen einzigen Satz von Genauigkeitsergebnissen zu erstellen, verwenden wir zunächst **Add Rows**, um die Ergebnisse in einer einzigen Tabelle zu kombinieren. Wir verwenden dann das folgende Python-Skript im Modul **Execute Python Script**, um den Modellnamen und den Trainingsansatz für jede Zeile in der Ergebnistabelle hinzuzufügen:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>Ergebnisse

Zum Anzeigen der Ergebnisse des Experiments können Sie mit der rechten Maustaste auf die Visualisierungsausgabe des letzten Moduls **Select Columns in Dataset** klicken.

![Visualisieren der Ausgabe](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

Die erste Spalte listet den Machine Learning-Algorithmus auf, der verwendet wurde, um das Modell zu generieren.
Die zweite Spalte gibt den Typ des Trainingsdatasets an.
Die dritte Spalte enthält den kostensensiblen Genauigkeitswert.

Aus diesen Ergebnissen können Sie ersehen, dass die beste Genauigkeit durch das Modell bereitgestellt wird, das mit **Two-Class Support Vector Machine** erstellt und mit dem replizierten Trainingsdataset trainiert wurde.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Untersuchen Sie die anderen Beispiele, die für die grafische Benutzeroberfläche zur Verfügung stehen:

- [Beispiel 1 – Regression: Vorhersagen des Preises eines Autos](ui-sample-regression-predict-automobile-price-basic.md)
- [Beispiel 2 – Regression: Vergleichen von Algorithmen für die Vorhersage von Autopreisen](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Beispiel 3 – Klassifizierung: Vorhersagen des Kreditrisikos](ui-sample-classification-predict-credit-risk-basic.md)
- [Beispiel 5 – Klassifizierung: Vorhersage der Kundenabwanderung](ui-sample-classification-predict-churn.md)
