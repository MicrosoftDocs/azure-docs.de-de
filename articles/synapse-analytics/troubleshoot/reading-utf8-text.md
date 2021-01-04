---
title: 'Problembehandlung: Lesen von UTF-8-Text aus CSV- oder Parquet-Dateien mithilfe eines serverlosen SQL-Pools'
description: Lesen von UTF-8-Text aus CSV- oder Parquet-Dateien mithilfe eines serverlosen SQL-Pools in Azure Synapse Analytics
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576416"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Problembehandlung beim Lesen von UTF-8-Text aus CSV- oder Parquet-Dateien mithilfe eines serverlosen SQL-Pools in Azure Synapse Analytics

In diesem Artikel werden Schritte zur Problembehandlung beim Lesen von UTF-8-Text aus CSV- oder Parquet-Dateien mithilfe eines serverlosen SQL-Pools in Azure Synapse Analytics beschrieben.

Wenn UTF-8-Text mithilfe eines serverlosen SQL-Pools aus einer CSV- oder Parquet-Datei gelesen wird, werden einige Sonderzeichen wie „ü“ und „ö“ fälschlicherweise konvertiert, wenn die Abfrage VARCHAR-Spalten mit Nicht-UTF8-Sortierungen zurückgibt. Dies ist ein bekanntes Problem in SQL Server und Azure SQL. Die Nicht-UTF8-Sortierung ist die Standardeinstellung in Synapse SQL, sodass Kundenabfragen beeinträchtigt werden. Kunden, die englische Standardzeichen und eine Teilmenge von erweiterten lateinischen Zeichen verwenden, bemerken die Konvertierungsfehler möglicherweise nicht. Die falsche Konvertierung wird in [Always use UTF-8 collations to read UTF-8 text in serverless SQL pool](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633) (Immer UTF-8-Sortierungen zum Lesen von UTF-8-Text im serverlosen SQL-Pool verwenden) ausführlich erläutert.

## <a name="workaround"></a>Problemumgehung

Die Umgehung für dieses Problem besteht darin, beim Lesen von UTF-8-Text aus CSV- oder Parquet-Dateien immer UTF-8-Sortierung zu verwenden.

- In vielen Fällen müssen Sie nur UTF8-Sortierung für die Datenbank festlegen (Metadatenvorgang).

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- Sie können die Sortierung der VARCHAR-Spalte explizit in OPENROWSET oder einer externen Tabelle definieren:

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- Wenn Sie keine UTF8-Sortierung für externe Tabellen angegeben haben, die UTF8-Daten lesen, müssen Sie betroffene externe Tabellen neu erstellen und UTF8-Sortierung für VARCHAR-Spalten (Metadatenvorgang) festlegen.


## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Parquet-Dateien mithilfe eines serverlosen SQL-Pools in Azure Synapse Analytics](../sql/query-parquet-files.md)
* [Abfragen von CSV-Dateien](../sql/query-single-csv-file.md)
* [CETAS mit Synapse SQL](../sql/develop-tables-cetas.md)
* [Schnellstart: Verwenden eines serverlosen SQL-Pools](../quickstart-sql-on-demand.md)
