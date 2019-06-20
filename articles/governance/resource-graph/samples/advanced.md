---
title: Beispiele erweiterter Abfragen
description: Verwenden Sie Azure Resource Graph, um einige erweiterte Abfragen auszuführen, etwa zur VMSS-Kapazität, zum Auflisten sämtlicher verwendeter Tags und zum Abgleichen von virtuellen Computern mit regulären Ausdrücken.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 7684ae6b4ddb6320efc62ef6f9963bef1b9a66fa
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691985"
---
# <a name="advanced-resource-graph-queries"></a>Erweiterte Resource Graph-Abfragen

Der erste Schritt zum Verstehen von Abfragen mit Azure Resource Graph sind Grundkenntnisse der [Abfragesprache](../concepts/query-language.md). Wenn Sie nicht bereits mit [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) vertraut sind, sollten Sie sich über die Grundlagen informieren, um zu verstehen, wie Sie Anforderungen für die gesuchten Ressourcen zusammenstellen müssen.

Wir behandeln die folgenden erweiterten Abfragen:

> [!div class="checklist"]
> - [Abrufen der Kapazität und Größe von VM-Skalierungsgruppen](#vmss-capacity)
> - [Auflisten aller Tagnamen](#list-all-tags)
> - [Einem regulären Ausdruck entsprechende VMs](#vm-regex)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Sprachunterstützung

Azure CLI (über eine Erweiterung) und Azure PowerShell (über ein Modul) unterstützen Azure Resource Graph. Überprüfen Sie vor dem Ausführen der folgenden Abfragen, ob Ihre Umgebung bereit ist. Unter [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) und [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) werden die Schritte zum Installieren und Überprüfen der Shellumgebung Ihrer Wahl beschrieben.

## <a name="vmss-capacity"></a>Abrufen der Kapazität und Größe von VM-Skalierungsgruppen

Diese Abfrage sucht nach Ressourcen für VM-Skalierungsgruppen und ruft verschiedene Details (einschließlich der Größe des virtuellen Computers und der Kapazität der Skalierungsgruppe) ab. Mit dieser Abfrage wandelt die `toint()`-Funktion die Kapazität in eine Zahl um, damit sie sortiert werden kann. Schließlich werden die Spalten in benutzerdefinierte benannte Eigenschaften umbenannt.

```kusto
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Auflisten aller Tagnamen

Diese Abfrage beginnt mit dem Tag und erstellt ein JSON-Objekt, das alle eindeutigen Tagnamen und ihre entsprechenden Typen auflistet.

```kusto
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Einem regulären Ausdruck entsprechende VMs

Diese Abfrage sucht nach virtuellen Computern, die einem [regulären Ausdruck](/dotnet/standard/base-types/regular-expression-language-quick-reference) (als _RegEx_ bekannt) entsprechen.
Mit **matches regex \@** wird der reguläre Ausdruck für den Abgleich definiert: `^Contoso(.*)[0-9]+$`. Diese RegEx-Definition wird wie folgt erläutert:

- `^` – Die Übereinstimmung muss am Anfang der Zeichenfolge beginnen.
- `Contoso` – Zeichenfolge mit Beachtung von Groß-/Kleinschreibung
- `(.*)` – Eine Übereinstimmung mit einem Teilausdruck:
  - `.` – stimmt mit einem beliebigen einzelnen Zeichen (außer eines Zeilenumbruchs) überein.
  - `*` – stimmt mit dem vorhergehenden Element null Mal oder mehrfach überein.
- `[0-9]` – Übereinstimmung von Zeichengruppe für die Ziffern 0 bis 9.
- `+` – stimmt mit dem vorhergehenden Element ein Mal oder mehrfach überein.
- `$` – Übereinstimmung des vorhergehenden Elements muss am Ende der Zeichenfolge auftreten.

Nach dem Namensabgleich stellt die Abfrage den Namen dar und sortiert aufsteigend nach Name.

```kusto
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>Nächste Schritte

- Siehe Beispiele der [einfachen Abfragen](starter.md)
- Erfahren Sie mehr über die [Abfragesprache](../concepts/query-language.md)
- Lernen Sie, [Ressourcen zu untersuchen](../concepts/explore-resources.md)
