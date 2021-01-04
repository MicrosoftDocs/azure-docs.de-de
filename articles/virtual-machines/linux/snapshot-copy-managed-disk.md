---
title: Erstellen einer Momentaufnahme einer VHD mithilfe der Azure CLI
description: Erfahren Sie, wie Sie eine Kopie einer VHD in Azure als Sicherung oder für die Behandlung von Problemen erstellen.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6374108247b9bfb950c42495b13b501ded8a02d2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015960"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>Erstellen einer Momentaufnahme mit dem Portal oder der Azure CLI

Erstellen Sie eine Momentaufnahme eines Betriebssystems oder Datenträgers für die Sicherung oder zum Behandeln von VM-Problemen. Eine Momentaufnahme ist eine vollständige, schreibgeschützte Kopie einer VHD. 

## <a name="use-azure-cli"></a>Mithilfe der Azure-Befehlszeilenschnittstelle 

Im folgenden Beispiel muss [Cloud Shell](https://shell.azure.com/bash) verwendet werden oder die Azure CLI installiert sein.

Die folgenden Schritte zeigen, wie Sie mithilfe des Befehls **az snapshot create** mit dem **--source-disk**-Parameter eine Momentaufnahme erstellen. Beim folgenden Beispiel wird davon ausgegangen, dass eine VM namens *myVM* in der Ressourcengruppe *myResourceGroup* vorhanden ist.

Rufen Sie mit [az vm show](/cli/azure/vm#az-vm-show) die Datenträger-ID ab.

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Erstellen Sie mit [az snapshot create](/cli/azure/snapshot#az-snapshot-create) eine Momentaufnahme namens *osDisk-backup*.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Wenn Sie die Momentaufnahme in Speichern mit Zonenresilienz speichern möchten, müssen Sie sie in einer Region erstellen, die [Verfügbarkeitszonen](../../availability-zones/az-overview.md) unterstützt, und den **--sku Standard_ZRS**-Parameter einbeziehen.

Mit [az snapshot list](/cli/azure/snapshot#az-snapshot-list) können Sie eine Liste von Momentaufnahmen anzeigen.

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie zunächst links oben auf **Ressource erstellen**, und suchen Sie nach **Momentaufnahme**. Wählen Sie **Momentaufnahme** aus den Suchergebnissen.
3. Klicken Sie auf dem Blatt **Momentaufnahme** auf **Erstellen**.
4. Geben Sie einen **Namen** für die Momentaufnahme ein.
5. Wählen Sie eine vorhandene Ressourcengruppe aus, oder geben Sie den Namen für eine neue Ressourcengruppe ein. 
7. Wählen Sie für **Quelldatenträger** den verwalteten Datenträger aus, für den eine Momentaufnahme erstellt werden soll.
8. Wählen Sie den **Kontotyp** aus, der zum Speichern der Momentaufnahme verwendet werden soll. Wir empfehlen **Standard-Festplattenlaufwerke**, es sei denn, Sie benötigen eine leistungsstarke SSD.
9. Klicken Sie auf **Erstellen**.


## <a name="next-steps"></a>Nächste Schritte

 Erstellen Sie einen virtuellen Computer aus einer Momentaufnahme, indem Sie aus der Momentaufnahme einen verwalteten Datenträger erstellen und diesen neuen verwalteten Datenträger anschließend als den Betriebssystemdatenträger anfügen. Weitere Informationen finden Sie im Skript [Erstellen eines virtuellen Computers aus einer Momentaufnahme](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).

