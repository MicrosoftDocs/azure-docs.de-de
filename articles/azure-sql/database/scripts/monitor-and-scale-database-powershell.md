---
title: Überwachen und Skalieren einer Einzeldatenbank in Azure SQL­-Datenbank mit PowerShell
description: Verwenden Sie ein Azure PowerShell-Beispielskript zum Überwachen und Skalieren einer einzelnen Datenbank in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 04c19ca8fbdaed85225b5af128c72d393e5350e8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573249"
---
# <a name="use-powershell-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Überwachen und Skalieren einer Einzeldatenbank in Azure SQL­-Datenbank mit PowerShell

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Dieses PowerShell-Skriptbeispiel überwacht die Leistungsmetriken einer einzelnen Datenbank, skaliert sie auf eine höhere Computegröße und erstellt eine Warnungsregel zu einer der Leistungsmetriken.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens die Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-script"></a>Beispielskript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=15-16 "Monitor and scale single database")]

> [!NOTE]
> Eine vollständige Liste der Metriken finden Sie unter [Unterstützte Metriken von Azure Monitor](../../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases).
> [!TIP]
> Rufen Sie mit [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) den Status von Datenbankvorgängen ab, und brechen Sie mit [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity) einen Aktualisierungsvorgang für die Datenbank ab.

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Erstellt einen Server, der eine Einzeldatenbank oder einen Pool für elastische Datenbanken hostet. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Zeigt die Größennutzungsinformationen für die Datenbank an.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Aktualisiert Datenbankeigenschaften oder verschiebt die Datenbank in Pools für elastische Datenbanken, daraus hinaus oder zwischen ihnen. |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Legt eine Warnungsregel zur zukünftigen automatischen Überwachung von Metriken fest. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

Zusätzliche PowerShell-Skriptbeispiele finden Sie unter [Azure PowerShell-Skripts](../powershell-script-content-guide.md).
