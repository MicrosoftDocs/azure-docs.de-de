---
title: 'Azure CLI: Azure Cache for Redis-Beispiele | Microsoft-Dokumentation'
description: Azure CLI-Beispiele für Azure Cache for Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: yegu
ms.openlocfilehash: eea74e8f1291901fea3c77b70ba3c636f40bd132
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829751"
---
# <a name="azure-cli-samples-for-azure-cache-for-redis"></a>Azure CLI-Beispiele für Azure Cache for Redis

Die folgende Tabelle enthält Links zu Bash-Skripts, die mithilfe der Azure CLI erstellt wurden.

| | |
|---|---|
|**Erstellen eines Caches**||
| [Erstellen eines Caches](./scripts/create-cache.md) | Erstellt eine Ressourcengruppe und einen Azure Cache for Redis im Basic-Tarif. |
| [Erstellen eines Premium-Caches mit Clustering](./scripts/create-premium-cache-cluster.md) | Erstellt eine Ressourcengruppe und einen Cache im Premium-Tarif mit aktiviertem Clustering.|
| [Abrufen von Cachedetails](./scripts/show-cache.md) | Ruft die Details einer Azure Cache for Redis-Instanz samt Bereitstellungsstatus ab. |
| [Abrufen von Hostname, Ports und Schlüsseln](./scripts/cache-keys-ports.md) | Ruft den Hostnamen, die Ports und Schlüssel einer Azure Cache for Redis-Instanz ab. |
|**Web-App plus Cache**||
| [Herstellen einer Verbindung zwischen einer Web-App und einem Azure Cache for Redis](./../app-service/scripts/cli-connect-to-redis.md) | Erstellt eine Azure-Web-App und einen Azure Cache for Redis und fügt dann die Redis-Verbindungsdetails zu den App-Einstellungen hinzu. |
|**Löschen eines Caches**||
| [Löschen eines Caches](./scripts/delete-cache.md) | Löscht eine Azure Cache for Redis-Instanz.  |
| | |

Weitere Informationen zur Azure CLI finden Sie unter [Installieren der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) und [Erste Schritte mit der Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
