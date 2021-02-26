---
title: Rollback bei Fehler zu erfolgreicher Bereitstellung
description: Geben Sie an, dass für eine fehlerhafte Bereitstellung ein Rollback zu einer erfolgreichen Bereitstellung erfolgen soll.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492211"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Rollback bei Fehler zu erfolgreicher Bereitstellung

Wenn eine Bereitstellung fehlschlägt, können Sie automatisch eine frühere, erfolgreiche Bereitstellung aus Ihrem Bereitstellungsverlauf bereitstellen. Diese Funktionalität ist nützlich, wenn es einen bekannten guten Zustand für die Infrastrukturbereitstellung gibt, der wiederhergestellt werden soll. Sie können entweder eine bestimmte frühere Bereitstellung oder die letzte erfolgreiche Bereitstellung angeben.

> [!IMPORTANT]
> Dieses Feature führt einen Rollback für eine nicht erfolgreiche Bereitstellung durch und stellt dazu eine frühere Bereitstellung bereit. Dieses Ergebnis entspricht möglicherweise nicht dem, was Sie erwarten, wenn Sie die nicht erfolgreiche Bereitstellung rückgängig machen. Daher müssen Sie genau verstehen, wie die frühere Bereitstellung erneut bereitgestellt wird.

## <a name="considerations-for-redeploying"></a>Überlegungen zur erneuten Bereitstellung

Machen Sie sich vor der Verwendung dieses Features mit den folgenden Details zur Behandlung der erneuten Bereitstellung vertraut:

- Die vorherige Bereitstellung wird unter Verwendung des [vollständigen Modus](./deployment-modes.md#complete-mode) ausgeführt, auch wenn bei der früheren Bereitstellung der [inkrementelle Modus](./deployment-modes.md#incremental-mode) verwendet wurde. Die erneute Bereitstellung im vollständigen Modus kann zu unerwarteten Ergebnissen führen, wenn bei der früheren Bereitstellung der inkrementelle Modus verwendet wurde. Im vollständigen Modus werden alle Ressourcen, die nicht in der vorherigen Bereitstellung enthalten waren, gelöscht. Geben Sie eine frühere Bereitstellung an, die alle Ressourcen und deren Zustände darstellt, die in der Ressourcengruppe vorhanden sein sollen. Weitere Informationen finden Sie unter [Azure Resource Manager-Bereitstellungsmodi](./deployment-modes.md).
- Die Bereitstellung wird genauso wie zuvor mit denselben Parametern ausgeführt. Sie können die Parameter nicht ändern.
- Die erneute Bereitstellung wirkt sich nur auf die Ressourcen aus. Datenänderungen sind davon nicht betroffen.
- Dieses Feature kann nur für Ressourcengruppenbereitstellungen verwendet werden. Bereitstellungen auf Abonnement-, Verwaltungsgruppen- oder Mandantenebene werden nicht unterstützt. Weitere Informationen zu Bereitstellungen auf Abonnementebene finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](./deploy-to-subscription.md).
- Diese Option kann nur für Bereitstellungen auf Stammebene verwendet werden. Bereitstellungen aus einer geschachtelten Vorlage können nicht erneut bereitgestellt werden.

Wenn Sie diese Option verwenden möchten, müssen Ihre Bereitstellungen im Bereitstellungsverlauf über eindeutige Namen verfügen. Eine bestimmte Bereitstellung kann nur mit eindeutigen Namen identifiziert werden. Ohne eindeutige Namen kann es passieren, dass eine erfolgreiche Bereitstellung im Verlauf durch eine nicht erfolgreiche Bereitstellung überschrieben wird.

Wenn Sie eine frühere Bereitstellung angeben, die nicht im Bereitstellungsverlauf vorhanden ist, tritt beim Rollback ein Fehler auf.

## <a name="powershell"></a>PowerShell

Um die letzte erfolgreiche Bereitstellung erneut bereitzustellen, fügen Sie den Parameter `-RollbackToLastDeployment` als Flag hinzu.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Um eine bestimmte Bereitstellung erneut bereitzustellen, verwenden den Parameter `-RollBackDeploymentName` und geben den Namen der Bereitstellung an. Die angegebene Bereitstellung muss erfolgreich ausgeführt worden sein.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Um die letzte erfolgreiche Bereitstellung erneut bereitzustellen, fügen Sie den Parameter `--rollback-on-error` als Flag hinzu.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Um eine bestimmte Bereitstellung erneut bereitzustellen, verwenden den Parameter `--rollback-on-error` und geben den Namen der Bereitstellung an. Die angegebene Bereitstellung muss erfolgreich ausgeführt worden sein.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST-API

Um die letzte erfolgreich ausgeführte Bereitstellung erneut bereitzustellen, wenn bei der aktuellen Bereitstellung Fehler auftreten, verwenden Sie folgenden Code:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Um eine bestimmte Bereitstellung erneut bereitzustellen, wenn bei der aktuellen Bereitstellung Fehler auftreten, verwenden Sie folgenden Code:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Die angegebene Bereitstellung muss erfolgreich ausgeführt worden sein.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum vollständigen und inkrementellen Modus finden Sie unter [Azure Resource Manager-Bereitstellungsmodi](deployment-modes.md).
- Um zu verstehen, wie Parameter in der Vorlage definiert werden, lesen Sie [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](template-syntax.md).
