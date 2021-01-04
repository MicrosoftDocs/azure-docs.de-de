---
title: Erstellen einer Azure Firewall-Testumgebung
description: Dieses Skriptbeispiel erstellt eine Firewall und eine Netzwerkumgebung zu Testzwecken. Das Netzwerk verfügt über ein VNET mit drei Subnetzen.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 4158bc07373a2d0aa6fb6ceaf2dce62b50bb6bd7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658365"
---
# <a name="create-an-azure-firewall-test-environment"></a>Erstellen einer Azure Firewall-Testumgebung

Dieses Skriptbeispiel erstellt eine Firewall und eine Netzwerkumgebung zu Testzwecken. Das Netzwerk umfasst ein einzelnes VNet mit drei Subnetzen: *AzureFirewallSubnet*, *ServersSubnet* und *JumpboxSubnet*. „ServersSubnet“ und „JumpboxSubnet“ enthalten jeweils einen Windows Server mit zwei Kernen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Die Firewall befindet sich in „AzureFirewallSubnet“ und wird mit einer Anwendungsregelsammlung mit einer einzelnen Regel konfiguriert, die den Zugriff auf `www.microsoft.com` ermöglicht.

Es wird eine benutzerdefinierte Route erstellt, die den Netzwerkverkehr von „ServersSubnet“ durch die Firewall leitet, wo die Firewallregeln angewendet werden.

Sie können das Skript über Azure [Cloud Shell](https://shell.azure.com/powershell) oder über eine lokale PowerShell-Installation ausführen. 

Wenn Sie PowerShell lokal ausführen, ist für dieses Skript Azure PowerShell erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. 

Sollte ein Upgrade erforderlich sein, können Sie `PowerShellGet` verwenden (in Windows 10 und Windows Server 2016 integriert).

> [!NOTE]
>Bei Verwendung einer anderen Windows-Version müssen Sie `PowerShellGet` installieren, um es verwenden zu können. Durch Ausführen von `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` können Sie ermitteln, ob es auf Ihrem System installiert ist. Ist die Ausgabe leer, installieren Sie die neueste Version von [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616).

Weitere Informationen finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-Az-ps).

Vorhandene Azure PowerShell-Installationen, die über den Webplattform-Installer durchgeführt wurden, führen zu einem Konflikt mit der PowerShellGet-Installation und müssen entfernt werden.

Zur Erinnerung: Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure zu erstellen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, ein virtuelles Netzwerk und Netzwerksicherheitsgruppen zu erstellen. Jeder Befehl in der folgenden Tabelle ist mit der zugehörigen Dokumentation verknüpft:

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Erstellt ein Subnetzkonfigurationsobjekt |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellt ein virtuelles Azure-Netzwerk und ein Front-End-Subnetz. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Erstellt Sicherheitsregeln, die einer Netzwerksicherheitsgruppe zugewiesen werden sollen |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Erstellt NSG-Regeln, die bestimmte Ports bei bestimmten Subnetzen zulassen oder blockieren. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Ordnet NSGs Subnetzen zu. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Erstellt eine öffentliche IP-Adresse für den Zugriff auf den virtuellen Computer aus dem Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Erstellt virtuelle Netzwerkschnittstellen und fügt sie an das Front-End- und Back-End-Subnetz des virtuellen Netzwerks an. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Erstellt eine VM-Konfiguration. Diese Konfiguration umfasst Informationen wie VM-Name, Betriebssystem und Administratoranmeldeinformationen. Die Konfiguration wird während der VM-Erstellung verwendet. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Erstellen Sie eine VM. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe und alle darin enthaltenen Ressourcen. |
|[New-AzFirewall](/powershell/module/az.network/new-azfirewall)| Erstellt eine neue Azure Firewall.|
|[Get-AzFirewall](/powershell/module/az.network/get-azfirewall)|Ruft ein Azure Firewall-Objekt ab.|
|[New-AzFirewallApplicationRule](/powershell/module/az.network/new-azfirewallapplicationrule)|Erstellt eine neue Azure Firewall-Anwendungsregel.|
|[Set-AzFirewall](/powershell/module/az.network/set-azfirewall)|Führt ein Commit für die Änderungen am Azure Firewall-Objekt aus.|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).