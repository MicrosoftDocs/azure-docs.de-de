---
title: Schnellstart zur VMware-Lösung von CloudSimple – Nutzen von VMware-VMs in Azure
description: In diesem Schnellstart erfahren Sie, wie Sie VMware-VMs aus dem Azure-Portal mit der Azure-VMware-Lösung von CloudSimple konfigurieren und nutzen können.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b430efbc931d72de4b095a7eac4c1e7ca496b1b9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393498"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Schnellstart: Nutzen von virtuellen VMware-Computern in Azure

Um einen virtuellen Computer im Azure-Portal zu erstellen, können Sie VM-Vorlagen verwenden, die in der vCenter-Instanz Ihrer privaten Cloud verfügbar sind. Ein vCenter-Administrator kann zusätzliche Vorlagen für die private Cloud erstellen.

## <a name="create-a-vm-template"></a>Erstellen einer VM-Vorlage

Erstellen Sie im ersten Schritt über die vCenter-Benutzeroberfläche eine VM in Ihrer private Cloud. Um eine Vorlage zu erstellen, folgen Sie den Anweisungen im VMware-Artikel [Klonen einer virtuellen Maschine in eine Vorlage im vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Speichern Sie die VM-Vorlage in dem vCenter Ihrer privaten Cloud.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Erstellen eines virtuellen Computers im Azure-Portal

1. Wählen Sie **Alle Dienste** aus.

2. Suchen Sie nach **CloudSimple-VMs**.

3. Wählen Sie **Hinzufügen**.

    ![Auswahl von „Hinzufügen“](media/create-cloudsimple-virtual-machine.png)

4. Geben Sie die folgenden Informationen zur VM ein, und klicken Sie dann auf **Weiter: Größe**.

    ![Erstellen einer CloudSimple-VM – Grundlagen](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Feld | BESCHREIBUNG |
    | ------------ | ------------- |
    | **Abonnement** | Das mit Ihrer privaten Cloud verknüpfte Azure-Abonnement.  |
    | **Ressourcengruppe** | Die Ressourcengruppe, der die VM zugewiesen wird. Wählen Sie eine vorhandene Gruppe auswählen oder eine neue erstellen. |
    | **Name** | Ein Name zur Identifizierung der VM.  |
    | **Location** | Die Azure-Region, in der die VM gehostet wird.  |
    | **Private Cloud** | Die private CloudSimple-Cloud, in der Sie die VM erstellen möchten. |
    | **ResourcePool** | Ein zugeordneter Ressourcenpool für die VM. Wählen Sie aus den verfügbaren Ressourcenpools aus. |
    | **vSphere-Vorlage** | Die vSphere-Vorlage für die VM.  |
    | **Benutzername** | Der Benutzername des VM-Administrators (für Windows-Vorlagen).|
    | **Kennwort** |  Das Kennwort des VM-Administrators (für Windows-Vorlagen). |
    | **Kennwort bestätigen** | Das Kennwort, das Sie im vorherigen Feld angegeben haben. |

5. Wählen Sie die Anzahl von Kernen und die Speicherkapazität für die VM aus. Aktivieren Sie das Kontrollkästchen **Für das Gastbetriebssystem bereitstellen**, wenn Sie die vollständige CPU-Virtualisierung für das Gastbetriebssystem bereitstellen möchten. Anwendungen, die eine Hardwarevirtualisierung erfordern, können auf virtuellen Computern ohne binäre Übersetzung oder Paravirtualisierung ausgeführt werden. Weitere Informationen finden Sie im VMware-Artikel <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Verfügbarmachen der hardwareunterstützten VMware-Virtualisierung</a>. Wenn Sie fertig sind, klicken Sie auf **Weiter: Konfigurationen**.

    ![Erstellen einer CloudSimple-VM – Größe](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurieren Sie Netzwerkschnittstellen und Datenträger wie in den folgenden Tabellen beschrieben, und klicken Sie dann auf **Überprüfen + erstellen**.

    ![Erstellen einer CloudSimple-VM – Konfigurationen](media/create-cloudsimple-virtual-machine-configurations.png)

    Wählen Sie für Netzwerkschnittstellen **Netzwerkschnittstelle hinzufügen** aus, und konfigurieren Sie die folgenden Einstellungen:
    
    | Einstellung | BESCHREIBUNG |
    | ------------ | ------------- |
    | **Name** | Geben Sie einen Namen zur Identifizierung der Schnittstelle ein.  |
    | **Netzwerk** | Treffen Sie eine Auswahl in der Liste der verteilten Portgruppen, die für die vSphere-Instanz Ihrer privaten Cloud konfiguriert sind.  |
    | **Adapter** | Wählen Sie einen vSphere-Adapter aus der Liste der verfügbaren Typen aus, die für die VM konfiguriert sind. Weitere Informationen finden Sie im VMware-Artikel <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Auswählen eines Netzwerkadapters für Ihre virtuelle Maschine</a>. |
    | **Beim Start einschalten** | Wählen Sie, ob die NIC-Hardware beim Booten der VM aktiviert werden soll. Die Standardeinstellung ist **Aktiviert**. |

    Klicken Sie bei Datenträgern auf **Datenträger hinzufügen**, und konfigurieren Sie die folgenden Einstellungen:

    | Einstellung | BESCHREIBUNG |
    | ------------ | ------------- |
    | **Name** | Geben Sie einen Namen zur Identifizierung des Datenträgers ein.  |
    | **Größe** | Wählen Sie eine der verfügbaren Größen aus.  |
    | **SCSI-Controller** | Wählen Sie einen SCSI-Controller für den Datenträger aus.  |
    | **Mode** | Der Modus bestimmt, wie der Datenträger in Momentaufnahmen beteiligt ist. Wählen Sie eine der Optionen aus: <br> **Unabhängig, persistent** Alle auf den Datenträger geschriebenen Änderungen werden dauerhaft gespeichert.<br> **Unabhängig, nicht persistent**: Auf dem Datenträger geschriebene Änderungen werden verworfen, wenn Sie den Computer ausschalten oder zurücksetzen. Der unabhängige, nicht dauerhafte Modus ermöglicht es Ihnen, die VM immer im gleichen Zustand neu zu starten. Weitere Informationen finden Sie in der <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware-Dokumentation</a>.

7. Überprüfen Sie nach Abschluss der Validierung die Einstellungen, und klicken Sie auf **Erstellen**. Um Änderungen durchzuführen, wählen Sie die Registerkarten oben aus, oder klicken Sie auf **Zurück: Konfigurationen**.

    ![Erstellen einer CloudSimple-VM – „Überprüfen und erstellen“](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen der Liste der virtuelle CloudSimple-VMs](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Verwalten einer CloudSimple-VM aus Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
