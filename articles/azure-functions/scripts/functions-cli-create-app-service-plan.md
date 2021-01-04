---
title: Erstellen einer Funktions-App in einem App Service-Plan – Azure CLI
description: Azure CLI-Skriptbeispiel – Erstellen einer Funktionen-App in einem App Service-Plan
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 67efb5cc146b10b7e8194b7de4f6e0f0d6c08de9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565448"
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Erstellen einer Funktionen-App in einem App Service-Plan

Dieses Azure Functions-Beispielskript erstellt eine Funktions-App, die als Container für Ihre Funktionen fungiert. Die erstellte Funktions-App verwendet einen dedizierten App Service-Plan. Die Serverressourcen sind also immer aktiv.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

Dieses Skript erstellt eine Azure-Funktionen-App mithilfe eines dedizierten [App Service-Plans](../functions-scale.md#app-service-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft. Das Skript verwendet die folgenden Befehle:

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Erstellt ein Azure Storage-Konto. |
| [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | Erstellt einen Premium-Tarif. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Erstellt eine Funktions-App im App Service-Plan. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere Azure Functions-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Functions](../functions-cli-samples.md).
