---
title: 'Azure CLI-Skriptbeispiel: Importieren in einen App Configuration-Speicher'
titleSuffix: Azure App Configuration
description: 'Verwenden eines Azure CLI-Skripts: Importieren der Konfiguration in Azure App Configuration'
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2a8bd22629bf0aa269125187a77710f6dc9fd93e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931309"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importieren in einen Azure-App-Konfigurationsspeicher

Dieses Beispielskript importiert Schlüssel-Wert-Einstellungen in einen Azure App Configuration-Speicher.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle, um in einen App Configuration-Speicher zu importieren. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importiert in eine App Configuration-Speicherressource. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure-Befehlszeilenschnittstelle finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere CLI-Skriptbeispiele für App Configuration finden Sie in den [CLI-Beispielen für Azure App Configuration](../cli-samples.md).
