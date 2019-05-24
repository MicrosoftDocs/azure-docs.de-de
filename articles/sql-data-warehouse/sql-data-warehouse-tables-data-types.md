---
title: Definieren von Datentypen – Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Empfehlungen zum Definieren von Tabellendatentypen in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06a273d3bfd5d416039a992e36bd4b0f72a85f78
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851550"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tabellendatentypen in Azure SQL Data Warehouse
Empfehlungen zum Definieren von Tabellendatentypen in Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Was sind die Datentypen?

SQL Data Warehouse unterstützt die gängigsten Datentypen. Eine Liste der unterstützten Datentypen finden Sie in der CREATE TABLE-Anweisung unter [Datentypen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes). 

## <a name="minimize-row-length"></a>Minimieren der Zeilenlänge
Durch das Minimieren der Größe von Datentypen wird die Zeilenlänge verkürzt. Dies führt zu einer besseren Abfrageleistung. Verwenden Sie den kleinsten Datentyp, der für Ihre Daten funktioniert. 

- Vermeiden Sie es, für Zeichenspalten eine hohe Standardlänge zu definieren. Wenn der längste Wert 25 Zeichen umfasst, sollten Sie die Spalte VARCHAR(25) definieren. 
- Vermeiden Sie die Verwendung von [NVARCHAR][NVARCHAR], wenn Sie nur VARCHAR benötigen.
- Verwenden Sie anstelle von NVARCHAR(MAX) oder VARCHAR(MAX) nach Möglichkeit NVARCHAR(4000) oder VARCHAR(8000).

Wenn Sie die Tabellen mit externen PolyBase-Tabellen laden, darf die definierte Länge der Tabellenzeile 1MB nicht überschreiten. Wenn eine Zeile mit Daten variabler Länge 1 MB überschreitet, können Sie die Zeile mit BCP, jedoch nicht mit PolyBase laden.

## <a name="identify-unsupported-data-types"></a>Identifizieren nicht unterstützter Datentypen
Wenn Sie die Datenbank von einer anderen SQL-Datenbank migrieren, kommen unter Umständen einige Datentypen vor, die in SQL Data Warehouse nicht unterstützt werden. Verwenden Sie diese Abfrage, um nicht unterstützte Datentypen in Ihrem vorhandenen SQL-Schema zu ermitteln.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Problemumgehungen für nicht unterstützte Datentypen

Die folgende Liste zeigt die Datentypen, die nicht von SQL Data Warehouse unterstützt werden, und bietet Alternativen, die Sie anstelle der nicht unterstützten Datentypen verwenden können.

| Nicht unterstützte Datentypen | Problemumgehung |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Spalte in mehrere Spalten mit starker Typisierung unterteilen. |
| [Tabelle](/sql/t-sql/data-types/table-transact-sql) |In temporäre Tabellen konvertieren. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Überarbeiten Sie Code mit [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) und der [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql)-Funktion. Nur Konstanten werden als Standardwerte unterstützt. Daher kann „current_timestamp“ nicht als Standardeinschränkung definiert werden. Wenn Sie Zeilenversionswerte aus einer Spalte vom Typ „timestamp“ migrieren müssen, sollten Sie [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) oder [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) für NOT NULL- oder NULL-Zeilenversionswerte verwenden. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [Benutzerdefinierter Typ](/sql/relational-databases/native-client/features/using-user-defined-types) |Führen Sie, wenn möglich, eine Rückkonvertierung in den nativen Datentyp durch. |
| Standardwerte | Standardwerte unterstützen nur Literale und Konstanten. |


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Entwickeln von Tabellen finden Sie unter [Einführung in das Entwerfen von Tabellen in Azure SQL Data Warehouse](sql-data-warehouse-tables-overview.md).
