---
title: Anzeigen der Zuweisungen benutzerdefinierter Rollen im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Sie können jetzt Mitglieder einer Azure Active Directory-Administratorrolle im Azure Active Directory Admin Center anzeigen und verwalten.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8546c2b50c78c7a24d07c75d2d730fd2566e8ba0
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862087"
---
# <a name="view-custom-role-assignments-using-azure-active-directory"></a>Anzeigen der Zuweisungen benutzerdefinierter Rollen mit Azure Active Directory

In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Rollen anzeigen, die Sie in Azure Active Directory (Azure AD) zugewiesen haben. In Azure Active Directory (Azure AD) können Rollen im organisationsweiten Bereich oder mit einem Einzelanwendungsbereich zugewiesen werden.

- Rollenzuweisungen im organisationsweiten Bereich werden der Liste der Rollenzuweisungen einzelner Anwendungen hinzugefügt und werden dort angezeigt.
- Rollenzuweisungen im Einzelanwendungsbereich werden der Liste der Zuweisungen im organisationsweiten Bereich nicht hinzugefügt und dort nicht angezeigt.

## <a name="view-role-assignments-in-the-azure-portal"></a>Verwalten von Rollenzuweisungen im Azure-Portal

In diesem Verfahren wird das Anzeigen von Zuweisungen einer Rolle mit einem organisationsweiten Bereich beschrieben.

1. Melden Sie sich bei [Azure AD Admin Center](https://aad.portal.azure.com) mit Berechtigungen vom Typ „Administrator für privilegierte Rollen“ oder „Globaler Administrator“ in der Azure AD-Organisation an.
1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** und anschließend eine Rolle aus, um sie zu öffnen und ihre Eigenschaften anzuzeigen.
1. Wählen Sie **Zuweisungen** aus, um die Zuweisungen für die Rolle anzuzeigen.

    ![Anzeigen von Rollenzuweisungen und Berechtigungen beim Öffnen einer Rolle in der Liste](./media/view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Anzeigen von Rollenzuweisungen mithilfe von Azure AD PowerShell

In diesem Abschnitt wird das Anzeigen von Zuweisungen einer Rolle mit einem organisationsweiten Bereich beschrieben. Dieser Artikel verwendet das [Azure Active Directory PowerShell Version 2](/powershell/module/azuread/#directory_roles)-Modul. Zum Anzeigen von Zuweisungen im Einzelanwendungsbereich mithilfe von PowerShell können Sie die Cmdlets in [Zuweisen benutzerdefinierter Rollen mit Ressourcengeltungsbereich unter Verwendung von PowerShell in Azure Active Directory](custom-assign-powershell.md) verwenden.

### <a name="prepare-powershell"></a>Vorbereiten von PowerShell

Zunächst müssen Sie das [Azure AD PowerShell-Vorschaumodul herunterladen](https://www.powershellgallery.com/packages/AzureAD/).

Verwenden Sie die folgenden Befehle, um das Azure AD PowerShell-Modul zu installieren:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Überprüfen Sie mithilfe des folgenden Befehls, ob das Modul verwendet werden kann:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Anzeigen der Zuweisungen einer Rolle

Beispiel für das Anzeigen der Zuweisungen einer Rolle:

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Anzeigen von Rollenzuweisungen mithilfe der Microsoft Graph-API

In diesem Abschnitt wird das Anzeigen von Zuweisungen einer Rolle mit einem organisationsweiten Bereich beschrieben.  Zum Anzeigen von Zuweisungen im Einzelanwendungsbereich mithilfe der Graph-API können Sie die Vorgänge in [Zuweisen von benutzerdefinierten Administratorrollen mithilfe der Graph-API in Azure Active Directory](custom-assign-graph.md) verwenden.

HTTP-Anforderung zum Abrufen einer Rollenzuweisung für eine bestimmte Rollendefinition

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Antwort

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Anzeigen von Zuweisungen im Einzelanwendungsbereich

In diesem Abschnitt wird das Anzeigen von Zuweisungen einer Rolle mit dem Einzelanwendungsbereich beschrieben. Dieses Feature ist zurzeit als öffentliche Preview verfügbar.

1. Melden Sie sich bei [Azure AD Admin Center](https://aad.portal.azure.com) mit Berechtigungen vom Typ „Administrator für privilegierte Rollen“ oder „Globaler Administrator“ in der Azure AD-Organisation an.
1. Wählen Sie **App-Registrierungen** und dann die gewünschte App-Registrierung aus, um ihre Eigenschaften anzuzeigen. Möglicherweise müssen Sie die Option **Alle Anwendungen** auswählen, um die vollständige Liste der App-Registrierungen in ihrer Azure AD-Organisation anzuzeigen.

    ![Erstellen oder Bearbeiten von App-Registrierungen auf der Seite „App-Registrierungen“](./media/view-assignments/app-reg-all-apps.png)

1. Wählen Sie in der App-Registrierung **Rollen und Administratoren** und anschließend eine Rolle aus, um ihre Eigenschaften anzuzeigen.

    ![Anzeigen der Rollenzuweisungen einer App-Registrierung auf der Seite „App-Registrierungen“](./media/view-assignments/app-reg-assignments.png)

1. Wählen Sie **Zuweisungen** aus, um die Zuweisungen für die Rolle anzuzeigen. Wenn Sie die Zuweisungenansicht in der App-Registrierung öffnen, werden Ihnen die Zuweisungen angezeigt, die auf diese Azure AD-Ressource beschränkt sind.

    ![Anzeigen der Rollenzuweisungen einer App-Registrierung über die Eigenschaften einer App-Registrierung](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Nächste Schritte

* Im [Forum für Azure AD-Administratorrollen](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) können Sie sich gerne mit uns in Verbindung setzen.
* Weitere Informationen zu Rollen und zur Zuweisung von Administratorrollen finden Sie unter [Zuweisen von Administratorrollen](permissions-reference.md).
* Informationen zu Standardbenutzerberechtigungen finden Sie unter [Vergleich von Standardbenutzerberechtigungen für Gäste und Mitglieder](../fundamentals/users-default-permissions.md).
