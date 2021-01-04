---
title: Kurzlebige Betriebssystemdatenträger
description: Erfahren Sie mehr über kurzlebige Betriebssystemdatenträger für virtuelle Azure-Computer.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 52071b964412071d820745b173e8835c6f9e7d0e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510990"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Kurzlebige Betriebssystemdatenträger für virtuelle Azure-Computer

Kurzlebige Betriebssystem-Datenträger werden auf dem lokalen Speicher des virtuellen Computers (VM) erstellt und nicht auf dem externen Azure Storage gespeichert. Kurzlebige Betriebssystem-Datenträger eignen sich gut für zustandslose Workloads, bei denen Anwendungen einzelne VM-Ausfälle tolerieren, sind aber stärker durch den Zeitpunkt der VM-Bereitstellung oder das Reimaging der einzelnen VM-Instanzen betroffen. Mit einem kurzlebigen Betriebssystem-Datenträger erzielen Sie eine geringere Latenzzeit für das Lesen/Schreiben auf dem Betriebssystem-Datenträger und schnelleres VM-Reimaging. 
 
Die wichtigsten Features von kurzlebigen Datenträgern sind: 
- Ideal für zustandslose Anwendungen geeignet.
- Sie können sowohl mit Marketplace- als auch benutzerdefinierten Images verwendet werden.
- Möglichkeit, VMs und Skalierungsgruppeninstanzen schnell zurückzusetzen oder in den ursprünglichen Startzustand zu versetzen.  
- Geringere Latenz ähnlich wie bei einem temporären Datenträger. 
- Kurzlebige Betriebssystem-Datenträger sind kostenlos, es fallen keine Speicherkosten für den Betriebssystem-Datenträger an.
- Sie sind in allen Azure-Regionen verfügbar. 
- Kurzlebige Betriebssystem-Datenträger werden von [Shared Image Gallery](./linux/shared-image-galleries.md) unterstützt. 
 

 
Die Hauptunterschiede zwischen permanenten und kurzlebigen Betriebssystem-Datenträgern:

|                             | Permanenter Betriebssystem-Datenträger                          | Kurzlebiger Betriebssystemdatenträger                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Maximale Größe für Betriebssystem-Datenträger**      | 2 TiB                                                                                        | Cachegröße für die VM-Größe oder 2TiB, je nachdem, was kleiner ist. Informationen zur **Cachegröße in GiB** finden Sie unter [DS](sizes-general.md), [ES](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md) und [GS](sizes-previous-gen.md#gs-series).              |
| **Unterstützte VM-Größen**          | All                                                                                          | VM-Größen, die Storage Premium unterstützen, etwa DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| **Unterstützung für Datenträgertyp**           | Verwalteter und nicht verwalteter Betriebssystem-Datenträger                                                                | Nur verwalteter Betriebssystem-Datenträger                                                               |
| **Unterstützung für Regionen**              | Alle Regionen                                                                                  | Alle Regionen                              |
| **Datenpersistenz**            | Auf Betriebssystem-Datenträger geschriebene Daten von einem Betriebssystem-Datenträger werden in Azure Storage gespeichert.                                  | Auf den Betriebssystem-Datenträger geschriebene Daten werden auf dem lokalen VM-Speicher gespeichert und nicht in Azure Storage gespeichert. |
| **Status „Zuordnung aufgehoben“**      | Die Zuordnung von VMs und Skalierungsgruppeninstanzen kann aufgehoben und sie können aus diesem Zustand neu gestartet werden. | Die Zuordnung für VMS und Skalierungsgruppeninstanzen kann nicht aufgehoben werden.                                  |
| **Unterstützung der spezialisierte Betriebssystem-Datenträger** | Ja                                                                                          | Nein                                                                                 |
| **Änderung der Betriebssystem-Datenträgergröße**              | Wird während der VM-Erstellung und nach dem Aufheben der VM-Zuordnung unterstützt.                                | Wird nur während der VM-Erstellung unterstützt.                                                  |
| **Ändern der Größe in eine neue VM-Größe**   | Daten des Betriebssystem-Datenträgers werden beibehalten.                                                                    | Daten auf dem Betriebssystem-Datenträger werden gelöscht, Betriebssystem wird erneut bereitgestellt       
| **Platzierung der Auslagerungsdatei**   | Unter Windows ist die Auslagerungsdatei auf dem Ressourcendatenträger gespeichert                                              | Unter Windows ist die Auslagerungsdatei auf dem Betriebssystemdatenträger gespeichert   |

## <a name="size-requirements"></a>Größenanforderungen

Sie können VM- und Instanzimages bis zur Größe des VM-Caches bereitstellen. Windows Server-Standardimages aus dem Marketplace sind beispielsweise etwa 127 GiB groß, was bedeutet, dass Sie eine VM-Größe mit einem Cache von mehr als 127 GiB benötigen. In diesem Fall hat [Standard_DS2_v2](dv2-dsv2-series.md) eine Cachegröße von 86 GiB und ist damit nicht groß genug. Standard_DS3_v2 weist eine Cachegröße von 172 GiB auf und ist damit groß genug. In diesem Fall ist Standard_DS3_v2 die kleinste Größe in der DSv2-Serie, die Sie mit diesem Image verwenden können. Einfache Linux-Images im Marketplace und Windows Server-Images, die mit `[smallsize]` gekennzeichnet sind, liegen in der Regel bei etwa 30 GiB und können die meisten der verfügbaren VM-Größen verwenden.

Kurzlebige Datenträger erfordern außerdem, dass die VM-Größe Storage Premium unterstützt. Die Größen weisen in der Regel (aber nicht immer) ein `s` im Namen auf, z.B. DSv2- und EsV3. Ausführliche Informationen dazu, welche Größen Storage Premium unterstützen, finden Sie unter den [Größen für virtuelle Azure-Computer](sizes.md).

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Vorschau: kurzlebige Betriebssystem Datenträger können jetzt auf temporären Datenträgern gespeichert werden
Kurzlebige Betriebssystemdatenträger können jetzt zusätzlich zum VM-Cache auf dem temporären bzw. Ressourcendatenträger einer VM gespeichert werden. Daher können Sie nun kurzlebige Betriebssystemdatenträger mit einer VM verwenden, die keinen Cache oder zu wenig Cache, aber einen temporären bzw. Ressourcendatenträger hat, um den kurzlebigen Betriebssystemdatenträger zu speichern, etwa Dav3, Dav4, Eav4 und Eav3. Hat eine VM ausreichend Cache und temporären Speicherplatz, können Sie nun auch angeben, wo der kurzlebige Betriebssystemdatenträger gespeichert werden soll, indem Sie die neue Eigenschaft [DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement) verwenden. Bei dieser Funktion wird bei der Bereitstellung eines virtuellen Windows-Computers die Auslagerungsdatei so konfiguriert, dass sie sich auf dem Betriebssystemdatenträger befindet. Diese Funktion steht derzeit als Vorschau zur Verfügung. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Um zu beginnen, [fordern Sie Zugriff an](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u).

## <a name="powershell"></a>PowerShell

Wenn Sie einen kurzlebigen Datenträger für eine PowerShell-VM-Bereitstellung verwenden möchten, verwenden Sie [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) in Ihrer VM-Konfiguration. Legen Sie `-DiffDiskSetting` auf `Local` und `-Caching` auf `ReadOnly` fest.     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Für Bereitstellungen von Skalierungsgruppen verwenden Sie das Cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) in Ihrer Konfiguration. Legen Sie `-DiffDiskSetting` auf `Local` und `-Caching` auf `ReadOnly` fest.


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Wenn Sie einen kurzlebigen Datenträger für eine CLI-VM-Bereitstellung verwenden möchten, legen Sie den Parameter `--ephemeral-os-disk` in [az vm create](/cli/azure/vm#az-vm-create) auf `true` und den Parameter `--os-disk-caching` auf `ReadOnly` fest.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Für Skalierungsgruppen verwenden Sie denselben Parameter `--ephemeral-os-disk true` für [az-vmss-create](/cli/azure/vmss#az-vmss-create) und legen den Parameter `--os-disk-caching` auf `ReadOnly` fest.

## <a name="portal"></a>Portal

Im Azure-Portal können Sie die Verwendung kurzlebiger Datenträger beim Bereitstellen einer VM auswählen. Dazu öffnen Sie auf der Registerkarte **Datenträger** den Abschnitt **Erweitert**. Wählen Sie für **Kurzlebigen Betriebssystemdatenträger verwenden** die Option **Ja** aus.

![Screenshot des Optionsfelds für die Verwendung eines kurzlebigen Betriebssystem-Datenträgers](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Wenn die Option für die Verwendung eines kurzlebigen Datenträgers abgeblendet ist, haben Sie möglicherweise eine VM-Größe gewählt, deren Cachegröße nicht größer als das Betriebssystemimage ist oder die Storage Premium nicht unterstützt. Kehren Sie zur Seite **Grundeinstellungen** zurück, und wählen Sie eine andere VM-Größe aus.

Sie können Skalierungsgruppen mit kurzlebigen Betriebssystem-Datenträgern auch über das Portal erstellen. Achten Sie lediglich darauf, dass Sie eine VM-Größe mit ausreichender Cachegröße und dann für **Kurzlebigen Betriebssystemdatenträger verwenden** die Option **Ja** auswählen.

![Screenshot des Optionsfelds für die Verwendung eines kurzlebigen Betriebssystem-Datenträgers für die Skalierungsgruppe](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Vorlagenbereitstellung einer Skalierungsgruppe  
Der Prozess zum Erstellen einer Skalierungsgruppe, die einen kurzlebigen Betriebssystem-Datenträger verwendet, besteht darin, die Eigenschaft `diffDiskSettings` zum Ressourcentyp `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` in der Vorlage hinzuzufügen. Außerdem muss die Cachingrichtlinie für den kurzlebigen Betriebssystem-Datenträger auf `ReadOnly` gesetzt werden. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Vorlagenbereitstellung einer VM 
Sie können eine VM mit einem kurzlebigen Betriebssystem-Datenträger mithilfe einer Vorlage bereitstellen. Der Prozess zum Erstellen einer VM, die kurzlebige Betriebssystem-Datenträger verwendet, besteht darin, die Eigenschaft `diffDiskSettings` zum Ressourcentyp „Microsoft.Compute/virtualMachines“ in der Vorlage hinzuzufügen. Außerdem muss die Cachingrichtlinie für den kurzlebigen Betriebssystem-Datenträger auf `ReadOnly` gesetzt werden. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Reimaging einer VM mithilfe von REST
Sie können ein Reimaging für eine VM-Instanz mit einem kurzlebigen Betriebssystemdatenträger über die REST-API, wie weiter unten beschrieben, und über das Azure-Portal durchführen. Für Skalierungsgruppen ist das Reimaging bereits über Powershell, CLI, und das Portal verfügbar.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F: Wie groß sind lokale Betriebssystem-Datenträger?**

A: Wir unterstützen Plattform- und benutzerdefinierte Images bis zur Größe des VM-Cache, wobei alle Lese-/Schreibvorgänge auf dem Betriebssystem-Datenträger lokal auf dem gleichen Knoten wie die VM erfolgen. 

**F: Kann die Größe des kurzlebigen Betriebssystem-Datenträgers geändert werden?**

A: Nein, sobald der kurzlebige Betriebssystem-Datenträger bereitgestellt wurde, kann dessen Größe nicht mehr geändert werden. 

**F: Kann ich einen verwalteten Datenträger an eine kurzlebigen VM anfügen?**

A: Ja, können Sie einen verwalteten Datenträger an eine VM anfügen, die einen kurzlebigen Betriebssystem-Datenträger verwendet. 

**F: Werden alle VM-Größen für kurzlebige Betriebssystem-Datenträger unterstützt?**

A: Nein, die meisten Storage Premium-VM-Größen werden unterstützt (DS, ES, FS, GS, M usw.). Um zu ermitteln, ob eine bestimmte VM-Größe kurzlebige Betriebssystemdatenträger unterstützt, können Sie wie folgt vorgehen:

Rufen Sie das PowerShell-Cmdlet `Get-AzComputeResourceSku` auf.
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**F: Kann der kurzlebige Betriebssystem-Datenträger für bestehende VMs und Skalierungsgruppen angewendet werden?**

A: Nein, kurzlebigen Betriebssystem-Datenträger kann nur während Erstellung der VM und der Skalierungsgruppe verwendet werden. 

**F: Können kurzlebige und normale Betriebssystem-Datenträger in einer Skalierungsgruppe kombiniert werden?**

A: Nein, Sie können kurzlebigen und persistenten Betriebssystem-Datenträgerinstanzen nicht innerhalb der gleichen Skalierungsgruppe kombinieren. 

**F: Kann der kurzlebiger Betriebssystem-Datenträger mithilfe von Powershell oder CLI erstellt werden?**

A: Ja, können Sie VMs mit einem kurzlebigen Betriebssystem-Datenträger mithilfe von REST, Vorlagen, PowerShell und CLI erstellen.

**F: Welche Features werden nicht mit dem kurzlebigen Betriebssystem-Datenträger unterstützt?**

A: Kurzlebige Datenträger unterstützen nicht:
- Erfassen von VM-Images
- Momentaufnahmen von Datenträgern 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Austausch des Betriebssystem-Datenträgers 
 
## <a name="next-steps"></a>Nächste Schritte
Sie können einen virtuellen Computer mit einem kurzlebigen Betriebssystemdatenträger über die [Azure-Befehlszeilenschnittstelle](/cli/azure/vm#az-vm-create) erstellen.
