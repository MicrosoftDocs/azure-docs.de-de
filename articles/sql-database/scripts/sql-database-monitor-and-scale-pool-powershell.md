---
title: 'PowerShell-Beispiel: Überwachen und Skalieren eines Pools für elastische Datenbanken in Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Azure PowerShell-Beispielskript zum Überwachen und Skalieren eines Pools für elastische Datenbanken in Azure SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3501a05ae874dd18a7ab4f7b98d7c85a8da8cc15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115988"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>Verwenden von PowerShell zum Überwachen und Skalieren eines Pools für elastische Datenbanken in Azure SQL-Datenbank

Dieses PowerShell-Beispielskript überwacht die Leistungsmetriken eines Pools für elastische Datenbanken, skaliert ihn auf eine höhere Computegröße und erstellt eine Warnungsregel auf einer der Leistungsmetriken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-script"></a>Beispielskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale a single SQL Database")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Erstellt einen SQL-Datenbank-Server, der eine Einzeldatenbank oder einen Pool für elastische Datenbanken hostet. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Erstellt einen Pool für elastische Datenbanken. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Erstellt eine Einzeldatenbank oder eine Datenbank in einem Pool für elastische Datenbanken. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Zeigt die Größennutzungsinformationen für die Datenbank an.|
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Fügt eine metrikbasierte Warnungsregel hinzu oder aktualisiert sie. |
| [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) | Aktualisiert die Eigenschaften von Pool für elastische Datenbanken. |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Legt eine Warnungsregel zur zukünftigen automatischen Überwachung von DTUs fest. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie in den [Azure PowerShell samples for Azure SQL Database](../sql-database-powershell-samples.md) (Azure PowerShell-Beispiele für Azure SQL-Datenbank).
