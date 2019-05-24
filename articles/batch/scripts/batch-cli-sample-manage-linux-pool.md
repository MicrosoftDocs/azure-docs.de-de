---
title: 'Azure CLI-Skriptbeispiel: Linux-Pool in Batch | Microsoft-Dokumentation'
description: 'Azure CLI-Skriptbeispiel: Erstellen und Verwalten eines Linux-Pools in Batch'
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: lahugh
ms.openlocfilehash: 0ebda53db852a613c2ad6f0e223c25b3946b7116
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66127477"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI-Beispiel: Erstellen und Verwalten eines Linux-Pools in Azure Batch

In diesem Skript werden einige der verfügbaren Azure CLI-Tools zum Erstellen und Verwalten eines Pools mit Linux-Computeknoten in Azure Batch präsentiert.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für diesen Schnellstart die Azure CLI-Version 2.0.20 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). 

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
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az-batch-pool-node-agent-skus-list) | Listet verfügbare Knoten-Agent-SKUs und Imageinformationen auf.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | Erstellt einen Pool mit Computeknoten.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-resize) | Ändert die Größe der ausgeführten virtuellen Computer im angegebenen Pool.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-show) | Zeigt die Eigenschaften eines Pools an.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-list) | Listet alle Computeknoten im angegebenen Pool auf.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-reboot) | Startet den angegebenen Computeknoten neu.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-delete) | Löscht die aufgelisteten Knoten aus dem angegebenen Pool.  |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).
