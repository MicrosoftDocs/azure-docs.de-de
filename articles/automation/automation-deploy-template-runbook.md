---
title: Bereitstellen einer Azure Resource Manager-Vorlage in einem Azure Automation-Runbook
description: Bereitstellen einer in Azure Storage gespeicherten Azure Resource Manager-Vorlage aus einem Runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: PowerShell, Runbook, JSON, Azure Automation
ms.openlocfilehash: 2008ba697665baa0e8cf73564ec31d6267425404
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61073745"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Bereitstellen einer Azure Resource Manager-Vorlage in einem Azure Automation-PowerShell-Runbook

Sie können ein [Azure Automation-PowerShell-Runbook](automation-first-runbook-textual-powershell.md) schreiben, mit dem eine Azure-Ressource bereitgestellt wird, indem Sie eine [Azure Resource Manager-Vorlage](../azure-resource-manager/resource-manager-create-first-template.md) verwenden.

Auf diese Weise können Sie die Bereitstellung von Azure-Ressourcen automatisieren. Sie können Ihre Resource Manager-Vorlagen an einem zentralen, sicheren Ort verwalten, z.B. in Azure Storage.

In diesem Artikel erstellen wir ein PowerShell-Runbook, für das eine in [Azure Storage](../storage/common/storage-introduction.md) gespeicherte Resource Manager-Vorlage verwendet wird, um ein neues Azure Storage-Konto bereitzustellen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto registrieren](https://azure.microsoft.com/free/).
* [Automation-Konto](automation-sec-configure-azure-runas-account.md) dient zur Aufbewahrung des Runbooks und zur Authentifizierung gegenüber Azure-Ressourcen.  Dieses Konto muss über die Berechtigung zum Starten und Beenden des virtuellen Computers verfügen.
* Ein [Azure Storage-Konto](../storage/common/storage-create-storage-account.md), unter dem die Resource Manager-Vorlage gespeichert wird.
* Eine Installation von Azure PowerShell auf einem lokalen Computer. Weitere Informationen zum Erhalt von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).

## <a name="create-the-resource-manager-template"></a>Erstellen der Resource Manager-Vorlage

Für dieses Beispiel verwenden wir eine Resource Manager-Vorlage, mit der ein neues Azure Storage-Konto bereitgestellt wird.

Kopieren Sie in einem Text-Editor den folgenden Text:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Speichern Sie die Datei lokal als `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Speichern der Resource Manager-Vorlage in Azure Storage

Wir verwenden PowerShell jetzt zum Erstellen einer Azure Storage-Datei und zum Hochladen der Datei `TemplateTest.json`.
Eine Anleitung zum Erstellen einer Dateifreigabe und Hochladen einer Datei im Azure-Portal finden Sie unter [Erste Schritte mit Azure File Storage unter Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Starten Sie PowerShell auf Ihrem lokalen Computer, und führen Sie die folgenden Befehle aus, um eine Dateifreigabe zu erstellen und die Resource Manager-Vorlage auf diese Dateifreigabe hochzuladen.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Erstellen des PowerShell-Runbookskripts

Wir erstellen jetzt ein PowerShell-Skript, mit dem die Datei `TemplateTest.json` aus Azure Storage abgerufen und die Vorlage bereitgestellt wird, um ein neues Azure Storage-Konto zu erstellen.

Fügen Sie in einem Text-Editor den folgenden Text ein:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Speichern Sie die Datei lokal als `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importieren und Veröffentlichen des Runbooks im Azure Automation-Konto

Als Nächstes verwenden wir PowerShell, um das Runbook in Ihr Azure Automation-Konto zu importieren und es anschließend zu veröffentlichen.
Informationen zum Importieren und Veröffentlichen eines Runbooks im Azure-Portal finden Sie unter [Verwalten von Runbooks in Azure Automation](manage-runbooks.md).

Führen Sie die folgenden PowerShell-Befehle aus, um `DeployTemplate.ps1` als PowerShell-Runbook in Ihr Automation-Konto zu importieren:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Starten des Runbooks

Wir starten jetzt das Runbook, indem wir das [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)-Cmdlet aufrufen.

Weitere Informationen zum Starten eines Runbooks im Azure-Portal finden Sie unter [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook.md).

Führen Sie die folgenden Befehle in der PowerShell-Konsole aus:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Das Runbook wird ausgeführt, und Sie können seinen Status prüfen, indem Sie `$job.Status` ausführen.

Das Runbook ruft die Resource Manager-Vorlage ab und verwendet sie zum Bereitstellen eines neuen Azure Storage-Kontos.
Sie können prüfen, ob das neue Speicherkonto erstellt wurde, indem Sie den folgenden Befehl ausführen:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Zusammenfassung

Das ist alles! Sie können jetzt Azure Automation, Azure Storage und Resource Manager-Vorlagen verwenden, um Ihre Azure-Ressourcen bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Resource Manager-Vorlagen finden Sie unter [Übersicht über Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Informationen zu den ersten Schritten mit Azure Storage finden Sie unter [Einführung in Azure Storage](../storage/common/storage-introduction.md).
* Informationen zu weiteren nützlichen Azure Automation-Runbooks finden Sie unter [Runbook und Modulkataloge für Azure Automation](automation-runbook-gallery.md).
* Informationen zu weiteren nützlichen Resource Manager-Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/).


