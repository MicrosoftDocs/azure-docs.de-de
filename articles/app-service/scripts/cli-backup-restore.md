---
title: Azure CLI-Skriptbeispiel – Wiederherstellen einer Web-App aus einem Backup | Microsoft Docs
description: Azure CLI-Skriptbeispiel – Wiederherstellen einer Web-App aus einem Backup
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
ms.openlocfilehash: affdf22a3c4cb496983da557b415773f4274db48
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136921"
---
# <a name="restore-a-web-app-from-a-backup-using-cli"></a>Wiederherstellen einer Web-App aus einem Backup mithilfe der CLI

Dieses Beispielskript erstellt eine Web-App in App Service mit den zugehörigen Ressourcen und anschließend eine einmalige Sicherung für sie. 

Um dieses Skript auszuführen, benötigen Sie eine vorhandene Sicherung für eine Web-App. Wie Sie eine Sicherung erstellen, erfahren Sie unter [Backup up a web app](cli-backup-onetime.md) (Sichern einer Web-app) oder [Create a scheduled backup for a web app](cli-backup-scheduled.md) (Erstellen einer geplanten Sicherung für eine Web-App).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, benötigen Sie die Azure CLI-Version 2.0 oder höher. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Ruft eine Liste der Sicherungen für eine Web-App ab. |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-restore) | Stellt eine Web-App aus einem Backup wieder her. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../samples-cli.md).
