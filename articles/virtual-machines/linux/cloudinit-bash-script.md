---
title: Verwenden von cloud-init zum Ausführen von Bash-Skripts auf einer Linux-VM in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie cloud-init zum Ausführen eines Bash-Skripts auf einer Linux-VM während der Erstellung mithilfe der Azure CLI verwenden.
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
ms.openlocfilehash: 4f65ebfd2e1ce508c5cf9b224871102a35b55fe0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60627842"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Verwenden von cloud-init zum Ausführen von Bash-Skripts auf einer Linux-VM in Azure
In diesem Artikel wird gezeigt, wie Sie [cloud-init](https://cloudinit.readthedocs.io) zum Ausführen eines vorhandenen Bash-Skripts auf einem virtuellen Linux-Computer (VM) oder in VM-Skalierungsgruppen (VMSS) während der Bereitstellung in Azure verwenden. Diese cloud-init-Skripts werden beim erstmaligen Starten ausgeführt, nachdem die Ressourcen von Azure bereitgestellt wurden. Weitere Informationen zur nativen Funktion von cloud-init in Azure und zu den unterstützten Linux-Distributionen finden Sie unter [Übersicht über cloud-init](using-cloud-init.md).

## <a name="run-a-bash-script-with-cloud-init"></a>Ausführen von Bash-Skripts mit cloud-init
Mit cloud-Init müssen Sie Ihre vorhandenen Skripts nicht in eine Cloudkonfiguration konvertieren, da cloud-init verschiedene Eingabetypen akzeptiert, zu denen auch Bash-Skripts gehören.

Wenn Sie die Azure-Erweiterung für benutzerdefinierte Linux-Skripts zum Ausführen Ihrer Skripts verwendet haben, können Sie diese mit cloud-init migrieren. Da in Azure-Erweiterungen jedoch Warnungen zu Skriptfehlern integriert sind, führt die Bereitstellung eines cloud-init-Images NICHT zu einem Fehler, wenn das Skript fehlerhaft ist.

Um diese Funktionalität in Aktion sehen zu können, erstellen Sie zu Testzwecken ein einfaches Bash-Skript. Wie die cloud-init-Datei `#cloud-config` muss auch dieses Skript lokal auf dem Computer gespeichert werden, auf dem Sie die Befehle der Azure-Befehlszeilenschnittstelle ausführen, um den virtuellen Computer bereitzustellen.  Erstellen Sie für dieses Beispiel die Datei in der Cloud Shell, nicht auf dem lokalen Computer. Dazu können Sie einen beliebigen Editor verwenden. Geben Sie `sensible-editor simple_bash.sh` ein, um die Datei zu erstellen und eine Liste der verfügbaren Editoren anzuzeigen. Wählen Sie #1 aus, um den Editor **nano** zu verwenden. Stellen Sie sicher, dass die gesamte cloud-init-Datei ordnungsgemäß kopiert wird, insbesondere die erste Zeile.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Vor der Bereitstellung dieses Images müssen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Erstellen Sie nun mit dem Befehl [az vm create](/cli/azure/vm) eine VM, und geben Sie mit `--custom-data simple_bash.sh` die Bash-Skriptdatei wie folgt an:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Überprüfen, ob das Bash-Skript ausgeführt wurde
Stellen Sie eine SSH-Verbindung mit der öffentlichen IP-Adresse Ihrer VM her, die in der Ausgabe über den vorherigen Befehl gezeigt wird. Geben Sie Ihre eigene **publicIpAddress** wie folgt ein:

```bash
ssh <publicIpAddress>
```

Wechseln Sie in das Verzeichnis **/tmp**, und überprüfen Sie, ob die Datei „myScript.txt“ vorhanden ist und den entsprechenden Text enthält.  Ist dies nicht der Fall, finden Sie in **/var/log/cloud-init.log** weitere Details.  Suchen Sie nach dem folgenden Eintrag:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Nächste Schritte
Weitere cloud-init-Beispiele für Änderungen an der Konfiguration finden Sie in den folgenden Themen:
 
- [Hinzufügen weiterer Linux-Benutzer zu virtuellen Computern](cloudinit-add-user.md)
- [Ausführen eines Paket-Managers zum Aktualisieren vorhandener Pakete beim ersten Start](cloudinit-update-vm.md)
- [Ändern des lokalen Hostnamens virtueller Computer](cloudinit-update-vm-hostname.md) 
- [Installieren von Anwendungspaketen, Aktualisieren von Konfigurationsdateien und Einfügen von Schlüsseln](tutorial-automate-vm-deployment.md)
