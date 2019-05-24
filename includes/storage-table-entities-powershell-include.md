---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159769"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Verwalten von Tabellenentitäten

Nachdem Sie nun über eine Tabelle verfügen, können wir uns als Nächstes mit der Verwaltung von Entitäten (oder Zeilen) in der Tabelle beschäftigen. 

Entitäten können bis zu 255 Eigenschaften aufweisen, einschließlich drei Systemeigenschaften: **PartitionKey**, **RowKey** und **Timestamp**. Sie sind für das Einfügen und Aktualisieren der Werte von **PartitionKey** und **RowKey** zuständig. Der Server verwaltet die Werte von **Timestamp**, die nicht geändert werden können. Zusammen identifizieren **PartitionKey** und **RowKey** jede Entität in einer Tabelle eindeutig.

* **PartitionKey**: Bestimmt die Partition, in der die Entität gespeichert ist.
* **RowKey**: Identifiziert die Entität innerhalb der Partition eindeutig.

Sie können bis zu 252 benutzerdefinierte Eigenschaften für eine Entität definieren. 

### <a name="add-table-entities"></a>Hinzufügen von Tabellenentitäten

Verwenden Sie **Add-AzTableRow**, um einer Tabelle Entitäten hinzuzufügen. In den folgenden Beispielen werden Partitionsschlüssel mit den Werten `partition1` und `partition2` sowie Zeilenschlüssel mit Abkürzungen für US-amerikanische Bundesstaaten verwendet. Die Eigenschaften in den einzelnen Entitäten sind `username` und `userid`. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Abfragen der Tabellenentitäten

Mit dem Befehl **Get-AzTableRow** können Sie die Entitäten in einer Tabelle abfragen.

> [!NOTE]
> Die Cmdlets **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName** und  **Get-AzureStorageTableRowByCustomFilter** sind veraltet und werden in einem zukünftigen Versionsupdate entfernt.

#### <a name="retrieve-all-entities"></a>Abrufen aller Entitäten

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Die Ergebnisse dieses Befehls sehen in etwa wie in der folgenden Tabelle aus:

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Abrufen von Entitäten für eine bestimmte Partition

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Die Ergebnisse sehen in etwa wie in der folgenden Tabelle aus:

| userid | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Abrufen von Entitäten für einen bestimmten Wert in einer bestimmten Spalte

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Diese Abfrage ruft einen einzelnen Datensatz ab.

|Feld|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Abrufen von Entitäten unter Verwendung eines benutzerdefinierten Filters 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Diese Abfrage ruft einen einzelnen Datensatz ab.

|Feld|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aktualisieren von Entitäten 

Die Aktualisierung von Entitäten umfasst drei Schritte: Rufen Sie die zu ändernde Entität ab. Nehmen Sie die Änderung vor. Committen Sie die Änderung mithilfe von **Update-AzTableRow**.

Aktualisieren Sie die Entität mit „username = 'Jessie'“, indem Sie sie in „username = 'Jessie2'“ ändern. Dieses Beispiel zeigt außerdem eine weitere Möglichkeit zum Erstellen eines benutzerdefinierten Filters mithilfe von .NET-Typen.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

In den Ergebnissen wird der Jessie2-Datensatz angezeigt.

|Feld|value|
|----|----|
| userid | 2 |
| username | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Löschen von Tabellenentitäten

Sie können eine einzelne Entität oder alle Entitäten in der Tabelle löschen.

#### <a name="deleting-one-entity"></a>Löschen einer einzelnen Entität

Wenn Sie eine einzelne Entität löschen möchten, rufen Sie einen Verweis auf diese Entität ab, und übergeben Sie sie an **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Löschen aller Entitäten in der Tabelle

Wenn Sie alle Entitäten in der Tabelle löschen möchten, rufen Sie sie ab, und übergeben Sie die Ergebnisse an das Remove-Cmdlet. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
