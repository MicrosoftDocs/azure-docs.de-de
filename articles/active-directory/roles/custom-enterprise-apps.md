---
title: Benutzerdefinierte Rollenberechtigungen für Zugriffszuweisungen für Unternehmens-Apps – Azure Active Directory | Microsoft-Dokumentation
description: Erstellen und Zuweisen benutzerdefinierter Azure AD-Rollen für den Zugriff auf Unternehmens-Apps in Azure Active Directory
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
ms.openlocfilehash: 0932e4b6163264b0b514958e1e898b297e249870
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378549"
---
# <a name="assign-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Zuweisen benutzerdefinierter Rollen zum Verwalten von Unternehmens-Apps in Azure Active Directory

In diesem Artikel wird erläutert, wie Sie eine benutzerdefinierte Rolle mit Berechtigungen zum Verwalten von Unternehmens-App-Zuweisungen für Benutzer und Gruppen in Azure Active Directory (Azure AD) erstellen. Erläuterungen der Elemente von Rollenzuweisungen und der Bedeutung von Begriffen wie Untertyp, Berechtigung und Eigenschaftensatz finden Sie in der [Übersicht über benutzerdefinierte Rollen](custom-overview.md).

## <a name="enterprise-app-role-permissions"></a>Rollenberechtigungen für Unternehmens-Apps

In diesem Artikel werden zwei Berechtigungen für Unternehmens-Apps erörtert. In allen Beispielen wird die Berechtigung zum Aktualisieren („update“) verwendet.

* Zum Lesen der Benutzer- und Gruppenzuweisungen im Bereich erteilen Sie die Berechtigung `microsoft.directory/servicePrincipals/appRoleAssignedTo/read`.
* Zum Verwalten der Benutzer- und Gruppenzuweisungen im Bereich erteilen Sie die Berechtigung `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`.

Das Erteilen der Berechtigung zum Aktualisieren führt dazu, dass die zugewiesene Person Zuweisungen von Benutzern und Gruppen zu Unternehmens-Apps verwalten kann. Der Bereich von Benutzer- und/oder Gruppenzuweisungen kann für eine einzelne Anwendung oder für alle Anwendungen erteilt werden. Wenn die Erteilung auf organisationsweiter Ebene erfolgt, kann die zugewiesene Person Zuweisungen für alle Anwendungen verwalten. Wenn die Erteilung auf Anwendungsebene erfolgt, kann die zugewiesene Person nur Zuweisungen für die angegebene Anwendung verwalten.

Das Erteilen der Berechtigung zum Aktualisieren erfolgt in zwei Schritten:

1. Erstellen einer benutzerdefinierten Rolle mit der Berechtigung `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Erteilen von Berechtigungen für Benutzer oder Gruppen zum Verwalten von Benutzer- und Gruppenzuweisungen für Unternehmens-Apps: Dabei können Sie den Bereich auf die organisationsweite Ebene oder auf eine einzelne Anwendung festlegen.

## <a name="use-the-azure-ad-admin-center"></a>Verwenden des Azure AD Admin Centers

### <a name="create-a-new-custom-role"></a>Erstellen einer neuen benutzerdefinierten Rolle

>[!NOTE]
> Benutzerdefinierte Rollen werden auf Organisationsebene erstellt und verwaltet und sind nur auf der Seite „Übersicht“ der Organisation verfügbar.

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) als Benutzer mit Berechtigungen vom Typ „Administrator für privilegierte Rollen“ oder „Globaler Administrator“ in Ihrer Organisation an.
1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** > **Neue benutzerdefinierte Rolle** aus.

    ![Hinzufügen einer neuen benutzerdefinierten Rolle aus der Liste der Rollen in Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. Geben Sie auf der Registerkarte **Grundeinstellungen** im Feld „Name“ für die Rolle die Bezeichnung „Benutzer- und Gruppenzuweisungen verwalten“ und im Feld „Beschreibung“ den Text „Berechtigungen zum Verwalten von Benutzer- und Gruppenzuweisungen erteilen“ ein, und wählen Sie dann **Weiter** aus.

    ![Angeben eines Namens und einer Beschreibung für die benutzerdefinierte Rolle](./media/custom-enterprise-apps/role-name-and-description.png)

1. Geben Sie auf der Registerkarte **Berechtigungen** im Suchfeld die Zeichenfolge „microsoft.directory/servicePrincipals/appRoleAssignedTo/update“ ein. Aktivieren Sie dann die Kontrollkästchen neben den gewünschten Berechtigungen, und wählen Sie anschließend **Weiter** aus.

    ![Hinzufügen der Berechtigungen für die benutzerdefinierte Rolle](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Überprüfen Sie die Berechtigungen auf der Registerkarte **Überprüfen + erstellen** , und wählen Sie **Erstellen** aus.

    ![Jetzt können Sie die benutzerdefinierte Rolle erstellen.](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Zuweisen der Rolle zu einem Benutzer über das Azure AD-Portal

1. Melden Sie sich mit Berechtigungen der Rolle „Administrator für privilegierte Rollen“ beim [Azure AD Admin Center](https://aad.portal.azure.com) an.
1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** aus.
1. Wählen Sie die Rolle **Berechtigungen zum Verwalten von Benutzer- und Gruppenzuweisungen erteilen** aus.

    ![„Rollen und Administratoren“ öffnen und nach der benutzerdefinierten Rolle suchen](./media/custom-enterprise-apps/select-custom-role.png)

1. Wählen Sie **Zuweisung hinzufügen** aus, wählen Sie den gewünschten Benutzer aus, und klicken Sie dann auf **Auswählen** , um dem Benutzer eine Rollenzuweisung hinzuzufügen.

    ![Dem Benutzer eine Zuweisung für die benutzerdefinierte Rolle hinzufügen](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Zuweisungstipps

* Wenn Sie zugewiesenen Personen Berechtigungen zum Verwalten des Benutzer- und Gruppenzugriffs für alle organisationsweiten Unternehmens-Apps erteilen möchten, öffnen Sie in Azure AD die Seite **Übersicht** für Ihre Organisation, und bearbeiten Sie die Liste der organisationsweiten **Rollen und Administratoren**.
* Wenn Sie zugewiesenen Personen Berechtigungen zum Verwalten des Benutzer- und Gruppenzugriffs für eine bestimmte Unternehmens-App erteilen möchten, navigieren Sie in Azure AD zu dieser App, und öffnen Sie die Liste **Rollen und Administratoren** für diese App. Wählen Sie die neue benutzerdefinierte Rolle aus, und schließen Sie die Benutzer- oder Gruppenzuweisung ab. Die zugewiesenen Personen können den Benutzer- und Gruppenzugriff nur für diese bestimmte App verwalten.
* Wenn Sie Ihre benutzerdefinierte Rollenzuweisung testen möchten, melden Sie sich als die zugewiesene Person an, und öffnen Sie die Seite **Benutzer und Gruppen** einer Anwendung, um zu überprüfen, ob die Option **Benutzer hinzufügen** aktiviert ist.

    ![Überprüfen der Benutzerberechtigungen](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Verwenden von Azure AD PowerShell

Ausführliche Informationen finden Sie unter [Erstellen und Zuweisen einer benutzerdefinierten Rolle](custom-create.md) und [Zuweisen benutzerdefinierter Rollen mit Ressourcengeltungsbereich unter Verwendung von PowerShell in Azure Active Directory](custom-assign-powershell.md).

Installieren Sie zunächst das Azure AD PowerShell-Modul aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importieren Sie anschließend das Azure AD PowerShell-Vorschaumodul mithilfe des folgenden Befehls:

```powershell
PowerShell
import-module azureadpreview
```

Gleichen Sie die Version, die durch den folgenden Befehl zurückgegeben wird, mit der hier aufgeführten Version ab, um sich zu vergewissern, dass das Modul verwendungsbereit ist:

```powershell
PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Verwenden Sie zum Erstellen einer neuen Rolle das folgende PowerShell-Skript:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Zuweisen der benutzerdefinierten Rolle

Weisen Sie die Rolle mithilfe des folgenden PowerShell-Skripts zu.

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>Verwenden der Microsoft Graph-API

Erstellen Sie anhand des bereitgestellten Beispiels eine benutzerdefinierte Rolle in der Microsoft Graph-API. Ausführliche Informationen finden Sie unter [Erstellen und Zuweisen einer benutzerdefinierten Rolle](custom-create.md) und [Zuweisen von benutzerdefinierten Administratorrollen mithilfe von Microsoft Graph-API in Azure Active Directory](custom-assign-graph.md).

HTTP-Anforderung zum Erstellen der benutzerdefinierten Rolle:

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Zuweisen der benutzerdefinierten Rolle mithilfe der Microsoft Graph-API

Die Rollenzuweisung kombiniert eine Sicherheitsprinzipal-ID (bei der es sich um einen Benutzer oder Dienstprinzipal handeln kann), eine Rollendefinitions-ID und einen Azure AD-Ressourcenbereich. Weitere Informationen zu den Elementen einer Rollenzuweisung finden Sie in der [Übersicht über benutzerdefinierte Rollen](custom-overview.md).

HTTP-Anforderung zum Zuweisen einer benutzerdefinierten Rolle:

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die verfügbaren [benutzerdefinierten Rollenberechtigungen für Unternehmens-Apps](custom-enterprise-app-permissions.md).
