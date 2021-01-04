---
title: Azure CLI-Skriptbeispiele
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure CLI-Skriptbeispiele zum Erstellen und Verwalten von Azure SQL-Datenbank und verwalteten Azure SQL-Instanzen
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 439167f29bb53d4a6e90b95826faa56e3c3170da
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563357"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Azure CLI-Beispiele für Azure SQL-Datenbank und verwaltete SQL-Instanzen 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Sie können Azure SQL-Datenbank und verwaltete SQL-Instanzen konfigurieren, indem Sie die <a href="/cli/azure">Azure-Befehlszeilenschnittstelle</a> verwenden.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

# <a name="azure-sql-database"></a>[Azure SQL-Datenbank](#tab/single-database)

Die folgende Tabelle enthält Links zu Azure CLI-Skriptbeispielen zum Verwalten von Einzel- und Pooldatenbanken in Azure SQL-Datenbank. 

|Bereich|BESCHREIBUNG|
|---|---|
|**Erstellen von Datenbanken in Azure SQL-Datenbank**||
| [Create a single SQL database and configure a firewall rule using PowerShell](scripts/create-and-configure-database-cli.md) (Erstellen einer einzelnen SQL-Datenbank und Konfigurieren einer Firewallregel mit PowerShell) | Erstellt eine Azure SQL-Datenbank-Instanz und konfiguriert eine Firewallregel auf Serverebene. |
| [Create elastic pools and move databases between pools and out of a pool using PowerShell](scripts/move-database-between-elastic-pools-cli.md) (Erstellen von Pools für elastische Datenbanken und Verschieben von Datenbanken zwischen Pools und aus einem Pool mit PowerShell) | Erstellt Pools für elastische Datenbanken, verschiebt Pooldatenbanken und ändert Computegrößen. |
|**Skalieren von Datenbanken in Azure SQL-Datenbank**||
| [Monitor and scale a single SQL database using the Azure CLI](scripts/monitor-and-scale-database-cli.md) (Überwachen und Skalieren einer einzelnen SQL-Datenbank mit Azure CLI) | Skaliert eine Datenbank in Azure SQL-Datenbank nach Abfrage der Größeninformationen für die Datenbank auf eine andere Computegröße. |
| [Scale an elastic pool in Azure SQL Database using the Azure CLI](scripts/scale-pool-cli.md) (Skalieren eines Pools für elastische Datenbanken in Azure SQL-Datenbank mit Azure CLI) | Skaliert einen Pool für elastische SQL-Datenbanken auf eine andere Computegröße. |
|**Konfigurieren von Georeplikation und Failover**||
| [Hinzufügen einer einzelnen Datenbank zu einer Failovergruppe](scripts/add-database-to-failover-group-cli.md)| Erstellt eine Datenbank und eine Failovergruppe, fügt die Datenbank zur Failovergruppe hinzu und testet das Failover auf den sekundären Server. |
| [Konfigurieren einer Failovergruppe für einen Pool für elastische Datenbanken](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Erstellt eine Datenbank, fügt die Datenbank zu einem Pool für elastische Datenbanken hinzu, fügt den Pool für elastische Datenbanken zur Failovergruppe hinzu und testet das Failover auf den sekundären Server. |
| [Konfiguration und Failover einer Einzeldatenbank mithilfe von aktiver Georeplikation](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Konfiguriert die aktive Georeplikation für eine Datenbank in Azure SQL-Datenbank und führt ein Failover zum sekundären Replikat aus. |
| [Konfiguration und Failover einer Pooldatenbank mithilfe von aktiver Georeplikation](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Konfiguriert die aktive Georeplikation für eine Datenbank in Azure SQL-Datenbank in einem Pool für elastische Datenbanken und führt dann ein Failover zum sekundären Replikat aus. |
| **Überwachung und Bedrohungserkennung** |
| [Configure SQL Database auditing and threat detection using PowerShell](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md) (Konfigurieren der Überwachung und Bedrohungserkennung für SQL-Datenbank mit PowerShell)| Konfiguriert Richtlinien zur Überwachung und Bedrohungserkennung für eine Datenbank in Azure SQL-Datenbank. |
| **Sichern, Wiederherstellen, Kopieren und Importieren einer Datenbank**||
| [Sichern einer Datenbank](../../sql-database/scripts/sql-database-backup-database-cli.md)| Sichert eine Datenbank in Azure SQL-Datenbank in eine Azure-Speichersicherung. |
| [Restore a SQL database using PowerShell (Wiederherstellen einer SQL-Datenbank mit PowerShell)](../../sql-database/scripts/sql-database-restore-database-cli.md)| Stellt eine Datenbank in Azure SQL-Datenbank aus einer georedundanten Sicherung wieder her und stellt eine gelöschte Datenbank gemäß der letzten Sicherung wieder her. |
| [Kopieren einer Datenbank auf einen neuen Server](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Erstellt eine Kopie einer vorhandenen Datenbank in Azure SQL-Datenbank auf einem neuen Server. |
| [Importieren einer Datenbank aus einer BACPAC-Datei](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Importiert eine Datenbank aus einer BACPAC-Datei in Azure SQL-Datenbank. |
|||

Erfahren Sie mehr über die [Azure CLI-API für Einzeldatenbanken](single-database-manage.md#the-azure-cli).

# <a name="azure-sql-managed-instance"></a>[Verwaltete Azure SQL-Datenbank-Instanz](#tab/managed-instance)

Die folgende Tabelle enthält Links zu Azure CLI-Skriptbeispielen für verwaltete Azure SQL-Instanzen.

|Bereich|BESCHREIBUNG|
|---|---|
| **Erstellen einer verwalteten SQL-Instanz**||
| [Erstellen einer verwalteten SQL-Instanz](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Erstellt eine verwaltete SQL-Instanz. |
| **Konfigurieren von Transparent Data Encryption (TDE)**||
| [Verwalten von Transparent Data Encryption in einer verwalteten SQL-Instanz mithilfe von Azure Key Vault](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Konfiguriert Transparent Data Encryption (TDE) in einer verwalteten SQL-Instanz mithilfe von Azure Key Vault und verschiedenen Schlüsselszenarien. |
|**Konfigurieren einer Failovergruppe**||
| [Konfigurieren einer Failovergruppe für eine verwaltete SQL-Instanz](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Erstellt zwei verwaltete SQL-Instanzen fügt sie einer Failovergruppe hinzu, und testet dann das Failover von der primären verwalteten SQL-Instanz auf die sekundäre verwaltete SQL-Instanz. |
|||

Weitere Beispiele zu verwalteten SQL-Instanzen finden Sie in den Artikeln zum [Erstellen](/archive/blogs/sqlserverstorageengine/create-azure-sql-managed-instance-using-azure-cli), [Aktualisieren](/archive/blogs/sqlserverstorageengine/modify-azure-sql-database-managed-instance-using-azure-cli), [Verschieben einer Datenbank](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance) und [Verwenden von Skripts](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

Erfahren Sie mehr über die [Azure CLI-API für verwaltete SQL-Instanzen](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances).

---