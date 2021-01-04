---
title: 'Mit PowerShell: Hinzufügen einer verwalteten Instanz zu einer Autofailover-Gruppe'
titleSuffix: Azure SQL Managed Instance
description: Hier finden Sie ein Azure PowerShell-Beispielskript, mit dem Sie eine verwaltete Instanz erstellen, die Instanz einer Autofailover-Gruppe hinzufügen und das Failover testen können.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.openlocfilehash: af1d7110cccfd8b0617d6c79eb5373cc7c087159
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594010"
---
# <a name="use-powershell-to-add-a-managed-instance-to-a-failover-group"></a>Hinzufügen einer verwalteten Instanz zu einer Failovergruppe mithilfe von PowerShell 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Mit diesem PowerShell-Beispielskript werden zwei verwaltete Instanzen erstellt und einer Failovergruppe hinzugefügt. Anschließend wird das Failover von der primären verwalteten Instanz zur sekundären verwalteten Instanz getestet. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens die Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="sample-scripts"></a>Beispielskripts

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Verwenden Sie den folgenden Befehl, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen: Die Ressourcengruppe muss zweimal entfernt werden. Wenn Sie die Ressourcengruppe zum ersten Mal entfernen, werden die verwaltete Instanz und die virtuellen Cluster entfernt, es wird jedoch die Fehlermeldung `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` ausgegeben. Führen Sie den Befehl Remove-AzResourceGroup ein zweites Mal aus, um alle übrigen Ressourcen sowie die Ressourcengruppe zu entfernen.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Azure-Ressourcengruppe.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellen Sie ein virtuelles Netzwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Fügt einem virtuellen Netzwerk eine Subnetzkonfiguration hinzu | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ruft ein virtuelles Netzwerk in einer Ressourcengruppe ab | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ruft ein Subnetz in einem virtuellen Netzwerk ab | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Erstellt eine Netzwerksicherheitsgruppe. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Erstellt eine Routingtabelle |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aktualisiert eine Subnetzkonfiguration für ein virtuelles Netzwerk  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aktualisiert ein virtuelles Netzwerk  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Ruft eine Netzwerksicherheitsgruppe ab |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Fügt einer Netzwerksicherheitsgruppe eine Netzwerksicherheits-Konfigurationsregel hinzu |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aktualisiert eine Netzwerksicherheitsgruppe  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Fügt einer Routingtabelle Routen hinzu |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aktualisiert eine Routingtabelle  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Erstellt eine verwaltete Instanz.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Gibt Informationen zu Azure SQL Managed Instance zurück. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Erstellt eine öffentliche IP-Adresse.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Erstellt eine IP-Konfiguration für ein Gateway des virtuellen Netzwerks |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Erstellt ein Gateway für das virtuelle Netzwerk |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Erstellt eine Verbindung zwischen den beiden Gateways der virtuellen Netzwerke   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Erstellt eine neue Azure SQL Managed Instance-Failovergruppe.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ruft die Azure SQL Managed Instance-Failovergruppen ab oder listet sie auf.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Führt ein Failover einer Azure SQL Managed Instance-Failovergruppe aus. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe | 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

Weitere PowerShell-Skriptbeispiele für SQL Managed Instance finden Sie unter [Azure PowerShell-Beispiele für Azure SQL-Datenbank und verwaltete Azure SQL-Instanzen](../../database/powershell-script-content-guide.md).
