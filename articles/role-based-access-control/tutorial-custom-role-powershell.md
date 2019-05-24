---
title: 'Tutorial: Erstellen einer benutzerdefinierten Rolle für Azure-Ressourcen mit Azure PowerShell | Microsoft-Dokumentation'
description: Erste Schritte beim Erstellen einer benutzerdefinierten Rolle für Azure-Ressourcen mithilfe von Azure PowerShell
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: 269bd74aca85ddbc2bafda30542c48f8ab391b32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158853"
---
# <a name="tutorial-create-a-custom-role-for-azure-resources-using-azure-powershell"></a>Tutorial: Erstellen einer benutzerdefinierten Rolle für Azure-Ressourcen mithilfe von Azure PowerShell

Wenn die [integrierten Rollen für Azure-Ressourcen](built-in-roles.md) den Ansprüchen Ihrer Organisation nicht entsprechen, können Sie Ihre eigenen benutzerdefinierten Rollen erstellen. In diesem Tutorial erstellen Sie mithilfe von Azure PowerShell eine benutzerdefinierte Rolle namens „Reader Support Tickets“. Die benutzerdefinierte Rolle ermöglicht dem Benutzer, alle Inhalte der Verwaltungsebene eines Abonnement anzuzeigen und auch Supporttickets zu erstellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer benutzerdefinierten Rolle
> * Auflisten benutzerdefinierter Rollen 
> * Aktualisieren einer benutzerdefinierten Rolle
> * Löschen einer benutzerdefinierten Rolle

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Um dieses Lernprogramm abzuschließen, benötigen Sie:

- Berechtigungen zum Erstellen von benutzerdefinierten Rollen, etwa [Besitzer](built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) oder [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Anmelden bei Azure PowerShell

Melden Sie sich bei [Azure PowerShell](/powershell/azure/authenticate-azureps) an.

## <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Zum Erstellen einer benutzerdefinierten Rolle beginnen Sie am besten mit einer integrierten Rolle, bearbeiten sie und erstellen dann eine neue Rolle.

1. Verwenden Sie in PowerShell den Befehl [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation), um die Liste der Vorgänge für den Ressourcenanbieter „Microsoft.Support“ abzurufen. Es ist hilfreich, die Vorgänge zu kennen, die zum Erstellen der Berechtigungen verfügbar sind. Eine Liste der verfügbaren Vorgänge finden Sie auch unter [Vorgänge für Azure Resource Manager-Ressourcenanbieter](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Verwenden Sie den Befehl [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition), um die Rolle [Leser](built-in-roles.md#reader) im JSON-Format auszugeben.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Öffnen Sie die Datei **ReaderSupportRole.json** in einem Editor.

    Die JSON-Ausgabe sieht wie folgt aus. Informationen zu den verschiedenen Eigenschaften finden Sie unter [Erstellen von benutzerdefinierten Rollen in Azure](custom-roles.md).

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. Bearbeiten Sie die JSON-Datei, um den Vorgang `"Microsoft.Support/*"` zur Eigenschaft `Actions` hinzuzufügen. Achten Sie darauf, ein Komma nach dem Lesevorgang einzufügen. Diese Aktion ermöglicht dem Benutzer das Erstellen von Supporttickets.

1. Rufen Sie die ID Ihres Abonnements mithilfe des Befehls [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) ab.

    ```azurepowershell
    Get-AzSubscription
    ```

1. Fügen Sie in `AssignableScopes` Ihre Abonnement-ID im folgenden Format ein: `"/subscriptions/00000000-0000-0000-0000-000000000000"`.

    Sie müssen explizite Abonnement-IDs hinzufügen. Andernfalls können Sie die Rolle nicht in Ihr Abonnement importieren.

1. Löschen Sie die Zeile mit der Eigenschaft `Id`, und ändern Sie die Eigenschaft `IsCustom` in `true`.

1. Ändern Sie die Eigenschaften `Name` und `Description` in „Reader Support Tickets“ und „View everything in the subscription and also open support tickets“.

    Ihre JSON-Datei sollte wie folgt aussehen:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Zum Erstellen der neuen benutzerdefinierten Rolle verwenden Sie den Befehl [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) und geben die JSON-Rollendefinitionsdatei an.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Die neue benutzerdefinierte Rolle ist jetzt im Azure-Portal verfügbar und kann wie integrierte Rollen Benutzern, Gruppen oder Dienstprinzipalen zugewiesen werden.

## <a name="list-custom-roles"></a>Auflisten benutzerdefinierter Rollen 

- Verwenden Sie zum Auflisten aller benutzerdefinierten Rollen den Befehl [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Sie können die benutzerdefinierte Rolle auch im Azure-Portal anzeigen.

    ![Screenshot der in das Azure-Portal importierten benutzerdefinierten Rolle](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Aktualisieren einer benutzerdefinierten Rolle

Um die benutzerdefinierte Rolle zu aktualisieren, können Sie die JSON-Datei aktualisieren oder das Objekt `PSRoleDefinition` verwenden.

1. Verwenden Sie zum Aktualisieren der JSON-Datei den Befehl [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition), um die benutzerdefinierte Rolle im JSON-Format auszugeben.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Öffnen Sie die Datei in einem Editor.

1. Fügen Sie in `Actions` den Vorgang zum Erstellen und Verwalten von Ressourcengruppenbereitstellungen (`"Microsoft.Resources/deployments/*"`) hinzu.

    Ihre aktualisierte JSON-Datei sollte wie folgt aussehen:

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Führen Sie zum Aktualisieren der benutzerdefinierten Rolle den Befehl [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) aus, und geben Sie die aktualisierte JSON-Datei an.

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Wenn Sie zum Aktualisieren der benutzerdefinierten Rolle das Objekt `PSRoleDefintion` verwenden möchten, führen Sie zuerst den Befehl [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) aus, um die Rolle abzurufen.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Rufen Sie die Methode `Add` auf, um den Vorgang zum Lesen der Diagnoseeinstellungen hinzuzufügen.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Aktualisieren Sie die Rolle mithilfe von [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition).

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Löschen einer benutzerdefinierten Rolle

1. Verwenden Sie zum Abrufen der ID der benutzerdefinierten Rolle den Befehl [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. Verwenden Sie den Befehl [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition), und geben Sie die Rollen-ID an, um die benutzerdefinierte Rolle zu löschen.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Wenn Sie zum Bestätigen aufgefordert werden, geben Sie **Y** ein.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen benutzerdefinierter Rollen für Azure-Ressourcen mithilfe von Azure PowerShell](custom-roles-powershell.md)
