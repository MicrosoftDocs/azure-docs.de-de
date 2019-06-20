---
title: Key Vault-Geheimnis mit Azure Resource Manager-Vorlage | Microsoft-Dokumentation
description: Informationen zum Übergeben eines geheimen Schlüssels aus einem Schlüsseltresor als Parameter während der Bereitstellung.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: e47a087e27b6a8ade947e36ded762ce2e518ca25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507993"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung

Anstatt einen sicheren Wert (wie ein Kennwort) direkt in Ihre Vorlage oder Parameterdatei einzufügen, können Sie den Wert während einer Bereitstellung aus einem [Azure Key Vault](../key-vault/key-vault-whatis.md) abrufen. Sie rufen den Wert ab, indem Sie den Schlüsseltresor und das Geheimnis in Ihrer Parameterdatei angeben. Der Wert wird nie offengelegt, da Sie nur auf die Schlüsseltresor-ID verweisen. Der Schlüsseltresor kann in einem anderen Abonnement als die Ressourcengruppe vorhanden sein, für die Sie ihn bereitstellen.

## <a name="deploy-key-vaults-and-secrets"></a>Bereitstellen von Schlüsseltresoren und Geheimnissen

Um während der Vorlagenbereitstellung auf einen Schlüsseltresor zuzugreifen, legen Sie `enabledForTemplateDeployment` für den Schlüsseltresor auf `true` fest.

Die folgenden Azure CLI- und Azure PowerShell-Beispiele zeigen, wie Sie den Schlüsseltresor erstellen und ein Geheimnis hinzufügen.

```azurecli
az group create --name $resourceGroupName --location $location
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "ExamplePassword" --value "hVFkk965BuUv"
```

```azurepowershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName $keyVaultName -Name 'ExamplePassword' -SecretValue $secretvalue
```

Als Besitzer des Schlüsseltresors haben Sie automatisch Zugriff auf die Erstellung von Geheimnissen. Wenn der Benutzer, der mit Geheimnissen arbeitet, nicht der Besitzer des Schlüsseltresors ist, gewähren Sie den Zugriff mit:

```azurecli
az keyvault set-policy \
  --upn $userPrincipalName \
  --name $keyVaultName \
  --secret-permissions set delete get list
```

```azurepowershell
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName $keyVaultName `
  -UserPrincipalName $userPrincipalName `
  -PermissionsToSecrets set,delete,get,list
```

Weitere Informationen zum Erstellen von Schlüsseltresoren und zum Hinzufügen von Geheimnissen finden Sie unter:

- [Festlegen und Abrufen eines Geheimnisses über die Befehlszeilenschnittstelle](../key-vault/quick-create-cli.md)
- [Festlegen und Abrufen eines Geheimnisses mit PowerShell](../key-vault/quick-create-powershell.md)
- [Festlegen und Abrufen eines Geheimnisses über das Portal](../key-vault/quick-create-portal.md)
- [Festlegen und Abrufen eines Geheimnisses mit .NET](../key-vault/quick-create-net.md)
- [Festlegen und Abrufen eines Geheimnisses mit Node.js](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Gewähren des Zugriffs auf die Geheimnisse

Der Benutzer, der die Vorlage bereitstellt, muss die Berechtigung `Microsoft.KeyVault/vaults/deploy/action` für den Bereich der Ressourcengruppe und des Schlüsseltresors besitzen. Die Rollen [Besitzer](../role-based-access-control/built-in-roles.md#owner) und [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor) gewähren diesen Zugriff. Wenn Sie den Schlüsseltresor erstellt haben, sind Sie der Besitzer und verfügen somit über die Berechtigung.

Die folgende Prozedur zeigt das Erstellen einer Rolle mit der Mindestberechtigung und das Zuweisen des Benutzers

1. Erstellen einer benutzerdefinierten Rollendefinition (JSON-Datei):

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Ersetzen Sie „00000000-0000-0000-0000-000000000000“ durch die Abonnement-ID des Benutzers, der die Vorlagen bereitstellen muss.

2. Erstellen Sie die neue Rolle mithilfe der JSON-Datei:

    ```azurecli
    az role definition create --role-definition "<PathToRoleFile>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee $userPrincipalName \
      --resource-group $resourceGroupName
    ```

    ```azurepowershell
    New-AzRoleDefinition -InputFile "<PathToRoleFile>" 
    New-AzRoleAssignment `
      -ResourceGroupName $resourceGroupName `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName $userPrincipalName
    ```

    Die Beispiele weisen dem Benutzer auf Ressourcengruppenebene die benutzerdefinierte Rolle zu.  

Wenn ein Schlüsseltresor zusammen mit der Vorlage für eine [verwaltete Anwendung](../managed-applications/overview.md) verwendet wird, müssen Sie Zugriff auf den Dienstprinzipal des **Ressourcenanbieters der Appliance** erteilen. Weitere Informationen finden Sie unter [Zugreifen auf das Geheimnis im Schlüsseltresor bei der Bereitstellung von Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Verweisen auf Geheimnisse mit einer statischen ID

Bei dieser Herangehensweise verweisen Sie auf den Schlüsseltresor in der Parameterdatei, nicht in der Vorlage. Die folgende Abbildung zeigt, wie die Parameterdatei auf das Geheimnis verweist und diesen Wert an die Vorlage übergibt.

![Diagramm der Resource Manager-Integration des Schlüsseltresors mit statischer ID](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Tutorial: Integrieren von Azure Key Vault in die Resource Manager-Vorlagenbereitstellung](./resource-manager-tutorial-use-key-vault.md) verwendet diese Methode.

Die folgende Vorlage stellt einen SQL-Server bereit, der ein Administratorkennwort enthält. Der Kennwortparameter ist auf eine sichere Zeichenfolge festgelegt. Aber die Vorlage gibt nicht an, woher dieser Wert stammt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Erstellen Sie jetzt eine Parameterdatei für der vorherige Vorlage. Geben Sie in der Parameterdatei einen Parameter an, der dem Namen des Parameters in der Vorlage entspricht. Verweisen Sie für den Parameterwert auf das Geheimnis aus dem Schlüsseltresor. Sie verweisen auf den geheimen Schlüssel, indem Sie den Ressourcenbezeichner des Schlüsseltresors und den Namen des Geheimnisses übergeben:

In der folgenden Parameterdatei muss das Schlüsseltresorgeheimnis bereits vorhanden sein, und Sie geben einen statischen Wert für seine Ressourcen-ID an.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "ExamplePassword"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Wenn Sie den geheimen Schlüssel in einer Version verwenden müssen, die nicht die aktuelle Version ist, verwenden Sie die `secretVersion`-Eigenschaft.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Stellen Sie die Vorlage bereit, und übergeben Sie sie in der Parameterdatei:

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Verwenden Sie für PowerShell Folgendes:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Verweisen auf Geheimnisse mit einer dynamischen ID

Im vorherigen Abschnitt wurde für das Geheimnis des Schlüsseltresors eine statische Ressourcen-ID aus dem Parameter übergeben. Manchmal muss jedoch auf einen geheimen Schlüsseltresorschlüssel verwiesen werden, der je nach aktueller Bereitstellung variiert. Oder Sie möchten Parameterwerte an die Vorlage übergeben, statt einen Referenzparameter in der Parameterdatei zu erstellen. In beiden Fällen können Sie die Ressourcen-ID für ein Schlüsseltresorgeheimnis mithilfe einer verknüpften Vorlage dynamisch erstellen.

Da in der Parameterdatei keine Vorlagenausdrücke zulässig sind, kann die Ressourcen-ID nicht dynamisch in der Parameterdatei generiert werden. 

Die verknüpfte Vorlage wird der übergeordneten Vorlage hinzugefügt, und es wird ein Parameter mit der dynamisch generierten Ressourcen-ID übergeben. Die folgende Abbildung zeigt, wie ein Parameter in der verknüpften Vorlage auf das Geheimnis verweist.

![Dynamische ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

Die [folgende Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) erstellt dynamisch die Schlüsseltresor-ID und übergibt sie als Parameter.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Stellen Sie die vorherige Vorlage bereit, und geben Sie Werte für die Parameter an. Sie können die Beispielvorlage von GitHub verwenden, müssen jedoch Parameterwerte für Ihre Umgebung angeben.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Verwenden Sie für PowerShell Folgendes:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Nächste Schritte

- Allgemeine Informationen zu Schlüsseltresoren finden Sie unter [Was ist Azure Key Vault?](../key-vault/key-vault-overview.md)
- Vollständige Beispiele für Verweise auf geheime Schlüssel finden Sie unter [Key Vault examples](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)(in englischer Sprache).
