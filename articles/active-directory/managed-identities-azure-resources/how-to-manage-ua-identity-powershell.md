---
title: Erstellen, Auflisten und Löschen einer vom Benutzer zugewiesenen verwalteten Identität mit Azure PowerShell
description: Ausführliche Anweisungen zum Erstellen, Auflisten und Löschen einer vom Benutzer zugewiesenen verwalteten Identität mit Azure PowerShell
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
ms.openlocfilehash: 886b56de194f38fbb4b94f96b92bff11f2288b37
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60293510"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Erstellen, Auflisten oder Löschen einer vom Benutzer zugewiesenen verwalteten Identität mit Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität verwenden, um sich bei Diensten mit Unterstützung für die Azure AD-Authentifizierung zu authentifizieren, ohne dass Anmeldeinformationen in Ihren Code eingebettet werden müssen. 

In diesem Artikel erfahren Sie, wie Sie mit Azure PowerShell eine vom Benutzer zugewiesene verwaltete Identität erstellen, auflisten und löschen.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#how-does-it-work)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Installieren Sie [die aktuelle Version von Azure PowerShell](/powershell/azure/install-az-ps), sofern noch nicht geschehen.
- Wenn Sie PowerShell lokal ausführen, ist außerdem Folgendes erforderlich: 
    - Führen Sie zum Starten `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.
    - Installieren Sie die [neueste Version von PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Führen Sie `Install-Module -Name PowerShellGet -AllowPrerelease` aus, um die Vorabversion des `PowerShellGet`-Moduls abzurufen (möglicherweise müssen Sie `Exit` in der aktuellen PowerShell-Sitzung ausführen, nachdem Sie diesen Befehl zum Installieren des `Az.ManagedServiceIdentity`-Moduls ausgeführt haben).
    - Führen Sie `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` aus, um die Vorabversion des `Az.ManagedServiceIdentity`-Moduls zu installieren und die Vorgänge für vom Benutzer zugewiesene verwaltete Identitäten in diesem Artikel auszuführen.

## <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität erstellt werden kann.

Um eine vom Benutzer zugewiesene verwaltete Identität zu erstellen, verwenden Sie den Befehl `New-AzUserAssignedIdentity`. Der Parameter `ResourceGroupName` gibt die Ressourcengruppe an, in der die vom Benutzer zugewiesene verwaltete Identität erstellt werden soll, während der Parameter `-Name` den Namen angibt. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<USER ASSIGNED IDENTITY NAME>` durch Ihre eigenen Werte:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Auflisten der vom Benutzer zugewiesenen verwalteten Identitäten

Ihrem Konto muss die Rolle [Operator für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-operator) oder [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität aufgelistet/gelesen werden kann.

Um die vom Benutzer zugewiesenen verwalteten Identitäten aufzulisten, verwenden Sie den Befehl [Get-AzUserAssigned].  Der Parameter `-ResourceGroupName` gibt die Ressourcengruppe an, in der die vom Benutzer zugewiesene verwaltete Identität erstellt wurde. Ersetzen Sie `<RESOURCE GROUP>` durch Ihren eigenen Wert:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
In der Antwort wird bei vom Benutzer zugewiesenen verwalteten Identitäten der Wert `"Microsoft.ManagedIdentity/userAssignedIdentities"` für den Schlüssel `Type` zurückgegeben.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Löschen einer vom Benutzer zugewiesenen verwalteten Identität

Ihrem Konto muss die Rolle [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) zugewiesen sein, damit eine benutzerseitig zugewiesene verwaltete Identität gelöscht werden kann.

Um eine vom Benutzer zugewiesene verwaltete Identität zu löschen, verwenden Sie den Befehl `Remove-AzUserAssignedIdentity`.  Der Parameter `-ResourceGroupName` gibt die Ressourcengruppe an, in der die vom Benutzer zugewiesene Identität erstellt wurde, und der Parameter `-Name` gibt den Namen an. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<USER ASSIGNED IDENTITY NAME>` durch Ihre eigenen Werte:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Durch das Löschen einer vom Benutzer zugewiesenen verwalteten Identität wird der Verweis nicht für andere Ressourcen entfernt, denen sie zugewiesen wurde. Zuweisungen von Identitäten müssen separat entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

Eine vollständige Liste der Azure PowerShell-Befehle für verwaltete Identitäten für Azure-Ressourcen und weitere Details hierzu finden Sie unter [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
