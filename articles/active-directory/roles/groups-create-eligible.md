---
title: Erstellen einer Gruppe für das Zuweisen von Rollen in Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure AD eine Gruppe erstellen, der Rollen zugewiesen werden können. Verwalten von Azure-Rollen im Azure-Portal, mit PowerShell oder über die Graph-API.
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
ms.openlocfilehash: bc2c42f407ee31a792a6634a813358d428120b46
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378617"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Erstellen einer Gruppe in Azure Active Directory für das Zuweisen von Rollen

Eine Rolle kann nur einer Gruppe zugewiesen werden, die mit der isAssignableToRole-Eigenschaft mit dem Wert TRUE oder die im Azure AD-Portal mit aktivierter Option **Azure AD-Rollen können der Gruppe zugewiesen werden** erstellt wurde. Mit diesem Gruppenattribut ist es möglich, der Gruppe eine Rolle in Azure Active Directory (Azure AD) zuzuweisen. In diesem Artikel wird beschrieben, wie Sie diesen besonderen Gruppentyp erstellen. **Hinweis:** Eine Gruppe, deren isAssignableToRole-Eigenschaft auf „true“ festgelegt ist, kann nicht den dynamischen Mitgliedschaftstyp aufweisen. Weitere Informationen finden Sie unter [Verwenden von Cloudgruppen zum Verwalten von Rollenzuweisungen in Azure Active Directory (Vorschau)](groups-concept.md).

## <a name="using-azure-ad-admin-center"></a>Mithilfe von Azure AD Admin Center

1. Melden Sie sich bei [Azure AD Admin Center](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit Berechtigungen vom Typ „Administrator für privilegierte Rollen“ oder „Globaler Administrator“ in der Azure AD-Organisation an.
1. Wählen Sie **Gruppen** > **Alle Gruppen** > **Neue Gruppe** aus.

    [![Öffnen von Azure Active Directory und Erstellen einer neuen Gruppe](./media/groups-create-eligible/new-group.png "Öffnen von Azure Active Directory und Erstellen einer neuen Gruppe")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. Geben Sie auf der Registerkarte **Neue Gruppe** den Gruppentyp, den Namen und eine Beschreibung an.
1. Aktivieren Sie **Azure AD-Rollen können der Gruppe zugewiesen werden**. Dieser Schalter wird nur den Rollen „Administratoren für privilegierte Rollen“ und „Globaler Administrator“ angezeigt, da nur diese beiden Rollen den Schalter festlegen können.

    [![Aktivieren der Rollenzuweisung für die neue Gruppe](./media/groups-create-eligible/eligible-switch.png "Aktivieren der Rollenzuweisung für die neue Gruppe")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. Wählen Sie die Mitglieder und Besitzer der Gruppe aus. Sie haben auch die Möglichkeit, der Gruppe Rollen zuzuweisen, dies ist hier allerdings nicht erforderlich.

    [![Hinzufügen von Mitgliedern zu der Gruppe mit Rollenzuweisung und Zuweisen von Rollen](./media/groups-create-eligible/specify-members.png "Hinzufügen von Mitgliedern zu der Gruppe mit Rollenzuweisung und Zuweisen von Rollen")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. Nachdem Sie die Mitglieder und Besitzer angegeben haben, wählen Sie **Erstellen** aus.

    [![Schaltfläche „Erstellen“ am unteren Rand der Seite](./media/groups-create-eligible/create-button.png "Schaltfläche „Erstellen“ am unteren Rand der Seite")](./media/groups-create-eligible/create-button.png#<lightbox>)

Die Gruppe wird mit allen Rollen erstellt, die Sie ihr zugewiesen haben.

## <a name="using-powershell"></a>PowerShell

### <a name="install-the-azure-ad-preview-module"></a>Installieren des Azure AD Preview-Moduls

```powershell
install-module azureadpreview
import-module azureadpreview
```

Überprüfen Sie mithilfe des folgenden Befehls, ob das Modul verwendet werden kann:

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Erstellen einer Gruppe, der die Rolle zugewiesen werden kann

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

Bei diesem Gruppentyp ist `isPublic` immer FALSE und `isSecurityEnabled` immer TRUE.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Kopieren der Benutzer und Dienstprinzipale einer Gruppe in eine Gruppe mit Rollenzuweisungen

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Verwenden der Microsoft Graph-API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Erstellen einer Gruppe, der Rollen zugewiesen werden können, in Azure AD

```powershell
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

Bei diesem Gruppentyp ist `isPublic` immer FALSE und `isSecurityEnabled` immer TRUE.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen einer Rolle zu einer Cloudgruppe](groups-assign-role.md)
- [Verwenden von Cloudgruppen zum Verwalten von Rollenzuweisungen](groups-concept.md)
- [Problembehandlung bei Rollen, die Cloudgruppen zugewiesen sind](groups-faq-troubleshooting.md)
