---
title: 'Azure CLI-Skriptbeispiel: Löschen eines Azure App Configuration-Speichers'
titleSuffix: Azure App Configuration
description: Löschen Sie einen Azure App Configuration-Speicher mithilfe eines Azure CLI-Beispielskripts. Sehen Sie sich die Links zu Referenzartikeln zu Befehlen an, die im Skript verwendet werden.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49d6a85faa55de5dbf50377998dbe2fc829d9f6f
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929785"
---
# <a name="delete-an-azure-app-configuration-store"></a>Löschen eines Azure-App-Konfigurationspeichers

Mit diesem Beispielskript wird eine Instanz der Azure-App-Konfiguration gelöscht.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle, um einen App Configuration-Speicher zu löschen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az appconfig delete](/cli/azure/appconfig#az-appconfig-delete) | Löscht eine App Configuration-Speicherressource. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere CLI-Skriptbeispiele für App Configuration finden Sie in den [CLI-Beispielen für Azure App Configuration](../cli-samples.md).
