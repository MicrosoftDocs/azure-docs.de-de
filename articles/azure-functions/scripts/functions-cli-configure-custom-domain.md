---
title: Azure CLI-Skriptbeispiel – Zuordnen einer benutzerdefinierten Domäne zu einer Funktionen-App | Microsoft-Dokumentation
description: Azure CLI-Skriptbeispiel – Zuordnen einer benutzerdefinierten Domäne zu einer Funktionen-App in Azure.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/04/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 790d095cfb1b59aed1b9014fc474f9ad6e1b3328
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131308"
---
# <a name="map-a-custom-domain-to-a-function-app"></a>Zuordnen einer benutzerdefinierten Domäne zu einer Funktionen-App

Dieses Beispielskript erstellt eine Funktions-App in einem App Service-Plan und ordnet sie dann einer von Ihnen bereitgestellten benutzerdefinierten Domäne zu. Wenn Ihre Funktions-App unter einem [Premium-Plan](../functions-scale.md#premium-plan-public-preview) oder einem [App Service-Plan](../functions-scale.md#app-service-plan) gehostet wird, können Sie entweder mit einem CNAME- oder A-Eintrag eine benutzerdefinierte Domäne zuordnen. Für Funktionen-Apps unter einem [Verbrauchstarif](../functions-scale.md#consumption-plan) wird nur die CNAME-Option unterstützt. Dieses Beispiel erstellt einen App Service-Plan und erfordert einen A-Eintrag zum zuordnen der Domäne. 

Zum Ausführen dieses Beispielskripts müssen Sie bereits einen A-Eintrag in Ihrer benutzerdefinierten Domäne konfiguriert haben, die auf den Standarddomänennamen Ihrer Web-App verweist. Weitere Informationen finden Sie in den [Anweisungen zum Zuordnen von benutzerdefinierten Domäne für Azure App Service](https://aka.ms/appservicecustomdns). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, benötigen Sie mindestens die Version 2.0 der Azure CLI. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Map a custom domain to a function app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle: Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Erstellt ein Speicherkonto, das für die Funktionen-App erforderlich ist. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az-appservice-plan-create) | Erstellt einen App Service-Plan, der zum Zuordnen einer benutzerdefinierten Domäne erforderlich ist. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Erstellt eine Funktions-App im App Service-Plan. |
| [az functionapp config hostname add](https://docs.microsoft.com/cli/azure/functionapp/config/hostname#az-functionapp-config-hostname-add) | Ordnet eine benutzerdefinierte Domäne zu einer Funktionen-App zu. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Weitere Functions-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Functions](../functions-cli-samples.md).
