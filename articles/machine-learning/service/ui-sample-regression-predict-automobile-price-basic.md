---
title: 'Regression: Preisprognose'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie, ohne eine einzige Codezeile zu schreiben, ein Machine Learning-Modell erstellen, um den-Preises eines Fahrzeugs vorherzusagen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 9dfa4b62f5cb79a5716f6f29651e85d0f8a3a409
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787842"
---
# <a name="sample-1---regression-predict-price"></a>Beispiel 1 – Regression: Preisprognose

Erfahren Sie, wie Sie über die grafische Benutzeroberfläche ein Machine Learning-Regressionsmodell erstellen, ohne eine einzige Codezeile zu schreiben.

In diesem Experiment wird ein **Entscheidungswaldregressor** trainiert, um den Preis eines Fahrzeugs anhand technischer Merkmale wie Marke, Modell, Leistung und Größe vorherzusagen. Weil wir versuchen, die Frage „Wie viel?“ zu beantworten, wird dies als Regressionsproblem bezeichnet. Sie können jedoch die gleichen elementaren Schritte aus diesem Experiment anwenden, um jede Art von Problem für maschinelles Lernen zu bewältigen, sei es Regression, Klassifizierung, Clustering usw.

Die elementaren Schritte des Trainings eines Machine Learning-Modells sind:

1. Abrufen von Daten
1. Vorverarbeiten der Daten
1. Modelltraining
1. Auswerten des Modells

Das endgültige, vollständige Diagramm des Experiments, an dem wir arbeiten sieht wie folgt aus. Es werden die Gründe für alle Module bereitgestellt, sodass Sie ähnliche Entscheidungen selbst treffen können.

![Diagramm des Experiments](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wählen Sie die Schaltfläche **Öffnen** für das Experiment „Beispiel 1“ aus:

    ![Öffnen des Experiments](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Abrufen von Daten

In diesem Experiment verwenden wir das Dataset **Automobile Price Data (Raw)** (Automobilpreisdaten (Rohdaten)) aus dem UCI Machine Learning-Repository. Dieses Dataset enthält 26 Spalten mit Informationen zu Automobilen, darunter Marke, Modell, Preis, Fahrzeugmerkmale (etwa die Anzahl der Zylinder), Treibstoffverbrauch und eine Versicherungsrisikobewertung. Das Ziel dieses Experiments besteht darin, den Preis eines Autos vorherzusagen.

## <a name="pre-process-the-data"></a>Vorverarbeiten der Daten

Zu den Hauptaufgaben der Datenaufbereitung gehören die Datenbereinigung, Integration, Transformation, Reduktion und Diskretisierung oder Quantisierung. In der grafischen Benutzeroberfläche finden Sie Module zur Durchführung dieser Vorgänge und anderer Aufgaben der Datenvorverarbeitung in der Gruppe **Data Transformation** (Datentransformation) im linken Bereich.

Wir verwenden das Modul **Select Columns in Dataset** (Spalten in Dataset auswählen), um normalisierte Verluste auszuschließen, die viele fehlende Werte aufweisen. Anschließend verwenden wir **Clean Missing Data** (Fehlende Daten bereinigen), um die Zeilen zu entfernen, die fehlende Werte aufweisen. Dies hilft, einen bereinigten Satz von Trainingsdaten zu erstellen.

![Datenvorverarbeitung](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Modelltraining

Machine Learning-Probleme sind vielfältig. Machine Learning-Aufgaben sind z.B. Klassifizierung, Clustering, Regression und Empfehlungssysteme, die möglicherweise jeweils einen anderen Algorithmus erfordern. Die Auswahl des Algorithmus hängt häufig von den Anforderungen des Anwendungsfalls ab. Nachdem Sie einen Algorithmus ausgewählt haben, müssen Sie seine Parameter optimieren, um ein genaueres Modell zu trainieren. Sie müssen alle Modelle basierend auf Metriken wie Genauigkeit, Verständlichkeit und Effizienz auswerten.

Da das Ziel dieses Experiments darin besteht, Automobilpreise vorherzusagen, und die Bezeichnungsspalte (Preis) reelle Zahlen enthält, ist ein Regressionsmodell eine gute Wahl. In Anbetracht der Tatsache, dass die Anzahl der Features relativ klein ist (weniger als 100) und diese Features nicht spärlich sind, ist es wahrscheinlich, dass die Entscheidungsgrenze nichtlinear ist. Daher verwenden wir die **Entscheidungswaldregression** für dieses Experiment.

Wir verwenden das Modul **Split Data** (Daten teilen), um nach dem Zufallsprinzip die eingegebenen Daten so aufzuteilen, dass das Trainingsdataset 70% der ursprünglichen Daten enthält und das Testdataset 30% der ursprünglichen Daten.

## <a name="test-evaluate-and-compare"></a>Testen, Evaluieren und Vergleichen

 Wir teilen das Dataset auf und verwenden unterschiedliche Datasets zum Trainieren und Testen des Modells, um die Auswertung des Modells objektiver zu gestalten.

Nachdem das Modell trainiert wurde, verwenden wir die Module **Score Model** (Modell bewerten) und **Evaluate Model** (Modell auswerten), um die vorhergesagten Ergebnisse zu generieren und die Modelle auszuwerten.

**Score Model** generiert Vorhersagen für das Testdataset mithilfe des trainierten Modells. Um das Ergebnis zu überprüfen, wählen Sie den Ausgabeport des **Score Model**-Moduls und dann **Visualize** (Visualisieren) aus.

![Bewerten des Ergebnisses](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Wir übergeben die Bewertungen dann an das **Evaluate Model**-Modul, um Auswertungsmetriken zu generieren. Um das Ergebnis zu überprüfen, wählen Sie den Ausgabeport des **Evaluate Model**-Moduls und dann **Visualize** (Visualisieren) aus.

![Auswerten des Ergebnisses](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Untersuchen Sie die anderen Beispiele, die für die grafische Benutzeroberfläche zur Verfügung stehen:

- [Beispiel 2 – Regression: Vergleichen von Algorithmen für die Vorhersage von Autopreisen](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Beispiel 3 – Klassifizierung: Vorhersagen des Kreditrisikos](ui-sample-classification-predict-credit-risk-basic.md)
- [Beispiel 4 – Klassifizierung: Vorhersagen des Kreditrisikos (kostensensibel)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Beispiel 5 – Klassifizierung: Vorhersage der Kundenabwanderung](ui-sample-classification-predict-churn.md)