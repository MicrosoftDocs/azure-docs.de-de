---
title: Bereitstellen von VMs auf Azure Stack Edge Pro über das Azure-Portal
description: Erfahren Sie, wie Sie VMs auf Azure Stack Edge Pro über das Azure-Portal erstellen und verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/02/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 89ef196cb5a124b8b1100871c408400f3fceef5c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465431"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Bereitstellen von VMs auf einem Azure Stack Edge Pro-GPU-Gerät über das Azure-Portal

Sie können virtuelle Computer (Virtual Machines, VMs) auf einem Azure Stack Edge-Gerät über das Azure-Portal, Vorlagen, Azure PowerShell-Cmdlets sowie über die Azure CLI bzw. Python-Skripts erstellen und verwalten. In diesem Artikel wird beschrieben, wie Sie eine VM auf Ihrem Azure Stack Edge-Gerät über das Azure-Portal erstellen und verwalten. 

Dieser Artikel bezieht sich auf Geräte vom Typ „Azure Stack Edge Pro-GPU“, „Azure Stack Edge Pro R“ und „Azure Stack Edge Mini R“. 

## <a name="vm-deployment-workflow"></a>VM-Bereitstellungsworkflow

Die allgemeine Zusammenfassung des Bereitstellungsworkflows lautet wie folgt:

1. Aktivieren einer Netzwerkschnittstelle für Compute auf dem Azure Stack Edge-Gerät Dadurch wird ein virtueller Switch an der angegebenen Netzwerkschnittstelle erstellt.
1. Aktivieren Sie die Cloudverwaltung von virtuellen Computern über das Azure-Portal.
1. Laden Sie mit dem Storage-Explorer eine VHD in ein Azure Storage-Konto hoch. 
1. Verwenden Sie die hochgeladene VHD, um diese auf das Gerät herunterzuladen und ein VM-Image von der VHD zu erstellen. 
1. Verwenden Sie die Ressourcen, die Sie in den vorherigen Schritten erstellt haben:
    1. VM-Image, das Sie erstellt haben
    1. VSwitch, der der Netzwerkschnittstelle zugeordnet ist, an der Sie Compute aktiviert haben
    1. Das Subnetz, das dem VSwitch zugeordnet ist

    Und erstellen Sie die folgenden Ressourcen inline, oder geben Sie diese inline an:
    1. VM-Name – wählen Sie eine unterstützte VM-Größe und Anmeldeinformationen für die VM aus. 
    1. Erstellen Sie neue Datenträger, oder fügen Sie vorhandene Datenträger an.
    1. Konfigurieren Sie eine statische oder dynamische IP-Adresse für die VM. Wenn Sie eine statische IP-Adresse bereitstellen, wählen Sie eine freie IP-Adresse im Subnetzbereich der für Compute aktivierten Netzwerkschnittstelle aus.

    Verwenden Sie die oben aufgeführten Ressourcen, um einen virtuellen Computer zu erstellen.


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit dem Erstellen und Verwalten von VMs auf Ihrem Gerät über das Azure-Portal beginnen, stellen Sie Folgendes sicher:

1. Sie haben die Netzwerkeinstellungen auf dem Azure Stack Edge Pro-Gerät wie unter [Schritt 1: Konfigurieren des Azure Stack Edge Pro-Geräts](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device) angegeben festgelegt.

    1. Sie haben eine Netzwerkschnittstelle für Compute aktiviert. Diese IP-Adresse der Netzwerkschnittstelle wird verwendet, um einen virtuellen Switch für die VM-Bereitstellung zu erstellen. Navigieren Sie auf der lokalen Benutzeroberfläche Ihres Geräts zu **Compute**. Wählen Sie die Netzwerkschnittstelle aus, die Sie verwenden möchten, um einen virtuellen Switch zu erstellen.

        > [!IMPORTANT] 
        > Sie können nur einen Port für Compute konfigurieren.

    1. Aktivieren Sie Compute für die Netzwerkschnittstelle. Azure Stack Edge Pro erstellt und verwaltet einen virtuellen Switch, der dieser Netzwerkschnittstelle entspricht.

1. Sie haben Zugriff auf eine Windows- oder Linux-VHD, die Sie verwenden, um das VM-Image für den virtuellen Computer zu erstellen, den Sie erstellen möchten.

## <a name="deploy-a-vm"></a>Bereitstellen einer VM

Führen Sie die folgenden Schritte aus, um auf Ihrem Azure Stack Edge-Gerät einen virtuellen Computer zu erstellen.

### <a name="add-a-vm-image"></a>Hinzufügen eines VM-Images

1. Laden Sie eine VHD in ein Azure Storage-Konto hoch. Führen Sie die Schritte unter [Hochladen einer VHD-Datei in das Speicherkonto eines Labs mithilfe des Microsoft Azure Storage-Explorers](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md) aus.

1. Wechseln Sie im Azure-Portal zur Azure Stack Edge-Ressource für Ihr Azure Stack Edge-Gerät. Wechseln Sie zu **Edgecomputing -> Virtuelle Computer**.

    ![VM-Image hinzufügen 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Wählen Sie **Virtuelle Computer** aus, um zur Seite **Übersicht** zu wechseln. **Aktivieren** Sie die Cloudverwaltung für virtuelle Computer.
    ![VM-Image hinzufügen 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. Der erste Schritt besteht darin, ein VM-Image hinzuzufügen. Sie haben im vorherigen Schritt bereits eine VHD in das Speicherkonto hochgeladen. Sie verwenden diese VHD, um ein VM-Image zu erstellen.

    Wählen Sie **Image hinzufügen** aus, um die VHD aus dem Speicherkonto herunterzuladen und zum Gerät hinzuzufügen. Der Downloadvorgang dauert einige Minuten und ist von der Größe der VHD und der für den Download verfügbaren Internetbandbreite abhängig. 

    ![VM-Image hinzufügen 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. Geben Sie auf dem Blatt **Image hinzufügen** die folgenden Parameter ein. Wählen Sie **Hinzufügen**.


    |Parameter  |BESCHREIBUNG  |
    |---------|---------|
    |Download from storage blob (Aus Speicherblob herunterladen)    |Navigieren Sie zum Speicherort des Speicherblobs im Speicherkonto, in das Sie die VHD hochgeladen haben.         |
    |Herunterladen nach    | Wird automatisch auf das aktuelle Gerät festgelegt, auf dem Sie den virtuellen Computer bereitstellen.        |
    |Image speichern unter      | Der Name des VM-Images, das Sie von der VHD erstellen, die Sie in das Speicherkonto hochgeladen haben.        |
    |Betriebssystemtyp     |Wählen Sie Windows oder Linux als Betriebssystem der VHD aus, die Sie zum Erstellen des VM-Images verwenden.         |
   

    ![VM-Image hinzufügen 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. Die VHD wird heruntergeladen, und das VM-Image wird erstellt. Die Imageerstellung dauert einige Minuten. Sie werden benachrichtigt, sobald das VM-Image erfolgreich erstellt wurde.

    ![VM-Image hinzufügen 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. Nachdem das VM-Image erfolgreich erstellt wurde, wird es der Liste der Images auf dem Blatt **Images** hinzugefügt.
    ![VM-Image hinzufügen 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    Das Blatt **Bereitstellungen** wird aktualisiert, um den Status der Bereitstellung widerzuspiegeln.

    ![VM-Image hinzufügen 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    Das neu hinzugefügte Image wird auch auf der Seite **Übersicht** angezeigt.
    ![VM-Image hinzufügen 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Hinzufügen eines virtuellen Computers

Führen Sie diese Schritte aus, um eine VM zu erstellen, nachdem Sie ein VM-Image erstellt haben.

1. Wählen Sie auf der Seite **Übersicht** die Option **Virtuellen Computer hinzufügen** aus.

    ![VM hinzufügen 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. Geben Sie auf dem Blatt **Grundlagen** die folgenden Parameter ein.


    |Parameter |BESCHREIBUNG  |
    |---------|---------|
    |Name des virtuellen Computers     |         |
    |Image     | Wählen Sie aus den VM-Images aus, die auf dem Gerät verfügbar sind.        |
    |Size     | Wählen Sie aus den [Unterstützten VM-Größen](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#supported-vm-sizes) aus.        |
    |Username     | Verwenden Sie den Standardbenutzernamen *azureuser*.        |
    |Authentifizierungsart    | Wählen Sie einen öffentlichen SSH-Schlüssel oder ein benutzerdefiniertes Kennwort aus.       |
    |Kennwort     | Geben Sie ein Kennwort ein, um sich beim virtuellen Computer anzumelden. Das Kennwort muss mindestens zwölf Zeichen lang sein und die definierten [Komplexitätsanforderungen](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) erfüllen.        |
    |Kennwort bestätigen    | Geben Sie das Kennwort erneut ein.        |


    ![VM hinzufügen 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Klicken Sie auf **Weiter: Datenträger**.

1. Auf der Registerkarte **Datenträger** fügen Sie Datenträger an Ihre VM an. 
    
    1. Sie können **Neuen Datenträger erstellen und anfügen** oder **Vorhandenen Datenträger anfügen** auswählen.

        ![VM hinzufügen 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Wählen Sie **Neuen Datenträger erstellen und anfügen** aus. Geben Sie auf dem Blatt **Neuen Datenträger erstellen** einen Namen für den Datenträger und die Größe in GiB an.

        ![VM hinzufügen 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  Wiederholen Sie den obigen Vorgang, um weitere Datenträger hinzuzufügen. Nachdem die Datenträger erstellt wurden, werden sie auf der Registerkarte **Datenträger** angezeigt.

        ![VM hinzufügen 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        Klicken Sie auf **Weiter: Netzwerk**.

1. Auf der Registerkarte **Netzwerk** konfigurieren Sie die Netzwerkkonnektivität für die VM.

    
    |Parameter  |BESCHREIBUNG |
    |---------|---------|
    |Virtuelles Netzwerk    | Wählen Sie in der Dropdownliste den virtuellen Switch aus, der auf Ihrem Azure Stack Edge-Gerät erstellt wurde, als Sie Compute an der Netzwerkschnittstelle aktiviert haben.    |
    |Subnet     | Dieses Feld wird automatisch mit dem Subnetz ausgefüllt, das der Netzwerkschnittstelle zugeordnet ist, an der Sie Compute aktiviert haben.         |
    |IP-Adresse     | Geben Sie eine statische oder dynamische IP-Adresse für die VM an. Die statische IP-Adresse sollte eine verfügbare, freie IP-Adresse aus dem angegebenen Subnetzbereich sein.        |

    ![VM hinzufügen 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Klicken Sie auf **Weiter: Überprüfen + erstellen**.

1. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die Angaben für die VM, und wählen Sie **Erstellen** aus.

    ![VM hinzufügen 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. Die Erstellung der VM wird gestartet und kann bis zu 20 Minuten dauern. Sie können zu **Bereitstellungen** wechseln, um die VM-Erstellung zu überwachen.

    ![VM hinzufügen 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. Nachdem die VM erfolgreich erstellt wurde, wird die Seite **Übersicht** aktualisiert, damit die neue VM angezeigt wird.

    ![VM hinzufügen 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Wählen Sie die neu erstellte VM aus, um zu **Virtuelle Computer** zu wechseln.

    ![VM hinzufügen 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Wählen Sie die VM aus, um die Details anzuzeigen. 

    ![VM hinzufügen 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>Herstellen einer Verbindung mit einem virtuellen Computer

Je nachdem, ob Sie eine Windows- oder Linux-VM erstellt haben, können die Schritte abweichen. Sie können keine Verbindung mit den auf Ihrem Gerät bereitgestellten VMs über das Azure-Portal herstellen. Sie müssen die folgenden Schritte ausführen, um eine Verbindung mit Ihrer Linux- oder Windows-VM herzustellen.

### <a name="connect-to-linux-vm"></a>Herstellen einer Verbindung mit einer Linux-VM

Führen Sie diese Schritte aus, um eine Verbindung mit einer Linux-VM herzustellen.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Herstellen einer Verbindung mit einer Windows-VM

Führen Sie diese Schritte aus, um eine Verbindung mit einer Windows-VM herzustellen.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verwalten Ihres Azure Stack Edge Pro-Geräts finden Sie unter [Verwenden der lokalen Webbenutzeroberfläche zur Verwaltung eines Azure Stack Edge Pro-Geräts](azure-stack-edge-manage-access-power-connectivity-mode.md).
