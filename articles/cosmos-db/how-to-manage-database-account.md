---
title: Verwalten von Datenbankkonten in Azure Cosmos DB
description: Verwalten von Datenbankkonten in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: abd50f4e2ca08bea2af491f4b3991278a6dc3b5e
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399884"
---
# <a name="manage-an-azure-cosmos-account"></a>Verwalten eines Azure Cosmos-Kontos

Dieser Artikel beschreibt die Verwaltung verschiedener Aufgaben für ein Azure Cosmos-Konto mithilfe von Azure-Portal, Azure PowerShell, der Azure-Befehlszeilenschnittstelle und Azure Resource Manager-Vorlagen.

## <a name="create-an-account"></a>Erstellen eines Kontos

### <a id="create-database-account-via-portal"></a>Azure-Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case.

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations WestUS=0 EastUS=1 \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="create-database-account-via-arm-template"></a>Azure Resource Manager-Vorlage

Diese Azure Resource Manager-Vorlage erstellt ein Azure Cosmos-Konto für alle unterstützten APIs, die mit zwei Regionen und Optionen zur Auswahl der Konsistenzebene, von automatischem Failover und Multimaster konfiguriert sind. Klicken Sie zum Bereitstellen dieser Vorlage auf der Infoseite zum [Erstellen eines Azure Cosmos-Kontos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account) auf „Bereitstellen in Azure“.

## <a name="addremove-regions-from-your-database-account"></a>Hinzufügen/Entfernen von Regionen für Ihr Datenbankkonto

### <a id="add-remove-regions-via-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an. 

1. Navigieren Sie zu Ihrem Azure Cosmos-Konto, und öffnen Sie das Menü **Daten global replizieren**.

1. Wenn Sie Regionen hinzufügen möchten, wählen Sie auf der Karte die Sechsecke mit der Beschriftung **+** aus, die den gewünschten Regionen entsprechen. Alternativ können Sie zum Hinzufügen einer Region die Option **+ Region hinzufügen** und anschließend eine Region aus dem Dropdownmenü auswählen.

1. Wenn Sie Regionen entfernen möchten, entfernen Sie sie von der Karte, indem Sie die blauen, mit einem Häkchen versehenen Sechsecke auswählen. Alternativ können Sie auch neben der Region auf der rechten Seite das Papierkorbsymbol (🗑) auswählen.

1. Wählen Sie zum Speichern der Änderungen **OK** aus.

   ![Menü zum Hinzufügen oder Entfernen von Regionen](./media/how-to-manage-database-account/add-region.png)

Im Schreibmodus mit einer einzelnen Region kann die Schreibregion nicht entfernt werden. In diesem Fall muss erst ein Failover auf eine andere Region durchgeführt werden, um die aktuelle Schreibregion löschen zu können.

Im Schreibmodus mit mehreren Regionen können Sie beliebige Regionen hinzufügen oder entfernen, solange noch mindestens eine Region vorhanden ist.

### <a id="add-remove-regions-via-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations westus=0

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>Konfigurieren mehrerer Schreibregionen

### <a id="configure-multiple-write-regions-portal"></a>Azure-Portal

Öffnen Sie die Registerkarte **Daten global replizieren**, und wählen Sie **Aktivieren** aus, um Schreibvorgänge für mehrere Regionen zu aktivieren. Nachdem Sie Schreibvorgänge für mehrere Regionen aktiviert haben, werden alle Leseregionen, die aktuell in Ihrem Konto konfiguriert sind, zu Lese- und Schreibregionen. 

> [!NOTE]
> Nach dem Aktivieren von Schreibvorgängen in mehreren Regionen ist keine Deaktivierung mehr möglich. 

![Screenshot der Konfiguration von Multimaster im Azure Cosmos-Konto](./media/how-to-manage-database-account/single-to-multi-master.png)

Wenden Sie sich mit weiteren Fragen zu dieser Funktion an den askcosmosdb@microsoft.com-Alias. 

### <a id="configure-multiple-write-regions-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager-Vorlage

Für ein Konto kann die Migration von Singlemaster zu Multimaster durch Bereitstellen der Resource Manager-Vorlage, die zum Erstellen des Kontos verwendet wurde, und Festlegen von `enableMultipleWriteLocations: true` durchgeführt werden. Die folgende Azure Resource Manager-Vorlage ist eine absolut minimale Vorlage, die ein Azure Cosmos-Konto für die SQL-API mit einer einzelnen Region und aktiviertem Multimaster bereitstellt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations": [
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Aktivieren des automatischen Failovers für Ihr Azure Cosmos-Konto

Die Option für automatisches Failover ermöglicht Azure Cosmos DB das Failover auf die Region mit der höchsten Failoverpriorität ohne Benutzereingriff, sollte bei einer Region die Nichtverfügbarkeit eintreten. Wenn automatisches Failover aktiviert ist, kann die Regionspriorität geändert werden. Das Konto muss über zwei oder mehr Regionen verfügen, um automatisches Failover zu aktivieren.

### <a id="enable-automatic-failover-via-portal"></a>Azure-Portal

1. Öffnen Sie in Ihrem Azure Cosmos-Konto den Bereich **Daten global replizieren**.

2. Wählen Sie im oberen Bereich die Option **Automatisches Failover** aus.

   ![Menü „Daten global replizieren“](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Vergewissern Sie sich im Bereich **Automatisches Failover**, dass **Automatisches Failover aktivieren** auf **EIN** festgelegt ist. 

4. Wählen Sie **Speichern** aus.

   ![Portalmenü für automatisches Failover](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Festlegen von Failoverprioritäten für Ihr Azure Cosmos-Konto

Nachdem ein Cosmos-Konto für automatisches Failover konfiguriert wurde, kann die Failoverpriorität für Regionen geändert werden.

> [!IMPORTANT]
> Die Schreibregion (Failoverpriorität null) kann nicht geändert werden, wenn das Konto für automatisches Failover konfiguriert ist. Um die Schreibregion zu ändern, müssen Sie das automatische Failover deaktivieren und ein manuelles Failover ausführen.

### <a id="set-failover-priorities-via-portal"></a>Azure-Portal

1. Öffnen Sie in Ihrem Azure Cosmos-Konto den Bereich **Daten global replizieren**.

2. Wählen Sie im oberen Bereich die Option **Automatisches Failover** aus.

   ![Menü „Daten global replizieren“](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Vergewissern Sie sich im Bereich **Automatisches Failover**, dass **Automatisches Failover aktivieren** auf **EIN** festgelegt ist.

4. Ziehen Sie zum Ändern der Failoverpriorität die Leseregionen mithilfe der drei Punkte, die auf der linken Seite der Zeile angezeigt werden, wenn Sie darauf zeigen.

5. Wählen Sie **Speichern** aus.

   ![Portalmenü für automatisches Failover](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Ausführen eines manuellen Failovers für ein Azure Cosmos-Konto

> [!IMPORTANT]
> Damit dieser Vorgang erfolgreich ausgeführt wird, muss das Azure Cosmos-Konto für manuelles Failover konfiguriert sein.

Der Prozess zum Ausführen eines manuellen Failovers beinhaltet das Ändern der Schreibregion des Kontos (Failoverpriorität = 0) in eine andere für das Konto konfigurierte Region.

> [!NOTE]
> Für Multimasterkonten kann kein manuelles Failover ausgeführt werden. Für Anwendungen, die das Azure Cosmos SDK verwenden, erkennt das SDK die eingetretene Nichtverfügbarkeit einer Region und leitet dann automatisch zur nächstgelegenen Region um, wenn die Multi-Homing-API im SDK verwendet wird.

### <a id="enable-manual-failover-via-portal"></a>Azure-Portal

1. Navigieren Sie zu Ihrem Azure Cosmos-Konto, und öffnen Sie das Menü **Daten global replizieren**.

2. Wählen Sie im oberen Bereich des Menüs die Option **Manuelles Failover** aus.

   ![Menü „Daten global replizieren“](./media/how-to-manage-database-account/replicate-data-globally.png)

3. Wählen Sie im Menü **Manuelles Failover** Ihre neue Schreibregion aus. Aktivieren Sie das Kontrollkästchen, um anzugeben, dass Sie wissen, dass diese Option Ihre Schreibregion ändert.

4. Wählen Sie **OK** aus, um das Failover zu starten.

   ![Portalmenü für manuelles Failover](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1
```

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen und Beispiele zum Verwalten von Azure-Cosmos-Konto sowie Datenbank und Containern finden Sie in den folgenden Artikeln:

* [Verwalten von Azure Cosmos DB mithilfe von Azure PowerShell](manage-with-powershell.md)
* [Verwalten von Azure Cosmos DB mithilfe der Azure-Befehlszeilenschnittstelle](manage-with-cli.md)
