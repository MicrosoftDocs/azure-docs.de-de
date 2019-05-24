---
title: Verwenden von Bezeichnungen zum Instrumentieren von Abfragen in SQL Data Warehouse | Microsoft-Dokumentation
description: Tipps zum Verwenden von Bezeichnungen zum Instrumentieren von Abfragen in Azure SQL Data Warehouse für die Entwicklung von Lösungen
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 5c53fc3594d02c92ea6a238f89417e31dad4818c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861783"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Verwenden von Bezeichnungen zum Instrumentieren von Abfragen in Azure SQL Data Warehouse
Tipps zum Verwenden von Bezeichnungen zum Instrumentieren von Abfragen in Azure SQL Data Warehouse für die Entwicklung von Lösungen


## <a name="what-are-labels"></a>Was sind Bezeichnungen?
In SQL Data Warehouse wird das Konzept der „Abfragebezeichnungen“ unterstützt. Bevor wir darauf näher eingehen, sehen wir uns ein Beispiel an:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Mit dieser letzten Zeile wird die Abfrage mit der Zeichenfolge „My Query Label“ versehen. Dieses Tag ist besonders hilfreich, da die Bezeichnung über die DMVs abgefragt werden kann. Abfragen für Bezeichnungen stellen einen Mechanismus dar, um fehlerhafte Abfragen zu suchen und den Fortschritt über eine ELT-Ausführung zu ermitteln.

Eine gute Namenskonvention ist wirklich hilfreich. Beispielsweise ist eine Bezeichnung, die mit „PROJEKT“, „PROZEDUR“, „ANWEISUNG“ oder „KOMMENTAR“ beginnt, hilfreich, um die Abfrage im Code der Quellcodeverwaltung eindeutig identifizieren zu können.

Die folgende Abfrage verwendet zum Suchen nach Bezeichnungen eine dynamische Verwaltungssicht.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Es ist wichtig, dass Sie das Wort „label“ bei Abfragen in eckige Klammern oder doppelte Anführungszeichen setzen. Das Wort „label“ ist ein reserviertes Wort und verursacht einen Fehler, wenn es nicht begrenzt wird. 
> 
> 

## <a name="next-steps"></a>Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).


