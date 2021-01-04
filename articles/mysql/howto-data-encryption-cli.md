---
title: 'Datenverschlüsselung: Azure-Befehlszeilenschnittstelle – Azure Database for MySQL'
description: Erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle die Datenverschlüsselung für Azure Database for MySQL einrichten und verwalten.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6d9abc67035b4581a028d8e59ef080b4f1ffa5b9
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519041"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Datenverschlüsselung für Azure Database for MySQL über die Azure-Befehlszeilenschnittstelle

Erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle die Datenverschlüsselung für Azure Database for MySQL einrichten und verwalten.

## <a name="prerequisites-for-azure-cli"></a>Voraussetzungen für die Azure-Befehlszeilenschnittstelle

* Sie müssen über ein Azure-Abonnement verfügen und ein Administrator für dieses Abonnement sein.
* Erstellen Sie einen Schlüsseltresor und einen Schlüssel, der als vom Kunden verwalteter Schlüssel verwendet werden soll. Aktivieren Sie außerdem den Löschschutz und das vorläufige Löschen für den Schlüsseltresor.

  ```azurecli-interactive
  az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
  ```

* Erstellen Sie in der von Ihnen erstellten Azure Key Vault-Instanz den Schlüssel, der für die Datenverschlüsselung in Azure Database for MySQL verwendet wird.

  ```azurecli-interactive
  az keyvault key create --name <key_name> -p software --vault-name <vault_name>
  ```

* Damit Sie einen vorhandenen Schlüsseltresor mit einem vom Kunden verwalteten Schlüssel verwenden können, muss er die folgenden Eigenschaften aufweisen:

  * [Vorläufiges Löschen](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Löschschutz](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * Auf 90 Tage festgelegte Datenaufbewahrung
  ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* Der Schlüssel muss die folgenden Attribute aufweisen, damit er als vom Kunden verwalteter Schlüssel verwendet werden kann:
  * Kein Ablaufdatum
  * Nicht deaktiviert
  * Ausführen der Vorgänge **get**, **wrap** und **unwrap**
  * Das recoverylevel-Attribut ist auf **Recoverable** festgelegt. (Dafür muss vorläufiges Löschen mit einer Beibehaltungsdauer von 90 Tagen aktiviert sein.)
  * Bereinigungsschutz aktiviert

Sie können die oben genannten Attribute des Schlüssels mit dem folgenden Befehl überprüfen:

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```

## <a name="set-the-right-permissions-for-key-operations"></a>Festlegen der richtigen Berechtigungen für Schlüsselvorgänge

1. Es gibt zwei Möglichkeiten, die verwaltete Identität für Ihre Azure Database for MySQL-Instanz zu erhalten.

   ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Erstellen Sie einen Azure Database for MySQL-Server mit einer verwalteten Identität.

   ```azurecli-interactive
   az mysql server create --name -g <resource_group> --location <locations> --storage-size size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> -geo-redundant-backup <Enabled/Disabled>  --assign-identity
   ```

   ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Aktualisieren Sie einen vorhandenen Azure Database for MySQL-Server, um eine verwaltete Identität zu erhalten.

   ```azurecli-interactive
   az mysql server update --name  <server name>  -g <resource_group> --assign-identity
   ```

2. Legen Sie die **Schlüsselberechtigungen** (**Get**, **Wrap**, **Unwrap**) für den **Prinzipal** (der Name des MySQL-Servers) fest.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Festlegen der Datenverschlüsselung für Azure Database for MySQL

1. Aktivieren Sie die Datenverschlüsselung für die Azure Database for MySQL-Instanz mithilfe des Schlüssels, der in Azure Key Vault erstellt wurde.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Schlüssel-URL: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Verwenden von Datenverschlüsselung für Wiederherstellungs- oder Replikatserver

Nachdem Azure Database for MySQL mit einem vom Kunden verwalteten Schlüssel verschlüsselt wurde, der in Key Vault gespeichert ist, wird jede neu erstellte Kopie des Servers ebenfalls verschlüsselt. Sie können diese neue Kopie entweder durch einen lokalen Wiederherstellungsvorgang, einen Geowiederherstellungsvorgang oder durch einen Replikationsvorgang (lokal/regionsübergreifend) erstellen. Sie können für einen verschlüsselten MySQL-Server also die folgenden Schritte ausführen, um einen verschlüsselten wiederhergestellten Server zu erstellen.

### <a name="creating-a-restoredreplica-server"></a>Erstellen eines wiederhergestellten/Replikatservers

* [Erstellen eines wiederhergestellten Servers](howto-restore-server-cli.md) 
* [Erstellen eines Lesereplikatservers](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Nach der Serverwiederherstellung – erneutes Überprüfen der Datenverschlüsselung des wiederhergestellten Servers

*   Zuweisen der Identität für den Replikatserver
```azurecli-interactive
az mysql server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Abrufen des vorhandenen Schlüssels, der für den wiederhergestellten Server bzw. den Replikatserver verwendet werden muss

```azurecli-interactive
az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Festlegen der Richtlinie für die neue Identität für den wiederhergestellten Server bzw. den Replikatserver
  
```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Erneutes Überprüfen des wiederhergestellten Servers bzw. des Replikatservers mit dem Verschlüsselungsschlüssel

```azurecli-interactive
az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Zusätzliche Funktionen für den von der Azure Database for MySQL-Instanz verwendeten Schlüssel

### <a name="get-the-key-used"></a>Abrufen des verwendeten Schlüssels

```azurecli-interactive
az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
```

Schlüssel-URL: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Auflisten des verwendeten Schlüssels

```azurecli-interactive
az mysql server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Löschen des verwendeten Schlüssels

```azurecli-interactive
az mysql server key delete -g <resource_group> --kid <key url>
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Verwenden einer Azure Resource Manager-Vorlage zum Aktivieren von Datenverschlüsselung

Sie können die Datenverschlüsselung nicht nur im Azure-Portal, sondern auch auf Ihrem Azure Database for MySQL-Einzelserver aktivieren, indem Sie Azure Resource Manager-Vorlagen für neue und vorhandene Server verwenden.

### <a name="for-a-new-server"></a>Neuer Server

Verwenden Sie eine der vorab erstellten Azure Resource Manager-Vorlagen, um den Server mit aktivierter Datenverschlüsselung bereitzustellen: [Beispiel mit Datenverschlüsselung](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Diese Azure Resource Manager-Vorlage erstellt einen Azure Database for MySQL-Server und verwendet **KeyVault** und **Key**, die als Parameter übergeben werden, um die Datenverschlüsselung auf dem Server zu aktivieren.

### <a name="for-an-existing-server"></a>Vorhandener Server

Sie können Azure Resource Manager-Vorlagen auch verwenden, um die Datenverschlüsselung auf vorhandenen Azure Database for MySQL-Servern zu aktivieren.

* Übergeben Sie die zuvor kopierte Ressourcen-ID des Azure Key Vault-Schlüssels unter der `Uri`-Eigenschaft im properties-Objekt.

* Verwenden Sie *2020-01-01-preview* als API-Version.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>Nächste Schritte

 Weitere Informationen zur Datenverschlüsselung finden Sie unter [Azure Database for MySQL-Datenverschlüsselung mit einem vom Kunden verwalteten Schlüssel](concepts-data-encryption-mysql.md).
