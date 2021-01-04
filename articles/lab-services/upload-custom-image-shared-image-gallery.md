---
title: Azure Lab Services – Hochladen eines benutzerdefinierten Images in Shared Image Gallery
description: Beschreibt das Hochladen eines benutzerdefinierten Images in Shared Image Gallery. Die IT-Abteilungen von Universitäten werden das Importieren von Images besonders vorteilhaft finden.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 93b4141636b629168e9bb7a73e71a9fe4bfc39f5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654642"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Hochladen eines benutzerdefiniertes Image in Shared Image Gallery

Die Shared Image Gallery steht Ihnen zum Importieren Ihrer eigenen benutzerdefinierten Images zum Erstellen von Labs in Azure Lab Services zur Verfügung. Die IT-Abteilungen von Universitäten werden das Importieren von Images aus folgenden Gründen besonders vorteilhaft finden: 

* Sie müssen keine Images manuell mithilfe der Vorlagen-VM eines Labs erstellen.
* Sie können Images hochladen, die mit anderen Tools wie SCCM, Endpoint Manager usw. erstellt wurden.

In diesem Artikel werden die Schritte beschrieben, die Sie ausführen können, um ein benutzerdefiniertes Image einzubringen und in Azure Lab Services zu verwenden. 

> [!IMPORTANT]
> Wenn Sie Ihre Images von einer physischen Lab-Umgebung in Az Labs verschieben, müssen Sie sie entsprechend neu strukturieren. Verwenden Sie nicht einfach erneut Ihre vorhandenen Images aus physischen Labs. <br/>Weitere Informationen finden Sie im Blogbeitrag [Moving from a Physical Lab to Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) (Der Weg von einem physischen Lab zu Azure Lab Services).

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Einbringen eines benutzerdefinierten Images aus einer physischen Laborumgebung

Die folgenden Schritte zeigen, wie Sie ein benutzerdefiniertes Image importieren, das aus einer physischen Laborumgebung gestartet wird. Anschließend wird eine virtuelle Festplatte aus dieser Umgebung erstellt und in die Shared Image Gallery in Azure importiert, damit sie in Azure Lab Services verwendet werden kann.

Viele Optionen sind für das Erstellen einer virtuellen Festplatte aus einer physischen Laborumgebung verfügbar. In den folgenden Schritten wird gezeigt, wie Sie eine virtuelle Festplatte aus einer Windows Hyper-V-VM erstellen:

1. Beginnen Sie mit einer Hyper-V-VM in ihrer physischen Laborumgebung, die aus dem Image erstellt wurde.
    1. Die VM muss als VM der Generation 1 erstellt werden.
    1. Die VM muss eine Festplatte mit fester Größe verwenden. Sie können auch die Größe des Datenträgers in diesem Fenster angeben. Die Datenträgergröße darf nicht über 128 GB liegen.<br/>    
    Images mit einer Datenträgergröße über 128 GB werden von Azure Lab Services nicht unterstützt. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Virtuelle Festplatte verbinden":::   
    1. Stellen Sie wie gewohnt ein Image der VM her.
1. [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md)
    1. [Festlegen der Windows-Konfigurationen für Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)
    1. [Überprüfen der Windows-Dienste](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
    1. [Aktualisieren der Remotedesktop-Registrierungseinstellungen](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
    1. [Konfigurieren von Windows-Firewallregeln](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)
    1. Installieren von Windows-Updates
    1. [Abschließen empfohlener Konfigurationen](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
    
    In den obigen Schritten wird ein spezialisiertes Image erstellt. Wenn Sie ein generalisiertes Image erstellen, müssen Sie auch [Sysprep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep) ausführen. <br/>
        Sie sollten ein spezialisiertes Image erstellen, wenn Sie das (möglicherweise Dateien, Benutzerkontoinformationen usw. enthaltende) Benutzerverzeichnis verwalten möchten, das für die im Image enthaltene Software erforderlich ist.
1. Da **Hyper-V** standardmäßig eine **VHDX**-Datei erstellt, müssen Sie diese in eine VHD-Datei konvertieren.
    1. Navigieren Sie zu **Hyper-V-Manager** -> **Aktion** -> **Datenträger bearbeiten**.
    1. Hier haben Sie die Möglichkeit, den Datenträger von einer VHDX-Datei in eine VHD-Datei zu **konvertieren**.
    1. Wenn Sie versuchen, die Datenträgergröße zu erweitern, achten Sie darauf, dass Sie 128 GB nicht überschreiten.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Aktion auswählen":::   
1. Laden Sie die VHD-Datei in Azure hoch, um einen verwalteten Datenträger zu erstellen.
    1. Sie können entweder Storage-Explorer oder AzCopy von der Befehlszeile aus verwenden, wie in [Hochladen einer VHD in Azure oder Kopieren eines verwalteten Datenträgers in eine andere Region](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md) beschrieben.        
    Wenn Ihr Computer in den Standbymodus wechselt oder gesperrt wird, wird der Uploadvorgang möglicherweise unterbrochen und ein Fehler tritt auf.
    1. Das Ergebnis dieses Schritts ist, dass Sie nun über einen verwalteten Datenträger verfügen, der im Azure-Portal angezeigt wird. 
        Sie können auf der Registerkarte „Größe\Leistung“ des Azure-Portals die Größe des Datenträgers auswählen. Wie bereits erwähnt, darf die Größe 128 GB nicht überschreiten.
1. Erstellen Sie eine Momentaufnahme des verwalteten Datenträgers.
    Dies kann entweder über PowerShell, mithilfe des Azure-Portals oder vom Storage-Explorer aus erfolgen, wie unter [Erstellen einer Momentaufnahme mithilfe des Portals oder PowerShell](../virtual-machines/windows/snapshot-copy-managed-disk.md) beschrieben.
1. Erstellen Sie in Shared Image Gallery eine Imagedefinition:
    1. [Erstellen Sie eine Imagedefinition](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition).
    1. Sie müssen hier auch angeben, ob Sie ein spezialisiertes/generalisiertes Image erstellen.
1. Erstellen Sie das Lab in Azure Lab Services, und wählen Sie das benutzerdefinierte Image in der Shared Image Gallery aus.

    Wenn Sie den Datenträger nach der Installation des Betriebssystems auf der ursprünglichen Hyper-V-VM erweitert haben, müssen Sie auch das Laufwerk C in Windows so erweitern, dass der nicht zugeordnete Datenträger-Speicherplatz verwendet wird. Melden Sie sich zu diesem Zweck bei der Vorlagen-VM an, nachdem das Lab erstellt wurde, und befolgen Sie die Schritte, die dem ähneln, was in [Erweitern eines Basisvolumes](/windows-server/storage/disk-management/extend-a-basic-volume) dargestellt wird. Es gibt Optionen, dies sowohl über die Benutzeroberfläche als auch mit PowerShell durchzuführen.

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über den Katalog mit freigegebenen Images](../virtual-machines/windows/shared-image-galleries.md)
* [Verwenden eines Katalogs mit freigegebenen Images in Azure Lab Services](how-to-use-shared-image-gallery.md)