---
title: 'Mit PowerShell: Hinzufügen eines Pools für elastische Datenbanken zu einer Autofailover-Gruppe'
description: Hier finden Sie ein Azure PowerShell-Beispielskript, mit dem Sie einen Pool für elastische Azure SQL-Datenbank-Instanzen erstellen und einer Autofailover-Gruppe hinzufügen sowie das Failover testen können.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.openlocfilehash: a8326249b48e98eccecb5f0c4953add26e167560
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594105"
---
# <a name="use-powershell-to-add-an-elastic-pool-to-a-failover-group"></a>Verwenden von PowerShell, um einen Pool für elastische Datenbanken einer Failovergruppe hinzuzufügen
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Mit diesem Azure PowerShell-Skriptbeispiel wird eine Datenbank in Azure SQL-Datenbank erstellt und einem Pool für elastische Datenbanken hinzugefügt, eine Failovergruppe erstellt und das Failover getestet.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens die Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-scripts"></a>Beispielskripts

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Erstellt einen Server, der Datenbanken und Pools für elastische Datenbanken hostet. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Erstellt eine Firewallregel auf Serverebene für einen Server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Erstellt eine neue Datenbank. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Erstellt einen elastischen Datenbankpool.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Legt Eigenschaften für eine Datenbank fest oder verschiebt eine vorhandene Datenbank in einen Pool für elastische Datenbanken. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Erstellt eine neue Failovergruppe. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ruft mindestens eine Datenbank ab. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Fügt einer Failovergruppe eine oder mehrere Datenbanken hinzu. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ruft Datenbank-Failovergruppen ab oder listet diese auf. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Führt ein Failover für eine Datenbank-Failovergruppe aus. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie in den [Azure PowerShell samples for Azure SQL Database](../powershell-script-content-guide.md) (Azure PowerShell-Beispiele für Azure SQL-Datenbank).
