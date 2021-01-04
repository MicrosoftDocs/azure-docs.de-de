---
title: Transparent Data Encryption (Portal) für dedizierten SQL-Pool (früher SQL DW)
description: Transparent Data Encryption (TDE) für dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 45c7b53d3bbe0c57e96fc5435650c4e86b0cb032
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455272"
---
# <a name="get-started-with-transparent-data-encryption-tde-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Erste Schritte mit Transparent Data Encryption (TDE) für dedizierten SQL-Pool (früher SQL DW) in Azure Synapse Analytics

> [!div class="op_single_selector"]
>
> * [Sicherheitsübersicht](sql-data-warehouse-overview-manage-security.md)
> * [Authentifizierung](sql-data-warehouse-authentication.md)
> * [Verschlüsselung (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Verschlüsselung (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Sie müssen Administrator oder ein Mitglied der Rolle „dbmanager“ sein, um Transparent Data Encryption (TDE) zu aktivieren.

## <a name="enabling-encryption"></a>Aktivieren der Verschlüsselung

Führen Sie zum Aktivieren von TDE die folgenden Schritte aus:

1. Öffnen Sie die Datenbank im [Azure-Portal](https://portal.azure.com)
2. Klicken Sie im Datenbank-Blatt auf die Schaltfläche **Einstellungen** .
3. Klicken Sie auf die Option **Transparent Data Encryption**. ![Portaleinstellungen](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Klicken Sie auf die Einstellung **An**. ![Portaleinstellungen „An“](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Klicken Sie auf **Speichern**.
   ![Portaleinstellungen „Speichern“](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Deaktivieren der Verschlüsselung

Führen Sie zum Deaktivieren von TDE die folgenden Schritte aus:

1. Öffnen Sie die Datenbank im [Azure-Portal](https://portal.azure.com)
2. Klicken Sie im Datenbank-Blatt auf die Schaltfläche **Einstellungen** .
3. Klicken Sie auf die Option **Transparent Data Encryption**. ![Portaleinstellungen](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Klicken Sie auf die Einstellung **Aus**. ![Portaleinstellungen „Aus“](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Klicken Sie auf **Speichern**.
   ![Portaleinstellungen „Speichern“ 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Verschlüsselung-DMVs

Die Verschlüsselung kann mit folgenden DMVs überprüft werden:

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
