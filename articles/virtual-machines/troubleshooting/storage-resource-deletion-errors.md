---
title: Problembehandlung bei Speicherressourcen-Löschfehlern auf Linux-VMs in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie auf Linux-VMs Probleme beim Löschen von Speicherressourcen beheben, die angefügte VHDs enthalten.
keywords: ''
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 8d727bc8bdc8f015504baa57f9596b3bacac9712
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022869"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Behebung von Problemen mit Speicherressourcen-Löschfehlern

In bestimmten Szenarios könnten Sie auf einen der folgenden Fehler treffen, wenn Sie versuchen, ein Azure-Speicherkonto, einen Container oder ein Blob in einer Azure Resource Manager-Bereitstellung zu löschen:

> **Fehler beim Löschen von Speicherkonto 'StorageAccountName'. Error: Das Speicherkonto kann nicht gelöscht werden, da zugehörige Artefakte gerade verwendet werden.**
> 
> **Fehler beim Löschen von # von # Container(n):<br>vhds: Es ist derzeit eine Lease für den Container vorhanden, und in der Anforderung wurde keine Lease-ID angegeben.**
> 
> **Fehler beim Löschen von # von # Blob(s):<br>BlobName.vhd: Es ist derzeit eine Lease für das Blob vorhanden, und in der Anforderung wurde keine Lease-ID angegeben.**

Die auf virtuellen Azure-Computern verwendeten VHDs sind VHD-Dateien, die als Seitenblobs in einem Standard- oder Premium-Speicherkonto in Azure gespeichert sind. Weitere Informationen zu Azure-Datenträgern finden Sie in unserer [Einführung in verwaltete Datenträger](../managed-disks-overview.md).

Azure verhindert das Löschen eines an eine VM angefügten Datenträgers, um eine Beschädigung zu vermeiden. Zudem verhindert es das Löschen von Containern und Speicherkonten mit einem Seitenblob, das an einen virtuellen Computer angefügt ist. 

Der Prozess zum Löschen eines Speicherkontos, Containers oder Blobs bei einem dieser Fehler umfasst folgende Schritte: 
1. Identifizieren von an eine VM angefügten Blobs
2. [Löschen von VMs mit angefügtem **Betriebssystemdatenträger**](#step-2-delete-vm-to-detach-os-disk)
3. [Trennen aller **Datenträger** von verbleibenden VMs](#step-3-detach-data-disk-from-the-vm)

Wenn Sie diese Schritte durchgeführt haben, versuchen Sie erneut, das Speicherkonto, einen Container bzw. ein Blob zu löschen.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Schritt 1: Identifizieren eines an eine VM angefügten Blobs

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Szenario 1: Löschen eines Blobs – Identifizieren einer angefügten VM
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü „Hub“ die Option **Alle Ressourcen** aus. Wechseln Sie zum Speicherkonto, wählen Sie unter **Blobdienst** die Option **Container** aus, und navigieren Sie zum Blob, das gelöscht werden soll.
3. Wenn das Blob als **Leasezustand** den Zustand **Geleast** aufweist, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Metadaten bearbeiten**, um den Bereich „Blobmetadaten“ zu öffnen. 

    ![Screenshot des Portals, in dem die Speicherkontoblobs und die per Rechtsklick aufgerufene Option „Metadaten bearbeiten“ hervorgehoben sind](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. Überprüfen und notieren Sie im Bereich „Blobmetadaten“ den Wert für **MicrosoftAzureCompute_VMName**. Dieser Wert ist der Name der VM, der die VHD angefügt ist. (Wenn dieses Feld nicht vorhanden ist, siehe **Wichtig**.)
5. Überprüfen und notieren Sie im Bereich „Blobmetadaten“ den Wert für **MicrosoftAzureCompute_DiskType**. Mit diesem Wert wird ermittelt, ob es sich beim angefügten Datenträger um einen Betriebssystemdatenträger oder Datenträger handelt (wenn dieses Feld nicht vorhanden ist. siehe **Wichtig**). 

     ![Screenshot des Portals, in dem der Speicherbereich „Blobmetadaten“ geöffnet ist](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Wenn der Blobdatenträger vom Typ **OSDisk** ist, führen Sie [Schritt 2: Löschen einer VM beim Trennen eines Betriebssystemdatenträgers](#step-2-delete-vm-to-detach-os-disk) durch. Wenn der Blobdatenträger hingegen vom Typ **DataDisk** ist, befolgen Sie die Anweisungen unter [Schritt 3: Trennen eines Datenträgers von der VM](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Wenn **MicrosoftAzureCompute_VMName** und **MicrosoftAzureCompute_DiskType** nicht in den Blobmetadaten angezeigt werden, bedeutet dies, dass das Blob explizit geleast und nicht an eine VM angefügt ist. Geleaste Blobs können erst gelöscht werden, wenn das Lease abgebrochen wurde. Um ein Lease abzubrechen, klicken Sie mit der rechten Maustaste auf das Blob, und wählen Sie **Lease abbrechen** aus. Geleaste Blobs, die nicht an einen virtuellen Computer angefügt sind, verhindern das Löschen des Blobs, jedoch nicht das Löschen eines Containers oder Speicherkontos.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Szenario 2: Löschen eines Containers – Identifizieren aller Blobs in einem an VMs angefügten Container
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü „Hub“ die Option **Alle Ressourcen** aus. Wechseln Sie zum Speicherkonto, wählen Sie unter **Blobdienst** die Option **Container** aus, und machen Sie den Container ausfindig, der gelöscht werden soll.
3. Klicken Sie darauf, um den Container zu öffnen. Daraufhin wird die Liste der darin enthaltenen Blobs angezeigt. Machen Sie in dieser Liste alle Blobs vom Blobtyp **Seitenblob** und mit dem Leasezustand **Geleast** ausfindig. Führen Sie die Schritte von Szenario 1 durch, um die mit diesen Blobs verknüpfte VM zu identifizieren.

    ![Screenshot des Portals, in dem die Speicherkontoblobs und die Option „Leasezustand“ mit dem Wert „Geleast“ hervorgehoben sind](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Führen Sie [Schritt 2](#step-2-delete-vm-to-detach-os-disk) und [Schritt 3](#step-3-detach-data-disk-from-the-vm) durch, um VMs mit Datenträgern vom Typ **OSDisk** zu löschen und Datenträger vom Typ **DataDisk** zu trennen. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Szenario 3: Löschen eines Speicherkontos – Identifizieren aller Blobs in einem an VMs angefügten Speicherkonto
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü „Hub“ die Option **Alle Ressourcen** aus. Wechseln Sie zum Speicherkonto, und wählen Sie unter **Blobdienst** die Option **Blobs** aus.
3. Identifizieren Sie im Bereich **Container** alle Container, die als **Leasezustand** den Wert **Geleast** aufweisen, und führen Sie für jeden Container mit dem Zustand **Geleast** die Schritte von [Szenario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) durch.
4. Führen Sie [Schritt 2](#step-2-delete-vm-to-detach-os-disk) und [Schritt 3](#step-3-detach-data-disk-from-the-vm) durch, um VMs mit Datenträgern vom Typ **OSDisk** zu löschen und Datenträger vom Typ **DataDisk** zu trennen. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Schritt 2: Löschen einer VM beim Trennen eines Betriebssystemdatenträgers
Wenn es sich bei der VHD um einen Betriebssystemdatenträger handelt, müssen Sie zuerst die VM löschen, bevor Sie die angefügte VHD löschen können. Für Datenträger, die an dieselbe VM angefügt sind, sind keine weiteren Aktionen erforderlich, nachdem Sie die folgenden Schritte durchgeführt haben:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü „Hub“ die Option **Virtuelle Computer** aus.
3. Wählen Sie die VM aus, an die die VHD angefügt ist.
4. Stellen Sie sicher, dass der virtuelle Computer nicht aktiv verwendet wird und dass Sie den virtuellen Computer nicht mehr benötigen.
5. Wählen Sie oben im Bereich **Details zum virtuellen Computer** die Option **Löschen** aus, und klicken Sie zur Bestätigung auf **Ja**.
6. Die VM sollte nun gelöscht, doch die VHD noch vorhanden sein. Die VHD sollte nicht mehr an eine VM angefügt bzw. es sollte keine Lease mehr für diese vorhanden sein. Es kann einige Minuten dauern, bis die Lease freigegeben wird. Um sich zu vergewissern, dass das Lease aufgehoben wurde, navigieren Sie zum Blobspeicherort, und prüfen Sie im Bereich **Blobeigenschaften**, ob der **Leasezustand** den Zustand **Verfügbar** aufweist.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Schritt 3: Trennen eines Datenträgers von der VM
Wenn die VHD ein Datenträger ist, trennen Sie die VHD von der VM, um die Lease zu entfernen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü „Hub“ die Option **Virtuelle Computer** aus.
3. Wählen Sie die VM aus, an die die VHD angefügt ist.
4. Wählen Sie im Bereich **Details zum virtuellen Computer** die Option **Datenträger**.
5. Wählen Sie den zu löschenden Datenträger aus, der die VHD angefügt ist. Anhand der URL der VHD können Sie erkennen, welches Blob an den Datenträger angefügt wurde.
6. Sie können den Blobspeicherort überprüfen, indem Sie durch Klicken auf den Datenträger den Pfad im Feld **VHD-URI** überprüfen.
7. Wählen Sie oben im Bereich **Datenträger** die Option **Bearbeiten** aus.
8. Klicken Sie für den zu löschenden Datenträger auf das Symbol **Trennen**.

     ![Screenshot des Portals mit geöffnetem Speicherbereich „Blobmetadaten“ und hervorgehobenem Symbol für das Trennen des zu löschenden Datenträgers](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Wählen Sie **Speichern** aus. Der Datenträger ist jetzt vom virtuellen Computer getrennt, und die VHD wird nicht mehr geleast. Es kann einige Minuten dauern, bis die Lease freigegeben wird. Um sich zu vergewissern, dass das Lease aufgehoben wurde, navigieren Sie zum Blobspeicherort, und prüfen Sie im Bereich **Blobeigenschaften**, ob der **Leasezustand** den Zustand **Entsperrt** oder **Verfügbar** aufweist.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

