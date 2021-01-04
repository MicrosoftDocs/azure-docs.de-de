---
title: 'Azure CLI-Skriptbeispiel: Exportieren aus einem Azure App Configuration-Speicher'
titleSuffix: Azure App Configuration
description: Verwenden eines Azure CLI-Skripts zum Exportieren der Konfiguration aus Azure App Configuration
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: be3fac17ba99194a76b27c78040a76cce43405a0
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931111"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Exportieren aus einem Azure App Configuration-Speicher

Dieses Beispielskript exportiert Schlüssel-Wert-Paare aus einem Azure App Configuration-Speicher.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="sample-script"></a>Beispielskript

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle für das Exportieren aus einem App Configuration-Speicher. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az appconfig kv export](/cli/azure/appconfig/kv#az-appconfig-kv-export) | Dient zum Exportieren aus einer App Configuration-Speicherressource. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Weitere CLI-Skriptbeispiele für App Configuration finden Sie in den [CLI-Beispielen für Azure App Configuration](../cli-samples.md).
