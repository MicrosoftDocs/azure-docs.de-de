---
title: Verwalten von Benutzerzuweisungen für eine App in Azure Active Directory
description: Erfahren Sie, wie Sie Benutzer und Gruppen für eine App zuweisen und deren Zuweisung aufheben, indem Sie Azure Active Directory für die Identitätsverwaltung verwenden.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 22acfc5095b1ffcad382fa2b665a86d382645e7a
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861676"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Verwalten von Benutzerzuweisungen für eine App in Azure Active Directory

In diesem Artikel wird gezeigt, wie Sie Benutzer und Gruppen in Azure Active Directory (Azure AD) Unternehmensanwendungen zuweisen können, und zwar entweder im Azure-Portal oder über PowerShell. Wenn Sie einen Benutzer einer Anwendung zuweisen, wird diese unter [Meine Apps](https://myapps.microsoft.com/) für den Benutzer angezeigt, um den Zugriff zu erleichtern. Wenn die Anwendung Rollen verfügbar macht, können Sie dem Benutzer auch eine bestimmte Rolle zuweisen.

Für eine bessere Kontrolle können bestimmte Arten von Unternehmensanwendungen so konfiguriert werden, dass [eine Benutzerzuweisung](#configure-an-application-to-require-user-assignment) erforderlich ist. 

> [!IMPORTANT]
> Wenn Sie einer Anwendung eine Gruppe zuweisen, haben nur die Benutzer in der Gruppe Zugriff. Die Zuweisung wird nicht an geschachtelte Gruppen weitergegeben.

> [!NOTE]
> Für die gruppenbasierte Zuweisung ist die Azure Active Directory Premium P1- oder P2-Edition erforderlich. Die gruppenbasierte Zuweisung wird nur für Sicherheitsgruppen unterstützt. Geschachtelte Gruppenmitgliedschaften und Microsoft 365-Gruppen werden aktuell nicht unterstützt. Weitere Informationen zu Lizenzierungsanforderungen für die in diesem Artikel erläuterten Features finden Sie in der [Preisübersicht für Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Konfigurieren einer Anwendung für das Anfordern einer Benutzerzuweisung

Bei den folgenden Anwendungstypen haben Sie die Möglichkeit anzufordern, dass Benutzer der Anwendung zugeordnet werden müssen, ehe sie darauf zugreifen können:

- Anwendungen, die für einmaliges Anmelden (SSO) im Verbund mit SAML-basierter Authentifizierung konfiguriert sind
- Anwendungsproxyanwendungen mit Azure Active Directory-Vorauthentifizierung
- Anwendungen, die auf der Azure AD-Anwendungsplattform bei Verwendung der OAuth 2.0/OpenID Connect-Authentifizierung erstellt werden (nachdem ein Benutzer oder Administrator seine Zustimmung für die Anwendung erteilt hat).

Wenn eine Benutzerzuweisung erforderlich ist, können sich nur die Benutzer anmelden, die Sie der Anwendung explizit zuweisen (durch direkte Benutzerzuweisung oder basierend auf Gruppenmitgliedschaft). Sie können auf die App auf ihrer Seite „Meine Apps“ oder über einen direkten Link zugreifen. 

Wenn die Zuweisung *nicht erforderlich* ist, entweder weil Sie diese Option auf **Nein** festgelegt haben oder die Anwendung einen anderen SSO-Modus verwendet, kann jeder Benutzer auf die Anwendung zugreifen. Dazu muss er über einen direkten Link zur Anwendung oder die **Benutzerzugriffs-URL** auf der Seite **Eigenschaften** der Anwendung verfügen. 

Diese Einstellung hat keine Auswirkung darauf, ob eine Anwendung in „Meine Apps“ angezeigt wird. Anwendungen werden im Zugriffsbereich „Meine Apps“ von Benutzern angezeigt, sobald Sie der Anwendung einen Benutzer oder eine Gruppe zugewiesen haben. Hintergrundinformationen finden Sie unter [Verwaltung des Zugriffs auf Apps](what-is-access-management.md).

So fordern Sie eine Benutzerzuweisung für eine Anwendung an
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Administratorkonto oder als Besitzer der Anwendung an.
2. Wählen Sie **Azure Active Directory** aus. Wählen Sie im linken Navigationsmenü **Unternehmensanwendungen** aus.
3. Wählen Sie die Anwendung in der Liste aus. Wenn die Anwendung nicht angezeigt wird, beginnen Sie mit der Eingabe ihres Namens in das Suchfeld. Oder verwenden Sie die Filtersteuerelemente, um den Anwendungstyp, Status oder die Sichtbarkeit auszuwählen, und wählen Sie dann **Übernehmen** aus.
4. Wählen Sie im Navigationsmenü auf der linken Seite **Eigenschaften** aus.
5. Vergewissern Sie sich, dass **Benutzerzuweisung erforderlich?** auf **Ja** festgelegt ist.
   > [!NOTE]
   > Wenn der Umschalter **Benutzerzuweisung erforderlich?** nicht verfügbar ist, können Sie mit PowerShell die Eigenschaft appRoleAssignmentRequired auf den Dienstprinzipal festlegen.
6. Wählen Sie oben auf der Seite die Schaltfläche **Speichern** aus.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Zuweisen von Benutzern und Gruppen für eine App und Aufheben ihrer Zuweisung über das Azure-Portal
Weitere Informationen zum Zuweisen eines Benutzers oder einer Gruppe und das Aufheben ihrer Zuweisung über das Azure-Portal finden Sie in der [Schnellstartreihe zur Anwendungsverwaltung](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Zuweisen von Benutzern und Gruppen für eine App und Aufheben ihrer Zuweisung mit der Graph-API
Mit der Graph-API können Sie einer App Benutzer und Gruppen zuweisen und die Zuweisung aufheben. Weitere Informationen finden Sie unter [App-Rollenzuweisungen](/graph/api/resources/approleassignment).

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Zuweisen von Benutzern und Gruppen zu einer App mithilfe von PowerShell
1. Öffnen Sie eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten.
   > [!NOTE]
   > Sie müssen das AzureAD-Modul installieren (verwenden Sie den Befehl `Install-Module -Name AzureAD`). Wenn Sie zum Installieren eines NuGet-Moduls oder des neuen Azure Active Directory V2-PowerShell-Moduls aufgefordert werden, geben Sie „J“ ein, und drücken Sie die EINGABETASTE.
2. Führen Sie `Connect-AzureAD` aus, und melden Sie sich mit dem Benutzerkonto eines globalen Administrators an.
3. Weisen Sie mithilfe des folgenden Skripts einer Anwendung einen Benutzer und eine Rolle zu:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
Weitere Informationen zum Zuweisen eines Benutzers zu einer Anwendungsrolle finden Sie in der Dokumentation zu [New-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment).

Wenn Sie eine Gruppe einer Unternehmens-App zuweisen möchten, müssen Sie `Get-AzureADUser` durch `Get-AzureADGroup` und `New-AzureADUserAppRoleAssignment` durch `New-AzureADGroupAppRoleAssignment` ersetzen.

Weitere Informationen zum Zuweisen einer Gruppe zu einer Anwendungsrolle finden Sie in der Dokumentation zu [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment).

### <a name="example"></a>Beispiel

In diesem Beispiel wird die Benutzerin Britta Simon mithilfe von PowerShell der Anwendung [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) zugewiesen.

1. Weisen Sie in PowerShell den Variablen „$username“, „$app_name“ und „$app_role_name“ die entsprechenden Werte zu.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. In diesem Beispiel kennen wir den genauen Namen der Anwendungsrolle nicht, die wir Britta Simon zuweisen möchten. Führen Sie die folgenden Befehle aus, um den Benutzer ($user) und den Dienstprinzipal ($sp) abzurufen. Verwenden Sie dabei die Anzeigenamen für den Benutzer-UPN und den Dienstprinzipal.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Führen Sie den Befehl `$sp.AppRoles` aus, um die für die Workplace Analytics-Anwendung verfügbaren Rollen anzuzeigen. In diesem Beispiel möchten wir Britta Simon die Rolle „Analyst (Limited access)“ (Analyst (Eingeschränkter Zugriff)) zuweisen.
   ![Zeigt die für Benutzer mit Workplace Analytics-Rolle verfügbaren Rollen an](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Weisen Sie den Rollennamen der Variablen `$app_role_name` zu.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Führen Sie den folgenden Befehl aus, um den Benutzer der App-Rolle zuzuweisen:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Aufheben der Zuweisung von Benutzern und Gruppen zu einer App mithilfe von PowerShell

1. Öffnen Sie eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten.
   > [!NOTE]
   > Sie müssen das AzureAD-Modul installieren (verwenden Sie den Befehl `Install-Module -Name AzureAD`). Wenn Sie zum Installieren eines NuGet-Moduls oder des neuen Azure Active Directory V2-PowerShell-Moduls aufgefordert werden, geben Sie „J“ ein, und drücken Sie die EINGABETASTE.
2. Führen Sie `Connect-AzureAD` aus, und melden Sie sich mit dem Benutzerkonto eines globalen Administrators an.
3. Entfernen Sie mithilfe des folgenden Skripts einen Benutzer und eine Rolle aus einer Anwendung:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```


## <a name="related-articles"></a>Verwandte Artikel

- [Weitere Informationen zum Endbenutzerzugriff auf Anwendungen](end-user-experiences.md)
- [Planen einer Bereitstellung über „Meine Apps“ in Azure AD](access-panel-deployment-plan.md)
- [Verwalten des Zugriffs auf Apps](what-is-access-management.md)
 
## <a name="next-steps"></a>Nächste Schritte

- [Alle meine Gruppen anzeigen](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App]()
- [Deaktivieren von Benutzeranmeldungen für eine Unternehmens-App](disable-user-sign-in-portal.md)
- [Ändern des Namens oder Logos einer Unternehmens-App](./add-application-portal-configure.md)
