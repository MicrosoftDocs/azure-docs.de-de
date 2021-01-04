---
title: 'Tutorial: Hinzufügen von Vorlagenfunktionen'
description: Fügen Sie Ihrer Azure Resource Manager-Vorlage (ARM-Vorlage) Vorlagenfunktionen hinzu, um Werte zu erstellen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 67c88e98d966a21163aafefcad8363086d5b3bf4
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931043"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Tutorial: Hinzufügen von Vorlagenfunktionen zu Ihrer ARM-Vorlage

In diesem Tutorial wird beschrieben, wie Sie Ihrer ARM-Vorlage (Azure Resource Manager-Vorlage) [Vorlagenfunktionen](template-functions.md) hinzufügen. Sie verwenden Funktionen, um dynamisch Werte zu erstellen. Zusätzlich zu diesen vom System bereitgestellten Vorlagenfunktionen können Sie auch [benutzerdefinierte Funktionen](./template-user-defined-functions.md) (User-Defined Functions, UDFs) erstellen. Dieses Tutorial dauert ungefähr **7 Minuten**.

## <a name="prerequisites"></a>Voraussetzungen

Wir empfehlen, das [Tutorial zu Parametern](template-tutorial-add-parameters.md) durchzuführen. Dies ist jedoch keine Voraussetzung.

Sie benötigen Visual Studio Code mit der Resource Manager-Tools-Erweiterung und entweder Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI). Weitere Informationen finden Sie unter [Vorlagentools](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Überprüfen der Vorlage

Am Ende des vorherigen Tutorials enthielt Ihre Vorlage den folgenden JSON-Code:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

Der Standort des Speicherkontos ist auf **USA, Osten** hartcodiert. Möglicherweise müssen Sie das Speicherkonto jedoch in anderen Regionen bereitstellen. Auch hier erweist sich die mangelnde Flexibilität Ihrer Vorlage als Problem. Sie könnten einen Parameter für den Standort hinzufügen, es wäre aber von Vorteil, wenn sein Standardwert nicht nur ein hartcodierter Wert wäre.

## <a name="use-function"></a>Verwenden einer Funktion

Wenn Sie das vorherige Tutorial dieser Reihe durchgeführt haben, haben Sie bereits eine Funktion verwendet. Beim Hinzufügen von `"[parameters('storageName')]"` haben Sie die Funktion [parameters](template-functions-deployment.md#parameters) verwendet. Die Klammern geben an, dass es sich bei der Syntax innerhalb der Klammern um einen [Vorlagenausdruck](template-expressions.md) handelt. Resource Manager löst die Syntax auf, anstatt sie als Literalwert zu behandeln.

Funktionen rufen während der Bereitstellung dynamisch Werte ab und erhöhen dadurch die Flexibilität Ihrer Vorlage. In diesem Tutorial verwenden Sie eine Funktion, um den Standort der Ressourcengruppe abzurufen, die Sie für die Bereitstellung verwenden.

Das folgende Beispiel zeigt die Änderungen, die Sie vornehmen, um einen Parameter namens **location** hinzuzufügen.  Der Standardwert des Parameters ruft die Funktion [resourceGroup](template-functions-resource.md#resourcegroup) auf. Diese Funktion gibt ein Objekt mit Informationen zu der für die Bereitstellung verwendeten Ressourcengruppe zurück. Eine der Eigenschaften im Objekt ist eine Standorteigenschaft. Wenn Sie den Standardwert verwenden, wird für das Speicherkonto der gleiche Standort verwendet wie für die Ressourcengruppe. Die Ressourcen in einer Ressourcengruppe müssen nicht den gleichen Standort haben. Sie können bei Bedarf auch einen anderen Standort angeben.

Kopieren Sie die gesamte Datei, und ersetzen Sie Ihre Vorlage durch den Inhalt der Datei.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Bereitstellen der Vorlage

In den vorherigen Tutorials haben Sie ein Speicherkonto in der Region „USA, Osten“ erstellt, Ihre Ressourcengruppe wurde aber in „USA, Mitte“ erstellt. In diesem Tutorial wird Ihr Speicherkonto in der gleichen Region erstellt wie die Ressourcengruppe. Verwenden Sie den Standardwert für den Standort, damit Sie diesen Parameterwert nicht angeben müssen. Da Sie ein Speicherkonto an einem anderen Standort erstellen, müssen Sie einen neuen Namen für das Speicherkonto angeben. Verwenden Sie beispielsweise anstelle von **store1** das Präfix **store2**.

Falls Sie die Ressourcengruppe noch nicht erstellt haben, folgen Sie den Anweisungen unter [Erstellen einer Ressourcengruppe](template-tutorial-create-first-template.md#create-resource-group). Dieses Beispiel setzt voraus, dass Sie die Variable **templateFile** wie im [ersten Tutorial](template-tutorial-create-first-template.md#deploy-template) beschrieben auf den Pfad zur Vorlagendatei festgelegt haben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Für die Ausführung dieses Bereitstellungsbefehls müssen Sie über die [aktuelle Version](/cli/azure/install-azure-cli) der Azure CLI verfügen.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Wenn bei der Bereitstellung ein Fehler aufgetreten ist, verwenden Sie den Schalter **verbose**, um Informationen zu den erstellten Ressourcen abzurufen. Verwenden Sie den Schalter **debug**, um weitere Informationen zum Debuggen zu erhalten.

## <a name="verify-deployment"></a>Überprüfen der Bereitstellung

Sie können die Bereitstellung überprüfen, indem Sie sich die Ressourcengruppe im Azure-Portal ansehen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Menü **Ressourcengruppen** aus.
1. Wählen Sie die Ressourcengruppe aus, in der Sie die Vorlage bereitgestellt haben.
1. Sie sehen, dass eine Speicherkontoressource bereitgestellt wurde, die den gleichen Standort aufweist wie die Ressourcengruppe.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit dem nächsten Tutorial fortfahren, müssen Sie die Ressourcengruppe nicht löschen.

Falls Sie keine weiteren Tutorials durchführen möchten, können Sie die bereitgestellten Ressourcen bereinigen, indem Sie die Ressourcengruppe löschen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie den Standardwert für einen Parameter mithilfe einer Funktion definiert. In den nächsten Tutorials dieser Reihe werden Sie weitere Funktionen verwenden. Am Ende der Reihe fügen Sie jedem Abschnitt der Vorlage Funktionen hinzu.

> [!div class="nextstepaction"]
> [Hinzufügen von Variablen](template-tutorial-add-variables.md)
