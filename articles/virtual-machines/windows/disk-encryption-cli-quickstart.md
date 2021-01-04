---
title: Erstellen und Verschlüsseln einer Windows-VM mit der Azure CLI
description: In diesem Schnellstart erfahren Sie, wie Sie mit der Azure CLI einen virtuellen Windows-Computer erstellen und verschlüsseln.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0156a1d7cea477a1725b60a5e1de229e76d2664c
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517987"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Schnellstart: Erstellen und Verschlüsseln einer Windows-VM mit der Azure CLI

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In diesem Schnellstart wird gezeigt, wie Sie mithilfe der Azure CLI einen virtuellen Computer (VM) mit Windows Server 2016 erstellen und verschlüsseln.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.30 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) einen virtuellen Computer. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* erstellt: Dieses Beispiel verwendet *azureuser* als Administratorbenutzernamen und *myPassword12* als Kennwort.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

Das Erstellen des virtuellen Computers und der unterstützenden Ressourcen dauert einige Minuten. In der folgenden Beispielausgabe wird angezeigt, dass der Vorgang der VM-Erstellung erfolgreich war.

```console
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Erstellen eines für Verschlüsselungsschlüssel konfigurierten Schlüsseltresors

Bei der Azure-Datenträgerverschlüsselung werden die Verschlüsselungsschlüssel in Azure Key Vault gespeichert. Erstellen Sie mit [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) einen Schlüsseltresor. Um dem Schlüsseltresor die Speicherung von Verschlüsselungscodes zu ermöglichen, verwenden Sie den Parameter „--enabled-for-disk-encryption“.
> [!Important]
> Jeder Schlüsseltresor muss einen eindeutigen Namen haben. Das folgende Beispiel erstellt einen Schlüsseltresor mit dem Namen *myKV*. Ihren eigenen müssen Sie jedoch mit einem anderen Namen versehen.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Verschlüsseln des virtuellen Computers

Verschlüsseln Sie Ihre VM mit [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest), und geben Sie dem Parameter „--disk-encryption-keyvault“ Ihren eindeutigen Schlüsseltresornamen an.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Sie können mit [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show) prüfen, ob die Verschlüsselung für Ihre VM aktiviert ist.

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

In der zurückgegebenen Ausgabe finden Sie Folgendes:

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe, VM und der Schlüsseltresor nicht mehr benötigt werden, können Sie sie mit dem Befehl [az group delete](/cli/azure/group) entfernen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie einen virtuellen Computer und einen Schlüsseltresor erstellt, der für Verschlüsselungsschlüssel aktiviert wurde, und die VM verschlüsselt.  Fahren Sie mit dem nächsten Artikel fort, um weitere Informationen zu den Voraussetzungen für Azure Disk Encryption und virtuelle IaaS-Computer zu erhalten.

> [!div class="nextstepaction"]
> [Übersicht über Azure Disk Encryption](disk-encryption-overview.md)
