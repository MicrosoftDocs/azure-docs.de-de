---
title: 'Tutorial: Hinzufügen von Parametern zu einer Vorlage'
description: Fügen Sie Ihrer Azure Resource Manager-Vorlage (ARM-Vorlage) Parameter hinzu, damit sie wiederverwendet werden kann.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7d0743d316b9d879017f3b0fbe08ee4dc2b3e1c2
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931060"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Tutorial: Hinzufügen von Parametern zu Ihrer ARM-Vorlage

Im [vorherigen Tutorial](template-tutorial-add-resource.md) haben Sie erfahren, wie Sie der Vorlage ein Speicherkonto hinzufügen und sie bereitstellen. In diesem Tutorial wird beschrieben, wie Sie die ARM-Vorlage (Azure Resource Manager-Vorlage) verbessern, indem Sie Parameter hinzufügen. Das Tutorial dauert ungefähr **14 Minuten**.

## <a name="prerequisites"></a>Voraussetzungen

Wir empfehlen, das [Tutorial zu Ressourcen](template-tutorial-add-resource.md) durchzuführen. Dies ist jedoch keine Voraussetzung.

Sie benötigen Visual Studio Code mit der Resource Manager-Tools-Erweiterung und entweder Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI). Weitere Informationen finden Sie unter [Vorlagentools](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Überprüfen der Vorlage

Am Ende des vorherigen Tutorials enthielt Ihre Vorlage den folgenden JSON-Code:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Möglicherweise haben Sie bemerkt, dass es bei dieser Vorlage ein Problem gibt. Der Name des Speicherkontos ist hartcodiert. Sie können mit dieser Vorlage immer nur das gleiche Speicherkonto bereitstellen. Um ein Speicherkonto mit einem anderen Namen bereitzustellen, müssten Sie eine neue Vorlage erstellen, was natürlich nicht praktisch ist, wenn Sie Ihre Bereitstellungen automatisieren möchten.

## <a name="make-template-reusable"></a>Ermöglichen der Wiederverwendung einer Vorlage

Damit Ihre Vorlage wiederverwendet werden kann, fügen Sie einen Parameter hinzu, mit dem Sie einen Speicherkontonamen übergeben können. Der hervorgehobene JSON-Code im folgenden Beispiel zeigt, was sich in der Vorlage geändert hat. Der Parameter **storageName** wird als Zeichenfolge identifiziert. Die maximale Länge ist auf 24 Zeichen festgelegt, damit keine zu langen Namen verwendet werden.

Kopieren Sie die gesamte Datei, und ersetzen Sie Ihre Vorlage durch den Inhalt der Datei.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Bereitstellen der Vorlage

Nun stellen Sie die Vorlage bereit. Im folgenden Beispiel wird die Vorlage mit der Azure CLI oder PowerShell bereitgestellt. Dabei geben Sie den Namen des Speicherkontos als einen der Werte im Bereitstellungsbefehl an. Geben Sie für das Speicherkonto den Namen an, den Sie im vorherigen Tutorial verwendet haben.

Falls Sie die Ressourcengruppe noch nicht erstellt haben, folgen Sie den Anweisungen unter [Erstellen einer Ressourcengruppe](template-tutorial-create-first-template.md#create-resource-group). Dieses Beispiel setzt voraus, dass Sie die Variable **templateFile** wie im [ersten Tutorial](template-tutorial-create-first-template.md#deploy-template) beschrieben auf den Pfad zur Vorlagendatei festgelegt haben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Für die Ausführung dieses Bereitstellungsbefehls müssen Sie über die [aktuelle Version](/cli/azure/install-azure-cli) der Azure CLI verfügen.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Grundlegendes zu Ressourcenaktualisierungen

Im vorherigen Abschnitt haben Sie ein Speicherkonto mit dem Namen bereitgestellt, den Sie zuvor verwendet haben. Vielleicht fragen Sie sich, wie sich die erneute Bereitstellung auf die Ressource auswirkt.

Wenn die Ressource bereits vorhanden ist und in den Eigenschaften keine Änderungen erkannt werden, wird keine Aktion ausgeführt. Ist die Ressource bereits vorhanden und eine Eigenschaft wurde geändert, wird die Ressource aktualisiert. Wenn die Ressource nicht vorhanden ist, wird sie erstellt.

Da Aktualisierungen auf diese Weise verarbeitet werden, kann Ihre Vorlage alle Ressourcen enthalten, die Sie für eine Azure-Lösung benötigen. Mit dem Wissen, dass Ressourcen nur bei Bedarf geändert oder erstellt werden, können Sie die Vorlage sicher erneut bereitstellen. Wenn Sie Ihrem Speicherkonto beispielsweise Dateien hinzugefügt haben, können Sie das Speicherkonto erneut bereitstellen, ohne dass diese Dateien verloren gehen.

## <a name="customize-by-environment"></a>Anpassen nach Umgebung

Mit Parametern können Sie die Bereitstellung anpassen, indem Sie Werte angeben, die für eine bestimmte Umgebung maßgeschneidert sind. Je nachdem, ob Sie die Vorlage in einer Entwicklungs-, Test- oder Produktionsumgebung bereitstellen, können Sie beispielsweise unterschiedliche Werte übergeben.

Mit der vorherigen Vorlage wurde immer ein Standard_LRS-Speicherkonto bereitgestellt. Möglicherweise möchten Sie in der Lage sein, je nach Umgebung flexibel verschiedene SKUs bereitzustellen. Das folgende Beispiel zeigt die Änderungen, die Sie zum Hinzufügen eines Parameters für die SKU vornehmen. Kopieren Sie die gesamte Datei, und überschreiben Sie damit Ihre Vorlage.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

Der Parameter **storageSKU** hat einen Standardwert. Dieser Wert wird verwendet, wenn während der Bereitstellung kein Wert angegeben wird. Außerdem verfügt er über eine Liste zulässiger Werte. Diese entsprechen den Werten, die zum Erstellen eines Speicherkontos erforderlich sind, und verhindern, dass Benutzer Ihrer Vorlage SKUs übergeben, die nicht funktionieren.

## <a name="redeploy-template"></a>Erneutes Bereitstellen der Vorlage

Jetzt können Sie Ihre Vorlage erneut bereitstellen. Da die Standard-SKU auf **Standard_LRS** festgelegt ist, müssen Sie keinen Wert für diesen Parameter angeben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Wenn bei der Bereitstellung ein Fehler aufgetreten ist, verwenden Sie den Schalter **verbose**, um Informationen zu den erstellten Ressourcen abzurufen. Verwenden Sie den Schalter **debug**, um weitere Informationen zum Debuggen zu erhalten.

Damit Sie sehen, wie flexibel Ihre Vorlage ist, stellen Sie sie nun nochmal bereit. Dieses Mal legen Sie den SKU-Parameter auf **Standard_GRS** fest. Sie können entweder einen neuen Namen übergeben, um ein anderes Speicherkonto zu erstellen, oder den gleichen Namen verwenden, um Ihr vorhandenes Speicherkonto zu aktualisieren. Beide Optionen funktionieren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Zum Schluss überprüfen Sie mithilfe eines weiteren Tests, was passiert, wenn Sie eine SKU übergeben, die nicht in der Liste zulässiger Werte enthalten ist. In diesem Fall testen Sie das Szenario, in dem ein Benutzer Ihrer Vorlage glaubt, dass **basic** eine der zulässigen SKUs ist.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

Der Befehl schlägt sofort fehl, und es wird eine Fehlermeldung mit den zulässigen Werten angezeigt. Resource Manager erkennt den Fehler vor dem Start der Bereitstellung.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit dem nächsten Tutorial fortfahren, müssen Sie die Ressourcengruppe nicht löschen.

Falls Sie keine weiteren Tutorials durchführen möchten, können Sie die bereitgestellten Ressourcen bereinigen, indem Sie die Ressourcengruppe löschen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

Sie haben die im [ersten Tutorial](template-tutorial-create-first-template.md) erstellte Vorlage verbessert, indem Sie ihr Parameter hinzugefügt haben. Im nächsten Tutorial befassen Sie sich mit Vorlagenfunktionen.

> [!div class="nextstepaction"]
> [Hinzufügen von Vorlagenfunktionen](template-tutorial-add-functions.md)
