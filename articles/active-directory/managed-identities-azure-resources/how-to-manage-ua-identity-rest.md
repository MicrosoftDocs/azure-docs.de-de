---
title: Verwalten der vom Benutzer zugewiesenen verwalteten Identitäten in Azure mithilfe von REST
description: Ausführliche Anweisungen zum Erstellen, Auflisten und Löschen einer vom Benutzer zugewiesenen verwalteten Identität zum Durchführen von REST-API-Aufrufen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75867242358881c963ab4470bdb7963d0ea4671c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60440180"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Erstellen, Auflisten oder Löschen einer vom Benutzer zugewiesenen verwalteten Identität mithilfe von REST-API-Aufrufen

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Verwaltete Identitäten für Azure-Ressourcen ermöglichen Azure-Diensten, sich bei Diensten mit Unterstützung für die Azure AD-Authentifizierung zu authentifizieren, ohne dass Sie Anmeldeinformationen in Ihren Code einfügen müssen. 

In diesem Artikel erfahren Sie, wie Sie mithilfe von cURL zum Durchführen von REST-API-Aufrufen eine vom Benutzer zugewiesene verwaltete Identität erstellen, auflisten und löschen können.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#how-does-it-work)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Wenn Sie Windows verwenden, installieren Sie das [Windows-Subsystem für Linux](https://msdn.microsoft.com/commandline/wsl/about) oder verwenden Sie [Azure Cloud Shell](../../cloud-shell/overview.md) im Azure-Portal.
- Wenn Sie das [Windows-Subsystem für Linux](https://msdn.microsoft.com/commandline/wsl/about) oder eine [Linux-Distribution](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) verwenden, [installieren Sie die lokale Konsole für die Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).
- Wenn Sie die lokale Konsole für die Azure-Befehlszeilenschnittstelle verwenden, melden Sie sich über `az login` bei Azure mit einem Konto an, das dem Azure-Abonnement zugeordnet ist, in dem Sie vom Benutzer zugewiesene verwaltete Identitäten bereitstellen oder für das Sie Informationen zu diesen abrufen möchten.
- Rufen Sie mit `az account get-access-token` ein Bearerzugriffstoken ab, um die folgenden Vorgänge für vom Benutzer zugewiesene verwaltete Identitäten auszuführen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität 

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität erstellt werden kann.

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

|NAME  |BESCHREIBUNG  |
|---------|---------|
|location     | Erforderlich. Ressourcenspeicherort        |

## <a name="list-user-assigned-managed-identities"></a>Auflisten der vom Benutzer zugewiesenen verwalteten Identitäten

Ihrem Konto muss die Rolle [Operator für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-operator) oder [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität aufgelistet/gelesen werden kann.

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

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität gelöscht werden kann.

> [!NOTE]
> Durch das Löschen einer vom Benutzer zugewiesenen verwalteten Identität wird der Verweis nicht für andere Ressourcen entfernt, denen sie zugewiesen wurde. Informationen zum Entfernen einer vom Benutzer zugewiesenen verwalteten Identität von einem virtuellen Computer mithilfe von cURL finden Sie unter [Entfernen einer vom Benutzer zugewiesenen Identität von einem virtuellen Azure-Computer](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

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
