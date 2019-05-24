---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 889b9c0cf944085f5f42ece892d5cac747a27240
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132159"
---
## <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Functions verwendet ein allgemeines Konto in Azure Storage, um den Zustand und andere Informationen über Ihre Funktionen zu verwalten. Erstellen Sie mit dem Befehl [az storage account create](/cli/azure/storage/account) ein allgemeines Storage-Konto in der erstellten Ressourcengruppe.

Ersetzen Sie im folgenden Befehl den Platzhalter `<storage_name>` durch einen eindeutigen Speicherkontonamen. Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Nach dem Erstellen des Speicherkontos zeigt die Azure-CLI ähnliche Informationen wie im folgenden Beispiel an:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```
