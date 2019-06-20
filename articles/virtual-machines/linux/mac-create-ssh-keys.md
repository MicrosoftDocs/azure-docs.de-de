---
title: Erstellen und Verwenden eines SSH-Schlüsselpaars für virtuelle Linux-Computer in Azure| Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie ein SSH-Schlüsselpaar (öffentlicher und privater Schlüssel) für virtuelle Linux-Computer in Azure erstellen und verwenden, um die Sicherheit des Authentifizierungsprozesses zu verbessern.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: d442d09c8c8ded3aa50faf74e28c8d95ded24a5e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614420"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Kurzanleitung: Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure

Mit einem SSH-Schlüsselpaar (Secure Shell) können Sie virtuelle Computer (VMs) in Azure erstellen, bei deren Authentifizierung SSH-Schlüssel verwendet werden, sodass zum Anmelden keine Kennwörter mehr erforderlich sind. In diesem Artikel erfahren Sie, wie Sie für virtuelle Linux-Computer schnell eine Datei mit einem SSH-Schlüsselpaar (ein öffentlicher und ein privater Schlüssel) generieren. Sie können diese Schritte mit Azure Cloud Shell, einem macOS- oder Linux-Host, dem Windows-Subsystem für Linux und anderen Tools ausführen, die OpenSSH unterstützen. 

> [!NOTE]
> Mit SSH-Schlüsseln erstellte VMs sind standardmäßig mit deaktivierten Kennwörtern konfiguriert, was die Schwierigkeit für Brute-Force-Angriffe wesentlich erhöht. 

Weitere Hintergrundinformationen und Beispiele finden Sie unter [Ausführliche Schritte zum Erstellen von SSH-Schlüsselpaaren](create-ssh-keys-detailed.md).

Weitere Möglichkeiten zum Generieren und Verwenden von SSH-Schlüsseln auf einem Windows-Computer finden Sie unter [Gewusst wie: Verwenden von SSH-Schlüsseln mit Windows in Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Verwenden Sie den Befehl `ssh-keygen`, um Dateien mit öffentlichen und privaten SSH-Schlüsseln zu generieren. Diese Dateien werden standardmäßig im Verzeichnis „~/.ssh“ erstellt. Sie können einen anderen Speicherort und ein optionales, zusätzliche Kennwort (*Passphrase*) für den Zugriff auf die Datei des privaten Schlüssels angeben. Wenn an dem angegebenen Speicherort bereits ein SSH-Schlüsselpaar mit demselben Namen vorhanden ist, werden diese Dateien überschrieben.

Der folgende Befehl erstellt ein SSH-Schlüsselpaar mittels RSA-Verschlüsselung und einer Bitlänge von 2048:

```bash
ssh-keygen -t rsa -b 2048
```

Bei Verwendung der [Azure CLI](/cli/azure) zum Erstellen Ihres virtuellen Computers mit dem Befehl [az vm create](/cli/azure/vm#az-vm-create) können Sie optional Dateien für öffentliche und private SSH-Schlüssel mit der Option `--generate-ssh-keys` generieren. Die Schlüsseldateien im Verzeichnis „~/.ssh“ gespeichert, sofern nicht mit der Option `--ssh-dest-key-path` anders angegeben. Die Option `--generate-ssh-keys` überschreibt keine vorhandenen Schlüsseldateien, sondern gibt stattdessen einen Fehler zurück. Ersetzen Sie im folgenden Befehl *VMname* und *RGname* durch Ihre eigenen Werte:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Angeben eines öffentlichen SSH-Schlüssels beim Bereitstellen eines virtuellen Computers

Um eine Linux-VM zu erstellen, die SSH-Schlüssel zur Authentifizierung verwendet, geben Sie Ihren öffentlichen SSH-Schlüssel beim Erstellen des virtuellen Computers mit Azure-Portal, Azure CLI, Azure Resource Manager-Vorlagen oder anderen Methoden ein:

* [Erstellen Sie eines virtuellen Linux-Computers mit dem Azure-Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Erstellen einer Linux-VM mit der Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Erstellen einer Linux-VM mit einer Azure-Vorlage](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Falls Sie nicht mit dem Format öffentlicher SSH-Schlüssel vertraut sein sollten, können Sie Ihren öffentlichen Schlüssel mit dem folgenden `cat`-Befehl anzeige, wobei Sie hierbei `~/.ssh/id_rsa.pub` durch den Pfad und Dateinamen Ihrer eigenen Datei mit dem öffentlichen Schlüssel ersetzen:

```bash
cat ~/.ssh/id_rsa.pub
```

Ein typischer Wert eines öffentlicher Schlüssels sieht aus wie in diesem Beispiel:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Stellen Sie sicher, dass Sie keine nachstehenden Leerzeichen kopieren, wenn Sie den Inhalt der Datei mit dem öffentlichen Schlüssel für die Verwendung im Azure-Portal oder in einer Resource Manager-Vorlage kopieren und einfügen. Um einen öffentlichen Schlüssel in macOS zu kopieren, können Sie die öffentliche Schlüsseldatei per Pipe an **pbcopy** übergeben. Ähnlich wie unter Linux, können Sie die öffentliche Schlüsseldatei per Pipe an Programme wie **xclip** übergeben.

Der öffentliche Schlüssel, den Sie in Ihrer Linux-VM in Azure ablegen, wird standardmäßig in „~/.ssh/id_rsa.pub“ gespeichert, sofern Sie den Speicherort nicht bei der Erstellung der Schlüsselpaars geändert haben. Um die [Azure CLI 2.0](/cli/azure) zum Erstellen Ihres virtuellen Computers mit einem vorhandenen öffentlichen Schlüssel zu verwenden, geben Sie den Wert und optional den Speicherort dieses öffentlichen Schlüssels mithilfe des Befehls [az vm create](/cli/azure/vm#az-vm-create) mit der Option `--ssh-key-value` an. Ersetzen Sie im folgenden Befehl *VMname*, *RGname* und *keyFile* durch Ihre eigenen Werte:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>Herstellen einer SSH-Verbindung mit Ihrem virtuellen Computer

Stellen Sie mit dem auf Ihrer Azure-VM bereitgestellten öffentlichen Schlüssel und dem privaten Schlüssel auf Ihrem lokalen System mithilfe der IP-Adresse oder dem DNS-Namen Ihres virtuellen Computers eine SSH-Verbindung mit Ihrem virtuellen Computer her. Ersetzen Sie im folgenden Befehl *azureuser* und *myvm.westus.cloudapp.azure.com* durch den Benutzernamen des Administrators und dem vollständig qualifizierten Domänennamen (oder der IP-Adresse):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Falls Sie beim Erstellen des Schlüsselpaars eine Passphrase angegeben haben, müssen Sie diese Passphrase eingeben, wenn Sie während des Anmeldeprozesses dazu aufgefordert werden. Die VM wird Ihrer Datei „~/.ssh/known_hosts“ hinzugefügt, und Sie werden erst wieder zum Herstellen der Verbindung aufgefordert, wenn sich der öffentliche Schlüssel auf Ihrer Azure-VM ändert oder der Servername aus „~/.ssh/known_hosts“ entfernt wird.

Wenn der virtuelle Computer die JIT-Zugriffsrichtlinie (Just-In-Time) verwendet, müssen Sie den Zugriff anfordern, bevor Sie eine Verbindung mit dem virtuellen Computer herstellen können. Weitere Informationen zur Just-In-Time-Richtlinie finden Sie unter [Verwalten des Zugriffs auf virtuelle Computer mithilfe der Just-In-Time-Richtlinie](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Arbeit mit SSH-Schlüsselpaaren finden Sie unter [Ausführliche Schritte zum Erstellen und Verwalten von SSH-Schlüsselpaaren](create-ssh-keys-detailed.md).

* Wenn beim Herstellen von SSH-Verbindungen mit einer Azure-VM Probleme auftreten, finden Sie weitere Informationen unter [Behandeln von Problemen mit SSH-Verbindungen mit einem virtuellen Azure Linux-Computer](troubleshoot-ssh-connection.md).
