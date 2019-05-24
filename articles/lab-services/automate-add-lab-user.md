---
title: Automatisieren des Hinzufügens eines Lab-Benutzers in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure DevTest Labs das Hinzufügen eines Lab-Benutzers zu einem Lab automatisieren können.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 2ad81ae97414abbf3266cc5728febf9abe836151
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522961"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatisieren des Hinzufügens eines Lab-Benutzers zu einem Lab in Azure DevTest Labs
Mit Azure DevTest Labs können Sie über das Azure-Portal schnell Self-Service-DevTest-Umgebungen erstellen. Wenn Sie jedoch über mehrere Teams und mehrere DevTest Labs-Instanzen verfügen, kann die Automatisierung des Erstellungsprozesses Zeit sparen. [Azure Resource Manager-Vorlagen](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) ermöglichen Ihnen das Erstellen von Labs, Lab-VMs, benutzerdefinierten Images, Formeln sowie das Hinzufügen von Benutzern auf automatisierte Weise. Dieser Artikel befasst sich insbesondere mit dem Hinzufügen von Benutzern zu einer DevTest Labs-Instanz.

Um einen Benutzer einem Lab hinzuzufügen, fügen Sie den Benutzer der Rolle **DevTest Labs-Benutzer** für das Lab hinzu. In diesem Artikel erfahren Sie, wie Sie das Hinzufügen eines Benutzers zu einem Lab mit einer der folgenden Methoden automatisieren können:

- Azure-Ressourcen-Manager-Vorlagen
- Azure PowerShell-Cmdlets 
- Azure-Befehlszeilenschnittstelle.

## <a name="use-azure-resource-manager-templates"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen
Die folgende Resource Manager-Beispielvorlage gibt an, dass ein Benutzer der Rolle **DevTest Labs-Benutzer** eines Labs hinzugefügt werden soll. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Wenn Sie die Rolle in der gleichen Vorlage zuweisen, in der auch das Lab erstellt wird, denken Sie daran, eine Abhängigkeit zwischen der Ressourcenzuordnung der Rolle und dem Lab hinzuzufügen. Weitere Informationen finden Sie im Artikel [Definieren von Abhängigkeiten in Azure Resource Manager-Vorlagen](../azure-resource-manager/resource-group-define-dependencies.md).

### <a name="role-assignment-resource-information"></a>Informationen der Rollenzuweisungsressource
Die Rollenzuweisungsressource muss den Typ und den Namen angeben.

Zunächst ist zu beachten, dass der Typ für die Ressource nicht `Microsoft.Authorization/roleAssignments` ist, wie es für eine Ressourcengruppe der Fall wäre.  Stattdessen folgt der Ressourcentyp dem Muster `{provider-namespace}/{resource-type}/providers/roleAssignments`. In diesem Fall ist der Ressourcentyp `Microsoft.DevTestLab/labs/providers/roleAssignments`.

Der Rollenzuweisungsname selbst muss global eindeutig sein.  Der Name der Zuweisung verwendet das Muster `{labName}/Microsoft.Authorization/{newGuid}`. `newGuid` ist ein Parameterwert für die Vorlage. Er stellt sicher, dass der Rollenzuweisungsname eindeutig ist. Da keine Vorlagenfunktionen zum Erstellen von GUIDs vorhanden sind, müssen Sie selbst mit einem GUID-Generatortool eine GUID erstellen.  

In der Vorlage wird der Namen für die Rollenzuweisung durch die Variable `fullDevTestLabUserRoleName` definiert. In der Vorlage lautet die Zeile folgendermaßen:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Eigenschaften der Rollenzuweisungsressource
Eine Rollenzuweisung selbst definiert drei Eigenschaften. Die `roleDefinitionId`, die `principalId`, und der `scope` sind erforderlich.

### <a name="role-definition"></a>Rollendefinition
Die Rollendefinitions-ID ist der Zeichenfolgenbezeichner für die vorhandene Rollendefinition. Die Rollen-ID weist das Format `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}` auf. 

Die Abonnement-ID wird mit der Vorlagenfunktion `subscription().subscriptionId` abgerufen.  

Sie müssen die Rollendefinition für die integrierte Rolle `DevTest Labs User` abrufen. Zum Abrufen der GUID für die Rolle [DevTest Labs-Benutzer](../role-based-access-control/built-in-roles.md#devtest-labs-user) können Sie die [Rollenzuweisungs-REST-API](/rest/api/authorization/roleassignments) oder das Cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) verwenden.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

Die Rollen-ID wird im Abschnitt „Variables“ definiert und trägt den Namen `devTestLabUserRoleId`. In der Vorlage wird die Rollen-ID auf Folgendes festgelegt: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Prinzipal-ID
Die Prinzipal-ID ist die Objekt-ID des Active Directory-Benutzers, der -Gruppe oder des -Dienstprinzipals, den bzw. die Sie dem Lab als Lab-Benutzer hinzufügen möchten. Die Vorlage verwendet die `ObjectId` als Parameter.

Sie können die Objekt-ID mit den PowerShell-Cmdlets [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), „[Get-AzureRMADGroup“ oder [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) abrufen. Diese Cmdlets geben ein einzelnes Active Directory-Objekt oder eine Liste von Active Directory-Objekten zurück, die eine ID-Eigenschaft aufweisen, die die Objekt-ID ist, die Sie benötigen. Das folgende Beispiel zeigt Ihnen, wie Sie die Objekt-ID eines einzelnen Benutzers in einem Unternehmen abrufen können.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Sie können auch die Azure Active Directory PowerShell-Cmdlets verwenden, beispielsweise [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0) und [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>`Scope`
Der Bereich gibt die Ressource oder Ressourcengruppe an, die für die Rollenzuweisung gelten soll. Für Ressourcen weist der Bereich das folgende Format auf: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. Die Vorlage verwendet die `subscription().subscriptionId`-Funktion zum Auffüllen der `subscription-id` und die `resourceGroup().name`-Vorlagenfunktion zum Auffüllen des `resource-group-name`. Die Verwendung dieser Funktionen bedeutet, dass das Lab, dem Sie eine Rolle zuweisen, im aktuellen Abonnement und in der gleichen Ressourcengruppe vorhanden sein muss, in der die Bereitstellung der Vorlage erfolgt. Der letzte Teil (`resource-name`) ist der Name des Labs. Dieser Wert wird in diesem Beispiel über den Vorlagenparameter empfangen. 

Der Rollenbereich in der Vorlage: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Bereitstellen der Vorlage
Erstellen Sie zunächst eine Parameterdatei (z.B. „azuredeploy.parameters.json“), die Werte für Parameter in der Resource Manager-Vorlage übergibt. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Verwenden Sie dann das PowerShell-Cmdlet [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0), um die Resource Manager-Vorlage bereitzustellen. Der folgende Beispielbefehl weist der Rolle „DevTest Labs-Benutzer“ für ein Lab eine Person, eine Gruppe oder einen Dienstprinzipal zu.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Es ist wichtig zu beachten, dass der Name der Gruppenbereitstellung und die GUID für die Rollenzuweisung eindeutig sein müssen. Wenn Sie versuchen, eine Ressourcenzuordnung mit einer nicht eindeutigen GUID bereitzustellen, erhalten Sie einen `RoleAssignmentUpdateNotPermitted`-Fehler.

Wenn Sie planen, die Vorlage mehrmals zu verwenden, um mehrere Active Directory-Objekte der Rolle „DevTest Labs-Benutzer“ für Ihr Lab hinzuzufügen, sollten Sie die Verwendung dynamischer Objekte in Ihrem PowerShell-Befehl in Betracht ziehen. Das folgende Beispiel verwendet das Cmdlet [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0), um den Namen des Ressourcengruppenbereitstellung und die GUID der Rollenzuweisung dynamisch anzugeben.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell
Wie in der Einleitung erläutert, erstellen Sie eine neue Azure-Rollenzuweisung, um einen Benutzer der Rolle **DevTest Labs-Benutzer** für das Lab hinzuzufügen. In PowerShell verwenden Sie zu diesem Zweck das Cmdlet [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0). Dieses Cmdlet verfügt über viele optionale Parameter, um Flexibilität zu ermöglichen. `ObjectId`, `SigninName` oder `ServicePrincipalName` kann als das Objekt angegeben werden, dem Berechtigungen erteilt werden.  

Hier ist ein Beispiel für einen Azure PowerShell-Befehl, der einen Benutzer der Rolle „DevTest Labs-Benutzer“ im angegebenen Lab hinzufügt.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Um die Ressource anzugeben, der Berechtigungen erteilt werden, kann eine Kombination von `ResourceName`, `ResourceType` und `ResourceGroup` oder der Parameter `scope` verwendet werden. Unabhängig von der Kombination der verwendeten Parameter stellen Sie dem Cmdlet genügend Informationen zur Verfügung, um das Active Directory-Objekt (Benutzer, Gruppe oder Dienstprinzipal), den Bereich (Ressourcengruppe oder Ressource) und die Rollendefinition eindeutig zu identifizieren.

## <a name="use-azure-command-line-interface-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle (CLI)
In der Azure CLI erfolgt das Hinzufügen eines Labs-Benutzers zu einem Lab mithilfe des Befehls `az role assignment create`. Weitere Informationen zu Cmdlets der Azure CLI finden Sie unter [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und der Azure CLI](../role-based-access-control/role-assignments-cli.md).

Das Objekt, dem Zugriff erteilt wird, kann mit den Parametern `objectId`, `signInName` und `spn` angegeben werden. Das Lab, auf das dem Objekt Zugriff erteilt wird, kann durch die `scope`-URL oder eine Kombination der Parameter `resource-name`, `resource-type` und `resource-group` identifiziert werden.

Das folgende Azure CLI-Beispiel zeigt Ihnen, wie Sie eine Person der Rolle „DevTest Labs-Benutzer“ für das angegebene Lab hinzufügen können.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln:

- [Erstellen und Verwalten virtueller Computer in DevTest Labs mit der Azure CLI](devtest-lab-vmcli.md)
- [Erstellen einer VM in DevTest Labs mithilfe von Azure PowerShell](devtest-lab-vm-powershell.md)
- [Starten und Beenden von virtuellen Computern in Azure DevTest Labs mit Befehlszeilentools](use-command-line-start-stop-virtual-machines.md)

