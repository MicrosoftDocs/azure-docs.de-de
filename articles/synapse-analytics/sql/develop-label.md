---
title: Verwenden von Abfragebezeichnungen in Synapse SQL
description: In diesem Artikel finden Sie wichtige Tipps zur Verwendung von Abfragebezeichnungen in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 59fa68d12f1d8be598810399fc5623c2af983979
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462244"
---
# <a name="use-query-labels-in-synapse-sql"></a>Verwenden von Abfragebezeichnungen in Synapse SQL

In diesem Artikel finden Sie wichtige Tipps zur Verwendung von Abfragebezeichnungen in Synapse SQL.

> [!NOTE]
> Der serverlose SQL-Pool unterstützt keine Bezeichnungsabfragen.

## <a name="what-are-query-labels"></a>Was sind Abfragebezeichnungen?

Für dedizierte SQL-Pools wird das Konzept der „Abfragebezeichnungen“ unterstützt. Bevor wir darauf näher eingehen, sehen wir uns ein Beispiel an:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Mit dieser letzten Zeile wird die Abfrage mit der Zeichenfolge „My Query Label“ versehen. Dieses Tag ist hilfreich, da die Bezeichnung über die DMVs abgefragt werden kann. Abfragen für Bezeichnungen stellen einen Mechanismus dar, um fehlerhafte Abfragen zu suchen und den Fortschritt über eine ELT-Ausführung zu ermitteln.

Gute Namenskonventionen sind sehr hilfreich. Beispielsweise ist eine Bezeichnung, die mit „PROJEKT“, „PROZEDUR“, „ANWEISUNG“ oder „KOMMENTAR“ beginnt, hilfreich, um die Abfrage im Code der Quellcodeverwaltung eindeutig zu identifizieren.

Die folgende Abfrage verwendet zum Suchen nach Bezeichnungen eine dynamische Verwaltungssicht:

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
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](develop-overview.md).


