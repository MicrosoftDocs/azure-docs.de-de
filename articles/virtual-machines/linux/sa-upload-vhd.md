---
title: Hochladen eines benutzerdefinierten Linux-Datenträgers mithilfe der Azure CLI | Microsoft-Dokumentation
description: Erstellen Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD) mithilfe des Resource Manager-Bereitstellungsmodells und der Azure CLI, und laden Sie sie in Azure hoch.
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
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 368fec658dd1f063c45f3d00d42a4549ca9dfd83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771157"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Hochladen und Erstellen eines virtuellen Linux-Computers auf der Grundlage eines benutzerdefinierten Datenträgers mithilfe der Azure CLI

In diesem Artikel erfahren Sie, wie Sie eine virtuelle Festplatte (Virtual Hard Disk, VHD) mit der Azure CLI in ein Azure-Speicherkonto hochladen und virtuelle Linux-Computer auf der Grundlage dieses benutzerdefinierten Datenträgers erstellen. Dadurch können Sie eine Linux-Distribution installieren und konfigurieren und die VHD dann zur schnellen Erstellung virtueller Azure-Computer (Azure-VMs) verwenden.

Dieses Thema verwendet die Speicherkonten für die endgültigen VHDs, Sie können diese Schritten jedoch auch mit [verwalteten Datenträgern](upload-vhd.md) ausführen. 

## <a name="quick-commands"></a>Schnellbefehle
Im folgenden Abschnitt werden die grundlegenden Befehle zum Hochladen einer VHD in Azure beschrieben, falls Sie die Aufgabe schnell durchführen müssen. Ausführlichere Informationen und Kontext für die einzelnen Schritte finden Sie im übrigen Dokument ([ab hier](#requirements)).

Überprüfen Sie, ob Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2) installiert haben und mit [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sind.

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Zu den Beispielparameternamen zählen `myResourceGroup`, `mystorageaccount` und `mydisks`.

Erstellen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Erstellen Sie mit [az storage account create](/cli/azure/storage/account) ein Speicherkonto für Ihre virtuellen Festplatten. Das folgende Beispiel erstellt ein Speicherkonto namens `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Listen Sie mit [az storage account keys list](/cli/azure/storage/account/keys) die Zugriffsschlüssel für Ihr Speicherkonto auf. Notieren Sie sich `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Erstellen Sie innerhalb des Speicherkontos unter Verwendung des Speicherschlüssels, den Sie mit [az storage container create](/cli/azure/storage/container) abgerufen haben, einen Container. Das folgende Beispiel erstellt unter Verwendung des Speicherschlüssels `key1` einen Container namens `mydisks`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Laden Sie schließlich Ihre VHD in den Container hoch, den Sie mit [az storage blob upload](/cli/azure/storage/blob) erstellt haben. Geben Sie den lokalen Pfad zur virtuellen Festplatte unter `/path/to/disk/mydisk.vhd` an:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Geben Sie den URI das Datenträgers (`--image`) mit [az vm create](/cli/azure/vm) an. Das folgende Beispiel erstellt einen virtuellen Computer namens `myVM` und verwendet dabei den zuvor hochgeladenen Datenträger:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Das Zielspeicherkonto muss mit dem Konto identisch sein, in das Sie den virtuellen Datenträger hochgeladen haben. Sie müssen außerdem alle erforderlichen Zusatzparameter für den **az vm create**-Befehl angeben bzw. entsprechende Eingabeaufforderungen beantworten. Dazu gehören: virtuelles Netzwerk, öffentliche IP-Adresse, Benutzername und SSH-Schlüssel. Erfahren Sie mehr zu den [verfügbaren Resource Manager-Parametern für die Befehlszeilenschnittstelle](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requirements (Anforderungen)
Um die folgenden Schritte ausführen zu können, benötigen Sie Folgendes:

* **In einer VHD-Datei installiertes Linux-Betriebssystem:** Installieren Sie auf einer virtuellen Festplatte im VHD-Format eine [von Azure unterstützte Linux-Distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). (Informationen zu nicht unterstützten Distributionen finden Sie [hier](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).) Für die Erstellung virtueller Computer und Festplatten stehen verschiedene Tools zur Verfügung:
  * Installieren und konfigurieren Sie [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) oder [KVM](https://www.linux-kvm.org/page/RunningKVM), und verwenden Sie dabei „VHD“ als Imageformat. Bei Bedarf können Sie ein Image mithilfe von `qemu-img convert` [konvertieren](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats).
  * Unter [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) und [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx) können Sie auch Hyper-V verwenden.

> [!NOTE]
> Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Wenn Sie einen virtuellen Computer erstellen, geben Sie als Format VHD an. VHDX-Datenträger können bei Bedarf mit [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) oder mit dem PowerShell-Cmdlet [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) in VHD konvertiert werden. Azure unterstützt außerdem das Hochladen von dynamischen VHDs nicht, daher müssen Sie solche Datenträger vor dem Hochladen in statische VHDs konvertieren. Mit Tools wie [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) (Azure-VHD-Hilfsprogramme für Go) können Sie dynamische Datenträger im Zuge des Uploads zu Azure konvertieren.
> 
> 

* Virtuelle Computer, die aus dem benutzerdefinierten Datenträger erstellt werden, müssen sich im gleichen Speicherkonto wie der eigentliche Datenträger befinden.
  * Erstellen eines Speicherkontos und eines Containers für den benutzerdefinierten Datenträger und die erstellten virtuellen Computer
  * Nachdem Sie alle virtuellen Computer erstellt haben, können Sie den Datenträger problemlos löschen.

Überprüfen Sie, ob Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2) installiert haben und mit [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sind.

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Zu den Beispielparameternamen zählen `myResourceGroup`, `mystorageaccount` und `mydisks`.

<a id="prepimage"></a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Vorbereiten des hochzuladenden Datenträgers
Azure unterstützt eine Vielzahl von Linux-Distributionen (siehe [Unterstützte Distributionen](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Die folgenden Artikel führen Sie durch die Vorbereitung der verschiedenen Linux-Distributionen, die in Azure unterstützt werden:

* **[CentOS-basierte Verteilungen](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES und openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Sonstige – nicht unterstützte Distributionen](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Beachten Sie auch die **[Installationshinweise für Linux](create-upload-generic.md#general-linux-installation-notes)** . Diese enthalten allgemeine Tipps zur Vorbereitung von Linux-Images für Azure.

> [!NOTE]
> Die [Azure Platform-SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gilt nur dann für virtuelle Computer unter Linux, wenn eine der unterstützten Distributionen mit Konfigurationsdetails verwendet wird, die im Abschnitt mit den unterstützten Versionen unter [Linux auf von Azure unterstützten Verteilungen](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) angegeben sind.
> 
> 

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Ressourcengruppen verknüpfen logisch alle Azure-Ressourcen zur Unterstützung Ihrer virtuellen Computer, z.B. das virtuelle Netzwerk und den Speicher. Weitere Informationen zu Ressourcengruppen finden Sie unter [Ressourcengruppen – Übersicht](../../azure-resource-manager/resource-group-overview.md). Vor dem Hochladen Ihres benutzerdefinierten Datenträgers und dem Erstellen virtueller Computer müssen Sie zuerst mit [az group create](/cli/azure/group) eine Ressourcengruppe erstellen.

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mithilfe von [az storage account create](/cli/azure/storage/account) ein Speicherkonto für Ihren benutzerdefinierten Datenträger und die virtuellen Computer. Alle virtuellen Computer mit nicht verwalteten Datenträgern, die Sie aus Ihrem benutzerdefinierten Datenträger erstellen, müssen sich im gleichen Speicherkonto wie der Datenträger befinden. 

Das folgende Beispiel erstellt ein Speicherkonto namens `mystorageaccount` in der zuvor erstellten Ressourcengruppe:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Auflisten von Speicherkontoschlüsseln
Azure generiert zwei 512-Bit-Zugriffsschlüssel für jedes Speicherkonto. Die Zugriffsschlüssel werden für die Authentifizierung beim Speicherkonto verwendet, um beispielsweise Schreibvorgänge auszuführen. Erfahren Sie [hier](../../storage/common/storage-account-manage.md#access-keys)mehr über das Verwalten von Speicherzugriff. Zeigen Sie mit [az storage account keys list](/cli/azure/storage/account/keys) die Zugriffsschlüssel an.

Zeigen Sie die Zugriffsschlüssel für das erstellte Speicherkonto an:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
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
Notieren Sie sich `key1` , da Sie ihn in den nächsten Schritten für die Interaktion mit Ihrem Speicherkonto benötigen.

## <a name="create-a-storage-container"></a>Erstellen eines Speichercontainers
Auf die gleiche Weise, in der Sie verschiedene Verzeichnisse erstellen, um Ihr lokales Dateisystem logisch zu organisieren, erstellen Sie Container in einem Speicherkonto, um die Datenträger zu organisieren. Ein Speicherkonto kann eine beliebige Anzahl von Containern enthalten. Erstellen Sie mit [az storage container create](/cli/azure/storage/container) einen Container.

Im folgenden Beispiel wird ein Container namens `mydisks` erstellt:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Hochladen der VHD
Laden Sie jetzt Ihren benutzerdefinierten Datenträger mit [az storage blob upload](/cli/azure/storage/blob) hoch. Das Hochladen und Speichern Ihrer benutzerdefinierten Datenträger erfolgt als Seitenblob.

Geben Sie Ihren Zugriffsschlüssel, den Container, den Sie im vorherigen Schritt erstellt haben, und dann den Pfad zum benutzerdefinierten Datenträger auf dem lokalen Computer an:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Erstellen des virtuellen Computers
Um einen virtuellen Computer mit nicht verwalteten Datenträgern zu erstellen, geben Sie den URI des Datenträgers (`--image`) in [az vm create](/cli/azure/vm) an. Das folgende Beispiel erstellt einen virtuellen Computer namens `myVM` und verwendet dabei den zuvor hochgeladenen Datenträger:

Geben Sie den Parameter `--image` im Befehl [az vm create](/cli/azure/vm) an, um auf Ihren benutzerdefinierten Datenträger zu verweisen. Stellen Sie sicher, dass `--storage-account` dem Speicherkonto entspricht, in dem Ihr benutzerdefinierter Datenträger gespeichert ist. Sie müssen nicht den gleichen Container verwenden wie der benutzerdefinierte Datenträger, um Ihre virtuellen Computer zu speichern. Stellen Sie sicher, dass Sie jegliche weiteren Container auf die gleiche Weise erstellen, wie in den vorherigen Schritten beschrieben, bevor Sie Ihren benutzerdefinierten Datenträger hochladen.

Das folgende Beispiel erstellt den virtuellen Computer `myVM` aus Ihrem benutzerdefinierten Datenträger:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Sie müssen alle erforderlichen Zusatzparameter für den **az vm create**-Befehl angeben bzw. entsprechende Eingabeaufforderungen beantworten. Dazu gehören Benutzername und SSH-Schlüssel.


## <a name="resource-manager-template"></a>Resource Manager-Vorlage
Azure Resource Manager-Vorlagen sind JSON-Dateien (JavaScript Object Notation), die die Umgebung definieren, die Sie erstellen möchten. Die Vorlagen werden nach unterschiedlichen Ressourcenanbieter unterteilt, z.B. Compute oder Netzwerk. Sie können vorhandene Vorlagen verwenden oder eigene schreiben. Erfahren Sie [Verwendung von Resource Manager und Vorlagen](../../azure-resource-manager/resource-group-overview.md)einen virtuellen Computer erstellen.

Im Anbieter `Microsoft.Compute/virtualMachines` der Vorlage ist ein `storageProfile`-Knoten vorhanden, der die Konfigurationsdetails für den virtuellen Computer enthält. Die beiden wichtigsten zu bearbeitenden Parameter sind die `image`- und `vhd`-URIs, die auf Ihren benutzerdefinierten Datenträger und die neue virtuelle Festplatte Ihres virtuellen Computers verweisen. Im Folgenden finden Sie ein JSON-Beispiel für die Verwendung eines benutzerdefinierten Datenträgers:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Sie können [diese vorhandene Vorlage zum Erstellen eines virtuellen Computers aus einem benutzerdefinierten Image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) verwenden oder sich mit dem [Erstellen eigener Azure Resource Manager-Vorlagen](../../azure-resource-manager/resource-group-authoring-templates.md) beschäftigen. 

Wenn Sie eine Vorlage konfiguriert haben, erstellen Sie Ihre virtuellen Computer mithilfe von [az group deployment create](/cli/azure/group/deployment). Geben Sie den URI der JSON-Vorlage mit dem Parameter `--template-uri` an:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Wenn Sie eine JSON-Datei lokal auf Ihrem Computer gespeichert haben, können Sie stattdessen den Parameter `--template-file` verwenden:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie den benutzerdefinierten virtuellen Datenträger vorbereitet und hochgeladen haben, können Sie sich mit der [Verwendung von Resource Manager und Vorlagen](../../azure-resource-manager/resource-group-overview.md)beschäftigen. Informationen zum Hinzufügen eines Datenträgers zu Ihren neuen virtuellen Computern finden Sie [hier](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . Falls auf Ihren virtuellen Computern Anwendungen ausgeführt werden, auf die Sie zugreifen müssen, müssen Sie [Ports und Endpunkte öffnen](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

