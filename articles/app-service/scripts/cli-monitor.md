---
title: 'Über die CLI: Überwachen einer Web-App mit Webserverprotokollen'
description: Hier erfahren Sie, wie Sie die Azure CLI zum Automatisieren der Bereitstellung und Verwaltung Ihrer App Service-App verwenden. Dieses Beispiel zeigt, wie Sie eine App mit Webserverprotokollen überwachen.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 9881a5f37c32849fc4f10acda8346510977e6b7b
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005783"
---
# <a name="monitor-an-app-service-app-with-web-server-logs-using-azure-cli"></a>Überwachen einer App Service-App mit Webserverprotokollen per Azure CLI

Dieses Beispielskript erstellt eine Ressourcengruppe, einen App Service-Plan und eine App und konfiguriert die App für die Aktivierung der Webserverprotokolle. Anschließend werden die Protokolldateien zur Überprüfung heruntergeladen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "Monitor Logs")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, eine App Service-App und alle zugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Erstellt einen App Service-Plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Erstellt eine App Service-App. |
| [`az webapp log config`](/cli/azure/webapp/log#az-webapp-log-config) | Konfiguriert, welche Protokolle von einer App Service-App dauerhaft gespeichert werden. |
| [`az webapp log download`](/cli/azure/webapp/log#az-webapp-log-download) | Lädt die Protokolle einer App Service-App auf Ihren lokalen Computer herunter. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../samples-cli.md).
