---
title: 'Azure CLI-Skriptbeispiel: Erstellen einer App mit Bereitstellung über GitHub | Microsoft-Dokumentation'
description: 'Azure CLI-Skriptbeispiel: Erstellen einer App Service-App mit Bereitstellung über GitHub'
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: sample
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 61cf31bcd82997a13ffae79df106485a86a9444d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136822"
---
# <a name="create-an-app-service-app-with-deployment-from-github-using-azure-cli"></a>Erstellen einer App Service-App mit Bereitstellung über GitHub mit der Azure CLI

Dieses Beispielskript erstellt in App Service eine App mit ihren zugehörigen Ressourcen. Anschließend wird der App-Code aus einem öffentlichen GitHub-Repository bereitgestellt (ohne Continuous Deployment). Informationen zur GitHub-Bereitstellung mit Continuous Deployment finden Sie unter [Erstellen einer App mit Continuous Deployment über GitHub](cli-continuous-deployment-github.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, benötigen Sie die Azure CLI-Version 2.0 oder höher. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Create an app with deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts 

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Erstellt einen App Service-Plan. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Erstellt eine App Service-App. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config) | Ordnet eine App Service-App einem Git- oder Mercurial-Repository zu. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../samples-cli.md).
