---
title: include file
description: include file
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: d81b6f5367efa92c9249956faa058441edf98561
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755883"
---
### <a name="create-a-user-assigned-identity"></a>Erstellen einer benutzerseitig zugewiesenen Identität

Erstellen Sie mithilfe des Befehls [az identity create][az-identity-create] eine Identität namens *myACRTasksId* in Ihrem Abonnement. Sie können entweder dieselbe Ressourcengruppe verwenden, die Sie zuvor zum Erstellen einer Containerregistrierung verwendet haben, oder Sie verwenden eine andere Ressourcengruppe.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Um die dem Benutzer zugewiesene Identität in den folgenden Schritten zu konfigurieren, verwenden Sie den Befehl [az identity show][az-identity-show], um die Ressourcen-ID, die Prinzipal-ID und die Client-ID der Identität in Variablen zu speichern.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
