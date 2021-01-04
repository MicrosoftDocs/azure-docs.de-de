---
title: 'Einbinden einer Dateifreigabe in eine Python-Funktions-App: Azure CLI'
description: Erstellen Sie eine serverlose Python-Funktions-App, und binden Sie eine vorhandene Dateifreigabe mithilfe der Azure-Befehlszeilenschnittstelle ein.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: bdcaeaca7c063f0532167077bba63f7e52a3d491
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565057"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Einbinden einer Dateifreigabe in eine Python-Funktions-App mithilfe der Azure CLI

Dieses Azure Functions-Beispielskript erstellt eine Funktions-App und eine Freigabe in Azure Files. Anschließend wird die Freigabe eingebunden, sodass Ihre Funktionen auf die Daten zugreifen können.  

>[!NOTE]
>Die erstellte Funktions-App wird unter Python-Version 3.7 ausgeführt. Azure Functions [unterstützt auch die Python-Versionen 3.6 und 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="sample-script"></a>Beispielskript

Dieses Skript erstellt eine Azure-Funktions-App unter Verwendung des [Verbrauchsplans](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft. Das Skript verwendet die folgenden Befehle:

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Erstellt ein Azure Storage-Konto. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Erstellt eine Funktionen-App. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Erstellt eine Azure Files-Freigabe im Speicherkonto. | 
| [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) | Erstellt ein Verzeichnis in der Freigabe. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Bindet die Freigabe in die Funktions-App ein. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Zeigt die in die Funktions-App eingebundenen Dateifreigaben an. | 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere Azure Functions-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Functions](../functions-cli-samples.md).
