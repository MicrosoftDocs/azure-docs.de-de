---
title: Purview-Connector – Übersicht
description: In diesem Artikel sind die verschiedenen in Purview unterstützten Datenspeicher und Funktionen skizziert.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 26efa840bacd3ee542816cf861cf2ef586b0582f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780211"
---
# <a name="supported-data-stores"></a>Unterstützte Datenspeicher

Purview unterstützt die folgenden Datenspeicher. Klicken Sie auf die einzelnen Datenspeicher, um mehr über die unterstützten Funktionen und die entsprechende Konfiguration zu erfahren.

## <a name="purview-data-sources"></a>Purview-Datenquellen

|**Kategorie**|  **Datenspeicher**  |**Metadatenextrahierung**|**Vollständige Überprüfung**|**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Herkunft**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure SQL-Datenbank](register-scan-azure-sql-database.md)|Ja| Ja| Nein| Ja| Ja| Ja|
||[Verwaltete Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database-managed-instance.md)|Ja| Ja| Nein| Ja| Ja| Ja|
||[Azure Synapse Analytics (ehemals SQL DW)](register-scan-azure-synapse-analytics.md)|Ja| Ja| Nein| Ja| Ja| Ja|
|Datenbank|[SQL Server](register-scan-on-premises-sql-server.md)|Ja| Ja| Nein| Ja| Ja| Ja|
||[Teradata (Preview)](register-scan-teradata-source.md)|Ja| Ja| Nein| Nein| Nein| Ja|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Ja| Ja| Nein| Nein| Nein| Ja|

## <a name="next-steps"></a>Nächste Schritte

- [Registrieren und Überprüfen von Azure Blob Storage](register-scan-azure-blob-storage-source.md)