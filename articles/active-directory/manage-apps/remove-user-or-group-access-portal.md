---
title: Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App in Azure Active Directory | Microsoft-Dokumentation
description: Entfernen der Zugriffszuweisung eines Benutzers oder einer Gruppe aus einer Unternehmens-App in Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ad995db26b67f365cd8ed9b2aeba5e47fe9c01
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826067"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Entfernen einer Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App in Azure Active Directory
In Azure Active Directory (Azure AD) können Sie auf einfache Weise eine Benutzer- oder Gruppenzuweisung für den Zugriff auf eine Ihrer Unternehmensanwendungen entfernen. Sie müssen über die entsprechenden Berechtigungen zum Verwalten der Unternehmens-App verfügen. Außerdem müssen Sie ein globaler Administrator für das Verzeichnis sein.

> [!NOTE]
> Verwenden Sie für Microsoft-Anwendungen (etwa Office 365-Apps) PowerShell, um Benutzer aus einer Unternehmens-App zu entfernen.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Wie entferne ich im Azure-Portal eine Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App?
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) über ein Konto an, das als globaler Administrator für das Verzeichnis konfiguriert ist.
1. Wählen Sie **Alle Dienste** aus, geben Sie **Azure Active Directory** in das Textfeld ein, und drücken Sie die **EINGABETASTE**.
1. Wählen Sie auf der Seite **Azure Active Directory – *Verzeichnisname*** (also der Azure AD-Seite für das Verzeichnis, das Sie verwalten) die Option **Unternehmensanwendungen** aus.
1. Auf der Seite **Unternehmensanwendungen – Alle Anwendungen** wird eine Liste der Apps angezeigt, die Sie verwalten können. Wählen Sie eine App aus.
1. Wählen Sie auf der Übersichtsseite ***App-Name*** (also der Seite mit dem Namen der ausgewählten App im Titel) die Option **Benutzer und Gruppen** aus.
1. Wählen Sie auf der Seite ***App-Name*** **– Benutzer- und Gruppenzuweisung** mindestens einen Benutzer oder eine Gruppe aus, und klicken Sie dann auf den Befehl **Entfernen**. Bestätigen Sie Ihre Entscheidung, wenn Sie dazu aufgefordert werden.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Wie entferne ich mithilfe von PowerShell eine Benutzer- oder Gruppenzuweisung aus einer Unternehmens-App?
1. Öffnen Sie eine Windows PowerShell-Eingabeaufforderung mit erhöhten Rechten.

    >[!NOTE] 
    > Sie müssen das AzureAD-Modul installieren (verwenden Sie den Befehl `Install-Module -Name AzureAD`). Wenn Sie zum Installieren eines NuGet-Moduls oder des neuen Azure Active Directory V2-PowerShell-Moduls aufgefordert werden, geben Sie „J“ ein, und drücken Sie die EINGABETASTE.

1. Führen Sie `Connect-AzureAD` aus, und melden Sie sich mit dem Benutzerkonto eines globalen Administrators an.
1. Entfernen Sie mithilfe des folgenden Skripts einen Benutzer und eine Rolle aus einer Anwendung:

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
   ## <a name="next-steps"></a>Nächste Schritte

- [Alle meine Gruppen anzeigen](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](assign-user-or-group-access-portal.md)
- [Deaktivieren von Benutzeranmeldungen für eine Unternehmens-App](disable-user-sign-in-portal.md)
- [Ändern des Namens oder Logos einer Unternehmens-App](change-name-or-logo-portal.md)
