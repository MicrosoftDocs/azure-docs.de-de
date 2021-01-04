---
title: Verwalten der von Benutzern zugewiesenen verwalteten Identität – Azure CLI – Azure AD
description: Ausführliche Anweisungen zum Erstellen, Auflisten und Löschen einer vom Benutzer zugewiesenen verwalteten Identität mit der Azure-Befehlszeilenschnittstelle.
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
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: edb326c889a76eedd82c8918c705bdb5bbe5d195
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893607"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Erstellen, Auflisten oder Löschen einer vom Benutzer zugewiesenen verwalteten Identität mithilfe der Azure-Befehlszeilenschnittstelle


Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität verwenden, um sich bei Diensten mit Unterstützung für die Azure AD-Authentifizierung zu authentifizieren, ohne dass Anmeldeinformationen in Ihren Code eingebettet werden müssen. 

In diesem Artikel erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle eine vom Benutzer zugewiesene verwaltete Identität erstellen, auflisten und löschen.

Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie mit verwalteten Identitäten für Azure-Ressourcen noch nicht vertraut sind, lesen Sie [Was sind verwaltete Identitäten für Azure-Ressourcen?](overview.md). Informationen zu systemseitig zugewiesenen und benutzerseitig zugewiesenen Typen verwalteter Identitäten finden Sie unter [Arten von verwalteten Identitäten](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> Um Benutzerberechtigungen bei Verwendung eines App Service-Prinzipals mithilfe der Befehlszeilenschnittstelle zu ändern, müssen Sie dem Dienstprinzipal zusätzliche Berechtigungen in der Azure AD Graph-API zuweisen, da Teile der GET-Anforderungen in der Befehlszeilenschnittstelle für die Graph-API ausgeführt werden. Andernfalls erhalten Sie möglicherweise eine Meldung mit einem Hinweis auf unzureichende Berechtigungen zum Durchführen des Vorgangs. Dazu müssen Sie die App-Registrierung in Azure Active Directory aufrufen, Ihre App auswählen, auf „API-Berechtigungen“ klicken, nach unten scrollen und „Azure Active Directory Graph“ auswählen. Wählen Sie anschließend „Anwendungsberechtigungen“ aus, und fügen Sie dann die entsprechenden Berechtigungen hinzu. 

## <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität 

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität erstellt werden kann.

Verwenden Sie den Befehl [az identity create](/cli/azure/identity#az-identity-create), um eine benutzerseitig zugewiesene verwaltete Identität zu erstellen. Der Parameter `-g` gibt die Ressourcengruppe an, in der die vom Benutzer zugewiesene verwaltete Identität erstellt werden soll, während der Parameter `-n` den Namen angibt. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<USER ASSIGNED IDENTITY NAME>` durch Ihre eigenen Werte:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Auflisten der vom Benutzer zugewiesenen verwalteten Identitäten

Ihrem Konto muss die Rolle [Operator für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-operator) oder [Mitwirkender für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität aufgelistet/gelesen werden kann.

Verwenden Sie den Befehl [az identity list](/cli/azure/identity#az-identity-list), um vom Benutzer zugewiesene verwaltete Identitäten aufzulisten. Ersetzen Sie `<RESOURCE GROUP>` durch Ihren eigenen Wert:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

In der JSON-Antwort wird bei vom Benutzer zugewiesenen verwalteten Identitäten der Wert `"Microsoft.ManagedIdentity/userAssignedIdentities"` für den Schlüssel `type` zurückgegeben.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Löschen einer vom Benutzer zugewiesenen verwalteten Identität

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität gelöscht werden kann.

Um eine vom Benutzer zugewiesene verwaltete Identität zu löschen, verwenden Sie den Befehl [az identity delete](/cli/azure/identity#az-identity-delete).  Der Parameter „-n“ gibt den Namen an, während der Parameter „-g“ die Ressourcengruppe angibt, in der die vom Benutzer zugewiesene verwaltete Identität erstellt wurde. Ersetzen Sie die Parameterwerte `<USER ASSIGNED IDENTITY NAME>` und `<RESOURCE GROUP>` durch Ihre eigenen Werte:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Durch das Löschen einer vom Benutzer zugewiesenen verwalteten Identität wird der Verweis nicht für andere Ressourcen entfernt, denen sie zugewiesen wurde. Entfernen Sie diese mit dem Befehl `az vm/vmss identity remove` von der VM/VMSS.

## <a name="next-steps"></a>Nächste Schritte

Eine vollständige Liste der Identitätsbefehle für die Azure CLI finden Sie unter [az identity](/cli/azure/identity).

Informationen zum Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einer Azure-VM finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle](qs-configure-cli-windows-vm.md#user-assigned-managed-identity).
