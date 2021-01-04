---
title: Häufig verwendete PowerShell-Befehle für virtuelle Azure-Computer
description: Enthält häufig verwendete PowerShell-Befehle als Einstiegshilfe zum Erstellen und Verwalten von VMs in Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 6f7f2adb5c3e154c3910ee1082e9afad70de9758
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010077"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Häufige PowerShell-Befehle zum Erstellen und Verwalten virtueller Azure-Computer

In diesem Artikel werden einige Azure PowerShell-Befehle beschrieben, die Sie zum Erstellen und Verwalten virtueller Computer Ihres Azure-Abonnements verwenden können.  Eine ausführlichere Hilfe mit speziellen Befehlszeilenswitches und -optionen erhalten Sie mit dem *Befehl* **Get-Help**.

 

Diese Variablen könnten hilfreich sein, wenn Sie mehr als einen der Befehle aus diesem Artikel ausführen:

- $location – Der Speicherort des virtuellen Computers. Sie können [Get-AzLocation](/powershell/module/az.resources/get-azlocation) verwenden, um eine [geografische Region](https://azure.microsoft.com/regions/) zu suchen, die sich für Ihre Zwecke eignet.
- $myResourceGroup – Der Name der Ressourcengruppe, die den virtuellen Computer enthält.
- $myVM – Der Name des virtuellen Computers.

## <a name="create-a-vm---simplified"></a>Erstellen einer VM (vereinfacht)

| Aufgabe | Get-Help |
| ---- | ------- |
| Erstellen einer einfachen VM | [New-AzVM](/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> „New-AzVM“ beinhaltet eine Reihe von *vereinfachten* Parametern, wobei lediglich ein einziger Name erforderlich ist. Der Wert für „-Name“ wird als Name für alle Ressourcen verwendet, die zum Erstellen einer neuen VM erforderlich sind. Sie können weitere Werte angeben, erforderlich ist jedoch nur dieser.|
| Erstellen eines virtuellen Computers aus einem benutzerdefinierten Image | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>Sie müssen bereits ein eigenes [verwaltetes Image](capture-image-resource.md) erstellt haben. Sie können ein Image auf mehreren identischen VMs verwenden. |



## <a name="create-a-vm-configuration"></a>Erstellen einer VM-Konfiguration

| Aufgabe | Get-Help |
| ---- | ------- |
| Erstellen einer VM-Konfiguration |$vm = [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>Die VM-Konfiguration wird verwendet, um Einstellungen für die VM zu definieren oder zu aktualisieren. Die Konfiguration wird mit dem Namen der VM und ihrer [Größe](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) initialisiert. |
| Hinzufügen von Konfigurationseinstellungen |$vm = [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Betriebssystemeinstellungen einschließlich der [Anmeldeinformationen](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1) werden dem Konfigurationsobjekt hinzugefügt, das Sie zuvor mit „New-AzVMConfig“ erstellt haben. |
| Hinzufügen einer Netzwerkschnittstelle |$vm = [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Eine VM muss über eine [Netzwerkschnittstelle](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json) für die Kommunikation in einem virtuellen Netzwerk verfügen. Sie können auch [Get-AzNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) verwenden, um ein vorhandenes Netzwerkschnittstellenobjekt abzurufen. |
| Angeben eines Plattformimage |$vm = [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[Imageinformationen](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) werden dem Konfigurationsobjekt hinzugefügt, das Sie zuvor mit „New-AzVMConfig“ erstellt haben. Das von diesem Befehl zurückgegebene Objekt wird nur verwendet, wenn Sie für den Betriebssystem-Datenträger die Verwendung eines Plattformimage festlegen. |
| Erstellen einer VM |[New-AzVM](/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Alle Ressourcen werden in einer [Ressourcengruppe](../../azure-resource-manager/management/manage-resource-groups-powershell.md) erstellt. Führen Sie vor dem Ausführen dieses Befehls „New-AzVMConfig“, „Set-AzVMOperatingSystem“, „Set-AzVMSourceImage“, „Add-AzVMNetworkInterface“ und „Set-AzVMOSDisk“ aus. |
| Aktualisieren einer VM |[Update-AzVM](/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Rufen Sie die aktuelle VM-Konfiguration ab, indem Sie „Get-AzVM“ verwenden, ändern Sie die Konfigurationseinstellungen auf dem VM-Objekt, und führen Sie dann diesen Befehl aus. |

## <a name="get-information-about-vms"></a>Abrufen von Informationen zu virtuellen Computern

| Aufgabe | Get-Help |
| ---- | ------- |
| Auflisten der VMs eines Abonnements |[Get-AzVM](/powershell/module/az.compute/get-azvm) |
| Auflisten der VMs einer Ressourcengruppe |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Verwenden Sie zum Abrufen einer Liste mit Ressourcengruppen Ihres Abonnements den Befehl [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup). |
| Abrufen von Informationen zu einem virtuellen Computer |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Verwalten von VMs
| Aufgabe | Get-Help |
| --- | --- |
| Starten eines virtuellen Computers |[Start-AzVM](/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Anhalten eines virtuellen Computers |[Stop-AzVM](/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Neustarten einer ausgeführten VM |[Restart-AzVM](/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Löschen eines virtuellen Computers |[Remove-AzVM](/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Nächste Schritte
* Die grundlegenden Schritte zum Erstellen eines virtuellen Computers finden Sie unter [Erstellen einer Windows-VM mit dem Resource Manager und PowerShell](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json).