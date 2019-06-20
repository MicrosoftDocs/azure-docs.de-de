---
title: Verwenden von cloud-init zum Aktualisieren und Installieren von Paketen auf einer Linux-VM in Azure | Microsoft-Dokumentation
description: Verwenden von cloud-init zum Aktualisieren und Installieren von Paketen auf einer Linux-VM während der Erstellung mit der Azure CLI
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
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: d5f4dc7f4abc13f253a206a63e65faf1106f9c7c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60627740"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Verwenden von cloud-init zum Aktualisieren und Installieren von Paketen auf einer Linux-VM in Azure
In diesem Artikel wird gezeigt, wie Sie [cloud-init](https://cloudinit.readthedocs.io) zum Aktualisieren von Paketen auf einem virtuellen Linux-Computer (VM) oder in VM-Skalierungsgruppen (VMSS) während der Bereitstellung in Azure verwenden. Diese cloud-init-Skripts werden beim erstmaligen Starten ausgeführt, nachdem die Ressourcen von Azure bereitgestellt wurden. Weitere Informationen zur nativen Funktionsweise von „cloud-init“ in Azure und zu den unterstützten Linux-Distributionen finden Sie in der [Übersicht zu „cloud-init“](using-cloud-init.md).

## <a name="update-a-vm-with-cloud-init"></a>Aktualisieren einer VM mit cloud-init
Aus Sicherheitsgründen sollten Sie eine VM so konfigurieren, dass die neuesten Updates beim ersten Systemstart angewendet werden. Da cloud-init in verschiedenen Linux-Distributionen funktioniert, müssen weder `apt` oder `yum` für den Paket-Manager angegeben werden. Stattdessen definieren Sie `package_upgrade` und lassen die geeignete Methode für die verwendete Distribution vom cloud-init-Vorgang auswählen. Bei diesem Workflow können Sie dieselben cloud-init-Skripts für verschiedene Distributionen verwenden.

Um den Upgradevorgang in Aktion zu sehen, erstellen Sie in Ihrer aktuellen Shell eine Datei mit dem Namen *cloud_init_upgrade.txt* und fügen die unten angegebene Konfiguration ein. Erstellen Sie für dieses Beispiel die Datei in der Cloud Shell, nicht auf dem lokalen Computer. Dazu können Sie einen beliebigen Editor verwenden. Geben Sie `sensible-editor cloud_init_upgrade.txt` ein, um die Datei zu erstellen und eine Liste der verfügbaren Editoren anzuzeigen. Wählen Sie #1 aus, um den Editor **nano** zu verwenden. Stellen Sie sicher, dass die gesamte cloud-init-Datei ordnungsgemäß kopiert wird, insbesondere die erste Zeile.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Vor der Bereitstellung dieses Images müssen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Erstellen Sie nun mit dem Befehl [az vm create](/cli/azure/vm) eine VM, und geben Sie mit `--custom-data cloud_init_upgrade.txt` die cloud-init-Datei an, wie im Folgenden gezeigt wird:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

Stellen Sie eine SSH-Verbindung mit der öffentlichen IP-Adresse Ihrer VM her, die in der Ausgabe über den vorherigen Befehl gezeigt wird. Geben Sie Ihre eigene **publicIpAddress** wie folgt ein:

```bash
ssh <publicIpAddress>
```

Führen Sie das Paketverwaltungstool aus, und suchen Sie nach Updates.

```bash
sudo yum update
```

Da cloud-init beim Systemstart nach Updates gesucht und sie ggf. installiert hat, sollten keine zusätzlichen anzuwendenden Updates gefunden werden.  Sie sehen den Updatevorgang, die Anzahl der geänderten Pakete sowie die Installation von `httpd` durch Ausführung von `yum history` und erhalten eine Ausgabe ähnlich der folgenden.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Nächste Schritte
Weitere cloud-init-Beispiele für Änderungen an der Konfiguration finden Sie in den folgenden Themen:
 
- [Hinzufügen weiterer Linux-Benutzer zu virtuellen Computern](cloudinit-add-user.md)
- [Ausführen eines Paket-Managers zum Aktualisieren vorhandener Pakete beim ersten Start](cloudinit-update-vm.md)
- [Ändern des lokalen Hostnamens virtueller Computer](cloudinit-update-vm-hostname.md) 
- [Installieren von Anwendungspaketen, Aktualisieren von Konfigurationsdateien und Einfügen von Schlüsseln](tutorial-automate-vm-deployment.md)
