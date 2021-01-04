---
title: Markieren von Ressourcen, Ressourcengruppen und Abonnements für die logische Organisation
description: Zeigt, wie Sie Tags zum Organisieren von Azure-Ressourcen für die Abrechnung und Verwaltung anwenden können.
ms.topic: conceptual
ms.date: 12/03/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e47d3acf15ce5e4f5cb70444419b76beb21ae98b
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558146"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Verwenden von Tags zum Organisieren von Azure-Ressourcen und Verwaltungshierarchie

Durch Anwenden von Tags können Sie Ihre Ressourcen, Ressourcengruppen und Abonnements in Azure logisch in einer Taxonomie strukturieren. Jedes Tag besteht aus einem Paar mit einem Namen und einem Wert. So können Sie beispielsweise den Namen „Umgebung“ und den Wert „Produktion“ auf alle Ressourcen in der Produktion anwenden.

Empfehlungen zum Implementieren einer Tagstrategie finden Sie unter [Leitfaden zur Entscheidungsfindung für Ressourcenbenennung und -markierung](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> Bei Tagnamen wird für Vorgänge die Groß-/Kleinschreibung nicht beachtet. Ein Tag mit einem Tagnamen (unabhängig von der Groß-/Kleinschreibung) wird aktualisiert oder abgerufen. Der Ressourcenanbieter übernimmt jedoch möglicherweise die Groß-/Kleinschreibung, die Sie für den Tagnamen verwenden. Diese Groß-/Kleinschreibung erscheint in Kostenberichten.
> 
> Bei den Tagwerten wird Groß- und Kleinschreibung unterschieden.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Erforderlicher Zugriff

Es gibt zwei Möglichkeiten, den erforderlichen Zugriff auf Tagressourcen zu erhalten.

- Sie können Schreibzugriff auf den Ressourcentyp **Microsoft.Resources/tags** haben. Mit diesem Zugriff können Sie alle Ressourcen markieren, auch wenn Sie keinen Zugriff auf die Ressource selbst haben. Die Rolle [Tagmitwirkender](../../role-based-access-control/built-in-roles.md#tag-contributor) gewährt diesen Zugriff. Derzeit kann die Rolle „Tagmitwirkender“ keine Tags über das Portal auf Ressourcen oder Ressourcengruppen anwenden. Sie kann über das Portal Tags auf Abonnements anwenden. Sie unterstützt alle Tagvorgänge über PowerShell und die REST-API.  

- Sie können Schreibzugriff auf die Ressource selbst haben. Die Rolle [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) gewährt den erforderlichen Zugriff zum Anwenden von Tags auf Entitäten. Zum Anwenden von Tags auf nur einen Ressourcentyp verwenden Sie die Rolle „Mitwirkender“ für die jeweilige Ressource. Zum Anwenden von Tags auf virtuelle Computer beispielsweise verwenden Sie [Mitwirkender von virtuellen Computern](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Anwenden von Tags

Azure PowerShell umfasst zwei Befehle zum Anwenden von Tags: [New-AzTag](/powershell/module/az.resources/new-aztag) und [Update-AzTag](/powershell/module/az.resources/update-aztag). Sie benötigen das Modul Az.Resources 1.12.0 oder höher. Sie können die Version mit `Get-Module Az.Resources` überprüfen. Sie können dieses Modul oder [Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 oder höher installieren.

Mit dem Befehl **New-AzTag** werden alle Tags für die Ressource, die Ressourcengruppe oder das Abonnement ersetzt. Übergeben Sie beim Aufrufen des Befehls die Ressourcen-ID der Entität, die markiert werden soll.

Im folgenden Beispiel wird eine Gruppe von Tags auf ein Speicherkonto angewandt:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Beachten Sie, dass die Ressource nach Abschluss des Befehls zwei Tags enthält.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Wenn Sie den Befehl erneut ausführen, jedoch dieses Mal mit anderen Tags, werden Sie feststellen, dass die früheren Tags entfernt wurden.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Um einer Ressource, die bereits Tags enthält, Tags hinzuzufügen, verwenden Sie **Update-AzTag**. Legen Sie den Parameter **-Operation** auf **Merge** fest.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Beachten Sie, dass die beiden neuen Tags den beiden vorhandenen Tags hinzugefügt wurden.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Jeder Tagname kann nur einen Wert enthalten. Wenn Sie einen neuen Wert für ein Tag angeben, wird der alte Wert auch dann ersetzt, wenn Sie den Zusammenführungsvorgang verwenden. Im folgenden Beispiel wird das Tag „Status“ von „Normal“ in „Green“ geändert.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Wenn Sie den Parameter **-Operation** auf **Replace** festlegen, werden die vorhandenen Tags durch die neue Gruppe von Tags ersetzt.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Nur die neuen Tags verbleiben in der Ressource.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Diese Befehle können auch für Ressourcengruppen und Abonnements verwendet werden. Sie übergeben den Bezeichner für die Ressourcengruppe oder das Abonnement, die Sie markieren möchten.

Verwenden Sie zum Hinzufügen einer neuen Gruppe von Tags zu einer Ressourcengruppe Folgendes:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Verwenden Sie zum Aktualisieren der Tags für eine Ressourcengruppe Folgendes:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Verwenden Sie zum Hinzufügen einer neuen Gruppe von Tags zu einem Abonnement Folgendes:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Verwenden Sie zum Aktualisieren der Tags für ein Abonnement Folgendes:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Eine Ressourcengruppe kann ggf. mehrere Ressourcen mit dem gleichen Namen enthalten. In diesem Fall können Sie die einzelnen Ressourcen jeweils mit den folgenden Befehlen festlegen:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Auflisten von Tags

Um die Tags für eine Ressource, eine Ressourcengruppe oder ein Abonnement abzurufen, verwenden Sie den Befehl [Get-AzTag](/powershell/module/az.resources/get-aztag), und übergeben Sie die Ressourcen-ID für die Entität.

Verwenden Sie zum Anzeigen der Tags für eine Ressource Folgendes:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Verwenden Sie zum Anzeigen der Tags für eine Ressourcengruppe Folgendes:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Verwenden Sie zum Anzeigen der Tags für ein Abonnement Folgendes:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Auflisten nach Tag

Verwenden Sie zum Abrufen von Ressourcen mit einem bestimmten Tagnamen und -wert Folgendes:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Verwenden Sie zum Abrufen von Ressourcen mit einem bestimmten Tagnamen und einem beliebigen Tagwert Folgendes:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Verwenden Sie zum Abrufen von Ressourcengruppen mit einem bestimmten Tagnamen und -wert Folgendes:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Entfernen von Tags

Um bestimmte Tags zu entfernen, verwenden Sie den Befehl **Update-AzTag**, und legen Sie **-Operation** auf **Delete** fest. Übergeben Sie die Tags, die gelöscht werden sollen.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

Die angegebenen Tags werden entfernt.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Um alle Tags zu entfernen, verwenden Sie den Befehl [Remove-AzTag](/powershell/module/az.resources/remove-aztag).

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>Anwenden von Tags

Azure CLI bietet zwei Befehle zum Anwenden von Tags: [az tag create](/cli/azure/tag#az_tag_create) und [az tag update](/cli/azure/tag#az_tag_update). Sie müssen Azure CLI 2.10.0 oder höher verwenden. Sie können die Version mit `az version` überprüfen. Informationen zur Aktualisierung oder Installation finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Mit dem Befehl **az tag create** werden alle Tags für die Ressource, die Ressourcengruppe oder das Abonnement ersetzt. Übergeben Sie beim Aufrufen des Befehls die Ressourcen-ID der Entität, die markiert werden soll.

Im folgenden Beispiel wird eine Gruppe von Tags auf ein Speicherkonto angewandt:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag create --resource-id $resource --tags Dept=Finance Status=Normal
```

Beachten Sie, dass die Ressource nach Abschluss des Befehls zwei Tags enthält.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Status": "Normal"
  }
},
```

Wenn Sie den Befehl erneut ausführen, jedoch dieses Mal mit anderen Tags, werden Sie feststellen, dass die früheren Tags entfernt wurden.

```azurecli-interactive
az tag create --resource-id $resource --tags Team=Compliance Environment=Production
```

```output
"properties": {
  "tags": {
    "Environment": "Production",
    "Team": "Compliance"
  }
},
```

Um einer Ressource, die bereits Tags enthält, Tags hinzuzufügen, verwenden Sie `az tag update`. Setzen Sie den `--operation`-Parameter auf `Merge`.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Dept=Finance Status=Normal
```

Beachten Sie, dass die beiden neuen Tags den beiden vorhandenen Tags hinzugefügt wurden.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Normal",
    "Team": "Compliance"
  }
},
```

Jeder Tagname kann nur einen Wert enthalten. Wenn Sie einen neuen Wert für ein Tag angeben, wird der alte Wert auch dann ersetzt, wenn Sie den Zusammenführungsvorgang verwenden. Im folgenden Beispiel wird das Tag „Status“ von „Normal“ in „Green“ geändert.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Status=Green
```

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Green",
    "Team": "Compliance"
  }
},
```

Wenn Sie den Parameter `--operation` auf `Replace` festlegen, werden die vorhandenen Tags durch die neue Gruppe von Tags ersetzt.

```azurecli-interactive
az tag update --resource-id $resource --operation Replace --tags Project=ECommerce CostCenter=00123 Team=Web
```

Nur die neuen Tags verbleiben in der Ressource.

```output
"properties": {
  "tags": {
    "CostCenter": "00123",
    "Project": "ECommerce",
    "Team": "Web"
  }
},
```

Diese Befehle können auch für Ressourcengruppen und Abonnements verwendet werden. Sie übergeben den Bezeichner für die Ressourcengruppe oder das Abonnement, die Sie markieren möchten.

Verwenden Sie zum Hinzufügen einer neuen Gruppe von Tags zu einer Ressourcengruppe Folgendes:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag create --resource-id $group --tags Dept=Finance Status=Normal
```

Verwenden Sie zum Aktualisieren der Tags für eine Ressourcengruppe Folgendes:

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags CostCenter=00123 Environment=Production
```

Verwenden Sie zum Hinzufügen einer neuen Gruppe von Tags zu einem Abonnement Folgendes:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag create --resource-id /subscriptions/$sub --tags CostCenter=00123 Environment=Dev
```

Verwenden Sie zum Aktualisieren der Tags für ein Abonnement Folgendes:

```azurecli-interactive
az tag update --resource-id /subscriptions/$sub --operation Merge --tags Team="Web Apps"
```

### <a name="list-tags"></a>Auflisten von Tags

Um die Tags für eine Ressource, eine Ressourcengruppe oder ein Abonnement abzurufen, verwenden Sie den Befehl [az tag list](/cli/azure/tag#az_tag_list), und übergeben Sie die Ressourcen-ID für die Entität.

Verwenden Sie zum Anzeigen der Tags für eine Ressource Folgendes:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag list --resource-id $resource
```

Verwenden Sie zum Anzeigen der Tags für eine Ressourcengruppe Folgendes:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag list --resource-id $group
```

Verwenden Sie zum Anzeigen der Tags für ein Abonnement Folgendes:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag list --resource-id /subscriptions/$sub
```

### <a name="list-by-tag"></a>Auflisten nach Tag

Verwenden Sie zum Abrufen von Ressourcen mit einem bestimmten Tagnamen und -wert Folgendes:

```azurecli-interactive
az resource list --tag CostCenter=00123 --query [].name
```

Verwenden Sie zum Abrufen von Ressourcen mit einem bestimmten Tagnamen und einem beliebigen Tagwert Folgendes:

```azurecli-interactive
az resource list --tag Team --query [].name
```

Verwenden Sie zum Abrufen von Ressourcengruppen mit einem bestimmten Tagnamen und -wert Folgendes:

```azurecli-interactive
az group list --tag Dept=Finance
```

### <a name="remove-tags"></a>Entfernen von Tags

Um bestimmte Tags zu entfernen, verwenden Sie den Befehl `az tag update`, und legen Sie `--operation` auf `Delete` fest. Übergeben Sie die Tags, die gelöscht werden sollen.

```azurecli-interactive
az tag update --resource-id $resource --operation Delete --tags Project=ECommerce Team=Web
```

Die angegebenen Tags werden entfernt.

```output
"properties": {
  "tags": {
    "CostCenter": "00123"
  }
},
```

Um alle Tags zu entfernen, verwenden Sie den Befehl [az tag delete](/cli/azure/tag#az_tag_delete).

```azurecli-interactive
az tag delete --resource-id $resource
```

### <a name="handling-spaces"></a>Behandeln von Leerzeichen

Wenn die Tagnamen oder -werte Leerzeichen enthalten, umschließen Sie sie mit doppelten Anführungszeichen.

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags "Cost Center"=Finance-1222 Location="West US"
```

## <a name="templates"></a>Vorlagen

Sie können Ressourcen, Ressourcengruppen und Abonnements während der Bereitstellung mit einer Resource Manager-Vorlage markieren.

### <a name="apply-values"></a>Anwenden von Werten

Im folgenden Beispiel wird ein Speicherkonto mit drei Tags bereitgestellt. Zwei der Tags (`Dept` und `Environment`) werden auf literale Werte festgelegt. Ein Tag (`LastDeployed`) wird auf einen Parameter festgelegt, der standardmäßig das aktuelle Datum angibt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>Anwenden eines Objekts

Sie können einen Objektparameter definieren, der mehrere Tags speichert, und dieses Objekt auf das Tagelement anwenden. Dieser Ansatz bietet mehr Flexibilität als das vorherige Beispiel, da das Objekt unterschiedliche Eigenschaften enthalten kann. Jede Eigenschaft in dem Objekt wird zu einem separaten Tag für die Ressource. Das folgende Beispiel enthält einen Parameter namens `tagValues`, der auf das Tagelement angewendet wird.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>Anwenden einer JSON-Zeichenfolge

Wenn Sie mehrere Werte in einem einzelnen Tag speichern möchten, wenden Sie eine JSON-Zeichenfolge an, die die gewünschten Werte darstellt. Die gesamte JSON-Zeichenfolge wird als einzelnes Tag gespeichert und darf maximal 256 Zeichen lang sein. Im folgenden Beispiel gibt es ein einzelnes Tag namens `CostCenter`, das mehrere Werte aus einer JSON-Zeichenfolge enthält:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Anwenden von Tags aus der Ressourcengruppe

Wenn Sie Tags aus einer Ressourcengruppe auf eine Ressource anwenden möchten, verwenden Sie die Funktion [resourceGroup()](../templates/template-functions-resource.md#resourcegroup). Wenn Sie den Tagwert abrufen, verwenden Sie die `tags[tag-name]`-Syntax anstelle der `tags.tag-name`-Syntax, da einige Zeichen in der Punktnotation nicht ordnungsgemäß analysiert werden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Anwenden von Tags auf Ressourcengruppen oder Abonnements

Durch Bereitstellen des Ressourcentyps **Microsoft.Resources/tags** können Sie einer Ressourcengruppe oder einem Abonnement Tags hinzufügen. Die Tags werden auf die Zielressourcengruppe oder das Zielabonnement für die Bereitstellung angewandt. Bei jeder Bereitstellung der Vorlage werden alle zuvor angewandten Tags ersetzt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Zum Anwenden der Tags auf eine Ressourcengruppe können Sie entweder PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden. Führen Sie die Bereitstellung für die Ressourcengruppe aus, die markiert werden soll.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Zum Anwenden der Tags auf ein Abonnement können Sie entweder PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden. Führen Sie die Bereitstellung für das Abonnement aus, das markiert werden soll.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Weitere Informationen zu Abonnementbereitstellungen finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](../templates/deploy-to-subscription.md).

Mit der folgenden Vorlage werden die Tags eines Objekts entweder in einer Ressourcengruppe oder einem Abonnement hinzugefügt.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST-API

Verwenden Sie zum Arbeiten mit Tags über die Azure-REST-API Folgendes:

* [Tags: Erstellen oder Aktualisieren im Bereich](/rest/api/resources/tags/createorupdateatscope) (PUT-Vorgang)
* [Tags: Aktualisieren im Bereich](/rest/api/resources/tags/updateatscope) (PATCH-Vorgang)
* [Tags: Abrufen im Bereich](/rest/api/resources/tags/getatscope) (GET-Vorgang)
* [Tags: Löschen im Bereich](/rest/api/resources/tags/deleteatscope) (DELETE-Vorgang)

## <a name="inherit-tags"></a>Erben von Tags

Auf eine Ressourcengruppe oder ein Abonnement angewandte Tags werden von den Ressourcen nicht geerbt. Informationen zum Anwenden von Tags aus einem Abonnement oder einer Ressourcengruppe auf die Ressourcen finden Sie unter [Azure-Richtlinien: Tags](tag-policies.md).

## <a name="tags-and-billing"></a>Tags und Abrechnung

Abrechnungsdaten können mithilfe von Tags gruppiert werden. Wenn Sie beispielsweise mehrere virtuelle Computer für verschiedene Organisationen betreiben, können Sie die Nutzung mithilfe von Tags nach Kostenstelle organisieren. Mit Tags können Sie auch Kosten nach Runtimeumgebung kategorisieren, beispielsweise zur Abrechnung der Nutzung virtueller Computer in der Produktionsumgebung.

Informationen zu Tags können Sie über die [Azure-Ressourcennutzungs- und RateCard-APIs](../../cost-management-billing/manage/usage-rate-card-overview.md) oder aus der Nutzungsdatei im CSV-Format abrufen. Sie laden die Nutzungsdatei nicht aus dem Azure-Portal herunter. Weitere Informationen finden Sie unter [Herunterladen oder Anzeigen Ihrer Azure-Rechnungen und täglichen Nutzungsdaten](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md). Wählen Sie beim Herunterladen der Nutzungsdatei aus dem Azure-Kontocenter die Option **Version 2**. Für Dienste, die die Verwendung von Tags für die Abrechnung unterstützen, sind die Tags in der Spalte **Tags** enthalten.

Hinweise zu REST-API-Vorgängen finden Sie unter [Azure Billing REST API Reference (Preview)](/rest/api/billing/)(in englischer Sprache).

## <a name="limitations"></a>Einschränkungen

Für Tags gelten folgende Einschränkungen:

* Nicht alle Ressourcentypen unterstützen Tags. Um festzustellen, ob Sie ein Tag auf einen Ressourcentyp anwenden können, lesen Sie [Tagunterstützung für Azure-Ressourcen](tag-support.md).
* Jede Ressource, jede Ressourcengruppe und jedes Abonnement kann maximal 50 Tagname-Wert-Paare enthalten. Wenn Sie mehr Tags als die maximal zulässige Anzahl anwenden müssen, verwenden Sie für den Tagwert eine JSON-Zeichenfolge. Die JSON-Zeichenfolge kann zahlreiche Werte enthalten, die auf einen einzelnen Tagnamen angewendet werden. Eine Ressourcengruppe oder ein Abonnement kann zahlreiche Ressourcen mit jeweils 50 Tagname-Wert-Paaren enthalten.
* Der Tagname ist auf 512 Zeichen beschränkt und der Tagwert auf 256 Zeichen. Bei Speicherkonten ist der Tagname auf 128 Zeichen beschränkt und der Tagwert auf 256 Zeichen.
* Tags können nicht auf klassische Ressourcen wie Cloud Services angewendet werden.
* Tag-Namen dürfen die folgenden Zeichen nicht enthalten: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Derzeit lassen Azure DNS-Zonen und Traffic Manager-Dienste auch keine Leerzeichen im Tag zu.
   >
   > Die Verwendung von `#` im Tagnamen wird in Azure Front Door nicht unterstützt.
   >
   > Azure Automation und Azure CDN unterstützen nur 15 Tags für Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

* Nicht alle Ressourcentypen unterstützen Tags. Um festzustellen, ob Sie ein Tag auf einen Ressourcentyp anwenden können, lesen Sie [Tagunterstützung für Azure-Ressourcen](tag-support.md).
* Empfehlungen zum Implementieren einer Tagstrategie finden Sie unter [Leitfaden zur Entscheidungsfindung für Ressourcenbenennung und -markierung](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
