---
title: 'Schnellstart: Massenladen von Daten mit einer einzelnen T-SQL-Anweisung'
description: Massenladen von Daten mit der COPY-Anweisung
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 83b5804888379316b855c36f803f646cec102d9e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024584"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Schnellstart: Massenladen von Daten mit der COPY-Anweisung

In dieser Schnellstartanleitung verwenden Sie das Massenladen von Daten in Ihren dedizierten SQL-Pool mithilfe der einfachen und flexiblen [COPY-Anweisung](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) für die Datenerfassung mit hohem Durchsatz. Die COPY-Anweisung ist das empfohlene Ladehilfsprogramm, da sie Ihnen mit folgender Funktionalität das nahtlose und flexible Laden von Daten ermöglicht:

- Benutzer mit niedrigeren Berechtigungen können ohne strikte Berechtigungen zur STEUERUNG im Data Warehouse laden
- Nutzen einer einzelnen T-SQL-Anweisung, ohne dass zusätzliche Datenbankobjekte erstellt werden müssen
- Nutzen eines feineren Berechtigungsmodells, ohne Speicherkontoschlüssel über SAS (Share Access Signature) verfügbar zu machen
- Angeben eines anderen Speicherkontos für den ERRORFILE-Speicherort (REJECTED_ROW_LOCATION)
- Anpassen der Standardwerte für jede Zielspalte und Angeben der Quelldatenfelder, die in bestimmte Zielspalten geladen werden sollen
- Angeben eines benutzerdefinierten Zeilenabschlusszeichens für CSV-Dateien
- Escapezeichenfolge, Feld und Zeilentrennzeichen für CSV-Dateien
- Nutzen von SQL Server-Datumsformaten für CSV-Dateien
- Angeben von Platzhaltern und mehreren Dateien im Speicherortpfad

## <a name="prerequisites"></a>Voraussetzungen

In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie bereits über einen dedizierten SQL-Pool verfügen. Wenn kein dedizierter SQL-Pool erstellt wurde, verwenden Sie den [Schnellstart: Erstellen und Abfragen eines Synapse-SQL-Pools mit dem Azure-Portal](create-data-warehouse-portal.md).

## <a name="set-up-the-required-permissions"></a>Einrichten der erforderlichen Berechtigungen

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>Erstellen der Zieltabelle

In diesem Beispiel laden wir Daten aus dem New York-Taxi-Dataset. Wir laden eine Tabelle mit dem Namen „Trip“, die Taxifahrten darstellt, die innerhalb eines einzelnen Jahres durchgeführt wurden. Führen Sie zum Erstellen der Tabelle Folgendes aus:

```sql
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
```

## <a name="run-the-copy-statement"></a>Ausführen der COPY-Anweisung

Führen Sie die folgende Copy-Anweisung aus, mit der Daten aus dem Azure-Blobspeicherkonto in die Trip-Tabelle geladen werden.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Überwachen des Ladens

Überprüfen Sie, ob Ihr Ladevorgang Fortschritte macht, indem Sie die folgende Abfrage regelmäßig ausführen:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Nächste Schritte

- Bewährte Methoden zum Laden von Daten finden Sie unter [Bewährte Methoden für das Laden von Daten mit einem Synapse SQL-Pool](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data).
- Informationen zum Verwalten der Ressourcen für Ihre Datenladevorgänge finden Sie unter [Schnellstart: Konfigurieren der Workloadisolation mithilfe von T-SQL](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
