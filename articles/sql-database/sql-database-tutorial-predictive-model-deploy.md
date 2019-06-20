---
title: 'Tutorial: Bereitstellen eines Vorhersagemodells in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In Teil drei dieses dreiteiligen Tutorials stellen Sie mithilfe der Machine Learning Services (Vorschau) von Azure SQL-Datenbank ein Vorhersagemodell in R bereit.
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
ms.openlocfilehash: 17b68f71f4034e5eb637d40b975cc22d94438fb7
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978708"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Bereitstellen eines Vorhersagemodells in R mit Machine Learning Services (Vorschauversion) für Azure SQL-Datenbank

In Teil drei dieses dreiteiligen Tutorials stellen Sie mithilfe der Machine Learning Services (Vorschau) von Azure SQL-Datenbank ein Vorhersagemodell in R bereit.

Sie erstellen eine gespeicherte Prozedur mit einem eingebetteten R-Skript, das mithilfe des Modells Vorhersagen macht. Da Ihr Modell in der Azure SQL-Datenbank ausgeführt wird, kann es auf einfache Weise anhand von Daten trainiert werden, die in der Datenbank gespeichert sind.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Speichern des Vorhersagemodells in einer Datenbanktabelle
> * Erstellen einer gespeicherten Prozedur, die das Modell generiert
> * Erstellen einer gespeicherten Prozedur, die mithilfe des Modells Vorhersagen trifft
> * Ausführen des Modells mit neuen Daten

In [Teil eins](sql-database-tutorial-predictive-model-prepare-data.md) haben Sie gelernt, eine Beispieldatenbank in Azure SQL-Datenbank zu importieren und anschließend die Daten vorzubereiten, die zum Trainieren eines Vorhersagemodells in R verwendet werden sollen.

In [Teil zwei](sql-database-tutorial-predictive-model-build-compare.md) haben Sie erfahren, wie Sie mehrere Modelle erstellen und trainieren und dann das genaueste auswählen.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* In Teil drei dieser Reihe von Tutorials wird angenommen, dass Sie [**Teil eins**](sql-database-tutorial-predictive-model-prepare-data.md) und [**Teil zwei**](sql-database-tutorial-predictive-model-build-compare.md) abgeschlossen haben.

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Erstellen einer gespeicherten Prozedur, die das Modell generiert

In Teil zwei dieser Tutorialreihe haben Sie entschieden, dass ein Entscheidungsstrukturmodell (dtree) die genauesten Ergebnisse liefert. Erstellen Sie jetzt eine gespeicherte Prozedur (`generate_rental_rx_model`), die das dtree-Modell mithilfe von rxDTree aus dem RevoScaleR-Paket trainiert und generiert.

Führen Sie in Azure Data Studio oder SSMS die folgenden Befehle aus.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Speichern des Modells in einer Datenbanktabelle

Erstellen Sie eine Tabelle in der TutorialDB-Datenbank, und speichern Sie dann das Modell in der Tabelle.

1. Erstellen Sie eine Tabelle (`rental_rx_models`) zum Speichern des Modells.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Speichern Sie das Modell als binäres Objekt in der Tabelle unter dem Modellnamen „rxDTree“.

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Erstellen einer gespeicherten Prozedur, die Vorhersagen trifft

Erstellen Sie eine gespeicherte Prozedur (`predict_rentalcount_new`), die mithilfe des trainierten Modells und eines Satzes neuer Daten Vorhersagen macht.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Ausführen des Modells mit neuen Daten

Jetzt können Sie die gespeicherte Prozedur `predict_rentalcount_new` verwenden, um die Anzahl der Vermietungen aus neuen Daten vorherzusagen.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Das Ergebnis sollte in etwa wie hier dargestellt aussehen.

```results
RentalCount_Predicted
332.571428571429
```

Sie haben erfolgreich ein Modell in Azure SQL-Datenbank erstellt, trainiert und bereitgestellt. Sie haben das Modell anschließend in einer gespeicherten Prozedur verwendet, um Werte auf der Grundlage neuer Daten vorherzusagen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Verwendung der TutorialDB-Datenbank abgeschlossen haben, löschen Sie sie von Ihrem Azure SQL-Datenbankserver.

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Wählen Sie im Menü auf der linken Seite im Azure-Portal **Alle Ressourcen** oder **SQL-Datenbanken** aus.
1. Geben Sie im Feld **Nach Name filtern...** die **TutorialDB** ein, und wählen Sie Ihr Abonnement aus.
1. Wählen Sie Ihre TutorialDB-Datenbank aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Im dritten Teil dieser Tutorialreihe haben Sie die folgenden Schritte ausgeführt:

* Speichern des Vorhersagemodells in einer Datenbanktabelle
* Erstellen einer gespeicherten Prozedur, die das Modell generiert
* Erstellen einer gespeicherten Prozedur, die mithilfe des Modells Vorhersagen trifft
* Ausführen des Modells mit neuen Daten

Weitere Informationen zum Verwenden von R in dem Machine Learning Services (Vorschau) von Azure SQL-Datenbank finden Sie unter:

* [Schreiben erweiterter R-Funktionen in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-functions.md)
* [Arbeiten mit R- und SQL-Daten in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-data-issues.md)
* [Hinzufügen eines R-Pakets zu Machine Learning Services in Azure SQL-Datenbank (Vorschauversion)](sql-database-machine-learning-services-add-r-packages.md)
