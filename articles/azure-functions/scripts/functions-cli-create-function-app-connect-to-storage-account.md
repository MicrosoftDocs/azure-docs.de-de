---
title: 'Erstellen einer Funktions-App mit verbundenem Speicher: Azure CLI'
description: 'Azure CLI-Skriptbeispiel: Erstellen einer Azure-Funktion, die eine Verbindung mit Azure Storage herstellt'
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: b95ec2da96e385ba4595a5552e64d6cc039c0e16
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565329"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>Erstellen einer Funktions-App mit einer benannten Speicherkontoverbindung 

Dieses Azure Functions-Beispielskript erstellt eine Funktions-App und stellt eine Verbindung zwischen der Funktion und einem Azure Storage-Konto her. Die erstellte App-Einstellung mit der Verbindung kann mit einem [Storage-Trigger oder einer Storage-Bindung](../functions-bindings-storage-blob.md) verwendet werden. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript

Dieses Beispiel erstellt eine Azure-Funktionen-App und fügt einer App-Einstellung die Speicher-Verbindungszeichenfolge hinzu.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellen Sie eine Ressourcengruppe mit einem Speicherort. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Erstellen Sie ein Speicherkonto. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Erstellt eine Funktions-App im serverlosen [Verbrauchsplan](../functions-scale.md#consumption-plan). |
| [az storage account show-connection-string](/cli/azure/storage/account#az-storage-account-show-connection-string) | Ruft die Verbindungszeichenfolge für das Konto ab. |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Legt die Verbindungszeichenfolge als App-Einstellung in der Funktions-App fest. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere Azure Functions-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Functions](../functions-cli-samples.md).
