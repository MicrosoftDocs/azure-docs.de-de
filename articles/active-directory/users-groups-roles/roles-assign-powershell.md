---
title: Hinzufügen und Entfernen von Administratorrollenzuweisungen mit Azure PowerShell – Azure Active Directory | Microsoft-Dokumentation
description: 'Alle, die häufig Rollenzuweisungen verwalten, wird es freuen: Sie können jetzt die Mitglieder einer Azure AD-Administratorrolle mit Azure PowerShell verwalten.'
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6877c3e547d625cf58129a546dae798b37a24ae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469093"
---
# <a name="assign-azure-active-directory-admin-roles-using-powershell"></a>Zuweisen von Azure Active Directory-Administratorrollen mithilfe von PowerShell

Sie können die Zuweisung von Rollen an Benutzerkonten mithilfe von Azure PowerShell automatisieren. Dieser Artikel verwendet das [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)-Modul.

## <a name="prepare-powershell"></a>Vorbereiten von PowerShell

Zunächst müssen Sie [das Azure AD PowerShell-Modul herunterladen](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Installieren des Azure AD PowerShell-Moduls

Verwenden Sie die folgenden Befehle, um das Azure AD PowerShell-Modul zu installieren:

```powershell
install-module azuread
import-module azuread
```

Überprüfen Sie mithilfe des folgenden Befehls, ob das Modul verwendet werden kann:

```powershell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Sie können die Cmdlets jetzt im Modul verwenden. Eine ausführliche Beschreibung der Cmdlets im Azure AD-Modul finden Sie in der Onlinereferenzdokumentation für [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles).

## <a name="permissions-required"></a>Erforderliche Berechtigungen

Stellen Sie eine Verbindung mit Ihrem Azure AD-Mandanten mithilfe eines globalen Administratorkontos her, um Rollen zuzuweisen oder zu entfernen.

## <a name="assign-a-single-role"></a>Zuweisen einer einzelnen Rolle

Um eine Rolle zuzuweisen, müssen Sie zunächst ihren Anzeigenamen und den Namen der Rolle abrufen, die Sie zuweisen möchten. Wenn Sie über den Anzeigenamen des Kontos und den Namen der Rolle verfügen, verwenden Sie die folgenden Cmdlets, um dem Benutzer die Rolle zuzuweisen.

``` PowerShell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="assign-a-role-to-a-service-principal"></a>Zuweisen einer Rolle zu einem Dienstprinzipal

Beispiel einer Zuweisung eines Dienstprinzipals zu einer Rolle.

```powershell
# Fetch a service principal to assign to role
$roleMember = Get-AzureADServicePrincipal -ObjectId "00221b6f-4387-4f3f-aa85-34316ad7f956"
 
#Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole
 
# Fetch a directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"
 
# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
 
# Fetch the assignment for the role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADServicePrincipal
```

## <a name="multiple-role-assignments"></a>Mehrere Rollenzuweisungen

Beispiele für das Zuweisen und Entfernen mehrerer Rollen in einem Durchgang.

```powershell
#File name
$fileName="<file path>" 
 
$input_Excel = New-Object -ComObject Excel.Application
$input_Workbook = $input_Excel.Workbooks.Open($fileName)
$input_Worksheet = $input_Workbook.Sheets.Item(1)
 
        #Count number of users who have to be assigned to role
$count = $input_Worksheet.UsedRange.Rows.Count
 
#Loop through each line of the csv file starting from line 2 (assuming first line is title)
for ($i=2; $i -le $count; $i++)
{
       #Fetch user display name
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       
       #Fetch role name
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       #Assign role
       Add-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -RefObjectId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
 
#Remove multiple role assignments
for ($i=2; $i -le $count; $i++)
{
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       Remove-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -MemberId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
```

## <a name="remove-a-role-assignment"></a>Entfernen einer Rollenzuweisung

In diesem Beispiel wird eine Rollenzuweisung für den angegebenen Benutzer entfernt.

```powershell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

#Fetch list of all directory roles with object id
Get-AzureADDirectoryRole
 
# Fetch a directory role by id
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"
 
# Remove user from role
Remove-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -MemberId $roleMember.ObjectId 

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
 
```

## <a name="next-steps"></a>Nächste Schritte

* Im [Forum für Azure AD-Administratorrollen](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) können Sie sich gerne mit uns in Verbindung setzen.
* Weitere Informationen zu Rollen und zur Zuweisung von Administratorrollen finden Sie unter [Zuweisen von Administratorrollen](directory-assign-admin-roles.md).
* Informationen zu Standardbenutzerberechtigungen finden Sie unter [Vergleich von Standardbenutzerberechtigungen für Gäste und Mitglieder](../fundamentals/users-default-permissions.md).
