---
title: 'Tutorial: Erstellen eines Clusteringmodells in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Im zweiten Teil dieser dreiteiligen Tutorialreihe erstellen Sie ein K-Means-Modell für das Clustering in R mit Machine Learning Services (Vorschau) in Azure SQL-Datenbank.
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
ms.openlocfilehash: 12738b63be92420c5f3afea6c133522cbd97f849
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420228"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Erstellen eines Clusteringmodells in R mit Machine Learning Services (Vorschauversion) in Azure SQL-Datenbank

Im zweiten Teil dieser dreiteiligen Tutorialreihe erstellen Sie ein K-Means-Modell für das Clustering in R mit Machine Learning Services (Vorschau) in Azure SQL-Datenbank.

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Definieren der Clusteranzahl für einen K-Means-Algorithmus
> * Ausführen des Clusterings
> * Analysieren der Ergebnisse

Im [ersten Teil](sql-database-tutorial-clustering-model-prepare-data.md) haben Sie gelernt, wie Sie die Daten aus einer Azure SQL-Datenbank für das Clustering in R vorbereiten.

Im [dritten Teil](sql-database-tutorial-clustering-model-deploy.md) erfahren Sie, wie Sie eine gespeicherte Prozedur in einer Azure SQL-Datenbank erstellen, die Clustering auf der Grundlage neuer Daten ausführen kann.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* In Teil zwei dieser Reihe von Tutorials wird angenommen, dass Sie [**Teil eins**](sql-database-tutorial-clustering-model-prepare-data.md) und seine Voraussetzungen abgeschlossen haben.

## <a name="define-the-number-of-clusters"></a>Definieren der Clusteranzahl

Für das Clustering Ihrer Kundendaten verwenden Sie den Clusteringalgorithmus **K-Means**. Hierbei handelt es sich um eine der einfachsten und bekanntesten Methoden zur Gruppierung von Daten.
Weitere Informationen zum Clusteringalgorithmus „K-Means“ finden Sie in [diesem umfassenden Leitfaden](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Der Algorithmus akzeptiert zwei Eingaben: die Daten selbst und eine vordefinierte Zahl (*k*), die die Anzahl der zu generierenden Cluster darstellt.
Ausgegeben werden *k* Cluster mit auf die Cluster verteilten Eingabedaten.

Verwenden Sie zur Bestimmung der Clusteranzahl für den Algorithmus einen Plot der Summe der Quadrate innerhalb der Gruppen (nach Anzahl der extrahierten Cluster). Die zu verwendende Clusteranzahl befindet sich an der Krümmung (Scheitelpunkt) des Plots.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Scheitelpunktdiagramm](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Ausgehend von dem Diagramm wäre *k = 4* ein guter Wert. Durch diesen Wert für *k* werden die Kunden in vier Clustern gruppiert.

## <a name="perform-clustering"></a>Ausführen des Clusterings

Im folgenden R-Skript wird die Funktion **rxKmeans** verwendet. Hierbei handelt es sich um die K-Means-Funktion im RevoScaleR-Paket.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Analysieren der Ergebnisse

Nach Abschluss des K-Means-Clusterings muss das Ergebnis analysiert werden, um zu ermitteln, ob verwertbare Informationen vorliegen.

Das Objekt **clust** enthält die Ergebnisse des K-Means-Clusterings.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Die vier Clustermittelwerte werden mithilfe der Variablen angegeben, die im [ersten Teil](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers) definiert wurden:

* *orderRatio*: Retourenverhältnis (Gesamtanzahl teilweise oder vollständig retournierter Bestellungen im Vergleich zur Gesamtanzahl von Bestellungen)
* *itemsRatio* Verhältnis retournierter Artikel (Gesamtanzahl retournierter Artikel im Vergleich zur Anzahl gekaufter Artikel)
* *monetaryRatio*: Retourenbetragsverhältnis (Gesamtbetrag der retournierten Artikel im Vergleich zum Einkaufsbetrag)
* *frequency*: Retourenhäufigkeit

Beim Data Mining mit K-Means müssen die Ergebnisse zwar häufig weiter analysiert werden, und es sind weitere Schritte zum besseren Verständnis der einzelnen Cluster erforderlich, gleichzeitig können aber auch einige gute Leads entstehen.
Im Anschluss folgen einige Interpretationsmöglichkeiten für diese Ergebnisse:

* Beim ersten Cluster (dem größten Cluster) scheint es sich um eine Gruppe von inaktiven Kunden zu handeln. (Alle Werte sind null.)
* Beim dritten Cluster scheint es sich um eine Gruppe mit außergewöhnlichem Retourenverhalten zu handeln.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

***Wenn Sie dieses Tutorial nicht fortsetzen möchten***, löschen Sie die Datenbank „tpcxbb_1gb“ von Ihrem Azure SQL-Datenbank-Server.

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Wählen Sie im Menü auf der linken Seite im Azure-Portal **Alle Ressourcen** oder **SQL-Datenbanken** aus.
1. Geben Sie im Feld **Nach Name filtern...** die Zeichenfolge **tpcxbb_1gb** ein, und wählen Sie Ihr Abonnement aus.
1. Wählen Sie die Datenbank **tpcxbb_1gb** aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Im zweiten Teil dieser Tutorialreihe haben Sie die folgenden Schritte ausgeführt:

* Definieren der Clusteranzahl für einen K-Means-Algorithmus
* Ausführen des Clusterings
* Analysieren der Ergebnisse

Um das Machine Learning-Modell bereitzustellen, das Sie erstellt haben, fahren Sie mit Teil drei dieser Tutorialreihe fort:

> [!div class="nextstepaction"]
> [Tutorial: Deploy a clustering model in R with Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-clustering-model-deploy.md) (Tutorial: Bereitstellen eines Clusteringmodells in R mit Machine Learning Services (Vorschauversion) in Azure SQL-Datenbank)