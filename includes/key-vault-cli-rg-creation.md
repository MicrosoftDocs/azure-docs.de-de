---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070260"
---
Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Verwenden Sie den Befehl [az group create](/cli/azure/group#az_group_create), um eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* zu erstellen.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
