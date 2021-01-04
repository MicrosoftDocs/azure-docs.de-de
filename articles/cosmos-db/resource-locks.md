---
title: Verhindern des Löschens oder Änderns von Azure Cosmos DB-Ressourcen
description: Verwenden Sie Azure-Ressourcensperren, um zu verhindern, dass Azure Cosmos DB-Ressourcen gelöscht oder geändert werden.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 1c8c766208132aec115e1fbeb15af3a057c3de3e
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636689"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Verhindern des Löschens oder Änderns von Azure Cosmos DB-Ressourcen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Als Administrator möchten Sie möglicherweise ein Azure Cosmos-Konto, eine Datenbank oder einen Container sperren, um zu verhindern, dass andere Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern. Sie können die Sperrebene auf CanNotDelete oder ReadOnly festlegen.

- **CanNotDelete** bedeutet, dass autorisierte Benutzer weiterhin eine Ressource lesen und ändern, aber nicht löschen können.
- **ReadOnly** bedeutet, dass autorisierte Benutzer eine Ressource zwar lesen, aber nicht löschen oder aktualisieren können. Mit dieser Sperre erzielen Sie einen ähnlichen Effekt wie durch die Beschränkung sämtlicher autorisierter Benutzer auf die Berechtigungen der Leserolle.

## <a name="how-locks-are-applied"></a>Anwenden von Sperren

Wenn Sie eine Sperre in einem übergeordneten Bereich anwenden, erben alle Ressourcen in diesem Bereich die entsprechende Sperre. Auch Ressourcen, die Sie später hinzufügen, erben die Sperre aus dem übergeordneten Element. Die restriktivste Sperre in der Vererbung hat Vorrang.

Im Gegensatz zur rollenbasierten Zugriffssteuerung in Azure verwenden Sie Verwaltungssperren, um eine Einschränkung für alle Benutzer und Rollen zu aktivieren. Informationen zu Azure RBAC für Azure Cosmos DB finden Sie unter [Rollenbasierte Azure-Zugriffssteuerung in Azure Cosmos DB](role-based-access-control.md).

Resource Manager-Sperren gelten nur für Vorgänge auf der Verwaltungsebene (also für Vorgänge, die an https://management.azure.com gesendet werden). Die Ausführung ressourceneigener Funktionen wird durch die Sperren nicht eingeschränkt. Die Ressourcenänderungen sind eingeschränkt, die Ressourcenvorgänge jedoch nicht. Beispielsweise verhindert eine ReadOnly-Sperre für einen Azure Cosmos-Container das Löschen oder Ändern des Containers. Sie verhindert jedoch nicht das Erstellen, Aktualisieren oder Löschen von Daten im Container. Datentransaktionen sind zulässig, da diese Vorgänge nicht an https://management.azure.com gesendet werden.

## <a name="manage-locks"></a>Verwalten von Sperren

> [!WARNING]
> Ressourcensperren funktionieren nicht bei Änderungen, die von Benutzern durchgeführt werden, die mithilfe von Kontoschlüsseln auf Azure Cosmos DB zugreifen, es sei denn, das Azure Cosmos-Konto wird zuerst durch Aktivieren der disableKeyBasedMetadataWriteAccess-Eigenschaft gesperrt. Stellen Sie vor dem Aktivieren dieser Eigenschaft sicher, dass sie keine vorhandenen Anwendungen unterbricht, die mithilfe von SDK, Azure-Portal oder Drittanbietertools, die über Kontoschlüssel eine Verbindung herstellen und Ressourcenänderungen wie Ändern des Durchsatzes, Aktualisieren von Indexrichtlinien usw. durchführen, Änderungen an Ressourcen vornehmen. Weitere Informationen und eine Prüfliste, um sicherzustellen, dass Ihre Anwendungen weiterhin funktionieren, finden Sie unter [Verhindern von Änderungen aus den Azure Cosmos DB SDKs](role-based-access-control.md#prevent-sdk-changes)

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Vorlage

Beim Anwenden einer Sperre auf eine Azure Cosmos DB-Ressource verwenden Sie die folgenden Formate:

- Name: `{resourceName}/Microsoft.Authorization/{lockName}`
- Typ: `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> Wenn Sie ein vorhandenes Azure Cosmos-Konto ändern, schließen Sie bei der erneuten Bereitstellung mit dieser Eigenschaft auch die anderen Eigenschaften für Ihr Konto und Ihre untergeordneten Ressourcen mit ein. Wenn Sie diese Vorlage unverändert bereitstellen, werden alle Ihre Kontoeigenschaften zurückgesetzt.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure Resource Manager-Sperren](../azure-resource-manager/management/lock-resources.md)
