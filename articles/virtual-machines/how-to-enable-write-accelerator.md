---
title: Azure-Schreibbeschleunigung
description: Dokumentation zum Aktivieren und Verwenden der Schreibbeschleunigung
author: raiye
manager: markkie
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 2/20/2019
ms.author: raiye
ms.subservice: disks
ms.openlocfilehash: e4f6cefd56c12162b370c78b6df2cd29ece030f1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515711"
---
# <a name="enable-write-accelerator"></a>Aktivieren der Schreibbeschleunigung

Bei der Schreibbeschleunigung handelt es sich um eine Datenträgerfunktion, die nur für virtuelle Computer (Virtual Machines, VMs) der M-Serie in Storage Premium mit Azure Managed Disks verfügbar ist. Wie der Name schon besagt, ist der Zweck der Funktion die Verbesserung der E/A-Wartezeit bei Schreibvorgängen für Azure Storage Premium. Die Schreibbeschleunigung ist hervorragend geeignet, wenn Protokolldateiupdates erforderlich sind, die für moderne Datenbanken auf äußerst leistungsfähige Weise auf dem Datenträger beibehalten werden sollen.

Die Schreibbeschleunigung ist für virtuelle Computer der M-Serie in der öffentlichen Cloud allgemein verfügbar.

## <a name="planning-for-using-write-accelerator"></a>Planen der Verwendung der Schreibbeschleunigung

Die Schreibbeschleunigung sollte für die Volumes verwendet werden, die das Transaktionsprotokoll oder die Protokolle der Rollforwardphase eines DBMS enthalten. Es wird davon abgeraten, die Schreibbeschleunigung für die Datenvolumes eines DBMS zu verwenden, weil die Funktion für die Verwendung auf Protokolldatenträgern optimiert wurde.

Die Schreibbeschleunigung funktioniert nur in Verbindung mit [verwalteten Azure-Datenträgern](https://azure.microsoft.com/services/managed-disks/).

> [!IMPORTANT]
> Wenn Sie die Schreibbeschleunigung für den Betriebssystemdatenträger des virtuellen Computers aktivieren, wird der virtuelle Computer neu gestartet.
>
> Um die Schreibbeschleunigung für einen vorhandenen Azure-Datenträger zu aktivieren, der NICHT Teil eines Volumes ist, das aus mehreren Datenträgern mit Windows-Datenträgerverwaltung oder Volume-Manager, Windows-Speicherplätzen, einem Windows-Dateiserver mit horizontaler Skalierung, Linux LVM oder MDADM besteht, muss die Workload, die auf den Azure-Datenträger zugreift, heruntergefahren werden. Datenbankanwendungen, die den Azure-Datenträger verwenden, MÜSSEN beendet werden.
>
> Wenn Sie die Schreibbeschleunigung für ein vorhandenes Volume aktivieren oder deaktivieren möchten, das aus mehreren Azure Storage Premium-Stripesetdatenträgern mit Windows-Datenträgerverwaltung oder Volume-Manager, Windows-Speicherplätzen, einem Windows-Dateiserver mit horizontaler Skalierung (Scale-out File Server, SOFS), Linux LVM oder MDADM besteht, müssen alle zum Volume gehörigen Datenträger in separaten Schritten für die Schreibbeschleunigung aktiviert oder deaktiviert werden. **Fahren Sie vor dem Aktivieren oder Deaktivieren der Schreibbeschleunigung in einer solchen Konfiguration die Azure-VM herunter**.

Für SAP-spezifische VM-Konfigurationen sollte das Aktivieren der Schreibbeschleunigung für Betriebssystemdatenträger nicht erforderlich sein.

### <a name="restrictions-when-using-write-accelerator"></a>Einschränkungen bei der Verwendung der Schreibbeschleunigung

Bei der Verwendung der Schreibbeschleunigung für einen Azure-Datenträger/eine Azure-VHD gelten folgende Einschränkungen:

- Die Premium-Datenträgerzwischenspeicherung muss auf „Keine“ oder „Schreibgeschützt“ festgelegt werden. Alle anderen Zwischenspeicherungsmodi werden nicht unterstützt.
- Momentaufnahmen werden derzeit nicht für Datenträger mit aktivierter Schreibbeschleunigung unterstützt. Während der Sicherung schließt der Azure Backup-Dienst an die VM angefügte Datenträger mit aktivierter Schreibbeschleunigung automatisch aus.
- Der beschleunigte Pfad wird nur für kleinere E/A-Größen (<=512 KiB) verwendet. In Workloadsituationen, bei denen Daten in einem Massenvorgang geladen oder die Transaktionsprotokollpuffer vor ihrer persistenten Speicherung stärker befüllt werden, wird für die auf Datenträger geschriebenen E/A-Vorgänge wahrscheinlich nicht der beschleunigte Pfad verwendet.

Die Anzahl von Azure Storage Premium-VHDs pro VM, die von der Schreibbeschleunigung unterstützt werden können, ist begrenzt. Die aktuellen Limits lauten wie folgt:

| VM-SKU | Anzahl von Datenträgern mit Schreibbeschleunigung | Datenträger-IOPS der Schreibbeschleunigung pro VM |
| --- | --- | --- |
| M416ms_v2, M416s_v2| 16 | 20000 |
| M208ms_v2, M208s_v2| 8 | 10000 |
| M128ms, M128s | 16 | 20000 |
| M64ms, M64ls, M64s | 8 | 10000 |
| M32ms, M32ls, M32ts, M32s | 4 | 5.000 |
| M16ms, M16s | 2 | 2500 |
| M8ms, M8s | 1 | 1250 |

Die IOPS-Grenzwerte gelten pro VM und *nicht* pro Datenträger. Alle Datenträger mit Schreibbeschleunigung verwenden denselben IOPS-Grenzwert pro VM. Angefügte Datenträger dürfen die IOPS-Begrenzung des Schreibbeschleunigers für eine VM nicht überschreiten. Beispiel: Obwohl die angefügten Festplatten 30.000 IOPS bewältigen können, lässt das System nicht zu, dass die Datenträger für M416ms_v2 20.000 IOPS überschreiten.

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>Aktivieren der Schreibbeschleunigung auf einem bestimmten Datenträger

In den nächsten Abschnitten wird beschrieben, wie die Schreibbeschleunigung auf Azure Storage Premium-VHDs aktiviert werden kann.

### <a name="prerequisites"></a>Voraussetzungen

Für die Verwendung der Schreibbeschleunigung gelten zurzeit die folgenden Voraussetzungen:

- Die Datenträger, auf die Sie die Azure-Schreibbeschleunigung anwenden, müssen [verwaltete Azure-Datenträger](https://azure.microsoft.com/services/managed-disks/) in Storage Premium sein.
- Sie müssen einen virtuellen Computer der M-Serie verwenden.

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>Aktivieren der Azure-Schreibbeschleunigung mit Azure PowerShell

Das Azure PowerShell-Modul ab Version 5.5.0 enthält die Änderungen der relevanten Cmdlets zum Aktivieren oder Deaktivieren der Schreibbeschleunigung für bestimmte Azure Storage Premium-Datenträger.
Zum Aktivieren oder Bereitstellen von Datenträgern, die von der Schreibbeschleunigung unterstützt werden, wurden die folgenden PowerShell-Befehle geändert und zum Akzeptieren eines Parameters für die Schreibbeschleunigung erweitert.

Den folgenden Cmdlets wurde ein neuer Switch-Parameter **-WriteAccelerator** hinzugefügt:

- [Set-AzVMOsDisk](/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

Wird der Parameter nicht angegeben, wird die Eigenschaft auf „false“ festgelegt, und es werden Datenträger bereitgestellt, die nicht von der Schreibbeschleunigung unterstützt werden.

Dem folgenden Cmdlet wurde ein neuer Switch-Parameter **-OsDiskWriteAccelerator** hinzugefügt:

- [Set-AzVmssStorageProfile](/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

Wenn Sie den Parameter nicht angeben, wird die Eigenschaft standardmäßig auf „false“ gesetzt und es werden Datenträger zurückgegeben, die keine Schreibbeschleunigung verwenden.

Den folgenden Cmdlets wurde ein neuer boolescher (NULL-Werte nicht zulässig) Parameter **-OsDiskWriteAccelerator** hinzugefügt:

- [Update-AzVM](/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

Setzen Sie den Parameter auf „$true“ oder „$false“, um die Unterstützung der Azure-Schreibbeschleunigung mit den Datenträgern zu steuern.

Im Folgenden sehen Sie einige Beispielbefehle:

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

Für zwei Hauptszenarien kann wie in den folgenden Abschnitten erläutert ein Skript verwendet werden.

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>Hinzufügen eines von der Schreibbeschleunigung unterstützten neuen Datenträgers mit PowerShell

Sie können das folgende Skript verwenden, um Ihrer VM einen neuen Datenträger hinzuzufügen. Der mit diesem Skript erstellte Datenträger verwendet die Schreibbeschleunigung.

Ersetzen Sie `myVM`, `myWAVMs`, `log001`, die Größe des Datenträgers und die LUN-ID des Datenträgers durch Werte, die für Ihre Bereitstellung geeignet sind.

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>Aktivieren der Schreibbeschleunigung auf einem vorhandenen Azure-Datenträger mit PowerShell

Sie können dieses Skript verwenden, um die Schreibbeschleunigung auf einem vorhandenen Datenträger zu aktivieren. Ersetzen Sie `myVM`, `myWAVMs`, und `test-log001` durch Werte, die für Ihre Bereitstellung geeignet sind. Das Skript fügt die Schreibbeschleunigung zu einem vorhandenen Datenträger hinzu, wenn der Wert für **$newstatus** auf „$true“ festgelegt ist. Mit dem Wert „$false“ wird die Schreibbeschleunigung auf einem Datenträger deaktiviert.

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> Durch die Ausführung des obigen Skripts wird der angegebene Datenträger getrennt, die Schreibbeschleunigung für den Datenträger aktiviert und anschließend der Datenträger wieder angefügt.

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>Aktivieren der Schreibbeschleunigung mit dem Azure-Portal

Sie können die Schreibbeschleunigung über das Portal aktivieren, in dem Sie die Datenträgercache-Einstellungen angeben:

![Schreibbeschleunigung im Azure-Portal](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>Aktivieren der Schreibbeschleunigung mit der Azure-Befehlszeilenschnittstelle

Sie können die Schreibbeschleunigung mithilfe der [Azure CLI](/cli/azure/?view=azure-cli-latest) aktivieren.

Um die Schreibbeschleunigung auf einem vorhandenen Datenträger zu aktivieren, verwenden Sie [az vm update](/cli/azure/vm?view=azure-cli-latest#az-vm-update). Sie können die folgenden Beispiele verwenden, wenn Sie diskName, VMName und ResourceGroup durch Ihre eigenen Werte ersetzen:`az vm update -g group1 -n vm1 -write-accelerator 1=true`

Um einen Datenträger mit Schreibbeschleunigung anzufügen, verwenden Sie [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach). Dabei können Sie das folgende Beispiel verwenden, wenn Sie Ihre eigenen Werte einsetzen:`az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

Um die Schreibbeschleunigung zu deaktivieren, verwenden Sie [az vm update](/cli/azure/vm?view=azure-cli-latest#az-vm-update) und legen die Eigenschaften auf „false“ fest:`az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>Aktivieren der Schreibbeschleunigung mit REST-APIs

Zur Bereitstellung über die Azure-REST-API müssen Sie Azure-ARMClient installieren.

### <a name="install-armclient"></a>Installieren von ARMClient

Um ARMClient auszuführen, müssen Sie ihn über Chocolatey installieren. Die Installation können Sie über „cmd.exe“ oder PowerShell ausführen. Verwenden Sie erhöhte Berechtigungen für diese Befehle („Als Administrator ausführen“).

Führen Sie mit cmd.exe den folgenden Befehl aus:`@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

Führen Sie mit PowerShell den folgenden Befehl aus:`Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

Jetzt können Sie ARMClient mithilfe des folgenden Befehls in cmd.exe oder PowerShell installieren `choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>Abrufen der aktuellen VM-Konfiguration

Um die Attribute Ihrer Datenträgerkonfiguration zu ändern, müssen Sie zunächst die aktuelle Konfiguration in einer JSON-Datei abrufen. Sie können die aktuelle Konfiguration abrufen, indem Sie den folgenden Befehl ausführen:`armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

Ersetzen Sie die Begriffe in „<<   >>“ durch Ihre Daten (einschließlich des Dateinamens, den Sie für die JSON-Datei verwenden möchten).

Die Ausgabe sieht in etwa wie folgt aus:

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

Als Nächstes aktualisieren Sie die JSON-Datei und aktivieren die Schreibbeschleunigung auf dem Datenträger namens „log1“. Dafür können Sie das folgende Attribut nach dem Cacheeintrag des Datenträgers in der JSON-Datei hinzufügen.

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

Aktualisieren Sie anschließend die vorhandene Bereitstellung, indem Sie den folgenden Befehl ausführen:`armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

Die Ausgabe sollte wie folgt aussehen. Sie können sehen, dass die Schreibbeschleunigung für einen Datenträger aktiviert wurde.

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

Nachdem Sie die Änderung vorgenommen haben, sollte das Laufwerk von der Schreibbeschleunigung unterstützt werden.
