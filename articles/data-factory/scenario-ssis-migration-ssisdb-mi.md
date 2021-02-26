---
title: SSIS-Migration mit Azure SQL Managed Instance als Datenbankworkloadziel
description: SSIS-Migration mit Azure SQL Managed Instance als Datenbankworkloadziel
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 3d2bc60f8ba7120f8d962500c06be50e905c11a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373588"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS-Migration mit Azure SQL Managed Instance als Datenbankworkloadziel

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Wenn Sie Datenbankworkloads von einer SQL Server-Instanz zu Azure SQL Managed Instance migrieren, sollten Sie mit [Azure Data Migration Service](../dms/dms-overview.md) (DMS) und den [Netzwerktopologien für SQL Managed Instance-Migrationsvorgänge mithilfe von DMS](../dms/resource-network-topologies.md) vertraut sein.

Dieser Artikel konzentriert sich auf die Migration von SSIS-Paketen (SQL Server Integration Service), die im SSIS-Katalog (SSISDB) gespeichert sind, und auf SQL Server-Agentaufträge, die die Ausführung von SSIS-Paketen planen.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrieren des SSIS-Katalogs (SSISDB)

Die SSISDB-Migration kann mithilfe von DMS erfolgen, wie im folgenden Artikel beschrieben: [Migrieren von SSIS-Paketen zu SQL Managed Instance](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS-Aufträge für den SQL Managed Instance-Agent

SQL Managed Instance verfügt wie der lokale SQL Server-Agent über einen nativen, erstklassigen Planer.  Sie können [SSIS-Pakete über den Azure SQL Managed Instance-Agent ausführen](how-to-invoke-ssis-package-managed-instance-agent.md).

Da noch kein Migrationstool für SSIS-Aufträge verfügbar ist, müssen sie vom lokalen SQL Server-Agent mithilfe von Skripts bzw. durch manuelles Kopieren zum SQL Managed Instance-Agent migriert werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Azure Data Factory](./introduction.md)
- [Azure-SSIS Integration Runtime](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [Netzwerktopologien für SQL Managed Instance-Migrationsvorgänge](../dms/resource-network-topologies.md)
- [Migrieren von SSIS-Paketen zu SQL Managed Instance](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Nächste Schritte

- [Herstellen einer Verbindung mit dem SSIS-Katalog (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Ausführen von in Azure bereitgestellten SSIS-Paketen](/sql/integration-services/lift-shift/ssis-azure-run-packages)