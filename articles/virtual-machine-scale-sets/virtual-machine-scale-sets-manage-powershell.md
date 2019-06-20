---
title: Verwalten von VM-Skalierungsgruppen mit Azure PowerShell | Microsoft-Dokumentation
description: Informationen zu gängigen Azure PowerShell-Cmdlets für die Verwaltung von VM-Skalierungsgruppen (etwa zum Starten und Beenden einer Instanz oder zum Ändern der Kapazität der Skalierungsgruppe).
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: a6474320fd8b1545d61320cd43e155ab077ba310
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683530"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Verwalten einer VM-Skalierungsgruppe mit Azure PowerShell

Während des Lebenszyklus einer Skalierungsgruppe müssen unter Umständen verschiedene Verwaltungsaufgaben durchgeführt werden. Darüber hinaus empfiehlt es sich, Skripts zum Automatisieren von verschiedenen Aufgaben im Lebenszyklus zu erstellen. In diesem Artikel werden einige der gängigen Microsoft Azure PowerShell-Cmdlets behandelt, mit denen Sie diese Aufgaben ausführen können.

Bei Bedarf können Sie [eine VM-Skalierungsgruppe mit Azure PowerShell erstellen](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Anzeigen von Informationen zu einer Skalierungsgruppe
Verwenden Sie [Get-AzVmss](/powershell/module/az.compute/get-azvmss), um die allgemeinen Informationen zu einer Skalierungsgruppe anzuzeigen. Im folgenden Beispiel werden Informationen zur Skalierungsgruppe namens *myScaleSet* in der Ressourcengruppe *myResourceGroup* abgerufen. Geben Sie Ihre eigenen Namen wie folgt ein:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Anzeigen von virtuellen Computern in einer Skalierungsgruppe
Verwenden Sie [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm), um eine Liste der VM-Instanzen in einer Skalierungsgruppe anzuzeigen. Im folgenden Beispiel werden alle VM-Instanzen in der Skalierungsgruppe namens *myScaleSet* und in der Ressourcengruppe *myResourceGroup* abgerufen. Geben Sie für diese Namen Ihre eigenen Werte an:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Wenn Sie zusätzliche Informationen zu einer bestimmten VM-Instanz anzeigen möchten, fügen Sie [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) den Parameter `-InstanceId` hinzu, und geben Sie die gewünschte Instanz an. Im folgenden Beispiel werden Informationen zur VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* angezeigt. Geben Sie Ihre eigenen Namen wie folgt ein:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändern der Kapazität einer Skalierungsgruppe
Die obigen Befehle dienen zum Anzeigen von Informationen zu Ihrer Skalierungsgruppe und zu den VM-Instanzen. Wenn Sie die Anzahl von Instanzen in der Skalierungsgruppe erhöhen oder verringern möchten, können Sie die Kapazität ändern. Die Skalierungsgruppe erstellt oder entfernt automatisch die erforderliche Anzahl von virtuellen Computern und konfiguriert die virtuellen Computer anschließend für den Empfang von Anwendungsdatenverkehr.

Erstellen Sie zunächst mit [Get-AzVmss](/powershell/module/az.compute/get-azvmss) ein Skalierungsgruppenobjekt, und geben Sie dann einen neuen Wert für `sku.capacity` an. Verwenden Sie zum Anwenden der Kapazitätsänderung [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Im folgenden Beispiel wird die Kapazität der Skalierungsgruppe *myScaleSet* in der Ressourcengruppe *myResourceGroup* in *5* Instanzen geändert. Geben Sie Ihre eigenen Werte wie folgt an:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Die Aktualisierung der Skalierungsgruppenkapazität dauert ein paar Minuten. Wenn Sie die Kapazität einer Skalierungsgruppe verringern, werden die virtuellen Computer mit den höchsten Instanz-IDs zuerst entfernt.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Beenden und Starten von virtuellen Computern in einer Skalierungsgruppe
Verwenden Sie [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss), um virtuelle Computer in einer Skalierungsgruppe zu beenden. Mit dem Parameter `-InstanceId` können Sie die zu beendenden virtuellen Computer angeben. Wenn Sie keine Instanz-ID angeben, werden alle virtuellen Computer in der Skalierungsgruppe beendet. Wenn Sie mehrere virtuelle Computer beenden möchten, trennen Sie die einzelnen Instanz-IDs durch ein Komma.

Im folgenden Beispiel wird die VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* beendet. Geben Sie Ihre eigenen Werte wie folgt an:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Standardmäßig wird die Zuordnung beendeter VMs aufgehoben, woraufhin keine Computegebühren mehr anfallen. Wenn Sie möchten, dass die VM in einem bereitgestellten Zustand verbleibt, nachdem sie beendet wurde, fügen Sie dem vorhergehenden Befehl den Parameter `-StayProvisioned` hinzu. Für beendete virtuelle Computer, die bereitgestellt bleiben, fallen die üblichen Computegebühren an.


### <a name="start-vms-in-a-scale-set"></a>Starten von virtuellen Computern in einer Skalierungsgruppe
Verwenden Sie [Start-AzVmss](/powershell/module/az.compute/start-azvmss), um virtuelle Computer in einer Skalierungsgruppe zu starten. Mit dem Parameter `-InstanceId` können Sie die zu startenden virtuellen Computer angeben. Wenn Sie keine Instanz-ID angeben, werden alle virtuellen Computer in der Skalierungsgruppe gestartet. Wenn Sie mehrere virtuelle Computer starten möchten, trennen Sie die einzelnen Instanz-IDs durch ein Komma.

Im folgenden Beispiel wird die VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* gestartet. Geben Sie Ihre eigenen Werte wie folgt an:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Neustarten von virtuellen Computern in einer Skalierungsgruppe
Verwenden Sie [Restart-AzVmss](/powershell/module/az.compute/restart-azvmss), um virtuelle Computer in einer Skalierungsgruppe neu zu starten. Mit dem Parameter `-InstanceId` können Sie die neu zu startenden virtuellen Computer angeben. Wenn Sie keine Instanz-ID angeben, werden alle virtuellen Computer in der Skalierungsgruppe neu gestartet. Wenn Sie mehrere virtuelle Computer neu starten möchten, trennen Sie die einzelnen Instanz-IDs durch ein Komma.

Im folgenden Beispiel wird die VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* neu gestartet. Geben Sie Ihre eigenen Werte wie folgt an:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Entfernen von virtuellen Computern aus einer Skalierungsgruppe
Verwenden Sie [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss), um virtuelle Computer in einer Skalierungsgruppe zu entfernen. Mit dem Parameter `-InstanceId` können Sie die zu entfernenden virtuellen Computer angeben. Wenn Sie keine Instanz-ID angeben, werden alle virtuellen Computer in der Skalierungsgruppe entfernt. Wenn Sie mehrere virtuelle Computer entfernen möchten, trennen Sie die einzelnen Instanz-IDs durch ein Komma.

Im folgenden Beispiel wird die VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* entfernt. Geben Sie Ihre eigenen Werte wie folgt an:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Nächste Schritte
Weitere gängige Aufgaben für Skalierungsgruppen sind beispielsweise das [Bereitstellen einer Anwendung](virtual-machine-scale-sets-deploy-app.md) und das [Upgraden von VM-Instanzen](virtual-machine-scale-sets-upgrade-scale-set.md). Außerdem können Sie mit Azure PowerShell [Regeln für die automatische Skalierung konfigurieren](virtual-machine-scale-sets-autoscale-overview.md).
