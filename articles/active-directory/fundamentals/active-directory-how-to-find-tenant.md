---
title: Ermitteln Ihrer Mandanten-ID – Azure Active Directory
description: Anweisungen zum Ermitteln einer Azure Active Directory-Mandanten-ID für ein vorhandenes Azure-Abonnement
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910e68e5b8933875f252faf65fb58e514dee8254
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326589"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Ermitteln Ihrer Azure Active Directory-Mandanten-ID

Azure-Abonnements weisen eine Vertrauensstellung mit Azure Active Directory (Azure AD) auf. Azure AD gilt als vertrauenswürdig für die Authentifizierung von Benutzern, Diensten und Geräten für das Abonnement. Jedem Abonnement ist eine Mandanten-ID zugeordnet. Es gibt verschiedene Möglichkeiten, die Mandanten-ID für Ihr Abonnement zu ermitteln.

## <a name="find-tenant-id-through-the-azure-portal"></a>Ermitteln der Mandanten-ID über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
 
1. Wählen Sie **Azure Active Directory** aus.

1. Wählen Sie **Eigenschaften** aus.

1. Scrollen Sie dann nach unten zum Feld **Mandanten-ID**. Ihre Mandanten-ID wird in diesem Feld angezeigt.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory – Eigenschaften – Mandanten-ID – Feld „Mandanten-ID“":::

## <a name="find-tenant-id-with-powershell"></a>Ermitteln der Mandanten-ID mit PowerShell

Sie können den Mandanten auch programmgesteuert suchen. Um die Mandanten-ID mit Azure PowerShell zu ermitteln, verwenden Sie das Cmdlet `Get-AzTenant`.

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Weitere Informationen finden Sie in der Azure PowerShell-Referenz zum Cmdlet [Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Ermitteln der Mandanten-ID über eine Befehlszeilenschnittstelle
Wenn Sie die Mandanten-ID über eine Befehlszeilenschnittstelle ermitteln möchten, können Sie dazu die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) oder die [Microsoft 365-Befehlszeilenschnittstelle](https://pnp.github.io/cli-microsoft365/) verwenden. 

Führen Sie bei der Azure-Befehlszeilenschnittstelle einen der Befehle **az login**, **az account list** oder **az account tenant list** wie im folgenden Beispiel gezeigt aus. Beachten Sie die **tenantId**-Eigenschaft für jedes Ihrer Abonnements in der Ausgabe der einzelnen Befehle.

```azurecli-interactive
az login
az account list
az account tenant list
```

Weitere Informationen finden Sie in der jeweiligen Referenz zu den Befehlen [az login](/cli/azure/reference-index#az_login), [az account](/cli/azure/ext/account/account) und [az account tenant](/cli/azure/ext/account/account/tenant).


Verwenden Sie bei der Microsoft 365-Befehlszeilenschnittstelle das Cmdlet **tenant id** wie im folgenden Beispiel gezeigt:
 
```cli
m365 tenant id get
```

Weitere Informationen finden Sie in der Microsoft 365-Referenz zum Befehl [tenant id get](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/).


## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen eines neuen Azure AD-Mandanten finden Sie unter [Schnellstart: Erstellen eines neuen Mandanten in Azure Active Directory](active-directory-access-create-new-tenant.md).

- Informationen zum Zuordnen oder Hinzufügen eines Abonnements zu einem Mandanten finden Sie unter [Zuordnen oder Hinzufügen eines Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten](active-directory-how-subscriptions-associated-directory.md).

- Informationen zum Ermitteln der Objekt-ID finden Sie unter [Suchen der Benutzerobjekt-ID](/partner-center/find-ids-and-domain-names#find-the-user-object-id).