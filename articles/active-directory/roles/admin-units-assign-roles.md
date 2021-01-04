---
title: 'Hinzufügen und Auflisten von Rollen im Bereich von Verwaltungseinheiten: Azure Active Directory | Microsoft-Dokumentation'
description: Verwenden Sie Verwaltungseinheiten, um den Geltungsbereich von Rollenzuweisungen in Azure Active Directory einzuschränken.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ed2741c7dd754127a57642703b650a70637c63
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393437"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Zuweisen von bereichsbezogenen Rollen zu einer Verwaltungseinheit

Um in Azure Active Directory (Azure AD) eine präzisere administrative Steuerung zu ermöglichen, können Sie Benutzer einer Azure AD-Rolle mit einem auf einzelne oder mehrere Verwaltungseinheiten beschränkten Bereich zuweisen.

Informationen zur Vorbereitung auf den Einsatz von PowerShell und Microsoft Graph für die Verwaltung von Verwaltungseinheiten finden Sie unter [Erste Schritte](admin-units-manage.md#get-started).

## <a name="available-roles"></a>Verfügbare Rollen

Role  |  BESCHREIBUNG
----- |  -----------
Authentifizierungsadministrator  |  Hat nur in der zugewiesenen Verwaltungseinheit Zugriff zum Anzeigen, Festlegen und Zurücksetzen von Informationen zur Authentifizierungsmethode für alle Benutzer ohne Administratorrechte.
Gruppenadministrator  |  Kann nur in der zugewiesenen Verwaltungseinheit alle Aspekte von Gruppen und Gruppeneinstellungen wie Benennungs- und Ablaufrichtlinien verwalten.
Helpdeskadministrator  |  Kann nur in der zugewiesenen Verwaltungseinheit Kennwörter für Nicht-Administratoren und Helpdeskadministratoren zurücksetzen.
Lizenzadministrator  |  Kann nur in der Verwaltungseinheit Lizenzzuweisungen vornehmen, entfernen und aktualisieren.
Kennwortadministrator  |  Kann nur in der zugewiesenen Verwaltungseinheit Kennwörter für Nicht-Administratoren und Kennwortadministratoren zurücksetzen.
Benutzeradministrator  |  Kann nur in der zugewiesenen Verwaltungseinheit alle Aspekte von Benutzern und Gruppen verwalten, einschließlich der Kennwortzurücksetzung für Administratoren mit eingeschränkten Berechtigungen.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Sicherheitsprinzipale, die einer bereichsbezogenen Rolle zugewiesen werden können

Die folgenden Sicherheitsprinzipale können einer Rolle im Bereich von Verwaltungseinheiten zugewiesen werden:

* Benutzer
* Cloudgruppen, die einer Rolle zugewiesen werden können (Vorschauversion)
* Dienstprinzipalname (Service Principal Name, SPN)

## <a name="assign-a-scoped-role"></a>Zuweisen einer bereichsbezogenen Rolle

Eine bereichsbezogene Rolle kann über das Azure-Portal, mithilfe von PowerShell oder per Microsoft Graph zugewiesen werden.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Navigieren Sie im Azure-Portal zu **Azure AD**.

1. Wählen Sie **Verwaltungseinheiten** und anschließend die Verwaltungseinheit aus, der Sie einen Benutzerrollenbereich zuweisen möchten. 

1. Wählen Sie im linken Bereich die Option **Rollen und Administratoren** aus, um alle verfügbaren Rollen aufzulisten.

   ![Screenshot: Bereich „Rollen und Administratoren“ zum Auswählen einer Verwaltungseinheit, deren Rollenbereich zugewiesen werden soll](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Wählen Sie die zuzuweisende Rolle und anschließend **Zuweisungen hinzufügen** aus. 

1. Wählen Sie im Bereich **Zuweisungen hinzufügen** mindestens einen Benutzer aus, dem die Rolle zugewiesen werden soll.

   ![Auswählen der Rolle für den Bereich und dann Auswählen von „Zuweisungen hinzufügen“](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Informationen zum Zuweisen einer Rolle für eine Verwaltungseinheit unter Verwendung von Azure AD Privileged Identity Management (PIM) finden Sie unter [Zuweisen von Azure AD-Rollen in Privileged Identity Management](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>Verwenden von PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

Der hervorgehobene Abschnitt kann nach Bedarf für die jeweilige Umgebung geändert werden.

### <a name="use-microsoft-graph"></a>Verwenden von Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Anzeigen einer Liste mit den bereichsbezogenen Administratoren in einer Verwaltungseinheit

Eine Liste mit bereichsbezogenen Administratoren kann über das Azure-Portal, mithilfe von PowerShell oder per Microsoft Graph angezeigt werden.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Alle Rollenzuweisungen, die mit einem auf Verwaltungseinheiten bezogenen Bereich erstellt wurden, können im Abschnitt [Verwaltungseinheiten von Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit) angezeigt werden. 

1. Navigieren Sie im Azure-Portal zu **Azure AD**.

1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** und dann die Verwaltungseinheit für die Liste mit den Rollenzuweisungen aus, die Sie anzeigen möchten. 

1. Wählen Sie **Rollen und Administratoren** aus, und öffnen Sie anschließend eine Rolle, um die Zuweisungen in der Verwaltungseinheit anzuzeigen.

### <a name="use-powershell"></a>Verwenden von PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

Der hervorgehobene Abschnitt kann nach Bedarf für Ihre individuelle Umgebung geändert werden.

### <a name="use-microsoft-graph"></a>Verwenden von Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Cloudgruppen zum Verwalten von Rollenzuweisungen](groups-concept.md)
- [Problembehandlung bei Rollen, die Cloudgruppen zugewiesen sind](groups-faq-troubleshooting.md)
