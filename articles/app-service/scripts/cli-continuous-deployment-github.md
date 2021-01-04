---
title: 'Über die CLI: Continuous Deployment über GitHub'
description: Hier erfahren Sie, wie Sie die Azure CLI zum Automatisieren der Bereitstellung und Verwaltung Ihrer App Service-App verwenden. In diesem Beispiel wird gezeigt, wie Sie eine App mit CI/CD über GitHub erstellen.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/02/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 2147976ae73f93e6f451dbd871ead865e2331455
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006322"
---
# <a name="create-an-app-service-app-with-continuous-deployment-from-github-using-cli"></a>Erstellen einer App Service-App mit Continuous Deployment über GitHub mithilfe der CLI

Dieses Beispielskript erstellt eine App in App Service mit den zugehörigen Ressourcen und richtet dann Continuous Deployment über ein GitHub-Repository ein. Informationen zur GitHub-Bereitstellung ohne Continuous Deployment finden Sie unter [Create an App Service app with deployment from GitHub using Azure CLI](cli-deploy-github.md) (Erstellen einer App Service-App mit Bereitstellung über GitHub mithilfe der Azure CLI). Für dieses Beispiel benötigen Sie Folgendes:

* Ein GitHub-Repository mit Anwendungscode, für das Sie über Administratorberechtigungen verfügen. Strukturieren Sie Ihr Repository entsprechend der Tabelle unter [Vorbereiten Ihres Repositorys](../deploy-continuous-deployment.md#prepare-your-repository), um automatische Builds zu erhalten.
* Ein [persönliches Zugriffstoken (PAT)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) für Ihr GitHub-Konto.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, benötigen Sie die Azure CLI-Version 2.0 oder höher. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Create an app with continuous deployment from GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Erstellt einen App Service-Plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Erstellt eine App Service-App. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config) | Ordnet eine App Service-App einem Git- oder Mercurial-Repository zu. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../samples-cli.md).
