---
title: Trennen eines Datenträgers für Daten von einem virtuellen Linux-Computer – Azure
description: Erfahren Sie, wie Sie einen Datenträger unter Verwendung der Azure CLI oder des Azure-Portals von einem virtuellen Computer in Azure trennen.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7e618475d7d0160bce5aee39c267bb191a3a9f0a
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897138"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Trennen eines Datenträgers von einem virtuellen Linux-Computer

Wenn Sie einen Datenträger, der an einen virtuellen Computer angefügt ist, nicht mehr benötigen, können Sie ihn leicht trennen. Dadurch wird der Datenträger von dem virtuellen Computer entfernt, aber nicht aus dem Speicher. In diesem Artikel wird eine Ubuntu LTS 16.04-Distribution verwendet. Wenn Sie eine andere Distribution verwenden, können die Anweisungen zum Aufheben der Datenträgereinbindung abweichen.

> [!WARNING]
> Beim Trennen eines Datenträgers wird dieser nicht automatisch gelöscht. Wenn Sie Storage Premium abonniert haben, fallen auch weiterhin Speichergebühren für den Datenträger an. Weitere Informationen finden Sie unter [Preisgestaltung und Abrechnung bei der Verwendung von Storage Premium](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Wenn Sie die vorhandenen Daten erneut auf dem Datenträger verwenden möchten, können Sie ihn erneut an denselben virtuellen Computer oder an einen anderen anfügen.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Herstellen einer Verbindung mit dem virtuellen Computer zum Aufheben der Datenträgereinbindung

Bevor Sie den Datenträger über die CLI oder das Portal trennen können, müssen Sie seine Einbindung aufheben und Verweise auf ihn aus der FSTAB-Datei entfernen.

Stellen Sie eine Verbindung zur VM her. In diesem Beispiel lautet die öffentliche IP-Adresse des virtuellen Computers *10.0.1.4* und der Benutzername *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Suchen Sie zunächst den Datenträger, den Sie trennen möchten. Im folgenden Beispiel wird „dmesg“ zum Filtern auf SCSI-Datenträgern verwendet:

```bash
dmesg | grep SCSI
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Hier ist *sdc* der Datenträger, den wir trennen möchten. Sie sollten auch die UUID des Datenträgers abrufen.

```bash
sudo -i blkid
```

Die Ausgabe sieht in etwa wie im folgenden Beispiel aus:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Bearbeiten Sie die Datei */etc/fstab*, um Verweise auf den Datenträger zu entfernen. 

> [!NOTE]
> Eine falsche Bearbeitung der Datei **/etc/fstab** könnte zu einem nicht startfähigen System führen. Wenn Sie sich nicht sicher sind, helfen Ihnen die Informationen zur richtigen Bearbeitung dieser Datei in der Dokumentation weiter. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

Öffnen Sie die Datei */etc/fstab* wie folgt in einem Text-Editor:

```bash
sudo vi /etc/fstab
```

In diesem Beispiel muss die folgende Zeile aus der Datei */etc/fstab* gelöscht werden:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Heben Sie die Einbindung des Datenträgers mithilfe von `umount` auf. Im folgenden Beispiel wird die Einbindung der Partition */dev/sdc1* im Einbindungspunkt */datadrive* aufgehoben:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Trennen eines Datenträgers mit der Azure CLI 

Im folgenden Beispiel wird der Datenträger *myDataDisk* vom virtuellen Computer *myVM* in *myResourceGroup* getrennt:

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Der Datenträger verbleibt im Speicher, ist jedoch nicht mehr an einen virtuellen Computer angefügt.


## <a name="detach-a-data-disk-using-the-portal"></a>Trennen eines Datenträgers im Portal

1. Wählen Sie im Menü auf der linken Seite die Option **Virtuelle Computer** aus.
1. Klicken Sie auf dem Blatt für den virtuellen Computer auf **Datenträger**.
1. Wählen Sie oben auf dem Blatt **Datenträger** die Option **Bearbeiten** aus.
1. Klicken Sie auf dem Blatt **Datenträger** ganz rechts neben dem Datenträger, den Sie trennen möchten, auf die Schaltfläche „Trennen“ (![Schaltfläche „Trennen“](./media/detach-disk/detach.png)).
1. Klicken Sie, nachdem der Datenträger entfernt wurde, oben auf dem Blatt auf **Speichern**.

Der Datenträger verbleibt im Speicher, ist jedoch nicht mehr an einen virtuellen Computer angefügt.



## <a name="next-steps"></a>Nächste Schritte
Wenn Sie den Datenträger wiederverwenden möchten, können Sie ihn einfach [an einen anderen virtuellen Computer anfügen](add-disk.md).

Wenn Sie den Datenträger löschen möchten, sodass keine Speicherkosten mehr anfallen, finden Sie entsprechende Informationen unter [Suchen und Löschen von nicht angefügten verwalteten und nicht verwalteten Azure-Datenträgern – Azure-Portal](../disks-find-unattached-portal.md).