---
title: Zuweisen von benutzerdefinierten Rollen mit Azure PowerShell – Azure AD | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Mitglieder einer benutzerdefinierten Azure AD-Administratorrolle mithilfe von Azure PowerShell verwalten.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4695d0844ef8b707edce53a05de611c91223a46
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861951"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Zuweisen benutzerdefinierter Rollen mit Ressourcengeltungsbereich unter Verwendung von PowerShell in Azure Active Directory

In diesem Artikel erfahren Sie, wie Sie eine Rollenzuweisung mit organisationsweitem Geltungsbereich in Azure Active Directory (Azure AD) erstellen. Durch Zuweisen einer Rolle mit organisationsweitem Geltungsbereich wird Zugriff innerhalb der gesamten Azure AD-Organisation gewährt. Informationen zum Erstellen einer Rollenzuweisung mit einem Geltungsbereich für eine einzelne Azure AD-Ressource finden Sie unter [Create a custom role and assign at resource scope in Azure Active Directory](custom-create.md) (Erstellen einer benutzerdefinierten Rolle und Zuweisen der Rolle im Ressourcengeltungsbereich in Azure Active Directory). In diesem Artikel wird das Modul [Azure Active Directory PowerShell Version 2](/powershell/module/azuread/#directory_roles) verwendet.

Weitere Informationen zu Azure AD-Administratorrollen finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](permissions-reference.md).

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Stellen Sie eine Verbindung mit Ihrer Azure AD-Organisation mithilfe eines globalen Administratorkontos her, um Rollen zuzuweisen oder zu entfernen.

## <a name="prepare-powershell"></a>Vorbereiten von PowerShell

Installieren Sie das Azure AD PowerShell-Modul aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importieren Sie anschließend das Azure AD PowerShell-Vorschaumodul mithilfe des folgenden Befehls:

``` PowerShell
import-module azureadpreview
```

Gleichen Sie die Version, die durch den folgenden Befehl zurückgegeben wird, mit der hier aufgeführten Version ab, um sich zu vergewissern, dass das Modul verwendungsbereit ist:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Sie können die Cmdlets jetzt im Modul verwenden. Eine ausführliche Beschreibung der Cmdlets im Azure AD-Modul finden Sie in der Onlinereferenzdokumentation für das [Azure AD-Vorschaumodul](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Zuweisen einer Rolle zu einem Benutzer oder Dienstprinzipal mit Ressourcengeltungsbereich

1. Öffnen Sie das Azure AD PowerShell-Vorschaumodul.
1. Führen Sie den Befehl `Connect-AzureAD` aus, um sich anzumelden.
1. Erstellen Sie mithilfe des folgenden PowerShell-Skripts eine neue Rolle:

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Falls Sie die Rolle keinem Benutzer, sondern einem Dienstprinzipal zuweisen möchten, verwenden Sie das Cmdlet [Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal).

## <a name="operations-on-roledefinition"></a>Vorgänge für „RoleDefinition“

Rollendefinitionsobjekte enthalten die Definition der integrierten oder benutzerdefinierten Rolle sowie die Berechtigungen, die durch diese Rollenzuweisung erteilt werden. Diese Ressource zeigt sowohl benutzerdefinierte Rollendefinitionen als auch integrierte Verzeichnisrollen (in einem „roleDefinition“ entsprechenden Format) an. Für eine Azure AD-Organisation können aktuell maximal 30 individuelle benutzerdefinierte Rollendefinitionen definiert werden.

### <a name="create-operations-on-roledefinition"></a>Erstellungsvorgänge für „RoleDefinition“

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Lesevorgänge für „RoleDefinition“

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Aktualisierungsvorgänge für „RoleDefinition“

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Löschvorgänge für „RoleDefinition“

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Vorgänge für „RoleAssignment“

Rollenzuweisungen enthalten Informationen, die einen bestimmten Sicherheitsprinzipal (Benutzer oder Anwendungsdienstprinzipal) mit einer Rollendefinition verknüpfen. Bei Bedarf können Sie für die zugewiesenen Berechtigungen einen Geltungsbereich für eine einzelne Azure AD-Ressource hinzufügen.  Das Einschränken des Geltungsbereichs für Berechtigungen wird für integrierte und benutzerdefinierte Rollen unterstützt.

### <a name="create-operations-on-roleassignment"></a>Erstellungsvorgänge für „RoleAssignment“

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-operations-on-roleassignment"></a>Lesevorgänge für „RoleAssignment“

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Löschvorgänge für „RoleAssignment“

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Nächste Schritte

- Im [Forum für Azure AD-Administratorrollen](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) können Sie sich mit uns in Verbindung setzen.
- Weitere Informationen zu Rollen und Azure AD-Administratorrollenzuweisungen finden Sie unter [Zuweisen von Administratorrollen](permissions-reference.md).
- Informationen zu Standardbenutzerberechtigungen finden Sie unter [Vergleich von Standardbenutzerberechtigungen für Gäste und Mitglieder](../fundamentals/users-default-permissions.md).
