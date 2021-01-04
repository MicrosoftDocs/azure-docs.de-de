---
title: 'Schnellstart: Erstellen eines dedizierten SQL-Pools (vormals SQL DW) mit Azure PowerShell'
description: Erstellen Sie mithilfe von Azure PowerShell schnell einen dedizierten SQL-Pool (vormals SQL DW) mit einer Firewallregel auf Serverebene.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse    , devx-track-azurepowershell
ms.openlocfilehash: 0ce94b62d67048896cdf7355043ec2dde7f2df79
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456585"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Schnellstart: Erstellen eines dedizierten SQL-Pools (vormals SQL DW) mit Azure PowerShell

Erstellen eines dedizierten SQL-Pools (vormals SQL DW) in Azure Synapse Analytics mithilfe von Azure PowerShell

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

> [!IMPORTANT]
> Die Erstellung eines dedizierten SQL-Pools (vormals SQL DW) führt möglicherweise zu einem neuen abrechenbaren Dienst.  Weitere Informationen finden Sie unter [Azure Synapse Analytics – Preise](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

Verwenden Sie [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), um zu ermitteln, welches Abonnement Sie verwenden.

```powershell
Get-AzSubscription
```

Falls Sie ein anderes Abonnement als das Standardabonnement verwenden müssen, führen Sie [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) aus.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Erstellen von Variablen

Definieren Sie Variablen zur Verwendung in den Skripts dieser Schnellstartanleitung.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) eine [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Eine Ressourcengruppe ist ein Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort `westeurope` erstellt.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Erstellen eines Servers

Erstellen Sie mit dem Befehl [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) einen [logischen SQL-Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Ein Server enthält eine Gruppe von Datenbanken, die als Gruppe verwaltet werden. Im folgenden Beispiel wird in Ihrer Ressourcengruppe ein zufällig benannter Server mit einem Administratorbenutzer namens `ServerAdmin` und dem Kennwort `ChangeYourAdminPassword1` erstellt. Ersetzen Sie ggf. diese vordefinierten Werte.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurieren einer Firewallregel auf Serverebene

Erstellen Sie mit dem Befehl [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) eine [Firewallregel auf Serverebene](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Eine Firewallregel auf Serverebene ermöglicht es einer externen Anwendung wie SQL Server Management Studio oder dem SQLCMD-Hilfsprogramm, über die Firewall des Diensts für dedizierte SQL-Pools eine Verbindung mit einem dedizierten SQL-Pool (vormals SQL DW) herzustellen.

Im folgenden Beispiel wird die Firewall nur für andere Azure-Ressourcen geöffnet. Ändern Sie die IP-Adresse in eine für Ihre Umgebung geeignete Adresse, um die externe Konnektivität zu ermöglichen. Verwenden Sie 0.0.0.0 als IP-Startadresse und 255.255.255.255 als Endadresse, wenn Sie alle IP-Adressen öffnen möchten.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL-Endpunkte kommunizieren über Port 1433. Wenn Sie versuchen, eine Verbindung aus einem Unternehmensnetzwerk heraus herzustellen, wird der ausgehende Datenverkehr über Port 1433 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Server herstellen, wenn Ihre IT-Abteilung Port 1433 öffnet.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Erstellen eines dedizierten SQL-Pools (vormals SQL DW)

Das folgende Beispiel erstellt einen dedizierten SQL-Pool (vormals SQL DW) mit den zuvor definierten Variablen.  Das Dienstziel wird als DW100c angegeben. Dies ist ein kostengünstiger Ausgangspunkt für Ihren dedizierten SQL-Pool (vormals SQL DW).

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

* **RequestedServiceObjectiveName**: Die Menge an [Data Warehouse-Einheiten](what-is-a-data-warehouse-unit-dwu-cdwu.md), die Sie anfordern. Durch das Erhöhen dieses Werts steigen die Computekosten. Eine Liste der unterstützten Werte finden Sie unter [Grenzwerte für Arbeitsspeicher und Parallelität](memory-concurrency-limits.md).
* **DatabaseName**: Der Name des dedizierten SQL-Pools (vormals SQL DW), den Sie erstellen
* **ServerName**: Der Name des Servers, den Sie für die Erstellung verwenden.
* **ResourceGroupName**: Die Ressourcengruppe, die Sie verwenden. Verwenden Sie zum Abrufen der in Ihrem Abonnement verfügbaren Ressourcengruppen das Cmdlet „Get-AzureResource“.
* **Edition**: Muss „DataWarehouse“ lauten, damit ein dedizierter SQL-Pool (vormals SQL DW) erstellt werden kann.

Optionale Parameter:

* **CollationName**: Ohne Angabe wird die Standardsortierung „SQL_Latin1_General_CP1_CI_AS“ verwendet. Die Sortierung kann für eine Datenbank nicht geändert werden.
* **MaxSizeBytes**: Die maximale Größe einer Datenbank beträgt standardmäßig 240 TB. Durch die maximale Größe werden die Rowstore-Daten begrenzt. Die Speichermenge für Spaltendaten ist nicht begrenzt.

Weitere Informationen zu den Parameteroptionen finden Sie unter [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstarttutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf.

> [!TIP]
> Wenn Sie mit weiteren Schnellstart-Tutorials fortfahren möchten, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen. Falls Sie nicht fortfahren möchten, können Sie die folgenden Schritte ausführen, um alle erstellten Ressourcen dieses Schnellstarts im Azure-Portal zu löschen.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben nun einen dedizierten SQL-Pool (vormals SQL DW) sowie eine Firewallregel erstellt und diese mit Ihrem dedizierten SQL-Pool verbunden. Weitere Informationen finden Sie im Artikel zum [Laden von Daten in einen dedizierten SQL-Pool](load-data-from-azure-blob-storage-using-polybase.md).
