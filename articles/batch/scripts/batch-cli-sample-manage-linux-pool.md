---
title: 'Azure CLI-Skriptbeispiel: Linux-Pool in Batch'
description: In diesem Skript werden einige der verfügbaren Azure CLI-Befehle zum Erstellen und Verwalten eines Pools mit Linux-Computeknoten in Azure Batch vorgestellt.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b2e0fbf44be5718cf5577f6bc9aea436968e2fc3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073542"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI-Beispiel: Erstellen und Verwalten eines Linux-Pools in Azure Batch

In diesem Skript werden einige der verfügbaren Azure CLI-Befehle zum Erstellen und Verwalten eines Pools mit Linux-Computeknoten in Azure Batch vorgestellt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.20 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Erstellt das Batch-Konto. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Authentifiziert das angegebene Batch-Konto zur weiteren CLI-Interaktion.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Listet verfügbare Knoten-Agent-SKUs und Imageinformationen auf.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Erstellt einen Pool mit Computeknoten.  |
| [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) | Ändert die Größe der ausgeführten virtuellen Computer im angegebenen Pool.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Zeigt die Eigenschaften eines Pools an.  |
| [az batch node list](/cli/azure/batch/node#az-batch-node-list) | Listet alle Computeknoten im angegebenen Pool auf.  |
| [az batch node reboot](/cli/azure/batch/node#az-batch-node-reboot) | Startet den angegebenen Computeknoten neu.  |
| [az batch node delete](/cli/azure/batch/node#az-batch-node-delete) | Löscht die aufgelisteten Knoten aus dem angegebenen Pool.  |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
