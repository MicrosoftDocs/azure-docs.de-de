---
title: 'Datenverschlüsselung: Azure-Befehlszeilenschnittstelle – Azure Database for PostgreSQL-Einzelserver'
description: Erfahren Sie, wie Sie über die Azure-Befehlszeilenschnittstelle die Datenverschlüsselung für Azure Database for PostgreSQL-Einzelserver einrichten und verwalten.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 757782e8842fbcaca9c8d95ec8086dd5791a817b
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240612"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Datenverschlüsselung für Azure Database for PostgreSQL-Einzelserver mithilfe der Azure-Befehlszeilenschnittstelle

Erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle die Datenverschlüsselung für Azure Database for PostgreSQL-Einzelserver einrichten und verwalten.

## <a name="prerequisites-for-azure-cli"></a>Voraussetzungen für die Azure-Befehlszeilenschnittstelle

* Sie müssen über ein Azure-Abonnement verfügen und ein Administrator für dieses Abonnement sein.
* Erstellen Sie einen Schlüsseltresor und einen Schlüssel, der als vom Kunden verwalteter Schlüssel verwendet werden soll. Aktivieren Sie außerdem den Löschschutz und das vorläufige Löschen für den Schlüsseltresor.

   ```azurecli-interactive
   az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
   ```

* Erstellen Sie in der von Ihnen erstellten Azure Key Vault-Instanz den Schlüssel, der für die Datenverschlüsselung des Azure Database for PostgreSQL-Einzelservers verwendet wird.

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

* Der Schlüssel muss die folgenden Attribute aufweisen, damit er als vom Kunden verwalteter Schlüssel verwendet werden kann:
  * Kein Ablaufdatum
  * Nicht deaktiviert
  * Ausführen der Vorgänge **get** , **wrap** und **unwrap**

## <a name="set-the-right-permissions-for-key-operations"></a>Festlegen der richtigen Berechtigungen für Schlüsselvorgänge

1. Es gibt zwei Möglichkeiten, die verwaltete Identität für Ihren Azure Database for PostgreSQL-Einzelserver zu erhalten.

    ### <a name="create-an-new-azure-database-for-postgresql-server-with-a-managed-identity"></a>Erstellen Sie einen Azure Database for PostgreSQL-Server mit einer verwalteten Identität.

    ```azurecli-interactive
    az postgres server create --name <server_name> -g <resource_group> --location <location> --storage-size <size>  -u <user> -p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled> --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-postgresql-server-to-get-a-managed-identity"></a>Aktualisieren Sie einen vorhandenen Azure Database for PostgreSQL-Server, um eine verwaltete Identität zu erhalten.

    ```azurecli-interactive
    az postgres server update --resource-group <resource_group> --name <server_name> --assign-identity
    ```

2. Legen Sie die **Schlüsselberechtigungen** ( **Get** , **Wrap** , **Unwrap** ) für den **Prinzipal** (Name des PostgreSQL-Einzelservers) fest.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Einrichten der Datenverschlüsselung für Azure Database for PostgreSQL-Einzelserver

1. Aktivieren Sie die Datenverschlüsselung für den Azure Database for PostgreSQL-Einzelserver mithilfe des Schlüssels, der in Azure Key Vault erstellt wurde.

    ```azurecli-interactive
    az postgres server key create --name <server_name> -g <resource_group> --kid <key_url>
    ```

    Schlüssel-URL: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Verwenden von Datenverschlüsselung für Wiederherstellungs- oder Replikatserver

Nachdem Azure Database for PostgreSQL mit dem vom Kunden verwalteten Schlüssel, der in Key Vault gespeichert ist, verschlüsselt wurde, wird jede neu erstellte Kopie des Servers ebenfalls verschlüsselt. Sie können diese neue Kopie entweder durch einen lokalen Wiederherstellungsvorgang, einen Geowiederherstellungsvorgang oder durch einen Replikationsvorgang (lokal/regionsübergreifend) erstellen. Sie können für einen verschlüsselten PostgreSQL-Einzelserver also die folgenden Schritte ausführen, um einen verschlüsselten wiederhergestellten Server zu erstellen.

### <a name="creating-a-restoredreplica-server"></a>Erstellen eines wiederhergestellten/Replikatservers

* [Erstellen eines wiederhergestellten Servers](howto-restore-server-cli.md)
* [Erstellen eines Lesereplikatservers](howto-read-replicas-cli.md)

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Nach der Serverwiederherstellung – erneutes Überprüfen der Datenverschlüsselung des wiederhergestellten Servers

*   Zuweisen der Identität für den Replikatserver
```azurecli-interactive
az postgres server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Abrufen des vorhandenen Schlüssels, der für den wiederhergestellten Server bzw. den Replikatserver verwendet werden muss

```azurecli-interactive
az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Festlegen der Richtlinie für die neue Identität für den wiederhergestellten Server bzw. den Replikatserver

```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Erneutes Überprüfen des wiederhergestellten Servers bzw. des Replikatservers mit dem Verschlüsselungsschlüssel

```azurecli-interactive
az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Zusätzliche Funktionen für den Schlüssel, der für den Azure Database for PostgreSQL-Einzelserver verwendet wird

### <a name="get-the-key-used"></a>Abrufen des verwendeten Schlüssels

```azurecli-interactive
az postgres server key show --name <server name>  -g <resource_group> --kid <key url>
```

Schlüssel-URL: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Auflisten des verwendeten Schlüssels

```azurecli-interactive
az postgres server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Löschen des verwendeten Schlüssels

```azurecli-interactive
az postgres server key delete -g <resource_group> --kid <key url> 
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Verwenden einer Azure Resource Manager-Vorlage zum Aktivieren von Datenverschlüsselung

Sie können die Datenverschlüsselung nicht nur im Azure-Portal, sondern auch auf Ihrem Azure Database for PostgreSQL-Einzelserver aktivieren, indem Sie Azure Resource Manager-Vorlagen für neue und vorhandene Server verwenden.

### <a name="for-a-new-server"></a>Neuer Server

Verwenden Sie eine der vorab erstellten Azure Resource Manager-Vorlagen, um den Server mit aktivierter Datenverschlüsselung bereitzustellen: [Beispiel mit Datenverschlüsselung](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Diese Azure Resource Manager-Vorlage erstellt einen Azure Database for PostgreSQL-Einzelserver und verwendet **KeyVault** und **Key** , die als Parameter übergeben werden, um die Datenverschlüsselung auf dem Server zu aktivieren.

### <a name="for-an-existing-server"></a>Vorhandener Server
Sie können Azure Resource Manager-Vorlagen auch verwenden, um die Datenverschlüsselung auf vorhandenen Azure Database for PostgreSQL-Einzelservern zu aktivieren.

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
      "type": "Microsoft.DBforPostgreSQL/servers",
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
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
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

 Weitere Informationen zur Datenverschlüsselung finden Sie unter [Datenverschlüsselung auf Azure Database for PostgreSQL-Einzelservern mit kundenseitig verwalteten Schlüsseln](concepts-data-encryption-postgresql.md).
