---
title: 'Azure VMware Solution by CloudSimple: Erstellen eines virtuellen Computers in Azure mit VM-Vorlagen'
description: Beschreibt, wie virtuelle Computer in Azure mithilfe von VM-Vorlagen in der VMware-Infrastruktur für Ihre private CloudSimple-Cloud erstellt werden.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014500"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Erstellen eines virtuellen Computers in Azure mithilfe von VM-Vorlagen für die VMware-Infrastruktur

Sie können einen virtuellen Computer im Azure-Portal erstellen, indem Sie VM-Vorlagen für die VMware-Infrastruktur verwenden, die Ihr CloudSimple-Administrator für Ihr Abonnement aktiviert hat.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-cloudsimple-virtual-machine"></a>Erstellen einer CloudSimple-VM

1. Wählen Sie **Alle Dienste** aus.

2. Suchen Sie nach **CloudSimple-VMs**.

3. Klicken Sie auf **Hinzufügen**.

    ![Erstellen einer CloudSimple-VM](media/create-cloudsimple-virtual-machine.png)

4. Geben Sie grundlegende Informationen ein, und klicken Sie auf **Weiter:Größe**.

    > [!NOTE]
    > Für die Erstellung eines virtuellen CloudSimple-Computers in Azure ist eine VM-Vorlage erforderlich.  Diese VM-Vorlage sollte in Ihrem vCenter für die private Cloud vorhanden sein.  Erstellen Sie einen virtuellen Computer in der privaten Cloud über die vCenter-Benutzeroberfläche mit dem gewünschten Betriebssystem und den gewünschten Konfigurationen.  Erstellen Sie eine Vorlage, indem Sie die Anleitungen unter [Klonen eines virtuellen Computers in einer Vorlage im vSphere-Webclient](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html) befolgen.

    ![Erstellen einer CloudSimple-VM – Grundlagen](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Feld | BESCHREIBUNG |
    | ------------ | ------------- |
    | Subscription | Das mit Ihrer privaten Cloud verknüpfte Azure-Abonnement.  |
    | Ressourcengruppe | Die Ressourcengruppe, der die VM zugewiesen wird. Wählen Sie eine vorhandene Gruppe auswählen oder eine neue erstellen. |
    | Name | Der Name zur Identifizierung der VM.  |
    | Position | Azure-Region, in der die VM gehostet wird.  |
    | Private Cloud | Die private CloudSimple-Cloud, in der Sie den virtuellen Computer erstellen möchten. |
    | Ressourcenpool | Zugeordneter Ressourcenpool für die VM. Wählen Sie aus den verfügbaren Ressourcenpools aus. |
    | vSphere-Vorlage | vSphere-Vorlage für die VM.  |
    | Benutzername | Benutzername des VM-Administrators (für Windows-Vorlagen).|
    | Kennwort <br>Kennwort bestätigen | Kennwort des VM-Administrators (für Windows-Vorlagen).  |

5. Wählen Sie die Anzahl der Kerne und die Speicherkapazität für die VM und klicken Sie auf **Weiter:Konfigurationen**. Aktivieren Sie das Kontrollkästchen, wenn Sie vollständige CPU-Virtualisierung für das Gastbetriebssystem bereitstellen möchten, damit Anwendungen, die eine Hardwarevirtualisierung erfordern, auf virtuellen Computern ohne binäre Übersetzung oder Paravirtualisierung ausgeführt werden können. Weitere Informationen finden Sie im VMware-Artikel [Verfügbarmachen der hardwareunterstützten VMware-Virtualisierung](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Erstellen einer CloudSimple-VM – Größe](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurieren Sie Netzwerkschnittstellen und Datenträger wie in den folgenden Tabellen beschrieben und klicken Sie auf **Überprüfen + Erstellen**.

    ![Erstellen einer CloudSimple-VM – Konfigurationen](media/create-cloudsimple-virtual-machine-configurations.png)

    Klicken Sie bei Netzwerkschnittstellen auf **Netzwerkschnittstelle hinzufügen**, und konfigurieren Sie die folgenden Einstellungen.

    | Control | BESCHREIBUNG |
    | ------------ | ------------- |
    | Name | Geben Sie einen Namen zur Identifizierung der Schnittstelle ein.  |
    | Netzwerk | Wählen Sie aus der Liste der konfigurierten verteilten Portgruppen in Ihrer Private Cloud vSphere aus.  |
    | Adapter | Wählen Sie einen vSphere-Adapter aus der Liste der verfügbaren Typen, die für die VM konfiguriert sind. Weitere Informationen finden Sie im VMware-Knowledge Base-Artikel [Auswählen eines Netzwerkadapters für Ihren virtuellen Computer](https://kb.vmware.com/s/article/1001805). |
    | Einschalten beim Starten | Wählen Sie, ob die NIC-Hardware beim Booten der VM aktiviert werden soll. Die Standardeinstellung ist **Aktiviert**. |

    Klicken Sie bei Datenträgern auf **Datenträger hinzufügen** und konfigurieren Sie die folgenden Einstellungen.

    | Element | BESCHREIBUNG |
    | ------------ | ------------- |
    | Name | Geben Sie einen Namen zur Identifizierung des Datenträgers ein.  |
    | Size | Wählen Sie eine der verfügbaren Größen aus.  |
    | SCSI-Controller | Wählen Sie einen SCSI-Controller für den Datenträger aus.  |
    | Mode | Bestimmt, wie der Datenträger in Momentaufnahmen beteiligt ist. Wählen Sie eine der Optionen aus: <br> - Unabhängig dauerhaft: Alle Daten, die auf den Datenträger geschrieben werden, werden dauerhaft geschrieben.<br> - Unabhängig nicht dauerhaft: Auf dem Datenträger geschriebene Änderungen werden verworfen, wenn Sie den Computer ausschalten oder zurücksetzen.  Der unabhängige, nicht dauerhafte Modus ermöglicht es Ihnen, die VM immer im gleichen Zustand neu zu starten. Weitere Informationen finden Sie in der [VMware-Dokumentation](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Sobald die Validierung abgeschlossen ist, überprüfen Sie die Einstellungen und klicken Sie auf **Erstellen**. Um Änderungen vorzunehmen, klicken Sie auf die Registerkarten oben.

    ![Erstellen einer CloudSimple-VM – Überprüfung](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Anzeigen der Liste der CloudSimple-VMs

1. Wählen Sie **Alle Dienste** aus.

2. Suchen Sie nach **CloudSimple-VMs**.

3. Wählen Sie den virtuellen Computer aus, der in der privaten Cloud erstellt wurde.

    ![Liste der CloudSimple-VMs](media/list-cloudsimple-virtual-machines.png)

Die Liste der virtuellen CloudSimple-Computer enthält virtuelle Computer, die über das Azure-Portal erstellt wurden.  Virtuelle Computer, die in vCenter in der privaten Cloud im zugeordneten vCenter-Ressourcenpool erstellt wurden, werden in der Liste angezeigt.  
