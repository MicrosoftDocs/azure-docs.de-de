---
title: Ultra-Datenträger für VMs – verwaltete Azure-Datenträger
description: Informationen zu Ultra-Datenträgern für virtuelle Azure-Computer
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/28/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: aa1c681d4b34199456f3447bcac5587005a044ce
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96016629"
---
# <a name="using-azure-ultra-disks"></a>Verwenden von Azure Ultra-Datenträgern

In diesem Artikel wird erläutert, wie eine Disk Ultra bereitgestellt und verwendet wird. Konzeptionelle Informationen zu Disk Ultra finden Sie unter [Welche Datenträgertypen sind in Azure verfügbar?](disks-types.md#ultra-disk).

Azure Ultra-Datenträger bieten hohen Durchsatz, einen hohen IOPS-Wert und einen Datenträgerspeicher mit durchgängig geringer Latenz für virtuelle Azure IaaS-Computer (VMs). Dieses neue Angebot bietet Spitzenleistung auf den gleichen Verfügbarkeitsebenen wie unsere vorhandenen Datenträgerangebote. Ein Hauptvorteil von Ultra-Datenträgern ist die Möglichkeit zum dynamischen Ändern der SSD-Leistung zusammen mit Ihren Workloads, ohne dass Sie Ihre VMs neu starten müssen. Ultra-Datenträger eignen sich für datenintensive Workloads wie SAP HANA, führende Datenbanksysteme und Workloads mit vielen Transaktionen.

## <a name="ga-scope-and-limitations"></a>Umfang und Einschränkungen für allgemeine Verfügbarkeit

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Ermitteln der VM-Größe und Regionsverfügbarkeit

### <a name="vms-using-availability-zones"></a>Virtuelle Computer mit Verfügbarkeitszonen

Sie müssen ermitteln, in welcher Verfügbarkeitszone Sie sich befinden, um Ultra-Datenträger verwenden zu können. Nicht jede Region unterstützt jede VM-Größe bei Ultra-Datenträgern. Führen Sie einen der folgenden Befehle aus, um zu ermitteln, ob Ihre Region, Zone und VM-Größe Ultra-Datenträger unterstützt. Ersetzen Sie dazu die Werte für **region**, **vmSize** und **subscription** entsprechend:

#### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

Die Antwort liegt in der nachstehend gezeigten Form vor, wobei X für die Zone steht, die für die Bereitstellung in Ihrer ausgewählten Region zu verwenden ist. X kann entweder 1, 2 oder 3 sein.

Behalten Sie den Wert für **Zones** bei. Er steht für Ihre Verfügbarkeitszone, und Sie benötigen ihn zum Bereitstellen eines Ultra-Datenträgers.

|ResourceType  |Name  |Position  |Zones  |Einschränkung  |Funktion  |Wert  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Kommt vom Befehl keine Antwort, wird die ausgewählte VM-Größe für Ultra-Datenträger in der ausgewählten Region nicht unterstützt.

Nachdem Sie nun wissen, in welcher Zone die Bereitstellung erfolgen muss, führen Sie die Bereitstellungsschritte in diesem Artikel aus, um entweder einen virtuellen Computer mit einem zugeordneten Ultra-Datenträger bereitzustellen oder einem vorhandenen virtuellen Computer einen Ultra-Datenträger zuzuordnen.

### <a name="vms-with-no-redundancy-options"></a>Virtuelle Computer ohne Redundanzoptionen

Die in den ausgewählten Regionen bereitgestellten Disk Ultra-Datenträger müssen vorerst ohne Redundanzoptionen bereitgestellt werden. Jedoch ist möglicherweise nicht jede Datenträgergröße, für die Disk Ultra-Datenträger unterstützt werden, in diesen Regionen verfügbar. Sie können einen der folgenden Codeausschnitte verwenden, um zu bestimmen, für welche Datenträgergrößen Disk Ultra-Datenträger unterstützt werden. Stellen Sie sicher, dass Sie zuerst die Werte `vmSize` und `subscription` ersetzen:

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

Die Antwort ist ähnlich der folgenden Form. `UltraSSDAvailable   True` gibt an, ob die Größe des virtuellen Computers Disk Ultra-Datenträger in dieser Region unterstützt.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Bereitstellen eines Ultra-Datenträgers über Azure Resource Manager

Bestimmen Sie zuerst die Größe des bereitzustellenden virtuellen Computers. Eine Liste der unterstützten VM-Größen finden Sie im Abschnitt [Umfang und Einschränkungen für allgemeine Verfügbarkeit](#ga-scope-and-limitations).

Wenn Sie einen virtuellen Computer mit mehreren Ultra-Datenträgern erstellen möchten, hilft Ihnen das Beispiel zum [Erstellen einer VM mit mehreren Ultra-Datenträgern](https://aka.ms/ultradiskArmTemplate) weiter.

Falls Sie Ihre eigene Vorlage verwenden möchten, sollten Sie sicherstellen, dass **apiVersion** für `Microsoft.Compute/virtualMachines` und `Microsoft.Compute/Disks` auf `2018-06-01` (oder höher) festgelegt ist.

Legen Sie die Datenträger-SKU auf **UltraSSD_LRS** fest, und geben Sie anschließend die Datenträgerkapazität, den IOPS-Wert, die Verfügbarkeitszone und den Durchsatz in MBit/s an, um einen Ultra-Datenträger zu erstellen.

Nachdem der virtuelle Computer bereitgestellt wurde, können Sie die Datenträger für Daten partitionieren und formatieren sowie für Ihre Workloads konfigurieren.


## <a name="deploy-an-ultra-disk"></a>Bereitstellen eines Ultra-Datenträgers

# <a name="portal"></a>[Portal](#tab/azure-portal)

In diesem Abschnitt wird die Bereitstellung eines virtuellen Computers mit einem Ultra-Datenträger als Datenträger beschrieben. Dabei wird davon ausgegangen, dass Sie mit der Bereitstellung eines virtuellen Computers vertraut sind. Andernfalls finden Sie entsprechende Informationen in [Schnellstart: Erstellen eines virtuellen Windows-Computers im Azure-Portal](./windows/quick-create-portal.md).

- Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an und navigieren Sie zur Bereitstellung eines virtuellen Computers.
- Achten Sie darauf, dass Sie eine [unterstützte VM-Größe und Region](#ga-scope-and-limitations) auswählen.
- Wählen Sie **Verfügbarkeitszone** in **Verfügbarkeitsoptionen** aus.
- Geben Sie die gewünschten Werte für die restlichen Einstellungen an.
- Wählen Sie **Datenträger** aus.

![Screenshot des Arbeitsablaufs zur VM-Erstellung, Blatt „Grundlegendes“.](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Wählen Sie auf dem Blatt „Datenträger“ **Ja** für **Ultra-Datenträgerkompatibilität aktivieren** aus.
- Wählen Sie **Neuen Datenträger erstellen und anfügen** aus, um jetzt einen Ultra-Datenträger anzufügen.

![Screenshot: Arbeitsablauf zur VM-Erstellung, Blatt „Datenträger“, „Ultra“ ist aktiviert, die Optionen zum Erstellen und Anfügen eines neuen Datenträgers sind hervorgehoben.](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Geben Sie auf dem Blatt **Neuen Datenträger erstellen** einen Namen ein und wählen Sie dann **Größe ändern** aus.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-create-new-disk-flow.png" alt-text="Screenshot des Blatts „Neuen Datenträger erstellen“ mit hervorgehobener Option zum Ändern der Größe.":::


- Ändern Sie den **Speichertyp** in **Disk Ultra**.
- Ändern Sie die Werte für **Benutzerdefinierte Datenträgergröße (GiB)** , **Datenträger-IOPS** und **Datenträgerdurchsatz** in die gewünschten Werte.
- Wählen Sie in beiden Blättern **OK** aus.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="Screenshot des Blatts zur Auswahl der Datenträgergröße mit ausgewähltem Speichertyp „Disk Ultra“, weitere Werte hervorgehoben.":::

- Setzen Sie die VM-Bereitstellung wie bei der Bereitstellung jeder anderen VM fort.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Bestimmen Sie zuerst die Größe des bereitzustellenden virtuellen Computers. Eine Liste der unterstützten VM-Größen finden Sie im Abschnitt [Umfang und Einschränkungen für allgemeine Verfügbarkeit](#ga-scope-and-limitations).

Sie müssen einen virtuellen Computer erstellen, der Ultra-Datenträger verwenden kann. Nur dann können Sie einen Ultra-Datenträger zuordnen.

Ersetzen Sie die Variablen **$vmname**, **$rgname**, **$diskname**, **$location**, **$password** und **$user** durch Ihre eigenen Werte, bzw. legen Sie sie fest. Legen Sie **$zone** auf den Wert Ihrer Verfügbarkeitszone fest, den Sie am [Anfang dieses Artikels](#determine-vm-size-and-region-availability) ermittelt haben. Führen Sie anschließend den folgenden CLI-Befehl aus, um eine Ultra-fähige VM zu erstellen:

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname

#create an ultra disk with 512 sector size
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400 --logical-sector-size 512
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bestimmen Sie zuerst die Größe des bereitzustellenden virtuellen Computers. Eine Liste der unterstützten VM-Größen finden Sie im Abschnitt [Umfang und Einschränkungen für allgemeine Verfügbarkeit](#ga-scope-and-limitations).

Um Ultra-Datenträger zu verwenden, müssen Sie eine VM erstellen, für die das Verwenden von Ultra-Datenträgern unterstützt wird. Ersetzen Sie die Variablen **$resourcegroup** und **$vmName** durch Ihre eigenen Werte. Legen Sie **$zone** auf den Wert Ihrer Verfügbarkeitszone fest, den Sie am [Anfang dieses Artikels](#determine-vm-size-and-region-availability) ermittelt haben. Führen Sie anschließend den folgenden [New-AzVm](/powershell/module/az.compute/new-azvm)-Befehl aus, um eine Ultra-fähige VM zu erstellen:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-and-attach-the-disk"></a>Erstellen und Anfügen des Datenträgers

Nachdem Ihre VM bereitgestellt wurde, können Sie einen Disk Ultra-Datenträger erstellen und ihn ihr anfügen, indem Sie das folgende Skript verwenden:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup

# Example for creating a disk with 512 sector size
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-LogicalSectorSize 512 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

```

---
## <a name="attach-an-ultra-disk"></a>Anfügen eines Disk Ultra-Datenträgers

# <a name="portal"></a>[Portal](#tab/azure-portal)

Alternativ können Sie, wenn sich Ihre vorhandene VM in einer Region/Verfügbarkeitszone befindet, in der Ultra-Datenträger verwendet werden können, Ultra-Datenträger verwenden, ohne eine neue VM erstellen zu müssen. Dazu aktivieren Sie Ultra-Datenträger in der vorhandenen VM und fügen diese dann als Datenträger an. Sie müssen den virtuellen Computer beenden, um die Disk Ultra-Datenträgerkompatibilität zu aktivieren. Nach dem Beenden der VM können Sie Kompatibilität aktivieren und die VM dann erneut starten. Nachdem Kompatibilität aktiviert wurde, können Sie einen Disk Ultra-Datenträger anfügen:

- Navigieren Sie zu Ihrem virtuellen Computer, beenden Sie ihn, und warten Sie die Aufhebung seiner Zuordnung ab.
- Nachdem die Zuordnung Ihrer VM aufgehoben wurde, wählen Sie **Datenträger** aus.
- Wählen Sie **Bearbeiten** aus.

![Screenshot eines vorhandenen Blatts für einen VM-Datenträger mit hervorgehobener Option „Bearbeiten“.](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Wählen Sie **Ja** für **Ultra-Datenträgerkompatibilität aktivieren** aus.

![Screenshot zum Aktivieren der Disk Ultra-Datenträgerkompatibilität.](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Wählen Sie **Speichern** aus.
- Wählen Sie **Datenträger hinzufügen** und dann im Dropdown für **Name** die Option **Datenträger erstellen** aus.

![Screenshot eines Datenträgerblatts, Hinzufügen eines neuen Datenträgers.](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Geben Sie einen Namen für den neuen Datenträger ein und wählen Sie dann **Größe ändern** aus.
- Ändern Sie den **Kontotyp** in **Ultra-Datenträger**.
- Ändern Sie die Werte für **Benutzerdefinierte Datenträgergröße (GiB)** , **Datenträger-IOPS** und **Datenträgerdurchsatz** in die gewünschten Werte.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="Screenshot des Blatts zur Auswahl der Datenträgergröße mit ausgewähltem Speichertyp „Disk Ultra“, weitere Werte hervorgehoben.":::

- Wählen Sie **OK** und anschließend **Erstellen** aus.
- Wählen Sie nach Rückkehr zum Blatt für den Datenträger **Speichern** aus.
- Starten Sie Ihre VM erneut.

![Screenshot des Datenträgerblatts für Ihre VM.](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Alternativ können Sie, wenn sich Ihre vorhandene VM in einer Region/Verfügbarkeitszone befindet, in der Ultra-Datenträger verwendet werden können, Ultra-Datenträger verwenden, ohne eine neue VM erstellen zu müssen.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>Aktivieren der Disk Ultra-Datenträgerkompatibilität auf einem vorhandenen virtuellen Computer: Befehlszeilenschnittstelle

Wenn Ihre VM die in [Umfang und Einschränkungen für allgemeine Verfügbarkeit](#ga-scope-and-limitations) genannten Anforderungen erfüllt und sich in der [geeigneten Zone für Ihr Konto](#determine-vm-size-and-region-availability) befindet, können Sie auf Ihrem virtuellen Computer die Disk Ultra-Datenträgerkompatibilität aktivieren.

Sie müssen den virtuellen Computer beenden, um die Disk Ultra-Datenträgerkompatibilität zu aktivieren. Nach dem Beenden der VM können Sie Kompatibilität aktivieren und die VM dann erneut starten. Nachdem Kompatibilität aktiviert wurde, können Sie einen Disk Ultra-Datenträger anfügen:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>Erstellen eines Disk Ultra-Datenträgers: Befehlszeilenschnittstelle

Nachdem Sie nun über eine VM verfügen, der Ultra-Datenträger zugeordnet werden können, können Sie einen Ultra-Datenträger erstellen und diesen der VM zuordnen.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="attach-the-disk---cli"></a>Anfügen des Datenträgers: Befehlszeilenschnittstelle

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Alternativ können Sie, wenn sich Ihre vorhandene VM in einer Region/Verfügbarkeitszone befindet, in der Ultra-Datenträger verwendet werden können, Ultra-Datenträger verwenden, ohne eine neue VM erstellen zu müssen.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>Aktivieren der Disk Ultra-Datenträgerkompatibilität auf einem vorhandenen virtuellen Computer: PowerShell

Wenn Ihre VM die in [Umfang und Einschränkungen für allgemeine Verfügbarkeit](#ga-scope-and-limitations) genannten Anforderungen erfüllt und sich in der [geeigneten Zone für Ihr Konto](#determine-vm-size-and-region-availability) befindet, können Sie auf Ihrem virtuellen Computer die Disk Ultra-Datenträgerkompatibilität aktivieren.

Sie müssen den virtuellen Computer beenden, um die Disk Ultra-Datenträgerkompatibilität zu aktivieren. Nach dem Beenden der VM können Sie Kompatibilität aktivieren und die VM dann erneut starten. Nachdem Kompatibilität aktiviert wurde, können Sie einen Disk Ultra-Datenträger anfügen:

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>Erstellen und Anfügen eines Ultra Disk-Datenträgers: PowerShell

Nachdem Sie nun über eine VM verfügen, für die Ultra-Datenträger verwendet werden können, können Sie einen Ultra-Datenträger erstellen und diesen der VM zuordnen:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>Anpassen der Leistung eines Disk Ultra-Datenträgers

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ultra-Datenträger haben eine einzigartige Funktion, mit der Sie deren Leistung anpassen können. Sie können diese Anpassung über das Azure-Portal für die jeweiligen Datenträger durchführen.

- Navigieren Sie zur VM und wählen Sie **Datenträger** aus.
- Wählen Sie den Ultra-Datenträger aus, dessen Leistung Sie ändern möchten.

![Screenshot des Datenträgerblatts Ihrer VM mit hervorgehobenem Disk Ultra-Datenträger.](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Wählen Sie **Konfiguration** aus und nehmen Sie dann die gewünschten Änderungen vor.
- Wählen Sie **Speichern** aus.

![Screenshot des Konfigurationsblatts für Ihren Disk Ultra-Datenträger mit hervorgehobenen Optionen für Datenträgergröße, IOPS, Durchsatz und Speichern.](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Ultra-Datenträger haben eine einzigartige Funktion, mit der Sie deren Leistung anpassen können. Mit dem folgenden Befehl wird die Verwendung dieser Funktion veranschaulicht:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Anpassen der Leistung eines Ultra-Datenträgers über PowerShell

Ultra-Datenträger haben eine einzigartige Funktion, mit der Sie die Leistung anpassen können. Der folgende Befehl ist ein Beispiel für eine Anpassung der Leistung, ohne dass der Datenträger getrennt werden muss:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure Ultra Disks in Azure Kubernetes Service (Vorschauversion)](../aks/use-ultra-disks.md).
- [Migrieren eines Protokolldatenträgers zu einem Disk Ultra-Datenträger](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md).
