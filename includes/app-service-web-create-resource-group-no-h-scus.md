---
title: Includedatei
description: Includedatei
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 458782011623721bb44771d42caa32130e23bbc9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66139310"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Erstellen Sie in Cloud Shell mit dem Befehl [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) eine Ressourcengruppe. Im folgenden Beispiel wird am Standort *South Central US* (USA, Süden-Mitte) die Ressourcengruppe *myResourceGroup* erstellt. Wenn Sie alle unterstützten Standorte für App Service im **Free**-Tarif anzeigen möchten, führen Sie den Befehl [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) aus.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Im Allgemeinen erstellen Sie Ressourcengruppen und Ressourcen in einer Region in Ihrer Nähe. 

Nach Ausführung dieses Befehls werden die Ressourcengruppeneigenschaften in einer JSON-Ausgabe angezeigt.