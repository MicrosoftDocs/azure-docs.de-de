---
title: Was-wäre-wenn für die Vorlagenbereitstellung
description: Legen Sie vor der Bereitstellung einer Azure Resource Manager-Vorlage fest, welche Änderungen an Ihren Ressourcen vorgenommen werden.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: tomfitz
ms.openlocfilehash: 8122fa5c00a61017b5f358a112c94a5299539cee
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591623"
---
# <a name="arm-template-deployment-what-if-operation"></a>ARM-Vorlagenbereitstellung: Was-wäre-wenn-Vorgang

Vor dem Bereitstellen einer ARM-Vorlage (Azure Resource Manager-Vorlage) können Sie eine Vorschau der Änderungen anzeigen, die vorgenommen werden. Azure Resource Manager stellt den Was-wäre-wenn-Vorgang bereit, damit Sie sehen können, wie sich Ressourcen ändern, wenn Sie die Vorlage bereitstellen. Der Was-wäre-wenn-Vorgang nimmt keine Änderungen an vorhandenen Ressourcen vor. Stattdessen sagt er die Änderungen vorher, wenn die angegebene Vorlage bereitgestellt wird.

Sie können den Was-wäre-wenn-Vorgang mit Azure PowerShell-, Azure CLI- oder REST-API-Vorgängen verwenden. Der Was-wäre-wenn-Vorgang wird für Bereitstellungen auf Ressourcengruppen-, Abonnements-, Verwaltungsgruppen- und Mandantenebene unterstützt.

## <a name="install-azure-powershell-module"></a>Installieren des Azure PowerShell-Moduls

Wenn Sie den Was-wäre-wenn-Vorgang in PowerShell verwenden möchten, benötigen Sie Version **4.2 oder höher des Az-Moduls**.

Führen Sie folgenden Befehl aus, um das Modul zu installieren:

```powershell
Install-Module -Name Az -Force
```

Weitere Informationen zum Installieren von Modulen finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="install-azure-cli-module"></a>Installieren des Azure CLI-Moduls

Um Was-wäre-wenn in Azure CLI zu verwenden, müssen Sie über Azure CLI 2.5.0 oder höher verfügen. Bei Bedarf [installieren Sie die neueste Version von Azure CLI](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Anzeigen der Ergebnisse

Wenn Sie Was-wäre-wenn in PowerShell oder Azure CLI verwenden, enthält die Ausgabe farbcodierte Ergebnisse, die Ihnen helfen, die verschiedenen Arten von Änderungen anzuzeigen.

![Resource Manager-Vorlagenbereitstellung: Was-wäre-wenn-Vorgang fullresourcepayload und Änderungstypen](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Die Textausgabe lautet:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> Der Was-wäre-wenn-Vorgang kann die [Reference-Funktion](template-functions-resource.md#reference) nicht auflösen. Jedes Mal, wenn Sie eine Eigenschaft auf einen Vorlagenausdruck festlegen, der die Reference-Funktion enthält, meldet der Was-wäre-wenn-Vorgang, dass sich die Eigenschaft ändern wird. Dieses Verhalten tritt auf, weil der Was-wäre-wenn-Vorgang den aktuellen Eigenschaftswert (z. B. `true` oder `false` bei einem booleschen Wert) mit dem nicht aufgelösten Vorlagenausdruck vergleicht. Natürlich stimmen diese Werte nicht überein. Wenn Sie die Vorlage bereitstellen, ändert sich die Eigenschaft nur, wenn der Vorlagenausdruck zu einem anderen Wert aufgelöst wird.

## <a name="what-if-commands"></a>Was-wäre-wenn-Befehle

### <a name="azure-powershell"></a>Azure PowerShell

Wenn Sie vor der Bereitstellung einer Vorlage eine Vorschau der Änderungen anzeigen möchten, führen Sie [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) oder [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment) aus. Fügen Sie dem Bereitstellungsbefehl den `-Whatif`-Parameter hinzu.

* `New-AzResourceGroupDeployment -Whatif` für Bereitstellungen von Ressourcengruppen
* `New-AzSubscriptionDeployment -Whatif` und `New-AzDeployment -Whatif` für Bereitstellungen auf Abonnementebene

Sie können auch den Parameterschalter `-Confirm` verwenden, um eine Vorschau der Änderungen anzuzeigen und dann eine Aufforderung zum Fortsetzen der Bereitstellung zu erhalten.

* `New-AzResourceGroupDeployment -Confirm` für Bereitstellungen von Ressourcengruppen
* `New-AzSubscriptionDeployment -Confirm` und `New-AzDeployment -Confirm` für Bereitstellungen auf Abonnementebene

Die vorangehenden Befehle geben eine Textzusammenfassung zurück, die Sie manuell überprüfen können. Führen Sie [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) oder [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult) aus, um ein Objekt abzurufen, das programmgesteuert auf Änderungen prüfen kann.

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` für Bereitstellungen von Ressourcengruppen
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` oder `$results = Get-AzDeploymentWhatIfResult` für Bereitstellungen auf Abonnementebene

### <a name="azure-cli"></a>Azure CLI

Wenn Sie vor der Bereitstellung einer Vorlage eine Vorschau der Änderungen anzeigen möchten, verwenden Sie:

* [az deployment group what-if](/cli/azure/deployment/group#az-deployment-group-what-if) für Bereitstellungen von Ressourcengruppen
* [az deployment sub what-if](/cli/azure/deployment/sub#az-deployment-sub-what-if) für Bereitstellungen auf Abonnementebene
* [az deployment mg what-if](/cli/azure/deployment/mg#az-deployment-mg-what-if) für Bereitstellungen von Verwaltungsgruppen
* [az deployment tenant what-if](/cli/azure/deployment/tenant#az-deployment-tenant-what-if) für Bereitstellungen von Mandanten

Sie können den Parameterschalter `--confirm-with-what-if` (oder seine Kurzform `-c`) verwenden, um eine Vorschau der Änderungen anzuzeigen und dann zum Fortsetzen der Bereitstellung aufgefordert zu werden. Fügen Sie diesen Schalter zu folgenden Befehlen hinzu:

* [az deployment group create](/cli/azure/deployment/group#az-deployment-group-create)
* [az deployment sub create](/cli/azure/deployment/sub#az-deployment-sub-create).
* [az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az-deployment-tenant-create)

Verwenden Sie beispielsweise `az deployment group create --confirm-with-what-if` oder `-c` für Bereitstellungen von Ressourcengruppen.

Die vorangehenden Befehle geben eine Textzusammenfassung zurück, die Sie manuell überprüfen können. Um ein JSON-Objekt zu erhalten, das Sie programmgesteuert auf Änderungen überprüfen können, verwenden Sie den Schalter `--no-pretty-print`. Verwenden Sie beispielsweise `az deployment group what-if --no-pretty-print` für Bereitstellungen von Ressourcengruppen.

Wenn die Ergebnisse ohne Farben zurückgegeben werden sollen, öffnen Sie die [Konfigurationsdatei in der Azure CLI](/cli/azure/azure-cli-configuration). Legen Sie **no_color** auf **yes** fest.

### <a name="azure-rest-api"></a>Azure-REST-API

Verwenden Sie für die REST-API Folgendes:

* [Bereitstellungen – Was-wäre-wenn](/rest/api/resources/deployments/whatif) für Bereitstellungen von Ressourcengruppen
* [Bereitstellungen – Was-wäre-wenn im Abonnementbereich](/rest/api/resources/deployments/whatifatsubscriptionscope) für Bereitstellungen von Abonnements
* [Bereitstellungen – Was-wäre-wenn im Verwaltungsgruppenbereich](/rest/api/resources/deployments/whatifatmanagementgroupscope) für Bereitstellungen von Verwaltungsgruppen
* [Bereitstellungen – Was-wäre-wenn im Mandantenbereich](/rest/api/resources/deployments/whatifattenantscope) für Bereitstellungen von Mandanten.

## <a name="change-types"></a>Änderungstypen

Der Was-wäre-wenn-Vorgang listet sechs verschiedene Typen von Änderungen auf:

- **Erstellen**: Die Ressource ist zurzeit nicht vorhanden, aber in der Vorlage definiert. Die Ressource wird erstellt.

- **Löschen:** Dieser Änderungstyp gilt nur, wenn der [Modus „Vollständig“](deployment-modes.md) für die Bereitstellung verwendet wird. Die Ressource ist vorhanden, aber nicht in der Vorlage definiert. Im Modus „Vollständig“ wird die Ressource gelöscht. In diesem Änderungstyp werden nur Ressourcen eingeschlossen, die [das Löschen im Modus „Vollständig“ unterstützen](complete-mode-deletion.md).

- **Ignorieren**: Die Ressource ist vorhanden, aber nicht in der Vorlage definiert. Die Ressource wird weder bereitgestellt noch geändert.

- **NoChange**: Die Ressource ist vorhanden und in der Vorlage definiert. Die Ressource wird erneut bereitgestellt, wobei sich die Eigenschaften der Ressource aber nicht ändern. Dieser Änderungstyp wird zurückgegeben, wenn [ResultFormat](#result-format) auf `FullResourcePayloads` festgelegt ist, wobei es sich um den Standardwert handelt.

- **Ändern**: Die Ressource ist vorhanden und in der Vorlage definiert. Die Ressource wird erneut bereitgestellt, und die Eigenschaften der Ressource ändern sich. Dieser Änderungstyp wird zurückgegeben, wenn [ResultFormat](#result-format) auf `FullResourcePayloads` festgelegt ist, wobei es sich um den Standardwert handelt.

- **Bereitstellen**: Die Ressource ist vorhanden und in der Vorlage definiert. Die Ressource wird erneut erstellt. Die Eigenschaften der Ressource können sich ändern oder auch nicht. Der Vorgang gibt diesen Änderungstyp zurück, wenn er nicht über genügend Informationen verfügt, um zu bestimmen, ob sich Eigenschaften ändern. Diese Bedingung wird nur angezeigt, wenn [ResultFormat](#result-format) auf `ResourceIdOnly` festgelegt ist.

## <a name="result-format"></a>Ergebnisformat

Sie können den Grad der Detailliertheit der Informationen steuern, die in Bezug auf die vorhergesagten Änderungen zurückgegeben werden. Sie haben zwei Möglichkeiten:

* **FullResourcePayloads**: Gibt eine Liste der Ressourcen zurück, die sich ändern, sowie Details zu den Eigenschaften, die sich ändern.
* **ResourceIdOnly**: Gibt eine Liste der Ressourcen zurück, die sich ändern.

Der Standardwert ist **FullResourcePayloads**.

Verwenden Sie für PowerShell-Bereitstellungsbefehle den Parameter `-WhatIfResultFormat`. Verwenden Sie in den Befehlen für programmgesteuerte Objekte den Parameter `ResultFormat`.

Verwenden Sie für die Azure CLI den Parameter `--result-format`.
 
Die folgenden Ergebnisse zeigen die zwei unterschiedlichen Ausgabeformate:

- Vollständige Ressourcennutzlasten

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Nur Ressourcen-ID

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Ausführen des Was-wäre-wenn-Vorgangs

### <a name="set-up-environment"></a>Einrichten der Umgebung

Um zu sehen, wie Was-wäre-wenn funktioniert, führen wir nun einige Tests aus. Stellen Sie zunächst eine [Vorlage zum Erstellen eines virtuellen Netzwerks](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) bereit. Sie verwenden dieses virtuelle Netzwerk, um zu testen, wie Änderungen bei Was-wäre-wenn gemeldet werden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Testen der Änderung

Nach Abschluss der Bereitstellung, sind Sie bereit, um den Was-wäre-wenn-Vorgang zu testen. Diesmal stellen Sie eine [Vorlage zum Ändern des virtuellen Netzwerks](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json) bereit. Es fehlt eins der ursprünglichen Tags, ein Subnetz wurde entfernt, und das Adresspräfix wurde geändert.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

Die Was-wäre-wenn-Ausgabe ähnelt der folgenden:

![Resource Manager-Vorlagenbereitstellung: Was-wäre-wenn-Ausgabe](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Die Textausgabe lautet:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Beachten Sie am Anfang der Ausgabe, dass Farben so definiert sind, dass der Typ der Änderungen angegeben wird.

Im unteren Bereich der Ausgabe wird angezeigt, dass das Tag „Owner“ gelöscht wurde. Das Adresspräfix wurde von 10.0.0.0/16 in 10.0.0.0/15 geändert. Das Subnetz mit dem Namen „subnet001“ wurde gelöscht. Beachten Sie, dass diese Änderungen nicht bereitgestellt wurden. Sie sehen eine Vorschau der Änderungen, die durchgeführt werden, wenn Sie die Vorlage bereitstellen.

Einige der als gelöscht aufgeführten Eigenschaften werden sich tatsächlich nicht ändern. Eigenschaften können fälschlicherweise als gelöscht gemeldet werden, wenn sie nicht in der Vorlage vorhanden sind, sondern während der Bereitstellung automatisch als Standardwerte festgelegt werden. Dieses Ergebnis wird in der Was-wäre-wenn-Antwort als „Rauschen“ (Noise) betrachtet. Für die endgültige bereitgestellte Ressource werden die Werte der Eigenschaften festgelegt. Mit der fortschreitenden Entwicklung des Was-wäre-wenn-Vorgangs werden diese Eigenschaften aus dem Ergebnis herausgefiltert.

## <a name="programmatically-evaluate-what-if-results"></a>Programmgesteuertes Auswerten von Was-wäre-wenn-Ergebnissen

Nun können Sie die Was-wäre-wenn-Ergebnisse programmgesteuert auswerten, indem Sie den Befehl auf eine Variable festlegen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Eine Zusammenfassung der einzelnen Änderungen wird angezeigt.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Löschvorgang bestätigen

Der Was-wäre-wenn-Vorgang unterstützt die Verwendung von [Bereitstellungsmodi](deployment-modes.md). Wenn er auf den Modus „Vollständig“ festgelegt ist, werden Ressourcen, die nicht in der Vorlage enthalten sind, gelöscht. Im folgenden Beispiel wird eine [Vorlage im Modus „Vollständig“ bereitgestellt, für die keine Ressourcen definiert sind](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json).

Wenn Sie vor der Bereitstellung einer Vorlage eine Vorschau der Änderungen anzeigen möchten, verwenden Sie den Parameterschalter „confirm“ mit dem Bereitstellungsbefehl. Wenn die Änderungen Ihren Erwartungen entsprechen, bestätigen Sie, dass Sie die Bereitstellung fertig stellen möchten.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Da in der Vorlage keine Ressourcen definiert sind und der Bereitstellungsmodus auf „Vollständig“ festgelegt ist, wird das virtuelle Netzwerk gelöscht.

![Resource Manager-Vorlagenbereitstellung: Was-wäre-wenn-Ausgabe Bereitstellungsmodus „Vollständig“](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Die Textausgabe lautet:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Die erwarteten Änderungen werden angezeigt, und Sie können bestätigen, dass die Bereitstellung ausgeführt werden soll.

## <a name="sdks"></a>SDKs

Sie können den Was-wäre-wenn-Vorgang über die Azure-SDKs verwenden.

* Verwenden Sie für Python [Was-wäre-wenn-](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-).

* Verwenden Sie für Java die [DeploymentWhatIf-Klasse](/java/api/com.microsoft.azure.management.resources.deploymentwhatif).

* Verwenden Sie für .NET die [DeploymentWhatIf-Klasse](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif).

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie falsche Ergebnisse von Was-wäre-wenn-Vorgängen bemerken, melden Sie die Probleme unter [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Ein Microsoft Learn-Modul, das die Verwendung von „Was-wäre-wenn“ behandelt, finden Sie unter [Anzeigen einer Vorschau von Änderungen und Überprüfen von Azure-Ressourcen mithilfe von „Was-wäre-wenn“ und dem ARM-Vorlagen-Testtoolkit](/learn/modules/arm-template-test/).
- Informationen zum Bereitstellen von Vorlagen mit Azure PowerShell finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md).
- Informationen zum Bereitstellen von Vorlagen mit Azure CLI finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure CLI](deploy-cli.md).
- Informationen zum Bereitstellen von Vorlagen mit REST finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Resource Manager-REST-API](deploy-rest.md).
