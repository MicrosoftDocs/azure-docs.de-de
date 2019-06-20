---
title: Kopieren einer Linux-VM mit Azure CLI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Kopie Ihrer Azure Linux-VM mithilfe von Azure CLI und Managed Disks erstellen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: abc8c09a51104c81b827afb7055531df98691714
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328747"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Erstellen einer Kopie Ihrer Linux-VM mithilfe von Azure CLI und Managed Disks

In diesem Artikel erfahren Sie, wie Sie eine Kopie des virtuellen Azure-Computers (VM) unter Linux mit der Azure CLI und dem Azure Resource Manager-Bereitstellungsmodell erstellen. 

Sie können auch [einen virtuellen Computer aus einer VHD hochladen und erstellen](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Voraussetzungen

-   Installieren Sie die [Azure CLI](/cli/azure/install-az-cli2).

-   Melden Sie sich mit [az login](/cli/azure/reference-index#az-login) bei einem Azure-Konto an.

-   Halten Sie eine Azure-VM zur Verwendung als Quelle für die Kopie bereit.

## <a name="stop-the-source-vm"></a>Beenden der Quell-VM

Heben Sie die Zuordnung des virtuellen Quellcomputers mit [az vm deallocate](/cli/azure/vm#az-vm-deallocate) auf.
Im folgenden Beispiel wird die Zuordnung für die VM *myVM* in der Ressourcengruppe *myResourceGroup* aufgehoben:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Kopieren der Quell-VM

Zum Kopieren eines virtuellen Computers erstellen Sie eine Kopie der zugrunde liegenden virtuellen Festplatte. Dieser Prozess erstellt eine spezialisierte VHD als verwalteten Datenträger, der die gleiche Konfiguration und die gleichen Einstellungen wie die Quell-VM aufweist.

Weitere Informationen zu Azure Managed Disks finden Sie in der [Übersicht über Azure Managed Disks](../windows/managed-disks-overview.md). 

1.  Listen Sie die einzelnen virtuellen Computer und den Namen des dazugehörigen Betriebssystemdatenträgers mit [az vm list](/cli/azure/vm#az-vm-list) auf. Im folgenden Beispiel werden alle virtuellen Computer in der Ressourcengruppe *myResourceGroup* aufgelistet:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Kopieren Sie den Datenträger, indem Sie einen neuen verwalteten Datenträger erstellen und [az disk create](/cli/azure/disk#az-disk-create) verwenden. Im folgenden Beispiel wird ein Datenträger mit dem Namen *myCopiedDisk* auf Grundlage des verwalteten Datenträgers *myDisk* erstellt:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Überprüfen Sie die verwalteten Datenträger jetzt in der Ressourcengruppe mit [az disk list](/cli/azure/disk#az-disk-list). Im folgenden Beispiel werden alle verwalteten Datenträger in der Ressourcengruppe *myResourceGroup* aufgelistet:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Einrichten eines virtuellen Netzwerks

Mit den folgenden optionalen Schritten erstellen Sie ein neues virtuelles Netzwerk, ein neues Subnetz, eine neue öffentliche IP-Adresse und eine neue virtuelle Netzwerkschnittstellenkarte (Network Interface Card, NIC).

Wenn Sie einen virtuellen Computer zur Problembehandlung oder für zusätzliche Bereitstellungen kopieren, kann es sein, dass Sie keinen virtuellen Computer in einem vorhandenen virtuellen Netzwerk verwenden möchten.

Führen Sie die nächsten Schritte aus, wenn Sie eine VM-Infrastruktur für Ihre kopierten VMs erstellen möchten. Wenn Sie kein virtuelles Netzwerk erstellen möchten, fahren Sie mit [Erstellen einer VM](#create-a-vm) fort.

1.  Erstellen Sie das virtuelle Netzwerk mit [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Im folgenden Beispiel werden ein virtuelles Netzwerk mit dem Namen *myVnet* und ein Subnetz mit dem Namen *mySubnet* erstellt:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Erstellen Sie mit [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) eine öffentliche IP-Adresse. Im folgenden Beispiel wird eine öffentliche IP-Adresse namens *myPublicIP* mit dem DNS-Namen *mypublicdns* erstellt. Da der DNS-Name eindeutig sein muss, geben Sie einen eindeutigen Namen an.

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Erstellen Sie die NIC mit [az network nic create](/cli/azure/network/nic#az-network-nic-create).
    Im folgenden Beispiel wird die NIC *myNic* erstellt, die an das Subnetz *mySubnet* angefügt ist:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Erstellen einer VM

Erstellen Sie mit [az vm create](/cli/azure/vm#az-vm-create) eine VM.

Geben Sie wie folgt den kopierten verwalteten Datenträger an, der als Betriebssystemdatenträger (`--attach-os-disk`) verwendet werden soll:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Verwendung der Azure-CLI für die Verwaltung des neuen virtuellen Computers finden Sie unter [Befehle der Azure-Befehlszeilenschnittstelle im Resource Manager-Modus](../azure-cli-arm-commands.md).
