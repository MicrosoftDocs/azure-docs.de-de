---
title: 'Tutorial: Hinzufügen von Ausgaben zur Vorlage'
description: Fügen Sie Ihrer Azure Resource Manager-Vorlage (ARM-Vorlage) Ausgaben hinzu, um die Syntax zu vereinfachen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 7acfb168462354017168ee333ea0b1ecaea434c1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107123"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Tutorial: Hinzufügen von Ausgaben zu Ihrer ARM-Vorlage

In diesem Tutorial wird beschrieben, wie Sie einen Wert aus Ihrer ARM-Vorlage (Azure Resource Manager-Vorlage) zurückgeben. Sie verwenden Ausgaben, wenn Sie einen Wert von einer bereitgestellten Ressource benötigen. Dieses Tutorial dauert ungefähr **7 Minuten**.

## <a name="prerequisites"></a>Voraussetzungen

Wir empfehlen, das [Tutorial zu Variablen](template-tutorial-add-variables.md) durchzuführen. Dies ist jedoch keine Voraussetzung.

Sie benötigen Visual Studio Code mit der Resource Manager-Tools-Erweiterung und entweder Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI). Weitere Informationen finden Sie unter [Vorlagentools](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Überprüfen der Vorlage

Am Ende des vorherigen Tutorials enthielt Ihre Vorlage den folgenden JSON-Code:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Der Code stellt ein Speicherkonto bereit, gibt aber keine Informationen zu diesem Konto zurück. Möglicherweise müssen Sie Eigenschaften von einer neuen Ressource erfassen, damit sie später zur Referenz verfügbar sind.

## <a name="add-outputs"></a>Hinzufügen von Ausgaben

Sie können Ausgaben verwenden, um Werte aus der Vorlage zurückzugeben. Beispielsweise kann es hilfreich sein, die Endpunkte für Ihr neues Speicherkonto abzurufen.

Im folgenden Beispiel ist die Änderung hervorgehoben, die Sie vornehmen, um Ihrer Vorlage einen Ausgabewert hinzuzufügen. Kopieren Sie die gesamte Datei, und ersetzen Sie Ihre Vorlage durch den Inhalt der Datei.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Beachten Sie bezüglich des von Ihnen hinzugefügten Ausgabewerts folgende wichtige Punkte.

Der Typ des Rückgabewerts ist auf `object` festgelegt, d. h. es wird ein JSON-Objekt zurückgegeben.

Der Laufzeitstatus des Speicherkontos wird mithilfe der Funktion [reference](template-functions-resource.md#reference) abgerufen. Um den Laufzeitstatus einer Ressource abzurufen, übergeben Sie den Namen oder die ID der Ressource. In diesem Fall verwenden Sie die Variable, die Sie zum Erstellen des Speicherkontonamens verwendet haben.

Am Ende wird die Eigenschaft `primaryEndpoints` des Speicherkontos zurückgegeben.

## <a name="deploy-template"></a>Bereitstellen der Vorlage

Jetzt können Sie die Vorlage bereitstellen und sich den zurückgegebenen Wert ansehen.

Falls Sie die Ressourcengruppe noch nicht erstellt haben, folgen Sie den Anweisungen unter [Erstellen einer Ressourcengruppe](template-tutorial-create-first-template.md#create-resource-group). Dieses Beispiel setzt voraus, dass Sie die Variable `templateFile` wie im [ersten Tutorial](template-tutorial-create-first-template.md#deploy-template) beschrieben auf den Pfad zur Vorlagendatei festgelegt haben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Für die Ausführung dieses Bereitstellungsbefehls müssen Sie über die [aktuelle Version](/cli/azure/install-azure-cli) der Azure CLI verfügen.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Die Ausgabe für den Bereitstellungsbefehl enthält ein ähnliches Objekt wie im folgenden Beispiel (allerdings nur, wenn die Ausgabe im JSON-Format vorliegt):

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Wenn bei der Bereitstellung ein Fehler aufgetreten ist, verwenden Sie den Schalter `verbose`, um Informationen zu den erstellten Ressourcen abzurufen. Verwenden Sie den Schalter `debug`, um weitere Informationen zum Debuggen zu erhalten.

## <a name="review-your-work"></a>Überprüfen Ihrer Arbeit

In den letzten sechs Tutorials haben Sie viele Aufgaben erledigt. Nehmen wir uns einen Moment Zeit, um uns Ihre Arbeit anzusehen. Sie haben eine Vorlage mit Parametern erstellt, die einfach angegeben werden können. Die Vorlage kann in unterschiedlichen Umgebungen wiederverwendet werden, da sie Anpassungen ermöglicht und erforderliche Werte dynamisch erstellt. Außerdem gibt sie Informationen zum Speicherkonto zurück, die Sie in Ihrem Skript verwenden können.

Sehen wir uns nun die Ressourcengruppe und den Bereitstellungsverlauf an.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Menü **Ressourcengruppen** aus.
1. Wählen Sie die Ressourcengruppe aus, in der Sie die Vorlage bereitgestellt haben.
1. Je nachdem, welche Schritte Sie ausgeführt haben, sollten Sie mindestens ein oder vielleicht mehrere Speicherkonten in der Ressourcengruppe haben.
1. Zudem sollten im Verlauf mehrere erfolgreiche Bereitstellungen angezeigt werden. Klicken Sie auf diesen Link.

   ![Auswählen von Bereitstellungen](./media/template-tutorial-add-outputs/select-deployments.png)

1. Im Verlauf werden all Ihre Bereitstellungen angezeigt. Wählen Sie die Bereitstellung **addoutputs** aus.

   ![Anzeigen des Bereitstellungsverlaufs](./media/template-tutorial-add-outputs/show-history.png)

1. Sie können die Eingaben überprüfen.

   ![Anzeigen der Eingaben](./media/template-tutorial-add-outputs/show-inputs.png)

1. Sie können die Ausgaben überprüfen.

   ![Anzeigen der Ausgaben](./media/template-tutorial-add-outputs/show-outputs.png)

1. Sie können die Vorlage überprüfen.

   ![Anzeigen der Vorlage](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit dem nächsten Tutorial fortfahren, müssen Sie die Ressourcengruppe nicht löschen.

Falls Sie keine weiteren Tutorials durchführen möchten, können Sie die bereitgestellten Ressourcen bereinigen, indem Sie die Ressourcengruppe löschen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie der Vorlage einen Rückgabewert hinzugefügt. Im nächsten Tutorial erfahren Sie, wie Sie eine Vorlage exportieren und Teile dieser exportierten Vorlage in Ihrer Vorlage verwenden.

> [!div class="nextstepaction"]
> [Verwenden einer exportierten Vorlage](template-tutorial-export-template.md)
