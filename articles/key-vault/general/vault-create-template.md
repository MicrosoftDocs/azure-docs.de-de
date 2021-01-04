---
title: Erstellen eines Azure-Schlüsseltresors und einer Tresorzugriffsrichtlinie mithilfe einer ARM-Vorlage (Vorschau)
description: Dieser Artikel veranschaulicht, wie Sie Azure-Schlüsseltresore und Tresorzugriffsrichtlinien erstellen, indem Sie eine Azure Resource Manager-Vorlage verwenden.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: d6f3d2051790ba02f5e4ea1687830ccbdf6dcf21
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285140"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Erstellen eines Azure-Schlüsseltresors und von Tresorzugriffsrichtlinien mithilfe einer Resource Manager-Vorlage

[Azure Key Vault](../general/overview.md) ist ein Clouddienst, der einen sicheren Speicher für Geheimnisse wie Schlüssel, Kennwörter und Zertifikate bereitstellt. In diesem Artikel wird die Bereitstellung einer Azure Resource Manager-Vorlage (ARM-Vorlage) zum Erstellen eines Schlüsseltresors beschrieben.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zur Durchführung der in diesem Artikel aufgeführten Schritte ist Folgendes erforderlich:

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.


## <a name="create-a-key-vault-resource-manager-template"></a>Erstellen einer Resource Manager-Vorlage für einen Schlüsseltresor

Mit der folgenden einfachen Vorlage wird ein Schlüsseltresor erstellt. Einige Werte werden in der Vorlage angegeben.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Weitere Informationen zu Key Vault-Vorlageneinstellungen finden Sie unter [Key Vault ARM-Vorlagenreferenz](/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Wenn eine Vorlage erneut bereitgestellt wird, werden alle vorhandenen Zugriffsrichtlinien im Schlüsseltresor überschrieben. Es wird empfohlen, die `accessPolicies`-Eigenschaft mit vorhandenen Zugriffsrichtlinien aufzufüllen, um zu verhindern, dass Sie den Zugriff auf den Schlüsseltresor verlieren. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Hinzufügen einer Zugriffsrichtlinie zu einer Key Vault Resource Manager-Vorlage

Sie können Zugriffsrichtlinien für einen vorhandenen Schlüsseltresor ohne erneute Bereitstellung der gesamten Schlüsseltresorvorlage bereitstellen. Die folgende Vorlage stellt eine einfache Möglichkeit zum Erstellen von Zugriffsrichtlinien dar:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Weitere Informationen zu Key Vault-Vorlageneinstellungen finden Sie unter [Key Vault ARM-Vorlagenreferenz](/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>Weitere Key Vault Resource Manager-Vorlagen

Für Key Vault-Objekte stehen weitere Resource Manager-Vorlagen zur Verfügung:

| Geheimnisse | Schlüssel | Zertifikate |
|--|--|--|
|<ul><li>[Schnellstart](../secrets/quick-create-template.md)<li>[Verweis](/azure/templates/microsoft.keyvault/vaults/secrets)|–|–|

Weitere Key Vault-Vorlagen finden Sie hier: [Key Vault Resource Manager-Referenz](/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>Bereitstellen der Vorlagen

Sie können das Azure-Portal verwenden, um die oben genannten Vorlagen mithilfe der Option **Eigene Vorlage im Editor erstellen** bereitzustellen, die hier beschrieben ist: [Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)

Sie können die oben aufgeführten Vorlagen auch in Dateien speichern und die folgenden Befehle verwenden:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) und [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit weiteren Schnellstarts und Tutorials fortfahren möchten, müssen Sie diese Ressourcen nicht bereinigen. Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Wenn Sie die Gruppe löschen, werden auch der Schlüsseltresor und zugehörige Ressourcen gelöscht. Wenn Sie die Ressourcengruppe mit der Azure CLI oder Azure PowerShell löschen möchten, führen Sie die folgenden Schritte aus:

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Ressourcen

- Lesen Sie die [Übersicht über den Azure-Schlüsseltresor](../general/overview.md).
- Hier finden Sie weitere Informationen zum [Azure-Ressourcen-Manager](../../azure-resource-manager/management/overview.md).
- Machen Sie sich mit den [bewährten Methoden zum Verwenden von Azure Key Vault](../general/best-practices.md) vertraut.

## <a name="next-steps"></a>Nächste Schritte

- [Sicherer Zugriff auf einen Schlüsseltresor](secure-your-key-vault.md)
- [Authentifizieren bei einem Schlüsseltresor](authentication.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](developers-guide.md)
