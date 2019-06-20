---
title: Erstellen und Verwalten von Azure Cosmos DB mit PowerShell
description: Verwenden Sie Azure PowerShell, um Ihre Azure Cosmos DB-Konten, -Datenbanken, -Container und den zugehörigen Durchsatz zu verwalten.
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: f720b678f2c7a6e564ef3e8fa9ae071b004ed1a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243385"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Verwalten von SQL-API-Ressourcen für Azure Cosmos DB mit PowerShell

In der folgenden Anleitung wird beschrieben, wie Sie mit PowerShell-Skripts beispielsweise die Verwaltung von Azure Cosmos DB-Konten, -Datenbanken, -Containern und des zugehörigen Durchsatzes automatisieren. Sie verwalten Azure Cosmos DB nicht mit spezifischen Cmdlets für diesen Dienst, sondern direkt über den Ressourcenanbieter mithilfe des Cmdlets „AzResource“. Sie können sich alle Eigenschaften anzeigen lassen, die mit PowerShell für den Azure Cosmos DB-Ressourcenanbieter verwaltet werden können. Informationen dazu finden Sie unter [Azure Cosmos DB resource provider schema (Schema des Azure Cosmos DB-Ressourcenanbieters)](/azure/templates/microsoft.documentdb/allversions).

Zur plattformübergreifenden Verwaltung von Azure Cosmos DB können Sie die [Azure CLI](manage-with-cli.md), die [REST-API][rp-rest-api] oder das [Azure-Portal](create-sql-api-dotnet.md#create-account) verwenden.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Erste Schritte

Führen Sie die Schritte unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure] aus, um PowerShell zu installieren und sich bei Ihrem Azure-Konto anzumelden.

* Wenn die folgenden Befehle ohne Benutzerbestätigung ausgeführt werden sollen, fügen Sie das Flag `-Force` an den Befehl an.
* Alle folgenden Befehle sind synchron.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos DB-Konten

In den folgenden Abschnitten erfahren Sie, wie Sie Azure Cosmos DB-Konten verwalten. Dabei lernen Sie u. a. Folgendes:

* [Erstellen eines Azure Cosmos DB-Kontos](#create-account)
* [Aktualisieren eines Azure Cosmos DB-Kontos](#update-account)
* [Abrufen der Eigenschaften eines Azure Cosmos DB-Kontos](#get-account)
* [Löschen eines Azure Cosmos DB-Kontos](#delete-account)
* [Aktualisieren der Tags eines Azure Cosmos DB-Kontos](#update-tags)
* [Anzeigen von Schlüsseln für ein Azure Cosmos DB-Konto](#list-keys)
* [Erneutes Generieren von Schlüsseln für ein Azure Cosmos DB-Konto](#regenerate-keys)
* [Anzeigen von Verbindungszeichenfolgen für ein Azure Cosmos DB-Konto](#list-connection-strings)
* [Anpassen der Failoverpriorität für ein Azure Cosmos DB-Konto](#modify-failover-priority)

### <a id="create-account"></a> Erstellen eines Azure Cosmos DB-Kontos

Mit diesem Befehl können Sie ein Azure Cosmos DB-Datenbankkonto erstellen. Konfigurieren Sie das neue Datenbankkonto entweder mit einer einzelnen Region oder mit [mehreren Regionen][distribute-data-globally] mit einer bestimmten [Konsistenzrichtlinie](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
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

* `$accountName`: der Name des Azure Cosmos DB-Kontos. Dieser darf nur alphanumerische Kleinbuchstaben und das Zeichen „-“ enthalten und muss zwischen 3 und 50 Zeichen lang sein.
* `$location`: der Standort für das Azure Cosmos DB-Konto.
* `$locations`: die Replikatregionen für das Datenbankkonto. Es muss für jedes Datenbankkonto eine Schreibregion mit dem Failoverprioritätswert 0 geben.
* `$consistencyPolicy`: die Standardkonsistenzebene des Azure Cosmos DB-Kontos. Weitere Informationen finden Sie unter [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties`: die Eigenschaftswerte, die dem Azure Resource Manager-Anbieter von Cosmos DB zum Bereitstellen des Kontos übergeben werden.

Azure Cosmos DB-Konten können sowohl einer IP-Firewall als auch mit Dienstendpunkten eines virtuellen Netzwerks konfiguriert werden. Informationen zum Konfigurieren der IP-Firewall für Azure Cosmos DB finden Sie unter [Konfigurieren der IP-Firewall](how-to-configure-firewall.md).  Weitere Informationen zum Aktivieren von Dienstendpunkten für Azure Cosmos DB finden Sie unter [Konfigurieren des Zugriffs über virtuelle Netzwerke](how-to-configure-vnet-service-endpoint.md).

### <a id="get-account"></a> Abrufen der Eigenschaften eines Azure Cosmos DB-Kontos

Mit diesem Befehl können Sie die Eigenschaften eines Azure Cosmos DB-Kontos abrufen.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Aktualisieren eines Azure Cosmos DB-Kontos

Mit diesem Befehl können Sie die Eigenschaften Ihres Azure Cosmos DB-Datenbankkontos aktualisieren. Die Eigenschaften für folgende Aufgaben können aktualisiert werden:

* Hinzufügen oder Entfernen von Regionen
* Anpassen der Standardkonsistenzrichtlinie
* Anpassen die Failoverrichtlinie
* Anpassen des IP-Bereichsfilters
* Anpassen von Konfigurationen für das virtuelle Netzwerk
* Aktivieren der Multimasterarchitektur

> [!NOTE]
> Mit diesem Befehl können Sie Regionen hinzufügen und entfernen, aber keine Failovereigenschaften ändern. Informationen zum Anpassen der Failoverprioritäten finden Sie unter [Anpassen der Failoverpriorität für ein Azure Cosmos DB-Konto](#modify-failover-priority).

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Löschen eines Azure Cosmos DB-Kontos

Mit diesem Befehl können Sie ein Azure Cosmos DB-Konto löschen.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Aktualisieren der Tags eines Azure Cosmos DB-Kontos

Im folgenden Beispiel wird beschrieben, wie Sie [Azure-Ressourcentags][azure-resource-tags] für ein Azure Cosmos DB-Konto festlegen.

> [!NOTE]
> Sie können diesen Befehl mit den Befehlen zum Erstellen oder Aktualisieren kombinieren, indem Sie das `-Tags`-Flag mit dem entsprechenden Parameter anfügen.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> Auflisten von Kontoschlüsseln

Wenn Sie ein Azure Cosmos DB-Konto erstellen, generiert der Dienst zwei Hauptzugriffsschlüssel, die für die Authentifizierung verwendet werden können, wenn der Zugriff auf das Azure Cosmos DB-Konto erfolgt. Durch Bereitstellen von zwei Zugriffsschlüsseln ermöglicht Azure Cosmos DB Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Zugriffs auf das Azure Cosmos DB-Konto. Schreibgeschützte Schlüssel für die Authentifizierung von schreibgeschützten Vorgängen sind ebenfalls verfügbar. Es gibt zwei Lese-Schreib-Schlüssel (primär und sekundär) und zwei schreibgeschützte Schlüssel (primär und sekundär).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a>: Verbindungszeichenfolgen auflisten

Für MongoDB-Konten kann die Verbindungszeichenfolge zum Herstellen einer Verbindung Ihrer MongoDB-App mit dem Datenbankkonto mit dem folgenden Befehl abgerufen werden.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> Erneutes Generieren von Kontoschlüsseln

Zugriffsschlüssel für ein Azure Cosmos DB-Konto sollten in regelmäßigen Abständen erneut generiert werden, um die Sicherheit von Verbindungen langfristig zu erhöhen. Dem Konto werden ein primärer und ein sekundärer Zugriffsschlüssel zugewiesen. Dadurch wird ein anhaltender Clientzugriff sichergestellt, wenn einer der beiden Schlüssel erneut generiert wird. Für Azure Cosmos DB-Konten werden vier Schlüsselarten verwendet: Primär- und Sekundärschlüssel sowie PrimaryReadonly- und SecondaryReadonly-Schlüssel.

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a> Anpassen der Failoverpriorität für ein Azure Cosmos DB-Konto

Bei Datenbankkonten mit mehreren Regionen können Sie die Failoverpriorität der verschiedenen Regionen ändern, in denen sich das Azure Cosmos DB-Datenbankkonto befindet. Weitere Informationen zum Failover in Ihrem Azure Cosmos DB-Datenbankkonto finden Sie unter [Globale Verteilung von Daten mit Azure Cosmos DB][distribute-data-globally].

Im unten stehenden Beispiel sei für das Konto angenommen, dass für„West US“ (USA, Westen) eine Failoverpriorität von 0 und für „East US“ (USA, Osten) eine Priorität von 1 festgelegt ist. Im folgenden Beispiel werden die Regionen vertauscht.

> [!CAUTION]
> Dieser Vorgang löst ein manuelles Failover Ihres Kontos auf die neue Region aus. Der Wert für „failoverPriority“ beträgt 0.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos DB-Datenbank

In den folgenden Abschnitten erfahren Sie, wie Sie Azure Cosmos DB-Datenbanken verwalten. Dabei lernen Sie u. a. Folgendes:

* [Erstellen einer Azure Cosmos DB-Datenbank](#create-db)
* [Erstellen einer Azure Cosmos DB-Datenbank mit gemeinsam genutzten Durchsatz](#create-db-ru)
* [Abrufen aller Azure Cosmos DB-Datenbanken eines Kontos](#get-all-db)
* [Abrufen einer einzelnen Azure Cosmos DB-Datenbank](#get-db)
* [Löschen einer Azure Cosmos DB-Datenbank](#delete-db)

### <a id="create-db"></a>Erstellen einer Azure Cosmos DB-Datenbank

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Erstellen einer Azure Cosmos DB-Datenbank mit gemeinsam genutzten Durchsatz

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-all-db"></a>Abrufen aller Azure Cosmos DB-Datenbanken eines Kontos

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Abrufen einer einzelnen Azure Cosmos DB-Datenbank

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Löschen einer Azure Cosmos DB-Datenbank

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos DB-Container

In den folgenden Abschnitten erfahren Sie, wie Sie Azure Cosmos DB-Container verwalten. Dabei lernen Sie u. a. Folgendes:

* [Erstellen eines Azure Cosmos DB-Containers](#create-container)
* [Erstellen eines Azure Cosmos DB-Containers mit gemeinsam genutzten Durchsatz](#create-container-ru)
* [Erstellen eines Azure Cosmos DB-Containers mit benutzerdefinierter Indizierungsrichtlinie](#create-container-custom-index)
* [Erstellen eines Azure Cosmos DB-Containers mit deaktivierter Indizierung](#create-container-no-index)
* [Erstellen eines Azure Cosmos DB-Containers mit einer Richtlinie für einen eindeutigen Schlüssel und mit einer Gültigkeitsdauer](#create-container-unique-key-ttl)
* [Erstellen eines Azure Cosmos DB-Containers mit Konfliktauflösung](#create-container-lww)
* [Abrufen aller Azure Cosmos DB-Container einer Datenbank](#list-all-container)
* [Abrufen eines einzelnen Azure Cosmos DB-Containers einer Datenbank](#get-container)
* [Löschen eines Azure Cosmos DB-Containers](#delete-container)

### <a id="create-container"></a>Erstellen eines Azure Cosmos DB-Containers

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{ "Throughput"="400" }
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-ru"></a>Erstellen eines Azure Cosmos DB-Containers mit gemeinsam genutzten Durchsatz

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Erstellen eines Azure Cosmos DB-Containers mit benutzerdefinierter Indizierungsrichtlinie

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent"; 
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Erstellen eines Azure Cosmos DB-Containers mit deaktivierter Indizierung

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Erstellen eines Azure Cosmos DB-Containers mit einer Richtlinie für einen eindeutigen Schlüssel und mit einer Gültigkeitsdauer

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent"; 
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    }; 
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Erstellen eines Azure Cosmos DB-Containers mit Konfliktauflösung

Wenn Sie eine gespeicherte Prozedur verwenden möchten, können Sie eine Konfliktauflösungsrichtlinie erstellen. Legen Sie dazu `"mode"="custom"` und den Auflösungspfad als Name der gespeicherten Prozedur (`"conflictResolutionPath"="myResolverStoredProcedure"`) fest. Legen Sie `"mode"="custom"` und `"conflictResolutionPath"=""` fest, um alle Konflikte in „ConflictsFeed“ zu schreiben und diese getrennt zu behandeln.

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins"; 
            "conflictResolutionPath"="/myResolutionPath"
        }
    }; 
    "options"=@{ "Throughput"="400" }
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-all-container"></a>Abrufen aller Azure Cosmos DB-Container einer Datenbank

```azurepowershell-interactive
# List all Azure Cosmos containers in a database 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Abrufen eines einzelnen Azure Cosmos DB-Containers einer Datenbank

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Löschen eines Azure Cosmos DB-Containers

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Nächste Schritte

* [PowerShell-Beispiele](powershell-samples.md)
* [Verwalten eines Azure Cosmos DB-Kontos](how-to-manage-database-account.md)
* [Erstellen eines Containers in Azure Cosmos DB](how-to-create-container.md)
* [Configure time-to-live in Azure Cosmos DB (Konfigurieren der Gültigkeitsdauer in Azure Cosmos DB)](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/