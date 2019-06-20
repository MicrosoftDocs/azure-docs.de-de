---
title: 'Tutorial: Bereitstellen eines Clusteringmodells in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In Teil 3 dieser dreiteiligen Tutorialreihe stellen Sie mithilfe der Machine Learning Services für Azure SQL-Datenbank (Vorschau) ein Clusteringmodell in R bereit.
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
ms.date: 05/17/2019
ms.openlocfilehash: 1fe9df6378884ba55cb1017da87522ae66edaff0
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420218"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Bereitstellen eines Clusteringmodells in R mit Machine Learning Services für Azure SQL-Datenbank (Vorschau)

In Teil 3 dieser dreiteiligen Tutorialreihe stellen Sie mithilfe der Machine Learning Services für Azure SQL-Datenbank (Vorschau) ein Clusteringmodell in R bereit.

Sie erstellen eine gespeicherte Prozedur mit einem eingebetteten R-Skript, das ein Clustering durchführt. Da Ihr Modell in der Azure SQL-Datenbank ausgeführt wird, kann es auf einfache Weise anhand von Daten trainiert werden, die in der Datenbank gespeichert sind.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer gespeicherten Prozedur, die das Modell generiert
> * Durchführen von Clustering in SQL-Datenbank
> * Verwenden der Clusteringinformationen

Im [ersten Teil](sql-database-tutorial-clustering-model-prepare-data.md) haben Sie gelernt, wie Sie die Daten aus einer Azure SQL-Datenbank für das Clustering in R vorbereiten.

In [Teil 2](sql-database-tutorial-clustering-model-build.md) haben Sie gelernt, wie ein K-Means-Modell für die Durchführung von Clustering erstellt wird.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* In Teil drei dieser Reihe von Tutorials wird angenommen, dass Sie [**Teil eins**](sql-database-tutorial-clustering-model-prepare-data.md) und [**Teil zwei**](sql-database-tutorial-clustering-model-build.md) abgeschlossen haben.

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Erstellen einer gespeicherten Prozedur, die das Modell generiert

Führen Sie das folgende T-SQL-Skript aus, um die gespeicherte Prozedur zu erstellen. Die Prozedur führt die Schritte durch, die in Teil 1 und 2 dieser Tutorialreihe entwickelt wurden:

* Klassifizieren von Kunden basierend auf ihren bisherigen Käufen und Rücksendungen
* Generieren von vier Kundenclustern mithilfe eines K-Means-Algorithmus

Die Prozedur speichert die daraus resultierenden Kundenclusterzuordnungen in der Datenbanktabelle **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
            WHEN (returns_count IS NULL)
                THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Durchführen von Clustering in SQL-Datenbank

Nachdem Sie die gespeicherte Prozedur erstellt haben, führen Sie das folgende Skript aus, um Clustering durchzuführen.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Stellen Sie sicher, dass die Ausführung erfolgreich war und die Liste der Kunden und deren Clusterzuordnungen vorliegt.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>Verwenden der Clusteringinformationen

Da Sie die Clusteringprozedur in der Datenbank gespeichert haben, kann sie das Clustering effizient anhand der Kundendaten durchführen, die in derselben Datenbank gespeichert sind. Sie können die Prozedur bei jeder Aktualisierung Ihrer Kundendaten ausführen und anschließend die aktualisierten Clusteringinformationen verwenden.

Angenommen, Sie möchten eine Werbe-E-Mail an Kunden in Cluster 3 senden, die Gruppe, die ein aktiveres Rücksendungsverhalten aufweist. (In [Teil 2](sql-database-tutorial-clustering-model-build.md#analyze-the-results) werden die vier Cluster beschrieben.) Der folgende Code wählt die E-Mail-Adressen von Kunden in Cluster 3 aus.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

Sie können den **r.cluster**-Wert so ändern, dass E-Mail-Adressen für Kunden in anderen Clustern zurückgegeben werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie dieses Tutorial abgeschlossen haben, können Sie die Datenbank „tpcxbb_1gb“ von Ihrem Azure SQL-Datenbank-Server löschen.

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Wählen Sie im Menü auf der linken Seite im Azure-Portal **Alle Ressourcen** oder **SQL-Datenbanken** aus.
1. Geben Sie im Feld **Nach Name filtern** die Zeichenfolge **tpcxbb_1gb** ein, und wählen Sie Ihr Abonnement aus.
1. Wählen Sie die Datenbank **tpcxbb_1gb** aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Im dritten Teil dieser Tutorialreihe haben Sie die folgenden Schritte ausgeführt:

* Erstellen einer gespeicherten Prozedur, die das Modell generiert
* Durchführen von Clustering in SQL-Datenbank
* Verwenden der Clusteringinformationen

Weitere Informationen zum Verwenden von R in dem Machine Learning Services (Vorschau) von Azure SQL-Datenbank finden Sie unter:

* [Tutorial: Vorbereiten von Daten zum Trainieren eines Vorhersagemodells in R mit Machine Learning Services (Vorschauversion) für Azure SQL-Datenbank](sql-database-tutorial-predictive-model-prepare-data.md)
* [Schreiben erweiterter R-Funktionen in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-functions.md)
* [Arbeiten mit R- und SQL-Daten in Azure SQL-Datenbank mit Machine Learning Services (Vorschauversion)](sql-database-machine-learning-services-data-issues.md)
* [Hinzufügen eines R-Pakets zu Machine Learning Services in Azure SQL-Datenbank (Vorschauversion)](sql-database-machine-learning-services-add-r-packages.md)
