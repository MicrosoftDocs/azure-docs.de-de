---
title: Herunterladen einer Windows-VHD | Microsoft-Dokumentation
description: Laden Sie eine Windows-VHD mithilfe des Azure-Portals herunter.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3d44a4a723c39bf9780475a2ac3088da94285f6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61076326"
---
# <a name="download-a-windows-vhd-from-azure"></a>Herunterladen einer Windows-VHD von Azure

In diesem Artikel erfahren Sie, wie Sie mithilfe des Azure-Portals eine Windows-VHD-Datei (Virtual Hard Disk, virtuelle Festplatte) von Azure herunterladen.

## <a name="stop-the-vm"></a>Beenden des virtuellen Computers

Eine VHD kann nicht von Azure heruntergeladen werden, wenn sie an eine ausgeführte VM angefügt ist. Sie müssen die VM beenden, um eine VHD herunterzuladen. Wenn Sie eine VHD als [Image](tutorial-custom-images.md) zum Erstellen anderer VMs mit neuen Datenträgern verwenden möchten, verwenden Sie [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), um das in der Datei enthaltene Betriebssystem zu generalisieren und dann die VM anzuhalten. Um die VHD als Datenträger für eine neue Instanz einer vorhandenen VM oder eines vorhandenen Datenträgers zu verwenden, müssen Sie nur die VM beenden und freigeben.

Um die VHD als Image zum Erstellen von anderen VMs zu verwenden, führen Sie die folgenden Schritte durch:

1.  Melden Sie sich am [Azure-Portal](https://portal.azure.com/)an, falls Sie dies noch nicht getan haben.
2.  [Stellen Sie eine Verbindung mit der VM her](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Öffnen Sie auf der VM das Eingabeaufforderungsfenster als Administrator.
4.  Wechseln Sie zum Verzeichnis *%windir%\system32\sysprep*, und führen Sie „sysprep.exe“ aus.
5.  Wählen Sie im Dialogfeld „Systemvorbereitungsprogramm“ die Option **Out-of-Box-Experience (OOBE) für System aktivieren** aus, und vergewissern Sie sich, dass **Verallgemeinern** ausgewählt ist.
6.  Wählen Sie unter „Optionen für Herunterfahren“ die Option **Herunterfahren** aus, und klicken Sie dann auf **OK**. 

Um die VHD als Datenträger eine neue Instanz einer vorhandenen VM oder eines vorhandenen Datenträgers zu verwenden, führen Sie die folgenden Schritte durch:

1.  Klicken Sie im Hub-Menü im Azure-Portal auf **Virtuelle Computer**.
2.  Wählen Sie die VM aus der Liste aus.
3.  Klicken Sie auf dem Blatt für die VM auf **Beenden**.

    ![Beenden der VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generieren der SAS-URL

Um die VHD-Datei herunterzuladen, müssen Sie eine [SAS-URL (Shared Access Signature)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) generieren. Wenn die URL generiert wird, wird der URL eine Ablaufzeit zugewiesen.

1.  Klicken Sie auf im Menü des Blatts für die VM auf **Datenträger**.
2.  Wählen Sie den Betriebssystem-Datenträger für die VM aus, und klicken Sie dann auf **Exportieren**.
3.  Legen Sie die Ablaufzeit der URL auf *36000* fest.
4.  Klicken Sie auf **URL generieren**.

    ![Generieren der URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> Die Ablaufzeit wird von der Standardzeit erhöht, um genug Zeit für das Herunterladen der großen VHD-Datei für ein Windows Server-Betriebssystem bereitzustellen. Sie können davon ausgehen, dass das Herunterladen einer VHD-Datei mit dem Windows Server-Betriebssystem mehrere Stunden dauert, je nach Ihrer Verbindung. Wenn Sie eine VHD für einen Datenträger herunterladen, reicht die Standardzeit. 
> 
> 

## <a name="download-vhd"></a>Herunterladen der VHD

1.  Klicken Sie unter der URL, die generiert wurde, auf „VHD-Datei herunterladen“.

    ![Herunterladen der VHD](./media/download-vhd/export-download.png)

2.  Möglicherweise müssen Sie im Browser auf **Speichern** klicken, um den Download zu starten. Der Standardname für die VHD-Datei lautet *abcd*.

    ![Klicken Sie im Browser auf „Speichern“](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie eine [VHD-Datei in Azure hochladen](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Erstellen verwalteter Datenträger auf der Grundlage nicht verwalteter Datenträger in einem Speicherkonto](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- [Verwalten von Azure-Datenträgern mit PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

