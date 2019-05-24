---
title: Verwenden benutzerdefinierter Schemas in SQL Data Warehouse | Microsoft Docs
description: Tipps für die Verwendung von benutzerdefinierten T-SQL-Schemas in Azure SQL Data Warehouse zum Entwickeln von Lösungen.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 7e22dc69a9da1d9b5a8c0ff13f73769b1ed4514a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861716"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Verwenden benutzerdefinierter Schemas in SQL Data Warehouse
Tipps für die Verwendung von benutzerdefinierten T-SQL-Schemas in Azure SQL Data Warehouse zum Entwickeln von Lösungen.

## <a name="schemas-for-application-boundaries"></a>Schemas für Anwendungsgrenzen

Herkömmliche Data Warehouses verwenden häufig separate Datenbanken, um Anwendungsgrenzen auf Basis von Workload, Domäne oder Sicherheit zu erstellen. Ein herkömmliches Data Warehouse in SQL Server kann z. B. eine Stagingdatenbank, eine Data Warehouse-Datenbank und einige Data Mart-Datenbanken enthalten. In dieser Topologie funktioniert jede Datenbank als Workload und Sicherheitsgrenze in der Architektur.

Im Gegensatz dazu werden mit SQL Data Warehouse die gesamten Data Warehouse-Workloads innerhalb einer Datenbank ausgeführt. Datenbankübergreifende Verknüpfungen sind nicht zulässig. SQL Data Warehouse erwartet daher, dass alle vom Warehouse verwendeten Tabellen innerhalb der einen Datenbank gespeichert werden.

> [!NOTE]
> SQL Data Warehouse unterstützt keine datenbankübergreifenden Abfragen. Data Warehouse-Implementierungen, die diese Funktion nutzen, müssen daher überarbeitet werden.
> 
> 

## <a name="recommendations"></a>Recommendations
Hierbei handelt es sich um Empfehlungen für die Konsolidierung der Workload-, Sicherheits-, Domänen- und Funktionsgrenzen mithilfe von benutzerdefinierten Schemas.

1. Verwenden Sie eine SQL Data Warehouse-Datenbank zum Ausführen Ihrer gesamten Data Warehouse-Workloads.
2. Konsolidieren Sie Ihre vorhandene Data Warehouse-Umgebung zur Verwendung einer SQL Data Warehouse-Datenbank.
3. Nutzen Sie **benutzerdefinierte Schemas** , um die Grenze bereitzustellen, die zuvor mithilfe von Datenbanken implementiert wurde.

Wenn zuvor keine benutzerdefinierten Schemas verwendet wurden, können Sie von Grund auf neu beginnen. Verwenden Sie einfach den alten Datenbanknamen als Grundlage für Ihre benutzerdefinierten Schemas in der SQL Data Warehouse-Datenbank.

Wenn bereits Schemas verwendet wurden, stehen Ihnen einige Optionen zur Verfügung:

1. Entfernen Sie die älteren Schemanamen, und beginnen Sie von vorn.
2. Behalten Sie die älteren Schemanamen bei, indem Sie diese vorab an den Tabellennamen anhängen.
3. Behalten Sie die älteren Schemanamen bei, indem Sie Sichten auf die Tabelle in einem zusätzlichen Schema zum Neuerstellen der alten Schemastruktur implementieren.

> [!NOTE]
> Option 3 mag auf den ersten Blick die attraktivste Option sein. Bei genauerem Hinsehen treten jedoch Probleme zutage. Sichten werden nur in SQL Data Warehouse gelesen. Jede Änderung von Daten oder Tabellen muss für die Basistabelle ausgeführt werden. Option 3 führt außerdem eine Schicht von Sichten im System ein. Sie sollten dies besonders berücksichtigen, wenn Sie in Ihrer Architektur bereits Sichten verwenden.
> 
> 

### <a name="examples"></a>Beispiele:
Implementieren von benutzerdefinierten Schemas basierend auf Datenbanknamen

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Behalten Sie die älteren Schemanamen bei, indem Sie diese vorab an den Tabellennamen anhängen. Verwenden Sie Schemas für die Workload-Grenze.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Beibehalten von älteren Schemanamen mit Sichten

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Jede Änderung in der Schemastrategie erfordert eine Überprüfung des Sicherheitsmodells für die Datenbank. In vielen Fällen können Sie das Sicherheitsmodell vereinfachen, indem Sie Berechtigungen auf Schemaebene zuweisen. Wenn präzisere Berechtigungen erforderlich sind, können Sie Datenbankrollen verwenden.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).

