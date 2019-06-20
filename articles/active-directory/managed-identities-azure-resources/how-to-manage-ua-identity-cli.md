---
title: Verwalten einer vom Benutzer zugewiesenen verwalteten Identität mithilfe der Azure-Befehlszeilenschnittstelle
description: Ausführliche Anweisungen zum Erstellen, Auflisten und Löschen einer vom Benutzer zugewiesenen verwalteten Identität mithilfe der Azure-Befehlszeilenschnittstelle
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
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28520b3ba5d4e62fd4e1c9b78e68cc7dc2b48c61
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60290996"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Erstellen, Auflisten oder Löschen einer vom Benutzer zugewiesenen verwalteten Identität mithilfe der Azure-Befehlszeilenschnittstelle

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität verwenden, um sich bei Diensten mit Unterstützung für die Azure AD-Authentifizierung zu authentifizieren, ohne dass Anmeldeinformationen in Ihren Code eingebettet werden müssen. 

In diesem Artikel erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle eine vom Benutzer zugewiesene verwaltete Identität erstellen, auflisten und löschen.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#how-does-it-work)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Um die CLI-Skriptbeispiele auszuführen, haben Sie drei Möglichkeiten:
    - Verwenden Sie [Azure Cloud Shell](../../cloud-shell/overview.md) aus dem Azure-Portal (siehe nächster Abschnitt).
    - Verwenden Sie die eingebettete Azure Cloud Shell, indem Sie die Schaltfläche „Ausprobieren“ in der oberen rechten Ecke jedes Codeblocks verwenden.
    - [Installieren Sie die neueste Version der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 oder höher), wenn Sie lieber eine lokale CLI-Konsole verwenden möchten. Melden Sie sich mit `az login` mit einem Konto, das mit dem Azure-Abonnement verknüpft ist, unter dem Sie die vom Benutzer zugewiesene verwaltete Identität bereitstellen möchten, bei Azure an.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität 

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität erstellt werden kann.

Verwenden Sie den Befehl [az identity create](/cli/azure/identity#az-identity-create), um eine benutzerseitig zugewiesene verwaltete Identität zu erstellen. Der Parameter `-g` gibt die Ressourcengruppe an, in der die vom Benutzer zugewiesene verwaltete Identität erstellt werden soll, während der Parameter `-n` den Namen angibt. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<USER ASSIGNED IDENTITY NAME>` durch Ihre eigenen Werte:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Auflisten der vom Benutzer zugewiesenen verwalteten Identitäten

Ihrem Konto muss die Rolle [Operator für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-operator) oder [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität aufgelistet/gelesen werden kann.

Verwenden Sie den Befehl [az identity list](/cli/azure/identity#az-identity-list), um vom Benutzer zugewiesene verwaltete Identitäten aufzulisten. Ersetzen Sie `<RESOURCE GROUP>` durch Ihren eigenen Wert:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
In der JSON-Antwort wird bei vom Benutzer zugewiesenen verwalteten Identitäten der Wert `"Microsoft.ManagedIdentity/userAssignedIdentities"` für den Schlüssel `type` zurückgegeben.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Löschen einer vom Benutzer zugewiesenen verwalteten Identität

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität gelöscht werden kann.

Um eine vom Benutzer zugewiesene verwaltete Identität zu löschen, verwenden Sie den Befehl [az identity delete](/cli/azure/identity#az-identity-delete).  Der Parameter „-n“ gibt den Namen an, während der Parameter „-g“ die Ressourcengruppe angibt, in der die vom Benutzer zugewiesene verwaltete Identität erstellt wurde. Ersetzen Sie die Parameterwerte `<USER ASSIGNED IDENTITY NAME>` und `<RESOURCE GROUP>` durch Ihre eigenen Werte:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Durch das Löschen einer vom Benutzer zugewiesenen verwalteten Identität wird der Verweis nicht für andere Ressourcen entfernt, denen sie zugewiesen wurde. Entfernen Sie diese mit dem Befehl `az vm/vmss identity remove` von der VM/VMSS.

## <a name="next-steps"></a>Nächste Schritte

Eine vollständige Liste der Identitätsbefehle für die Azure CLI finden Sie unter [az identity](/cli/azure/identity).

Informationen zum Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einer Azure-VM finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md#user-assigned-managed-identity).


 
