---
title: 'Schnellstart: Erstellen eines Workloadklassifizierers: T-SQL | Microsoft-Dokumentation'
description: Verwenden Sie T-SQL, um einen Workloadklassifizierer mit hoher Wichtigkeit zu erstellen.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 9fd11425d22daa87ec5bf2191a5864251ef86f77
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241247"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Schnellstart: Erstellen eines Workloadklassifizierers mit T-SQL

In dieser Schnellstartanleitung erstellen Sie in kurzer Zeit einen Workloadklassifizierer mit hoher Wichtigkeit für den CEO Ihrer Organisation. Mit diesem Workloadklassifizierer wird erreicht, dass CEO-Abfragen Vorrang vor anderen Abfragen in der Warteschlange erhalten, die eine weniger hohe Wichtigkeit aufweisen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

> [!NOTE]
> Wenn Sie eine SQL Data Warehouse-Instanz erstellen, wird unter Umständen auch ein neuer abrechenbarer Dienst erstellt.  Weitere Informationen finden Sie unter [SQL Data Warehouse – Preise](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Voraussetzungen

In dieser Schnellstartanleitung wird vorausgesetzt, dass Sie bereits über eine SQL Data Warehouse-Instanz und über CONTROL DATABASE-Berechtigungen verfügen. Wenn Sie ein Data Warehouse erstellen müssen, verwenden Sie die Anweisungen unter [Erstellen und Verbinden – Portal](create-data-warehouse-portal.md), um ein Data Warehouse namens **mySampleDataWarehouse** zu erstellen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-login-for-theceo"></a>Erstellen einer Anmeldung für TheCEO

Erstellen Sie eine SQL Server-Authentifizierungsanmeldung in der `master`-Datenbank, indem Sie [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) für „TheCEO“ verwenden.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Benutzer erstellen

[Erstellen des Benutzers](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest) „TheCEO“ in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Erstellen eines Workloadklassifizierers

Erstellen eines [Workloadklassifizierers](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) für TheCEO mit hoher Wichtigkeit

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Anzeigen von vorhandenen Klassifizierern

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Ihnen werden Gebühren für Ihre Data Warehouse-Einheiten und die in Ihrem Data Warehouse gespeicherten Daten berechnet. Diese Compute- und Speicherressourcen werden separat in Rechnung gestellt.

- Falls Sie die Daten im Speicher belassen möchten, können Sie Computeressourcen anhalten, wenn Sie das Data Warehouse nicht verwenden. Wenn Sie Computeressourcen anhalten, werden Ihnen nur die Datenspeichergebühren in Rechnung gestellt. Sie können die Computeressourcen fortsetzen, wenn Sie mit den Daten arbeiten möchten.
- Wenn zukünftig keine Gebühren anfallen sollen, können Sie das Data Warehouse löschen.

Führen Sie die folgenden Schritte aus, um Ressourcen zu bereinigen.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und wählen Sie Ihre Data Warehouse-Instanz aus.

    ![Bereinigen von Ressourcen](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Wählen Sie zum Anhalten von Computeressourcen die Schaltfläche **Anhalten**. Wenn das Data Warehouse angehalten ist, wird die Schaltfläche **Starten** angezeigt.  Wählen Sie zum Fortsetzen der Computeressourcen die Option **Starten**.

3. Wählen Sie **Löschen**, wenn Sie die Data Warehouse-Instanz entfernen möchten, damit keine Gebühren für Compute- oder Speicherressourcen anfallen.

4. Wählen Sie zum Entfernen des von Ihnen erstellten SQL-Servers die Option **mynewserver-20180430.database.windows.net** (siehe Abbildung oben) und dann **Löschen**.  Seien Sie bei diesem Löschvorgang vorsichtig, da beim Löschen des Servers auch alle Datenbanken gelöscht werden, die dem Server zugewiesen sind.

5. Wählen Sie zum Entfernen der Ressourcengruppe die Option **myResourceGroup** und dann **Ressourcengruppe löschen**.

## <a name="next-steps"></a>Nächste Schritte

- Sie haben nun einen Workloadklassifizierer erstellt. Führen Sie einige Abfragen als TheCEO durch, um zu ermitteln, welche Leistung erzielt wird. Sie können die Abfragen und die zugewiesene Wichtigkeit unter [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) anzeigen.
- Weitere Informationen zur Workloadverwaltung für Azure SQL Data Warehouse finden Sie unter [Workloadpriorität für SQL Data Warehouse](sql-data-warehouse-workload-importance.md) und [SQL Data Warehouse-Workloadklassifizierung](sql-data-warehouse-workload-classification.md).
- Sehen Sie sich die Anleitungsartikel zum [Konfigurieren der Workloadpriorität](sql-data-warehouse-how-to-configure-workload-importance.md) und zum [Verwalten und Überwachen der Workloadpriorität](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) an.
