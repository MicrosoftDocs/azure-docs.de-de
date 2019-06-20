---
title: Zuweisen des Zugriffs auf eine Azure-Ressource für eine verwaltete Identität mithilfe der Azure CLI
description: Dieser Artikel enthält Schritt-für-Schritt-Anleitungen für das Zuweisen einer verwalteten Identität für eine Ressource und für das Zuweisen des Zugriffs auf eine andere Ressource mithilfe der Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fb16b1762f1e5330cf058c37a6b7e0f008eb447
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60290977"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Zuweisen des Zugriffs auf eine Ressource für eine verwaltete Identität mithilfe der Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Nachdem Sie eine Azure-Ressource mit einer verwalteten Identität konfiguriert haben, können Sie der verwalteten Identität genau wie bei allen anderen Sicherheitsprinzipalen Zugriff auf eine andere Ressource erteilen. In diesem Beispiel wird gezeigt, wie Sie der verwalteten Identität eines virtuellen Azure-Computers oder einer VM-Skalierungsgruppe mit der Azure CLI den Zugriff auf ein Azure-Speicherkonto gewähren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#how-does-it-work)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Um die CLI-Skriptbeispiele auszuführen, haben Sie drei Möglichkeiten:
    - Verwenden Sie [Azure Cloud Shell](../../cloud-shell/overview.md) aus dem Azure-Portal (siehe nächster Abschnitt).
    - Verwenden Sie die eingebettete Azure Cloud Shell, indem Sie die Schaltfläche „Ausprobieren“ in der oberen rechten Ecke jedes Codeblocks verwenden.
    - [Installieren Sie die aktuelle Version der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), wenn Sie lieber eine lokale CLI-Konsole verwenden möchten. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Verwenden von RBAC zum Zuweisen des Zugriffs einer verwalteten Identität auf eine andere Ressource

Gehen Sie wie folgt vor, nachdem Sie die verwaltete Identität auf einer Azure-Ressource aktiviert haben, z.B. einem [virtuellen Azure-Computer](qs-configure-cli-windows-vm.md) oder einer [Azure-VM-Skalierungsgruppe](qs-configure-cli-windows-vmss.md): 

1. Melden Sie sich bei Verwendung der Azure CLI in einer lokalen Konsole zunächst mit [az login](/cli/azure/reference-index#az-login) bei Azure an. Verwenden Sie ein Konto, das dem Azure-Abonnement zugeordnet ist, unter dem Sie die VM oder VM-Skalierungsgruppe bereitstellen möchten:

   ```azurecli-interactive
   az login
   ```

2. In diesem Beispiel wird einem virtuellen Azure-Computer der Zugriff auf ein Speicherkonto gewährt. Zunächst verwenden wir [az resource list](/cli/azure/resource/#az-resource-list), um den Dienstprinzipal für den virtuellen Computer mit dem Namen „myVM“ abzurufen:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Für eine Azure-VM-Skalierungsgruppe ist der Befehl identisch, aber Sie rufen jetzt den Dienstprinzipal für die VM-Skalierungsgruppe „DevTestVMSS“ ab:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Nachdem Sie die Dienstprinzipal-ID beschafft haben, können Sie [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) verwenden, um dem virtuellen Computer oder der VM-Skalierungsgruppe Lesezugriff („Reader“) auf das Speicherkonto „myStorageAcct“ zu gewähren:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](overview.md)
- Informationen zum Aktivieren einer verwalteten Identität auf einem virtuellen Azure-Computer finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md).
- Informationen zum Aktivieren einer verwalteten Identität in einer Azure-VM-Skalierungsgruppe finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe mithilfe der Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vmss.md).
