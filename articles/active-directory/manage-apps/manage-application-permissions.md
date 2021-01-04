---
title: Verwalten von Benutzer- und Administratorberechtigungen – Azure Active Directory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Berechtigungen für Ihre Anwendung in Azure AD überprüfen und verwalten. Dazu zählt beispielsweise auch das Widerrufen aller Berechtigungen, die einer Anwendung erteilt wurden.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ff97d0a69efbe624e959f92f5320f921476a306
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658977"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Maßnahmen bei verdächtigen Anwendungen oder Anwendungen mit zu vielen Berechtigungen in Azure Active Directory

Hier erfahren Sie, wie Sie Anwendungsberechtigungen überprüfen und verwalten. In diesem Artikel werden verschiedene Aktionen vorgestellt, die Sie entsprechend des jeweiligen Szenarios zum Schutz Ihrer Anwendung ausführen können. Diese Aktionen gelten für alle Anwendungen, die Ihrem Azure Active Directory (Azure AD)-Mandanten per Benutzer- oder Administratoreinwilligung hinzugefügt wurden.

Weitere Informationen zur Einwilligung für Anwendungen finden Sie unter [Azure Active Directory-Zustimmungsframework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der folgenden Aktionen müssen Sie sich als globaler Administrator, als Anwendungsadministrator oder als Cloudanwendungsadministrator anmelden.

Um den Zugriff auf Anwendungen zu beschränken, benötigen Sie die Benutzerzuweisung, und weisen Sie dann Benutzer oder Gruppen der Anwendung zu.  Weitere Informationen finden Sie unter [Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory](./assign-user-or-group-access-portal.md).

Über das Azure AD-Portal können Sie kontextbezogene PowerShell-Skripts abrufen, um die Aktionen auszuführen.
 
1. Melden Sie sich globaler Administrator, als Anwendungsadministrator oder als Cloudanwendungsadministrator beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** aus.
3. Wählen Sie die Anwendung aus, für die ein eingeschränkter Zugriff eingerichtet werden sollen.
4. Wählen Sie **Berechtigungen** aus. Klicken Sie in der Befehlsleiste auf **Berechtigungen überprüfen**.

![Screenshot des Fensters „Berechtigungen überprüfen“](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Steuern des Zugriffs auf eine Anwendung

Wir empfehlen, den Zugriff auf eine Anwendung durch Aktivieren der Einstellung **Benutzerzuweisung** einzuschränken.

1. Melden Sie sich globaler Administrator, als Anwendungsadministrator oder als Cloudanwendungsadministrator beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** aus.
3. Wählen Sie die Anwendung aus, für die ein eingeschränkter Zugriff eingerichtet werden sollen.
4. Wählen Sie **Eigenschaften** aus, und legen Sie **Benutzerzuweisung erforderlich** auf **Ja** fest.
5. Wählen Sie **Benutzer und Gruppen** aus, und entfernen Sie dann Benutzer, die der Anwendung zugewiesen, aber unerwünscht sind.
6. Weisen Sie der Anwendung Benutzer oder Gruppen zu.

Optional können Sie auch mithilfe von PowerShell alle Benutzer entfernen, die der Anwendung zugewiesen sind.

## <a name="revoke-all-permissions-for-an-application"></a>Widerrufen aller Berechtigungen für eine Anwendung

Mit dem PowerShell-Skript werden alle Berechtigungen widerrufen, die der jeweiligen Anwendung erteilt wurden.

> [!NOTE]
> Durch das Widerrufen aktueller Berechtigungen wird eine erneute Benutzereinwilligung für die Anwendung nicht verhindert. Wenn Sie die Benutzereinwilligung blockieren möchten, lesen Sie [Konfigurieren der Benutzereinwilligung für Anwendungen](configure-user-consent.md).

Optional können Sie die Anwendung deaktivieren, um den Zugriff von Benutzern auf die App und den Zugriff der App auf Ihre Daten zu verhindern.

1. Melden Sie sich globaler Administrator, als Anwendungsadministrator oder als Cloudanwendungsadministrator beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** aus.
3. Wählen Sie die Anwendung aus, für die ein eingeschränkter Zugriff eingerichtet werden sollen.
4. Wählen Sie **Eigenschaften** aus, und legen Sie dann **Aktiviert für die Benutzeranmeldung?** auf **Nein** fest.

## <a name="investigate-a-suspicious-application"></a>Untersuchen einer verdächtigen Anwendung

Wir empfehlen, den Zugriff auf die Anwendung durch Aktivieren der Einstellung **Benutzerzuweisung** einzuschränken. Überprüfen Sie dann die Berechtigungen, die Benutzer und Administratoren der Anwendung erteilt haben.

1. Melden Sie sich globaler Administrator, als Anwendungsadministrator oder als Cloudanwendungsadministrator beim [Azure-Portal](https://portal.azure.com) an.
3. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** aus.
5. Wählen Sie die Anwendung aus, für die ein eingeschränkter Zugriff eingerichtet werden sollen.
6. Wählen Sie **Eigenschaften** aus, und legen Sie **Benutzerzuweisung erforderlich** auf **Ja** fest.
7. Wählen Sie **Berechtigungen** aus, und überprüfen Sie die per Administrator- und Benutzereinwilligung erteilten Berechtigungen.

Optional können Sie mithilfe von PowerShell folgende Aktionen ausführen:

- Entfernen aller zugewiesenen Benutzer, um eine erneute Anmeldung bei der Anwendung zu verhindern
- Annullieren der Aktualisierungstoken für die Benutzer, die auf die Anwendung zugreifen können
- Widerrufen aller Berechtigungen für die Anwendung

Alternativ können Sie auch die Anwendung deaktivieren, um den Zugriff von Benutzern auf die Anwendung und den Zugriff der Anwendung auf Ihre Daten zu verhindern.


## <a name="disable-a-malicious-application"></a>Deaktivieren einer bösartigen Anwendung 

Wir empfehlen, die Anwendung zu deaktivieren, um den Benutzerzugriff zu blockieren und den Zugriff der Anwendung auf Ihre Daten zu verhindern. Wenn Sie stattdessen die Anwendung löschen, können Benutzer der Anwendung erneut zustimmen und Zugriff auf Ihre Daten gewähren.

1. Melden Sie sich globaler Administrator, als Anwendungsadministrator oder als Cloudanwendungsadministrator beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** aus.
3. Wählen Sie die Anwendung aus, für die ein eingeschränkter Zugriff eingerichtet werden sollen.
4. Wählen Sie **Eigenschaften** aus, und kopieren Sie dann die Objekt-ID.

### <a name="powershell-commands"></a>PowerShell-Befehle


Rufen Sie die Objekt-ID des Dienstprinzipals ab.

1. Melden Sie sich globaler Administrator, als Anwendungsadministrator oder als Cloudanwendungsadministrator beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** aus.
3. Wählen Sie die Anwendung aus, für die ein eingeschränkter Zugriff eingerichtet werden sollen.
4. Wählen Sie **Eigenschaften** aus, und kopieren Sie dann die Objekt-ID.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
Entfernen Sie alle Benutzer, die der Anwendung zugewiesen sind.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

Widerrufen Sie die Berechtigungen, die der Anwendung erteilt wurden.

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
Machen Sie die Aktualisierungstoken ungültig.
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>Nächste Schritte
- [Verwalten der Einwilligung zu Anwendungen und Auswerten von Einwilligungsanforderungen](manage-consent-requests.md)
- [Konfigurieren der Benutzereinwilligung](configure-user-consent.md)
- [Konfigurieren des Workflows für die Administratoreinwilligung](configure-admin-consent-workflow.md)