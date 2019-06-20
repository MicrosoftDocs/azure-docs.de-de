---
title: Verwenden von cloud-init zum Hinzufügen von Benutzern zu einer Linux-VM in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie cloud-init zum Hinzufügen eines Benutzers zu einer Linux-VM während der Erstellung mithilfe von Azure CLI verwenden.
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
ms.openlocfilehash: 51de92eb64e9879b769baf7e574ee1dca9355040
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60650351"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Verwenden von cloud-init zum Hinzufügen von Benutzern zu einer Linux-VM in Azure
In diesem Artikel wird gezeigt, wie Sie [cloud-init](https://cloudinit.readthedocs.io) zum Hinzufügen eines Benutzers zu einem virtuellen Computer (VM) oder einer VM-Skalierungsgruppe (VMSS) während der Bereitstellung in Azure verwenden. Dieses cloud-init-Skript wird beim erstmaligen Starten ausgeführt, nachdem die Ressourcen von Azure bereitgestellt wurden. Weitere Informationen zur nativen Funktion von cloud-init in Azure und zu den unterstützten Linux-Distributionen finden Sie unter [Übersicht über cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Hinzufügen eines Benutzers zu einer VM mit cloud-init
Eine der ersten Aufgaben auf jeder neuen Linux-VM ist das Hinzufügen eines weiteren Benutzers für sich selbst, um die Verwendung von *root* zu vermeiden. SSH-Schlüssel stellen eine bewährte Methode dar, um Sicherheit und Benutzerfreundlichkeit bereitzustellen. Schlüssel werden mit diesem cloud-init-Skript zur Datei *~/.ssh/authorized_keys* hinzugefügt.

Um einer Linux-VM einen Benutzer hinzuzufügen, erstellen Sie in der aktuellen Shell eine Datei namens *cloud_init_add_user.txt*, und fügen Sie die folgende Konfiguration ein. Erstellen Sie für dieses Beispiel die Datei in der Cloud Shell, nicht auf dem lokalen Computer. Dazu können Sie einen beliebigen Editor verwenden. Geben Sie `sensible-editor cloud_init_add_user.txt` ein, um die Datei zu erstellen und eine Liste der verfügbaren Editoren anzuzeigen. Wählen Sie #1 aus, um den Editor **nano** zu verwenden. Stellen Sie sicher, dass die gesamte cloud-init-Datei ordnungsgemäß kopiert wird, insbesondere die erste Zeile.  Sie müssen Ihren eigenen öffentlichen Schlüssel (z.B. den Inhalt von *~/.ssh/id_rsa.pub*) als Wert von `ssh-authorized-keys:` angeben (der Inhalt wurde zur Vereinfachung des Beispiels gekürzt).

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> Die #cloud-config-Datei enthält den Parameter `- default`. Dadurch wird der Benutzer an den vorhandenen Administrator angefügt, der während der Bereitstellung erstellt wurde. Wenn Sie einen Benutzer ohne den `- default`-Parameter erstellen, wird der automatisch generierte Administratorbenutzer, der von der Azure-Plattform erstellt wurde, überschrieben. 

Vor der Bereitstellung dieses Images müssen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Erstellen Sie nun mit dem Befehl [az vm create](/cli/azure/vm) eine VM, und geben Sie mit `--custom-data cloud_init_add_user.txt` die cloud-init-Datei an, wie im Folgenden gezeigt wird:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

Stellen Sie eine SSH-Verbindung mit der öffentlichen IP-Adresse Ihrer VM her, die in der Ausgabe über den vorherigen Befehl gezeigt wird. Geben Sie Ihre eigene **publicIpAddress** wie folgt ein:

```bash
ssh <publicIpAddress>
```

Um zu bestätigen, dass Ihr Benutzer zur VM und den angegebenen Gruppen hinzugefügt wurde, zeigen Sie den Inhalt der Datei */etc/group* wie folgt an:

```bash
cat /etc/group
```

Die folgende Beispielausgabe zeigt, dass der Benutzer aus der Datei *cloud_init_add_user.txt* zur VM und der entsprechenden Gruppe hinzugefügt wurde:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Nächste Schritte
Weitere cloud-init-Beispiele für Änderungen an der Konfiguration finden Sie in den folgenden Themen:
 
- [Hinzufügen weiterer Linux-Benutzer zu virtuellen Computern](cloudinit-add-user.md)
- [Ausführen eines Paket-Managers zum Aktualisieren vorhandener Pakete beim ersten Start](cloudinit-update-vm.md)
- [Ändern des lokalen Hostnamens virtueller Computer](cloudinit-update-vm-hostname.md) 
- [Installieren von Anwendungspaketen, Aktualisieren von Konfigurationsdateien und Einfügen von Schlüsseln](tutorial-automate-vm-deployment.md)
