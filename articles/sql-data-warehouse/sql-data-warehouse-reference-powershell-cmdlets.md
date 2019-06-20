---
title: PowerShell-Cmdlets für Azure SQL Data Warehouse
description: Hier finden Sie die wichtigsten PowerShell-Cmdlets für Azure SQL Data Warehouse, einschließlich der Cmdlets zum Anhalten und Fortsetzen einer Datenbank.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 095e66c6c5f75a27b1f0231dfe8cabfd4d741d18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205178"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>PowerShell-Cmdlets und REST-APIs für SQL Data Warehouse
Viele SQL Data Warehouse-Verwaltungsaufgaben können entweder mithilfe von Azure PowerShell-Cmdlets oder REST-APIs verwaltet werden.  Die folgenden Beispiele zeigen einen Einblick, wie PowerShell-Befehle verwendet werden, um häufige Aufgaben in SQL Data Warehouse zu automatisieren.  Einige gute REST-Beispiele finden Sie im Artikel [Verwalten der Skalierbarkeit mit REST][Manage scalability with REST].

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Erste Schritte mit Azure PowerShell-Cmdlets
1. Öffnen Sie Windows PowerShell.
2. Zum Anmelden bei Azure Resource Manager und Auswählen Ihres Abonnements führen Sie bei der PowerShell-Eingabeaufforderung die folgenden Befehle aus:
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Beispiel für das Anhalten von Azure SQL Data Warehouse
Halten Sie eine Datenbank mit dem Namen „Database02“ an, die auf einem Server mit dem Namen „Server01“ gehostet wird.  Der Server befindet sich in einer Azure-Ressourcengruppe namens „ResourceGroup1“.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Variation: Dieses Beispiel leitet das abgerufene Objekt an [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase] weiter.  Dadurch wird die Datenbank angehalten. Der letzte Befehl zeigt die Ergebnisse an.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Beispiel für das Starten von Azure SQL Data Warehouse
Setzen Sie den Betrieb der auf dem Server „Server01“ gehosteten Datenbank „Database02“ fort. Der Server befindet sich in einer Ressourcengruppe namens „ResourceGroup1“.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Abweichung: Eine Datenbank „Database02“ von einem Server „Server01“, der sich in einer Ressourcengruppe „ResourceGroup1“ befindet, wird abgerufen. Das abgerufene Objekt wird an [Resume-AzSqlDatabase][Resume-AzSqlDatabase] weitergeleitet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Hinweis: Wenn Ihr Server „foo.database.windows.net“ heißt, verwenden Sie in den PowerShell-Cmdlets für „-ServerName“ den Namen „foo“.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Andere unterstützte PowerShell-Cmdlets
Diese PowerShell-Cmdlets werden für Azure SQL Data Warehouse unterstützt.

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoint][Get-AzSqlDatabaseRestorePoint]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Resume-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>Nächste Schritte
Weitere PowerShell-Beispiele finden Sie unter:

* [Erstellen von SQL Data Warehouse mithilfe von PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Datenbankwiederherstellung][Database restore]

Weitere Aufgaben, die mit PowerShell automatisiert werden können, finden Sie unter [Azure SQL-Datenbank-Cmdlets][Azure SQL Database Cmdlets]. Beachten Sie, dass nicht alle Azure SQL-Datenbank-Cmdlets für Azure SQL Data Warehouse unterstützt werden.  Eine Liste mit Aufgaben, die mit REST automatisiert werden können, finden Sie unter [Vorgänge für Azure SQL-Datenbank][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/az.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
