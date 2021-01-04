---
title: Erstellen eines IoT Hubs mit der Azure-CLI | Microsoft Docs
description: Hier erfahren Sie, wie Sie mit den Azure CLI-Befehlen eine Ressourcengruppe und dann in der Ressourcengruppe einen IoT Hub erstellen. Außerdem erfahren Sie, wie die Hub-Instanz entfernt wird.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659912"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Erstellen eines IoT Hubs mit der Azure-Befehlszeilenschnittstelle

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In diesem Artikel erfahren Sie, wie Sie unter Verwendung der Azure-Befehlszeilenschnittstelle eine IoT Hub-Instanz erstellen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

Verwenden Sie die Azure CLI zum Erstellen einer Ressourcengruppe, und fügen Sie dann einen IoT Hub hinzu.

1. Wenn Sie einen IoT Hub erstellen, müssen Sie ihn in einer Ressourcengruppe erstellen. Verwenden Sie entweder eine vorhandene Ressourcengruppe, oder führen Sie den folgenden [Befehl zum Erstellen einer Ressourcengruppe](/cli/azure/resource) aus:
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > Im vorherigen Beispiel wird die Ressourcengruppe am Standort „USA, Westen“ erstellt. Mit dem folgenden Befehl können Sie eine Liste mit verfügbaren Standorten anzeigen: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Führen Sie den folgenden [Befehl zum Erstellen einer IoT Hub-Instanz](/cli/azure/iot/hub#az-iot-hub-create) in Ihrer Ressourcengruppe aus, und verwenden Sie dabei einen global eindeutigen Namen für Ihre IoT Hub-Instanz:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Mit dem vorherigen Befehl wird ein kostenpflichtiger IoT Hub im S1-Tarif erstellt. Weitere Informationen finden Sie unter [Azure IoT Hub – Preise](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Entfernen eines IoT Hubs

Über die Azure-Befehlszeilenschnittstelle können Sie [eine einzelne Ressource](/cli/azure/resource) (beispielsweise eine IoT Hub-Instanz) oder eine Ressourcengruppe mit allen dazugehörigen Ressourcen (einschließlich IoT Hub-Instanzen) löschen.

Führen Sie zum [Löschen einer IoT Hub-Instanz](/cli/azure/iot/hub#az-iot-hub-delete) den folgenden Befehl aus:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Führen Sie zum [Löschen einer Ressourcengruppe und aller dazugehörigen Ressourcen](/cli/azure/group#az-group-delete) den folgenden Befehl aus:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung einer IoT Hub-Instanz finden Sie in folgenden Artikeln:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)
* [Erstellen eines IoT Hubs über das Portal](iot-hub-create-through-portal.md)