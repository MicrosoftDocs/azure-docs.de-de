---
title: 'Azure CLI-Skriptbeispiel: Abrufen des Hostnamens, der Ports und Schlüssel für Azure Cache for Redis | Microsoft-Dokumentation'
description: 'Azure CLI-Skriptbeispiel: Abrufen des Hostnamens, der Ports und Schlüssel für eine Azure Cache for Redis-Instanz'
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: ff410db561879089c4c1f20acb7cb349f0484fda
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60234320"
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
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Details zu einer Azure Cache for Redis-Instanz abrufen. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Zugriffsschlüssel für eine Azure Cache for Redis-Instanz abrufen. |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche Azure Cache for Redis-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Cache for Redis](../cli-samples.md).
