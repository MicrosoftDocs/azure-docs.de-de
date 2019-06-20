---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 44ee258567ca357687feb24337f2d5974e2532b0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66169626"
---
## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mit dem Befehl [az storage account create](/cli/azure/storage/account) ein allgemeines Speicherkonto. Das allgemeine Speicherkonto kann für alle vier Dienste verwendet werden: Blobs, Files, Tables und Queues. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Angeben der Anmeldeinformationen für das Speicherkonto

Für die meisten Befehle der Azure-Befehlszeilenschnittstelle, die in diesem Tutorial verwendet werden, werden die Anmeldeinformationen für das Speicherkonto benötigt. Diese können auf unterschiedliche Weise angegeben werden. Eine der einfachsten Methoden besteht jedoch darin, die Umgebungsvariablen `AZURE_STORAGE_ACCOUNT` und `AZURE_STORAGE_KEY` festzulegen.

Zeigen Sie zunächst mithilfe des Befehls [az storage account keys list](/cli/azure/storage/account/keys) Ihre Speicherkontoschlüssel an:

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Legen Sie dann die Umgebungsvariablen `AZURE_STORAGE_ACCOUNT` und `AZURE_STORAGE_KEY` fest. Hierzu können Sie in der Bash-Shell den Befehl `export` verwenden:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_KEY="myStorageAccountKey"
```
