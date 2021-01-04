---
title: DNS-Alias (PowerShell und Azure-Befehlszeilenschnittstelle)
description: Mit Cmdlets in PowerShell und der Azure-Befehlszeilenschnittstelle können Sie neue Clientverbindungen an einen anderen SQL-Server in Azure umleiten, ohne Clientkonfigurationen bearbeiten zu müssen.
keywords: dns sql database
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 02cfd839ed1b75fd85553f2e5a5150cadc29ff8e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790456"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell für den DNS-Alias für Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Dieser Artikel bietet ein PowerShell-Skript, das veranschaulicht, wie Sie einen DNS-Alias für den [SQL-Server](logical-servers.md) verwalten, der Ihre Azure SQL-Datenbank-Instanz hostet.

> [!NOTE]
> Dieser Artikel wurde aktualisiert und behandelt nun die Verwendung des Azure PowerShell Az-Moduls bzw. der Azure CLI. Sie können das AzureRM-Modul weiterhin verwenden, das bis mindestens Dezember 2020 weiterhin Fehlerbehebungen erhält.
>
> Weitere Informationen zum Az-Modul und zur Kompatibilität mit AzureRM finden Sie unter [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az). Anweisungen zur Installation finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps) oder [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>DNS-Alias in der Verbindungszeichenfolge

Um eine Verbindung mit einem [logischen SQL-Server](logical-servers.md) herzustellen, kann ein Client wie SQL Server Management Studio (SSMS) anstelle des tatsächlichen Servernamens den DNS-Aliasnamen angeben. In der folgenden Beispielserverzeichenfolge ersetzt der Alias *any-unique-alias-name* den ersten durch Punkte getrennten Knoten in der Serverzeichenfolge mit vier Knoten:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie das in diesem Artikel angegebene PowerShell-Demoskript ausführen möchten, gelten die folgenden Voraussetzungen:

- Ein Azure-Abonnement und -Konto; für eine kostenlose Testversion siehe [Azure-Testversionen](https://azure.microsoft.com/free/)
- Zwei Server

## <a name="example"></a>Beispiel

Im folgenden Codebeispiel werden mehreren Variablen zunächst Literalwerte zugewiesen.

Um den Code auszuführen, ändern Sie die Platzhalterwerte in die entsprechenden tatsächlichen Werten in Ihrem System.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Die folgenden Cmdlets werden verwendet:

- [New-AzSqlServerDNSAlias](/powershell/module/az.Sql/New-azSqlServerDnsAlias): Erstellt einen DNS-Alias im Dienstsystem von Azure SQL-Datenbank. Der Alias verweist auf Server 1.
- [Get-AzSqlServerDNSAlias](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Ruft alle Aliase auf, die Server 1 zugewiesen sind, und listet diese auf.
- [Set-AzSqlServerDNSAlias](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Ändert den Servernamen, für dessen Referenzierung der Alias konfiguriert ist, aus Server 1 in Server 2.
- [Remove-AzSqlServerDNSAlias](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Entfernt den Alias von Server 2 anhand des Aliasnamens.

Informationen zum Installieren oder Upgraden finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps).

Verwenden Sie `Get-Module -ListAvailable Az` in der Datei *powershell\_ise.exe* , um die entsprechende Version zu ermitteln.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Die folgenden Befehle werden verwendet:

- [az sql server dns-alias create](/powershell/module/az.Sql/New-azSqlServerDnsAlias): Erstellt einen DNS-Alias für einen Server. Der Alias verweist auf Server 1.
- [az sql server dns-alias show](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Ruft alle Aliase auf, die Server 1 zugewiesen sind, und listet diese auf.
- [az sql server dns-alias set](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Ändert den Servernamen, für dessen Referenzierung der Alias konfiguriert ist, aus Server 1 in Server 2.
- [az sql server dns-alias delete](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Entfernt den Alias von Server 2 anhand des Aliasnamens.

Informationen zum Ausführen einer Installation oder eines Upgrades finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Nächste Schritte

Eine vollständige Erläuterung des DNS-Aliasfeatures für SQL-Datenbank finden Sie unter [DNS-Alias für Azure SQL-Datenbank](./dns-alias-overview.md).