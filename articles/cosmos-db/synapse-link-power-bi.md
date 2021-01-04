---
title: Power BI und serverloser SQL-Pool zum Analysieren von Azure Cosmos DB-Daten mit Synapse Link
description: Hier erfahren Sie, wie Sie eine serverlose SQL-Pooldatenbank und Sichten zu Synapse Link für Azure Cosmos DB erstellen, die Azure Cosmos DB-Container abfragen und dann mit Power BI ein Modell für diese Sichten erstellen.
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: acomet
ms.openlocfilehash: 959070ca431c3397779a2a22c16f03b3adebbb35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444507"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-preview-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>Verwenden von Power BI und serverlosem Synapse SQL-Pool (Vorschau) zum Analysieren von Azure Cosmos DB-Daten mit Synapse Link 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

In diesem Artikel erfahren Sie, wie Sie eine Datenbank im serverlosen SQL-Pool und Sichten zu Synapse Link für Azure Cosmos DB erstellen. Sie werden die Azure Cosmos DB-Container abfragen und dann ein Modell mit Power BI für diese Sichten erstellen, um die betreffende Abfrage anzuzeigen.

In diesem Szenario verwenden Sie simulierte Daten zu Surface-Produktverkäufen in einem Partner-Einzelhandelsgeschäft. Sie werden den Umsatz pro Geschäft – basierend auf der Nähe zu großen Haushalten und der Auswirkung von Werbung – für eine bestimmte Woche analysieren. In diesem Artikel erstellen Sie die zwei Sichten **RetailSales** und **StoreDemographics** sowie eine Abfrage zwischen beiden. Sie können die Beispielproduktdaten aus diesem [GitHub](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData)-Repository abrufen.

> [!IMPORTANT]
> Die Unterstützung von serverlosen SQL-Pools in Synapse für Azure Synapse Link für Azure Cosmos DB befindet sich derzeit in der Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen die folgenden Ressourcen erstellen, bevor Sie beginnen:

* [Erstellen Sie ein Azure Cosmos DB-Konto der Art „SQL (Core)“ oder „MongoDB“.](create-cosmosdb-resources-portal.md)

* Aktivieren von Azure Synapse Link für Ihr [Azure Cosmos-Konto](configure-synapse-link.md#enable-synapse-link)

* Erstellen Sie eine Datenbank im Azure Cosmos-Konto und zwei Container, bei denen der [Analysespeicher aktiviert wurde.](configure-synapse-link.md#create-analytical-ttl)

* Laden Sie die Produktdaten in die Azure Cosmos-Container, wie in diesem Notebook zur [Batchdatenerfassung](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) beschrieben wird.

* [Erstellen Sie den Synapse-Arbeitsbereich](../synapse-analytics/quickstart-create-workspace.md) **SynapseLinkBI**.

* [Stellen Sie eine Verbindung zwischen der Azure Cosmos-Datenbank und dem Synapse-Arbeitsbereich her](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Erstellen einer Datenbank und von Sichten

Wechseln Sie im Synapse-Arbeitsbereich zur Registerkarte **Entwickeln**, wählen Sie das Symbol **+** und dann **SQL-Skript** aus.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Hinzufügen eines SQL-Skripts zum Synapse Analytics-Arbeitsbereich":::

Jeder Arbeitsbereich enthält einen serverlosen SQL-Endpunkt. Nachdem Sie ein SQL-Skript erstellt haben, stellen Sie über die Symbolleiste oben eine Verbindung mit **Integriert** her.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Aktivieren des SQL-Skripts zur Verwendung des serverlosen SQL-Endpunkts im Arbeitsbereich":::

Erstellen Sie die neue Datenbank **RetailCosmosDB** und eine SQL-Sicht für die Container mit Synapse Link-Aktivierung. Der folgende Befehl zeigt, wie eine Datenbank erstellt wird:

```sql
-- Create database
Create database RetailCosmosDB
```

Erstellen Sie als Nächstes mehrere Sichten für verschiedene Azure Cosmos-Container mit Synapse Link-Aktivierung. Sichten ermöglichen es Ihnen, Azure Cosmos DB-Daten in verschiedenen Containern mithilfe von T-SQL zu verknüpfen und abzufragen.  Beim Erstellen der Sichten müssen Sie die Datenbank **RetailCosmosDB** auswählen.

Die folgenden Skripts zeigen, wie Sichten für jeden Container erstellt werden. Der Einfachheit halber verwenden wir das Feature [Automatischer Schemarückschluss](analytical-store-introduction.md#analytical-schema) des serverlosen SQL-Pools in Containern mit Synapse Link-Aktivierung:


### <a name="retailsales-view"></a>Sicht „RetailSales“:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

In das vorherige SQL-Skript müssen Sie Ihre Azure Cosmos DB-Region und den Primärschlüssel einfügen. Alle Zeichen im Regionsnamen sollten Kleinbuchstaben ohne Leerzeichen sein. Im Gegensatz zu den anderen Parametern des Befehls `OPENROWSET` sollte der Parameter für den Containernamen ohne Anführungszeichen angegeben werden.

### <a name="storedemographics-view"></a>Sicht „StoreDemographics“:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Führen Sie jetzt das SQL-Skript durch Auswählen des Befehls **Ausführen** aus.

## <a name="query-the-views"></a>Abfragen der Sichten

Nachdem die beiden Sichten erstellt wurden, definieren Sie die Abfrage jetzt so, dass diese Sichten folgendermaßen verknüpft werden:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Wählen Sie **Ausführen** aus, um die folgende Tabelle als Ergebnis zu erhalten:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Abfrageergebnisse nach dem Verknüpfen der Sichten „StoreDemographics“ und „RetailSales“":::

## <a name="model-views-over-containers-with-power-bi"></a>Modellsichten für Container mit Power BI

Öffnen Sie als Nächstes den Power BI-Desktop, und stellen Sie mit den folgenden Schritten eine Verbindung mit dem serverlosen SQL-Endpunkt her:

1. Öffnen Sie die Power BI Desktop-Anwendung. Wählen Sie **Daten abrufen** und dann **Weitere** aus.

1. Wählen Sie in der Liste mit den Verbindungsoptionen **Azure Synapse Analytics (SQL DW)** aus.

1. Geben Sie den Namen des SQL-Endpunkts ein, auf dem sich die Datenbank befindet. Geben Sie im Feld **Server** `SynapseLinkBI-ondemand.sql.azuresynapse.net` ein. In diesem Beispiel ist **SynapseLinkBI** der Name des Arbeitsbereichs. Ersetzen Sie ihn, wenn Sie für Ihren Arbeitsbereich einen anderen Namen angegeben haben. Wählen Sie für den Datenkonnektivitätsmodus **Direkte Abfrage** und dann **OK** aus.

1. Wählen Sie die bevorzugte Authentifizierungsmethode aus, z. B. „Azure AD“.

1. Wählen Sie die Datenbank **RetailCosmosDB** sowie die Sichten **RetailSales** und **StoreDemographics** aus.

1. Wählen Sie **Laden** aus, um die beiden Sichten in den Direktabfragemodus zu laden.

1. Wählen Sie **Modell** aus, um eine Beziehung zwischen den beiden Sichten über die Spalte **storeId** zu erstellen.

1. Ziehen Sie die Spalte **StoreId** aus der Sicht **RetailSales** zur Spalte **StoreId** in der Sicht **StoreDemographics**.

1. Wählen Sie die Beziehung „Viele zu eins (*:1)“ aus, weil es in der Sicht **RetailSales** mehrere Zeilen mit derselben Speicher-ID gibt. Die Sicht **StoreDemographics** enthält jedoch nur eine Zeile mit der Speicher-ID (es handelt sich um eine Dimensionstabelle).

Navigieren Sie jetzt zum Fenster **Bericht**, und erstellen Sie einen Bericht, um die relative Wichtigkeit der Haushaltsgröße mit dem durchschnittlichen Umsatz pro Geschäft – basierend auf der verstreuten Darstellung von Umsatz und LargeHH-Index – zu vergleichen:

1. Wählen Sie **Punktdiagramm** aus.

1. Verschieben Sie **LargeHH** mit Drag und Drop aus der Sicht **StoreDemographics** in die x-Achse.

1. Verschieben Sie **Umsatz** aus der Sicht **RetailSales** in die y-Achse. Wählen Sie **Durchschnitt** aus, um den durchschnittlichen Umsatz pro Produkt pro Geschäft und pro Woche zu erhalten.

1. Verschieben Sie den **productCode** mit Drag und Drop aus der Sicht **RetailSales** in die Legende, um eine bestimmte Produktlinie auszuwählen.
Nachdem Sie diese Optionen ausgewählt haben, sollte ein Diagramm wie der folgende Screenshot angezeigt werden:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Bericht, der die relative Wichtigkeit der Haushaltsgröße mit dem durchschnittlichen Umsatz pro Geschäft vergleicht":::

## <a name="next-steps"></a>Nächste Schritte

[Verwenden von T-SQL zum Abfragen von Azure Cosmos DB-Daten mithilfe von Azure Synapse Link](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

Verwenden Sie den serverlosen SQL-Pool zum [Analysieren von Azure Open Datasets und Visualisieren der Ergebnisse in Azure Synapse Studio](../synapse-analytics/sql/tutorial-data-analyst.md).
