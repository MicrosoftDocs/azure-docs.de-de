---
title: Verwenden von verwalteten Identitäten in Azure API Management | Microsoft-Dokumentation
description: Informationen, wie verwaltete Identitäten in API Management verwendet werden
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 75a02abb6cce332daad12e1feb25fb425f89f7f4
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393385"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Verwenden von verwalteten Identitäten in Azure API Management

In diesem Artikel erfahren Sie, wie Sie eine verwaltete Identität für eine API Management-Dienstinstanz erstellen und auf andere Ressourcen zugreifen. Mithilfe einer von Azure Active Directory (Azure AD) generierten verwalteten Identität kann Ihre API Management-Instanz einfach und sicher auf andere mit Azure AD geschützte Ressourcen wie Azure Key Vault zugreifen. Da die Identität von Azure verwaltet wird, müssen Sie keine Geheimnisse bereitstellen oder rotieren. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Erstellen einer verwalteten Identität für eine API Management-Instanz

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Um eine verwaltete Identität im Portal einzurichten, erstellen Sie zunächst wie gewohnt eine API Management-Instanz und aktivieren dann das Feature.

1. Erstellen Sie wie gewohnt eine API Management-Instanz im Portal. Navigieren Sie im Portal zu dieser App.
2. Wählen Sie **Verwaltete Dienstidentitäten** aus.
3. Legen Sie „Bei Azure Active Directory registrieren“ auf „Ein“ fest. Klicken Sie auf Speichern.

![Aktivieren der MSI](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Verwenden der Azure Resource Manager-Vorlage

Sie können eine API Management-Instanz mit einer Identität erstellen, indem die folgende Eigenschaft in die Ressourcendefinition aufgenommen wird:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Hierdurch erhält Azure die Anweisung, die Identität für Ihre API Management-Instanz zu erstellen und zu verwalten.

Eine vollständige Azure Resource Manager-Vorlage kann beispielsweise wie folgt aussehen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "contoso",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "Developer",
                "capacity": "1"
            },
            "properties": {
                "publisherEmail": "admin@contoso.com",
                "publisherName": "Contoso"
            },
            "identity": {
                "type": "systemAssigned"
            }
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Verwenden der verwalteten Dienstidentität für den Zugriff auf andere Ressourcen

> [!NOTE]
> Derzeit können verwaltete Identitäten für das Abrufen von Zertifikaten von Azure Key Vault für benutzerdefinierte API Management-Domänennamen verwendet werden. Weitere Szenarien werden in Kürze unterstützt.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Anfordern eines Zertifikats von Azure Key Vault

#### <a name="prerequisites"></a>Voraussetzungen
1. Die Key Vault-Instanz mit dem PFX-Zertifikat muss sich im gleichen Azure-Abonnement und in der gleichen Ressourcengruppe wie der API Management-Dienst befinden. Dies ist eine Voraussetzung der Azure Resource Manager-Vorlage.
2. Der Inhaltstyp des Geheimnisses muss *application/x-pkcs12* lauten. Sie können das folgende Skript verwenden, um das Zertifikat hochzuladen:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Wenn die Objektversion des Zertifikats nicht angegeben wird, erhält API Management automatisch die neuere Version des Zertifikats, nachdem sie in Key Vault hochgeladen wurde.

Das folgende Beispiel zeigt eine Azure Resource Manager-Vorlage mit den folgenden Schritten:

1. Erstellen einer API Management-Instanz mit einer verwalteten Identität
2. Aktualisieren der Zugriffsrichtlinien einer Azure Key Vault-Instanz und Gestatten des Abrufs von Geheimnissen aus diesem Key Vault durch die API Management-Instanz
3. Aktualisieren der API Management-Instanz durch Festlegen eines benutzerdefinierten Domänennamens über ein Zertifikats aus der Key Vault-Instanz

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über verwaltete Identitäten für Azure-Ressourcen:

* [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure-Ressourcen-Manager-Vorlagen](https://github.com/Azure/azure-quickstart-templates)
* [Authentifizieren mit einer verwalteten Identität in einer Richtlinie](./api-management-authentication-policies.md#ManagedIdentity)
