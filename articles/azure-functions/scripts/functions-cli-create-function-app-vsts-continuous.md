---
title: 'Erstellen einer Funktions-App mit DevOps-Bereitstellung: Azure CLI'
description: Erstellen einer Funktions-App und Bereitstellen von Funktionscode über Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 25e0f43a851bbf7658847aa52cc9ee778941f718
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565210"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Erstellen einer Funktion in Azure, die über Azure DevOps bereitgestellt wird

In diesem Thema wird gezeigt, wie sich mit Azure Functions eine [serverlose](https://azure.microsoft.com/solutions/serverless/) Funktions-App unter Verwendung des [Verbrauchsplans](../functions-scale.md#consumption-plan) erstellen lässt. Die Funktions-App, die als Container für Ihre Funktionen fungiert, wird kontinuierlich aus einem Azure DevOps-Repository bereitgestellt. 

Um dieses Thema durchzuarbeiten, benötigen Sie Folgendes:

* Ein Azure DevOps-Repository, das Ihr Funktions-App-Projekt enthält und für das Sie über Administratorberechtigungen verfügen.
* Ein [persönliches Zugriffstoken (PAT)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate), um auf Ihr Azure DevOps-Repository zuzugreifen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

In diesem Beispiel wird eine Azure-Funktions-App erstellt und Funktionscode über Azure DevOps bereitgestellt.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, ein Speicherkonto, eine Funktions-App und alle zugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Erstellt das für die Funktions-App erforderliche Speicherkonto. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Erstellt eine Funktions-App im serverlosen [Verbrauchsplan](../functions-scale.md#consumption-plan). |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Ordnet eine Funktionen-App einem Git- oder Mercurial-Repository zu. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere Azure Functions-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Functions](../functions-cli-samples.md).
