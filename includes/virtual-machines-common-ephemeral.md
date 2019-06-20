---
title: include file
description: include file
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 3e9885466d422a0428311ed3013e2ab34341cd25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391406"
---
Kurzlebige Betriebssystem-Datenträger werden auf dem lokalen Speicher des virtuellen Computers (VM) erstellt und nicht auf dem externen Azure Storage gespeichert. Kurzlebige Betriebssystem-Datenträger eignen sich gut für zustandslose Workloads, bei denen Anwendungen einzelne VM-Ausfälle tolerieren, aber mehr Wert auf die Zeit legen, die für umfangreiche Bereitstellung oder die Zeit für das Reimaging der einzelnen VM-Instanzen benötigt wird. Sie eignen sich auch für Anwendungen, die mit dem klassischen Bereitstellungsmodell bereitgestellt werden, um zum Resource Manager-Bereitstellungsmodell zu wechseln. Mit einem kurzlebigen Betriebssystem-Datenträger sind eine geringere Latenzzeit für das Lesen/Schreiben auf dem Betriebssystem-Datenträger und ein schnelleres VM-Reimaging festzustellen. Darüber hinaus ist der kurzlebige Betriebssystem-Datenträger kostenlos, es fallen keine Speicherkosten für den Betriebssystem-Datenträger an. 
 
Die wichtigsten Features von kurzlebigen Datenträgern sind: 
- Sie können sowohl mit Marketplace-Images als auch mit benutzerdefinierten Images verwendet werden.
- Sie können VM-Images bis zur Größe des VM-Cache bereitstellen.
- Möglichkeit, ihre VMs schnell zurückzusetzen oder in den ursprünglichen Startzustand zu versetzen.  
- Geringere Runtimelatenz ähnlich wie bei einem temporären Datenträger. 
- Keine Kosten für den Betriebssystem-Datenträger. 
 
 
Die Hauptunterschiede zwischen permanenten und kurzlebigen Betriebssystem-Datenträgern:

|                             | Permanenter Betriebssystem-Datenträger                          | Kurzlebiger Betriebssystemdatenträger                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Maximale Größe für Betriebssystem-Datenträger      | 2 TiB                                                                                        | Cachegröße für die VM-Größe oder 2TiB, je nachdem, was kleiner ist – [DS](../articles/virtual-machines/linux/sizes-general.md), [ES](../articles/virtual-machines/linux/sizes-memory.md), [M](../articles/virtual-machines/linux/sizes-memory.md), [FS](../articles/virtual-machines/linux/sizes-compute.md) und [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| Unterstützte VM-Größen          | Alle                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Unterstützung für Datenträgertyp           | Verwalteter und nicht verwalteter Betriebssystem-Datenträger                                                                | Nur verwalteter Betriebssystem-Datenträger                                                               |
| Unterstützung für Regionen              | Alle Regionen                                                                                  | Alle Regionen                              |
| Datenpersistenz            | Auf Betriebssystem-Datenträger geschriebene Daten von einem Betriebssystem-Datenträger werden in Azure Storage gespeichert.                                  | Auf den Betriebssystem-Datenträger geschriebene Daten werden auf dem lokalen VM-Speicher gespeichert und nicht in Azure Storage gespeichert. |
| Status „Zuordnung aufgehoben“      | Die Zuordnung von VMs und Skalierungsgruppeninstanzen kann aufgehoben und sie können aus diesem Zustand neu gestartet werden. | Die Zuordnung für VMS und Skalierungsgruppeninstanzen kann nicht aufgehoben werden.                                  |
| Unterstützung der spezialisierte Betriebssystem-Datenträger | Ja                                                                                          | Nein                                                                                 |
| Änderung der Betriebssystem-Datenträgergröße              | Wird während der VM-Erstellung und nach dem Aufheben der VM-Zuordnung unterstützt.                                | Wird nur während der VM-Erstellung unterstützt.                                                  |
| Ändern der Größe auf eine neue VM-Größe   | Daten des Betriebssystem-Datenträgers werden beibehalten.                                                                    | Daten auf dem Betriebssystem-Datenträger werden gelöscht, Betriebssystem wird erneut bereitgestellt                                      |

## <a name="scale-set-deployment"></a>Bereitstellung der Skalierungsgruppe  
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

## <a name="vm-deployment"></a>VM-Bereitstellung 
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
Derzeit ist die einzige Methode für das Reimaging einer Instanz eines virtuellen Computers mit kurzlebigen Betriebssystem-Datenträgern die Verwendung der REST-API. Für Skalierungsgruppen ist das Reimaging bereits über Powershell, CLI, und das Portal verfügbar.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F: Wie groß sind lokale Betriebssystem-Datenträger?**

A: Für die Vorschauversion unterstützen wir Plattformen und/oder Images bis zur Größe des VM-Cache, wobei alle Lese-/Schreibvorgänge auf dem Betriebssystem-Datenträger lokal auf dem gleichen Knoten wie die VM erfolgen. 

**F: Kann die Größe des kurzlebigen Betriebssystem-Datenträgers geändert werden?**

A: Nein, sobald der kurzlebige Betriebssystem-Datenträger bereitgestellt wurde, kann dessen Größe nicht mehr geändert werden. 

**F: Kann ich einen verwalteten Datenträger an eine kurzlebigen VM anfügen?**

A: Ja, können Sie einen verwalteten Datenträger an eine VM anfügen, die einen kurzlebigen Betriebssystem-Datenträger verwendet. 

**F: Werden alle VM-Größen für kurzlebige Betriebssystem-Datenträger unterstützt?**

A: Nein, alle Storage Premium-VM-Größen werden unterstützt (DS, ES, FS, GS und M), mit Ausnahme der Größen der B-Serie, N-Serie und H-Serie.  
 
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
