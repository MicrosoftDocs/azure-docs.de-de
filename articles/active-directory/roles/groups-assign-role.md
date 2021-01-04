---
title: Zuweisen einer Rolle zu einer Cloudgruppe in Azure Active Directory | Microsoft-Dokumentation
description: Weisen Sie einer Gruppe mit Rollenzuweisung im Azure-Portal, mit PowerShell oder über die Graph-API eine Azure AD-Rolle zu.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb81b5dc98bf75a17e55abafdebfbeca68774a99
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379144"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Zuweisen einer Rolle zu einer Cloudgruppe in Azure Active Directory

In diesem Abschnitt wird beschrieben, wie ein IT-Administrator einer Azure AD-Gruppe (Azure Active Directory) eine Azure AD-Rolle zuweisen kann.

## <a name="using-azure-ad-admin-center"></a>Mithilfe von Azure AD Admin Center

Das Zuweisen einer Gruppe zu einer Azure AD-Rolle ähnelt der Zuweisung von Benutzern und Dienstprinzipalen mit der Ausnahme, dass nur Gruppen verwendet werden können, denen Rollen zugewiesen werden können. Im Azure-Portal werden nur Gruppen angezeigt, denen Rollen zugewiesen werden können.

1. Melden Sie sich bei [Azure AD Admin Center](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit Berechtigungen vom Typ „Administrator für privilegierte Rollen“ oder „Globaler Administrator“ in der Azure AD-Organisation an.

1. Klicken Sie auf **Azure Active Directory** > **Rollen und Administratoren** , und wählen Sie die Rolle aus, die Sie zuweisen möchten.

1. Wählen Sie auf der Seite **_Rollenname_*die Option* Zuweisung hinzufügen** aus.

   ![Hinzufügen der neuen Rollenzuweisung](./media/groups-assign-role/add-assignment.png)

1. Wählen Sie die Gruppe aus. Es werden nur die Gruppen angezeigt, die zu Azure AD-Rollen hinzugefügt werden können.

    [![Es werden nur die Gruppen für eine neue Rollenzuweisung angezeigt, die zugewiesen werden können.](./media/groups-assign-role/eligible-groups.png "Es werden nur die Gruppen für eine neue Rollenzuweisung angezeigt, die zugewiesen werden können.")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. Wählen Sie **Hinzufügen**.

Weitere Informationen zum Zuweisen von Rollenberechtigungen finden Sie unter [Zuweisen von Administrator- und anderen Rollen zu Benutzern mithilfe von Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="using-powershell"></a>PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Erstellen einer Gruppe, der die Rolle zugewiesen werden kann

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>Abrufen der Rollendefinition für die Rolle, die Sie zuweisen möchten

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Erstellen einer Rollenzuweisung

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Verwenden der Microsoft Graph-API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Erstellen einer Gruppe, die einer Azure AD-Rolle zugewiesen werden kann

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Abrufen der Rollendefinition

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Erstellen der Rollenzuweisung

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Cloudgruppen zum Verwalten von Rollenzuweisungen](groups-concept.md)
- [Problembehandlung bei Rollen, die Cloudgruppen zugewiesen sind](groups-faq-troubleshooting.md)
