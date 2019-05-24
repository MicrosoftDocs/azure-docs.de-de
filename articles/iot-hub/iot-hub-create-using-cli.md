---
title: Erstellen eines IoT Hubs mit der Azure-CLI | Microsoft Docs
description: Erstellen Sie eine Azure IoT Hub-Instanz unter Verwendung der Azure-Befehlszeilenschnittstelle.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 78ea9071f220b2a78c6d9260d47145f22284d760
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66166292"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Erstellen eines IoT Hubs mit der Azure-Befehlszeilenschnittstelle

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In diesem Artikel erfahren Sie, wie Sie unter Verwendung der Azure-Befehlszeilenschnittstelle eine IoT Hub-Instanz erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Anleitung wird ein Azure-Abonnement benötigt. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Anmelden und Festlegen Ihres Azure-Kontos

Wenn Sie die Azure-Befehlszeilenschnittstelle lokal ausführen anstatt Cloud Shell zu verwenden, müssen Sie sich bei Ihrem Azure-Konto anmelden.

Führen Sie an der Eingabeaufforderung den [Anmeldebefehl](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) aus:

   ```azurecli
   az login
   ```

Befolgen Sie die Anweisungen zur Authentifizierung mit dem Code, und melden Sie sich in einem Webbrowser bei Ihrem Azure-Konto an.

## <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

Verwenden Sie die Azure CLI zum Erstellen einer Ressourcengruppe, und fügen Sie dann einen IoT Hub hinzu.

1. Wenn Sie einen IoT Hub erstellen, müssen Sie ihn in einer Ressourcengruppe erstellen. Verwenden Sie entweder eine vorhandene Ressourcengruppe, oder führen Sie den folgenden [Befehl zum Erstellen einer Ressourcengruppe](https://docs.microsoft.com/cli/azure/resource) aus:
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > Im vorherigen Beispiel wird die Ressourcengruppe am Standort „USA, Westen“ erstellt. Mit dem folgenden Befehl können Sie eine Liste mit verfügbaren Standorten anzeigen: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Führen Sie den folgenden [Befehl zum Erstellen einer IoT Hub-Instanz](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) in Ihrer Ressourcengruppe aus, und verwenden Sie dabei einen global eindeutigen Namen für Ihre IoT Hub-Instanz:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Mit dem vorherigen Befehl wird ein kostenpflichtiger IoT Hub im S1-Tarif erstellt. Weitere Informationen finden Sie unter [Azure IoT Hub – Preise](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Entfernen eines IoT Hubs

Über die Azure-Befehlszeilenschnittstelle können Sie [eine einzelne Ressource](https://docs.microsoft.com/cli/azure/resource) (beispielsweise eine IoT Hub-Instanz) oder eine Ressourcengruppe mit allen dazugehörigen Ressourcen (einschließlich IoT Hub-Instanzen) löschen.

Führen Sie zum [Löschen einer IoT Hub-Instanz](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete) den folgenden Befehl aus:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Führen Sie zum [Löschen einer Ressourcengruppe und aller dazugehörigen Ressourcen](https://docs.microsoft.com/cli/azure/group#az-group-delete) den folgenden Befehl aus:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung einer IoT Hub-Instanz finden Sie in folgenden Artikeln:

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)
* [Erstellen eines IoT Hubs über das Portal](iot-hub-create-through-portal.md)
