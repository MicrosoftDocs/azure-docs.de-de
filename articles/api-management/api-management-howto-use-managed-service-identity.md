---
title: Verwenden von verwalteten Identitäten in Azure API Management | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie systemseitig und benutzerseitig zugewiesene Identitäten in API Management erstellen, indem Sie das Azure-Portal, PowerShell und eine Resource Manager-Vorlage verwenden.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 11/14/2020
ms.author: apimpm
ms.openlocfilehash: db1a8238cf9ddae57d73438d43daa54294ce6860
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686224"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Verwenden von verwalteten Identitäten in Azure API Management

In diesem Artikel erfahren Sie, wie Sie eine verwaltete Identität für eine Azure API Management-Instanz erstellen und auf andere Ressourcen zugreifen. Mithilfe einer von Azure Active Directory (Azure AD) generierten verwalteten Identität kann Ihre API Management-Instanz einfach und sicher auf andere mit Azure AD geschützte Ressourcen wie Azure Key Vault zugreifen. Azure verwaltet diese Identität, sodass Sie keine Geheimnisse bereitstellen oder rotieren müssen. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

Sie können einer API Management-Instanz zwei Arten von Identitäten zuweisen:

- Eine *systemseitig zugewiesene Identität* ist an Ihren Dienst gebunden und wird gelöscht, wenn dieser gelöscht wird. Der Dienst kann nur über eine systemseitig zugewiesene Identität verfügen.
- Eine *benutzerseitig zugewiesene Identität* ist eine eigenständige Azure-Ressource, die Ihrem Dienst zugewiesen werden kann. Der Dienst kann über mehrere benutzerseitig zugewiesene Identitäten verfügen.

## <a name="create-a-system-assigned-managed-identity"></a>Erstellen einer systemseitig zugewiesenen verwalteten Identität

### <a name="azure-portal"></a>Azure-Portal

Erstellen Sie zunächst wie gewohnt eine API Management-Instanz, und aktivieren Sie dann das Feature, um eine verwaltete Identität im Azure-Portal einzurichten.

1. Erstellen Sie wie gewohnt eine API Management-Instanz im Portal. Navigieren Sie im Portal zu dieser Instanz.
2. Klicken Sie auf **Verwaltete Identitäten**.
3. Ändern Sie auf der Registerkarte **Systemseitig zugewiesen** den **Status** in **Ein**. Wählen Sie **Speichern** aus.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Auswahl zum Aktivieren einer systemseitig zugewiesenen verwalteten Identität" border="true":::


### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In den folgenden Schritten werden Sie durch das Erstellen einer API Management-Instanz und das Zuweisen einer Identität zur App mithilfe von Azure PowerShell geleitet. 

1. Installieren Sie bei Bedarf Azure PowerShell anhand der Anweisungen im [Azure PowerShell-Leitfaden](/powershell/azure/install-az-ps). Führen Sie dann `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

2. Verwenden Sie zum Erstellen der Instanz den folgenden Code. Weitere Beispiele zum Verwenden von Azure PowerShell mit einer API Management-Instanz finden Sie unter [PowerShell-Beispiele für API Management](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Aktualisieren Sie eine vorhandene Instanz, um die Identität zu erstellen:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage

Sie können eine API Management-Instanz mit einer Identität erstellen, indem die folgende Eigenschaft in die Ressourcendefinition aufgenommen wird:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Diese Eigenschaft weist Azure an, die Identität für Ihre API Management-Instanz zu erstellen und zu verwalten.

Eine vollständige Azure Resource Manager-Vorlage kann beispielsweise wie folgt aussehen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
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
    }]
}
```

Wenn die Instanz erstellt wurde, weist sie folgende zusätzliche Eigenschaften auf:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Die `tenantId`-Eigenschaft gibt an, zu welchem Azure AD-Mandanten die Identität gehört. Die `principalId`-Eigenschaft ist ein eindeutiger Bezeichner für die neue Identität der Instanz. In Azure AD ist der Name des Dienstprinzipals mit dem Namen der API Management-Instanz identisch.


> [!NOTE]
> Eine API Management-Instanz kann gleichzeitig über systemseitig und über benutzerseitig zugewiesene Identitäten verfügen. In diesem Fall hat die `type`-Eigenschaft den Wert `SystemAssigned,UserAssigned`.

## <a name="supported-scenarios-using-system-assigned-identity"></a>Unterstützte Szenarien mit systemseitig zugewiesener Identität

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Abrufen eines benutzerdefinierten TLS/SSL-Zertifikats von Azure Key Vault für die API Management-Instanz
Sie können die systemseitig zugewiesene Identität einer API Management-Instanz verwenden, um benutzerdefinierte TLS/SSL-Zertifikate abzurufen, die in Azure Key Vault gespeichert werden. Anschließend können Sie diese Zertifikate den benutzerdefinierten Domänen in der API Management-Instanz zuweisen. Berücksichtigen Sie dabei Folgendes:

- Der Inhaltstyp des Geheimnisses muss *application/x-pkcs12* lauten.
- Verwenden Sie den geheimen Endpunkt des Key Vault-Zertifikats, das das Geheimnis enthält.

> [!Important]
> Wenn Sie die Objektversion des Zertifikats nicht angeben, ruft API Management innerhalb von vier Stunden, nachdem sie in Key Vault hochgeladen wurde, die neuere Version des Zertifikats ab.

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
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
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
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
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

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Authentifizieren beim Back-End mithilfe einer API Management-Identität

Sie können die systemseitig zugewiesene Identität verwenden, um sich über die Richtlinie [authentication-managed-identity](api-management-authentication-policies.md#ManagedIdentity) beim Back-End zu authentifizieren.


## <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

> [!NOTE]
> Sie können eine API Management-Instanz mit bis zu zehn benutzerseitig zugewiesenen verwalteten Identitäten verknüpfen.

### <a name="azure-portal"></a>Azure-Portal

Erstellen Sie zunächst wie gewohnt eine API Management-Instanz, und aktivieren Sie dann das Feature, um eine verwaltete Identität im Portal einzurichten.

1. Erstellen Sie wie gewohnt eine API Management-Instanz im Portal. Navigieren Sie im Portal zu dieser Instanz.
2. Klicken Sie auf **Verwaltete Identitäten**.
3. Wählen Sie auf der Registerkarte **Benutzerseitig zugewiesen** die Option **Hinzufügen** aus.
4. Suchen Sie nach der zuvor erstellten Identität, und wählen Sie sie aus. Wählen Sie **Hinzufügen**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Auswahl zum Aktivieren einer benutzerseitig zugewiesenen verwalteten Identität" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In den folgenden Schritten werden Sie durch das Erstellen einer API Management-Instanz und das Zuweisen einer Identität zur App mithilfe von Azure PowerShell geleitet. 

1. Installieren Sie bei Bedarf Azure PowerShell anhand der Anweisungen im [Azure PowerShell-Leitfaden](/powershell/azure/install-az-ps). Führen Sie dann `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

2. Verwenden Sie zum Erstellen der Instanz den folgenden Code. Weitere Beispiele zum Verwenden von Azure PowerShell mit einer API Management-Instanz finden Sie unter [PowerShell-Beispiele für API Management](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Weisen Sie einem vorhandenen Dienst eine Identität zu:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage

Sie können eine API Management-Instanz mit einer Identität erstellen, indem die folgende Eigenschaft in die Ressourcendefinition aufgenommen wird:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Durch das Hinzufügen des benutzerseitig zugewiesenen Typs wird Azure angewiesen, die benutzerseitig zugewiesene Identität zu verwenden, die für Ihre Instanz angegeben ist.

Eine vollständige Azure Resource Manager-Vorlage kann beispielsweise wie folgt aussehen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
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
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
        "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Wenn der Dienst erstellt wurde, weist er folgende zusätzliche Eigenschaften auf:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

Die `principalId`-Eigenschaft ist ein eindeutiger Bezeichner für die Identität, der bei der Azure AD-Verwaltung verwendet wird. Die `clientId`-Eigenschaft ist ein eindeutiger Bezeichner für die neue Identität der Anwendung, der bei Runtimeaufrufen angibt, welche Identität verwendet werden soll.

> [!NOTE]
> Eine API Management-Instanz kann gleichzeitig über systemseitig und über benutzerseitig zugewiesene Identitäten verfügen. In diesem Fall hat die `type`-Eigenschaft den Wert `SystemAssigned,UserAssigned`.

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Unterstützte Szenarien mit benutzerseitig zugewiesener verwalteter Identität

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Abrufen eines benutzerdefinierten TLS/SSL-Zertifikats von Azure Key Vault für die API Management-Instanz
Sie können eine beliebige benutzerseitig zugewiesene Identität verwenden, um eine Vertrauensstellung zwischen einer API Management-Instanz und Key Vault einzurichten. Diese Vertrauensstellung kann dann verwendet werden, um benutzerdefinierte TLS/SSL-Zertifikate abzurufen, die in Azure Key Vault gespeichert sind. Anschließend können Sie diese Zertifikate den benutzerdefinierten Domänen in der API Management-Instanz zuweisen. 

Berücksichtigen Sie dabei Folgendes:

- Der Inhaltstyp des Geheimnisses muss *application/x-pkcs12* lauten.
- Verwenden Sie den geheimen Endpunkt des Key Vault-Zertifikats, das das Geheimnis enthält.

> [!Important]
> Wenn Sie die Objektversion des Zertifikats nicht angeben, ruft API Management innerhalb von vier Stunden, nachdem sie in Key Vault hochgeladen wurde, die neuere Version des Zertifikats ab.

Die vollständige Vorlage finden Sie unter [API Management mit SSL auf Key Vault-Basis und benutzerseitig zugewiesener Identität](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json).

In dieser Vorlage stellen Sie Folgendes bereit:

* Azure API Management
* Von Azure verwaltete benutzerseitig zugewiesene Identität
* Azure Key Vault zur Speicherung des SSL/TLS-Zertifikats

Klicken Sie auf folgende Schaltfläche, um die Bereitstellung automatisch auszuführen:

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Authentifizieren beim Back-End mithilfe einer benutzerseitig zugewiesenen Identität

Sie können die benutzerseitig zugewiesene Identität verwenden, um sich über die Richtlinie [authentication-managed-identity](api-management-authentication-policies.md#ManagedIdentity) beim Back-End zu authentifizieren.


## <a name="remove-an-identity"></a><a name="remove"></a>Entfernen einer Identität

Sie können eine systemseitig zugewiesene Identität entfernen, indem Sie das Feature über das Portal oder die Azure Resource Manager-Vorlage genau so deaktivieren, wie es aktiviert wurde. Benutzerseitig zugewiesene Identitäten können einzeln entfernt werden. Legen Sie den Identitätstyp auf `"None"` fest, um alle Identitäten zu entfernen.

Bei dieser Methode zum Entfernen einer systemseitig zugewiesenen Identität wird diese auch aus Azure AD gelöscht. Systemseitig zugewiesene Identitäten werden automatisch aus Azure AD entfernt, wenn die API Management-Instanz gelöscht wird.

Aktualisieren Sie den folgenden Abschnitt, um alle Identitäten mithilfe der Azure Resource Manager-Vorlage zu entfernen:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Wenn eine API Management-Instanz mit einem benutzerdefinierten SSL-Zertifikat von Key Vault konfiguriert ist und Sie versuchen, eine verwaltete Identität zu deaktivieren, schlägt die Anforderung fehl.
>
> Sie können die Blockierung aufheben, indem Sie von einem Azure Key Vault-Zertifikat zu einem inline codierten Zertifikat wechseln und die verwaltete Identität deaktivieren. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens](configure-custom-domain.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über verwaltete Identitäten für Azure-Ressourcen:

* [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure-Ressourcen-Manager-Vorlagen](https://github.com/Azure/azure-quickstart-templates)
* [Authentifizieren mit einer verwalteten Identität in einer Richtlinie](./api-management-authentication-policies.md#ManagedIdentity)
