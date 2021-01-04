---
title: 'Über die CLI: Erstellen einer geplanten Sicherung'
description: Hier erfahren Sie, wie Sie die Azure CLI zum Automatisieren der Bereitstellung und Verwaltung Ihrer App Service-App verwenden. In diesem Beispiel wird das Erstellen einer geplanten Sicherung für eine App veranschaulicht.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 500ac99cd35cfdf601be75a19a1d43f84795cbe8
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006421"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>Erstellen einer geplanten Sicherung für eine App Service-App mit der CLI

Dieses Beispielskript erstellt in App Service eine App mit den zugehörigen Ressourcen und dann eine geplante Sicherung dafür. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, benötigen Sie die Azure CLI-Version 2.0 oder höher. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [`az storage account create`](/cli/azure/storage/account#az-storage-account-create) | Erstellt ein Speicherkonto. |
| [`az storage container create`](/cli/azure/storage/container#az-storage-container-create) | Erstellt einen Azure-Speichercontainer. |
| [`az storage container generate-sas`](/cli/azure/storage/container#az-storage-container-generate-sas) | Generiert ein SAS-Token für einen Azure-Speichercontainer.  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Erstellt einen App Service-Plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Erstellt eine App Service-App. |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup#az-webapp-config-backup-update) | Konfiguriert einen neuen Sicherungszeitplan für eine App Service-App. |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup#az-webapp-config-backup-show) | Zeigt den Sicherungszeitplan für eine App Service-App an. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az-webapp-config-backup-list) | Ruft eine Liste der Sicherungen für eine App Service-App ab. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../samples-cli.md).
