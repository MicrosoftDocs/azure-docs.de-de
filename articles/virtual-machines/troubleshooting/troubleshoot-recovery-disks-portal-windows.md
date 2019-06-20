---
title: Verwenden einer Windows-Problembehebungs-VM im Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme auf einer Windows-VM in Azure beheben, indem Sie mithilfe des Azure-Portals eine Verbindung zwischen dem Betriebssystemdatenträger und einer Wiederherstellungs-VM herstellen.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/13/2018
ms.author: genli
ms.openlocfilehash: ec2da7d9f659f32c40f7a2685ab08be4eec27ed5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320822"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Beheben von Problemen einer Windows-VM durch Hinzufügen des Betriebssystemdatenträgers zu einer Wiederherstellungs-VM mit dem Azure-Portal
Wenn für Ihren virtuellen Windows-Computer in Azure ein Start- oder Datenträgerfehler auftritt, müssen Sie unter Umständen Schritte zur Problembehebung auf der virtuellen Festplatte selbst ausführen. Ein gängiges Beispiel wäre ein ungültiges Anwendungsupdate, das den erfolgreichen Start der VM verhindert. In diesem Artikel wird erläutert, wie das Azure-Portal die Verbindung zwischen Ihrer virtuellen Festplatte und einer anderen Windows-VM herstellt, um alle Fehler zu beheben und dann Ihre ursprüngliche VM neu zu erstellen.

## <a name="recovery-process-overview"></a>Übersicht über den Wiederherstellungsprozess
Der Problembehebungsprozess sieht wie folgt aus:

1. Löschen Sie die VM, auf der Probleme auftreten, und behalten Sie die virtuellen Festplatten bei.
2. Fügen Sie einer anderen Windows-Problembehebungs-VM die virtuelle Festplatte hinzu, und stellen Sie sie bereit.
3. Stellen Sie eine Verbindung mit der Problembehebungs-VM her. Bearbeiten Sie Dateien, oder führen Sie ein beliebiges Tool zum Beheben von Problemen auf der ursprünglichen virtuellen Festplatte aus.
4. Heben Sie die Bereitstellung auf, und trennen Sie die virtuelle Festplatte von der Problembehebungs-VM.
5. Erstellen Sie eine VM mithilfe der ursprünglichen virtuellen Festplatte.

Mit Azure PowerShell können Sie für einen virtuellen Computer, der verwaltete Datenträger verwendet, nun den Betriebssystemdatenträger ändern. Sie müssen die VM nicht löschen und neu erstellen. Weitere Informationen finden Sie unter [Beheben von Problemen mit einer Windows-VM durch Hinzufügen des Betriebssystemdatenträgers zu einer Wiederherstellungs-VM per Azure PowerShell](troubleshoot-recovery-disks-windows.md).

## <a name="determine-boot-issues"></a>Bestimmen von Problemen beim Start
Überprüfen Sie den Screenshot mit der Startdiagnose der VM, um zu bestimmen, warum Ihre VM nicht richtig gestartet werden kann. Ein gängiges Beispiel wäre ein ungültiges Anwendungsupdate oder eine zugrunde liegende virtuelle Festplatte, die gelöscht oder verschoben wird.

Wählen Sie Ihre VM im Portal aus, und scrollen Sie dann nach unten zum Abschnitt **Unterstützung + Problembehebung**. Klicken Sie auf **Boot diagnostics** (Startdiagnose), um den Screenshot anzuzeigen. Beachten Sie konkrete Fehlermeldungen oder Fehlercodes, um besser ermitteln zu können, warum für die VM ein Problem vorliegt. 

![Anzeigen der Konsolenprotokolle für die VM-Startdiagnose](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Sie können auch auf **Screenshot herunterladen** klicken, um eine Aufnahme des VM-Screenshots herunterzuladen.

## <a name="view-existing-virtual-hard-disk-details"></a>Anzeigen von Details vorhandener virtueller Festplatten
Bevor Sie Ihre virtuelle Festplatte zu einer anderen VM hinzufügen können, müssen Sie den Namen der virtuellen Festplatte (VHD) identifizieren. 

Wählen Sie im Portal Ihre Ressourcengruppe und dann Ihr Speicherkonto aus. Klicken Sie auf **Blobs**, wie im folgenden Beispiel:

![Auswählen von Speicherblobs](./media/troubleshoot-recovery-disks-portal-windows/storage-account-overview.png)

In der Regel verfügen Sie über einen Container namens **VHDs**, der Ihre virtuellen Festplatten speichert. Wählen Sie die Container aus, um eine Liste der virtuellen Festplatten anzuzeigen. Notieren Sie den Namen Ihrer virtuellen Festplatte (das Präfix ist in der Regel der Name Ihrer VM):

![Identifizieren der VHD im Speichercontainer](./media/troubleshoot-recovery-disks-portal-windows/storage-container.png)

Wählen Sie Ihre vorhandenen virtuellen Festplatte aus der Liste aus, und kopieren Sie die URL zur Verwendung in den folgenden Schritten:

![Kopieren vorhandener virtueller Festplatten-URLS](./media/troubleshoot-recovery-disks-portal-windows/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Löschen einer vorhandener VM
Virtuelle Festplatten und VMs sind zwei unterschiedliche Ressourcen in Azure. Eine virtuelle Festplatte ist der Ort, an dem das Betriebssystem selbst, Anwendungen und Konfigurationen gespeichert werden. Die VM selbst besteht nur aus Metadaten, die die Größe oder Position definieren und auf Ressourcen verweisen, z.B. eine virtuelle Festplatte oder virtuelle Netzwerkschnittstellenkarte (NIC). Jede virtuelle Festplatte verfügt über eine zugewiesene Lease, wenn sie mit einer VM verknüpft ist. Obwohl Datenträger auch bei laufendem Betrieb der VM hinzugefügt und getrennt werden können, kann der Betriebssystemdatenträger nicht getrennt werden, es sei denn, die VM-Ressource wird gelöscht. Die Lease ordnet den Betriebssystemdatenträger weiterhin einer VM zu, selbst wenn diese VM beendet und die Zuordnung aufgehoben ist.

Der erste Schritt beim Wiederherstellen Ihrer VM ist das Löschen der VM-Ressource selbst. Das Löschen der VM belässt die virtuellen Festplatten in Ihrem Speicherkonto. Nachdem die VM gelöscht wird, fügen Sie die virtuelle Festplatte zu einer anderen VM hinzu, um die Fehler zu beheben.

Wählen Sie Ihre VM im Portal aus, und klicken Sie auf **Löschen**:

![Der Screenshot der VM-Startdiagnose zeigt einen Startfehler](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Warten Sie, bis die VM gelöscht wurde, bevor Sie die virtuelle Festplatte einer anderen VM hinzufügen. Die Lease auf der virtuellen Festplatte, die sie zur VM zuordnet, muss freigegeben werden, bevor Sie die virtuelle Festplatte einer anderen VM hinzufügen können.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Hinzufügen einer vorhandenen virtuellen Festplatte zu einer anderen VM
Verwenden Sie eine andere Problembehebungs-VM für die nächsten Schritte. Fügen Sie die vorhandene virtuelle Festplatte zu dieser Problembehebungs-VM hinzu, um den Inhalt des Datenträgers zu durchsuchen und zu bearbeiten. Durch diesen Prozess können Sie z.B. alle Konfigurationsfehler beheben oder zusätzliche Anwendungs- oder Systemprotokolldateien überprüfen. Wählen Sie eine andere Problembehebungs-VM aus, oder erstellen Sie eine.

1. Wählen Sie im Portal Ihre Ressourcengruppe und dann Ihre Problembehebungs-VM aus. Wählen Sie **Datenträger** aus, und klicken Sie dann auf **Vorhandener hinzufügen**:

    ![Hinzufügen des vorhandenen Datenträgers im Portal](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Klicken Sie auf **VHD-Datei**, um Ihre vorhandene virtuelle Festplatte auszuwählen:

    ![Navigieren zu einer vorhandenen VHD](./media/troubleshoot-recovery-disks-portal-windows/select-vhd-location.png)

3. Wählen Sie Ihr Speicherkonto und Ihren Container aus, und klicken Sie auf Ihre vorhandenen VHD. Klicken Sie auf die Schaltfläche **Auswählen**, um Ihre Auswahl zu bestätigen:

    ![Auswählen Ihrer vorhandenen VHD](./media/troubleshoot-recovery-disks-portal-windows/select-vhd.png)

4. Nachdem Sie Ihre VHD ausgewählt haben, klicken Sie auf **OK**, um die vorhandene virtuelle Festplatte hinzuzufügen:

    ![Bestätigen Sie das Hinzufügen der vorhandene virtuellen Festplatten](./media/troubleshoot-recovery-disks-portal-windows/attach-disk-confirm.png)

5. Nach wenigen Sekunden zeigt der Bereich **Datenträger** für Ihre VM Ihre vorhandenen virtuellen Festplatte an, die als Datenträger verbunden ist:

    ![Vorhandene virtuelle Festplatte, die als Datenträger angefügt ist](./media/troubleshoot-recovery-disks-portal-windows/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Bereitstellen des hinzugefügten Datenträgers

1. Öffnen Sie eine Remotedesktopverbindung mit Ihrem virtuellen Computer. Wählen Sie Ihre VM im Portal aus, und klicken Sie auf **Verbinden**. Laden Sie die RDP-Verbindungsdatei herunter, und öffnen Sie sie. Geben Sie Ihre Anmeldeinformationen ein, um sich wie folgt auf der VM anzumelden:

    ![Anmelden auf der VM per Remotedesktop](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Öffnen Sie **Server-Manager**, und wählen Sie dann die Option **Datei- und Speicherdienste**. 

    ![Auswählen von Datei- und Speicherdiensten im Server-Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Der Datenträger wird automatisch erkannt und angefügt. Wählen Sie zum Anzeigen einer Liste mit den verbundenen Datenträgern die Option **Datenträger**. Sie können Ihren Datenträger auswählen, um die Volumeinformationen anzuzeigen, z.B. den Laufwerkbuchstaben. Im folgenden Beispiel ist zu sehen, wie der Datenträger angefügt wird und den Buchstaben **F:** aufweist:

    ![Angefügter Datenträger und Volumeinformationen in Server-Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Beheben von Problemen auf der ursprünglichen virtuellen Festplatte
Nun, da die vorhandene virtuelle Festplatte bereitgestellt ist, können Sie jetzt alle Schritte zur Wartung und Problembehebung ausführen. Fahren Sie mit den folgenden Schritte fort, nachdem Sie die Probleme behoben haben.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Aufheben der Bereitstellung und Trennen der ursprünglichen virtuellen Festplatte
Sobald Ihre Fehler behoben sind, trennen Sie die vorhandene virtuelle Festplatte von Ihrer Problembehebungs-VM. Sie können Ihre virtuelle Festplatte nicht mit einer anderen VM nutzen, bis die Lease freigegeben wird, die die virtuelle Festplatte zur Problembehebungs-VM hinzufügt.

1. Öffnen Sie in der RDP-Sitzung für Ihre VM den **Server-Manager**, und wählen Sie anschließend die Option **Datei- und Speicherdienste**:

    ![Auswählen von Datei- und Speicherdiensten im Server-Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Wählen Sie die Option **Datenträger**, und wählen Sie dann Ihren Datenträger aus. Klicken Sie mit der rechten Maustaste auf Ihren Datenträger, und wählen Sie die Option **Take Offline** (Offline schalten):

    ![Festlegen des Offlinezustands für den Datenträger in Server-Manager](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Trennen Sie die virtuelle Festplatte von der VM. Wählen Sie Ihre VM im Azure-Portal aus, und klicken Sie auf **Datenträger**. Wählen Sie Ihre vorhandene virtuelle Festplatte aus, und klicken Sie dann auf **Trennen**:

    ![Trennen einer vorhandenen virtuellen Festplatte](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Warten Sie, bis die VM den Datenträger erfolgreich getrennt hat, bevor Sie fortfahren.

## <a name="create-vm-from-original-hard-disk"></a>Erstellen einer VM von der ursprünglichen Festplatte
Verwenden Sie zum Erstellen einer VM von Ihrer ursprünglichen virtuellen Festplatte [diese Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet). Die Vorlage stellt eine VM in einem vorhandenen oder neuen virtuellen Netzwerk bereit und nutzt die VHD-URL aus dem früheren Befehl. Klicken Sie wie folgt auf die Schaltfläche **Deploy to Azure** (Bereitstellung in Azure):

![Bereitstellen eines virtuellen Computers aus der GitHub-Vorlage](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

Die Vorlage wird im Azure-Portal für die Bereitstellung geladen. Geben Sie die Namen für Ihre neue VM und die vorhandenen Azure-Ressourcen ein, und fügen Sie die URL zu Ihrer vorhandenen virtuellen Festplatte hinzu. Um die Bereitstellung zu beginnen, klicken Sie auf **Kaufen**:

![Erstellen einer VM über eine Vorlage](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Erneutes Aktivieren der Startdiagnose
Wenn Sie Ihre VM aus der vorhandenen virtuellen Festplatte erstellen, werden Startdiagnoseeinstellungen möglicherweise nicht automatisch aktiviert. Wählen Sie Ihre VM im Portal aus, um den Status der Startdiagnoseeinstellungen zu überprüfen und sie ggf. zu aktivieren. Klicken Sie unter **Überwachen** auf **Diagnoseeinstellungen**. Stellen Sie sicher, dass der Status **An** lautet und das Häkchen neben **Startdiagnose** gesetzt ist. Wenn Sie Änderungen vornehmen, klicken Sie auf **Speichern**:

![Aktualisieren der Startdiagnoseeinstellungen](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Nächste Schritte
Wenn Probleme beim Herstellen einer Verbindung mit Ihrer VM auftreten, helfen Ihnen die Informationen unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](troubleshoot-rdp-connection.md) weiter. Konsultieren Sie [Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](troubleshoot-app-connection.md) bei Problemen mit dem Zugriff auf Anwendungen, die auf Ihrer VM ausgeführt werden.

Weitere Informationen zu Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

