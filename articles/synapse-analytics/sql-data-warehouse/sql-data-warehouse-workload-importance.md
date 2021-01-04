---
title: Workloadpriorität
description: Leitfaden zum Festlegen der Priorität für Abfragen eines dedizierten SQL-Pools in Azure Synapse Analytics
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 07c781672874bff306c9d25a464ec66414ebc9f1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322120"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure Synapse Analytics-Workloadpriorität

In diesem Artikel wird erläutert, wie sich die Workloadpriorität auf die Ausführungsreihenfolge von Anforderungen eines dedizierten SQL-Pools in Azure Synapse auswirken kann.

## <a name="importance"></a>Wichtigkeit

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Unternehmensanforderungen können erfordern, dass Data Warehousing-Workloads über mehr Priorität verfügen als andere.  Stellen Sie sich ein Szenario vor, in dem unternehmenskritische Vertriebsdaten vor der Abrechnung des Geschäftsjahres geladen werden.  Datenladevorgänge für andere Quellen wie Wetterdaten verfügen über keine strikten SLAs (Vereinbarungen zum Servicelevel). Durch Festlegen einer hohen Priorität für eine Anforderung zum Laden von Vertriebsdaten und einer niedrigen Priorität für eine Anforderung zum Laden von Wetterdaten wird sichergestellt, dass der Ladevorgang für die Vertriebsdaten zuerst auf Ressourcen zugreifen kann und schneller abgeschlossen wird.

## <a name="importance-levels"></a>Prioritätsstufen

Es gibt fünf Prioritätsstufen: „low“, „below_normal“, „normal“, „above_normal“ und „high“.  Anforderungen, für die keine Priorität festgelegt wird, weisen die Standardstufe „normal“ auf. Anforderungen mit der gleichen Prioritätsstufe weisen das herkömmliche Planungsverhalten auf.

## <a name="importance-scenarios"></a>Prioritätsszenarios

Neben dem oben beschriebenen herkömmlichen Prioritätsszenario mit Vertriebs- und Wetterdaten gibt es weitere Szenarios, in denen die Workloadpriorität bei den Anforderungen für Datenverarbeitung und Abfragen hilft.

### <a name="locking"></a>Sperren

Der Zugriff auf Sperren für Lese- und Schreibaktivitäten ist ein Bereich, in dem natürliche Konflikte entstehen. Aktivitäten wie [Partitionswechsel](sql-data-warehouse-tables-partition.md) oder [RENAME OBJECT](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) erfordern Sperren mit erhöhten Rechten.  Ohne Workloadpriorität optimiert der dedizierte SQL-Pool in Azure Synapse den Durchsatz. Die Optimierung des Durchsatzes bedeutet, dass Anforderungen in der Warteschlange Anforderungen mit höheren Sperranforderungen umgehen können, die vorher in die Anforderungswarteschlange aufgenommen wurden, wenn ausgeführte Anforderungen und Anforderungen in der Warteschlange die gleichen Sperranforderungen und Ressourcen aufweisen. Sobald die Workloadpriorität für Anforderungen mit höheren Sperranforderungen angewendet wurde, werden Anforderungen mit höherer Priorität vor Anforderungen mit niedriger Priorität ausgeführt.

Betrachten Sie das folgenden Beispiel:

- Die Abfrage „Q1“ wird aktiv ausgeführt und wählt Daten aus SalesFact aus.
- Die Abfrage „Q2“ befindet sich in der Warteschlange und wartet darauf, dass die Abfrage „Q1“ abgeschlossen ist.  Die Abfrage „Q2“ wurde um 9:00 Uhr übermittelt und versucht, neue Daten per Partitionswechsel in SalesFact einzufügen.
- Die Abfrage „Q3“ wurde um 9:01 Uhr übermittelt und soll Daten aus SalesFact auswählen.

Wenn die Abfragen „Q2“ und „Q3“ dieselbe Priorität aufweisen und „Q1“ immer noch ausgeführt wird, wird zuerst „Q3“ ausgeführt. Die Abfrage „Q2“ wartet weiterhin auf eine exklusive Sperre für SalesFact.  Wenn die Abfrage „Q2“ eine höhere Priorität als „Q3“ aufweist, muss die Abfrage „Q3“ auf den Abschluss von „Q2“ warten, bevor sie ausgeführt werden kann.

### <a name="non-uniform-requests"></a>Nicht einheitliche Anforderungen

Die Priorität ist auch zum Erfüllen von Abfrageanforderungen in Szenarios nützlich, in denen Anforderungen mit verschiedenen Ressourcenklassen übermittelt werden.  Wie bereits erwähnt, optimiert der dedizierte SQL-Pool in Azure Synapse bei gleicher Priorität den Durchsatz. Wenn Anforderungen mit gemischten Größen (z. B. „smallrc“ oder „mediumrc“) in die Warteschlange eingereiht werden, wählt der dedizierte SQL-Pool die erste eingegangene Anforderung aus, die von den verfügbaren Ressourcen abgedeckt werden kann. Wenn die Workloadpriorität angewendet wird, wird als Nächstes die Anforderung mit der höchsten Priorität geplant.
  
Betrachten Sie das folgende Beispiel für DW500c:

- Mit „Q1“, „Q2“, „Q3“ und „Q4“ werden smallrc-Abfragen ausgeführt.
- „Q5“ wird um 9:00 Uhr mit der Ressourcenklasse „mediumrc“ übermittelt.
- „Q6“ wird um 9:01 Uhr mit der Ressourcenklasse „smallrc“ übermittelt.

Da die Abfrage „Q5“ die Klasse „mediumrc“ aufweist, erfordert sie zwei Parallelitätsslots. Die Abfrage „Q5“ muss warten, bis zwei der aktiven Abfragen abgeschlossen sind.  Jedoch wird sofort „Q6“ geplant, da die Ressourcen zum Ausführen der Abfrage vorhanden sind, wenn eine der aktiven Abfragen (Q1-Q4) abgeschlossen wird.  Wenn „Q5“ über eine höhere Priorität als „Q6“ verfügt, wartet „Q6“ darauf, dass „Q5“ ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Erstellen einer Klassifizierung finden Sie unter [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  
- Weitere Informationen zur Workloadklassifizierung finden Sie unter [Workloadklassifizierung](sql-data-warehouse-workload-classification.md).  
- Lesen Sie für die Erstellung eines Workloadklassifizierers den Schnellstart [Erstellen eines Workloadklassifizierers](quickstart-create-a-workload-classifier-tsql.md).
- Lesen Sie die Anleitungsartikel zum [Konfigurieren der Workloadpriorität](sql-data-warehouse-how-to-configure-workload-importance.md) und zum [Verwalten und Überwachen der Workloadpriorität](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Sie können die Abfragen und die zugewiesene Wichtigkeit unter [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) anzeigen.
