---
title: Verwenden von „cloud-init“ zum Konfigurieren einer Auslagerungsdatei auf einer Linux-VM | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie „cloud-init“ zum Konfigurieren einer Auslagerungsdatei auf einer Linux-VM während der Erstellung mithilfe der Azure CLI verwenden.
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 626fd4739daf2506854c42f16ac986a361ebab38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60729816"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Verwenden von „cloud-init“ zum Konfigurieren einer Auslagerungsdatei auf einer Linux-VM
Dieser Artikel zeigt, wie Sie [cloud-init](https://cloudinit.readthedocs.io) zum Konfigurieren der Auslagerungsdatei auf verschiedenen Linux-Distributionen verwenden. Die Auslagerungsdatei wurde traditionell vom Linux-Agent (WALA) konfiguriert – abhängig davon, für welche Distributionen eine Auslagerungsdatei benötigt wurde.  In diesem Dokument wird das Vorgehen für die bedarfsgesteuerte Erstellung der Auslagerungsdatei zur Bereitstellungszeit unter Verwendung von „cloud-init“ beschrieben.  Weitere Informationen zur nativen Funktionsweise von „cloud-init“ in Azure und zu den unterstützten Linux-Distributionen finden Sie in der [Übersicht zu „cloud-init“](using-cloud-init.md).

## <a name="create-swapfile-for-ubuntu-based-images"></a>Erstellen einer Auslagerungsdatei für Ubuntu-basierte Images
In Azure werden für Ubuntu-Katalogimages standardmäßig keine Auslagerungsdateien erstellt. Informationen zum Aktivieren der Konfiguration einer Auslagerungsdatei während der VM-Bereitstellung mit „cloud-init“ finden Sie im Ubuntu-Wiki im [AzureSwapPartitions-Dokument](https://wiki.ubuntu.com/AzureSwapPartitions).

## <a name="create-swapfile-for-red-hat-and-centos-based-images"></a>Erstellen einer Auslagerungsdatei für Red Hat- und CentOS-basierte Images

Erstellen Sie in der aktuellen Shell eine Datei namens *cloud_init_swapfile.txt*, und fügen Sie die folgende Konfiguration ein. Erstellen Sie für dieses Beispiel die Datei in der Cloud Shell, nicht auf dem lokalen Computer. Dazu können Sie einen beliebigen Editor verwenden. Geben Sie `sensible-editor cloud_init_swapfile.txt` ein, um die Datei zu erstellen und eine Liste der verfügbaren Editoren anzuzeigen. Wählen Sie #1 aus, um den Editor **nano** zu verwenden. Stellen Sie sicher, dass die gesamte cloud-init-Datei ordnungsgemäß kopiert wird, insbesondere die erste Zeile.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Vor der Bereitstellung dieses Images müssen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Erstellen Sie nun mit dem Befehl [az vm create](/cli/azure/vm) eine VM, und geben Sie mit `--custom-data cloud_init_swapfile.txt` die cloud-init-Datei an, wie im Folgenden gezeigt wird:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Überprüfen, ob die Auslagerungsdatei erstellt wurde
Stellen Sie eine SSH-Verbindung mit der öffentlichen IP-Adresse Ihrer VM her, die in der Ausgabe über den vorherigen Befehl gezeigt wird. Geben Sie Ihre eigene **publicIpAddress** wie folgt ein:

```bash
ssh <publicIpAddress>
```

Nachdem Sie eine SSH-Verbindung mit der VM hergestellt haben, überprüfen Sie, ob die Auslagerungsdatei erstellt wurde.

```bash
swapon -s
```

Die Ausgabe dieses Befehls sollte wie folgt aussehen:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Wenn für Ihr vorhandenes Azure-Image eine Auslagerungsdatei konfiguriert wurde und Sie die Konfiguration der Auslagerungsdatei für neue Images ändern möchten, müssen Sie die vorhandene Auslagerungsdatei entfernen. Weitere Informationen finden Sie im Dokument „Anpassen von Images für die Bereitstellung mit cloud-init“.

## <a name="next-steps"></a>Nächste Schritte
Weitere cloud-init-Beispiele für Änderungen an der Konfiguration finden Sie in den folgenden Themen:
 
- [Hinzufügen weiterer Linux-Benutzer zu virtuellen Computern](cloudinit-add-user.md)
- [Ausführen eines Paket-Managers zum Aktualisieren vorhandener Pakete beim ersten Start](cloudinit-update-vm.md)
- [Ändern des lokalen Hostnamens virtueller Computer](cloudinit-update-vm-hostname.md) 
- [Installieren von Anwendungspaketen, Aktualisieren von Konfigurationsdateien und Einfügen von Schlüsseln](tutorial-automate-vm-deployment.md)
