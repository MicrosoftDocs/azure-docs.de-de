---
title: Beispiele einfacher Abfragen
description: Verwenden Sie Azure Resource Graph, um einige einfache Abfragen auszuführen, etwa Abfragen zum Zählen oder Bestellen von Ressourcen oder Abfragen anhand eines bestimmten Tags.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 98b05f74f0d6f7d20b5aa7ed77047818f217f147
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691175"
---
# <a name="starter-resource-graph-queries"></a>Einfache Resource Graph-Abfragen

Der erste Schritt zum Verstehen von Abfragen mit Azure Resource Graph sind Grundkenntnisse der [Abfragesprache](../concepts/query-language.md). Wenn Sie nicht bereits mit [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) vertraut sind, sollten Sie sich über die Grundlagen informieren, um zu verstehen, wie Sie Anforderungen für die gesuchten Ressourcen zusammenstellen müssen.

Wir behandeln die folgenden einfachen Abfragen:

> [!div class="checklist"]
> - [Anzahl der Azure-Ressourcen](#count-resources)
> - [Auflisten von Ressourcen nach Namen sortiert](#list-resources)
> - [Anzeigen aller virtuellen Computer in absteigender Folge nach Namen sortiert](#show-vms)
> - [Anzeigen der ersten fünf virtuellen Computer nach Name und BS-Typ](#show-sorted)
> - [Anzahl von virtuellen Computern nach Betriebssystemtyp](#count-os)
> - [Anzeigen von Ressourcen, die Speicher enthalten](#show-storage)
> - [Liste der öffentlichen IP-Adressen](#list-publicip)
> - [Anzahl von Ressourcen, für die IP-Adressen konfiguriert sind, nach Abonnement](#count-resources-by-ip)
> - [Auflisten von Ressourcen mit einem bestimmten Tagwert](#list-tag)
> - [Auflisten aller Speicherkonten mit einem bestimmten Tagwert](#list-specific-tag)
> - [Aliase für die Ressource eines virtuellen Computers anzeigen](#show-aliases)
> - [Unterschiedliche Werte für einen bestimmten Alias anzeigen](#distinct-alias-values)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Sprachunterstützung

Azure CLI (über eine Erweiterung) und Azure PowerShell (über ein Modul) unterstützen Azure Resource Graph. Überprüfen Sie vor dem Ausführen der folgenden Abfragen, ob Ihre Umgebung bereit ist. Unter [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) und [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) werden die Schritte zum Installieren und Überprüfen der Shellumgebung Ihrer Wahl beschrieben.

## <a name="a-namecount-resourcescount-azure-resources"></a><a name="count-resources"/>Zählen der Azure-Ressourcen

Diese Abfrage gibt die Anzahl der Azure-Ressourcen zurück, die in den Abonnements vorhanden sind, auf die Sie zugreifen können. Sie ist auch eine gute Abfrage, um sicherzustellen, dass für die Shell Ihrer Wahl die entsprechenden Azure Resource Graph-Komponenten installiert und in funktionsfähigem Zustand sind.

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a><a name="list-resources"/>Auflisten von Ressourcen, sortiert nach Name

Mit dieser Abfrage werden alle Ressourcentypen, aber nur die Eigenschaften **name**, **type** und **location** zurückgegeben. Sie nutzt `order by`, um die Eigenschaften in aufsteigender Reihenfolge (`asc`) nach der Eigenschaft **name** zu sortieren.

```kusto
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"/>Anzeigen aller virtuellen Computer nach Name in absteigender Reihenfolge

Wenn Sie nur virtuelle Computer (Typ: `Microsoft.Compute/virtualMachines`) auflisten möchten, können Sie die Eigenschaft **type** in den Ergebnissen zum Abgleich heranziehen. Ähnelt der vorherigen Abfrage, `desc` ändert `order by` in absteigend. `=~` in der Typübereinstimmung weist Resource Graph an, die Groß-/Kleinschreibung nicht zu berücksichtigen.

```kusto
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"/>Anzeigen der ersten fünf virtuellen Computer nach Name und Betriebssystemtyp

Diese Abfrage verwendet `limit`, um nur fünf übereinstimmende Datensätze abzurufen, die nach Namen sortiert werden. Der Typ der Azure-Ressource ist `Microsoft.Compute/virtualMachines`. `project` teilt Azure Ressource Graph mit, welche Eigenschaften einbezogen werden sollen.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a><a name="count-os"/>Zählen von virtuellen Computern nach Betriebssystemtyp

Auf der vorherigen Abfrage aufbauend schränken wir immer noch nach Azure-Ressourcen vom Typ `Microsoft.Compute/virtualMachines` ein, jedoch nicht mehr die Anzahl der zurückgegebenen Datensätze.
Stattdessen haben wir `summarize` und `count()` verwendet, um festzulegen, wie Werte nach Eigenschaft gruppiert und aggregiert werden sollen, in diesem Beispiel `properties.storageProfile.osDisk.osType`. Ein Beispiel, wie diese Zeichenfolge im vollständigen Objekt aussieht, finden Sie unter [Untersuchen virtueller Computer – VM-Ermittlung](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Eine andere Möglichkeit zum Schreiben derselben Abfrage ist, eine Eigenschaft zu erweitern (`extend`) und ihr einen temporären Namen für die Verwendung in der Abfrage zu geben, in diesem Fall **os**. **os** wird dann wie im vorherigen Beispiel von `summarize` und `count()` verwendet.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Denken Sie daran, dass `=~` zwar Übereinstimmungen ohne Berücksichtigung der Groß-/Kleinschreibung ermöglicht, die Verwendung von Eigenschaften (z.B. **properties.storageProfile.osDisk.osType**) in der Abfrage jedoch die richtige Groß-/Kleinschreibung erfordert. Wenn die Eigenschaft in falscher Groß-/Kleinschreibung vorliegt, kann trotzdem ein Wert zurückgegeben werden, aber die Gruppierung oder Zusammenfassung wäre falsch.

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a><a name="show-storage"/>Anzeigen von Ressourcen mit Speicher

Anstatt explizit den Typ zu definieren, mit dem Übereinstimmung vorliegen muss, findet diese Beispielabfrage jede Azure-Ressource, die das Wort **storage** enthält (`contains`).

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a><a name="list-publicip"/>Auflisten aller öffentlichen IP-Adressen

Ähnlich wie bei der vorherigen Abfrage wird jeder Typ mit dem Wort **publicIPAddresses** gefunden.
Diese Abfrage baut auf diesem Muster auf, um nur Ergebnisse mit **properties.ipAddress**
`isnotempty` einzuschließen, nur die **properties.ipAddress** zurückzugeben und die Ergebnisse auf die obersten zu begrenzen (`limit`).
100. Je nach Ihrer ausgewählten Shell müssen Sie Anführungszeichen möglicherweise mit Escapezeichen versehen.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"/>Zählen von Ressourcen, für die IP-Adressen konfiguriert sind (nach Abonnement)

Mithilfe der vorherigen Beispielabfrage und Hinzufügen von `summarize` und `count()` können wir eine Liste nach Abonnement von Ressourcen mit konfigurierten IP-Adressen abrufen.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a><a name="list-tag"/>Auflisten von Ressourcen mit einem bestimmten Tagwert

Wir können die Ergebnisse nach anderen Eigenschaften als dem Azure-Ressourcentyp einschränken, z.B. einem Tag. In diesem Beispiel filtern wir mit dem Tagnamen **Environment** mit dem Wert **Internal** nach Azure-Ressourcen.

```kusto
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Wenn Sie darüber hinaus die Tags der Ressource und ihre Werte zurückgeben möchten, fügen Sie dem Schlüsselwort `project` die Eigenschaft **tags** hinzu.

```kusto
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"/>Auflisten aller Speicherkonten mit einem bestimmten Tagwert

Kombinieren Sie die Filterfunktionen des vorherigen Beispiels, und filtern Sie den Azure-Ressourcentyp nach der Eigenschaft **type**. Diese Abfrage schränkt auch unsere Suche nach spezifischen Azure-Ressourcentypen mit einem bestimmten Tagnamen und -wert ein.

```kusto
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> Dieses Beispiel verwendet `==` für den Abgleich statt der `=~`-Bedingung. Bei `==` wird die Groß-/Kleinschreibung bei der Übereinstimmung beachtet.

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"/>Aliase für die Ressource eines virtuellen Computers anzeigen

[Azure Policy-Aliase](../../policy/concepts/definition-structure.md#aliases) werden von Azure Policy zum Verwalten der Ressourcencompliance verwendet. Azure Resource Graph kann die _Aliase_ eines Ressourcentyps zurückgeben. Diese Werte sind hilfreich zum Vergleichen des aktuellen Werts von Aliasen, wenn eine benutzerdefinierte Richtliniendefinition erstellt wird. Das _Aliase_-Array wird in den Ergebnissen einer Abfrage nicht standardmäßig bereitgestellt. Verwenden Sie `project aliases`, es den Ergebnissen explizit hinzuzufügen.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"/>Unterschiedliche Werte für einen bestimmten Alias anzeigen

Das Anzeigen des Werts von Aliasen für eine einzelne Ressource ist hilfreich, zeigt aber nicht den wirklichen Nutzen der Verwendung von Azure Resource Graph zum Abfragen über Abonnements hinweg. In diesem Beispiel werden alle Werte eines bestimmten Alias untersucht und die unterschiedlichen Werte zurückgegeben.

```kusto
where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Abfragesprache](../concepts/query-language.md)
- Lernen Sie, [Ressourcen zu untersuchen](../concepts/explore-resources.md)
- Siehe Beispiele der [erweiterten Abfragen](advanced.md)