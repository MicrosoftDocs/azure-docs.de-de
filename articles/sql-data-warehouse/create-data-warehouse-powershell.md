---
title: 'Schnellstart: Erstellen eines Azure SQL Data Warehouse: Azure PowerShell | Microsoft-Dokumentation'
description: Erstellen Sie schnell einen logischen SQL-Datenbankserver, eine Firewallregel auf Serverebene und ein Data Warehouse mit Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: cdaa932a4996d559b5974e39e20cf75acd6571c5
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873783"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Schnellstart: Erstellen und Abfragen eines Azure SQL Data Warehouse mit Azure PowerShell

Erstellen Sie schnell ein Azure SQL Data Warehouse mit Azure PowerShell.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

> [!NOTE]
> Wenn Sie eine SQL Data Warehouse-Instanz erstellen, wird unter Umständen auch ein neuer abrechenbarer Dienst erstellt.  Weitere Informationen finden Sie unter [SQL Data Warehouse – Preise](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

Verwenden Sie [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription), um zu ermitteln, welches Abonnement Sie verwenden.

```powershell
Get-AzSubscription
```

Wenn Sie ein anderes Abonnement als das Standardabonnement verwenden müssen, führen Sie [Set-AzContext](/powershell/module/az.accounts/set-azcontext) aus.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Erstellen von Variablen

Definieren Sie Variablen zur Verwendung in den Skripts dieser Schnellstartanleitung.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort `westeurope` erstellt.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Erstellen eines logischen Servers

Erstellen Sie mit dem Befehl [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) einen [logischen Azure SQL-Server](../sql-database/sql-database-logical-servers.md). Ein logischer Server enthält eine Gruppe von Datenbanken, die als Gruppe verwaltet werden. Im folgenden Beispiel wird in Ihrer Ressourcengruppe ein zufällig benannter Server mit einem Administratorbenutzer namens `ServerAdmin` und dem Kennwort `ChangeYourAdminPassword1` erstellt. Ersetzen Sie ggf. diese vordefinierten Werte.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurieren einer Serverfirewallregel

Erstellen Sie mit dem Befehl [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) eine [Firewallregel für Azure SQL auf Serverebene](../sql-database/sql-database-firewall-configure.md). Eine Firewallregel auf Serverebene lässt zu, dass eine externe Anwendung wie SQL Server Management Studio oder das SQLCMD-Hilfsprogramm über die Firewall des SQL Data Warehouse-Diensts eine Verbindung mit einem SQL Data Warehouse herstellt. Im folgenden Beispiel wird die Firewall nur für andere Azure-Ressourcen geöffnet. Ändern Sie die IP-Adresse in eine für Ihre Umgebung geeignete Adresse, um die externe Konnektivität zu ermöglichen. Verwenden Sie 0.0.0.0 als IP-Startadresse und 255.255.255.255 als Endadresse, wenn Sie alle IP-Adressen öffnen möchten.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Die SQL-Datenbank und SQL Data Warehouse kommunizieren über Port 1433. Wenn Sie versuchen, eine Verbindung aus einem Unternehmensnetzwerk heraus herzustellen, wird der ausgehende Datenverkehr über Port 1433 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Azure SQL-Server herstellen, wenn Ihre IT-Abteilung Port 1433 öffnet.
>


## <a name="create-a-data-warehouse"></a>Erstellen eines Data Warehouse
Dieses Beispiel erstellt ein Data Warehouse mit den zuvor definierten Variablen.  Das Dienstziel wird als DW100c angegeben. Dies ist ein kostengünstiger Ausgangspunkt für Ihr Data Warehouse. 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Erforderliche Parameter:

* **RequestedServiceObjectiveName**: Die Menge an [Data Warehouse-Einheiten](what-is-a-data-warehouse-unit-dwu-cdwu.md), die Sie anfordern. Durch das Erhöhen dieses Werts steigen die Computekosten. Eine Liste der unterstützten Werte finden Sie unter [Grenzwerte für Arbeitsspeicher und Parallelität](memory-and-concurrency-limits.md).
* **DatabaseName**: Der Name des SQL Data Warehouse, das Sie erstellen.
* **ServerName**: Der Name des Servers, den Sie für die Erstellung verwenden.
* **ResourceGroupName**: Die Ressourcengruppe, die Sie verwenden. Verwenden Sie zum Abrufen der in Ihrem Abonnement verfügbaren Ressourcengruppen das Cmdlet „Get-AzureResource“.
* **Edition**: Muss für die Erstellung einer SQL Data Warehouse-Instanz auf „DataWarehouse“ festgelegt werden.

Optionale Parameter:

- **CollationName**: Ohne Angabe wird die Standardsortierung „SQL_Latin1_General_CP1_CI_AS“ verwendet. Die Sortierung kann für eine Datenbank nicht geändert werden.
- **MaxSizeBytes**: Die maximale Größe einer Datenbank beträgt standardmäßig 240 TB. Durch die maximale Größe werden die Rowstore-Daten begrenzt. Die Speichermenge für Spaltendaten ist nicht begrenzt.

Weitere Informationen zu den Parameteroptionen finden Sie unter [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstarttutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. 

> [!TIP]
> Wenn Sie mit weiteren Schnellstart-Tutorials fortfahren möchten, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen. Falls Sie nicht fortfahren möchten, können Sie die folgenden Schritte ausführen, um alle erstellten Ressourcen dieses Schnellstarts im Azure-Portal zu löschen.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben jetzt ein Data Warehouse sowie eine Firewallregel erstellt, diese mit Ihrem Data Warehouse verbunden und einige Abfragen ausgeführt. Weitere Informationen zu Azure SQL Data Warehouse erhalten Sie im Tutorial zum Laden von Daten.
> [!div class="nextstepaction"]
>[Laden von Daten in ein SQL-Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
