---
title: Mandantenübergreifendes Freigeben von Katalogimages
description: Erfahren Sie, wie Sie VM-Images mithilfe von Katalogen mit freigegebenen Images und der Azure-Befehlszeilenschnittstelle über Azure-Mandanten hinweg freigeben.
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: f0192a6eb60c72c9f7ac605db492f4e5f97b76ed
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779259"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-the-azure-cli"></a>Freigeben von Katalog-VM-Images über Azure-Mandanten hinweg mithilfe der Azure-Befehlszeilenschnittstelle

Mithilfe von Katalogen mit freigegebenen Images können Sie Images über Azure RBAC freigeben. Sie können Azure RBAC verwenden, um Images innerhalb Ihres Mandanten und sogar für Personen außerhalb Ihres Mandanten freizugeben. Weitere Informationen zu dieser einfachen Freigabeoption finden Sie unter [Freigeben des Katalogs](./shared-images-portal.md#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> Sie können das Portal nicht verwenden, um einen virtuellen Computer aus einem Image in einem anderen Azure-Mandanten bereitzustellen. Zum Erstellen eines virtuellen Computers aus einem von Mandanten gemeinsam verwendeten Image müssen Sie die Azure CLI oder [PowerShell](../windows/share-images-across-tenants.md) verwenden.

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

Sollten Probleme auftreten, finden Sie unter [Problembehandlung für Kataloge mit freigegebenen Images](../troubleshooting-shared-images.md) mögliche Problembehandlungen.
