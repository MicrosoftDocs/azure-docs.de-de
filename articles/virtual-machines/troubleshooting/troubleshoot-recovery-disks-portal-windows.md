---
title: Beheben von Problemen mit einer Windows-VM im Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme mit einer Windows-VM in Azure beheben, indem Sie über das Azure-Portal eine Verbindung zwischen dem Betriebssystemdatenträger und einer Wiederherstellungs-VM herstellen.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 04adf3903cd925f4507e94347251c762a576ff93
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735227"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>Beheben von Problemen mit einer Windows-VM durch Hinzufügen des Betriebssystemdatenträgers zu einer Wiederherstellungs-VM über das Azure-Portal
Wenn bei Ihrem virtuellen Windows-Computer in Azure ein Start- oder Datenträgerfehler auftritt, müssen Sie unter Umständen Schritte zur Problembehebung auf der virtuellen Festplatte (VHD) ausführen. Ein gängiges Beispiel ist ein fehlerhaftes Anwendungsupdate, das ein erfolgreiches Starten des virtuellen Computers verhindert. In diesem Artikel wird erläutert, wie über das Azure-Portal eine Verbindung zwischen Ihrer virtuellen Festplatte und einer anderen Windows-VM hergestellt wird, um Fehler zu beheben, und dann die ursprüngliche VM neu erstellt wird. 

## <a name="recovery-process-overview"></a>Übersicht über den Wiederherstellungsprozess
Der Problembehebungsprozess sieht wie folgt aus:

1. Beenden Sie die betroffene VM.
1. Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des virtuellen Computers.
1. Erstellen Sie eine virtuelle Festplatte aus der Momentaufnahme.
1. Fügen Sie einer anderen Windows-Problembehebungs-VM die virtuelle Festplatte hinzu, und stellen Sie sie bereit.
1. Stellen Sie eine Verbindung mit der Problembehebungs-VM her. Bearbeiten Sie Dateien, oder führen Sie ein beliebiges Tool zum Beheben von Problemen auf der ursprünglichen virtuellen Festplatte aus.
1. Heben Sie die Bereitstellung auf, und trennen Sie die virtuelle Festplatte von der Problembehebungs-VM.
1. Tauschen Sie den Betriebssystemdatenträger für den virtuellen Computer aus.

> [!NOTE]
> Dieser Artikel gilt nicht für VMs mit nicht verwalteten Datenträgern.

## <a name="take-a-snapshot-of-the-os-disk"></a>Erstellen einer Momentaufnahme des Betriebssystemdatenträgers
Eine Momentaufnahme ist eine vollständige, schreibgeschützte Kopie einer virtuellen Festplatte. Es empfiehlt sich, den virtuellen Computer vor dem Erstellen einer Momentaufnahme ordnungsgemäß herunterzufahren, um alle Prozesse, die ausgeführt werden, ordnungsgemäß abzuschließen. Gehen Sie folgendermaßen vor, um eine Momentaufnahme eines Betriebssystemdatenträgers zu erstellen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie **Virtuelle Computer** auf der Seitenleiste aus, und wählen Sie dann den virtuellen Computer aus, bei dem das Problem auftritt.
1. Klicken Sie im linken Bereich auf **Datenträger**, und wählen Sie dann den Namen des Betriebssystemdatenträgers aus.
    ![Screenshot der Datenträgereinstellungen mit dem Namen des Betriebssystemdatenträgers](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Wählen Sie auf der Seite **Übersicht** des Betriebssystemdatenträgers die Option **Momentaufnahme erstellen** aus.
1. Erstellen Sie eine Momentaufnahme in dem Standort, in dem sich auch der Betriebssystemdatenträger befindet.

## <a name="create-a-disk-from-the-snapshot"></a>Erstellen eines Datenträgers aus der Momentaufnahme
Um einen Datenträger aus der Momentaufnahme zu erstellen, führen Sie die folgenden Schritte aus:

1. Wählen Sie **Cloud Shell** im Azure-Portal aus.

    ![Screenshot der Schaltfläche zum Öffnen von Azure Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Führen Sie die folgenden PowerShell-Befehle aus, um einen verwalteten Datenträger aus der Momentaufnahme zu erstellen. Ersetzen Sie die Beispielnamen durch die zutreffenden Namen.

    ```powershell
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (for example, westus) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Wenn die Befehle erfolgreich ausgeführt wurden, wird der neue Datenträger in der von Ihnen bereitgestellten Ressourcengruppe angezeigt.

## <a name="attach-the-disk-to-another-vm"></a>Zuordnen des Datenträgers zu einem anderen virtuellen Computer
Verwenden Sie eine andere Problembehebungs-VM für die nächsten Schritte. Nachdem Sie den Datenträger der Problembehebungs-VM zugeordnet haben, können Sie den Inhalt des Datenträgers durchsuchen und bearbeiten. Durch diesen Prozess können Sie alle Konfigurationsfehler beheben oder zusätzliche Anwendungs- oder Systemprotokolldateien überprüfen. Führen Sie die folgenden Schritte aus, um den Datenträger einem andere virtuellen Computer zuzuordnen:

1. Wählen Sie im Portal Ihre Ressourcengruppe und dann Ihre Problembehebungs-VM aus. Wählen Sie **Datenträger** > **Bearbeiten** >  **Datenträger hinzufügen** aus.

    ![Screenshot mit Auswahlmöglichkeiten für das Hinzufügen eines vorhandenen Datenträgers im Portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Wählen Sie in der Liste **Datenträger** den Betriebssystemdatenträger der VM aus, die Sie identifiziert haben. Wenn der Betriebssystemdatenträger nicht angezeigt wird, stellen Sie sicher, dass sich die Problembehebungs-VM und der Betriebssystemdatenträger in derselben Region (am selben Standort) befinden. 
3. Klicken Sie auf **Speichern**, um die Änderungen zu übernehmen.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Bereitstellen des zugeordneten Datenträgers für die VM

1. Öffnen Sie eine Remotedesktopverbindung mit der Problembehebungs-VM. 
2. Öffnen Sie in der Problembehebungs-VM den **Server-Manager**, und wählen Sie dann **Datei- und Speicherdienste** aus. 

    ![Screenshot der Auswahl von Datei- und Speicherdiensten im Server-Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Der Datenträger wird automatisch erkannt und angefügt. Wählen Sie zum Anzeigen einer Liste mit den verbundenen Datenträgern die Option **Datenträger**. Sie können Ihren Datenträger auswählen, um die Volumeinformationen anzuzeigen, z.B. den Laufwerkbuchstaben. Im folgenden Beispiel ist der hinzugefügte Datenträger und die Verwendung von Laufwerk **F** zu sehen.

    ![Screenshot eines hinzugefügten Datenträgers mit Volumeinformationen in Server-Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>Beheben von Problemen auf der ursprünglichen virtuellen Festplatte
Nun, da die vorhandene virtuelle Festplatte bereitgestellt ist, können Sie jetzt alle Schritte zur Wartung und Problembehebung ausführen. Nachdem Sie alle Fehler behoben haben, fahren Sie mit den folgenden Schritten fort.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Aufheben der Bereitstellung und Trennen der ursprünglichen virtuellen Festplatte
Trennen Sie die vorhandene virtuelle Festplatte von Ihrer Problembehebungs-VM. Die virtuelle Festplatte kann erst wieder für eine andere VM verwendet werden, nachdem die Lease freigegeben wurde, die die virtuelle Festplatte an die Problembehebungs-VM anfügt.

1. Öffnen Sie in der Remotedesktop-Sitzung für Ihre VM den **Server-Manager**, und wählen Sie anschließend die Option **Datei- und Speicherdienste**.

    ![Screenshot der Auswahl von Datei- und Speicherdiensten im Server-Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Wählen Sie **Datenträger** aus, klicken Sie mit der rechten Maustaste auf Ihren Datenträger, und wählen Sie dann **Offline schalten** aus.

    ![Screenshot zum Festlegen des Datenträgers als offline im Server-Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Trennen Sie die virtuelle Festplatte von der VM. Wählen Sie Ihre VM im Azure-Portal aus, und klicken Sie dann auf **Datenträger**. 
4. Klicken Sie auf **Bearbeiten**, wählen Sie den angefügten Betriebssystemdatenträger aus, und klicken Sie dann auf **Löschen**.

    ![Screenshot mit Auswahlmöglichkeiten für das Trennen einer vorhandenen virtuellen Festplatte](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Bevor Sie fortfahren, warten Sie, bis der Datenträger auf der VM erfolgreich gelöscht wurde.

## <a name="swap-the-os-disk-for-the-vm"></a>Austauschen des Betriebssystemdatenträgers für den virtuellen Computer

Das Azure-Portal unterstützt jetzt den Austausch des Betriebssystemdatenträgers des virtuellen Computers. Gehen Sie dazu folgendermaßen vor:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Wählen Sie **Virtuelle Computer** auf der Seitenleiste aus, und wählen Sie dann den virtuellen Computer aus, bei dem das Problem auftritt.
1. Wählen Sie im linken Bereich **Datenträger** aus, und wählen Sie dann **Betriebssystemdatenträger austauschen** aus.
   
    ![Screenshot der Schaltfläche zum Austauschen eines Betriebssystemdatenträgers im Azure-Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Wählen Sie den neuen Datenträger aus, den Sie repariert haben, und geben Sie dann den Namen der VM ein, um die Änderung zu bestätigen. Wird der Datenträger in der Liste nicht angezeigt, warten Sie 10 bis 15 Minuten, nachdem Sie den Datenträger von der Problembehebungs-VM getrennt haben. Vergewissern Sie sich außerdem, dass sich der Datenträger am selben Standort wie der virtuelle Computer befindet.
1. Klicken Sie auf **OK**.

## <a name="next-steps"></a>Nächste Schritte
Wenn Probleme beim Herstellen einer Verbindung mit Ihrer VM auftreten, helfen Ihnen die Informationen unter [Behandeln von Problemen bei Remotedesktopverbindungen mit einem virtuellen Azure-Computer](troubleshoot-rdp-connection.md) weiter. Bei Problemen mit dem Zugriff auf Anwendungen, die auf Ihrer VM ausgeführt werden, lesen Sie den Artikel [Beheben von Anwendungskonnektivitätsproblemen auf einem virtuellen Windows-Computer](troubleshoot-app-connection.md).

Weitere Informationen zur Verwendung von Azure Resource Manager finden Sie in der [Übersicht über Azure Resource Manager](../../azure-resource-manager/management/overview.md).


