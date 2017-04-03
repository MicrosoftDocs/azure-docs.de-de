---
title: Exportieren einer Azure SQL-Datenbank in eine BACPAC-Datei | Microsoft-Dokumentation
description: Exportieren einer Azure SQL-Datenbank in eine BACPAC-Datei mithilfe des Azure-Portals
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 7b96e8b144f329819596e3ff63e6febbbcc1ffb0
ms.lasthandoff: 03/18/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file"></a>Exportieren einer Azure SQL- oder SQL Server-Datenbank in eine BACPAC-Datei

In diesem Artikel wird das Exportieren Ihrer Azure SQL-Datenbank oder einer SQL Server-Datenbank in eine BACPAC-Datei erläutert. 

> [!IMPORTANT]
> Der automatisierte Export von Azure SQL-Datenbanken ist jetzt als Vorschau verfügbar und wird am 1. März 2017 eingestellt. Seit dem 1. Dezember 2016 ist das Konfigurieren des automatisierten Exports für SQL-Datenbanken nicht mehr möglich. Alle vorhandenen Aufträge für automatisierten Export werden bis zum 1. März 2017 weiterhin ausgeführt. Seit dem 1. Dezember 2016 können Sie die [langfristige Sicherungsaufbewahrung](sql-database-long-term-retention.md) oder [Azure Automation](../automation/automation-intro.md) zum regelmäßigen Archivieren von SQL-Datenbanken mithilfe von PowerShell nach einem Zeitplan Ihrer Wahl verwenden. Als Beispielskript können Sie das [Beispielskript von GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) herunterladen. 
>

## <a name="overview"></a>Übersicht

Wenn Sie eine Datenbank zur Archivierung oder zum Verschieben auf eine andere Plattform exportieren müssen, können Sie das Datenbankschema und die Daten in eine BACPAC-Datei exportieren. Eine BACPAC-Datei ist einfach eine ZIP-Datei mit der Erweiterung BACPAC. Eine BACPAC-Datei kann später im Azure-Blobspeicher oder im lokalen Speicher an einem lokalen Standort gespeichert werden und dann zurück in die Azure SQL-Datenbank oder in eine lokale SQL-Serverinstallation importiert werden. 

* Sie können Ihre Azure SQL-Datenbankdatei über das [Azure-Portal](sql-database-export-portal.md), [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) oder [SQL Server Management Studio](sql-database-export-ssms.md) in eine BACPAC-Datei exportieren.
* Sie können eine SQL Server-Datenbank über [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) oder [SQL Server Management Studio](sql-database-export-ssms.md) exportieren.

> [!IMPORTANT]
> Wenn der Export aus SQL Server als Vorbereitung auf die Migration zu Azure SQL-Datenbank erfolgt, finden Sie unter [Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank](sql-database-cloud-migrate.md) nützliche Hinweise.
> 

## <a name="considerations"></a>Überlegungen

* Damit ein Export hinsichtlich der Transaktionen konsistent ist, müssen Sie entweder sicherstellen, dass während des Exports keine Schreibaktivitäten erfolgen, oder den Export aus einer [in Hinblick auf Transaktionen konsistenten Kopie](sql-database-copy.md) Ihrer Azure SQL-Datenbank durchführen.
* Beim Exportieren in Blobspeicher beträgt die maximale Größe einer BACPAC-Datei 200 GB. Führen Sie zum Archivieren einer größeren BACPAC-Datei einen Export in lokalen Speicher durch.
* Das Exportieren einer BACPAC-Datei in Azure Storage Premium mit den in diesem Artikel erläuterten Methoden wird nicht unterstützt.
* Falls der Exportvorgang aus Azure SQL-Datenbank länger als 20 Stunden dauert, wird er unter Umständen abgebrochen. Um die Leistung während des Exports zu erhöhen, können Sie Folgendes tun:
  * Erhöhen Sie vorübergehend Ihr Servicelevel.
  * Verhindern Sie jegliche Lese- und Schreibaktivitäten während des Exports.
  * Verwenden Sie einen [gruppierten Index](https://msdn.microsoft.com/library/ms190457.aspx) mit Werten ungleich NULL in allen großen Tabellen. Ohne gruppierte Indizes schlägt ein Export, der länger als sechs bis zwölf Stunden dauert, ggf. fehl. Dies liegt daran, dass der Exportdienst einen Tabellenscan durchführen muss, um die gesamte Tabelle zu exportieren. Eine gute Möglichkeit, um zu ermitteln, ob Ihre Tabellen für den Export optimiert sind, besteht darin, **DBCC SHOW_STATISTICS** auszuführen und sicherzustellen, dass *RANGE_HI_KEY* nicht NULL ist und der Wert eine gute Verteilung aufweist. Weitere Informationen finden Sie unter [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPAC-Dateien eignen sich nicht für Backup- und Wiederherstellungsvorgänge. Azure SQL-Datenbank erstellt für jede Benutzerdatenbank automatisch Sicherungen. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md) und [Sicherungen von SQL-Datenbanken](sql-database-automated-backups.md).  
> 


## <a name="next-steps"></a>Nächste Schritte

* Eine Erläuterung des gesamten Migrationsprozesses von SQL Server-Datenbanken finden Sie unter [Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank](sql-database-cloud-migrate.md).
* Einen Überblick über das Kopieren einer Datenbank innerhalb von Azure finden Sie unter [Kopieren einer Azure SQL-Datenbank](sql-database-copy.md).
* Sie können Ihre Azure SQL-Datenbank innerhalb von Azure über das [Azure-Portal](sql-database-copy-portal.md), [PowerShell](sql-database-copy-powershell.md) oder [Transact-SQL](sql-database-copy-transact-sql.md) kopieren. 

