---
title: 'Tutorial: Hinzufügen einer Variablen zur Vorlage'
description: Fügen Sie Ihrer Azure Resource Manager-Vorlage (ARM-Vorlage) Variablen hinzu, um die Syntax zu vereinfachen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: d259510d880cbfc60e9ae80b533af6792cc95536
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930727"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Tutorial: Hinzufügen von Variablen zu Ihrer ARM-Vorlage

In diesem Tutorial wird beschrieben, wie Sie Ihrer ARM-Vorlage (Azure Resource Manager-Vorlage) eine Variable hinzufügen. Variablen vereinfachen Ihre Vorlagen, da sie es Ihnen ermöglichen, einen Ausdruck einmal zu schreiben und dann in der gesamten Vorlage wiederzuverwenden. Dieses Tutorial dauert ungefähr **7 Minuten**.

## <a name="prerequisites"></a>Voraussetzungen

Wir empfehlen, das [Tutorial zu Funktionen](template-tutorial-add-functions.md) durchzuführen. Dies ist jedoch keine Voraussetzung.

Sie benötigen Visual Studio Code mit der Resource Manager-Tools-Erweiterung und entweder Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI). Weitere Informationen finden Sie unter [Vorlagentools](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Überprüfen der Vorlage

Am Ende des vorherigen Tutorials enthielt Ihre Vorlage den folgenden JSON-Code:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

Die Verwendung des Parameters für den Speicherkontonamen ist schwierig, da Sie einen eindeutigen Namen angeben müssen. Wenn Sie die vorherigen Tutorials in dieser Reihe durchgeführt haben, sind Sie es wahrscheinlich leid, einen eindeutigen Namen erraten zu müssen. Um dieses Problem zu lösen, fügen Sie eine Variable hinzu, die einen eindeutigen Namen für das Speicherkonto erstellt.

## <a name="use-variable"></a>Verwenden einer Variablen

Im folgenden Beispiel sind die Änderungen hervorgehoben, die Sie vornehmen, um Ihrer Vorlage eine Variable hinzuzufügen, die einen eindeutigen Speicherkontonamen erstellt. Kopieren Sie die gesamte Datei, und ersetzen Sie Ihre Vorlage durch den Inhalt der Datei.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

Beachten Sie, dass die Datei eine Variable namens **uniqueStorageName** enthält. Diese Variable verwendet vier Funktionen, um einen Zeichenfolgenwert zu erstellen.

Da Sie bereits mit der Funktion [parameters](template-functions-deployment.md#parameters) vertraut sind, werden wir uns an dieser Stelle nicht mit ihr befassen.

Sie kennen auch die Funktion [resourceGroup](template-functions-resource.md#resourcegroup). Im vorherigen Tutorial haben Sie die **location**-Eigenschaft abgerufen. In diesem Fall rufen Sie stattdessen die **id**-Eigenschaft ab. Die **id**-Eigenschaft gibt den vollständigen Bezeichner der Ressourcengruppe zurück, einschließlich der Abonnement-ID und des Ressourcengruppennamens.

Die Funktion [uniqueString](template-functions-string.md#uniquestring) erstellt einen Hashwert mit 13 Zeichen. Der zurückgegebene Wert hängt von den Parametern ab, die Sie übergeben. In diesem Tutorial verwenden Sie die Ressourcengruppen-ID als Eingabe für den Hashwert. Sie könnten die Vorlage also in anderen Ressourcengruppen bereitstellen und einen anderen eindeutigen Zeichenfolgenwert abrufen. Wenn Sie die Vorlage in derselben Ressourcengruppe bereitstellen, erhalten Sie jedoch den gleichen Wert.

Die Funktion [concat](template-functions-string.md#concat) akzeptiert Werte und kombiniert sie. Bei dieser Variable akzeptiert sie die Zeichenfolge aus dem Parameter und die Zeichenfolge aus der uniqueString-Funktion und kombiniert sie zu einer Zeichenfolge.

Mit dem Parameter **storagePrefix** können Sie ein Präfix übergeben, das Ihnen die Identifizierung von Speicherkonten erleichtert. Sie können eine eigene Benennungskonvention erstellen, mit der Sie Speicherkonten nach der Bereitstellung in einer langen Liste von Ressourcen leichter erkennen können.

Beachten Sie auch, dass der Speichername jetzt nicht mehr auf einen Parameter, sondern auf die Variable festgelegt ist.

## <a name="deploy-template"></a>Bereitstellen der Vorlage

Nun stellen Sie die Vorlage bereit. Die Bereitstellung dieser Vorlage ist einfacher als die der vorherigen Vorlagen, da Sie nur das Präfix für den Speichernamen angeben.

Falls Sie die Ressourcengruppe noch nicht erstellt haben, folgen Sie den Anweisungen unter [Erstellen einer Ressourcengruppe](template-tutorial-create-first-template.md#create-resource-group). Dieses Beispiel setzt voraus, dass Sie die Variable **templateFile** wie im [ersten Tutorial](template-tutorial-create-first-template.md#deploy-template) beschrieben auf den Pfad zur Vorlagendatei festgelegt haben.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Für die Ausführung dieses Bereitstellungsbefehls müssen Sie über die [aktuelle Version](/cli/azure/install-azure-cli) der Azure CLI verfügen.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Wenn bei der Bereitstellung ein Fehler aufgetreten ist, verwenden Sie den Schalter **verbose**, um Informationen zu den erstellten Ressourcen abzurufen. Verwenden Sie den Schalter **debug**, um weitere Informationen zum Debuggen zu erhalten.

## <a name="verify-deployment"></a>Überprüfen der Bereitstellung

Sie können die Bereitstellung überprüfen, indem Sie sich die Ressourcengruppe im Azure-Portal ansehen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Menü **Ressourcengruppen** aus.
1. Wählen Sie die Ressourcengruppe aus, in der Sie die Vorlage bereitgestellt haben.
1. Sie sehen, dass eine Speicherkontoressource bereitgestellt wurde. Der Name des Speicherkontos lautet **store** gefolgt von einer Folge zufälliger Zeichen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mit dem nächsten Tutorial fortfahren, müssen Sie die Ressourcengruppe nicht löschen.

Falls Sie keine weiteren Tutorials durchführen möchten, können Sie die bereitgestellten Ressourcen bereinigen, indem Sie die Ressourcengruppe löschen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Variable hinzugefügt, die einen eindeutigen Namen für ein Speicherkonto erstellt. Im nächsten Tutorial geben Sie einen Wert aus dem bereitgestellten Speicherkonto zurück.

> [!div class="nextstepaction"]
> [Hinzufügen von Ausgaben](template-tutorial-add-outputs.md)
