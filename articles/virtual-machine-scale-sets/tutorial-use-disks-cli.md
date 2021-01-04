---
title: 'Tutorial: Erstellen und Verwenden von Datenträgern für Skalierungsgruppen mit der Azure CLI'
description: Hier wird beschrieben, wie Sie die Azure CLI zum Erstellen und Verwenden von Managed Disks mit einer VM-Skalierungsgruppe verwenden, z.B. das Hinzufügen, Vorbereiten, Auflisten und Trennen von Datenträgern.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: adaa7d1c2cf4a78a680ef4fbbec06975ceda812b
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433499"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>Tutorial: Erstellen und Verwalten von Datenträgern mit VM-Skalierungsgruppe mithilfe der Azure CLI
Für VM-Skalierungsgruppen werden Datenträger zum Speichern des Betriebssystems, der Anwendungen und der Daten von VM-Instanzen verwendet. Beim Erstellen und Verwalten einer Skalierungsgruppe muss darauf geachtet werden, eine für den erwarteten Workload geeignete Datenträgergröße und -konfiguration auszuwählen. In diesem Tutorial wird beschrieben, wie Sie VM-Datenträger erstellen und verwalten. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Betriebssystemdatenträger und temporäre Datenträger
> * Datenträger
> * Standard- und Premium-Datenträger
> * Datenträgerleistung
> * Anfügen und Vorbereiten von Datenträgern

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.29 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="default-azure-disks"></a>Azure-Standarddatenträger
Wenn eine Skalierungsgruppe erstellt oder skaliert wird, werden automatisch zwei Datenträger an jede VM-Instanz angefügt.

**Betriebssystem-Datenträger**: Betriebssystem-Datenträger können in der Größe auf bis zu 2 TB angepasst werden und hosten das Betriebssystem der VM. Der Betriebssystem-Datenträger wird standardmäßig mit */dev/sda* bezeichnet. Die Konfiguration der Datenträgerzwischenspeicherung des Betriebssystem-Datenträgers ist für die Leistung des Betriebssystems optimiert. Aufgrund dieser Konfiguration sollte der Betriebssystem-Datenträger **nicht** Anwendungen oder Daten hosten. Verwenden Sie für Anwendungen und Daten einen Datenträger. Dies wird weiter unten in diesem Artikel ausführlich erläutert.

**Temporärer Datenträger**: Für temporäre Datenträger wird ein Solid State Drive verwendet, das sich auf demselben Azure-Host wie die VM-Instanz befindet. Dies sind äußerst leistungsfähige Datenträger, die für Vorgänge wie die temporäre Datenverarbeitung verwendet werden können. Wenn die VM-Instanz aber auf einen neuen Host verschoben wird, werden alle auf einem temporären Datenträger gespeicherten Daten entfernt. Die Größe des temporären Datenträgers richtet sich nach der Größe der VM-Instanz. Temporäre Datenträger werden mit bezeichnet */dev/sdb* und haben den Bereitstellungspunkt */mnt*.

### <a name="temporary-disk-sizes"></a>Größe von temporären Datenträgern
| type | Gängige Größen | Max. Größe des temporären Datenträgers (GiB) |
|----|----|----|
| [Allgemeiner Zweck](../virtual-machines/sizes-general.md) | A-, B- und D-Serie | 1600 |
| [Computeoptimiert](../virtual-machines/sizes-compute.md) | F-Serie | 576 |
| [Arbeitsspeicheroptimiert](../virtual-machines/sizes-memory.md) | D-, E-, G- und M-Serie | 6.144 |
| [Speicheroptimiert](../virtual-machines/sizes-storage.md) | L-Serie | 5.630 |
| [GPU](../virtual-machines/sizes-gpu.md) | N-Serie | 1440 |
| [Hohe Leistung](../virtual-machines/sizes-hpc.md) | A- und H-Serie | 2000 |


## <a name="azure-data-disks"></a>Azure-Datenträger
Zusätzliche Datenträger können hinzugefügt werden, wenn Sie Anwendungen installieren und Daten speichern müssen. Datenträger sollten in allen Fällen verwendet werden, in denen eine dauerhafte und dynamische Datenspeicherung erwünscht ist. Jeder Datenträger weist eine maximale Kapazität von 4 TB auf. Die Größe der VM-Instanz bestimmt, wie viele Datenträger angefügt werden können. Für jede vCPU eines virtuellen Computers können zwei Datenträger bis zu einem absoluten Maximum von 64 Datenträgern pro virtuellem Computer angefügt werden.

## <a name="vm-disk-types"></a>VM-Datenträgertypen
In Azure stehen zwei verschiedene Datenträgertypen zur Verfügung.

### <a name="standard-disk"></a>Standarddatenträger
Standardspeicher basiert auf Festplatten und stellt eine kostengünstige Speicherlösung mit hoher Leistung dar. Standarddatenträger sind ideal für eine kostengünstige Entwicklungs- und Testworkload.

### <a name="premium-disk"></a>Premium-Datenträger
Premium-Datenträger zeichnen sich durch SSD-basierte hohe Leistung und geringe Wartezeit aus. Diese Datenträger werden für VMs empfohlen, auf denen Workloads für die Produktion ausgeführt werden. Storage Premium unterstützt virtuelle Computer der DS-, DSv2-, GS- und FS-Serie. Bei Auswahl einer Datenträgergröße wird der Wert auf den nächsten Datenträgertyp aufgerundet. Liegt die Größe des Datenträgers z.B. unter 128GB, ist der Datenträgertyp P10. Wenn die Größe des Datenträgers zwischen 129 und 512GB liegt, ist die Größe P20. Bei mehr als 512 GB ist die Größe P30.

### <a name="premium-disk-performance"></a>Leistung von Premium-Datenträgern
|Storage Premium-Datenträgertyp | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Datenträgergröße (aufgerundet) | 32 GB | 64 GB | 128 GB | 512 GB | 1\.024GB (1TB) | 2\.048 GB (2 TB) | 4\.095 GB (4 TB) |
| Max. IOPS pro Datenträger | 120 | 240 | 500 | 2\.300 | 5\.000 | 7\.500 | 7\.500 |
Durchsatz pro Datenträger | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

In dieser Tabelle ist zwar die maximale IOPS-Anzahl pro Datenträger angegeben, eine höhere Leistung kann aber durch Striping mehrerer Datenträger erreicht werden. Eine Standard_GS5-VM kann z.B. ein Maximum von 80.000 IOPS erreichen. Ausführliche Informationen zur maximalen IOPS-Anzahl pro virtuellem Computer finden Sie unter [Größen für virtuelle Linux-Computer](../virtual-machines/sizes.md).


## <a name="create-and-attach-disks"></a>Erstellen und Anfügen von Datenträgern
Sie können Datenträger beim Erstellen einer Skalierungsgruppe oder für eine vorhandene Skalierungsgruppe erstellen und anfügen.

Ab API-Version `2019-07-01` können Sie die Größe des Betriebssystemdatenträgers in einer VM-Skalierungsgruppe mit der Eigenschaft [storageProfile.osDisk.diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk) festlegen. Nach der Bereitstellung müssen Sie den Datenträger möglicherweise erweitern oder neu partitionieren, damit der gesamte Speicherplatz genutzt werden kann. Weitere Informationen zum [Erweitern des Datenträgers finden Sie hier](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os).

### <a name="attach-disks-at-scale-set-creation"></a>Anfügen von Datenträgern bei der Erstellung einer Skalierungsgruppe
Erstellen Sie zunächst mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe. In diesem Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *eastus* erstellt.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Erstellen Sie mit dem Befehl [az vmss create](/cli/azure/vmss) eine VM-Skalierungsgruppe. Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Namen *myScaleSet* erstellt, und es werden SSH-Schlüssel generiert, falls sie noch nicht vorhanden sind. Mit dem Parameter `--data-disk-sizes-gb` werden zwei Datenträger erstellt. Der erste Datenträger hat eine Größe von *64*, und der zweite Datenträger hat eine Größe von *128* GB:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

Die Erstellung und Konfiguration aller Ressourcen und VM-Instanzen der Skalierungsgruppe dauert einige Minuten.

### <a name="attach-a-disk-to-existing-scale-set"></a>Anfügen eines Datenträgers an eine vorhandene Skalierungsgruppe
Sie können Datenträger auch an eine vorhandene Skalierungsgruppe anfügen. Verwenden Sie die im vorherigen Schritt erstellte Skalierungsgruppe, um mit [az vmss disk attach](/cli/azure/vmss/disk) einen weiteren Datenträger hinzuzufügen. Im folgenden Beispiel wird ein zusätzlicher Datenträger mit *128* GB angefügt:

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>Vorbereiten der Datenträger
Bei den Datenträgern, die erstellt und an die VM-Instanzen Ihrer Skalierungsgruppe angefügt werden, handelt es sich um unformatierte Datenträger. Bevor Sie diese für Ihre Daten und Anwendungen nutzen können, müssen die Datenträger vorbereitet werden. Zum Vorbereiten der Datenträger erstellen Sie eine Partition und ein Dateisystem und stellen diese bereit.

Sie können die benutzerdefinierte Skripterweiterung von Azure verwenden, um den Prozess für mehrere VM-Instanzen einer Skalierungsgruppe zu automatisieren. Mit dieser Erweiterung können Skripts lokal auf jeder VM-Instanz ausgeführt werden, z.B. um angefügte Datenträger vorzubereiten. Weitere Informationen finden Sie unter [Übersicht über benutzerdefinierte Skripterweiterungen](../virtual-machines/extensions/custom-script-linux.md).

Im folgenden Beispiel wird mit [az vmss extension set](/cli/azure/vmss/extension) ein Skript aus einem GitHub-Beispielrepository auf jeder VM-Instanz ausgeführt, um alle angefügten unformatierten Datenträger vorzubereiten:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Stellen Sie eine SSH-Verbindung mit einer der VM-Instanzen her, um zu bestätigen, dass die Datenträger richtig vorbereitet wurden. Listen Sie mit [az vmss list-instance-connection-info](/cli/azure/vmss) die Verbindungsinformationen für Ihre Skalierungsgruppe auf:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Verwenden Sie Ihre eigene öffentliche IP-Adresse und Portnummer, um wie im folgenden Beispiel eine Verbindung mit der ersten VM-Instanz herzustellen:

```console
ssh azureuser@52.226.67.166 -p 50001
```

Überprüfen Sie die Partitionen auf der VM-Instanz wie folgt:

```bash
sudo fdisk -l
```

In der folgenden Beispielausgabe ist zu sehen, dass drei Datenträger an die VM-Instanz angefügt sind: */dev/sdc*, */dev/sdd* und */dev/sde*. Jeder dieser Datenträger verfügt über eine einzelne Partition, für die der gesamte verfügbare Speicherplatz verwendet wird:

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

Überprüfen Sie die Dateisysteme und Bereitstellungspunkte auf der VM-Instanz wie folgt:

```bash
sudo df -h
```

In der folgenden Beispielausgabe ist zu sehen, dass die Dateisysteme für die drei Datenträger richtig unter */datadisks* bereitgestellt wurden:

```output
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

Die Datenträger auf den einzelnen VM-Instanzen in Ihrer Skalierung werden auf die gleiche Weise automatisch vorbereitet. Wenn Ihre Skalierungsgruppe hochskaliert wird, werden die erforderlichen Datenträger an die neuen VM-Instanzen angefügt. Die benutzerdefinierte Skripterweiterung wird ebenfalls automatisch ausgeführt, um die Datenträger vorzubereiten.

Schließen Sie die SSH-Verbindung mit Ihrer VM-Instanz:

```bash
exit
```


## <a name="list-attached-disks"></a>Auflisten von angefügten Datenträgern
Verwenden Sie zum Anzeigen von Informationen zu den Datenträgern, die an eine Skalierungsgruppe angefügt sind, den Befehl [az vmss show](/cli/azure/vmss), und führen Sie eine Abfrage für *virtualMachineProfile.storageProfile.dataDisks* durch:

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

Es werden Informationen zur Datenträgergröße, Speicherebene und logischen Gerätenummer (Logical Unit Number, LUN) angezeigt. Die folgende Beispielausgabe enthält ausführliche Informationen zu den drei Datenträgern, die an die Skalierungsgruppe angefügt sind:

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>Trennen eines Datenträgers
Wenn Sie einen bestimmten Datenträger nicht mehr benötigen, können Sie ihn von der Skalierungsgruppe trennen. Der Datenträger wird aus allen VM-Instanzen in der Skalierungsgruppe entfernt. Verwenden Sie zum Trennen eines Datenträgers von einer Skalierungsgruppe den Befehl [az vmss disk detach](/cli/azure/vmss/disk), und geben Sie die LUN des Datenträgers an. Die LUNs sind in der Ausgabe von [az vmss show](/cli/azure/vmss) im vorherigen Abschnitt enthalten. Im folgenden Beispiel wird LUN *2* von der Skalierungsgruppe getrennt:

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen mit [az group delete](/cli/azure/group), um Ihre Skalierungsgruppe und die Datenträger zu entfernen. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde beschrieben, wie Sie Datenträger mit Skalierungsgruppen mithilfe der Azure CLI erstellen und verwenden:

> [!div class="checklist"]
> * Betriebssystemdatenträger und temporäre Datenträger
> * Datenträger
> * Standard- und Premium-Datenträger
> * Datenträgerleistung
> * Anfügen und Vorbereiten von Datenträgern

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie ein benutzerdefiniertes Image für Ihre in Skalierungsgruppen enthaltenen VM-Instanzen verwenden.

> [!div class="nextstepaction"]
> [Verwenden eines benutzerdefinierten Images für VM-Instanzen von Skalierungsgruppen](tutorial-use-custom-image-cli.md)