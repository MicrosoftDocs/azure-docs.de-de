---
title: 'Klassifizierung: Vorhersage von Kundenabwanderung, Kauflust und Up-Selling '
titleSuffix: Azure Machine Learning service
description: Dieses Beispielexperiment auf der grafischen Benutzeroberfläche zeigt eine auf einem binären Klassifizierer basierende Vorhersage der Kundenabwanderung, eine übliche Aufgabe beim Customer Relationship Management (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 42724f5fcb3101015cef0d218a3d548f349646be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65785821"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Beispiel 5 – Klassifizierung: Vorhersage von Kundenabwanderung, Kauflust und Up-Selling 

Erfahren Sie, wie Sie über die grafische Benutzeroberfläche ein komplexes Experiment für maschinelles Lernen erstellen, ohne eine einzige Codezeile zu schreiben.

In diesem Experiment werden drei Klassifikationen des Typs **Um zwei Klassen verstärkte Entscheidungsstruktur** trainiert, um allgemeine Aufgaben für CRM-Systeme (Customer-Relationship-Management) vorherzusagen: Kundenabwanderung, Kauflust und Up-Selling. Die Datenwerte und Bezeichnungen sind über mehrere Datenquellen verteilt und verschlüsselt, um Kundeninformationen zu anonymisieren. Trotzdem kann die grafische Benutzeroberfläche weiterhin verwendet werden, um Datasets zu kombinieren und ein Modell mit den verschlüsselten Werten zu trainieren.

Weil wir versuchen, die Frage „Welche?“ zu beantworten, wird dies als Klassifikationsproblem bezeichnet. Sie können jedoch die gleichen Schritte aus diesem Experiment anwenden, um jede Art von Problem für maschinelles Lernen zu bewältigen, sei es Regression, Klassifizierung, Clustering usw.

Das vollständige Diagramm für dieses Experiment sieht wie folgt aus:

![Diagramm des Experiments](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Klicken Sie für das Experiment „Beispiel 5“ auf die Schaltfläche **Öffnen**.

    ![Öffnen des Experiments](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Daten

Die Daten, die wir für dieses Experiment verwenden, stammen vom KDD Cup 2009. Das Dataset enthält 50.000 Zeilen und 230 Featurespalten. Die Aufgabe ist die Vorhersage der Kundenabwanderung, Kauflust und des Up-Sellings für Kunden, die diese Features nutzen. Weitere Informationen zu den Daten und zur Aufgabe finden Sie auf der [KDD-Website](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Experimentzusammenfassung

Dieses Beispielexperiment in der grafischen Benutzeroberfläche zeigt eine auf einem binären Klassifizierer basierende Vorhersage der Kundenabwanderung, Kauflust und des Up-Sellings, eine übliche Aufgabe beim Customer Relationship Management (CRM).

Zunächst führen wir eine einfache Datenverarbeitung durch.

- Im rohen Dataset fehlen zahlreiche Werte. Wir verwenden das Modul **Clean Missing Data** (Bereinigen fehlender Daten), um die fehlenden Werte durch 0 zu ersetzen.

    ![Bereinigen des Datasets](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Die Features und die zugehörigen Bezeichnungen für Kundenabwanderung, Kauflust und Up-Selling befinden sich in verschiedenen Datasets. Wir verwenden das Modul **Add Columns** (Hinzufügen von Spalten), um die Bezeichnungsspalten an die Featurespalten anzufügen. Die erste Spalte, **Col1**, ist die Bezeichnungsspalte. Die übrigen Spalten, **Var1**, **Var2** usw., sind die Featurespalten.

    ![Hinzufügen des Spaltendatasets](./media/ui-sample-classification-predict-churn/added-column1.png)

- Wir verwenden das Modul **Split Data** (Aufteilen von Daten) zum Aufteilen des Datasets in Trainings- und Testdatasets.

    Dann verwenden wir die binäre Klassifizierung „Boosted Decision Tree“ (Verstärkter Entscheidungsbaum) mit den Standardparametern, um die Vorhersagemodelle zu erstellen. Wir erstellen ein Modell pro Aufgabe, d.h. jeweils ein Modell zur Vorhersage von Up-Selling, Kauflust und Kundenabwanderung.

## <a name="results"></a>Ergebnisse

Visualisieren Sie die Ausgabe des Moduls **Evaluate Model** (Auswerten des Modells), um die Leistung des Modells für das Testdataset zu ermitteln. Für die Up-Selling-Aufgabe zeigt die ROC-Kurve, dass das Modell besser abschneidet als ein Zufallsmodell. Die Fläche unter der Kurve (AUC) ist 0,857. Beim Schwellenwert 0,5 betragen die Genauigkeit 0,7, die Trefferquote 0,463 und das F1-Maß 0,545.

![Auswertung der Ergebnisse](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Sie können den Schieberegler **Threshold** (Schwellenwert) verschieben und prüfen, wie sich die Metriken für die binäre Klassifizierungsaufgabe ändern.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Untersuchen Sie die anderen Beispiele, die für die grafische Benutzeroberfläche zur Verfügung stehen:

- [Beispiel 1 – Regression: Vorhersagen des Preises eines Autos](ui-sample-regression-predict-automobile-price-basic.md)
- [Beispiel 2 – Regression: Vergleichen von Algorithmen für die Vorhersage von Autopreisen](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Beispiel 3 – Klassifizierung: Vorhersagen des Kreditrisikos](ui-sample-classification-predict-credit-risk-basic.md)
- [Beispiel 4 – Klassifizierung: Vorhersagen des Kreditrisikos (kostensensibel)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
