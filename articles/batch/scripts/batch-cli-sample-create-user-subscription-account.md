---
title: 'Azure CLI-Skriptbeispiel: Erstellen eines Batch-Kontos – Benutzerabonnement'
description: Dieses Skript erstellt ein Azure Batch-Konto im Modus „Benutzerabonnement“. Dieses Konto ordnet Computeknoten in Ihrem Abonnement zu.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: c9b8ba2ef782dcdc99cb18698175b8b53a53f0dd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076774"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI-Beispiel: Erstellen eines Batch-Kontos im Modus „Benutzerabonnement“

Dieses Skript erstellt ein Azure Batch-Konto im Modus „Benutzerabonnement“. Ein Konto, das Ihrem Abonnement Computeknoten zuordnet, muss über ein Azure Active Directory-Token authentifiziert werden. Die zugeordneten Computeknoten zählen zum vCPU-Kontingent (Kernkontingent) Ihres Abonnements. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.20 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.  

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
| [az keyvault create](/cli/azure/keyvault#az-keyvault-create) | Erstellt einen Schlüsseltresor. |
| [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) | Aktualisiert die Sicherheitsrichtlinie des angegebenen Schlüsseltresors. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Erstellt das Batch-Konto.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Authentifiziert das angegebene Batch-Konto zur weiteren CLI-Interaktion.  |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
