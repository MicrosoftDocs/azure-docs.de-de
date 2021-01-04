---
title: Mandantenübergreifendes Freigeben von Katalogimages
description: Erfahren Sie, wie Sie Skalierungsgruppen mithilfe von Images erstellen, die von Azure-Mandanten mithilfe von Shared Image Gallerys freigegeben wurden.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: ea61b3bd76fc4ada48a8a2fb734a841b8a969272
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323480"
---
# <a name="share-images-across-tenants-with-shared-image-gallery"></a>Freigeben von Images für Mandanten über eine Shared Image Gallery

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Erstellen einer Skalierungsgruppe mit der Azure-Befehlszeilenschnittstelle

Melden Sie den Dienstprinzipal für den Mandanten 1 mit der App-ID, dem App-Schlüssel und der ID des Mandanten 1 an. Sie können bei Bedarf `az account show --query "tenantId"` verwenden, um die Mandanten-IDs abzurufen.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Melden Sie den Dienstprinzipal für den Mandanten 2 mit der App-ID, dem App-Schlüssel und der ID des Mandanten 2 an:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Erstellen Sie die Skalierungsgruppe. Ersetzen Sie die Informationen im Beispiel durch Ihre eigenen.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Nächste Schritte

Sollten Probleme auftreten, finden Sie unter [Problembehandlung für Kataloge mit freigegebenen Images](../virtual-machines/troubleshooting-shared-images.md) mögliche Problembehandlungen.
