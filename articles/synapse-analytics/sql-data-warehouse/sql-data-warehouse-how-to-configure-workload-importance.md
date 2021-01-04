---
title: Konfigurieren der Workloadpriorität für dedizierte SQL-Pools
description: Hier erfahren Sie, wie Sie die Priorität der Anforderungsebene in Azure Synapse Analytics festlegen.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 067551d198f717dd40995cb8bc3e1345e82f078f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461917"
---
# <a name="configure-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Konfigurieren der Workloadpriorität in dedizierten SQL-Pools für Azure Synapse Analytics

Durch Festlegen der Priorität in dedizierten SQL-Pools für Azure Synapse können Sie die Zeitplanung von Abfragen beeinflussen. Abfragen mit höherer Priorität werden so geplant, dass sie vor Abfragen mit niedrigerer Priorität ausgeführt werden. Um Abfragen Priorität zuzuweisen, müssen Sie einen Workloadklassifizierer erstellen.

## <a name="create-a-workload-classifier-with-importance"></a>Erstellen eines Workloadklassifizierers mit Priorität

Oft gibt es in einem Data Warehouse-Szenario Benutzer in einem ausgelasteten System, deren Abfragen schnell ausgeführt werden müssen.  Der Benutzer könnte eine Führungskraft im Unternehmen sein, die Berichte ausführen muss, oder ein Analyst, der eine Ad-hoc-Abfrage ausführt. Sie erstellen einen Workloadklassifizierer, sodass einer Abfrage eine Priorität zugewiesen wird.  Bei den nachstehenden Beispielen wird die Syntax [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) verwendet, um zwei Klassifizierer zu erstellen. `Membername` kann ein einzelner Benutzer oder eine Gruppe sein.  Sie können die vorhandenen Benutzer des dedizierten SQL-Pools wie folgt suchen:

```sql
Select name from sys.sysusers
```

Zum Erstellen eines Workloadklassifizierers für einen Benutzer mit höherer Priorität führen Sie aus:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

Zum Erstellen eines Workloadklassifizierers für einen Benutzer, der Ad-hoc-Abfragen mit niedrigerer Priorität ausführt, führen Sie aus:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Workloadverwaltung finden Sie unter [Workloadklassifizierung](sql-data-warehouse-workload-classification.md).
- Weitere Informationen zu Priorität finden Sie unter [Workloadpriorität](sql-data-warehouse-workload-importance.md).

> [!div class="nextstepaction"]
> [Wechseln zu „Verwalten“ und Überwachen der Workloadpriorität](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
