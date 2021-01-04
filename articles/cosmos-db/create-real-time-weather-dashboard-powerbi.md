---
title: Erstellen eines Echtzeitdashboards mit Azure Cosmos DB, Azure Analysis Services und Power BI
description: Erfahren Sie, wie Sie mithilfe von Azure Cosmos DB und Azure Analysis Services ein Livedashboard mit Wetterdaten in Power BI erstellen.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: b3ec3e96aa1ba4bce3893c1af2446bb509a867b6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333595"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Erstellen eines Echtzeitdashboards mit Azure Cosmos DB und Power BI
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel werden die Schritte beschrieben, die zum Erstellen eines Livedashboards mit Wetterdaten in Power BI mithilfe von Azure Cosmos DB und Azure Analysis Services erforderlich sind. Im Power BI-Dashboard werden Diagramme angezeigt, um Echtzeitinformationen zur Temperatur und zu den Niederschlägen in einer Region anzuzeigen.

## <a name="reporting-scenarios"></a>Berichterstellungsszenarien

Es gibt mehrere Möglichkeiten, Berichtdashboards für in Azure Cosmos DB gespeicherte Daten einzurichten. Je nach Veraltungsanforderungen und Datenmenge wird in der folgenden Tabelle das Berichterstellungssetup für die einzelnen Szenarien beschrieben:


|Szenario |Einrichten |
|---------|---------|
|1. Generieren von Ad-hoc-Berichten (keine Aktualisierung)    |  [Power BI-Connector für Azure Cosmos DB mit Importmodus](powerbi-visualize.md)       |
|2. Generieren von Ad-hoc-Berichten mit regelmäßiger Aktualisierung   |  [Power BI-Connector für Azure Cosmos DB mit Importmodus (regelmäßige Aktualisierung gemäß Zeitplan)](powerbi-visualize.md)       |
|3. Berichterstellung für große Datasets (< 10 GB)     |  Power BI-Connector für Azure Cosmos DB mit inkrementeller Aktualisierung       |
|4. Berichterstellung in Echtzeit für große Datasets    |  Power BI-Connector für Azure Analysis Services mit direkter Abfrage + Azure Analysis Services (Azure Cosmos DB-Connector)       |
|5. Berichterstellung für Livedaten mit Aggregaten     |  [Power BI-Connector für Spark mit direkter Abfrage + Azure Databricks + Cosmos DB Spark-Connector](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Berichterstellung für Livedaten mit Aggregaten für große Datasets   |  Power BI-Connector für Azure Analysis Services mit direkter Abfrage + Azure Analysis Services + Azure Databricks + Cosmos DB Spark-Connector       |

Die Szenarien 1 und 2 können problemlos mithilfe des Power BI-Connectors für Azure Cosmos DB eingerichtet werden. In diesem Artikel werden die Setups für die Szenarien 3 und 4 beschrieben.

### <a name="power-bi-with-incremental-refresh"></a>Power BI mit inkrementeller Aktualisierung

Power BI bietet einen Modus, in dem die inkrementelle Aktualisierung konfiguriert werden kann. In diesem Modus ist es nicht notwendig, Azure Analysis Services-Partitionen zu erstellen und zu verwalten. Die inkrementelle Aktualisierung kann so eingerichtet werden, dass nur die neuesten Updates in großen Datasets gefiltert werden. Dieser Modus funktioniert jedoch nur mit dem Power BI Premium-Dienst, der eine Datasetbeschränkung auf 10 GB aufweist.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI-Connector für Azure Analysis + Azure Analysis Services

Azure Analysis Services bietet eine vollständig verwaltete PaaS-Lösung (Platform-as-a-Service), mit der Datenmodelle auf Unternehmensniveau in der Cloud gehostet werden. Umfangreiche Datasets können aus Azure Cosmos DB in Azure Analysis Services geladen werden. Um zu vermeiden, dass ständig das gesamte Dataset abgefragt wird, können die Datasets in Azure Analysis Services-Partitionen unterteilt werden, die unabhängig voneinander in unterschiedlichen Intervallen aktualisiert werden können.

## <a name="power-bi-incremental-refresh"></a>Inkrementelle Aktualisierung in Power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Erfassen von Wetterdaten in Azure Cosmos DB

Richten Sie eine Erfassungspipeline zum Laden von [Wetterdaten](https://catalog.data.gov/dataset/local-weather-archive/resource/c28974a2-fc83-4722-8977-9a701323f729) in Azure Cosmos DB ein. Sie können einen [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md)-Auftrag einrichten, um die neuesten Wetterdaten in regelmäßigen Abständen mithilfe der HTTP-Quelle und der Cosmos DB-Senke in Azure Cosmos DB zu laden.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Verbinden von Power BI mit Azure Cosmos DB

1. **Verbinden des Azure Cosmos-Kontos mit Power BI** : Öffnen Sie Power BI Desktop, und wählen Sie mit dem Azure Cosmos DB-Connector die richtige Datenbank und den richtigen Container aus.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Azure Cosmos DB: Power BI-Connector":::

1. **Konfigurieren der inkrementellen Aktualisierung** : Befolgen Sie die Schritte im Artikel [Inkrementelle Aktualisierung in Power BI Premium](/power-bi/service-premium-incremental-refresh), um die inkrementelle Aktualisierung für das Dataset zu konfigurieren. Fügen Sie die Parameter **RangeStart** und **RangeEnd** wie im folgenden Screenshot gezeigt hinzu:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="Konfigurieren von Bereichsparametern":::

   Da das Dataset eine Datumsspalte in Textform aufweist, müssen die Parameter **RangeStart** und **RangeEnd** so transformiert werden, dass der folgende Filter verwendet wird. Ändern Sie im Bereich **Erweiterter Editor** die Abfrage, und fügen Sie den folgenden Text hinzu, um die Zeilen basierend auf den Parametern „RangeStart“ und „RangeEnd“ zu filtern:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Abhängig davon, welcher Spalten- und welcher Datentyp im Quelldataset vorhanden sind, können Sie die Felder „RangeStart“ und „RangeEnd“ entsprechend ändern.

   
   |Eigenschaft  |Datentyp  |Filtern  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) and [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0)))       |
   |Datum (Beispiel: 2019-08-19)     |   String      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Datum (Beispiel: 2019-08-11 12:00:00)   |  String       |  [Document.date]> DateTime.ToText(RangeStart," yyyy-mm-dd HH:mm:ss") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **Definieren der Aktualisierungsrichtlinie** : Definieren Sie die Aktualisierungsrichtlinie, indem Sie im **Kontextmenü** für die Tabelle zur Registerkarte **Inkrementelle Aktualisierung** navigieren. Legen Sie die Aktualisierungsrichtlinie auf die **tägliche Aktualisierung** fest, und speichern Sie die Daten des letzten Monats.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="Definieren der Aktualisierungsrichtlinie":::

   Ignorieren Sie die Warnung, *dass für die M-Abfrage kein Query Folding bestätigt werden kann*. Der Azure Cosmos DB-Connector führt für Filterabfragen ein Query Folding durch.

1. **Laden der Daten und Generieren von Berichten** : Erstellen Sie mithilfe der zuvor geladenen Daten die Diagramme zur Berichterstellung für Temperatur und Niederschläge.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Laden von Daten und Generieren von Berichten":::

1. **Veröffentlichen des Berichts in Power BI Premium** : Da die inkrementelle Aktualisierung nur in der Premium-Version verfügbar ist, ermöglicht das Dialogfeld „Veröffentlichen“ nur die Auswahl eines Arbeitsbereichs der Premium-Kapazität. Die erste Aktualisierung kann beim Importieren der Verlaufsdaten längere Zeit in Anspruch nehmen. Nachfolgende Datenaktualisierungen laufen wesentlich schneller ab, weil sie die inkrementelle Aktualisierung verwenden.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI-Connector für Azure Analysis + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Erfassen von Wetterdaten in Azure Cosmos DB 

Richten Sie eine Erfassungspipeline zum Laden von [Wetterdaten](https://catalog.data.gov/dataset/local-weather-archive/resource/c28974a2-fc83-4722-8977-9a701323f729) in Azure Cosmos DB ein. Sie können einen Azure Data Factory (ADF)-Auftrag einrichten, um die neuesten Wetterdaten in regelmäßigen Abständen mithilfe der HTTP-Quelle und der Cosmos DB-Senke in Azure Cosmos DB zu laden.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Verbinden von Azure Analysis Services mit dem Azure Cosmos-Konto

1. **Erstellen eines neuen Azure Analysis Services-Clusters** - [Erstellen Sie eine Instanz von Azure Analysis Services](../analysis-services/analysis-services-create-server.md) in derselben Region, in der sich das Azure Cosmos-Konto und der Databricks-Cluster befinden.

1. **Erstellen eines neuen Analysis Services-Tabellenprojekts in Visual Studio** -  [Installieren Sie SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017&preserve-view=true), und erstellen Sie in Visual Studio ein Analysis Services-Tabellenprojekt.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Erstellen eines Azure Analysis Services-Projekts":::

   Wählen Sie die Instanz **Integrierter Arbeitsbereich** aus, und legen Sie den Kompatibilitätsgrad auf **SQL Server 2017/Azure Analysis Services (1400)** fest.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Azure Analysis Services-Designer für tabellarische Modelle":::

1. **Hinzufügen der Azure Cosmos DB-Datenquelle** : Navigieren Sie zu **Modelle**> **Datenquellen** > **Neue Datenquelle** , und fügen Sie die Azure Cosmos DB-Datenquelle hinzu, wie im folgenden Screenshot gezeigt:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Hinzufügen einer Cosmos DB-Datenquelle":::

   Stellen Sie eine Verbindung mit Azure Cosmos DB her, indem Sie den **Konto-URI** , den **Datenbanknamen** und den **Containernamen** angeben. Jetzt sehen Sie, dass die Daten aus dem Azure Cosmos-Container in Power BI importiert werden.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Anzeigen einer Vorschau der Azure Cosmos DB-Daten":::

1. **Erstellen des Analysis Services-Modells** : Öffnen Sie den Abfrage-Editor, und führen Sie die erforderlichen Vorgänge aus, um das geladene Dataset zu optimieren:

   * Extrahieren Sie nur die wetterbezogenen Spalten (Temperatur und Niederschläge).

   * Extrahieren Sie die Monatsinformationen aus der Tabelle. Diese Daten sind nützlich zum Erstellen von Partitionen, wie im nächsten Abschnitt beschrieben.

   * Konvertieren Sie die Temperaturspalten in eine Zahl.

   Daraus ergibt sich folgender M-Ausdruck:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Ändern Sie außerdem den Datentyp der Temperaturspalten in „Dezimal“, um sicherzustellen, dass diese Werte in Power BI gezeichnet werden können.

1. **Erstellen von Azure Analysis Services-Partitionen** : Erstellen Sie Partitionen in Azure Analysis Services, um das Dataset in logische Partitionen aufzuteilen, die unabhängig voneinander und in unterschiedlichen Abständen aktualisiert werden können. In diesem Beispiel erstellen Sie zwei Partitionen, durch die das Dataset in die Daten des aktuellen Monats und die aller übrigen Monate aufgeteilt werden.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Erstellen von Analysis Services-Partitionen":::

   Erstellen Sie die folgenden beiden Partitionen in Azure Analysis Services:

   * **Aktueller Monat** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Historisch** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Bereitstellen des Modells auf dem Azure-Analysis-Server** : Klicken Sie mit der rechten Maustaste auf das Azure Analysis Services-Projekt, und wählen Sie **Bereitstellen** aus. Fügen Sie den Servernamen im Bereich mit den **Eigenschaften des Bereitstellungsservers** hinzu.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Bereitstellen des Azure Analysis Services-Modells":::

1. **Konfigurieren von Partitionsaktualisierungen und -zusammenführungen** : Azure Analysis Services ermöglicht die unabhängige Verarbeitung von Partitionen. Da die Partition **Aktueller Monat** ständig mit den neuesten Daten aktualisiert werden soll, legen Sie das Aktualisierungsintervall auf 5 Minuten fest. Sie können die Daten mithilfe der [REST-API](../analysis-services/analysis-services-async-refresh.md), [Azure Automation](../analysis-services/analysis-services-refresh-azure-automation.md) oder mit einer [Logik-App](../analysis-services/analysis-services-refresh-logic-app.md) aktualisieren. Es ist nicht erforderlich, die Daten in der historischen Partition zu aktualisieren. Darüber hinaus müssen Sie Code schreiben, um die Partition des aktuellen Monats mit der historischen Partition zu konsolidieren und eine neue Partition für den aktuellen Monat zu erstellen.

## <a name="connect-power-bi-to-analysis-services"></a>Verbinden von Power BI mit Analysis Services

1. **Herstellen einer Verbindung mit dem Azure-Analysis-Server über den Azure Analysis Services-Datenbankconnector** : Wählen Sie den **Livemodus** aus, und stellen Sie eine Verbindung mit der Azure Analysis Services-Instanz her, wie im folgenden Screenshot gezeigt:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Abrufen von Daten aus Azure Analysis Services":::

1. **Laden der Daten und Generieren von Berichten** : Erstellen Sie mithilfe der zuvor geladenen Daten die Diagramme zur Berichterstellung für Temperatur und Niederschläge. Da Sie eine Liveverbindung erstellen, müssen die Abfragen für die Daten in dem Azure Analysis Services-Modell ausgeführt werden, das Sie im vorherigen Schritt bereitgestellt haben. Die Temperaturdiagramme werden innerhalb von fünf Minuten aktualisiert, nachdem die neuen Daten in Azure Cosmos DB geladen wurden.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Laden der Daten und Generieren von Berichten":::

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Power BI finden Sie unter [Erste Schritte mit Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Verbinden von Qlik Sense mit Azure Cosmos DB und Visualisieren Ihrer Daten](visualize-qlik-sense.md)
