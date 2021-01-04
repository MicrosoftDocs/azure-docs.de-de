---
title: 'Abrufen des Hostnamens, der Ports und Schlüssel – Azure Cache for Redis: Azure CLI'
description: In diesem Azure CLI-Codebeispiel wird gezeigt, wie Sie den Hostnamen, die Ports und Schlüssel für eine Azure Cache for Redis-Instanz abrufen.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: d3e8e359a97c091e025049ac8a978e1beca1d759
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184217"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Abrufen des Hostnamens, der Ports und Schlüssel für Azure Cache for Redis

In diesem Szenario erfahren Sie, wie Sie den Hostnamen, die Ports und Schlüssel abrufen, mit deren Hilfe eine Verbindung mit einer Azure Cache for Redis-Instanz hergestellt wird.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle, um den Hostnamen, die Schlüssel und Ports einer Azure Cache for Redis-Instanz abzurufen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az redis show](/cli/azure/redis) | Ruft Details zu einer Azure Cache for Redis-Instanz ab. |
| [az redis list-keys](/cli/azure/redis) | Zugriffsschlüssel für eine Azure Cache for Redis-Instanz abrufen. |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).

Zusätzliche Azure Cache for Redis-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Cache for Redis](../cli-samples.md).