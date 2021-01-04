---
title: 'ML Studio (Classic): Bereitstellen von Arbeitsbereichen mit Azure Resource Manager (Azure)'
description: Bereitstellen eines Arbeitsbereichs für Azure Machine Learning Studio (klassisch) mit Azure Resource Manager-Vorlagen
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-azurepowershell
ms.date: 02/05/2018
ms.openlocfilehash: f860a52691d4d7fe72cbd7c8989277b224214064
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322773"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Bereitstellen eines Azure Machine Learning Studio-Arbeitsbereichs (klassisch) mithilfe von Azure Resource Manager

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Mithilfe einer Azure Resource Manager-Bereitstellungsvorlage verfügen Sie über eine zeitsparende, skalierbare Möglichkeit, um miteinander verbundene Komponente mit einem Mechanismus zum Überprüfen und Wiederholen bereitzustellen. Zum Einrichten eines Azure Machine Learning Studio-Arbeitsbereichs (klassisch) müssen Sie beispielsweise zuerst ein Azure Storage-Konto konfigurieren und anschließend Ihren Arbeitsbereich bereitstellen. Stellen Sie sich vor, Sie müssen diese Schritte manuell für Hunderte von Arbeitsbereichen ausführen. Eine einfachere Alternative besteht in der Verwendung einer Azure Resource Manager-Vorlage, um einen Studio-Arbeitsbereich (klassisch) mit allen Abhängigkeiten bereitzustellen. In diesem Artikel finden Sie eine schrittweise Anleitung für diesen Prozess. Einen allgemeinen Überblick über Azure Resource Manager erhalten Sie unter [Übersicht über Azure Resource Manager](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Schritt für Schritt: Erstellen eines Machine Learning-Arbeitsbereichs
Wir erstellen eine Azure-Ressourcengruppe und stellen anschließend ein neues Azure Storage-Konto und einen neuen Azure Machine Learning Studio-Arbeitsbereich (klassisch) mithilfe einer Resource Manager-Vorlage bereit. Nach Abschluss der Bereitstellung drucken wir wichtige Informationen zu den erstellten Arbeitsbereichen aus (Primärschlüssel, workspaceID und URL des Arbeitsbereichs).

### <a name="create-an-azure-resource-manager-template"></a>Erstellen einer Azure Resource Manager-Vorlage

Für einen Machine Learning-Arbeitsbereich ist ein Azure-Speicherkonto erforderlich, um das verknüpfte Dataset zu speichern.
Die folgende Vorlage verwendet den Namen der Ressourcengruppe, um den Namen des Speicherkontos sowie des Arbeitsbereichs zu erzeugen.  Der Name des Speicherkontos wird beim Erstellen des Arbeitsbereichs auch als Eigenschaft verwendet.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Speichern Sie diese Vorlage als mlworkspace.json-Datei unter „c:\temp“.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Bereitstellen einer Ressourcengruppe ausgehend von der Vorlage

* Öffnen Sie PowerShell.
* Installieren Sie die Module für Azure Resource Manager und Azure Service Management.

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Mithilfe dieser Schritte werden die Module heruntergeladen und installiert, die zum Ausführen der verbleibenden Schritte erforderlich sind. Dies ist nur einmal in der Umgebung erforderlich, in der Sie die PowerShell-Befehle ausführen.

* Authentifizierung bei Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Dieser Schritt muss für jede Sitzung wiederholt werden. Nach der Authentifizierung sollten die Informationen zu Ihrem Abonnement angezeigt werden.

![Azure-Konto](./media/deploy-with-resource-manager-template/azuresubscription.png)

Da nun Zugriff auf Azure besteht, können wir die Ressourcengruppe erstellen.

* Erstellen einer Ressourcengruppe

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Prüfen Sie, ob die Ressourcengruppe korrekt bereitgestellt wurde. **ProvisioningState** sollte „Erfolgreich“ sein.
Der Name der Ressourcengruppe wird von der Vorlage verwendet, um den Namen des Speicherkontos zu erzeugen. Der Speicherkontoname muss zwischen 3 und 24 Zeichen lang sein und darf nur Zahlen und Kleinbuchstaben enthalten.

![Ressourcengruppe](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Erstellen Sie mithilfe der Ressourcengruppenbereitstellung einen neuen Machine Learning-Arbeitsbereich.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Nach Abschluss der Bereitstellung können Sie ganz einfach auf die Eigenschaften des bereitgestellten Arbeitsbereichs zugreifen. Sie können z.B. auf das Primärschlüsseltoken zugreifen.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Eine weitere Möglichkeit zum Abrufen von Token des vorhandenen Arbeitsbereich ist der Befehl „Invoke-AzResourceAction“. Beispielsweise können Sie die primären und sekundären Token aller Arbeitsbereiche auflisten.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Nach der Bereitstellung des Arbeitsbereichs können Sie viele Azure Machine Learning Studio-Aufgaben (klassisch) mithilfe des [PowerShell-Moduls für Azure Machine Learning Studio (klassisch)](https://aka.ms/amlps) automatisieren.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md).
* Werfen Sie auch einen Blick auf das [Azure-Schnellstartvorlagen-Repository](https://github.com/Azure/azure-quickstart-templates).
* Sehen Sie sich dieses Video zum [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39)an.
* Weitere Informationen finden Sie unter [Resource Manager template reference help](/azure/templates/microsoft.machinelearning/allversions) (Referenzhilfe zu Resource Manager-Vorlagen).

<!--Link references-->