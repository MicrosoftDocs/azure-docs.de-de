---
title: Optimieren des Gen2-Cache | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Gen2-Cache im Azure-Portal überwachen.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 26791aecb2ca57b31358d3385d07230c73c84904
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61474398"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Überwachen des Gen2-Cache
In der Gen2-Speicherarchitektur werden die am häufigsten abgefragten Columnstore-Segmente automatisch in einem Cache eingeordnet, der sich auf NVMe-basierten SSDs befindet, die für Gen2-Data Warehouses entwickelt wurden. Es wird eine bessere Leistung erreicht, wenn bei Ihren Abfragen Segmente abgerufen werden, die sich im Cache befinden. In diesem Artikel wird beschrieben, wie eine langsame Abfrageleistung überwacht und behoben wird, indem bestimmt wird, ob die Workload den Gen2-Cache optimal nutzt.  
## <a name="troubleshoot-using-the-azure-portal"></a>Behandeln von Problemen über das Azure-Portal
Mithilfe von Azure Monitor können Sie Metriken des Gen2-Cache anzeigen, um Probleme mit der Abfrageleistung zu beheben. Klicken Sie zunächst im Azure-Portal auf „Monitor“:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Wählen Sie die Schaltfläche „Metriken“ aus, und geben Sie die Informationen für **Abonnement**, **Ressourcengruppe**, **Ressourcentyp** und **Ressourcenname** für Ihr **Data Warehouse** an.

Die wesentlichen Metriken für die Problembehandlung des Gen2-Cache sind **Prozentsatz der Cachetreffer** und **Cacheverwendung in Prozent**. Konfigurieren Sie das Azure-Metrikdiagramm so, dass diese beiden Metriken angezeigt werden.

![Cachemetriken](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Prozentsatz der Cachetreffer und Cacheverwendung in Prozent
In der folgenden Matrix sind Szenarien basierend auf den Werten der Cachemetriken beschrieben:

|                                | **Hoher Prozentsatz der Cachetreffer** | **Niedriger Prozentsatz der Cachetreffer** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Hohe Cacheverwendung in Prozent** |          Szenario 1           |          Szenario 2:          |
| **Geringe Cacheverwendung in Prozent**  |          Szenario 3           |          Szenario 4          |

**Szenario 1:** Sie verwenden den Cache auf optimale Weise. [Problembehandlung](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) muss in anderen Bereichen erfolgen, in denen die Abfragen möglicherweise verlangsamt werden.

**Szenario 2:** Das aktuelle Arbeitsdataset passt nicht in den Cache, sodass aufgrund der physischen Lesevorgänge ein niedriger Prozentsatz an Cachetreffern anfällt. Sie können die Leistungsebene zentral hochskalieren und die Workload erneut ausführen, um den Cache zu füllen.

**Szenario 3:** Die Abfragen werden wahrscheinlich unabhängig von Problemen mit dem Cache langsam ausgeführt. [Problembehandlung](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) muss in anderen Bereichen erfolgen, in denen die Abfragen möglicherweise verlangsamt werden. Sie können zudem [Ihre Instanz zentral herunterskalieren](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor), um die Cachegröße zu reduzieren und so Kosten zu sparen. 

**Szenario 4:** Die Abfragen wurden möglicherweise aufgrund eines „kalten“ Cache langsam ausgeführt. Sie können Ihre Abfragen erneut ausführen, da das Arbeitsdataset sich nun im Cache befinden sollte. 

**Wichtig: Wenn der Prozentsatz der Cachetreffer oder die Cacheverwendung in Prozent nach der erneuten Ausführung der Workload nicht aktualisiert wird, befindet sich der Arbeitssatz möglicherweise bereits im Speicher. Beachten Sie, dass nur gruppierte Columnstore-Tabellen zwischengespeichert werden.**

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur allgemeinen Optimierung der Abfrageleistung finden Sie unter [Überwachen der Abfrageausführung](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
