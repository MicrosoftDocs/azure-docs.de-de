---
title: Erstellen einer Windows-VM mithilfe einer Vorlage in Azure | Microsoft Docs
description: Verwenden Sie eine Resource Manager-Vorlage und PowerShell, um ganz einfach einen neuen virtuellen Windows-Computer zu erstellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58c8ca9bb9b4dfd58c801eb4dd1dad95c1969c18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127615"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Erstellen eines virtuellen Windows-Computer mit einer Resource Manager-Vorlage

Es wird beschrieben, wie Sie einen virtuellen Windows-Computer mit einer Azure Resource Manager-Vorlage und mit Azure PowerShell über die Azure Cloud Shell erstellen. Mit der in diesem Artikel verwendeten Vorlage wird ein einzelner virtueller Computer mit Windows Server in einem neuen virtuellen Netzwerk mit nur einem Subnetz bereitgestellt. Informationen zum Erstellen eines virtuellen Linux-Computers finden Sie unter [Erstellen eines virtuellen Linux-Computers mithilfe von Azure Resource Manager-Vorlagen](../linux/create-ssh-secured-vm-from-template.md).

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Die Erstellung eines virtuellen Azure-Computers umfasst normalerweise zwei Schritte:

- Erstellen Sie eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Vor dem virtuellen Computer muss eine Ressourcengruppe erstellt werden.
- Erstellen Sie eine VM.

Im folgenden Beispiel wird eine VM über eine [Azure-Schnellstartvorlage](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) erstellt. Hier ist eine Kopie der Vorlage angegeben:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Wählen Sie zum Ausführen des PowerShell-Skripts die Option **Testen Sie es.** aus, um Azure Cloud Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie **Einfügen** aus:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Wenn Sie nicht die Azure Cloud Shell verwenden, sondern PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial das Azure PowerShell-Modul verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

Im vorherigen Beispiel haben Sie eine in GitHub gespeicherte Vorlage angegeben. Sie können eine Vorlage außerdem herunterladen oder erstellen und den lokalen Pfad mit dem Parameter `--template-file` angeben.

Hier sind einige zusätzlichen Ressourcen:

- Informationen zur Entwicklung von Resource Manager-Vorlagen finden Sie in der [Azure Resource Manager-Dokumentation](/azure/azure-resource-manager/).
- Die Schemas zu virtuellen Azure-Computern finden Sie in der [Azure-Vorlagenreferenz](/azure/templates/microsoft.compute/allversions).
- Weitere Beispiele für VM-Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Herstellen einer Verbindung mit dem virtuellen Computer

Im letzten PowerShell-Befehl aus dem vorherigen Skript wird der Name des virtuellen Computers angezeigt. Informationen zum Herstellen einer Verbindung mit dem virtuellen Computer finden Sie unter [Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden auf diesem Computer](./connect-logon.md).

## <a name="next-steps"></a>Nächste Schritte

- Falls bei der Bereitstellung Probleme aufgetreten sind, können Sie den Artikel [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../../resource-manager-common-deployment-errors.md) lesen.
- Erfahren Sie, wie Sie einen virtuellen Computer erstellen und verwalten, indem Sie [Erstellen und Verwalten von virtuellen Windows-Computern mit dem Azure PowerShell-Modul](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) durcharbeiten.

Wenn Sie weitere Informationen zum Erstellen von Vorlagen benötigen, sehen Sie sich die JSON-Syntax und die Eigenschaften für die von Ihnen bereitgestellten Ressourcentypen an:

- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
