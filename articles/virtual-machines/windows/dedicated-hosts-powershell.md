---
title: Bereitstellen von dedizierten Azure-Hosts mit Azure PowerShell
description: Stellen Sie VMs auf dedizierten Hosts über Azure PowerShell bereit.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/12/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 2f8f2d9eb14e1272af126c9a6d6663f41aaee33f
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005085"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Bereitstellen von VMs auf dedizierten Hosts über Azure PowerShell

Dieser Artikel führt Sie durch die Erstellung eines [dedizierten Azure-Hosts](../dedicated-hosts.md) zum Hosten Ihrer virtuellen Computer (VMs). 

Vergewissern Sie sich, dass die Azure PowerShell-Version 2.8.0 oder höher installiert ist und Sie mithilfe von `Connect-AzAccount` bei einem Azure-Konto angemeldet wurden. 

## <a name="limitations"></a>Einschränkungen

- VM-Skalierungsgruppen werden zurzeit auf dedizierten Hosts nicht unterstützt.
- Die verfügbaren Größen und Hardwaretypen für dedizierte Hosts variieren je nach Region. Weitere Informationen finden Sie unter [Azure Dedicated Host – Preise](https://aka.ms/ADHPricing).

## <a name="create-a-host-group"></a>Erstellen einer Hostgruppe

Eine **Hostgruppe** ist eine Ressource, die eine Sammlung dedizierter Hosts darstellt. Sie erstellen eine Hostgruppe in einer Region und einer Verfügbarkeitszone und fügen ihr Hosts hinzu. Bei der Planung für Hochverfügbarkeit stehen zusätzliche Optionen zur Verfügung. Sie können eine oder beide der folgenden Optionen mit Ihren dedizierten Hosts verwenden: 
- Ausdehnen über mehrere Verfügbarkeitszonen. In diesem Fall muss in jeder Zone, die Sie verwenden möchten, eine Hostgruppe vorhanden sein.
- Ausdehnen über mehrere Fehlerdomänen, die physischen Racks zugeordnet sind. 
 
In beiden Fällen müssen Sie die Anzahl der Fehlerdomänen für Ihre Hostgruppe angeben. Wenn Ihre Gruppe keine Fehlerdomänen umfassen soll, verwenden Sie die Anzahl 1 von Fehlerdomänen. 

Sie können auch sowohl Verfügbarkeitszonen als auch Fehlerdomänen verwenden. In diesem Beispiel wird eine Hostgruppe in Zone 1 mit zwei Fehlerdomänen erstellt. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


Fügen Sie den Parameter `-SupportAutomaticPlacement true` hinzu, damit Ihre VMs und Skalierungsgruppeninstanzen automatisch auf Hosts innerhalb einer Hostgruppe platziert werden. Weitere Informationen finden Sie unter [Manuelle und automatische Platzierung im Vergleich](../dedicated-hosts.md#manual-vs-automatic-placement).


## <a name="create-a-host"></a>Erstellen eines Hosts

Nun erstellen Sie einen dedizierten Host in der Hostgruppe. Zusätzlich zu einem Namen müssen Sie die SKU für den Host angeben. Die Host-SKU erfasst die unterstützte VM-Serie sowie die Hardwaregenerierung für Ihren dedizierten Host.

Weitere Informationen zu den Host-SKUs und Preisen finden Sie unter [Azure Dedicated Host – Preise](https://aka.ms/ADHPricing).

Beim Festlegen der Anzahl der Fehlerdomänen für die Hostgruppe werden Sie aufgefordert, die Fehlerdomäne für den Host anzugeben. In diesem Beispiel wird die Fehlerdomäne für den Host auf „1“ festgelegt.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Erstellen einer VM

Erstellen Sie einen virtuellen Computer auf dem dedizierten Host. 

Wenn Sie beim Erstellen der Hostgruppe eine Verfügbarkeitszone angegeben haben, müssen Sie beim Erstellen des virtuellen Computers dieselbe Zone verwenden. Da sich in diesem Beispiel die Hostgruppe in Zone 1 befindet, müssen wir die VM in Zone 1 erstellen.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Wenn Sie einen virtuellen Computer auf einem Host erstellen, der nicht über ausreichende Ressourcen verfügt, wird der virtuelle Computer im Zustand „Fehler“ erstellt. 

## <a name="check-the-status-of-the-host"></a>Überprüfen des Status des Hosts

Sie können mit [GetAzHost](/powershell/module/az.compute/get-azhost) mit dem Parameter `-InstanceView` den Integritätsstatus des Hosts sowie die Anzahl von virtuellen Computern überprüfen, die Sie noch auf dem Host bereitstellen können.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Die Ausgabe sieht etwa wie folgt aus:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe 

Wenn Sie eine Skalierungsgruppe bereitstellen, geben Sie die Hostgruppe an.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

Wenn Sie den Host, auf dem die Skalierungsgruppe bereitgestellt werden soll, manuell auswählen möchten, fügen Sie `--host` und den Namen des Hosts hinzu.



## <a name="add-an-existing-vm"></a>Hinzufügen eines vorhandenen virtuellen Computers 

Sie können einem dedizierten Host eine VM hinzufügen. Diese muss allerdings zuerst beendet bzw. Ihre Zuordnung muss aufgehoben werden. Vergewissern Sie sich vor dem Verschieben eines virtuellen Computers auf einen dedizierten Host, dass die VM-Konfiguration unterstützt wird:

- Die VM-Größe muss sich in der gleichen Größenfamilie befinden wie der dedizierte Host. Wenn der dedizierte Host z. B. DSv3 ist, kann die VM-Größe Standard_D4s_v3, aber nicht Standard_A4_v2 sein. 
- Der virtuelle Computer muss sich in der gleichen Region befinden wie der dedizierte Host.
- Der virtuelle Computer darf nicht Teil einer Näherungsplatzierungsgruppe sein. Entfernen Sie den virtuellen Computer aus der Näherungsplatzierungsgruppe, bevor Sie ihn auf einen dedizierten Host verschieben. Weitere Informationen finden Sie unter [Verschieben einer vorhandenen VM aus einer Näherungsplatzierungsgruppe](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group).
- Der virtuelle Computer darf sich nicht in einer Verfügbarkeitsgruppe befinden.
- Wenn sich der virtuelle Computer in einer Verfügbarkeitszone befindet, muss es sich dabei um die gleiche Verfügbarkeitszone handeln wie bei der Hostgruppe. Die Verfügbarkeitszoneneinstellungen für den virtuellen Computer und die Hostgruppe müssen identisch sein.

Ersetzen Sie die Werte der Variablen durch Ihre eigenen Informationen.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Bereinigung

Ihre dedizierten Hosts werden Ihnen auch dann in Rechnung gestellt, wenn keine virtuellen Computer bereitgestellt sind. Sie sollten, um Kosten zu sparen, alle Hosts löschen, die Sie zurzeit nicht verwenden.  

Ein Host kann nur gelöscht werden, wenn er nicht mehr von virtuellen Computern verwendet wird. Löschen Sie die VMs mit [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Nachdem Sie die VMs gelöscht haben, können Sie den Host mithilfe von [Remove-AzHost](/powershell/module/az.compute/remove-azhost) löschen.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Wenn Sie alle Hosts gelöscht haben, können Sie die Hostgruppe mit [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup) löschen. 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Sie können mithilfe von [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) auch die gesamte Ressourcengruppe mit einem einzigen Befehl löschen. Dabei werden alle in der Gruppe erstellten Ressourcen gelöscht, einschließlich aller VMs, Hosts und Hostgruppen.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Nächste Schritte

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) finden Sie eine Beispielvorlage, die sowohl Zonen als auch Fehlerdomänen für maximale Resilienz in einer Region verwendet.

- Sie können dedizierte Hosts auch über das [Azure-Portal](../dedicated-hosts-portal.md) bereitstellen.