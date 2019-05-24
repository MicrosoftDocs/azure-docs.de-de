---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c20f86fe7fdcfc7ecc940923a8c98fa1fbf4cf65
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132204"
---
## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen wie Funktionen-Apps, Datenbanken und Speicherkonten bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe namens `myResourceGroup` erstellt.  
Falls Sie nicht Cloud Shell verwenden, melden Sie sich erst mithilfe von `az login` an.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Im Allgemeinen erstellen Sie Ressourcengruppen und Ressourcen in einer Region in Ihrer Nähe. Um alle unterstützten Standorte für App Service-Pläne anzuzeigen, führen Sie den Befehl [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations) aus.
