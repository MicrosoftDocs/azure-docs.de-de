---
title: Tutorial – Erstellen und Verwenden von Datenträgern für Skalierungsgruppen mit Azure PowerShell | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Azure PowerShell zum Erstellen und Verwenden von Managed Disks mit einer VM-Skalierungsgruppe verwenden, z.B. das Hinzufügen, Vorbereiten, Auflisten und Trennen von Datenträgern.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6035a6ddd690db456edfa5777ca2d41e4be8b919
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728582"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>Tutorial: Erstellen und Verwalten von Datenträgern mit VM-Skalierungsgruppe mit Azure PowerShell

Für VM-Skalierungsgruppen werden Datenträger zum Speichern des Betriebssystems, der Anwendungen und der Daten von VM-Instanzen verwendet. Beim Erstellen und Verwalten einer Skalierungsgruppe muss darauf geachtet werden, eine für den erwarteten Workload geeignete Datenträgergröße und -konfiguration auszuwählen. In diesem Tutorial wird beschrieben, wie Sie VM-Datenträger erstellen und verwalten. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Betriebssystemdatenträger und temporäre Datenträger
> * Datenträger
> * Standard- und Premium-Datenträger
> * Datenträgerleistung
> * Anfügen und Vorbereiten von Datenträgern

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="default-azure-disks"></a>Azure-Standarddatenträger
Wenn eine Skalierungsgruppe erstellt oder skaliert wird, werden automatisch zwei Datenträger an jede VM-Instanz angefügt. 

**Betriebssystem-Datenträger**: Betriebssystem-Datenträger können in der Größe auf bis zu 2 TB angepasst werden und hosten das Betriebssystem der VM. Der Betriebssystem-Datenträger wird standardmäßig mit */dev/sda* bezeichnet. Die Konfiguration der Datenträgerzwischenspeicherung des Betriebssystem-Datenträgers ist für die Leistung des Betriebssystems optimiert. Aufgrund dieser Konfiguration sollte der Betriebssystem-Datenträger **nicht** Anwendungen oder Daten hosten. Verwenden Sie für Anwendungen und Daten einen Datenträger. Dies wird weiter unten in diesem Artikel ausführlich erläutert. 

**Temporärer Datenträger**: Für temporäre Datenträger wird ein Solid State Drive verwendet, das sich auf demselben Azure-Host wie die VM-Instanz befindet. Dies sind äußerst leistungsfähige Datenträger, die für Vorgänge wie die temporäre Datenverarbeitung verwendet werden können. Wenn die VM-Instanz aber auf einen neuen Host verschoben wird, werden alle auf einem temporären Datenträger gespeicherten Daten entfernt. Die Größe des temporären Datenträgers richtet sich nach der Größe der VM-Instanz. Temporäre Datenträger werden mit bezeichnet */dev/sdb* und haben den Bereitstellungspunkt */mnt*.

### <a name="temporary-disk-sizes"></a>Größe von temporären Datenträgern
| Type | Gängige Größen | Max. Größe des temporären Datenträgers (GiB) |
|----|----|----|
| [Allgemeiner Zweck](../virtual-machines/windows/sizes-general.md) | A-, B- und D-Serie | 1600 |
| [Computeoptimiert](../virtual-machines/windows/sizes-compute.md) | F-Serie | 576 |
| [Arbeitsspeicheroptimiert](../virtual-machines/windows/sizes-memory.md) | D-, E-, G- und M-Serie | 6.144 |
| [Speicheroptimiert](../virtual-machines/windows/sizes-storage.md) | L-Serie | 5.630 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | N-Serie | 1.440 |
| [Hohe Leistung](../virtual-machines/windows/sizes-hpc.md) | A- und H-Serie | 2000 |


## <a name="azure-data-disks"></a>Azure-Datenträger
Zusätzliche Datenträger können hinzugefügt werden, wenn Sie Anwendungen installieren und Daten speichern müssen. Datenträger sollten in allen Fällen verwendet werden, in denen eine dauerhafte und dynamische Datenspeicherung erwünscht ist. Jeder Datenträger weist eine maximale Kapazität von 4 TB auf. Die Größe der VM-Instanz bestimmt, wie viele Datenträger angefügt werden können. Für jede vCPU eines virtuellen Computers können zwei Datenträger angefügt werden.

### <a name="max-data-disks-per-vm"></a>Max. Anzahl der Datenträger pro virtuellem Computer
| Type | Gängige Größen | Max. Anzahl der Datenträger pro virtuellem Computer |
|----|----|----|
| [Allgemeiner Zweck](../virtual-machines/windows/sizes-general.md) | A-, B- und D-Serie | 64 |
| [Computeoptimiert](../virtual-machines/windows/sizes-compute.md) | F-Serie | 64 |
| [Arbeitsspeicheroptimiert](../virtual-machines/windows/sizes-memory.md) | D-, E-, G- und M-Serie | 64 |
| [Speicheroptimiert](../virtual-machines/windows/sizes-storage.md) | L-Serie | 64 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | N-Serie | 64 |
| [Hohe Leistung](../virtual-machines/windows/sizes-hpc.md) | A- und H-Serie | 64 |


## <a name="vm-disk-types"></a>VM-Datenträgertypen
In Azure stehen zwei verschiedene Datenträgertypen zur Verfügung.

### <a name="standard-disk"></a>Standarddatenträger
Standardspeicher basiert auf Festplatten und stellt eine kostengünstige Speicherlösung mit hoher Leistung dar. Standarddatenträger sind ideal für eine kostengünstige Entwicklungs- und Testworkload.

### <a name="premium-disk"></a>Premium-Datenträger
Premium-Datenträger basieren auf SSD-basierten Datenträgern mit hoher Leistung und geringer Wartezeit. Diese Datenträger werden für VMs empfohlen, auf denen Workloads für die Produktion ausgeführt werden. Storage Premium unterstützt virtuelle Computer der DS-, DSv2-, GS- und FS-Serie. Bei Auswahl einer Datenträgergröße wird der Wert auf den nächsten Datenträgertyp aufgerundet. Liegt die Größe des Datenträgers z.B. unter 128GB, ist der Datenträgertyp P10. Wenn die Größe des Datenträgers zwischen 129 und 512GB liegt, ist die Größe P20. Bei mehr als 512 GB ist die Größe P30.

### <a name="premium-disk-performance"></a>Leistung von Premium-Datenträgern
|Storage Premium-Datenträgertyp | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Datenträgergröße (aufgerundet) | 32 GB | 64 GB | 128 GB | 512 GB | 1.024GB (1TB) | 2.048 GB (2 TB) | 4.095 GB (4 TB) |
| Max. IOPS pro Datenträger | 120 | 240 | 500 | 2.300 | 5.000 | 7.500 | 7.500 |
Durchsatz pro Datenträger | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

In dieser Tabelle ist zwar die maximale IOPS-Anzahl pro Datenträger angegeben, eine höhere Leistung kann aber durch Striping mehrerer Datenträger erreicht werden. Eine Standard_GS5-VM kann z.B. ein Maximum von 80.000 IOPS erreichen. Ausführliche Informationen zur maximalen IOPS-Anzahl pro virtuellem Computer finden Sie unter [Größen für virtuelle Windows-Computer in Azure](../virtual-machines/windows/sizes.md).


## <a name="create-and-attach-disks"></a>Erstellen und Anfügen von Datenträgern
Sie können Datenträger beim Erstellen einer Skalierungsgruppe oder für eine vorhandene Skalierungsgruppe erstellen und anfügen.

### <a name="attach-disks-at-scale-set-creation"></a>Anfügen von Datenträgern bei der Erstellung einer Skalierungsgruppe
Erstellen Sie mit [New-AzVmss](/powershell/module/az.compute/new-azvmss) eine VM-Skalierungsgruppe. Geben Sie bei entsprechender Aufforderung einen Benutzernamen und ein Kennwort für die VM-Instanzen an. Um Datenverkehr an die einzelnen VM-Instanzen zu verteilen, wird auch ein Lastenausgleich erstellt. Der Lastenausgleich enthält Regeln zum Verteilen von Datenverkehr über TCP-Port 80 und zum Zulassen von Remotedesktop-Datenverkehr über TCP-Port 3389 und PowerShell-Remoting über TCP-Port 5985.

Mit dem Parameter `-DataDiskSizeGb` werden zwei Datenträger erstellt. Der erste Datenträger hat eine Größe von *64* GB, und der zweite Datenträger hat eine Größe von *128* GB. Geben Sie Ihre gewünschten Administratoranmeldeinformationen für die VM-Instanzen in der Skalierungsgruppe an, wenn Sie dazu aufgefordert werden:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

Die Erstellung und Konfiguration aller Ressourcen und VM-Instanzen der Skalierungsgruppe dauert einige Minuten.

### <a name="attach-a-disk-to-existing-scale-set"></a>Anfügen eines Datenträgers an eine vorhandene Skalierungsgruppe
Sie können Datenträger auch an eine vorhandene Skalierungsgruppe anfügen. Verwenden Sie die im vorherigen Schritt erstellte Skalierungsgruppe, um mit [Add-AzVmssDataDisk](/powershell/module/az.compute/add-azvmssdatadisk) einen weiteren Datenträger hinzuzufügen. Im folgenden Beispiel wird an eine Skalierungsgruppe ein weiterer Datenträger mit *128* GB angefügt:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>Vorbereiten der Datenträger
Bei den Datenträgern, die erstellt und an die VM-Instanzen Ihrer Skalierungsgruppe angefügt werden, handelt es sich um unformatierte Datenträger. Bevor Sie diese für Ihre Daten und Anwendungen nutzen können, müssen die Datenträger vorbereitet werden. Zum Vorbereiten der Datenträger erstellen Sie eine Partition und ein Dateisystem und stellen diese bereit.

Sie können die benutzerdefinierte Skripterweiterung von Azure verwenden, um den Prozess für mehrere VM-Instanzen einer Skalierungsgruppe zu automatisieren. Mit dieser Erweiterung können Skripts lokal auf jeder VM-Instanz ausgeführt werden, z.B. um angefügte Datenträger vorzubereiten. Weitere Informationen finden Sie unter [Übersicht über benutzerdefinierte Skripterweiterungen](../virtual-machines/windows/extensions-customscript.md).


Im folgenden Beispiel wird mit [Add-AzVmssExtension](/powershell/module/az.compute/Add-AzVmssExtension) ein Skript aus einem GitHub-Beispielrepository auf jeder VM-Instanz ausgeführt, um alle angefügten unformatierten Datenträger vorzubereiten:


```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Stellen Sie eine RDP-Verbindung mit einer der VM-Instanzen her, um zu bestätigen, dass die Datenträger richtig vorbereitet wurden. 

Rufen Sie zuerst mit [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer) das Lastenausgleichsobjekt ab. Zeigen Sie anschließend mit [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig) die NAT-Regeln für eingehenden Datenverkehr an. Mit den NAT-Regeln wird jeweils der *FrontendPort* für eine VM-Instanz aufgelistet, über die per RDP gelauscht wird. Rufen Sie abschließend mit [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) die öffentliche IP-Adresse des Lastenausgleichs ab:


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

Geben Sie zum Herstellen einer Verbindung mit Ihrer VM Ihre eigene öffentliche IP-Adresse und Portnummer der erforderlichen VM-Instanz an, wie in den vorherigen Befehlen zu sehen. Geben Sie bei entsprechender Aufforderung die beim Erstellen der Skalierungsgruppe verwendeten Anmeldeinformationen ein. Führen Sie diesen Schritt bei Verwendung von Azure Cloud Shell an einer lokalen PowerShell-Eingabeaufforderung oder über einen Remotedesktopclient aus. Im folgenden Beispiel wird eine Verbindung mit der VM-Instanz *1* hergestellt:

```powershell
mstsc /v 52.168.121.216:50001
```

Öffnen Sie auf der VM-Instanz eine lokale PowerShell-Sitzung, und sehen Sie sich mit [Get-Disk](/powershell/module/storage/get-disk) die verbundenen Datenträger an:

```powershell
Get-Disk
```

In der folgenden Beispielausgabe ist zu sehen, dass die drei Datenträger an die VM-Instanz angefügt sind.

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

Überprüfen Sie die Dateisysteme und Bereitstellungspunkte auf der VM-Instanz wie folgt:

```powershell
Get-Partition
```

In der folgenden Beispielausgabe ist zu sehen, dass den drei Datenträgern Laufwerkbuchstaben zugewiesen sind:

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

Die Datenträger auf den einzelnen VM-Instanzen in Ihrer Skalierung werden auf die gleiche Weise automatisch vorbereitet. Wenn Ihre Skalierungsgruppe zentral hochskaliert wird, werden die erforderlichen Datenträger an die neuen VM-Instanzen angefügt. Die benutzerdefinierte Skripterweiterung wird ebenfalls automatisch ausgeführt, um die Datenträger vorzubereiten.

Schließen Sie die Remotedesktopverbindung mit der VM-Instanz.


## <a name="list-attached-disks"></a>Auflisten von angefügten Datenträgern
Verwenden Sie [Get-AzVmss](/powershell/module/az.compute/get-azvmss) wie folgt, um Informationen zu den Datenträgern anzuzeigen, die an eine Skalierungsgruppe angefügt sind:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

Unter der *VirtualMachineProfile.StorageProfile*-Eigenschaft wird die Liste mit *DataDisks* angezeigt. Es werden Informationen zur Datenträgergröße, Speicherebene und logischen Gerätenummer (Logical Unit Number, LUN) angezeigt. Die folgende Beispielausgabe enthält ausführliche Informationen zu den drei Datenträgern, die an die Skalierungsgruppe angefügt sind:

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>Trennen eines Datenträgers
Wenn Sie einen bestimmten Datenträger nicht mehr benötigen, können Sie ihn von der Skalierungsgruppe trennen. Der Datenträger wird aus allen VM-Instanzen in der Skalierungsgruppe entfernt. Verwenden Sie [Remove-AzVmssDataDisk](/powershell/module/az.compute/remove-azvmssdatadisk), und geben Sie die LUN des Datenträgers an, um einen Datenträger von einer Skalierungsgruppe zu trennen. Die LUNs sind in der Ausgabe von [Get-AzVmss](/powershell/module/az.compute/get-azvmss) im vorherigen Abschnitt enthalten. Im folgenden Beispiel wird LUN *3* von der Skalierungsgruppe getrennt:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie mit [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) die Ressourcengruppe und alle dazugehörigen Ressourcen, um Ihre Skalierungsgruppe und die Datenträger zu entfernen. Der Parameter `-Force` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten. Der Parameter `-AsJob` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde beschrieben, wie Sie Datenträger mit Skalierungsgruppen mithilfe von Azure PowerShell erstellen und verwenden:

> [!div class="checklist"]
> * Betriebssystemdatenträger und temporäre Datenträger
> * Datenträger
> * Standard- und Premium-Datenträger
> * Datenträgerleistung
> * Anfügen und Vorbereiten von Datenträgern

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie ein benutzerdefiniertes Image für Ihre in Skalierungsgruppen enthaltenen VM-Instanzen verwenden.

> [!div class="nextstepaction"]
> [Verwenden eines benutzerdefinierten Images für VM-Instanzen von Skalierungsgruppen](tutorial-use-custom-image-powershell.md)
