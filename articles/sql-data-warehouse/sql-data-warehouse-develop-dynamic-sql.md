---
title: Verwenden von dynamischem SQL in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Tipps zur Verwendung von dynamischem SQL-Code in Azure SQL Data Warehouse für die Entwickelung von Lösungen
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 567637cab1c983992b08f65352ab9a92bd448c5a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861819"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamischer SQL-Code in SQL Data Warehouse
Tipps zur Verwendung von dynamischem SQL-Code in Azure SQL Data Warehouse für die Entwickelung von Lösungen

## <a name="dynamic-sql-example"></a>Beispiel für dynamisches SQL

Beim Entwickeln von Anwendungscode für SQL Data Warehouse müssen Sie unter Umständen dynamischen SQL-Code verwenden, um flexible, generische und modulare Lösungen bereitstellen zu können. SQL Data Warehouse unterstützt derzeit keine BLOB-Datentypen. Dass keine Blobdatentypen unterstützt werden, könnte die Einschränkung der Größe Ihrer Zeichenfolgen zur Folge haben, da Blobdatentypen sowohl den Typ VARCHAR(MAX) als auch NVARCHAR(MAX) enthalten. Wenn Sie diese Typen im Anwendungscode zum Erstellen großer Zeichenfolgen verwendet haben, müssen Sie den Code in Segmente unterteilen und stattdessen die EXEC-Anweisung verwenden.

Ein einfaches Beispiel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Wenn die Zeichenfolge kurz ist, können Sie [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) wie üblich verwenden.

> [!NOTE]
> Anweisungen, die als dynamisches SQL ausgeführt werden, unterliegen weiterhin allen TSQL-Validierungsregeln.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).

