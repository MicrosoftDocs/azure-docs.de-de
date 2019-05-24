---
title: Migrieren Ihrer Lösung nach SQL Data Warehouse | Microsoft Docs
description: Migrationshinweise zum Übertragen Ihrer Lösung auf die Plattform Azure SQL Data Warehouse.
services: sql-data-warehouse
author: TwoUnder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: mausher
ms.reviewer: igorstan
ms.openlocfilehash: 2cf552dc42d2ef425098fed0ac80126f9885356b
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873684"
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migrieren Ihrer Lösung zu Azure SQL Data Warehouse
Finden Sie heraus, welcher Aufwand bei der Migration einer vorhandenen Datenbanklösung zu Azure SQL Data Warehouse erforderlich ist. 

## <a name="profile-your-workload"></a>Erstellen eines Profils für die Workload
Vor dem Migrieren möchten Sie sicher sein, dass SQL Data Warehouse die richtige Lösung für Ihre Workload ist. SQL Data Warehouse ist ein verteiltes System für die Analyse von großen Daten.  Das Migrieren zu SQL Data Warehouse erfordert einige Designänderungen, die nicht schwer zu verstehen sind, aber möglicherweise bei der Umsetzung einige Zeit in Anspruch nehmen. Wenn Ihr Unternehmen ein professionelles Data Warehouse benötigt, sind die Vorteile den Aufwand wert. Wenn Sie die Leistungsfähigkeit von SQL Data Warehouse nicht benötigen, ist es jedoch kostengünstiger, den SQL Server oder die Azure SQL-Datenbank zu verwenden.

Erwägen Sie die Verwendung von SQL Data Warehouse, wenn Sie:
- Ein oder mehrere Terabytes an Daten haben
- Analysen für große Mengen von Daten ausführen möchten
- Die Fähigkeit brauchen, Compute und Speicher zu skalieren 
- Kosten sparen wollen, indem Sie Computeressourcen anhalten, wenn Sie diese nicht benötigen.

Verwenden Sie SQL Data Warehouse nicht für operative Workloads (OLTP), die Folgendes haben:
- Sehr häufig Lese- und Schreibvorgänge
- Große Anzahl an skalaren Unterabfragen
- Große Mengen an einzelnen Zeileneinfügungen
- Verarbeitungsanforderungen Zeile für Zeile
- Inkompatible Formate (.json, .xml)


## <a name="plan-the-migration"></a>Planen der Migration

Nachdem Sie entschieden haben, eine vorhandene Lösung zu SQL Data Warehouse zu migrieren, ist es wichtig, die Migration zu planen, bevor Sie beginnen. 

Ein Ziel der Planung besteht darin sicherzustellen, dass Ihre Daten, die Tabellenschemas und Ihr Code mit SQL Data Warehouse kompatibel sind. Es gibt einige Kompatibilitätsunterschiede zwischen dem aktuellen System und SQL Data Warehouse, die Sie umgehen müssen. Darüber hinaus benötigt das Migrieren großer Datenmengen zu Azure Zeit. Eine sorgfältige Planung beschleunigt das Abrufen Ihrer Daten in Azure. 

Ein weiteres Ziel der Planung besteht darin, Designanpassungen vorzunehmen, um sicherzustellen, dass Ihre Lösung die Vorteile der hohen Abfrageleistung nutzt, die SQL Data Warehouse bereitstellen soll. Beim Auslegen von Data Warehouses auf Skalierung kommen andere Designmuster zum Tragen, sodass herkömmliche Ansätzen nicht immer optimal sind. Obwohl Sie nach der Migration einige Designanpassungen vornehmen können, wird das frühere Ändern im Prozess später Zeit sparen.

Sie müssen Ihre Tabellenschemas, Ihren Code und Ihre Daten migrieren, um eine erfolgreiche Migration durchzuführen. Anleitungen zu den Migrationsthemen finden Sie unter:

-  [Migrieren Ihres Schemas](sql-data-warehouse-migrate-schema.md)
-  [Migrieren Ihres Codes](sql-data-warehouse-migrate-code.md)
-  [Migrieren von Daten](sql-data-warehouse-migrate-data.md) 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>Nächste Schritte
Das CAT (Customer Advisory Team) bietet auch einige hervorragende SQL Data Warehouse-Anleitungen, die es in Blogs veröffentlicht.  Der Artikel [Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice] (Migrieren von Daten zu Azure SQL Data Warehouse in der Praxis) bietet Ihnen eine zusätzliche Anleitung zur Migration.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/
