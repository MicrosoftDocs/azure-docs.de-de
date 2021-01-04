---
title: Problembehandlung einer fehlerhaften Azure-VM durch Verwenden geschachtelter Virtualisierung in Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Probleme mit einem virtuellen Azure-Computer unter Verwendung der geschachtelten Virtualisierung in Azure behandeln.
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: f950cb63b5083a85ab5420434abdd9a720115b1a
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734547"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Problembehandlung einer fehlerhaften Azure-VM durch Verwenden geschachtelter Virtualisierung in Azure

In diesem Artikel erfahren Sie, wie Sie in Microsoft Azure eine geschachtelte Virtualisierungsumgebung erstellen, um den Datenträger des fehlerhaften virtuellen Computers zur Problembehandlung auf dem Hyper-V-Host (Rettungs-VM) einbinden zu können.

## <a name="prerequisites"></a>Voraussetzungen

Zum Einbinden der fehlerhaften VM muss die Rettungs-VM die gleiche Art von Speicherkonto (Standard oder Premium) verwenden wie die fehlerhafte VM.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Schritt 1: Erstellen eines virtuellen Rettungscomputers und Installieren der Hyper-V-Rolle

1.  Erstellen Sie einen neuen virtuellen Rettungscomputer:

    -  Betriebssystem: Windows Server 2016 Datacenter

    -  Größe: Beliebige V3-Serie mit mindestens zwei Kernen, die die geschachtelte Virtualisierung unterstützen. Weitere Informationen finden Sie unter [Introducing the new Dv3 and Ev3 VM sizes](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) (Vorstellung der neuen VM-Größen Dv3 und Ev3).

    -  Gleicher Standort, gleiches Speicherkonto und gleiche Ressourcengruppe wie die fehlerhafte VM.

    -  Wählen Sie den gleichen Speichertyp wie auf der fehlerhaften VM aus (Standard oder Premium).

2.  Stellen Sie nach dem Erstellen des virtuellen Rettungscomputers eine Remotedesktopverbindung mit ihm her.

3.  Klicken Sie im Server-Manager auf **Verwalten** > **Rollen und Features hinzufügen**.

4.  Wählen Sie im Abschnitt **Installationstyp** die Option **Rollenbasierte oder featurebasierte Installation** aus.

5.  Vergewissern Sie sich im Abschnitt **Zielserver auswählen**, dass der virtuelle Rettungscomputer ausgewählt ist.

6.  Klicken Sie auf **Hyper-V-Rolle** > **Features hinzufügen**.

7.  Klicken Sie im Abschnitt **Features** auf **Weiter**.

8.  Sollte ein virtueller Switch verfügbar sein, wählen Sie ihn aus. Klicken Sie andernfalls auf **Weiter**.

9.  Klicken Sie im Abschnitt **Migration** auf **Weiter**.

10. Klicken Sie im Abschnitt **Standardspeicher** auf **Weiter**.

11. Aktivieren Sie das Kontrollkästchen für den automatischen Neustart des Servers, sofern erforderlich.

12. Wählen Sie **Installieren** aus.

13. Lassen Sie die Installation der Hyper-V-Rolle auf dem Server zu. Dieser Vorgang dauert einige Minuten. Anschließend wird der Server automatisch neu gestartet.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Schritt 2: Erstellen der fehlerhaften VM auf dem Hyper-V-Server des virtuellen Rettungscomputers

1.  [Erstellen Sie einen Momentaufnahmendatenträger](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) für den Betriebssystemdatenträger des virtuellen Computers mit dem Problem, und fügen Sie den Momentaufnahmendatenträger an den virtuellen Rettungscomputer an.

2.  Remotedesktopverbindung mit der Rettungs-VM.

3.  Öffnen Sie die Datenträgerverwaltung (diskmgmt.msc). Vergewissern Sie sich, dass der Datenträger der fehlerhaften VM den Status **Offline** aufweist.

4.  Öffnen Sie den Hyper-V Manager: Wählen Sie im **Server-Manager** die **Hyper-V-Rolle** aus. Klicken Sie mit der rechten Maustaste auf den Server, und wählen Sie den **Hyper-V Manager** aus.

5.  Klicken Sie im Hyper-V-Manager mit der rechten Maustaste auf den virtuellen Rettungscomputer, und klicken Sie auf **Neu** > **Virtueller Computer** > **Weiter**.

6.  Geben Sie einen Namen für den virtuellen Computer ein, und klicken Sie auf **Weiter**.

7.  Wählen Sie **Generation 1** aus.

8.  Legen Sie den Startspeicher auf mindestens 1.024 MB fest.

9. Wählen Sie ggf. den erstellten Hyper-V-Netzwerkswitch aus. Navigieren Sie andernfalls zur nächsten Seite.

10. Wählen Sie **Virtuelle Festplatte später zuordnen** aus.

    ![Abbildung mit der Option „Virtuelle Festplatte später zuordnen“](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Klicken Sie auf **Fertig stellen**, wenn der virtuelle Computer erstellt wurde.

12. Klicken Sie mit der rechten Maustaste auf den erstellten virtuellen Computer, und klicken Sie auf **Einstellungen**.

13. Klicken Sie auf **IDE-Controller 0** > **Festplatte** > **Hinzufügen**.

    ![Abbildung zum Hinzufügen der neuen Festplatte](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. Wählen Sie unter **Physische Festplatte** den Datenträger der fehlerhaften VM aus, die Sie die Azure-VM angefügt haben. Sollten keine Datenträger aufgeführt werden, überprüfen Sie mithilfe der Datenträgerverwaltung, ob der Datenträger auf „Offline“ festgelegt ist.

    ![Abbildung zum Einbinden des Datenträgers](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Klicken Sie auf **Apply** (Anwenden) und dann auf **OK**.

16. Doppelklicken Sie auf den virtuellen Computer, und starten Sie ihn.

17. Der virtuelle Computer kann nun als lokaler virtueller Computer verwendet werden. Sie können beliebige Problembehandlungsschritte ausführen.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Schritt 3: Ersetzen des Betriebssystemdatenträgers, der von der fehlerhaften VM verwendet wird

1.  Wenn Sie den virtuellen Computer wieder online geschaltet haben, fahren Sie den virtuellen Computer im Hyper-V-Manager herunter.

2.  [Heben Sie die Bereitstellung auf, und trennen Sie den reparierten Betriebssystemdatenträger.](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-the-original-virtual-hard-disk
)
3.  [Ersetzen Sie den verwendeten Betriebssystemdatenträger durch die VM mit dem reparierten Betriebssystemdatenträger.](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
)

## <a name="next-steps"></a>Nächste Schritte

Wenn Probleme beim Herstellen einer Verbindung mit Ihrer VM auftreten, helfen Ihnen die Informationen unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](troubleshoot-rdp-connection.md) weiter. Konsultieren Sie [Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](troubleshoot-app-connection.md) bei Problemen mit dem Zugriff auf Anwendungen, die auf Ihrer VM ausgeführt werden.
