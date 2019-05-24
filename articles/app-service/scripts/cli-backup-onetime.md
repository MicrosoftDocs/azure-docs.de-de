---
title: 'Azure CLI-Skriptbeispiel: Sichern einer App | Microsoft-Dokumentation'
description: 'Azure CLI-Skriptbeispiel: Sichern einer App'
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu;cephalin
ms.custom: seodec18
ms.openlocfilehash: b394eb4674175f96d7fc0ceb8eafee0003312327
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66137009"
---
# <a name="back-up-an-app-using-cli"></a>Sichern einer App mithilfe der CLI

Dieses Beispielskript erstellt eine App in App Service mit den zugehörigen Ressourcen und anschließend eine einmalige Sicherung für sie. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, benötigen Sie die Azure CLI-Version 2.0 oder höher. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-onetime/backup-onetime.sh?highlight=3-7 "Back up an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) | Erstellt ein Speicherkonto. |
| [`az storage container create`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) | Erstellt einen Azure-Speichercontainer. |
| [`az storage container generate-sas`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-generate-sas) | Generiert ein SAS-Token für einen Azure-Speichercontainer.  |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Erstellt einen App Service-Plan. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Erstellt eine App Service-App. |
| [`az webapp config backup create`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-create) | Erstellt eine Sicherung für eine App Service-App. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Ruft eine Liste der Sicherungen für eine App Service-App ab. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../samples-cli.md).
