---
title: Konfigurieren der Workloadpriorität in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Informationen zum Festlegen der Priorität für die Anforderungsebene.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 7121d2f022f9c9a5bbc02f04955d2857f3ec986f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241240"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Konfigurieren der Workloadpriorität in Azure SQL Data Warehouse

Das Festlegen der Priorität in SQL Data Warehouse ermöglicht es Ihnen, die Planung von Abfragen zu beeinflussen. Abfragen mit höherer Priorität werden so geplant, dass sie vor Abfragen mit niedrigerer Priorität ausgeführt werden. Um Abfragen Priorität zuzuweisen, müssen Sie einen Workloadklassifizierer erstellen.

## <a name="create-a-workload-classifier-with-importance"></a>Erstellen eines Workloadklassifizierers mit Priorität

Oft gibt es in einem Data Warehouse-Szenario Benutzer, deren Abfragen schnell ausgeführt werden müssen.  Der Benutzer könnte eine Führungskraft im Unternehmen sein, die Berichte ausführen muss, oder ein Analyst, der eine Ad-hoc-Abfrage ausführt. Sie erstellen einen Workloadklassifizierer, um einer Abfrage eine bestimmte Priorität zuzuweisen.  Bei den nachstehenden Beispielen wird die neue Syntax [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) verwendet, um zwei Klassifizierer zu erstellen.  „MEMBERNAME“ kann ein einzelner Benutzer oder eine Gruppe sein. Einzelbenutzerklassifizierungen haben Vorrang vor Rollenklassifizierungen. Für die Suche nach vorhandenen Data Warehouse-Benutzern führen Sie aus:

```sql
Select name from sys.sysusers
```

Zum Erstellen eines Workloadklassifizierers für einen Benutzer mit höherer Priorität führen Sie aus:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Zum Erstellen eines Workloadklassifizierers für einen Benutzer, der Ad-hoc-Abfragen mit niedrigerer Priorität ausführt, führen Sie aus:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Workloadverwaltung finden Sie unter [Workloadklassifizierung](sql-data-warehouse-workload-classification.md).
- Weitere Informationen zu Priorität finden Sie unter [Workloadpriorität](sql-data-warehouse-workload-importance.md).

> [!div class="nextstepaction"]
> [Wechseln zu „Verwalten“ und Überwachen der Workloadpriorität](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
