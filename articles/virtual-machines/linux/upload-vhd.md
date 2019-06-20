---
title: Hochladen oder Kopieren einer benutzerdefinierten Linux-VM mithilfe der Azure CLI | Microsoft-Dokumentation
description: Laden Sie mit dem Resource Manager-Bereitstellungsmodell und der Azure-Befehlszeilenschnittstelle einen benutzerdefinierten virtuellen Computer hoch, oder kopieren Sie ihn.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: 6483fa8737ee3de6a60c4e4646fefec30ae702b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61473412"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Erstellen eines virtuellen Linux-Computers auf der Grundlage eines benutzerdefinierten Datenträgers mithilfe der Azure-Befehlszeilenschnittstelle

<!-- rename to create-vm-specialized -->

In diesem Artikel erfahren Sie, wie Sie eine benutzerdefinierte virtuelle Festplatte (virtual hard disk, VHD) hochladen und eine vorhandene virtuelle Festplatte in Azure kopieren. Die neu erstellte VHD wird dann verwendet, um neue virtuelle-Linux-Computer (virtual machines, VMs) zu erstellen. Sie können eine Linux-Distribution installieren und konfigurieren und die VHD dann zur Erstellung eines neuen virtuellen Azure-Computers verwenden.

Wenn Sie auf der Grundlage Ihres benutzerdefinierten Datenträgers mehrere virtuelle Computer erstellen möchten, erstellen Sie zunächst ein Image ihres virtuellen Computers oder Ihrer VHD. Weitere Informationen finden Sie in [Erstellen eines benutzerdefinierten Images eines virtuellen Azure-Computers mithilfe der Befehlszeilenschnittstelle](tutorial-custom-images.md).

Ein benutzerdefinierter Datenträger kann auf zwei Arten erstellt werden:
* Hochladen einer VHD-Datei
* Kopieren einer vorhandenen Azure-VM

## <a name="quick-commands"></a>Schnellbefehle

Wenn Sie per [az vm create](/cli/azure/vm#az-vm-create) einen neuen virtuellen Computer auf der Grundlage eines benutzerdefinierten oder spezialisierten Datenträgers erstellen, müssen Sie den Datenträger mit „--attach-os-disk“ **anfügen**, anstatt mit „--image“ ein benutzerdefiniertes oder Marketplace-basiertes Image anzugeben. Das folgende Beispiel erstellt einen virtuellen Computer namens *myVM* mithilfe des verwalteten Datenträgers namens *myManagedDisk*, der aus Ihrer benutzerdefinierten VHD erstellt wurde:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Requirements (Anforderungen)
Für die folgenden Schritte benötigen Sie Folgendes:

* Einen virtuellen Linux-Computer, der für die Verwendung in Azure vorbereitet wurde. Im Abschnitt [Vorbereiten des virtuellen Computers](#prepare-the-vm) dieses Artikels erfahren Sie, wo Sie distributionsspezifische Informationen zur Installation des Azure Linux-Agents (waagent) finden. Dieser ist erforderlich, um eine SSH-Verbindung mit einem virtuellen Computer herstellen zu können.
* Die VHD-Datei einer vorhandenen [ von Azure unterstützten Linux-Distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (oder [Informationen zu nicht unterstützten Distributionen](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) an einen virtuellen Datenträger im VHD-Format. Für die Erstellung virtueller Computer und Festplatten stehen verschiedene Tools zur Verfügung:
  * Installieren und konfigurieren Sie [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) oder [KVM](https://www.linux-kvm.org/page/RunningKVM), und verwenden Sie dabei „VHD“ als Imageformat. Bei Bedarf können Sie mit `qemu-img convert` [ein Image konvertieren](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats).
  * Unter [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) und [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx) können Sie auch Hyper-V verwenden.

> [!NOTE]
> Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Wenn Sie einen virtuellen Computer erstellen, geben Sie als Format VHD an. VHDX-Datenträger können bei Bedarf mit [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) oder mit dem PowerShell-Cmdlet [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) in eine VHD konvertiert werden. Das Hochladen dynamischer VHDs wird von Azure nicht unterstützt. Daher müssen solche Datenträger vor dem Hochladen in statische VHDs konvertiert werden. Mit Tools wie den [Azure VHD-Hilfsprogrammen für Go](https://github.com/Microsoft/azure-vhd-utils-for-go) können Sie dynamische Datenträger beim Hochladen in Azure konvertieren.
> 
> 


* Vergewissern Sie sich, dass Sie die neueste Version der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-az-cli2) installiert und sich mithilfe von [az login](/cli/azure/reference-index#az-login) bei einem Azure-Konto angemeldet haben.

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen wie *myResourceGroup*, *mystorageaccount* und *mydisks* durch Ihre eigenen Werte.

<a id="prepimage"></a>

## <a name="prepare-the-vm"></a>Vorbereiten des virtuellen Computers

Azure unterstützt eine Vielzahl von Linux-Distributionen (siehe [Unterstützte Distributionen](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). In den folgenden Artikeln wird die Vorbereitung der verschiedenen Linux-Distributionen beschrieben, die in Azure unterstützt werden:

* [CentOS-basierte Verteilungen](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES &amp; openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Sonstige: nicht unterstützte Distributionen](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Beachten Sie auch die [Installationshinweise für Linux](create-upload-generic.md#general-linux-installation-notes). Diese enthalten allgemeine Tipps zur Vorbereitung von Linux-Images für Azure.

> [!NOTE]
> Die [Azure Platform-SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gilt nur dann für virtuelle Computer unter Linux, wenn eine der unterstützten Distributionen mit Konfigurationsdetails verwendet wird, die im Abschnitt mit den unterstützten Versionen unter [Linux auf von Azure unterstützten Verteilungen](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) angegeben sind.
> 
> 

## <a name="option-1-upload-a-vhd"></a>Option 1: Hochladen einer VHD-Datei

Sie können eine benutzerdefinierte VHD hochladen, die bei Ihnen auf einem lokalen Computer ausgeführt wird oder die Sie aus einer anderen Cloud exportiert haben. Um eine VHD für die Erstellung eines neuen virtuellen Azure-Computers verwenden zu können, müssen Sie die VHD in ein Speicherkonto hochladen und aus ihr einen verwalteten Datenträger erstellen. Weitere Informationen finden Sie in der [Übersicht über Azure Managed Disks](../windows/managed-disks-overview.md).

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Vor dem Hochladen Ihres benutzerdefinierten Datenträgers und dem Erstellen virtueller Computer müssen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe erstellen.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mithilfe von [az storage account create](/cli/azure/storage/account) ein Speicherkonto für Ihren benutzerdefinierten Datenträger und die virtuellen Computer. Das folgende Beispiel erstellt ein Speicherkonto namens *mystorageaccount* in der zuvor erstellten Ressourcengruppe:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Auflisten von Speicherkontoschlüsseln
Azure generiert zwei 512-Bit-Zugriffsschlüssel für jedes Speicherkonto. Die Zugriffsschlüssel werden für die Authentifizierung beim Speicherkonto verwendet (etwa beim Ausführen von Schreibvorgängen). Weitere Informationen finden Sie unter [Zugriffsschlüssel](../../storage/common/storage-account-manage.md#access-keys). 

Zeigen Sie mit [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) die Zugriffsschlüssel an. Verwenden Sie beispielsweise Folgendes, um die Zugriffsschlüssel für das erstellte Speicherkonto anzuzeigen:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

Die Ausgabe sieht in etwa wie folgt aus:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Notieren Sie sich **key1**, da Sie ihn in den nächsten Schritten für die Interaktion mit Ihrem Speicherkonto benötigen.

### <a name="create-a-storage-container"></a>Erstellen eines Speichercontainers
Auf die gleiche Weise, in der Sie verschiedene Verzeichnisse erstellen, um Ihr lokales Dateisystem logisch zu organisieren, erstellen Sie Container in einem Speicherkonto, um die Datenträger zu organisieren. Ein Speicherkonto kann zahlreiche Container enthalten. Erstellen Sie mit [az storage container create](/cli/azure/storage/container#az-storage-container-create) einen Container.

Im folgenden Beispiel wird ein Container namens *mydisks* erstellt:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Hochladen der VHD
Laden Sie Ihren benutzerdefinierten Datenträger mit [az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload) hoch. Ihr benutzerdefinierter Datenträger wird als Seitenblob hochgeladen und gespeichert.

Geben Sie Ihren Zugriffsschlüssel, den Container, den Sie im vorherigen Schritt erstellt haben, und dann den Pfad zum benutzerdefinierten Datenträger auf dem lokalen Computer an:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Das Hochladen der VHD kann eine Weile dauern.

### <a name="create-a-managed-disk"></a>Erstellen eines verwalteten Datenträgers


Erstellen Sie einen verwalteten Datenträger mit [az disk create](/cli/azure/disk#az-disk-create). Das folgende Beispiel erstellt den verwalteten Datenträger namens *myManagedDisk* aus der VHD, die Sie in Ihr benanntes Speicherkonto und den Container hochgeladen haben:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Option 2: Kopieren einer vorhandenen VM

Um eine weitere Kopie zu erstellen, können Sie auch einen benutzerdefinierten virtuellen Computer in Azure erstellen und dann den Betriebssystemdatenträger kopieren und an einen neuen virtuellen Computer anfügen. Dies ist für Testzwecke ausreichend. Wenn Sie allerdings einen vorhandenen virtuellen Azure-Computer als Modell für mehrere neue virtuelle Computer verwenden möchten, sollten Sie stattdessen ein *Image* erstellen. Weitere Informationen zum Erstellen eines Images eines vorhandenen virtuellen Azure-Computers finden Sie unter [Erstellen eines benutzerdefinierten Images eines virtuellen Azure-Computers mithilfe der Befehlszeilenschnittstelle](tutorial-custom-images.md).

### <a name="create-a-snapshot"></a>Erstellen einer Momentaufnahme

Mit diesem Beispiel wird eine Momentaufnahme von einer VM namens *myVM* in der Ressourcengruppe *myResourceGroup* erstellt. Der Name der erstellten Momentaufnahme lautet *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Erstellen des verwalteten Datenträgers

Erstellen eines neuen verwalteten Datenträgers aus der Momentaufnahme.

Ermitteln Sie die ID der Momentaufnahme. In unserem Beispiel heißt die Momentaufnahme *osDiskSnapshot* und befindet sich in der Ressourcengruppe *myResourceGroup*.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Erstellen Sie den verwalteten Datenträger. In diesem Beispiel erstellen wir einen verwalteten Datenträger namens *myManagedDisk* auf der Grundlage unserer Momentaufnahme. Der Datenträger befindet sich im Standardspeicher und hat eine Größe von 128 GB.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Erstellen Sie Ihren virtuellen Computer mit [az vm create](/cli/azure/vm#az-vm-create), und fügen Sie den verwalteten Datenträger als Betriebssystemdatenträger an (--attach-os-disk). Das folgende Beispiel erstellt den virtuellen Computer *myNewVM* unter Verwendung des verwalteten Datenträgers, der auf der Grundlage der hochgeladenen VHD erstellt wurde:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Mit den Anmeldeinformationen des virtuellen Quellcomputers sollten Sie eine SSH-Verbindung mit dem virtuellen Computer herstellen können. 

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie den benutzerdefinierten virtuellen Datenträger vorbereitet und hochgeladen haben, können Sie sich mit der [Verwendung von Resource Manager und Vorlagen](../../azure-resource-manager/resource-group-overview.md)beschäftigen. Informationen zum Hinzufügen eines Datenträgers zu Ihren neuen virtuellen Computern finden Sie [hier](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . Falls auf Ihren virtuellen Computern Anwendungen ausgeführt werden, auf die Sie zugreifen müssen, müssen Sie [Ports und Endpunkte öffnen](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
