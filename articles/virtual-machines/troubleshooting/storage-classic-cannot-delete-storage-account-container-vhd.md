---
title: Beheben von Fehlern beim Löschen von klassischen Azure Storage-Konten, -Containern oder -VHDs | Microsoft-Dokumentation
description: Behebung von Problemen beim Löschen von Speicherressourcen, die angefügte VHDs enthalten.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: c74f2ef9eed25719e722970671406c850b6a59b2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002594"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Behebung von Problemen beim Löschen klassischer Speicherressourcen
Dieser Artikel enthält Informationen zur Problembehandlung, wenn beim Versuch, ein klassisches Azure Storage-Konto, einen Azure-Container oder eine Seitenblobdatei (*.vhd) zu löschen, einer der folgenden Fehler auftritt. 


In diesem Artikel werden nur Probleme mit klassischen Speicherressourcen behandelt. Wenn ein Benutzer einen klassischen virtuellen Computer über das Azure-Portal, PowerShell oder die Befehlszeilenschnittstelle löscht, werden die Datenträger nicht automatisch gelöscht. Der Benutzer erhält die Option, die Datenträgerressource zu löschen. Wenn die Option nicht ausgewählt wird, verhindert die Datenträgerressource das Löschen des Speicherkontos, des Containers und der eigentlichen Seitenblobdatei (*.vhd).

Weitere Informationen zu Azure-Datenträgern finden Sie [hier](../../virtual-machines/managed-disks-overview.md). Azure verhindert das Löschen eines an eine VM angefügten Datenträgers, um eine Beschädigung zu vermeiden. Zudem wird das Löschen von Containern und Speicherkonten, die ein an eine VM angefügtes Seitenblob aufweisen, verhindert. 

## <a name="what-is-a-disk"></a>Was ist ein „Datenträger“?
Eine „Datenträgerressource“ wird verwendet, um eine Seitenblobdatei (*.vhd) auf einem virtuellen Computer als Betriebssystem-Datenträger oder Datenträger für Daten einzubinden. Die Ressource eines Betriebssystem-Datenträgers oder Datenträgers für Daten behält bis zum Löschen eine Lease für die VHD-Datei bei. Eine Speicherressource in dem im unten stehenden Bild dargestellten Pfad kann nicht gelöscht werden, wenn eine Datenträgerressource auf sie verweist.

![Screenshot des Portals mit geöffnetem Bereich „Eigenschaften“ (klassisch) für den Datenträger](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Schritte zum Löschen eines klassischen virtuellen Computers 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Löschen Sie den klassischen virtueller Computer.
2. Wenn das Kontrollkästchen „Datenträger“ aktiviert ist, wird die mit dem Seitenblob (*.vhd) verknüpfte **Datenträgerlease** (siehe Abbildung oben) unterbrochen. Die eigentliche VHD-Datei des Seitenblobs befindet sich immer noch im Speicherkonto.
![Screenshot: Dialogfeld zum Bestätigen der Löschung eines virtuellen Computers](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Nachdem die Datenträgerlease unterbrochen wurde, kann das eigentliche Seitenblob gelöscht werden. Ein Speicherkonto oder Container kann gelöscht werden, nachdem alle darin enthaltenen Datenträgerressourcen gelöscht wurden.

>[!NOTE] 
>Wenn ein Benutzer den virtuellen Computer löscht, jedoch nicht die VHD, fallen weiterhin Speichergebühren für die VHD-Datei des Seitenblobs an. Die Gebühren richten sich nach dem Typ des Speicherkontos. Weitere Details finden Sie auf der [Seite mit den Preisen](https://azure.microsoft.com/pricing/details/storage/). Wenn der Benutzer eine VHD nicht mehr verwenden möchte, löschen Sie sie, um weitere Gebühren zu vermeiden. 

## <a name="unable-to-delete-storage-account"></a>Speicherkonto kann nicht gelöscht werden. 

Wenn der Benutzer versucht, ein klassisches Speicherkonto zu löschen, das nicht mehr benötigt wird, kann das folgende Verhalten auftreten.

#### <a name="azure-portal"></a>Azure-Portal 
Der Benutzer navigiert im [Azure-Portal](https://portal.azure.com) zum klassischen Speicherkonto und klickt auf **Löschen**. Daraufhin wird die folgende Meldung angezeigt: 

Mit an einen virtuellen Computer angefügten Datenträgern

![Screenshot: Meldung, in der erklärt wird, warum ein Speicherkonto nicht gelöscht werden kann](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Ohne an einen virtuellen Computer angefügte Datenträger

![Screenshot des Portals geöffnetem Bereich „Löschen“ (klassisch) des virtuellen Computers ohne Fehler](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Der Benutzer versucht, ein nicht mehr verwendetes Speicherkonto mithilfe von klassischen PowerShell-Cmdlets zu löschen. Dem Benutzer wird die folgende Meldung angezeigt:

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: Speicherkonto „myclassicaccount“ weist einige aktive Images und/oder Datenträger auf, z.B.  
> „myclassicaccount“. Entfernen Sie diese Bilder und/oder Datenträger, bevor Sie dieses Speicherkonto löschen.</span>

## <a name="unable-to-delete-storage-container"></a>Speichercontainer konnte nicht gelöscht werden.

Wenn der Benutzer versucht, einen klassischen Speicherblobcontainer zu löschen, der nicht mehr benötigt wird, kann das folgende Verhalten auftreten.

#### <a name="azure-portal"></a>Azure-Portal 
Im Azure-Portal wird dem Benutzer nicht erlaubt, einen Container zu löschen, wenn eine Datenträgerlease vorhanden ist, die auf eine VHD-Seitenblobdatei im Container verweist. Dies ist beabsichtigt, um das versehentliche Löschen einer VHD-Datei für eine Datenträgerlease zu verhindern. 

![Screenshot des Portals mit geöffnetem Listenbereich für den Speichercontainer](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Wenn der Benutzer versucht, den Löschvorgang mit PowerShell durchzuführen, tritt folgender Fehler auf. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : The remote server returned an error: (412) Es ist derzeit eine Lease für den Container vorhanden, und in der Anforderung wurde keine Lease-ID angegeben. HTTP-Statuscode: 412 – HTTP-Fehlermeldung: Es ist derzeit eine Lease für den Container vorhanden, und in der Anforderung wurde keine Lease-ID angegeben.</span>

## <a name="unable-to-delete-a-vhd"></a>VHD kann nicht gelöscht werden. 

Nach dem Löschen des virtuellen Azure-Computers versucht der Benutzer, die VHD-Datei (Seitenblob) zu löschen und erhält die folgende Meldung:

#### <a name="azure-portal"></a>Azure-Portal 
Im Portal sind möglicherweise zwei Benutzeroberflächen vorhanden, abhängig von der Liste der zum Löschen ausgewählten Blobs.

1. Wenn nur Blobs mit Leases ausgewählt sind, wird die Schaltfläche „Löschen“ nicht angezeigt.
![Screenshot: Portal mit geöffnetem Containerblob-Listenbereich, in dem nur Blobs mit Leases ausgewählt sind](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Wenn eine Mischung aus Blobs mit Leases und verfügbaren Blobs ausgewählt ist, wird die Schaltfläche „Löschen“ angezeigt. Beim Löschvorgang werden jedoch die Seitenblobs zurückgelassen, für die eine Datenträgerlease besteht. 
![Screenshot: Portal mit geöffnetem Containerblob-Listenbereich, in dem sowohl Blobs mit Leases als auch verfügbare Blobs ausgewählt sind](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![Screenshot: Portal mit geöffnetem Löschbereich für ausgewählte Blobs](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Wenn der Benutzer versucht, den Löschvorgang mit PowerShell durchzuführen, tritt folgender Fehler auf. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : The remote server returned an error: (412) Es ist derzeit eine Lease für das Blob vorhanden, und in der Anforderung wurde keine Lease-ID angegeben. HTTP-Statuscode: 412 – HTTP-Fehlermeldung: Es ist derzeit eine Lease für das Blob vorhanden, und in der Anforderung wurde keine Lease-ID angegeben.</span>


## <a name="resolution-steps"></a>Lösungsschritte

### <a name="to-remove-classic-disks"></a>So entfernen Sie klassische Datenträger
Führen Sie die folgenden Schritte im Azure-Portal aus:
1.  Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2.  Navigieren Sie zu den Datenträgern (klassisch). 
3.  Klicken Sie auf die Registerkarte „Datenträger“. ![Screenshot: Azure-Portal mit ausgewählter Option „Datenträger (klassisch)“ sowie mit einem klassischen Datenträgernamen und Speicherkonto](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Wählen Sie Ihren Datenträger aus, und klicken Sie dann auf „Datenträger löschen“.
 ![Screenshot: Azure-Portal mit ausgewählter Option „Datenträger (klassisch)“ sowie mit einem ausgewählten Datenträger und der Option zum Löschen](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Wiederholen Sie den Löschvorgang, bei dem zuvor ein Fehler aufgetreten ist.
6.  Ein Speicherkonto oder Container kann nicht gelöscht werden, solange er über einen einzelnen Datenträger verfügt.

### <a name="to-remove-classic-images"></a>So entfernen Sie klassische Images   
Führen Sie die folgenden Schritte im Azure-Portal aus:
1.  Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2.  Navigieren Sie zu den Betriebssystemimages (klassisch).
3.  Löschen Sie das Image.
4.  Wiederholen Sie den Löschvorgang, bei dem zuvor ein Fehler aufgetreten ist.
5.  Ein Speicherkonto oder Container kann nicht gelöscht werden, solange er über ein einzelnes Image verfügt.
