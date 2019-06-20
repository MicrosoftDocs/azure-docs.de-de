---
title: Herunterladen einer Linux-VHD von Azure | Microsoft-Dokumentation
description: Laden Sie eine Linux-VHD mithilfe der Azure-Befehlszeilenschnittstelle und dem Azure-Portal herunter.
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
ms.openlocfilehash: f72d49a3ab204ce64eb89d0f05630b640c138e0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61390227"
---
# <a name="download-a-linux-vhd-from-azure"></a>Herunterladen einer Linux-VHD von Azure

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle und des Azure-Portals eine Linux-VHD-Datei (Virtual Hard Disk, virtuelle Festplatte) von Azure herunterladen. 

Installieren Sie [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2), falls Sie dies noch nicht getan haben.

## <a name="stop-the-vm"></a>Beenden des virtuellen Computers

Eine VHD kann nicht von Azure heruntergeladen werden, wenn sie an eine ausgeführte VM angefügt ist. Sie müssen die VM beenden, um eine VHD herunterzuladen. Wenn Sie eine VHD als [Image](tutorial-custom-images.md) zum Erstellen anderer VMs mit neuen Datenträgern verwenden möchten, müssen Sie das in der Datei enthaltene Betriebssystem aufheben und generalisieren und dann die VM anhalten. Um die VHD als Datenträger für eine neue Instanz einer vorhandenen VM oder eines vorhandenen Datenträgers zu verwenden, müssen Sie nur die VM beenden und freigeben.

Um die VHD als Image zum Erstellen von anderen VMs zu verwenden, führen Sie die folgenden Schritte durch:

1. Verwenden Sie SSH, den Kontonamen und die öffentliche IP-Adresse der VM, um eine Verbindung zu ihr aufzubauen und die Bereitstellung aufzuheben. Die öffentliche IP-Adresse finden Sie mit [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Der Parameter „+Benutzer“ entfernt auch das zuletzt bereitgestellte Benutzerkonto. Wenn Sie Kontoanmeldeinformationen in der VM sichern, lassen die den +Benutzer-Parameter weg. Im folgenden Beispiel wird das letzte bereitgestellte Benutzerkonto entfernt:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Melden Sie sich mit [az login](https://docs.microsoft.com/cli/azure/reference-index) bei Ihrem Azure-Konto an.
3. Beenden und Aufheben der Zuordnung für die VM

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalisieren der VM 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Um die VHD-Datei als Datenträger für eine neue Instanz eines vorhandenen virtuellen Computers oder Datenträgers für Daten zu verwenden, führen Sie folgende Schritte aus:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2.  Klicken Sie im Menü „Hub“ auf **Virtuelle Computer**.
3.  Wählen Sie die VM aus der Liste aus.
4.  Klicken Sie auf dem Blatt für die VM auf **Beenden**.

    ![Beenden der VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generieren der SAS-URL

Um die VHD-Datei herunterzuladen, müssen Sie eine [SAS-URL (Shared Access Signature)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) generieren. Wenn die URL generiert wird, wird der URL eine Ablaufzeit zugewiesen.

1.  Klicken Sie auf im Menü des Blatts für die VM auf **Datenträger**.
2.  Wählen Sie den Betriebssystem-Datenträger für die VM aus, und klicken Sie dann auf **Exportieren**.
3.  Klicken Sie auf **URL generieren**.

    ![Generieren der URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Herunterladen der VHD

1.  Klicken Sie unter der URL, die generiert wurde, auf „VHD-Datei herunterladen“.

    ![Herunterladen der VHD](./media/download-vhd/export-download.png)

2.  Möglicherweise müssen Sie im Browser auf **Speichern** klicken, um den Download zu starten. Der Standardname für die VHD-Datei lautet *abcd*.

    ![Klicken Sie im Browser auf „Speichern“](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum [Hochladen und Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe von Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Verwalten von Azure-Datenträgern mit der Azure-CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

