---
title: Steuerung der Wartung für virtuelle Azure-Computer mithilfe von PowerShell
description: Erfahren Sie, wie Sie die Wartung steuern, wenn diese mithilfe der Wartungssteuerung und PowerShell auf Ihre virtuellen Azure-Computer angewendet wird.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 2cc935e81e867609159b5c150b6ee7c346bb9f8e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026147"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>Steuern von Updates mit der Wartungssteuerung und Azure PowerShell

Mit der Wartungssteuerung können Sie entscheiden, wann Sie Plattformupdates der Hostinfrastruktur auf Ihre isolierten VMs und Ihre dedizierten Azure-Hosts anwenden. In diesem Thema werden die Azure PowerShell-Optionen für die Wartungssteuerung behandelt. Weitere Informationen zu den Vorteilen der Verwendung der Wartungssteuerung, ihren Einschränkungen und anderen Verwaltungsoptionen finden Sie unter [Verwalten von Plattformupdates mit der Wartungssteuerung](maintenance-control.md).
 
## <a name="enable-the-powershell-module"></a>Aktivieren des PowerShell-Moduls

Stellen Sie sicher, dass `PowerShellGet` auf dem neuesten Stand ist.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Installieren Sie das `Az.Maintenance`-PowerShell-Modul.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Wenn Sie lokal installieren, stellen Sie sicher, dass Sie die PowerShell-Eingabeaufforderung als Administrator öffnen.

Möglicherweise werden Sie auch aufgefordert zu bestätigen, dass Sie aus einem *nicht vertrauenswürdigen Repository* installieren möchten. Geben Sie `Y` ein, oder wählen Sie **Ja, alle** aus, um das Modul zu installieren.


## <a name="create-a-maintenance-configuration"></a>Erstellen einer Wartungskonfiguration

Erstellen Sie eine Ressourcengruppe als Container für Ihre Konfiguration. In diesem Beispiel wird eine Ressourcengruppe mit dem Namen *myMaintenanceRG* in *eastus* erstellt. Wenn Sie bereits über eine Ressourcengruppe verfügen, die Sie verwenden möchten, können Sie diesen Teil überspringen und den Ressourcengruppennamen in den restlichen Beispielen durch Ihren eigenen ersetzen.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Verwenden Sie [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration), um eine Wartungskonfiguration zu erstellen. In diesem Beispiel wird eine Wartungskonfiguration mit dem Namen *myConfig* erstellt, die für den Host gilt. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Durch die Verwendung von `-MaintenanceScope host` wird sichergestellt, dass die Wartungskonfiguration zum Steuern von Updates für den Host verwendet wird.

Wenn Sie versuchen, eine Konfiguration mit dem gleichen Namen, aber an einem anderen Speicherort zu erstellen, erhalten Sie eine Fehlermeldung. Konfigurationsnamen müssen für Ihre Ressourcengruppe eindeutig sein.

Sie können verfügbare Wartungskonfigurationen abfragen, indem Sie [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration) verwenden.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Erstellen einer Wartungskonfiguration mit einem geplanten Fenster

Sie können auch ein geplantes Fenster deklarieren, in dem Azure die Updates auf Ihre Ressourcen anwendet. In diesem Beispiel wird eine Wartungskonfiguration mit dem Namen myConfig mit einem geplanten Fenster von 5 Stunden am vierten Montag jeden Monats erstellt. Nachdem Sie ein geplantes Fenster erstellt haben, müssen Sie die Updates nicht mehr manuell anwenden.

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> Die **Dauer** der Wartung muss *2 Stunden* oder länger sein. Die Wartung muss mindestens ein Mal in 35 Tagen **wiederholt** werden.

Die **Wiederholung** der Wartung kann täglich, wöchentlich oder monatlich ausgedrückt werden. Beispiele:
 - **täglich**: RecurEvery „Day“ **oder** „3Days“ 
 - **wöchentlich**: RecurEvery „3Weeks“ **oder** „Week Saturday,Sunday“ 
 - **monatlich**: RecurEvery „Month day23,day24“ **oder** „Month Last Sunday“ **oder** „Month Fourth Monday“  
      

## <a name="assign-the-configuration"></a>Zuweisen der Konfiguration

Verwenden Sie [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment), um die Konfiguration Ihrer isolierten VM oder dem Azure Dedicated Host zuzuweisen.

### <a name="isolated-vm"></a>Isolierte VM

Wenden Sie die Konfiguration auf einen virtuellen Computer an, indem Sie die ID der Konfiguration verwenden. Geben Sie `-ResourceType VirtualMachines` an, und geben Sie den Namen der VM für `-ResourceName` sowie die Ressourcengruppe der VM für `-ResourceGroupName` an. 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Dedicated Host

Wenn Sie eine Konfiguration auf einen dedizierten Host anwenden möchten, müssen Sie auch `-ResourceType hosts`, `-ResourceParentName` mit dem Namen der Hostgruppe und `-ResourceParentType hostGroups` einschließen. 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Prüfen auf ausstehende Updates

Verwenden Sie [Get-AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate), um zu überprüfen, ob noch Updates ausstehen. Verwenden Sie `-subscription`, um das Azure-Abonnement der VM anzugeben, wenn diese nicht der Computer ist, bei dem Sie sich angemeldet haben.

Wenn keine Updates zum Anzeigen vorhanden sind, gibt der Befehl keinen Wert zurück. Andernfalls wird ein PSApplyUpdate-Objekt zurückgegeben:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>Isolierte VM

Überprüfen Sie, ob für eine isolierte VM ausstehende Updates angezeigt werden. In diesem Beispiel ist die Ausgabe zur besseren Lesbarkeit als Tabelle formatiert.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Dedicated Host

Überprüfen Sie, ob für einen dedizierten Host ausstehende Updates angezeigt werden. In diesem Beispiel ist die Ausgabe zur besseren Lesbarkeit als Tabelle formatiert. Ersetzen Sie die Werte für die Ressourcen durch Ihre eigenen.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Anwenden von Updates

Verwenden Sie [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate), um ausstehende Updates anzuwenden.

### <a name="isolated-vm"></a>Isolierte VM

Erstellen Sie eine Anforderung, um Updates auf eine isolierte VM anzuwenden.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Bei einer erfolgreichen Ausführung gibt dieser Befehl ein `PSApplyUpdate`-Objekt zurück. Sie können das Name-Attribut im Befehl `Get-AzApplyUpdate` verwenden, um den Aktualisierungsstatus zu überprüfen. Weitere Informationen finden Sie unter [Überprüfen des Updatestatus](#check-update-status).

### <a name="dedicated-host"></a>Dedicated Host

Wenden Sie Updates auf einen dedizierten Host an.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Überprüfen des Updatestatus
Verwenden Sie [Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate), um den Status eines Updates zu überprüfen. Die unten gezeigten Befehle zeigen den Status des neuesten Updates mithilfe von `default` für den Parameter `-ApplyUpdateName` an. Sie können den Namen des Updates ersetzen (vom Befehl [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) zurückgegeben), um den Status eines bestimmten Updates zu erhalten.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime ist die Uhrzeit, zu der das Update vollständig durchgeführt wurde. Dies kann durch Sie initiiert werden oder durch die Plattform, falls das Selbstwartungsfenster nicht genutzt wurde. Wenn noch nie über die Wartungssteuerung ein Update angewandt wurde, wird der Standardwert angezeigt.

### <a name="isolated-vm"></a>Isolierte VM

Suchen Sie nach Updates für einen bestimmten virtuellen Computer.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Dedicated Host

Suchen Sie nach Updates für einen dedizierten Host.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Entfernen einer Wartungskonfiguration

Verwenden Sie [Remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration), um eine Wartungskonfiguration zu löschen.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Wartung und Updates](maintenance-and-updates.md).
