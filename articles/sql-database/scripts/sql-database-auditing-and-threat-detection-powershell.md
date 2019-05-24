---
title: 'PowerShell-Beispiel: Überwachung und Bedrohungserkennung in einer Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Azure PowerShell-Beispielskript zum Konfigurieren der Überwachung und Bedrohungserkennung in einer Azure SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: PowerShell
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: cce395a9e476189570a520be0b3e1cc3913933b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66167039"
---
# <a name="use-powershell-to-configure-sql-database-auditing-and-threat-detection"></a>Konfigurieren der SQL-Datenbank-Überwachung und -Bedrohungserkennung

Dieses PowerShell-Skriptbeispiel konfiguriert die SQL-Datenbank-Überwachung und -Bedrohungserkennung.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-script"></a>Beispielskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1?highlight=15-16 "Configure auditing and threat detection")]

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
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Erstellt einen SQL-Datenbank-Server, der eine Einzeldatenbank oder einen Pool für elastische Datenbanken hostet. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Erstellt eine Einzeldatenbank oder einen Pool für elastische Datenbanken. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Erstellt ein Speicherkonto. |
| [Set-AzSqlDatabaseAuditing](/powershell/module/az.sql/set-azsqldatabaseauditing) | Legt die Überwachungsrichtlinie für eine Datenbank fest. |
| [Set-AzSqlDatabaseThreatDetectionPolicy](/powershell/module/az.sql/set-azsqldatabasethreatdetectionpolicy) | Legt eine Richtlinie zur Erkennung von Bedrohungen für eine Datenbank fest. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie in den [Azure PowerShell samples for Azure SQL Database](../sql-database-powershell-samples.md) (Azure PowerShell-Beispiele für Azure SQL-Datenbank).
