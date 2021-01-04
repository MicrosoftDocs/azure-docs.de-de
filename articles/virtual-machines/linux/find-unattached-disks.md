---
title: 'Azure-Befehlszeilenschnittstelle: Suchen und Löschen nicht angefügter Azure-Datenträger (verwaltet und nicht verwaltet)'
description: Informationen zum Suchen und Löschen von nicht angefügten verwalteten und nicht verwalteten Azure-Datenträgern (VHDs/Seitenblobs) über die Azure-Befehlszeilenschnittstelle
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0c3e8bb2ff6f3313e851a4253a95a5ad923a8f70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016215"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Suchen und Löschen von nicht angefügten verwalteten und nicht verwalteten Azure-Datenträgern mit der Azure CLI
Beim Löschen eines virtuellen Computers (VM) in Azure werden standardmäßig Datenträger, die an den virtuellen Computer angefügt sind, nicht gelöscht. Dieses Feature verhindert Datenverluste aufgrund der versehentlich Löschung von virtuellen Computern. Nach dem Löschen eines virtuellen Computers bezahlen Sie nicht angefügte Datenträger weiterhin. In diesem Artikel erfahren Sie, wie Sie nicht angefügte Datenträger suchen und löschen, um unnötige Kosten zu verringern. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Verwaltete Datenträger: Suchen und Löschen nicht angefügter Datenträger 

Das folgende Skript sucht nach nicht angefügten [verwalteten Datenträgern](../managed-disks-overview.md), indem es den Wert der **ManagedBy**-Eigenschaft untersucht. Wenn ein verwalteter Datenträger an einen virtuellen Computer angefügt ist, enthält die **ManagedBy**-Eigenschaft die Ressourcen-ID des virtuellen Computers. Wenn ein verwalteter Datenträger nicht angefügt ist, ist die **ManagedBy**-Eigenschaft NULL. Das Skript überprüft alle verwalteten Datenträger in einem Azure-Abonnement. Wenn das Skript einen verwalteten Datenträger findet, dessen **ManagedBy**-Eigenschaft den Wert NULL hat, legt das Skript fest, dass der Datenträger nicht angefügt ist.

>[!IMPORTANT]
>Führen Sie zunächst das Skript aus, indem Sie die **deleteUnattachedDisks**-Variable auf „0“ festlegen. Auf diese Weise können Sie alle nicht angefügten verwalteten Datenträger suchen und anzeigen.
>
>Nachdem Sie alle nicht angefügten Datenträger überprüft haben, führen Sie das Skript erneut aus, legen die **deleteUnattachedDisks**-Variable dabei jedoch auf „1“ fest. Dadurch können Sie alle nicht angefügten verwalteten Datenträger löschen.
>

```azurecli
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0
unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nicht verwaltete Datenträger: Suchen und Löschen nicht angefügter Datenträger 

Nicht verwaltete Datenträger sind VHD-Dateien, die als [Seitenblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) in [Azure Storage-Konten](../../storage/common/storage-account-overview.md) gespeichert sind. Das folgende Skript sucht nach nicht angefügten nicht verwalteten Datenträgern (Seitenblobs) durch Untersuchen des Werts der **LeaseStatus**-Eigenschaft. Wenn ein nicht verwalteter Datenträger an einen virtuellen Computer angefügt ist, weist die **LeaseStatus**-Eigenschaft den Wert **Locked** auf. Wenn ein nicht verwalteter Datenträger nicht angefügt ist, weist die **LeaseStatus**-Eigenschaft den Wert **Unlocked** auf. Das Skript überprüft alle nicht verwalteten Datenträger in allen Azure Storage-Konten in einem Azure-Abonnement. Wenn das Skript einen nicht verwalteten Datenträger findet, dessen **LeaseStatus**-Eigenschaft den Wert **Unlocked** hat, geht das Skript davon aus, dass der Datenträger nicht angefügt ist.

>[!IMPORTANT]
>Führen Sie zunächst das Skript aus, indem Sie die **deleteUnattachedVHDs**-Variable auf „0“ festlegen. Auf diese Weise können Sie alle nicht angefügten nicht verwalteten VHDs suchen und anzeigen.
>
>Nachdem Sie alle nicht angefügten Datenträger überprüft haben, führen Sie das Skript erneut aus, legen die **deleteUnattachedVHDs**-Variable dabei jedoch auf „1“ fest. Auf diese Weise können Sie alle nicht angefügten nicht verwalteten Datenträger löschen.
>

```azurecli
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0
storageAccountIds=$(az storage account list --query [].[id] -o tsv)
for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Löschen eines Speicherkontos](../../storage/common/storage-account-create.md#delete-a-storage-account).