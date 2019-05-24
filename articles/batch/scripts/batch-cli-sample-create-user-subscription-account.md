---
title: 'Azure CLI-Skriptbeispiel: Erstellen eines Batch-Kontos – Benutzerabonnement | Microsoft-Dokumentation'
description: 'Azure CLI-Skriptbeispiel: Erstellen eines Batch-Kontos im Modus „Benutzerabonnement“'
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
ms.openlocfilehash: 3358c1aab5dc39e30690b242039940830f2e9ffc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66127441"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI-Beispiel: Erstellen eines Batch-Kontos im Modus „Benutzerabonnement“

Dieses Skript erstellt ein Azure Batch-Konto im Modus „Benutzerabonnement“. Ein Konto, das Ihrem Abonnement Computeknoten zuordnet, muss über ein Azure Active Directory-Token authentifiziert werden. Die zugeordneten Computeknoten zählen zum vCPU-Kontingent (Kernkontingent) Ihres Abonnements. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens die Azure CLI-Version 2.0.20 verwenden. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az role assignment create](/cli/azure/role) | Erstellt eine neue Rollenzuweisung für einen Benutzer, eine Gruppe oder einen Dienstprinzipal. |
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-create) | Erstellt einen Schlüsseltresor. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-set-policy) | Aktualisiert die Sicherheitsrichtlinie des angegebenen Schlüsseltresors. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Erstellt das Batch-Konto.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Authentifiziert das angegebene Batch-Konto zur weiteren CLI-Interaktion.  |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
