---
title: Verwalten von Ressourcen für die Core-API (SQL) von Azure Cosmos DB mit PowerShell
description: Verwalten Sie Ressourcen für die Core-API (SQL) von Azure Cosmos DB mit PowerShell.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 2d99a760e3bf44febcee05bf9827479616bf7bd6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333425"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>Verwalten von Ressourcen für die Core-API (SQL) von Azure Cosmos DB mit PowerShell
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In der folgenden Anleitung erfahren Sie, wie Sie PowerShell-Skripts verwenden und die Verwaltung von Ressourcen für die Core-API (SQL) von Azure Cosmos DB (einschließlich Cosmos-Konten, Datenbanken, Container und Durchsatz) automatisieren. PowerShell-Cmdlets für andere APIs finden Sie unter [Azure PowerShell-Beispiele für Azure Cosmos DB: Cassandra-API](powershell-samples-cassandra.md), [Azure PowerShell-Beispiele für Azure Cosmos DB: MongoDB-API](powershell-samples-mongodb.md), [Azure PowerShell-Beispiele für Azure Cosmos DB: Gremlin-API](powershell-samples-gremlin.md) und [Azure PowerShell-Beispiele für Azure Cosmos DB: Tabellen-API](powershell-samples-table.md).

> [!NOTE]
> In den Beispielen in diesem Artikel werden die [Az.CosmosDB](/powershell/module/az.cosmosdb)-Verwaltungs-Cmdlets verwendet. Informationen zu den letzten Änderungen finden Sie auf der Referenzseite zur [Az.CosmosDB](/powershell/module/az.cosmosdb)-API.

Zur plattformübergreifenden Verwaltung von Azure Cosmos DB können Sie die Cmdlets `Az` und `Az.CosmosDB` mit [PowerShell (plattformübergreifend)](/powershell/scripting/install/installing-powershell) und die [Azure CLI](manage-with-cli.md), die [REST-API][rp-rest-api] oder das [Azure-Portal](create-sql-api-dotnet.md#create-account) verwenden.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Erste Schritte

Führen Sie die Schritte unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure] aus, um PowerShell zu installieren und sich bei Ihrem Azure-Konto anzumelden.

> [!IMPORTANT]
> Azure Cosmos DB-Ressourcen können nicht umbenannt werden, da dies gegen die Funktionsweise von Azure Resource Manager mit Ressourcen-URIs verstößt.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-Konten

In den folgenden Abschnitten erfahren Sie, wie Sie Azure Cosmos DB-Konten verwalten. Dabei lernen Sie u. a. Folgendes:

* [Erstellen eines Azure Cosmos DB-Kontos](#create-account)
* [Aktualisieren eines Azure Cosmos DB-Kontos](#update-account)
* [Auflisten aller Azure Cosmos DB-Konten in einem Abonnement](#list-accounts)
* [Abrufen der Eigenschaften eines Azure Cosmos DB-Kontos](#get-account)
* [Azure Cosmos DB-Konto löschen](#delete-account)
* [Aktualisieren der Tags eines Azure Cosmos DB-Kontos](#update-tags)
* [Anzeigen von Schlüsseln für ein Azure Cosmos DB-Konto](#list-keys)
* [Erneutes Generieren von Schlüsseln für ein Azure Cosmos DB-Konto](#regenerate-keys)
* [Anzeigen von Verbindungszeichenfolgen für ein Azure Cosmos DB-Konto](#list-connection-strings)
* [Anpassen der Failoverpriorität für ein Azure Cosmos DB-Konto](#modify-failover-priority)
* [Auslösen eines manuellen Failovers für ein Azure Cosmos-Konto](#trigger-manual-failover)
* [Auflisten von Ressourcensperren in einem Azure Cosmos DB-Konto](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Erstellen eines Azure Cosmos DB-Kontos

Mit diesem Befehl wird ein Azure Cosmos DB-Datenbankkonto mit [mehreren Regionen][distribute-data-globally], [automatischem Failover](how-to-manage-database-account.md#automatic-failover) und einer [Konsistenzrichtlinie](consistency-levels.md) mit begrenzter Veraltung erstellt.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName`: Die Azure-Ressourcengruppe, in der das Cosmos-Konto bereitgestellt werden soll. Es muss bereits vorhanden sein.
* `$locations`: Die Regionen für das Datenbankkonto. Die Region mit `FailoverPriority 0` ist die Schreibregion.
* `$accountName`: der Name des Azure Cosmos DB-Kontos. Der Name muss eindeutig sein, darf nur alphanumerische Kleinbuchstaben bzw. Zeichen und den Bindestrich enthalten und muss eine Länge von 3 bis 31 Zeichen haben.
* `$apiKind`: Der Typ des zu erstellenden Cosmos-Kontos. Weitere Informationen finden Sie unter [APIs in Cosmos DB](introduction.md#simplified-application-development).
* `$consistencyPolicy`, `$maxStalenessInterval` und `$maxStalenessPrefix`: Die Standardkonsistenzebene und die Einstellungen des Azure Cosmos-Kontos. Weitere Informationen finden Sie unter [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md).

Für Azure Cosmos-Konten können eine IP-Firewall, VNET-Dienstendpunkte und private Endpunkte konfiguriert werden. Informationen zum Konfigurieren der IP-Firewall für Azure Cosmos DB finden Sie unter [Konfigurieren der IP-Firewall](how-to-configure-firewall.md). Informationen zum Aktivieren von Dienstendpunkten für Azure Cosmos DB finden Sie unter [Konfigurieren des Zugriffs über virtuelle Netzwerke](how-to-configure-vnet-service-endpoint.md). Informationen zum Aktivieren von privaten Endpunkten für Azure Cosmos DB finden Sie unter [Konfigurieren des Zugriffs über private Endpunkte](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> Auflisten aller Azure Cosmos-Konten in einer Ressourcengruppe

Mit diesem Befehl werden alle Azure Cosmos-Konten einer Ressourcengruppe aufgelistet.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Abrufen der Eigenschaften eines Azure Cosmos DB-Kontos

Mit diesem Befehl können Sie die Eigenschaften eines Azure Cosmos DB-Kontos abrufen.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Aktualisieren eines Azure Cosmos DB-Kontos

Mit diesem Befehl können Sie die Eigenschaften Ihres Azure Cosmos DB-Datenbankkontos aktualisieren. Die Eigenschaften für folgende Aufgaben können aktualisiert werden:

* Hinzufügen oder Entfernen von Regionen
* Anpassen der Standardkonsistenzrichtlinie
* Anpassen des IP-Bereichsfilters
* Anpassen von Konfigurationen für das virtuelle Netzwerk
* Aktivieren von Schreibvorgängen in mehreren Regionen

> [!NOTE]
> Sie können nicht gleichzeitig Regionselemente (`locations`) hinzufügen oder entfernen und andere Eigenschaften für ein Azure Cosmos-Konto ändern. Die Anpassung von Regionen muss getrennt von anderen Änderungen des Kontos vorgenommen werden.
> [!NOTE]
> Mit diesem Befehl können Sie Regionen hinzufügen und entfernen, aber weder Failoverprioritäten anpassen noch ein manuelles Failover auslösen. Weitere Informationen finden Sie unter [Anpassen der Failoverpriorität für ein Azure Cosmos DB-Konto](#modify-failover-priority) und [Auslösen eines manuellen Failovers](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> Aktivieren mehrerer Schreibregionen für ein Azure Cosmos-Konto

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Löschen eines Azure Cosmos DB-Kontos

Mit diesem Befehl wird ein vorhandenes Azure Cosmos-Konto gelöscht.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Aktualisieren der Tags eines Azure Cosmos DB-Kontos

Mit diesem Befehl werden die [Azure-Ressourcentags][azure-resource-tags] für ein Azure Cosmos-Konto festgelegt. Tags können sowohl bei der Kontoerstellung (mit `New-AzCosmosDBAccount`) als auch bei der Kontoaktualisierung (mit `Update-AzCosmosDBAccount`) festgelegt werden.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> Auflisten von Kontoschlüsseln

Wenn Sie ein Azure Cosmos-Konto erstellen, generiert der Dienst zwei primäre Zugriffsschlüssel, die für die Authentifizierung verwendet werden können, wenn der Zugriff auf das Azure Cosmos-Konto erfolgt. Schreibgeschützte Schlüssel für die Authentifizierung von schreibgeschützten Vorgängen werden ebenfalls generiert.
Durch Bereitstellen von zwei Zugriffsschlüsseln ermöglicht Azure Cosmos DB Ihnen jeweils das erneute Generieren und Rotieren eines Schlüssels ohne Unterbrechung des Zugriffs auf das Azure Cosmos-Konto.
Cosmos DB-Konten verfügen über zwei Lese-/Schreibschlüssel (primär und sekundär) und zwei schreibgeschützte Schlüssel (primär und sekundär).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a>: Verbindungszeichenfolgen auflisten

Mit dem folgenden Befehl werden Verbindungszeichenfolgen zum Verbinden von Apps mit dem Cosmos DB-Konto abgerufen.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> Erneutes Generieren von Kontoschlüsseln

Zugriffsschlüssel für ein Azure Cosmos DB-Konto sollten in regelmäßigen Abständen erneut generiert werden, um für die Sicherheit von Verbindungen zu sorgen. Dem Konto werden ein primärer und ein sekundärer Zugriffsschlüssel zugewiesen. Hierdurch wird ein anhaltender Clientzugriff sichergestellt, während jeweils ein Schlüssel erneut generiert wird.
Für Azure Cosmos DB-Konten werden vier Schlüsselarten verwendet: Primär- und Sekundärschlüssel sowie PrimaryReadonly- und SecondaryReadonly-Schlüssel.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> Aktivieren des automatischen Failovers

Mit dem folgenden Befehl wird für ein Cosmos DB-Konto festgelegt, dass automatisch ein Failover zur sekundären Region ausgeführt wird, falls die primäre Region nicht verfügbar ist.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> Anpassen der Failoverpriorität für ein Azure Cosmos DB-Konto

Bei Konten, für die automatisches Failover konfiguriert ist, können Sie die Reihenfolge ändern, in der Cosmos sekundäre Replikate auf primäre Replikate höher stuft, sollten die primären Replikate nicht mehr verfügbar sein.

Im folgenden Beispiel wird von der folgenden aktuellen Failoverpriorität ausgegangen: `West US 2 = 0`, `East US 2 = 1`, `South Central US = 2`. Mit dem Befehl wird dies wie folgt geändert: `West US 2 = 0`, `South Central US = 1`, `East US 2 = 2`.

> [!CAUTION]
> Durch die Änderung des Standorts für `failoverPriority=0` wird ein manuelles Failover für ein Azure Cosmos-Konto ausgelöst. Bei anderen Prioritätsänderungen wird kein Failover ausgelöst.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> Auslösen eines manuellen Failovers

Bei Konten, für die manuelles Failover konfiguriert ist, können Sie ein Failover ausführen und sekundäre Replikate auf primäre Replikate höher stufen, indem Sie `failoverPriority=0` festlegen. Mit diesem Vorgang kann ein Notfallwiederherstellungsverfahren initiiert werden, um die Planung für die Notfallwiederherstellung zu testen.

Gehen Sie im folgenden Beispiel davon aus, dass für das Konto aktuell die Failoverprioritäten `West US 2 = 0` und `East US 2 = 1` festgelegt sind und die Regionen vertauscht werden.

> [!CAUTION]
> Wenn `locationName` in `failoverPriority=0` geändert wird, wird ein manuelles Failover für ein Azure Cosmos DB-Konto ausgelöst. Bei anderen Prioritätsänderungen wird kein Failover ausgelöst.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Auflisten von Ressourcensperren in einem Azure Cosmos DB-Konto

Ressourcensperren können für Azure Cosmos DB-Ressourcen, einschließlich Datenbanken und Sammlungen, eingerichtet werden. Das folgende Beispiel zeigt, wie Sie alle Azure-Ressourcensperren in einem Azure Cosmos DB-Konto auflisten.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB-Datenbank

In den folgenden Abschnitten erfahren Sie, wie Sie Azure Cosmos DB-Datenbanken verwalten. Dabei lernen Sie u. a. Folgendes:

* [Erstellen einer Azure Cosmos DB-Datenbank](#create-db)
* [Erstellen einer Azure Cosmos DB-Datenbank mit gemeinsam genutztem Durchsatz](#create-db-ru)
* [Abrufen des Durchsatzes einer Azure Cosmos DB-Datenbank](#get-db-ru)
* [Migrieren eines Datenbankdurchsatzes zur Autoskalierung](#migrate-db-ru)
* [Auflisten aller Azure Cosmos DB-Datenbanken eines Kontos](#list-db)
* [Abrufen einer einzelnen Azure Cosmos DB-Datenbank](#get-db)
* [Löschen einer Azure Cosmos DB-Datenbank](#delete-db)
* [Einrichten einer Ressourcensperre für eine Azure Cosmos DB-Datenbank, um das Löschen zu verhindern](#create-db-lock)
* [Entfernen einer Ressourcensperre von einer Azure Cosmos DB-Datenbank](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Erstellen einer Azure Cosmos DB-Datenbank

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Erstellen einer Azure Cosmos DB-Datenbank mit gemeinsam genutztem Durchsatz

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Abrufen des Durchsatzes einer Azure Cosmos DB-Datenbank

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>Migrieren eines Datenbankdurchsatzes zur Autoskalierung

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Abrufen aller Azure Cosmos DB-Datenbanken eines Kontos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Abrufen einer einzelnen Azure Cosmos DB-Datenbank

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Löschen einer Azure Cosmos DB-Datenbank

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>Einrichten einer Ressourcensperre für eine Azure Cosmos DB-Datenbank, um das Löschen zu verhindern

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a> Entfernen einer Ressourcensperre von einer Azure Cosmos DB-Datenbank

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB-Container

In den folgenden Abschnitten erfahren Sie, wie Sie Azure Cosmos DB-Container verwalten. Dabei lernen Sie u. a. Folgendes:

* [Erstellen eines Azure Cosmos DB-Containers](#create-container)
* [Erstellen eines Azure Cosmos DB-Containers mit automatischer Skalierung](#create-container-autoscale)
* [Erstellen eines Azure Cosmos DB-Containers mit einem großen Partitionsschlüssel](#create-container-big-pk)
* [Abrufen des Durchsatzes eines Azure Cosmos DB-Containers](#get-container-ru)
* [Migrieren eines Containerdurchsatzes zur Autoskalierung](#migrate-container-ru)
* [Erstellen eines Azure Cosmos DB-Containers mit benutzerdefinierter Indizierung](#create-container-custom-index)
* [Erstellen eines Azure Cosmos DB-Containers mit deaktivierter Indizierung](#create-container-no-index)
* [Erstellen eines Azure Cosmos DB-Containers mit einem eindeutigen Schlüssel und einer Gültigkeitsdauer](#create-container-unique-key-ttl)
* [Erstellen eines Azure Cosmos DB-Containers mit Konfliktauflösung](#create-container-lww)
* [Auflisten aller Azure Cosmos DB-Container einer Datenbank](#list-containers)
* [Abrufen eines einzelnen Azure Cosmos DB-Containers einer Datenbank](#get-container)
* [Löschen eines Azure Cosmos DB-Containers](#delete-container)
* [Einrichten einer Ressourcensperre für einen Azure Cosmos DB-Container, um das Löschen zu verhindern](#create-container-lock)
* [ Entfernen einer Ressourcensperre von einem Azure Cosmos DB-Container](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Erstellen eines Azure Cosmos DB-Containers

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>Erstellen eines Azure Cosmos DB-Containers mit automatischer Skalierung

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Erstellen eines Azure Cosmos DB-Containers mit einem großen Partitionsschlüssel

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Abrufen des Durchsatzes eines Azure Cosmos DB-Containers

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>Migrieren eines Containerdurchsatzes zur Autoskalierung

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Erstellen eines Azure Cosmos DB-Containers mit benutzerdefinierter Indizierungsrichtlinie

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Erstellen eines Azure Cosmos DB-Containers mit deaktivierter Indizierung

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Erstellen eines Azure Cosmos DB-Containers mit einer Richtlinie für einen eindeutigen Schlüssel und mit einer Gültigkeitsdauer

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Erstellen eines Azure Cosmos DB-Containers mit Konfliktauflösung

Übergeben Sie `-Type "Custom" -Path ""`, um alle Konflikte in „ConflictsFeed“ zu schreiben und diese getrennt zu behandeln.

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Wenn Sie eine Richtlinie zur Konfliktlösung für die Verwendung einer gespeicherten Prozedur erstellen möchten, rufen Sie `New-AzCosmosDBSqlConflictResolutionPolicy` auf, und übergeben Sie die Parameter `-Type` und `-ConflictResolutionProcedure`.

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Auflisten aller Azure Cosmos DB-Container einer Datenbank

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Abrufen eines einzelnen Azure Cosmos DB-Containers einer Datenbank

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Löschen eines Azure Cosmos DB-Containers

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>Einrichten einer Ressourcensperre für einen Azure Cosmos DB-Container, um das Löschen zu verhindern

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Entfernen einer Ressourcensperre von einem Azure Cosmos DB-Container

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>Nächste Schritte

* [PowerShell-Beispiele](powershell-samples.md)
* [Verwalten eines Azure Cosmos DB-Kontos](how-to-manage-database-account.md)
* [Erstellen eines Azure Cosmos DB-Containers](how-to-create-container.md)
* [Configure time-to-live in Azure Cosmos DB (Konfigurieren der Gültigkeitsdauer in Azure Cosmos DB)](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: ../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/