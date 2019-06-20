---
title: 'PowerShell-Beispiel: Erstellen einer verwalteten Instanz in Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Azure PowerShell-Beispielskript zum Erstellen einer verwalteten Instanz in Azure SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 929ab995ea76fa0d1d5227e3a53c2b50bc43fdc0
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729361"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Erstellen einer verwalteten Azure SQL-Datenbank-Instanz mithilfe von PowerShell

In diesem PowerShell-Beispiel wird eine verwaltete Azure SQL-Datenbank-Instanz in einem dedizierten Subnetz innerhalb eines neuen virtuellen Netzwerks erstellt. Außerdem werden eine Routingtabelle und eine Netzwerksicherheitsgruppe für das virtuelle Netzwerk konfiguriert. Nach erfolgreicher Ausführung des Skripts kann innerhalb des virtuellen Netzwerks oder über eine lokale Umgebung auf die verwaltete Instanz zugegriffen werden. Siehe [Konfigurieren einer Azure-VM für das Herstellen einer Verbindung mit einer verwalteten Azure SQL-Datenbank-Instanz](../sql-database-managed-instance-configure-vm.md) sowie [Konfigurieren einer Point-to-Site-Verbindung von einem lokalen Computer mit einer verwalteten Azure SQL-Datenbank-Instanz](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Informationen zu Einschränkungen finden Sie unter [unterstützte Regionen](../sql-database-managed-instance-resource-limits.md#supported-regions) und [unterstützte Abonnementtypen](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-script"></a>Beispielskript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellt ein virtuelles Netzwerk. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Fügt einem virtuellen Netzwerk eine Subnetzkonfiguration hinzu. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Ruft ein virtuelles Netzwerk in einer Ressourcengruppe ab. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Legt den Zielzustand für ein virtuelles Netzwerk fest. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Ruft ein Subnetz in einem virtuellen Netzwerk ab. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Konfiguriert den Zielzustand für eine Subnetzkonfiguration in einem virtuellen Netzwerk. |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Erstellt eine Routingtabelle. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Ruft Routingtabellen ab. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Legt den Zielzustand für eine Routingtabelle fest. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Erstellt eine verwaltete Azure SQL-Datenbank-Instanz. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche PowerShell-Skriptbeispiele für SQL-Datenbank finden Sie in den [Azure PowerShell samples for Azure SQL Database](../sql-database-powershell-samples.md) (Azure PowerShell-Beispiele für Azure SQL-Datenbank).
