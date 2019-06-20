---
title: 'Azure PowerShell-Skriptbeispiel: Azure Monitor-Protokolle | Microsoft-Dokumentation'
description: 'Azure PowerShell-Skriptbeispiel: Azure Monitor-Protokolle'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 130ca2dc156df8c816fecec6cf4302472d072e68
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723118"
---
# <a name="create-an-azure-monitor-vm-with-powershell"></a>Erstellen eines virtuellen Azure Monitor-Computers mit PowerShell 

Mit diesem Skript wird ein virtueller Azure-Computer erstellt, der Log Analytics-Agent installiert und das System in einem Log Analytics-Arbeitsbereich registriert. Nach Ausführung des Skripts wird der virtuelle Computer in Azure Monitor angezeigt. Außerdem müssen Sie die Log Analytics-Arbeitsbereichs-ID und den Arbeitsbereichsschlüssel aktualisieren.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-monitor-oms/create-windows-vm-detailed-oms.ps1 "Create VM Log Analytics")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle zum Erstellen der Bereitstellung. Jedes Element in der Tabelle ist mit der befehlsspezifischen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Erstellt den virtuellen Computer und verbindet diesen mit der Netzwerkkarte, dem virtuellen Netzwerk, dem Subnetz und der Netzwerksicherheitsgruppe. Mit diesem Befehl werden außerdem Port 80 geöffnet und die Administratoranmeldeinformationen festgelegt. |
| [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) | Hinzufügen einer VM-Erweiterung zum virtuellen Computer. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe und alle darin enthaltenen Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche VM-PowerShell-Skriptbeispiele finden Sie in der [Dokumentation zu Windows-VMs in Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
