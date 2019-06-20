---
title: Mandantenübergreifendes Freigeben von Katalogimages in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie VM-Images mithilfe von Katalogen mit freigegebenen Images über Azure-Mandanten hinweg freigeben.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 1578ba840c6dca93feb68754863439811d7ef099
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65157768"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Freigeben von Katalog-VM-Images über Azure-Mandanten hinweg

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-vm-using-azure-cli"></a>Erstellen eines virtuellen Computers mit der Azure-Befehlszeilenschnittstelle

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

Erstellen Sie den virtuellen Computer. Ersetzen Sie die Informationen im Beispiel durch Ihre eigenen.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Nächste Schritte

Sollten Probleme auftreten, finden Sie unter [Problembehandlung für Kataloge mit freigegebenen Images](troubleshooting-shared-images.md) mögliche Problembehandlungen.