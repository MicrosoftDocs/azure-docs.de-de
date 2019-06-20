---
title: Verschlüsseln von Datenträgern auf einem virtuellen Linux-Computer in Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie virtuelle Datenträger auf einem virtuellen Linux-Computer mithilfe der Azure CLI verschlüsseln, um die Sicherheit zu erhöhen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 15bd3cf2ab6ea5285662610c2c0a850bb180e2f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731616"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Verschlüsseln eines virtuellen Linux-Computers in Azure

Zum Verbessern der Sicherheit und Compliance von virtuellen Computern können virtuelle Datenträger und der virtuelle Computer selbst verschlüsselt werden. Die Verschlüsselung der virtuellen Computer basiert auf kryptografischen Schlüsseln, die in Azure Key Vault gesichert werden. Diese kryptografischen Schlüssel werden von Ihnen kontrolliert, und Sie können deren Verwendung überwachen. In diesem Artikel wird erläutert, wie Sie virtuelle Datenträger auf einem virtuellen Linux-Computer mithilfe der Azure CLI verschlüsseln. 

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

Wenn Sie die Befehlszeilenschnittstelle (CLI) lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 2.0.30 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Übersicht über die Datenträgerverschlüsselung
Virtuelle Datenträger auf virtuellen Linux-Computern werden im Ruhezustand mithilfe von [dm-crypt](https://wikipedia.org/wiki/Dm-crypt) verschlüsselt. Für die Verschlüsselung virtueller Datenträger in Azure fallen keine Gebühren an. Kryptografische Schlüssel werden in Azure Key Vault mit Softwareschutz gespeichert. Alternativ können Sie Schlüssel aber auch in Hardwaresicherheitsmodulen (HSMs) mit FIPS 140-2 Level 2-Zertifizierung importieren oder generieren. Diese kryptografischen Schlüssel werden allein von Ihnen kontrolliert, und Sie können deren Verwendung überwachen. Die kryptografischen Schlüssel dienen zum Verschlüsseln und Entschlüsseln virtueller Datenträger, die an Ihren virtuellen Computer angefügt sind. 

Gehen Sie zum Verschlüsseln eines virtuellen Computers wie folgt vor:

1. Erstellen Sie in einer Azure Key Vault-Instanz einen kryptografischen Schlüssel.
1. Konfigurieren Sie den kryptografischen Schlüssel so, dass er zum Verschlüsseln von Datenträgern verwendet werden kann.
1. Aktivieren Sie die Datenträgerverschlüsselung für Ihre virtuellen Datenträger.
1. Die erforderlichen kryptografischen Schlüssel werden von Azure Key Vault angefordert.
1. Die virtuellen Datenträger werden mithilfe des angegebenen kryptografischen Schlüssels verschlüsselt.


## <a name="requirements-and-limitations"></a>Voraussetzungen und Einschränkungen
Unterstützte Szenarien und Voraussetzungen für die Datenträgerverschlüsselung:

* Folgende Linux-Server-SKUs: Ubuntu, CentOS, SUSE und SUSE Linux Enterprise Server (SLES) sowie Red Hat Enterprise Linux
* Alle Ressourcen (wie Key Vault, Speicherkonto und virtueller Computer) müssen sich in der gleichen Azure-Region und im gleichen Abonnement befinden.
* Virtuelle Computer der standardmäßigen Serien A, D, DS, G, GS usw.
* Bei Aktualisierung der kryptografischen Schlüssel auf einem bereits verschlüsselten virtuellen Linux-Computer

Die Datenträgerverschlüsselung wird derzeit in folgenden Szenarien nicht unterstützt:

* Bei virtuellen Computern des Basic-Tarifs
* Bei virtuellen Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden
* Bei Deaktivierung der Betriebssystem-Datenträgerverschlüsselung auf virtuellen Linux-Computern
* Bei Verwendung von benutzerdefinierten Linux-Images

Weitere Informationen zu unterstützten Szenarien und Einschränkungen finden Sie unter [Azure Disk Encryption für virtuelle IaaS-Computer](../../security/azure-security-disk-encryption.md).


## <a name="create-an-azure-key-vault-and-keys"></a>Erstellen einer Azure Key Vault-Instanz und von Schlüsseln
Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispielparameternamen sind u. a. *myResourceGroup*, *myKey* und *myVM*.

Erstellen Sie zum Speichern Ihrer kryptografischen Schlüssel zunächst eine Azure Key Vault-Instanz. In Azure Key Vault können Schlüssel, geheime Schlüssel und Kennwörter gespeichert werden, um eine sichere Implementierung in Anwendungen und Diensten zu ermöglichen. Bei der Verschlüsselung virtueller Datenträger dient Key Vault zum Speichern eines kryptografischen Schlüssels, der zum Verschlüsseln oder Entschlüsseln der virtuellen Datenträger verwendet wird.

Aktivieren Sie in Ihrem Azure-Abonnement mit [az provider register](/cli/azure/provider#az-provider-register) den Azure Key Vault-Anbieter, und erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort `eastus`:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

Die Azure Key Vault-Instanz mit den kryptografischen Schlüsseln und die dazugehörigen Computeressourcen wie Speicher und der eigentliche virtuelle Computer müssen sich in der gleichen Region befinden. Erstellen Sie mit [az keyvault create](/cli/azure/keyvault#az-keyvault-create) einen Azure-Schlüsseltresor, und aktivieren Sie den Schlüsseltresor zur Verwendung der Datenträgerverschlüsselung. Geben Sie einen eindeutigen Key Vault-Namen für *keyvault_name* an, wie nachfolgend gezeigt:

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Kryptografische Schlüssel können mit Softwareschutz oder mit HSM-Schutz (Hardwaresicherheitsmodul) gespeichert werden. Für die Verwendung eines HSMs wird eine Key Vault-Premiuminstanz benötigt. Die Erstellung einer Key Vault-Premiuminstanz ist im Gegensatz zur Verwendung einer Key Vault-Standardinstanz, bei der Schlüssel mit Softwareschutz gespeichert werden, mit zusätzlichen Kosten verbunden. Wenn Sie eine Key Vault-Premiuminstanz erstellen möchten, fügen Sie dem Befehl aus dem vorherigen Schritt `--sku Premium` hinzu. Im folgenden Beispiel werden softwaregeschützte Schlüssel verwendet, da Sie eine Key Vault-Standardinstanz erstellt haben.

Bei beiden Schutzmodellen muss der Azure-Plattform Zugriff gewährt werden, um beim Start des virtuellen Computers die kryptografischen Schlüssel anfordern und die virtuellen Datenträger entschlüsseln zu können. Erstellen Sie mithilfe von [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) einen kryptografischen Schlüssel in Ihrem Schlüsseltresor. Im folgenden Beispiel wird ein Schlüssel mit dem Namen *myKey* erstellt:

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers
Erstellen Sie mit [az vm create](/cli/azure/vm#az-vm-create) eine VM, und fügen Sie einen 5-GB-Datenträger an die VM an. Nur bestimmte Marketplace-Images unterstützen die Datenträgerverschlüsselung. Im folgenden Beispiel wird unter Verwendung eines *Ubuntu 16.04 LTS*-Images ein virtueller Computer mit dem Namen *myVM* erstellt:

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Stellen Sie mithilfe des SSH-Netzwerkprotokolls eine Verbindung mit Ihrer VM her, und verwenden Sie dabei die in der Ausgabe des vorherigen Befehls enthaltene *publicIpAddress*. Erstellen Sie eine Partition und ein Dateisystem, und binden Sie dann den Datenträger ein. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einer Linux-VM zum Einbinden des neuen Datenträgers](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Schließen Sie Ihre SSH-Sitzung.


## <a name="encrypt-the-virtual-machine"></a>Verschlüsseln des virtuellen Computers


Verschlüsseln Sie Ihre VM mit [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable).

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Es dauert etwas, bis der Vorgang zum Verschlüsseln des Datenträgers abgeschlossen ist. Überwachen Sie den Status des Vorgangs mit [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

Wenn der Vorgang abgeschlossen ist, sieht die Ausgabe etwa wie das folgende Beispiel aus:

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>Hinzufügen zusätzlicher Datenträger
Nachdem Sie Ihre Datenträger verschlüsselt haben, können Sie dem virtuellen Computer weitere virtuelle Datenträger hinzufügen und diese verschlüsseln. 

Nachdem der Datenträger zum virtuellen Computer hinzugefügt wurde, führen Sie den Befehl zum Verschlüsseln der virtuellen Datenträger wie folgt erneut aus:

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Verwalten von Azure Key Vault (einschließlich Informationen zum Löschen kryptografischer Schlüssel und Vault-Instanzen) finden Sie unter [Verwalten von Schlüsseltresor mit CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Weitere Informationen zur Datenträgerverschlüsselung (etwa zum Vorbereiten des Uploads eines verschlüsselten benutzerdefinierten virtuellen Computers in Azure) finden Sie unter [Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer](../../security/azure-security-disk-encryption.md).
