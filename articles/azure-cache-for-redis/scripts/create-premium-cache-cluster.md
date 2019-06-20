---
title: Azure CLI-Skriptbeispiel – Erstellen eines Azure Cache for Redis vom Typ „Premium“ mit Clustering | Microsoft-Dokumentation
description: Azure CLI-Skriptbeispiel – Erstellen eines Azure Cache for Redis mit Premium-Tarif mit Clustering
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 18538cf713d2938b0b595c9d4664c39c6ada5231
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130448"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Erstellen eines Azure Cache for Redis vom Typ „Premium“ mit Clustering

In diesem Szenario erfahren Sie, wie Sie einen 6 GB-Azure Cache for Redis mit Premium-Tarif erstellen. Dabei ist Clustering aktiviert, und es sind zwei Shards vorhanden.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Beispielskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe und einen Azure Cache for Redis mit Premium-Tarif und aktiviertem Clustering zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis) | Erstellt eine Azure Cache for Redis-Instanz. |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](https://docs.microsoft.com/cli/azure).

Zusätzliche Azure Cache for Redis-CLI-Skriptbeispiele finden Sie in der [Dokumentation zu Azure Cache for Redis](../cli-samples.md).
