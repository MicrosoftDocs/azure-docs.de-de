---
title: Toolmatrix von Azure Database Migration Service
description: Lernen Sie die Dienste und Tools zum Migrieren von Datenbanken und zur Unterstützung der verschiedenen Phasen des Migrationsprozesses kennen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: reference
ms.date: 03/03/2020
ms.openlocfilehash: 1a8ae7fd03a0182256970b9eb9eb18b7c77894ff
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962976"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Dienste und Tools für Datenmigrationsszenarien

Dieser Artikel enthält eine Übersicht über die Dienste und Tools von Microsoft und Drittanbietern, mit deren Hilfe Sie verschiedene Datenbank- und Datenmigrationsszenarien und Spezialaufgaben ausführen können.

In den folgenden Tabellen sind jeweils die Dienste und Tools aufgeführt, die Sie für eine erfolgreiche Planung der Datenmigration und für den erfolgreichen Abschluss der verschiedenen Phasen verwenden können.

> [!NOTE]
> Bei den Elementen, die in den folgenden Tabellen mit einem Sternchen (*) gekennzeichnet sind, handelt es sich um Tools von Drittanbietern.

## <a name="business-justification-phase"></a>Phase: Geschäftliche Begründung

| `Source` | Ziel | Entdecken/<br/>Inventory | Ziel und SKU<br/>Empfehlung | TCO/ROI und<br/>Geschäftsszenario |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL-Datenbank | [MAP-Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) |
 SQL Server | Azure SQL-Datenbank MI | [MAP-Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Virtueller Azure SQL-Computer | [MAP-Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | Azure Synapse Analytics | [MAP-Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) |  | [Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS SQL | Azure SQL DB, MI, VM |  | [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Azure SQL DB, MI, VM | [MAP-Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[MigVisor*](https://www.migvisor.com/) |  |
| Oracle | Azure Synapse Analytics | [MAP-Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure-Datenbank für PostgreSQL:<br/>Einzelserver | [MAP-Toolkit](/previous-versions//bb977556(v=technet.10))<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/) | [Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Azure-Datenbank für MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS MySQL | Azure-Datenbank für MySQL |  |  | [Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | Azure-Datenbank für PostgreSQL:<br/>Einzelserver | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) |
| RDS PostgreSQL | Azure-Datenbank für PostgreSQL:<br/>Einzelserver |  |  | [Gesamtkostenrechner](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Zugriff | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase: SAP ASE | Azure SQL DB, MI, VM | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase: SAP IQ | Azure SQL DB, MI, VM |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Phase: Vor der Migration

| `Source` | Ziel | App-Datenzugriff<br/>Bewertung der Ebenen | Datenbank<br/>Bewertung | Leistung<br/>Bewertung |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL-Datenbank | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL-Datenbank MI | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Virtueller Azure SQL-Computer | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure Synapse Analytics | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) |  |  |
| RDS SQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure Synapse Analytics | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | Azure-Datenbank für PostgreSQL:<br/>Einzelserver |  | [Ora2Pg*](http://ora2pg.darold.net/start.html) |  |
| MongoDB | Cosmos DB |  | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Azure-Datenbank für MySQL |  |  |  |
| RDS MySQL | Azure-Datenbank für MySQL |  |  |  |
| PostgreSQL | Azure-Datenbank für PostgreSQL:<br/>Einzelserver |  |  |  |
| RDS PostgreSQL | Azure-Datenbank für PostgreSQL:<br/>Einzelserver |  |  |  |
| DB2 | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Zugriff | Azure SQL DB, MI, VM |  | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase: SAP ASE | Azure SQL DB, MI, VM | [DAMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase: SAP IQ | Azure SQL DB, MI, VM |  | |  |
| | | | | |

## <a name="migration-phase"></a>Phase: Migration

| `Source` | Ziel | Schema | Data<br/>(Offline) | Data<br/>(Online) |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL-Datenbank | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure SQL-Datenbank MI | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Virtueller Azure SQL-Computer | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize*](https://www.cloudamize.com/) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize*](https://www.cloudamize.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Azure Synapse Analytics |  |  |  |
| RDS SQL | Azure SQL DB, MI, VM | [DMA](/sql/dma/dma-overview?view=sql-server-2017) | [DMA](/sql/dma/dma-overview?view=sql-server-2017)<br/>[DMS](https://azure.microsoft.com/services/database-migration/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure Synapse Analytics | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Azure-Datenbank für PostgreSQL:<br/>Einzelserver | [Ispirer*](https://www.ispirer.com/solutions) | [Ispirer*](https://www.ispirer.com/solutions) | [DMS](https://azure.microsoft.com/services/database-migration/) |
| MongoDB | Cosmos DB | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data *](https://www.imanisdata.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data *](https://www.imanisdata.com/) | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data *](https://www.imanisdata.com/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis Data *](https://www.imanisdata.com/) | [Imanis Data *](https://www.imanisdata.com/) | [Imanis Data *](https://www.imanisdata.com/) |
| MySQL | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Azure-Datenbank für MySQL | [MySQL dump*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS MySQL | Azure-Datenbank für MySQL | [MySQL dump*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | Azure-Datenbank für PostgreSQL:<br/>Einzelserver | [PG dump*](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| RDS PostgreSQL | Azure-Datenbank für PostgreSQL:<br/>Einzelserver | [PG dump*](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [DMS](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Zugriff | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase: SAP ASE | Azure SQL DB, MI, VM | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [SSMA](/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Ispirer*](https://www.ispirer.com/solutions) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Sybase: SAP IQ | Azure SQL DB, MI, VM | [Ispirer*](https://www.ispirer.com/solutions) | [Ispirer*](https://www.ispirer.com/solutions) | |
| | | | | |

## <a name="post-migration-phase"></a>Phase: Nach der Migration

| `Source` | Ziel | Optimieren |
| --- | --- | --- |
| SQL Server | Azure SQL-Datenbank | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure SQL-Datenbank MI | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Virtueller Azure SQL-Computer | [Cloud Atlas*](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Azure Synapse Analytics |  |
| RDS SQL | Azure SQL DB, MI, VM |  |
| Oracle | Azure SQL DB, MI, VM |  |
| Oracle | Azure Synapse Analytics |  |
| Oracle | Azure-Datenbank für PostgreSQL:<br/>Einzelserver |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Azure SQL DB, MI, VM |  |
| MySQL | Azure-Datenbank für MySQL |  |
| RDS MySQL | Azure-Datenbank für MySQL |  |
| PostgreSQL | Azure-Datenbank für PostgreSQL:<br/>Einzelserver |  |
| RDS PostgreSQL | Azure-Datenbank für PostgreSQL:<br/>Einzelserver |  |
| DB2 | Azure SQL DB, MI, VM |  |
| Zugriff | Azure SQL DB, MI, VM |  |
| Sybase: SAP ASE | Azure SQL DB, MI, VM |  |
| Sybase: SAP IQ | Azure SQL DB, MI, VM |  |
| | | |

## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](dms-overview.md).