---
title: Verwenden von Bezeichnungen zum Instrumentieren von Abfragen
description: Tipps zum Verwenden von Bezeichnungen zum Instrumentieren von Abfragen an dedizierte SQL-Pools in Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7958caf71658dcdcbf31bac84697931e9049452f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462738"
---
# <a name="using-labels-to-instrument-queries-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Verwenden von Bezeichnungen zum Instrumentieren von Abfragen an dedizierte SQL-Pools in Azure Synapse Analytics

In diesem Artikel erhalten Sie Tipps zum Entwickeln von Lösungen mit Bezeichnungen zum Instrumentieren von Abfragen in dedizierten SQL-Pools.

## <a name="what-are-labels"></a>Was sind Bezeichnungen?

Für dedizierte SQL-Pools wird das Konzept der „Abfragebezeichnungen“ unterstützt. Bevor wir darauf näher eingehen, sehen wir uns ein Beispiel an:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Mit dieser letzten Zeile wird die Abfrage mit der Zeichenfolge „My Query Label“ versehen. Dieses Tag ist hilfreich, da die Bezeichnung über die DMVs abgefragt werden kann.

Abfragen für Bezeichnungen stellen einen Mechanismus dar, um fehlerhafte Abfragen zu suchen und den Fortschritt über eine ELT-Ausführung zu ermitteln.

Eine gute Namenskonvention ist wirklich hilfreich. Beispielsweise ist eine Bezeichnung, die mit „PROJEKT“, „PROZEDUR“, „ANWEISUNG“ oder „KOMMENTAR“ beginnt, hilfreich, um die Abfrage im Code der Quellcodeverwaltung eindeutig zu identifizieren.

Die folgende Abfrage verwendet zum Suchen nach Bezeichnungen eine dynamische Verwaltungssicht:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Es ist wichtig, dass Sie das Wort „label“ bei Abfragen in eckige Klammern oder doppelte Anführungszeichen setzen. Das Wort „label“ ist ein reserviertes Wort und verursacht einen Fehler, wenn es nicht begrenzt wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).
