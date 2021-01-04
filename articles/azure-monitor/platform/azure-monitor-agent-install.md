---
title: 'Installieren des Azure Monitor-Agents:'
description: Optionen zum Installieren des Azure Monitor-Agents (AMA) auf Azure-VMs und Servern mit Azure Arc-Unterstützung.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 893a04f5acd48cf1e6f34033c06a758492e70abf
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516723"
---
# <a name="install-the-azure-monitor-agent-preview"></a>Installieren des Azure Monitor-Agents (Vorschau)
In diesem Artikel werden die verschiedenen Optionen beschrieben, die derzeit für die Installation des [Azure Monitor-Agents](azure-monitor-agent-overview.md) auf Azure-VMs und Servern mit Azure Arc-Unterstützung verfügbar sind, sowie die Optionen zum Erstellen von [Zuordnungen für Datensammlungsregeln](data-collection-rule-azure-monitor-agent.md), die definieren, welche Daten der Agent erfassen soll.

## <a name="prerequisites"></a>Voraussetzungen
Vor der Installation des Azure Monitor-Agents müssen die folgenden Voraussetzungen erfüllt sein.

- Auf Azure-VMs muss eine [verwaltete Systemidentität](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) aktiviert sein. Bei Servern mit Azure Arc-Unterstützung ist dies nicht erforderlich. Die Systemidentität wird automatisch aktiviert, wenn der Agent als Teil des Prozesses zum [Erstellen und Zuweisen einer Datensammlungsregel über das Azure-Portal](#install-with-azure-portal) installiert wird.
- Das [Diensttag „AzureResourceManager“](../../virtual-network/service-tags-overview.md) muss im virtuellen Netzwerk für die VM aktiviert sein.

## <a name="virtual-machine-extension-details"></a>Details zur VM-Erweiterung
Der Azure Monitor-Agent wird als [Azure-VM-Erweiterung](../../virtual-machines/extensions/overview.md) mit den Details aus der folgenden Tabelle implementiert: Er kann mit einer der Methoden zum Installieren von VM-Erweiterungen installiert werden, einschließlich der in diesem Artikel beschriebenen Methoden.

| Eigenschaft | Windows | Linux |
|:---|:---|:---|
| Herausgeber | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| type      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>Installieren über das Azure-Portal
Zum Installieren des Azure Monitor-Agents über das Azure-Portal führen Sie das Verfahren zum [Erstellen einer Datensammlungsregel](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) im Azure-Portal aus. Auf diese Weise können Sie die Datensammlungsregel einer oder mehreren Azure-VMs oder einem oder mehreren Servern mit Azure Arc-Unterstützung zuordnen. Der Agent wird auf allen VMs installiert, auf denen er noch nicht installiert ist.


## <a name="install-with-resource-manager-template"></a>Installieren mit einer Resource Manager-Vorlage
Mithilfe von Resource Manager-Vorlagen können Sie den Azure Monitor-Agent auf Azure-VMs und Servern mit Azure Arc-Unterstützung installieren und eine Zuordnung zu Datensammlungsregeln erstellen. Die entsprechenden Datensammlungsregeln müssen Sie vor dem Erstellen der Zuordnung erstellen.

Hier finden Sie Beispielvorlagen für die Installation des Agents und die Erstellung der Zuordnung: 

- [Vorlage zum Installieren des Azure Monitor-Agents (Azure und Azure Arc)](../samples/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Vorlage zum Erstellen einer Zuordnung zu einer Datensammlungsregel](../samples/resource-manager-data-collection-rules.md)

Installieren Sie die Vorlagen mit [einer Bereitstellungsmethode für Resource Manager-Vorlagen](../../azure-resource-manager/templates/deploy-powershell.md), z. B. mit den folgenden Befehlen.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Installieren mit PowerShell
Sie können den Azure Monitor-Agent auf Azure-VMs und Servern mit Azure Arc-Unterstützung über den PowerShell-Befehl zum Hinzufügen einer VM-Erweiterung installieren. 

### <a name="azure-virtual-machines"></a>Virtuelle Azure-Computer
Verwenden Sie die folgenden PowerShell-Befehle, um den Azure Monitor-Agent auf Azure-VMs zu installieren.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---

### <a name="azure-arc-enabled-servers"></a>Server mit Azure Arc-Unterstützung
Verwenden Sie die folgenden PowerShell-Befehle, um den Azure Monitor-Agent auf Servern mit Azure Arc-Unterstützung zu installieren.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Azure CLI
Sie können den Azure Monitor-Agent auf Azure-VMs und Servern mit Azure Arc-Unterstützung über den Azure CLI-Befehl zum Hinzufügen einer VM-Erweiterung installieren. 

### <a name="azure-virtual-machines"></a>Virtuelle Azure-Computer
Verwenden Sie die folgenden CLI-Befehle, um den Azure Monitor-Agent auf Azure-VMs zu installieren.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Server mit Azure Arc-Unterstützung
Verwenden Sie die folgenden CLI-Befehle, um den Azure Monitor-Agent auf Servern mit Azure Arc-Unterstützung zu installieren.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie eine Datensammlungsregel](data-collection-rule-azure-monitor-agent.md), um Daten des Agents zu sammeln und an Azure Monitor zu senden.
