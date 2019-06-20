---
title: 'Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für einen Benutzer mit RBAC und einer Resource Manager-Vorlage | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einem Benutzer mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) Zugriff auf Azure-Ressourcen gewähren, indem Sie eine Azure Resource Manager-Vorlage verwenden.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control,azure-resource-manager
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: e99a9d2cfa38c9b2ea74f9075b18f81006b34881
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65802749"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für einen Benutzer mit RBAC und einer Resource Manager-Vorlage

Der Zugriff auf Azure-Ressourcen wird mithilfe der [rollenbasierten Zugriffssteuerung (RBAC)](overview.md) verwaltet. In diesem Tutorial erstellen Sie eine Ressourcengruppe und gewähren einem Benutzer Zugriff, um virtuelle Computer in dieser Ressourcengruppe zu erstellen und zu verwalten. In diesem Tutorial geht es schwerpunktmäßig um den Prozess der Bereitstellung einer Resource Manager-Vorlage, um den Zugriff zu gewähren. Weitere Informationen zur Entwicklung von Resource Manager-Vorlagen finden Sie in der [Resource Manager-Dokumentation](/azure/azure-resource-manager/) und der [Vorlagenreferenz](/azure/templates/microsoft.authorization/allversions
).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Gewähren des Zugriffs für einen Benutzer im Ressourcengruppenbereich
> * Überprüfen der Bereitstellung
> * Bereinigen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um Rollenzuweisungen hinzufügen und entfernen zu können, benötigen Sie folgende Berechtigungen:

* `Microsoft.Authorization/roleAssignments/write`- und `Microsoft.Authorization/roleAssignments/delete`-Berechtigungen, wie z.B. [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) oder [Besitzer](built-in-roles.md#owner)

## <a name="grant-access"></a>Gewähren von Zugriff

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Weitere auf die Azure-Autorisierung bezogene Vorlagen finden Sie [hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Wählen Sie zum Bereitstellen der Vorlage **Jetzt testen** aus, um die Azure Cloud Shell zu öffnen, und fügen Sie anschließend das folgende PowerShell-Skript in das Shellfenster ein. Klicken Sie zum Einfügen des Codes mit der rechten Maustaste auf das Shell-Fenster, und wählen Sie **Einfügen** aus.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Öffnen Sie die Ressourcengruppe, die Sie im letzten Verfahren erstellt haben. Der Standardname ist der Projektname mit dem Zusatz **rg**.
1. Wählen Sie im linken Menü **Zugriffssteuerung (IAM)** .
1. Wählen Sie **Rollenzuweisungen** aus. 
1. Geben Sie in **Name** die E-Mail-Adresse ein, die Sie im letzten Verfahren eingegeben haben. Sie sehen dann, dass der Benutzer mit der E-Mail-Adresse die Rolle **Mitwirkender für virtuelle Computer** besitzt.

## <a name="clean-up"></a>Bereinigen

Um die im letzten Verfahren erstellte Ressourcengruppe zu entfernen, wählen Sie **Jetzt testen** aus, um die Azure Cloud Shell zu öffnen, und fügen Sie anschließend das folgende PowerShell-Skript in das Shellfenster ein.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für einen Benutzer mit RBAC und Azure PowerShell](tutorial-role-assignments-user-powershell.md)