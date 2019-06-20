---
title: Kennzeichnen von Azure-Ressourcen für die logische Organisation | Microsoft-Dokumentation
description: Zeigt, wie Sie Tags zum Organisieren von Azure-Ressourcen für die Abrechnung und Verwaltung anwenden können.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: tomfitz
ms.openlocfilehash: 0ee1cc9673d5e4fbd71706af74e82f6520d42b6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868784"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Verwenden von Tags zum Organisieren von Azure-Ressourcen

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Um Tags auf Ressourcen anwenden zu können, muss der Benutzer Schreibzugriff auf den jeweiligen Ressourcentyp haben. Zum Anwenden von Tags auf alle Ressourcentypen verwenden Sie die Rolle [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor). Zum Anwenden von Tags auf nur einen Ressourcentyp verwenden Sie die Rolle „Mitwirkender“ für die jeweilige Ressource. Zum Anwenden von Tags auf virtuelle Computer beispielsweise verwenden Sie [Mitwirkender von virtuellen Computern](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Richtlinien

Sie können [Azure Policy](../governance/policy/overview.md) verwenden, um Taggingregeln und -konventionen zu erzwingen. Durch die Erstellung einer Richtlinie vermeiden Sie das Szenario, dass Ressourcen in Ihrem Abonnement bereitgestellt werden, die nicht den erwarteten Tags für Ihr Unternehmen entsprechen. Anstelle der manuellen Anwendung von Tags oder der Suche nach Ressourcen, die nicht konform sind, können Sie eine Richtlinie erstellen, die die erforderlichen Tags während der Bereitstellung automatisch anwendet. Im folgenden Abschnitt werden Beispielrichtlinien für Tags dargestellt.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Verwenden Sie Folgendes, um die vorhandenen Tags für eine *Ressourcengruppe* anzuzeigen:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Das Skript gibt das folgende Format zurück:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Verwenden Sie Folgendes, um die vorhandenen Tags für eine *Ressource mit einer angegebenen Ressourcen-ID* anzuzeigen:

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Oder verwenden Sie Folgendes, um die vorhandenen Tags für eine *Ressource mit einem angegebenen Namen und einer Ressourcengruppe* anzuzeigen:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Verwenden Sie Folgendes, um *Ressourcengruppen mit einem bestimmten Tag* abzurufen:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Verwenden Sie Folgendes, um *Ressourcen mit einem bestimmten Tag* abzurufen:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

Verwenden Sie Folgendes, um *Ressourcen mit einem bestimmten Tagnamen* abzurufen:

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

Wenn Sie Tags auf eine Ressource oder Ressourcengruppe anwenden, werden die bereits vorhandenen Tags für diese Ressource oder Ressourcengruppe überschrieben. Daher müssen Sie Ihre Vorgehensweise darauf abstimmen, ob für die Ressource oder Ressourcengruppe bereits Tags vorhanden sind.

Verwenden Sie Folgendes, um einer *Ressourcengruppe ohne vorhandene Tags* Tags hinzuzufügen:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Rufen Sie die vorhandenen Tags ab, fügen Sie das neue Tag hinzu, und wenden Sie die Tags wieder an, um einer *Ressourcengruppe mit vorhandenen Tags* Tags hinzuzufügen:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Verwenden Sie Folgendes, um einer *Ressource ohne vorhandene Tags* Tags hinzuzufügen:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Verwenden Sie Folgendes, um einer *Ressource mit vorhandenen Tags* Tags hinzuzufügen:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Verwenden Sie das folgende Skript, um alle Tags einer Ressourcengruppe auf die darin enthaltenen Ressourcen anzuwenden und *vorhandene Tags für die Ressourcen nicht beizubehalten*:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Verwenden Sie das folgende Skript, um alle Tags einer Ressourcengruppe auf die darin enthaltenen Ressourcen anzuwenden und *vorhandene Tags für Ressourcen beizubehalten, die keine Duplikate sind*:

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Durch Übergeben einer leeren Hashtabelle können Sie alle Tags löschen:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Verwenden Sie Folgendes, um die vorhandenen Tags für eine *Ressourcengruppe* anzuzeigen:

```azurecli
az group show -n examplegroup --query tags
```

Das Skript gibt das folgende Format zurück:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Oder verwenden Sie Folgendes, um die vorhandenen Tags für eine *Ressource mit einem angegebenen Namen, Typen und einer Ressourcengruppe* anzuzeigen:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Beim Durchlaufen einer Ressourcensammlung können Sie die Ressourcen nach Ressourcen-ID anzeigen. Ein vollständiges Beispiel finden Sie weiter unten in diesem Artikel. Verwenden Sie Folgendes, um die vorhandenen Tags für eine *Ressource mit einer angegebenen Ressourcen-ID* anzuzeigen:

```azurecli
az resource show --id <resource-id> --query tags
```

Verwenden Sie `az group list`, um Ressourcengruppen mit einem bestimmten Tag abzurufen:

```azurecli
az group list --tag Dept=IT
```

Zum Abrufen aller Ressourcen mit einem bestimmten Tag und Wert verwenden Sie `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Wenn Sie Tags auf eine Ressource oder Ressourcengruppe anwenden, werden die bereits vorhandenen Tags für diese Ressource oder Ressourcengruppe überschrieben. Daher müssen Sie Ihre Vorgehensweise darauf abstimmen, ob für die Ressource oder Ressourcengruppe bereits Tags vorhanden sind.

Verwenden Sie Folgendes, um einer *Ressourcengruppe ohne vorhandene Tags* Tags hinzuzufügen:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Verwenden Sie Folgendes, um einer *Ressource ohne vorhandene Tags* Tags hinzuzufügen:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Zum Hinzufügen von Tags zu einer Ressource, die bereits Tags aufweist, rufen Sie die vorhandenen Tags ab, formatieren den Wert neu und wenden die vorhandenen und neuen Tags erneut an:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Verwenden Sie das folgende Skript, um alle Tags einer Ressourcengruppe auf die darin enthaltenen Ressourcen anzuwenden und *vorhandene Tags für die Ressourcen nicht beizubehalten*:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Verwenden Sie das folgende Skript, um alle Tags einer Ressourcengruppe auf die darin enthaltenen Ressourcen anzuwenden und *vorhandene Tags für Ressourcen beizubehalten*:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Vorlagen

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST-API

Das Azure-Portal und PowerShell verwenden im Hintergrund die [Resource Manager-REST-API](https://docs.microsoft.com/rest/api/resources/). Wenn Sie das Tagging in eine andere Umgebung integrieren müssen, können Sie Tags mit **GET** für die Ressourcen-ID abrufen und die Tags mit einem **PATCH**-Aufruf aktualisieren.

## <a name="tags-and-billing"></a>Tags und Abrechnung

Abrechnungsdaten können mithilfe von Tags gruppiert werden. Wenn Sie beispielsweise mehrere virtuelle Computer für verschiedene Organisationen betreiben, können Sie die Nutzung mithilfe von Tags nach Kostenstelle organisieren. Mit Tags können Sie auch Kosten nach Runtimeumgebung kategorisieren, beispielsweise zur Abrechnung der Nutzung virtueller Computer in der Produktionsumgebung.

Informationen zu Tags können Sie über die [Azure-Ressourcennutzungs- und RateCard-APIs](../billing/billing-usage-rate-card-overview.md) oder aus der Nutzungsdatei im CSV-Format abrufen. Sie laden die Nutzungsdatei aus dem [Azure-Kontocenter](https://account.azure.com/Subscriptions) oder dem Azure-Portal herunter. Weitere Informationen finden Sie unter [Herunterladen oder Anzeigen Ihrer Azure-Rechnungen und täglichen Nutzungsdaten](../billing/billing-download-azure-invoice-daily-usage-date.md). Wählen Sie beim Herunterladen der Nutzungsdatei aus dem Azure-Kontocenter die Option **Version 2**. Für Dienste, die die Verwendung von Tags für die Abrechnung unterstützen, sind die Tags in der Spalte **Tags** enthalten.

Hinweise zu REST-API-Vorgängen finden Sie unter [Azure Billing REST API Reference (Preview)](/rest/api/billing/)(in englischer Sprache).

## <a name="next-steps"></a>Nächste Schritte

* Nicht alle Ressourcentypen unterstützen Tags. Um festzustellen, ob Sie ein Tag auf einen Ressourcentyp anwenden können, lesen Sie [Tagunterstützung für Azure-Ressourcen](tag-support.md).
* Eine Einführung zum Verwenden des Portals finden Sie unter [Verwenden des Azure-Portals zum Verwalten Ihrer Azure-Ressourcen](manage-resource-groups-portal.md).  
