---
title: include file
description: include file
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 173c9156f253e43111299b53287e97ab7b2c0aa5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746946"
---
## <a name="create-azure-container-registry"></a>Erstellen einer Azure-Containerregistrierung

Bevor Sie Ihre Containerregistrierung erstellen können, benötigen Sie eine *Ressourcengruppe* für die Bereitstellung. Eine Azure-Ressourcengruppe ist eine logische Sammlung, in der alle Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mithilfe des Befehls [az group create][az-group-create] eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *eastus* erstellt:

```azurecli
az group create --name myResourceGroup --location eastus
```

Erstellen Sie nach der Erstellung der Ressourcengruppe mit dem Befehl [az acr create][az-acr-create] eine Azure-Containerregistrierung. Der Containerregistrierungsname muss innerhalb von Azure eindeutig sein und zwischen 5 und 50 alphanumerische Zeichen enthalten. Ersetzen Sie `<acrName>` durch einen eindeutigen Namen für die Registrierung:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Hier ist ein Teil einer Ausgabe für eine neue Azure-Containerregistrierung mit dem Namen *mycontainerregistry082* angegeben:

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Im restlichen Teil dieses Tutorials wird `<acrName>` als Platzhalter für den von Ihnen in diesem Schritt gewählten Namen der Containerregistrierung bezeichnet.

## <a name="log-in-to-container-registry"></a>Anmelden an der Containerregistrierung

Sie müssen sich bei der Azure-Containerregistrierung anmelden, damit Sie Images mithilfe von Push an sie übertragen können. Verwenden Sie den Befehl [az acr login][az-acr-login], um den Vorgang abzuschließen. Geben Sie den eindeutigen Namen an, den Sie beim Erstellen der Containerregistrierung gewählt haben.

```azurecli
az acr login --name <acrName>
```

Beispiel:

```azurecli
az acr login --name mycontainerregistry082
```

Der Befehl gibt nach Abschluss des Vorgangs `Login Succeeded` zurück:

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
