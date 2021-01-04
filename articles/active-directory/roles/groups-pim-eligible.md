---
title: Zuweisen einer Rolle zu einer Gruppe mithilfe von Privileged Identity Management in Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einer Gruppe mithilfe von Azure AD Privileged Identity Management (PIM) eine Azure Active Directory-Rolle (Azure AD) zuweisen können.
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
ms.openlocfilehash: 74ad503374e0148e9813508b6c7f8b21e2dca7a3
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379161"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Zuweisen einer Rolle zu einer Gruppe mithilfe von Privileged Identity Management

In diesem Artikel wird beschrieben, wie Sie einer Gruppe mithilfe von Azure AD Privileged Identity Management (PIM) eine Azure Active Directory-Rolle (Azure AD) zuweisen können.

> [!NOTE]
> Sie müssen die aktualisierte Version von Privileged Identity Management verwenden, um einer Azure AD-Rolle mithilfe von PIM eine Gruppe zuweisen zu können. Möglicherweise verwenden Sie eine ältere PIM-Version, wenn Ihre Azure AD-Organisation die Privileged Identity Management-API nutzt. Wenden Sie sich in diesem Fall an den Alias pim_preview@microsoft.com, um Ihre Organisation umzustellen und die API zu aktualisieren. Weitere Informationen finden Sie unter [Azure AD-Rollen und Features in PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="using-azure-ad-admin-center"></a>Mithilfe von Azure AD Admin Center

1. Melden Sie sich bei [Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) als Administrator für privilegierte Rollen oder als globaler Administrator in Ihrer Organisation an.

1. Wählen Sie **Privileged Identity Management** > **Azure AD-Rollen** > **Rollen** > **Zuweisungen hinzufügen** aus.

1. Wählen Sie eine Rolle und dann eine Gruppe aus. Es werden nur Gruppen angezeigt, die für die Rollenzuweisung berechtigt sind (Gruppen, denen Rollen zugewiesen werden können), nicht alle Gruppen.

    ![Screenshot der Seite „Zuweisungen hinzufügen“ mit den hervorgehobenen Abschnitten „Rolle auswählen“ und „Mitglied(er) auswählen“.](./media/groups-pim-eligible/select-member.png)

1. Wählen Sie die gewünschte Mitgliedschaftseinstellung aus. Wählen Sie für Rollen, die eine Aktivierung erfordern, die Option **Berechtigt** aus. Standardmäßig ist der Benutzer permanent berechtigt, doch Sie können auch eine Start- und Endzeit für die Berechtigung des Benutzers festlegen. Wenn Sie fertig sind, klicken Sie auf „Speichern“ und „Hinzufügen“, um die Rollenzuweisung abzuschließen.

    ![Auswählen des Benutzers, dem Sie die Rolle zuweisen](./media/groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>Mithilfe von PowerShell

### <a name="download-the-azure-ad-preview-powershell-module"></a>Herunterladen des Azure AD PowerShell-Vorschaumoduls

Verwenden Sie die folgenden Cmdlets, um das Azure AD PowerShell-Modul zu installieren:

```powershell
install-module azureadpreview
import-module azureadpreview
```

Überprüfen Sie mithilfe des folgenden Cmdlets, ob das Modul verwendet werden kann:

```powershell
get-module azureadpreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Zuweisen einer Gruppe als berechtigtes Mitglied einer Rolle

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Mithilfe der Microsoft Graph-API

```powershell
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests  

{

 "roleDefinitionId": {roleDefinitionId},

 "resourceId": {tenantId},

 "subjectId": {GroupId},

 "assignmentState": "Eligible",

 "type": "AdminAdd",

 "reason": "reason string",

 "schedule": {

   "startDateTime": {DateTime},

   "endDateTime": {DateTime},

   "type": "Once"

 }

}
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Cloudgruppen zum Verwalten von Rollenzuweisungen](groups-concept.md)
- [Problembehandlung bei Rollen, die Cloudgruppen zugewiesen sind](groups-faq-troubleshooting.md)
- [Konfigurieren von Einstellungen für Azure AD-Rollen in PIM](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Zuweisen von Azure-Ressourcenrollen in PIM](../privileged-identity-management/pim-resource-roles-assign-roles.md)
