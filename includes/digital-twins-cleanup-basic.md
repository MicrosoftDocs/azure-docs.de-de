---
author: baanders
description: Includedatei zum Bereinigen einer Azure Digital Twins-Instanz
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575682"
---
* **Falls Sie die in diesem Tutorial erstellten Ressourcen nicht benötigen**, können Sie mit dem Befehl [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) die Azure Digital Twins-Instanz und alle anderen Ressourcen aus diesem Artikel löschen. Dadurch werden alle Azure-Ressourcen in einer Ressourcengruppe sowie die Ressourcengruppe selbst gelöscht.
    
    > [!IMPORTANT]
    > Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen.
    
    Öffnen Sie [Azure Cloud Shell](https://shell.azure.com), und führen Sie den folgenden Befehl aus, um die Ressourcengruppe und alle darin enthaltenen Elemente zu löschen.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```