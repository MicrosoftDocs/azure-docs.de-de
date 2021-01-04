---
title: Erstellen von benutzerdefinierten Rollen in der rollenbasierten Zugriffssteuerung von Azure AD | Microsoft-Dokumentation
description: Erstellen Sie benutzerdefinierte Azure AD-Rollen mit den Azure Active Directory-Ressourcen als Ressourcenbereich, und weisen Sie sie zu.
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
ms.openlocfilehash: 4436385b07dd4f1da81f17eb19b38e922e21a1ef
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180420"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Erstellen und Zuweisen einer benutzerdefinierten Rolle in Azure Active Directory

In diesem Artikel wird beschrieben, wie Sie neue benutzerdefinierte Rollen in Azure Active Directory (Azure AD) erstellen. Weitere Informationen zu den Grundlagen benutzerdefinierter Rollen finden Sie in der [Übersicht über benutzerdefinierte Rollen](custom-overview.md). Die Rolle kann entweder für den Bereich der Verzeichnisebene oder nur für den Ressourcenbereich einer App-Registrierung zugewiesen werden.

Benutzerdefinierte Rollen können auf der Übersichtsseite für Azure AD auf der Registerkarte [Rollen und Administratoren](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) erstellt werden.

## <a name="create-a-role-in-the-azure-portal"></a>Erstellen einer Rolle im Azure-Portal

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Erstellen einer neuen benutzerdefinierten Rolle für den Zugriff zum Verwalten von App-Registrierungen

1. Melden Sie sich bei [Azure AD Admin Center](https://aad.portal.azure.com) mit Berechtigungen vom Typ „Administrator für privilegierte Rollen“ oder „Globaler Administrator“ in der Azure AD-Organisation an.
1. Wählen Sie **Azure Active Directory** > **Rollen und Administratoren** > **Neue benutzerdefinierte Rolle** aus.

   ![Erstellen oder Bearbeiten von Rollen auf der Seite „Rollen und Administratoren“](./media/custom-create/new-custom-role.png)

1. Geben Sie auf der Registerkarte **Grundlagen** einen Namen und eine Beschreibung für die Rolle ein, und klicken Sie dann auf **Weiter**.

   ![Angeben eines Namens und einer Beschreibung für eine benutzerdefinierte Rolle auf der Registerkarte „Grundlagen“](./media/custom-create/basics-tab.png)

1. Wählen Sie auf der Registerkarte **Berechtigungen** die Berechtigungen aus, die zum Verwalten von grundlegenden Eigenschaften und Anmeldeinformationen für App-Registrierungen erforderlich sind. Eine ausführliche Beschreibung der einzelnen Berechtigungen finden Sie unter [Anwendungsregistrierungsuntertypen und -berechtigungen in Azure Active Directory](custom-available-permissions.md).
   1. Geben Sie zunächst „credentials“ in die Suchleiste ein, und wählen Sie die Berechtigung `microsoft.directory/applications/credentials/update` aus.

      ![Auswählen der Berechtigungen für eine benutzerdefinierte Rolle auf der Registerkarte „Berechtigungen“](./media/custom-create/permissions-tab.png)

   1. Dann geben Sie „basic“ in die Suchleiste ein, wählen die Berechtigung `microsoft.directory/applications/basic/update` aus und klicken anschließend auf **Weiter**.
1. Überprüfen Sie die Berechtigungen auf der Registerkarte **Überprüfen + erstellen**, und wählen Sie **Erstellen** aus.

Die benutzerdefinierte Rolle wird in der Liste der für die Zuweisung verfügbaren Rollen angezeigt.

## <a name="create-a-role-using-powershell"></a>Erstellen einer Rolle mit PowerShell

### <a name="prepare-powershell"></a>Vorbereiten von PowerShell

Zunächst müssen Sie das [Azure AD PowerShell-Vorschaumodul herunterladen](https://www.powershellgallery.com/packages/AzureADPreview).

Verwenden Sie die folgenden Befehle, um das Azure AD PowerShell-Modul zu installieren:

``` PowerShell
Install-Module AzureADPreview
Import-Module AzureADPreview
```

Überprüfen Sie mithilfe des folgenden Befehls, ob das Modul verwendet werden kann:

``` PowerShell
Get-Module AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Erstellen der benutzerdefinierten Rolle

Verwenden Sie zum Erstellen einer neuen Rolle das folgende PowerShell-Skript:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Zuweisen der benutzerdefinierten Rolle mithilfe von Azure AD PowerShell

Weisen Sie die Rolle mithilfe des folgenden PowerShell-Skripts zu:

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

## <a name="create-a-role-with-graph-api"></a>Erstellen einer Rolle mit der Graph-API

1. Erstellen Sie die Rollendefinition.

    HTTP-Anforderung zum Erstellen einer benutzerdefinierten Rollendefinition.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > Die `"templateId": "GUID"` ist ein optionaler Parameter, der je nach der Anforderung im Text gesendet wird. Wenn Sie mehrere verschiedene benutzerdefinierte Rollen mit allgemeinen Parametern erstellen müssen, empfiehlt es sich, eine Vorlage zu erstellen und einen `templateId`-Wert zu definieren. Vorher können Sie mit dem PowerShell-Cmdlet `(New-Guid).Guid` einen `templateId`-Wert generieren. 

1. Erstellen Sie die Rollenzuweisung.

    HTTP-Anforderung zum Erstellen einer benutzerdefinierten Rollendefinition.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Body

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Zuweisen einer auf eine Ressource begrenzten benutzerdefinierten Rolle

Wie integrierte Rollen werden benutzerdefinierte Rollen standardmäßig im organisationsweiten Standardbereich zugewiesen, um Zugriffsberechtigungen für alle App-Registrierungen in Ihrer Organisation zu erteilen. Anders als integrierte Rollen können benutzerdefinierte Rollen jedoch auch im Bereich einer einzelnen Azure AD-Ressource zugewiesen werden. Dadurch können Sie dem Benutzer die Berechtigung zum Aktualisieren von Anmeldeinformationen und grundlegenden Eigenschaften einer einzelnen App erteilen, ohne eine zweite benutzerdefinierte Rolle erstellen zu müssen.

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit Berechtigungen des Typs „Anwendungsentwickler“ in der Azure AD-Organisation an.
1. Wählen Sie **App-Registrierungen** aus.
1. Wählen Sie die App-Registrierung aus, für die Sie Zugriff zum Verwalten gewähren möchten. Möglicherweise müssen Sie die Option **Alle Anwendungen** auswählen, um die vollständige Liste der App-Registrierungen in ihrer Azure AD-Organisation anzuzeigen.

    ![Auswählen der App-Registrierung als Ressourcenbereich für eine Rollenzuweisung](./media/custom-create/appreg-all-apps.png)

1. Wählen Sie in der App-Registrierung die Option **Rollen und Administratoren** aus. Wenn Sie noch keine erstellt haben, finden Sie Anweisungen im [vorherigen Verfahren](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Wählen Sie die Rolle aus, um die Seite **Zuweisungen** zu öffnen.
1. Wählen Sie **Zuweisung hinzufügen** aus, um einen Benutzer hinzuzufügen. Dem Benutzer werden ausschließlich Berechtigungen für die ausgewählte App-Registrierung erteilt.

## <a name="next-steps"></a>Nächste Schritte

- Im [Forum für Azure AD-Administratorrollen](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) können Sie sich gerne mit uns in Verbindung setzen.
- Weitere Informationen zu Rollen und zur Zuweisung von Administratorrollen finden Sie unter [Zuweisen von Administratorrollen](permissions-reference.md).
- Informationen zu Standardbenutzerberechtigungen finden Sie unter [Vergleich von Standardbenutzerberechtigungen für Gäste und Mitglieder](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context).