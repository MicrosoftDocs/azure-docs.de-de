---
title: 'Schnellstart: Konfigurieren der Workloadisolation – T-SQL'
description: Verwenden Sie T-SQL zum Konfigurieren der Workloadisolation.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: ae21368269a2c098b4e35bf83b8c9fdf2749a297
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460553"
---
# <a name="quickstart-configure-workload-isolation-in-a-dedicated-sql-pool-using-t-sql"></a>Schnellstart: Konfigurieren der Workloadisolation in einem dedizierten SQL-Pool mithilfe von T-SQL

In dieser Schnellstartanleitung erstellen Sie schnell eine Arbeitsauslastungsgruppe und einen Klassifizierer zum Reservieren von Ressourcen für das Laden von Daten. Die Arbeitsauslastungsgruppe weist den Datenladevorgängen 20 % der Systemressourcen zu.  Der Workloadklassifizierer weist der Arbeitsauslastungsgruppe für Datenladevorgänge Anforderungen zu.  Bei einer Isolation von 20 % für Datenladevorgänge sind Ressourcen zum Einhalten von SLAs garantiert.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

> [!NOTE]
> Das Erstellen einer Synapse SQL-Instanz in Azure Synapse Analytics führt möglicherweise zu einem neuen abrechenbaren Dienst.  Weitere Informationen finden Sie unter [Azure Synapse Analytics – Preise](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Voraussetzungen

In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie bereits über eine Synapse SQL-Instanz in Azure Synapse und über CONTROL DATABASE-Berechtigungen verfügen. Verwenden Sie die Anleitung unter [Erstellen und Verbinden – Portal](create-data-warehouse-portal.md), um bei Bedarf einen dedizierten SQL-Pool namens **mySampleDataWarehouse** zu erstellen.

## <a name="create-login-for-dataloads"></a>Erstellen einer Anmeldung für „DataLoads“

Erstellen Sie eine SQL Server-Authentifizierungsanmeldung in der `master`-Datenbank, indem Sie [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) für „ELTLogin“ verwenden.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Benutzer erstellen

[Erstellen Sie den Benutzer](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) „ELTLogin“ in „mySampleDataWarehouse“.

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Erstellen einer Arbeitsauslastungsgruppe

Erstellen Sie eine [Arbeitsauslastungsgruppe](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) für „DataLoads“ mit einer Isolation von 20 %.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Erstellen eines Workloadklassifizierers

Erstellen Sie einen [Workloadklassifizierer](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), um „ELTLogin“ der Arbeitsauslastungsgruppe „DataLoads“ zuzuordnen.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Anzeigen vorhandener Arbeitsauslastungsgruppen, Klassifizierer und Laufzeitwerte

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Ihnen werden Gebühren für Data Warehouse-Einheiten und die in Ihrem dedizierten SQL-Pool gespeicherten Daten in Rechnung gestellt. Diese Compute- und Speicherressourcen werden separat in Rechnung gestellt.

- Falls Sie die Daten im Speicher belassen möchten, können Sie Computeressourcen anhalten, wenn Sie den dedizierten SQL-Pool nicht verwenden. Wenn Sie Computeressourcen anhalten, werden Ihnen nur die Datenspeichergebühren in Rechnung gestellt. Sie können die Computeressourcen fortsetzen, wenn Sie mit den Daten arbeiten möchten.
- Wenn künftig keine Gebühren mehr anfallen sollen, können Sie den dedizierten SQL-Pool löschen.

## <a name="next-steps"></a>Nächste Schritte

- Sie haben nun eine Arbeitsauslastungsgruppe erstellt. Führen Sie einige Abfragen als ELTLogin durch, um zu ermitteln, welche Leistung erzielt wird. Sie können die Abfragen und die zugewiesene Arbeitsauslastungsgruppe unter [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) anzeigen.
- Weitere Informationen zur Synapse SQL-Workloadverwaltung finden Sie unter [Workloadverwaltung](sql-data-warehouse-workload-management.md) und [Workloadisolation](sql-data-warehouse-workload-isolation.md).
