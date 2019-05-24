---
title: Exportieren einer einzelnen oder in einem Pool enthaltenen Azure SQL-Datenbank in eine BACPAC-Datei | Microsoft-Dokumentation
description: Exportieren einer Azure SQL-Datenbank in eine BACPAC-Datei mithilfe des Azure-Portals
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: c87979760730cbe8f57d8f65463c94d08888aa2b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762752"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportieren einer Azure SQL-Datenbank in eine BACPAC-Datei

Wenn Sie eine Datenbank zur Archivierung oder zum Verschieben auf eine andere Plattform exportieren müssen, können Sie das Datenbankschema und die Daten in eine [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-Datei exportieren. Eine BACPAC-Datei ist eine ZIP-Datei mit der Erweiterung BACPAC. Sie enthält die Metadaten und Daten aus einer SQL Server-Datenbank. Eine BACPAC-Datei kann in Azure Blob Storage oder im lokalen Speicher an einem lokalen Standort gespeichert werden und dann zurück in Azure SQL-Datenbank oder in eine lokale Installation von SQL Server importiert werden.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Überlegungen beim Exportieren einer Azure SQL-Datenbank

- Damit ein Export hinsichtlich der Transaktionen konsistent ist, müssen Sie entweder sicherstellen, dass während des Exports keine Schreibaktivitäten erfolgen, oder den Export aus einer [in Hinblick auf Transaktionen konsistenten Kopie](sql-database-copy.md) Ihrer Azure SQL-Datenbank durchführen.
- Beim Exportieren in Blobspeicher beträgt die maximale Größe einer BACPAC-Datei 200 GB. Führen Sie zum Archivieren einer größeren BACPAC-Datei einen Export in lokalen Speicher durch.
- Das Exportieren einer BACPAC-Datei in Azure Storage Premium mit den in diesem Artikel erläuterten Methoden wird nicht unterstützt.
- Speicher hinter einer Firewall wird derzeit nicht unterstützt.
- Falls der Exportvorgang aus Azure SQL-Datenbank länger als 20 Stunden dauert, wird er unter Umständen abgebrochen. Um die Leistung während des Exports zu erhöhen, können Sie Folgendes tun:

  - Erhöhen Sie vorübergehend Ihre Computegröße.
  - Verhindern Sie jegliche Lese- und Schreibaktivitäten während des Exports.
  - Verwenden Sie einen [gruppierten Index](https://msdn.microsoft.com/library/ms190457.aspx) mit Werten ungleich NULL in allen großen Tabellen. Ohne gruppierte Indizes schlägt ein Export, der länger als sechs bis zwölf Stunden dauert, ggf. fehl. Dies liegt daran, dass der Exportdienst einen Tabellenscan durchführen muss, um die gesamte Tabelle zu exportieren. Eine gute Möglichkeit, um zu ermitteln, ob Ihre Tabellen für den Export optimiert sind, besteht darin, **DBCC SHOW_STATISTICS** auszuführen und sicherzustellen, dass *RANGE_HI_KEY* nicht NULL ist und der Wert eine gute Verteilung aufweist. Weitere Informationen finden Sie unter [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPAC-Dateien eignen sich nicht für Backup- und Wiederherstellungsvorgänge. Azure SQL-Datenbank erstellt für jede Benutzerdatenbank automatisch Sicherungen. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md) und [Sicherungen von SQL-Datenbanken](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportieren in eine BACPAC-Datei mithilfe des Azure-Portals

> [!NOTE]
> Für eine [verwaltete Instanz](sql-database-managed-instance.md) wird das Exportieren einer Datenbank in eine BACPAC-Datei über das Azure-Portal derzeit nicht unterstützt. Verwenden Sie zum Exportieren einer verwalteten Instanz in eine BACPAC-Datei SQL Server Management Studio oder SQLPackage.

1. Um eine Datenbank über das [Azure-Portal](https://portal.azure.com) zu exportieren, öffnen Sie die Seite für Ihre Datenbank, und klicken Sie auf der Symbolleiste auf **Exportieren**.

   ![Datenbankexport](./media/sql-database-export/database-export1.png)

2. Geben Sie den Namen der BACPAC-Datei an, und wählen Sie ein vorhandenes Azure-Speicherkonto und einen Container für den Export aus. Geben Sie anschließend die entsprechenden Anmeldeinformationen für den Zugriff auf die Quelldatenbank an.

    ![Datenbankexport](./media/sql-database-export/database-export2.png)

3. Klicken Sie auf **OK**.

4. Um den Status des Exportvorgangs zu überwachen, öffnen Sie die Seite für den SQL-Datenbank-Server mit der zu exportierenden Datenbank. Klicken Sie unter **Einstellungen** auf **Import-/Exportverlauf**.

   ![Exportverlauf](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportieren in eine BACPAC-Datei mit dem Hilfsprogramm „SQLPackage“

Informationen zum Exportieren einer SQL-Datenbank mit dem Befehlszeilenprogramm [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) finden Sie unter [Exportparameter und -eigenschaften](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Das SQLPackage-Hilfsprogramm wird mit den neuesten Versionen von [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) und [SQL Server Data Tools für Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) ausgeliefert. Alternativ dazu können Sie die neueste Version von [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direkt aus dem Microsoft Download Center herunterladen.

Die Verwendung des SQLPackage-Hilfsprogramms wird aus Gründen der Skalierbarkeit und Leistung für die meisten Produktionsumgebungen empfohlen. Einen Blogbeitrag des SQL Server-Kundenberatungsteams zur Migration mithilfe von BACPAC-Dateien finden Sie unter [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien).

In diesem Beispiel wird gezeigt, wie eine Datenbank mithilfe von „SqlPackage.exe“ mit universeller Active Directory-Authentifizierung exportiert wird:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportieren in eine BACPAC-Datei mit SQL Server Management Studio (SSMS)

Die neuesten Versionen von SQL Server Management Studio enthalten einen Assistenten zum Exportieren einer Azure SQL-Datenbank in eine BACPAC-Datei. Weitere Informationen finden Sie unter [Exportieren einer Datenebenenanwendung](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportieren in eine BACPAC-Datei mit PowerShell

> [!NOTE]
> Für eine [verwaltete Instanz](sql-database-managed-instance.md) wird das Exportieren einer Datenbank in eine BACPAC-Datei mit Azure PowerShell derzeit nicht unterstützt. Verwenden Sie zum Exportieren einer verwalteten Instanz in eine BACPAC-Datei SQL Server Management Studio oder SQLPackage.

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

- Informationen zur langfristigen Archivierung von Einzel- und Pooldatenbanken als Alternative zum Exportieren einer Datenbank für Archivzwecke finden Sie unter [Langfristige Archivierung von Sicherungen](sql-database-long-term-retention.md). Sie können SQL-Agent-Aufträge verwenden, um [Kopiesicherungen von Datenbanken](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) als Alternative zur langfristigen Sicherungsaufbewahrung zu planen.
- Einen Blogbeitrag des SQL Server-Kundenberatungsteams zur Migration mithilfe von BACPAC-Dateien finden Sie unter [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrieren von SQL Server zu Azure SQL-Datenbank mithilfe von BACPAC-Dateien).
- Informationen zum Importieren einer BACPAC-Datei in eine SQL Server-Datenbank finden Sie unter [Importieren einer BACPAC-Datei in eine SQL Server-Datenbank](https://msdn.microsoft.com/library/hh710052.aspx).
- Informationen zum Exportieren einer BACPAC-Datei aus einer SQL Server-Datenbank finden Sie unter [Exportieren einer Datenschichtanwendung](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).
- Informationen zur Verwendung des Datenmigrationsdienstes für die Migration einer Datenbank finden Sie unter [Offlinemigration von SQL Server zu Azure SQL-Datenbank mit DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Wenn der Export aus SQL Server als Vorbereitung auf die Migration zu Azure SQL-Datenbank erfolgt, finden Sie unter [Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank](sql-database-single-database-migrate.md) nützliche Hinweise.
- Informationen zum sicheren Verwalten und Freigeben von Speicherschlüsseln und SAS finden Sie im [Azure Storage-Sicherheitsleitfaden](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
