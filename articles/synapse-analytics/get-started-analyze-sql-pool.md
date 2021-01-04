---
title: 'Tutorial: Erste Schritte beim Analysieren von Daten mit dedizierten SQL-Pools'
description: In diesem Tutorial verwenden Sie die NYC Taxi-Beispieldaten, um die Analysefunktionen des SQL-Pools zu erforschen.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: 9014469ca063ca52be0965ecbd4e8b21709d10a0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455169"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Analysieren von Daten mit dedizierten SQL-Pools

Azure Synapse Analytics ermöglicht das Analysieren von Daten mit einem dedizierten SQL-Pool. In diesem Tutorial werden die NYC Taxi-Daten verwendet, um die Analysefunktionen eines dedizierten SQL-Pools zu untersuchen.

## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Laden der NYC Taxi-Daten in SQLPOOL1

1. Navigieren Sie in Synapse Studio zum Hub **Entwickeln**, und erstellen Sie ein neues SQL-Skript.
1. Wählen Sie den Pool „SQLPOOL1“ (der Pool wurde in [SCHRITT 1](https://docs.microsoft.com/azure/synapse-analytics/get-started-create-workspace#create-a-sql-pool) dieses Tutorials erstellt) im Abschnitt „Verbinden mit“ des Skripts aus.
1. Geben Sie den folgenden Code ein:
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. Dieses Skript wird in ungefähr 60 Sekunden abgeschlossen. Es lädt zwei Millionen Zeilen NYC Taxi-Daten in eine Tabelle mit dem Namen **dbo.Trip**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>Untersuchen der NYC Taxi-Daten im dedizierten SQL-Pool

1. Navigieren Sie in Synapse Studio zum Hub **Daten**.
1. Navigieren Sie zu **SQLPOOL1** > **Tabellen**. Es werden mehrere geladene Tabellen angezeigt.
1. Klicken Sie mit der rechten Maustaste auf die Tabelle **dbo.Trip**, und wählen Sie **Neues SQL-Skript** > **OBERSTE 100 Zeilen auswählen** aus.
1. Warten Sie, während ein neues SQL-Skript erstellt und ausgeführt wird.
1. Beachten Sie, dass am oberen Rand des SQL-Skripts **Verbinden mit** automatisch auf den SQL-Pool mit dem Namen **SQLPOOL1** festgelegt ist.
1. Ersetzen Sie den Text des SQL-Skripts durch diesen Code, und führen Sie ihn aus.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Diese Abfrage zeigt, wie die Gesamtzahl der Fahrtstrecken und die durchschnittliche Fahrtstrecke mit der Anzahl der Fahrgäste in Beziehung stehen.
1. Ändern Sie im Ergebnisfenster des SQL-Skripts die **Ansicht** in **Diagramm**, um eine Visualisierung der Ergebnisse als Liniendiagramm anzuzeigen.
    
    > [!NOTE]
    > Ein dedizierter SQL-Pool (ehemals SQL DW) mit Arbeitsbereichsunterstützung kann über die QuickInfo im Datenhub identifiziert werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Analysieren mithilfe von Spark](get-started-analyze-spark.md)

