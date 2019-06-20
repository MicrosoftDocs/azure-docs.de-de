---
title: Verwenden von cloud-init zum Festlegen eines Hostnamens für eine Linux-VM in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie cloud-init zum Anpassen einer Linux-VM während der Erstellung mithilfe der Azure CLI verwenden.
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
ms.openlocfilehash: da20c4b30e2708bf7754d025cfbd2c269c3b5c7c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648940"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Verwenden von cloud-init zum Festlegen eines Hostnamens für eine Linux-VM in Azure
In diesem Artikel wird gezeigt, wie Sie [cloud-init](https://cloudinit.readthedocs.io) zum Festlegen eines Hostnamens für einen virtuellen Computer (VM) oder eine VM-Skalierungsgruppe (VMSS) während der Bereitstellung in Azure verwenden. Diese cloud-init-Skripts werden beim erstmaligen Starten ausgeführt, nachdem die Ressourcen von Azure bereitgestellt wurden. Weitere Informationen zur nativen Funktionsweise von „cloud-init“ in Azure und zu den unterstützten Linux-Distributionen finden Sie in der [Übersicht zu „cloud-init“](using-cloud-init.md).

## <a name="set-the-hostname-with-cloud-init"></a>Festlegen des Hostnamens mit cloud-init
Standardmäßig ist der Hostname mit dem VM-Namen identisch, wenn Sie einen neuen virtuellen Computer in Azure erstellen.  Um mit [az vm create](/cli/azure/vm) beim Erstellen einer VM in Azure ein cloud-init-Skript zum Ändern des Standardhostnamens auszuführen, geben Sie die cloud-init-Datei mit dem Schalter `--custom-data` an.  

Um den Upgradevorgang in Aktion zu sehen, erstellen Sie in Ihrer aktuellen Shell eine Datei mit dem Namen *cloud_init_hostname.txt* und fügen die unten angegebene Konfiguration ein. Erstellen Sie für dieses Beispiel die Datei in der Cloud Shell, nicht auf dem lokalen Computer. Dazu können Sie einen beliebigen Editor verwenden. Geben Sie `sensible-editor cloud_init_hostname.txt` ein, um die Datei zu erstellen und eine Liste der verfügbaren Editoren anzuzeigen. Wählen Sie #1 aus, um den Editor **nano** zu verwenden. Stellen Sie sicher, dass die gesamte cloud-init-Datei ordnungsgemäß kopiert wird, insbesondere die erste Zeile.  

```yaml
#cloud-config
hostname: myhostname
```

Vor der Bereitstellung dieses Images müssen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Erstellen Sie nun mit dem Befehl [az vm create](/cli/azure/vm) eine VM, und geben Sie mit `--custom-data cloud_init_hostname.txt` die cloud-init-Datei an, wie im Folgenden gezeigt wird:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Nach der Erstellung zeigt die Azure CLI Informationen zur VM an. Verwenden Sie `publicIpAddress`, um eine SSH-Verbindung mit Ihrer VM herzustellen. Geben Sie Ihre eigene Adresse wie folgt ein:

```bash
ssh <publicIpAddress>
```

Um den Namen der VM anzuzeigen, verwenden Sie den Befehl `hostname` wie folgt:

```bash
hostname
```

Die VM sollte den Hostnamen als den in der cloud-init-Datei festgelegten Wert melden, wie in der folgenden Beispielausgabe gezeigt wird:

```bash
myhostname
```

## <a name="next-steps"></a>Nächste Schritte
Weitere cloud-init-Beispiele für Änderungen an der Konfiguration finden Sie in den folgenden Themen:
 
- [Hinzufügen weiterer Linux-Benutzer zu virtuellen Computern](cloudinit-add-user.md)
- [Ausführen eines Paket-Managers zum Aktualisieren vorhandener Pakete beim ersten Start](cloudinit-update-vm.md)
- [Ändern des lokalen Hostnamens virtueller Computer](cloudinit-update-vm-hostname.md) 
- [Installieren von Anwendungspaketen, Aktualisieren von Konfigurationsdateien und Einfügen von Schlüsseln](tutorial-automate-vm-deployment.md)
