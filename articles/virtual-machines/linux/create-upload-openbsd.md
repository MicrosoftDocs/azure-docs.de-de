---
title: Erstellen und Hochladen eines OpenBSD-VM-Images in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD), die das OpenBSD-Betriebssystem enthält, erstellen und hochladen, um einen virtuellen Azure-Computer über Azure CLI zu erstellen.
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: 2e580a94e568f201587c06efa827006386cd6bd9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60327679"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Erstellen und Hochladen eines OpenBSD-Datenträgerimages in Azure
In diesem Artikel erfahren Sie, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD) mit dem OpenBSD-Betriebssystem erstellen und hochladen. Nach dem Hochladen können Sie sie als eigenes Image verwenden, um über Azure CLI einen virtuellen Computer (Virtual Machine, VM) in Azure zu erstellen.


## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Elemente verfügen:

* **Azure-Abonnement**: Falls Sie noch nicht über ein Konto verfügen, können Sie in wenigen Minuten eines erstellen. MSDN-Abonnenten finden weitere Informationen unter [Monatliche Azure-Gutschrift für Visual Studio-Abonnenten](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Alle anderen Benutzer können sich unter [Erstellen eines kostenlosen Testkontos](https://azure.microsoft.com/pricing/free-trial/)informieren.  
* **Azure CLI**: Überprüfen Sie, ob Sie die neueste Version der [Azure CLI](/cli/azure/install-azure-cli) installiert haben und mit [az login](/cli/azure/reference-index) in Ihrem Azure-Konto angemeldet sind.
* **In einer VHD-Datei installiertes OpenBSD-Betriebssystem:** Auf einer virtuellen Festplatte muss ein unterstütztes OpenBSD-Betriebssystem ([amd64, Version 6.2](https://ftp.openbsd.org/pub/OpenBSD/6.2/amd64/)) installiert sein. Zum Erstellen von VHD-Dateien stehen mehrere verschiedene Tools bereit. Sie können beispielsweise eine Virtualisierungslösung wie Hyper-V verwenden, um die VHD-Datei zu erstellen und das Betriebssystem zu installieren. Eine Anleitung zum Installieren und Verwenden von Hyper-V finden Sie unter [Installieren von Hyper-V und Erstellen eines virtuellen Computers](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Vorbereiten des OpenBSD-Images für Azure
Führen Sie auf dem virtuellen Computer, auf dem Sie das Betriebssystem OpenBSD 6.1 mit zusätzlicher Hyper-V-Unterstützung installiert haben, die folgenden Verfahren durch:

1. Wenn DHCP während der Installation nicht aktiviert ist, aktivieren Sie den Dienst wie folgt:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Richten Sie wie folgt eine serielle Konsole ein:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Konfigurieren Sie die Paketinstallation wie folgt:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Standardmäßig ist der Benutzer `root` auf virtuellen Computern in Azure deaktiviert. Benutzer können auf dem virtuellen OpenBSD-Computer mit dem Befehl `doas` Befehle mit erweiterten Rechten ausführen. „Doas“ ist standardmäßig aktiviert. Weitere Informationen finden Sie unter [doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Installieren und konfigurieren Sie die Voraussetzungen für den Azure-Agent wie folgt:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Die neueste Version des Azure-Agents finden Sie stets auf [GitHub](https://github.com/Azure/WALinuxAgent/releases). Installieren Sie den Agent wie folgt:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Vergewissern Sie sich nach der Installation des Azure-Agents wie folgt, dass er ausgeführt wird:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Heben Sie die Bereitstellung des Systems auf, um es zu bereinigen und für eine erneute Bereitstellung vorzubereiten. Mit dem folgenden Befehl werden auch das zuletzt bereitgestellte Benutzerkonto und die dazugehörigen Daten gelöscht:

    ```sh
    waagent -deprovision+user -force
    ```

Nun können Sie Ihren virtuellen Computer herunterfahren.


## <a name="prepare-the-vhd"></a>Vorbereiten der VHD
Das VHDX-Format wird in Azure noch nicht unterstützt, dafür jedoch **virtuelle Festplatten mit fester Größe**. Sie können den Datenträger mit Hyper-V-Manager oder dem Powershell-Cmdlet [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) in das feste VHD-Format konvertieren. Im Folgenden finden Sie ein Beispiel.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Erstellen von Speicherressourcen und Hochladen
Erstellen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli
az group create --name myResourceGroup --location eastus
```

Um die VHD hochzuladen, erstellen Sie ein Speicherkonto mithilfe von [az storage account create](/cli/azure/storage/account). Da die Namen von Speicherkonten eindeutig sein müssen, geben Sie einen eigenen Namen an. Das folgende Beispiel erstellt das Speicherkonto *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Rufen Sie zum Steuern des Zugriffs auf das Speicherkonto den Speicherschlüssel wie folgt mit [az storage account keys list](/cli/azure/storage/account/keys) ab:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Um die hochgeladenen VHDs logisch zu trennen, erstellen Sie mit [az storage container create](/cli/azure/storage/container) einen Container innerhalb des Speicherkontos:

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Laden Sie schließlich die VHD mit [az storage blob upload](/cli/azure/storage/blob) wie folgt hoch:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Erstellen eines virtuellen Computers von der virtuellen Festplatte
Sie können einen virtuellen Computer mit einem [Beispielskript](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) oder direkt mit [az vm create](/cli/azure/vm) erstellen. Um die hochgeladene OpenBSD-VHD anzugeben, verwenden Sie den `--image`-Parameter wie folgt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Rufen Sie die IP-Adresse für den virtuellen OpenBSD-Computer wie folgt mit [az vm list-ip-addresses](/cli/azure/vm) ab:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Jetzt können Sie für den virtuellen OpenBSD-Computer ganz regulär SSH verwenden:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Hyper-V-Unterstützung unter OpenBSD 6.1 finden Sie unter [OpenBSD 6.1](https://www.openbsd.org/61.html) und [hyperv.4](https://man.openbsd.org/hyperv.4).

Informationen zum Erstellen eines virtuellen Computers aus verwalteten Datenträgern finden Sie unter [az disk](/cli/azure/disk). 
