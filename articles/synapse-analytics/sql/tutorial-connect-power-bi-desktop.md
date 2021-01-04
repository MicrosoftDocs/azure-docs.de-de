---
title: 'Tutorial: Herstellen einer Verbindung zwischen einem serverlosen SQL-Pool und Power BI Desktop und Erstellen eines Berichts'
description: In diesem Tutorial erfahren Sie, wie Sie einen serverlosen SQL-Pool in Azure Synapse Analytics mit Power BI Desktop verbinden und einen Demobericht auf der Grundlage einer Ansicht erstellen.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6b45bad7c439fb98737f0caee08b2996323fbd3b
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602698"
---
# <a name="tutorial-use-serverless-sql-pool-with-power-bi-desktop--create-a-report"></a>Tutorial: Verwenden eines serverlosen SQL-Pools mit Power BI Desktop und Erstellen eines Berichts

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen einer Demodatenbank
> - Erstellen der für den Bericht verwendeten Ansicht
> - Herstellen einer Verbindung zwischen Power BI Desktop und einem serverlosen SQL-Pool
> - Erstellen eines Berichts anhand einer Ansicht

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

- [Power BI Desktop](https://powerbi.microsoft.com/downloads/) – Zum Visualisieren der Daten und zum Erstellen eines Berichts erforderlich.
- [Azure Synapse-Arbeitsbereich](https://docs.microsoft.com/azure/synapse-analytics/quickstart-synapse-studio) – Zum Erstellen von Datenbank, externer Datenquelle and Ansicht erforderlich.

Optional:

- Ein SQL-Abfragetool wie [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) oder [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)

Werte für die folgenden Parameter:

| Parameter                                 | BESCHREIBUNG                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adresse des Dienstendpunkts des serverlosen SQL-Pools    | Wird als Servername verwendet.                                   |
| Region des Dienstendpunkts des serverlosen SQL-Pools     | Wird verwendet, um den in den Beispielen verwendeten Speicher zu bestimmen. |
| Benutzername und Kennwort für den Endpunktzugriff | Wird für den Zugriff auf den Endpunkt verwendet.                               |
| Datenbank zum Erstellen von Ansichten     | Die Datenbank wird als Ausgangspunkt in den Beispielen verwendet.       |

## <a name="1---create-database"></a>1\. Erstellen der Datenbank

Erstellen Sie für die Demoumgebung eine eigene Demodatenbank. Sie verwenden diese Datenbank zum Anzeigen von Metadaten und nicht zum Speichern tatsächlicher Daten.

Führen Sie das folgende T-SQL-Skript (Transact-SQL) aus, um die Demodatenbank zu erstellen (und ggf. eine vorhandene Datenbank zu löschen):

```sql
-- Drop database if it exists
DROP DATABASE IF EXISTS Demo
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-data-source"></a>2 – Erstellen einer Datenquelle

Eine Datenquelle wird vom Dienst für den serverlosen SQL-Pool für den Zugriff auf Dateien im Speicher benötigt. Erstellen Sie die Datenquelle für ein Speicherkonto, das sich in derselben Region wie Ihr Endpunkt befindet. Der serverlose SQL-Pool kann zwar auf Speicherkonten aus verschiedenen Regionen zugreifen, die Leistung ist jedoch besser, wenn sich Speicher und Endpunkt in der gleichen Region befinden.

Führen Sie zum Erstellen der Datenquelle das folgende T-SQL-Skript (Transact-SQL) aus:

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a secret.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="3---prepare-view"></a>3\. Vorbereiten der Ansicht

Führen Sie das folgende T-SQL-Skript (Transact-SQL) aus, um die Ansicht auf der Grundlage der externen, für Power BI vorgesehenen Demodaten zu erstellen:

Erstellen Sie die Ansicht `usPopulationView` mit der folgenden Abfrage in der Datenbank `Demo`:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS uspv;
```

Die Demodaten enthalten die folgenden Datasets:

US-amerikanische Bevölkerung nach Geschlecht und Ethnie für jeden US-amerikanischen Bezirk, erstellt auf der Grundlage der Erhebungen aus den Jahren 2000 und 2010 im Parquet-Format.

| Ordnerpfad                                                  | BESCHREIBUNG                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | Übergeordneter Ordner für Daten im Demospeicherkonto               |
| /release/us_population_county/                               | Datendateien mit der US-amerikanischen Bevölkerung im Parquet-Format, mithilfe des Hive-/Hadoop-Partitionierungsschemas nach Jahr partitioniert |

## <a name="4---create-power-bi-report"></a>4\. Erstellen eines Power BI-Berichts

Erstellen Sie anhand der folgenden Schritte den Bericht für Power BI Desktop:

1. Öffnen Sie die Power BI Desktop-Anwendung, und wählen Sie **Daten abrufen** aus.

   ![Öffnen der Power BI Desktop-Anwendung und Auswählen von „Daten abrufen“](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Wählen Sie **Azure** > **Azure SQL-Datenbank** aus. 

   ![Wählen Sie die Datenquelle aus.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Geben Sie in das Feld **Server** den Namen des Servers ein, auf dem sich die Datenbank befindet, und geben Sie dann als Datenbanknamen `Demo` ein. Wählen Sie die Option **Importieren** und dann **OK** aus. 

   ![Wählen Sie die Datenbank auf dem Endpunkt aus.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Wählen Sie die bevorzugte Authentifizierungsmethode aus:

    - Beispiel für AAD 
  
        ![Klicken Sie auf „Anmelden“.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Beispiel für SQL-Anmeldung: Geben Sie Ihren Benutzernamen und Ihr Kennwort ein:

        ![Verwenden Sie die SQL-Anmeldung.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Wählen Sie die Ansicht `usPopulationView` und dann **Laden** aus. 

   ![Auswählen einer Ansicht für die ausgewählte Datenbank](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Warten Sie, bis der Vorgang abgeschlossen ist. Anschließend wird ein Popup mit dem Hinweis `There are pending changes in your queries that haven't been applied` angezeigt. Wählen Sie **Änderungen übernehmen** aus. 

   ![Klicken auf „Änderungen übernehmen“](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Warten Sie, bis das Dialogfeld **Abfrageänderungen übernehmen** nicht mehr angezeigt wird. Das kann einige Minuten dauern. 

   ![Warten auf den Abschluss einer Abfrage](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Wählen Sie nach Abschluss des Ladevorgangs die folgenden Spalten in der angegebenen Reihenfolge aus, um den Bericht zu erstellen:
   - countyName
   - Auffüllung
   - stateName

   ![Auswählen der gewünschten Spalten zum Generieren eines Kartenberichts](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diesen Bericht nicht mehr benötigen, löschen Sie die Ressourcen mit den folgenden Schritten:

1. Löschen der Anmeldeinformationen für das Speicherkonto

   ```sql
   DROP EXTERNAL DATA SOURCE AzureOpenData
   ```

2. Löschen der Ansicht

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Löschen der Datenbank

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem Artikel zum [Abfragen von Speicherdateien](develop-storage-files-overview.md) fort, um zu erfahren, wie Sie Speicherdateien mithilfe von Synapse SQL abfragen.
