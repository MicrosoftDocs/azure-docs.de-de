---
title: Verwalten benutzerseitig zugewiesener verwalteter Identitäten mithilfe von REST – Azure AD
description: Schrittweise Anweisungen zum Erstellen, Auflisten und Löschen einer vom Benutzer zugewiesenen verwalteten Identität zum Durchführen von REST-API-Aufrufen.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546691"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Erstellen, Auflisten oder Löschen einer vom Benutzer zugewiesenen verwalteten Identität mit REST-API-Aufrufen

Verwaltete Identitäten für Azure-Ressourcen ermöglichen Azure-Diensten, sich bei Diensten zu authentifizieren, die die Azure AD-Authentifizierung unterstützen, ohne dass Sie Anmeldeinformationen in Ihren Code einfügen müssen. 

In diesem Artikel erfahren Sie, wie Sie mithilfe von cURL zum Durchführen von REST-API-Aufrufen eine vom Benutzer zugewiesene verwaltete Identität erstellen, auflisten und löschen können.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#managed-identity-types)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Sie können alle Befehle in diesem Artikel in der Cloud oder lokal ausführen:
    - Verwenden Sie für die Ausführung in der Cloud [Azure Cloud Shell](../../cloud-shell/overview.md).
    - Installieren Sie für die lokale Ausführung [curl](https://curl.haxx.se/download.html) und die [Azure CLI](/cli/azure/install-azure-cli).

## <a name="obtain-a-bearer-access-token"></a>Abrufen eines Bearerzugriffstokens

1. Wenn Sie die Befehle lokal ausführen, melden Sie sich über die Azure CLI bei Azure an:

    ```
    az login
    ```

1. Rufen Sie mit [az account get-access-token](/cli/azure/account#az_account_get_access_token) ein Zugriffstoken ab.

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität 

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität erstellt werden kann.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Anforderungsheader**

|Anforderungsheader  |BESCHREIBUNG  |
|---------|---------|
|*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
|*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        |

**Anforderungstext**

|Name  |BESCHREIBUNG  |
|---------|---------|
|location     | Erforderlich. Ressourcenspeicherort        |

## <a name="list-user-assigned-managed-identities"></a>Auflisten der vom Benutzer zugewiesenen verwalteten Identitäten

Ihrem Konto muss die Rolle [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator) oder [Mitwirkender für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität aufgelistet/gelesen werden kann.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Anforderungsheader  |BESCHREIBUNG  |
|---------|---------|
|*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
|*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Löschen einer vom Benutzer zugewiesenen verwalteten Identität

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität gelöscht werden kann.

> [!NOTE]
> Durch das Löschen einer vom Benutzer zugewiesenen verwalteten Identität wird der Verweis nicht für andere Ressourcen entfernt, denen sie zugewiesen wurde. Informationen zum Entfernen einer vom Benutzer zugewiesenen verwalteten Identität von einem virtuellen Computer mithilfe von cURL finden Sie unter [Entfernen einer vom Benutzer zugewiesenen Identität von einem virtuellen Azure-Computer](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Anforderungsheader  |BESCHREIBUNG  |
|---------|---------|
|*Content-Type*     | Erforderlich. Legen Sie diese Option auf `application/json` fest.        |
|*Autorisierung*     | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer`-Zugriffstoken fest.        |

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einer Azure-VM/-VMSS mithilfe von cURL finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von REST-API-Aufrufen](qs-configure-rest-vm.md#user-assigned-managed-identity) und [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe mithilfe von REST-API-Aufrufen](qs-configure-rest-vmss.md#user-assigned-managed-identity).
