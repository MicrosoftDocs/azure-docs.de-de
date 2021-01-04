---
title: Exportieren aus Azure SQL-Datenbank in eine BACPAC-Datei (Azure-Portal)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Exportieren einer Datenbank in eine BACPAC-Datei mithilfe des Azure-Portals
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.topic: how-to
ms.openlocfilehash: 7dc6cd580687544226b61a29ca9ccf2d1b8dff42
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671536"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>Exportieren in eine BACPAC-Datei – Azure SQL-Datenbank und Azure SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Wenn Sie eine Datenbank zur Archivierung oder zum Verschieben auf eine andere Plattform exportieren müssen, können Sie das Datenbankschema und die Daten in eine [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#Anchor_4)-Datei exportieren. Eine BACPAC-Datei ist eine ZIP-Datei mit der Erweiterung BACPAC. Sie enthält die Metadaten und Daten aus der Datenbank. Eine BACPAC-Datei kann in Azure Blob Storage oder im lokalen Speicher an einem lokalen Standort gespeichert werden und dann zurück in Azure SQL-Datenbank, Azure SQL Managed Instance oder in eine SQL Server-Instanz importiert werden.

## <a name="considerations"></a>Überlegungen

- Damit ein Export hinsichtlich der Transaktionen konsistent ist, müssen Sie sicherstellen, dass entweder während des Exports keine Schreibaktivitäten stattfinden, oder dass Sie den Export aus einer [transaktionskonsistenten Kopie](database-copy.md) Ihrer Datenbank durchführen.
- Beim Exportieren in Blobspeicher beträgt die maximale Größe einer BACPAC-Datei 200 GB. Führen Sie zum Archivieren einer größeren BACPAC-Datei einen Export in lokalen Speicher durch.
- Das Exportieren einer BACPAC-Datei in Azure Storage Premium mit den in diesem Artikel erläuterten Methoden wird nicht unterstützt.
- Speicher hinter einer Firewall wird derzeit nicht unterstützt.
- Falls der Exportvorgang länger als 20 Stunden dauert, wird er unter Umständen abgebrochen. Um die Leistung während des Exports zu erhöhen, können Sie Folgendes tun:

  - Erhöhen Sie vorübergehend Ihre Computegröße.
  - Verhindern Sie jegliche Lese- und Schreibaktivitäten während des Exports.
  - Verwenden Sie einen [gruppierten Index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) mit Werten ungleich NULL in allen großen Tabellen. Ohne gruppierte Indizes schlägt ein Export, der länger als sechs bis zwölf Stunden dauert, ggf. fehl. Dies liegt daran, dass der Exportdienst einen Tabellenscan durchführen muss, um die gesamte Tabelle zu exportieren. Eine gute Möglichkeit, um zu ermitteln, ob Ihre Tabellen für den Export optimiert sind, besteht darin, **DBCC SHOW_STATISTICS** auszuführen und sicherzustellen, dass *RANGE_HI_KEY* nicht NULL ist und der Wert eine gute Verteilung aufweist. Weitere Informationen finden Sie unter [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql).

> [!NOTE]
> BACPAC-Dateien eignen sich nicht für Backup- und Wiederherstellungsvorgänge. In Azure werden automatisch Sicherungen für jede Benutzerdatenbank erstellt. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md) und [Sicherungen von SQL-Datenbanken](automated-backups-overview.md).

## <a name="the-azure-portal"></a>Das Azure-Portal

Das Exportieren einer BACPAC-Datei einer Datenbank aus [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) mithilfe des Azure-Portals wird derzeit nicht unterstützt. Verwenden Sie stattdessen SQL Server Management Studio oder SQLPackage.

> [!NOTE]
> Computer, die über das Azure-Portal oder PowerShell übermittelte Import-/Exportanforderungen verarbeiten, müssen die BACPAC-Datei sowie die von Data-Tier Application Framework (DacFX) generierten temporären Dateien speichern. Der erforderliche Speicherplatz variiert bei Datenbanken mit derselben Größe enorm. Der erforderliche Speicherplatz kann bis zum Dreifachen der Größe der Datenbank betragen. Der lokale Speicherplatz von Computern, die die Import-/Exportanforderung ausführen, beträgt nur 450 GB. Daher kann bei einigen Anforderungen der Fehler `There is not enough space on the disk` auftreten. In diesem Fall besteht die Problemumgehung darin, „sqlpackage.exe“ auf einem Computer mit ausreichend Speicherplatz auszuführen. Es wird empfohlen, [SqlPackage](#sqlpackage-utility) zum Importieren oder Exportieren von Datenbanken zu verwenden, die größer als 150GB sind, um dieses Problem zu vermeiden.

1. Um eine Datenbank über das [Azure-Portal](https://portal.azure.com) zu exportieren, öffnen Sie die Seite für Ihre Datenbank, und klicken Sie auf der Symbolleiste auf **Exportieren** .

   ![Screenshot mit hervorgehobener Schaltfläche „Exportieren“](./media/database-export/database-export1.png)

2. Geben Sie den Namen der BACPAC-Datei an, und wählen Sie ein vorhandenes Azure-Speicherkonto und einen Container für den Export aus. Geben Sie anschließend die entsprechenden Anmeldeinformationen für den Zugriff auf die Quelldatenbank an. Eine SQL **Server-Administratoranmeldung** ist hier auch dann erforderlich, wenn Sie der Azure-Administrator sind, weil ein Azure-Administrator keine Administratorberechtigungen in Azure SQL-Datenbank oder Azure SQL Managed Instance hat.

    ![Datenbankexport](./media/database-export/database-export2.png)

3. Klicken Sie auf **OK** .

4. Um den Status des Exportvorgangs zu überwachen, öffnen Sie die Seite für den Server mit der zu exportierenden Datenbank. Klicken Sie unter **Einstellungen** auf **Import-/Exportverlauf** .

   ![Exportverlauf](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>SqlPackage-Hilfsprogramm

Informationen zum Exportieren einer Datenbank in SQL-Datenbank mit dem Befehlszeilen-Hilfsprogramm [SqlPackage](/sql/tools/sqlpackage) finden Sie unter [Exportparameter und -eigenschaften](/sql/tools/sqlpackage#export-parameters-and-properties). Das SQLPackage-Hilfsprogramm wird mit den neuesten Versionen von [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) und [SQL Server Data Tools für Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt) ausgeliefert. Alternativ dazu können Sie die neueste Version von [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direkt aus dem Microsoft Download Center herunterladen.

Die Verwendung des SQLPackage-Hilfsprogramms wird aus Gründen der Skalierbarkeit und Leistung für die meisten Produktionsumgebungen empfohlen. Einen Blogbeitrag des SQL Server-Kundenberatungsteams zur Migration mithilfe von BACPAC-Dateien finden Sie unter [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien).

In diesem Beispiel wird gezeigt, wie eine Datenbank mithilfe von „SqlPackage.exe“ mit universeller Active Directory-Authentifizierung exportiert wird:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Die neuesten Versionen von SQL Server Management Studio enthalten einen Assistenten zum Exportieren einer Datenbank in Azure SQL-Datenbank oder SQL Managed Instance in eine BACPAC-Datei. Weitere Informationen finden Sie unter [Exportieren einer Datenebenenanwendung](/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

> [!NOTE]
> Für [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) wird das Exportieren einer Datenbank in eine BACPAC-Datei mit Azure PowerShell derzeit nicht unterstützt. Verwenden Sie zum Exportieren einer verwalteten Instanz in eine BACPAC-Datei SQL Server Management Studio oder SQLPackage.

Verwenden Sie das Cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport), um eine Anforderung für einen Datenbankexport beim Azure SQL-Datenbank-Dienst einzureichen. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Exportvorgang abgeschlossen ist.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Zum Überprüfen des Status der Exportanforderung verwenden Sie das Cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus). Wenn Sie dieses Cmdlet direkt nach der Anforderung ausführen, wird in der Regel **Status: InProgress** zurückgegeben. Wenn **Status: Succeeded** angezeigt wird, ist der Export abgeschlossen.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Langzeitaufbewahrung von Sicherungen von Singletons und Pooldatenbanken als Alternative zum Exportieren einer Datenbank für Archivzwecke finden Sie unter [Langzeitaufbewahrung von Sicherungen](long-term-retention-overview.md). Sie können SQL-Agent-Aufträge verwenden, um [Kopiesicherungen von Datenbanken](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) als Alternative zur langfristigen Sicherungsaufbewahrung zu planen.
- Einen Blogbeitrag des SQL Server-Kundenberatungsteams zur Migration mithilfe von BACPAC-Dateien finden Sie unter [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien).
- Informationen zum Importieren einer BACPAC-Datei in eine SQL Server-Datenbank finden Sie unter [Importieren einer BACPAC-Datei in eine SQL Server-Datenbank](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database).
- Informationen zum Exportieren einer BACPAC-Datei aus einer SQL Server-Datenbank finden Sie unter [Exportieren einer Datenschichtanwendung](/sql/relational-databases/data-tier-applications/export-a-data-tier-application).
- Informationen zur Verwendung von Data Migration Service für die Migration einer Datenbank finden Sie unter [Offlinemigration von SQL Server zu Azure SQL-Datenbank mit DMS](../../dms/tutorial-sql-server-to-azure-sql.md).
- Wenn der Export aus SQL Server als Vorbereitung auf die Migration zu Azure SQL-Datenbank erfolgt, finden Sie unter [Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank](migrate-to-database-from-sql-server.md) nützliche Hinweise.
- Informationen zum sicheren Verwalten und Freigeben von Speicherschlüsseln und SAS finden Sie im [Azure Storage-Sicherheitsleitfaden](../../storage/blobs/security-recommendations.md).