---
title: 'Über die CLI: Skalieren einer App mit Traffic Manager'
description: Hier erfahren Sie, wie Sie die Azure CLI zum Automatisieren der Bereitstellung und Verwaltung Ihrer App Service-App verwenden. In diesem Beispiel wird gezeigt, wie eine App mit Traffic Manager weltweit skaliert werden kann.
author: msangapu-msft
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 9c1c4ad16a491f7e868a395fa5c36d9605348cdb
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005681"
---
# <a name="scale-an-app-service-app-worldwide-with-a-high-availability-architecture-using-azure-cli"></a>Weltweites Skalieren einer App Service-App mit einer Hochverfügbarkeitsarchitektur mit der Azure CLI

Dieses Beispielskript erstellt eine Ressourcengruppe, zwei App Service-Pläne, zwei Apps, ein Traffic Manager-Profil und zwei Traffic Manager-Endpunkte. Nach Abschluss der Übung verfügen Sie über eine hoch verfügbare Architektur, die basierend auf der niedrigsten Netzwerklatenz die globale Verfügbarkeit Ihrer App ermöglicht.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, eine App Service-App, ein Traffic Manager-Profil und alle zugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Erstellt einen App Service-Plan. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Erstellt eine App Service-App. |
| [`az network traffic-manager profile create`](/cli/azure/network/traffic-manager/profile#az-network-traffic-manager-profile-create) | Erstellt ein Azure Traffic Manager-Profil. |
| [`az network traffic-manager endpoint create`](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-create) | Fügt einem Azure Traffic Manager-Profil einen Endpunkt hinzu. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche App Service-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure App Service](../samples-cli.md).
