---
title: 'Tutorial: Trainieren und Vergleichen von Vorhersagemodellen in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In Teil zwei dieser dreiteiligen Tutorialreihe erstellen Sie zwei Vorhersagemodelle in R mit den Machine Learning Services (Vorschau) von Azure SQL-Datenbank und wählen dann das genaueste Modell aus.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: 3d336d6a53b6d234048c56d8492d278bef6fed64
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957511"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Erstellen eines Vorhersagemodells in R mit Machine Learning Services (Vorschauversion) für Azure SQL-Datenbank

In Teil zwei dieser dreiteiligen Tutorialreihe erstellen Sie zwei Vorhersagemodelle in R mit den Machine Learning Services (Vorschau) von Azure SQL-Datenbank und wählen dann das genaueste Modell aus.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Trainieren von zwei Machine Learning-Modellen
> * Erstellen von Vorhersagen aus beiden Modellen
> * Vergleichen der Ergebnisse, um das genaueste Modell zu wählen

In [Teil eins](sql-database-tutorial-predictive-model-prepare-data.md) haben Sie gelernt, eine Beispieldatenbank in Azure SQL-Datenbank zu importieren und anschließend die Daten vorzubereiten, die zum Trainieren eines Vorhersagemodells in R verwendet werden sollen.

In [Teil drei](sql-database-tutorial-predictive-model-deploy.md) lernen Sie, wie Sie das Modell in einer Datenbank speichern und anschließend eine gespeicherte Prozedur erstellen, die auf der Grundlage neuer Daten Vorhersagen treffen kann.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* In Teil zwei dieser Reihe von Tutorials wird angenommen, dass Sie [**Teil eins**](sql-database-tutorial-predictive-model-prepare-data.md) und seine Voraussetzungen abgeschlossen haben.

## <a name="train-two-models"></a>Trainieren von zwei Modellen

Um das beste Modell für die Skiverleihdaten zu finden, erstellen Sie zwei verschiedene Modelle (lineare Regression und Entscheidungsstruktur) und stellen fest, welches genauere Vorhersagen liefert. Sie werden den Datenrahmen `rentaldata` verwenden, den Sie in Teil eins dieser Reihe erstellt haben.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Erstellen von Vorhersagen aus beiden Modellen

Verwenden Sie eine Vorhersagefunktion, um die Vermietungszahlen mithilfe jedes der trainierten Modelle vorherzusagen.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Vergleichen der Ergebnisse

Jetzt möchten Sie bestimmen, welches der Modelle die besten Vorhersagen ergibt. Eine schnelle und bequeme Möglichkeit dazu bietet eine einfache Zeichenfunktion, um den Unterschied zwischen den Ist-Werten in Ihren Trainingsdaten und den vorhergesagten Daten darzustellen.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Vergleich der beiden Modelle](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Offenbar ist das Entscheidungsstruktur-Modell das genauere der beiden Modelle.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie dieses Tutorial nicht fortsetzen möchten, löschen Sie die TutorialDB-Datenbank von Ihrem Azure SQL-Datenbankserver.

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Wählen Sie im Menü auf der linken Seite im Azure-Portal **Alle Ressourcen** oder **SQL-Datenbanken** aus.
1. Geben Sie im Feld **Nach Name filtern...** die **TutorialDB** ein, und wählen Sie Ihr Abonnement aus.
1. Wählen Sie Ihre TutorialDB-Datenbank aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Im zweiten Teil dieser Tutorialreihe haben Sie die folgenden Schritte ausgeführt:

* Trainieren von zwei Machine Learning-Modellen
* Erstellen von Vorhersagen aus beiden Modellen
* Vergleichen der Ergebnisse, um das genaueste Modell zu wählen

Um das Machine Learning-Modell bereitzustellen, das Sie erstellt haben, fahren Sie mit Teil drei dieser Tutorialreihe fort:

> [!div class="nextstepaction"]
> [Tutorial: Bereitstellen eines Vorhersagemodells in R mit Machine Learning Services (Vorschauversion) für Azure SQL-Datenbank](sql-database-tutorial-predictive-model-deploy.md)
