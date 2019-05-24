---
title: 'Azure CLI-Skriptbeispiel: Erstellen eines Batch-Kontos – Batch-Dienst | Microsoft-Dokumentation'
description: 'Azure CLI-Skriptbeispiel: Erstellen eines Batch-Kontos im Modus „Batch-Dienst“'
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
ms.openlocfilehash: 67504d9597eb68faceb67a3e5a1d4d7abc7079c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66127410"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI-Beispiel: Erstellen eines Batch-Kontos im Modus „Batch-Dienst“

Dieses Skript erstellt ein Azure Batch-Konto im Modus „Batch-Dienst“ und zeigt, wie verschiedene Eigenschaften des Kontos abgefragt und aktualisiert werden. Wenn Sie ein Batch-Konto im Standardmodus „Batch-Dienst“ erstellen, werden die Computeknoten standardmäßig intern durch den Batch-Dienst zugewiesen. Zugeordnete Computeknoten unterliegen einem separaten vCPU-Kontingent (Kernkontingent), und das Konto kann entweder über Anmeldeinformationen eines gemeinsam verwendeten Schlüssels oder über ein Azure Active Directory-Token authentifiziert werden.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens die Azure CLI-Version 2.0.20 verwenden. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Erstellt ein Speicherkonto. |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Aktualisiert die Eigenschaften des Batch-Kontos.  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | Ruft Details zum angegebenen Batch-Konto ab.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | Ruft die Zugriffsschlüssel zum angegebenen Batch-Konto ab.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Authentifiziert das angegebene Batch-Konto zur weiteren CLI-Interaktion.  |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
