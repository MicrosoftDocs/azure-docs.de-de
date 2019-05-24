---
title: Key Vault-Referenzen – Azure App Service | Microsoft-Dokumentation
description: Konzeptverweis und Einrichtungsleitfaden für Azure Key Vault-Referenzen in Azure App Service und Azure Functions
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: f086850ecc2f15c41ab89db34b16d6d2e4a229cb
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956221"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Verwenden von Key Vault-Verweisen in App Service und Azure Functions (Vorschauversion)

> [!NOTE] 
> Key Vault-Verweise befinden sich derzeit in der Vorschauversion.

Dieses Thema zeigt Ihnen, wie Sie mit Geheimnissen von Azure Key Vault in Ihrer App Service- oder Azure Functions-Anwendung arbeiten können, ohne dass Codeänderungen erforderlich sind. [Azure Key Vault](../key-vault/key-vault-overview.md) ist ein Dienst, der eine zentralisierte Verwaltung von Geheimnissen mit voller Kontrolle über Zugriffsrichtlinien und Überprüfungsverlauf ermöglicht.

## <a name="granting-your-app-access-to-key-vault"></a>Gewähren des Zugriffs auf Key Vault für Ihre App

Um Geheimnisse aus Key Vault auslesen zu können, müssen Sie einen Tresor erstellen und Ihrer App die Berechtigung erteilen, darauf zuzugreifen.

1. Erstellen Sie einen Schlüsseltresor anhand dieser [Key Vault-Schnellstartanleitung](../key-vault/quick-create-cli.md).

1. Erstellen Sie eine [systemseitig zugewiesene verwaltete Identität](overview-managed-identity.md) für Ihre App.

   > [!NOTE] 
   > Key Vault-Verweise unterstützen derzeit nur systemseitig zugewiesene verwaltete Identitäten. Vom Benutzer zugewiesene Identitäten können nicht verwendet werden.

1. Erstellen Sie eine [Zugriffsrichtlinie im Schlüsseltresor](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) für die zuvor von Ihnen erstellte Anwendungsidentität. Aktivieren Sie die „Get“-Geheimnisberechtigung für diese Richtlinie. Konfigurieren Sie nicht die Einstellungen „Autorisierte Anwendung“ oder `appliationId`, da dies mit einer verwalteten Identität nicht kompatibel ist.

## <a name="reference-syntax"></a>Verweissyntax

Ein Key Vault-Verweis hat die Form `@Microsoft.KeyVault({referenceString})`, wobei `{referenceString}` durch eine der folgenden Optionen ersetzt wird:

> [!div class="mx-tdBreakAll"]
> | Verweiszeichenfolge                                                            | BESCHREIBUNG                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** ist die vollständige URI der Datenebene eines Geheimnisses in Key Vault, einschließlich einer Version, z.B. https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931.  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** ist der Name Ihrer Key Vault-Ressource. **SecretName** ist der Name des Zielgeheimnisses. **SecretVersion** ist die Version des zu verwendenden Geheimnisses. |

> [!NOTE] 
> In der aktuelle Vorschau sind Versionen erforderlich. Beim Rotieren von Geheimnissen müssen Sie die Version in der Anwendungskonfiguration aktualisieren.

Ein vollständiger Verweis kann beispielsweise wie folgt aussehen:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Alternativ:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Einbinden von Anwendungseinstellungen aus Key Vault

Key Vault-Verweise können als Werte für [Anwendungseinstellungen](configure-common.md#configure-app-settings) verwendet werden, sodass Sie Geheimnisse in Key Vault anstelle der Websitekonfiguration behalten können. Die Anwendungseinstellungen sind im Ruhezustand sicher verschlüsselt, aber wenn Sie die Funktion zum Verwalten von Geheimnissen benötigen, sollten sie in Key Vault aufgenommen werden.

Um einen Key Vault-Verweis für eine Anwendungseinstellung zu verwenden, legen Sie den Verweis als Wert für die Einstellung fest. Ihre App kann wie gewohnt durch seinen Schlüssel auf das Geheimnis verweisen. Es sind keine Codeänderungen erforderlich.

> [!TIP]
> Die meisten Anwendungseinstellungen, die Key Vault-Verweise verwenden, sollten als Slot-Einstellungen markiert werden, da Sie für jede Umgebung eigene Tresore verwenden sollten.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-Bereitstellung

Wenn Sie Ressourcenbereitstellungen über Azure Resource Manager-Vorlagen automatisieren, müssen Sie möglicherweise Ihre Abhängigkeiten in einer bestimmten Reihenfolge sortieren, damit diese Funktion funktioniert. Beachten Sie, dass Sie Ihre Anwendungseinstellungen als eigene Ressource definieren müssen, anstatt eine `siteConfig`-Eigenschaft in der Websitedefinition zu verwenden. Grund dafür ist, dass zuerst die Website definiert werden muss, damit die systemseitig zugeordnete Identität gleichzeitig erstellt und in der Zugriffsrichtlinie verwendet werden kann.

Ein Beispiel für eine Pseudovorlage für eine Funktions-App könnte wie folgt aussehen:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> In diesem Beispiel hängt die Bereitstellung der Quellcodeverwaltung von den Anwendungseinstellungen ab. Dies ist normalerweise ein unsicheres Verhalten, da sich das App-Einstellungsupdate asynchron verhält. Da wir aber die Anwendungseinstellung `WEBSITE_ENABLE_SYNC_UPDATE_SITE` integriert haben, verläuft das Update synchron. Dies bedeutet, dass die Bereitstellung der Quellcodeverwaltung erst dann beginnt, wenn die Anwendungseinstellungen vollständig aktualisiert wurden.
