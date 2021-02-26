---
title: 'Schnellstart: Erstellen einer Resource Manager-Vorlage für Azure DB for MySQL Flexible Server'
description: In diesem Schnellstart erfahren Sie, wie Sie eine Azure Database for MySQL Flexible Server-Instanz mithilfe einer Resource Manager-Vorlage erstellen.
author: mksuni
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 10/23/2020
ms.openlocfilehash: def9e4f1b3f1c4e8f88f77dfe6906a8c96a94744
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389466"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql---flexible-server-preview"></a>Schnellstart: Verwenden einer Resource Manager-Vorlage zum Erstellen einer Azure Database for MySQL Flexible Server-Instanz (Vorschau)

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Azure Database for MySQL Flexible Server (Vorschau) ist ein verwalteter Dienst, mit dem Sie hochverfügbare MySQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können. Mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) können Sie eine Flexible Server-Instanz bereitstellen, um mehrere Server oder mehrere Datenbanken auf einem Server bereitzustellen.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)

## <a name="review-the-template"></a>Überprüfen der Vorlage

Bei einer Azure Database for MySQL Flexible Server-Instanz handelt es sich um die übergeordnete Ressource für eine oder mehrere Datenbanken innerhalb einer Region. Sie stellt den Bereich für Verwaltungsrichtlinien bereit, die auf die jeweiligen Datenbanken angewendet werden (z. B. Anmeldungen, Firewalls, Benutzer, Rollen und Konfigurationen).

Erstellen Sie eine _mysql-flexible-server-template.json_-Datei, und kopieren Sie dieses JSON-Skript in diese Datei.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "administratorLogin": {
      "type": "String"
    },
    "administratorLoginPassword": {
      "type": "SecureString"
    },
    "location": {
      "type": "String"
    },
    "serverName": {
      "type": "String"
    },
    "serverEdition": {
      "type": "String"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "haEnabled": {
      "type": "string",
      "defaultValue": "Disabled"
    },
    "availabilityZone": {
      "type": "String"
    },
    "version": {
      "type": "String"
    },
    "tags": {
      "defaultValue": {},
      "type": "Object"
    },
    "firewallRules": {
      "defaultValue": {},
      "type": "Object"
    },
    "vnetData": {
      "defaultValue": {},
      "type": "Object"
    },
    "backupRetentionDays": {
      "type": "Int"
    }
  },
  "variables": {
    "api": "2020-07-01-preview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_D4ds_v4",
        "tier": "[parameters('serverEdition')]"
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "DelegatedSubnetArguments": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "haEnabled": "[parameters('haEnabled')]",
        "storageProfile": {
          "storageMB": "[parameters('storageSizeMB')]",
          "backupRetentionDays": "[parameters('backupRetentionDays')]"
        },
        "availabilityZone": "[parameters('availabilityZone')]"
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-08-01",
      "name": "[concat('firewallRules-', copyIndex())]",
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforMySQL/flexibleServers/firewallRules",
              "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
              "apiVersion": "[variables('api')]",
              "properties": {
                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
              }
            }
          ]
        }
      },
      "copy": {
        "name": "firewallRulesIterator",
        "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
        "mode": "Serial"
      },
      "condition": "[greater(length(variables('firewallRules')), 0)]"
    }
  ]
}
```

Diese Ressourcen sind in der Vorlage definiert:

- Microsoft.DBforMySQL/flexibleServers

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Klicken Sie im folgenden PowerShell-Codeblock auf **Ausprobieren**, um die [Azure Cloud Shell](../../cloud-shell/overview.md) zu öffnen.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Befolgen Sie diese Schritte, um zu überprüfen, ob der Server in Azure erstellt wurde.

### <a name="azure-portal"></a>Azure-Portal

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Azure Database for MySQL-Server**, und wählen Sie die Option aus.
1. Wählen Sie in der Datenbankliste den neuen Server aus. Die Seite **Übersicht** für den neuen Azure Database for MySQL-Server wird angezeigt.

### <a name="powershell"></a>PowerShell

Sie müssen den Namen des neuen Servers eingeben, damit die Details Ihrer Azure Database for MySQL Flexible Server-Instanz angezeigt werden.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Sie müssen den Namen und die Ressourcengruppe des neuen Servers eingeben, damit die Details Ihrer Azure Database for MySQL Flexible Server-Instanz angezeigt werden.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/flexibleServers"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Behalten Sie die Ressourcengruppe, den Server und die Einzeldatenbank, wenn Sie mit [Nächste Schritte](#next-steps) fortfahren möchten. Die nächsten Schritte zeigen, wie Sie mithilfe verschiedener Methoden eine Verbindung mit Ihrer Datenbank herstellen und die Datenbank abfragen.

So löschen Sie die Ressourcengruppe:

### <a name="azure-portal"></a>Azure-Portal

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.
1. Wählen Sie in der Liste der Ressourcengruppen den Namen Ihrer Ressourcengruppe aus.
1. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```
---

## <a name="next-steps"></a>Nächste Schritte

Ein Tutorial mit Schritt-für-Schritt-Anleitungen zum Erstellen einer ARM-Vorlage finden Sie unter folgendem Link:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Ein ausführliches Tutorial zum Erstellen einer App mit App Service mithilfe von MySQL finden Sie hier:

> [!div class="nextstepaction"]
>[Erstellen einer PHP-Web-App (Laravel) mit MySQL](tutorial-php-database-app.md)
