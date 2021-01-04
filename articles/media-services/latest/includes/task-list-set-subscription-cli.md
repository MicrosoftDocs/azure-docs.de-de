---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 94df457cc93a059de3f9bb294bd1c568b095781c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94884710"
---
<!-- List and set subscriptions -->

1. Rufen Sie eine Liste Ihrer Abonnements mithilfe des Befehls [az account list](/cli/azure/account#az-account-list) ab.

    ```
    az account list --output table
    ```

2. Verwenden Sie `az account set` mit der ID oder dem Namen des Abonnements, zu dem Sie wechseln möchten.

    ```
    az account set --subscription "My Demos"
    ```
