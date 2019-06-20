---
title: Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Arbeitsbereichs
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage verwenden, um einen neuen Azure Machine Learning Service-Arbeitsbereich zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: abe497ed96515e8194fb2ddefd8e7f4cb9908758
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205131"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Arbeitsbereichs für den Azure Machine Learning Service

In diesem Artikel erlernen Sie verschiedene Möglichkeiten zum Erstellen eines Azure Machine Learning Service-Arbeitsbereichs mithilfe von Azure Resource Manager-Vorlagen. Eine Resource Manager-Vorlage erleichtert das Erstellen von Ressourcen in einem einzelnen, koordinierten Vorgang. Eine Vorlage ist ein JSON-Dokument, das die Ressourcen definiert, die für eine Bereitstellung erforderlich sind. Es kann außerdem bestimmte Bereitstellungsparameter angeben. Parameter werden verwendet, um Eingabewerte bereitzustellen, wenn die Vorlage verwendet wird.

Weitere Informationen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Resource Manager-Vorlage](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein **Azure-Abonnement**. Wenn Sie keins besitzen, probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

* Um eine Vorlage über eine Befehlszeilenschnittstelle zu verwenden, benötigen Sie entweder [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) oder die [Azure-Befehlszeilenschnittstelle (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Resource Manager-Vorlage

Die folgende Resource Manager-Vorlage kann verwendet werden, um einen Azure Machine Learning Service-Arbeitsbereich mit zugeordneten Azure-Ressourcen zu erstellen:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Diese Vorlage erstellt die folgenden Azure-Dienste:

* Azure-Ressourcengruppe
* Azure Storage-Konto
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-Arbeitsbereich

Die Ressourcengruppe ist der Container, der die Dienste enthält. Die verschiedenen Dienste sind für den Azure Machine Learning-Arbeitsbereich erforderlich.

Die Beispielvorlage verfügt über zwei Parameter:

* Den **Ort**, an dem die Ressourcengruppe und die Dienste erstellt werden.

    Die Vorlage verwendet den von Ihnen ausgewählten Ort für die meisten Ressourcen. Die Ausnahme ist hierbei der Application Insights-Dienst, der nicht an allen Orten verfügbar ist, an denen die anderen Dienste verfügbar sind. Wenn Sie einen Ort auswählen, an dem er nicht verfügbar ist, wird der Dienst am Ort „USA, Süden-Mitte“ erstellt.

* Der **Arbeitsbereichsname**, wobei es sich um den Anzeigenamen des Azure Machine Learning-Arbeitsbereichs handelt.

    Die Namen der anderen Dienste werden nach dem Zufallsprinzip generiert.

Weitere Informationen zu Vorlagen finden Sie in den folgenden Artikeln:

* [Erstellen von Azure Resource-Manager-Vorlagen](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Bereitstellen einer Anwendung mit Azure Resource Manager-Vorlagen](../../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.MachineLearningServices-Ressourcentypen](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Befolgen Sie die Schritte in [Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Wenn Sie zum Bildschirm __Vorlage bearbeiten__ gelangen, fügen Sie die Vorlage aus diesem Dokument ein.
1. Wählen Sie __Speichern__ aus, um die Vorlage zu verwenden. Geben Sie die folgenden Informationen an, und stimmen Sie den aufgeführten Bestimmungen und Bedingungen zu:

   * Abonnement: Wählen Sie aus, welches Azure-Abonnement für diese Ressourcen verwendet werden soll.
   * Ressourcengruppe: Wählen Sie eine Ressourcengruppe für die Aufnahme der Dienste aus, oder erstellen Sie eine.
   * Arbeitsbereichsname: Der für den Azure Machine Learning-Arbeitsbereich, der erstellt wird, zu verwendende Name. Der Arbeitsbereichsname muss zwischen 3 und 33 Zeichen umfassen. Er darf nur alphanumerische Zeichen und Bindestriche („-“) enthalten.
   * Standort: Wählen Sie den Ort aus, an dem die Ressourcen erstellt werden.

     ![Die Vorlagenparameter im Azure-Portal](media/how-to-create-workspace-template/template-parameters.png)

Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Mithilfe von Azure PowerShell

In diesem Beispiel wird vorausgesetzt, dass Sie die Vorlagen in einer Datei namens `azuredeploy.json` im aktuellen Verzeichnis gespeichert haben:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) und [Bereitstellen privater Resource Manager-Vorlagen mit SAS-Token und Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Mithilfe der Azure-Befehlszeilenschnittstelle

In diesem Beispiel wird vorausgesetzt, dass Sie die Vorlagen in einer Datei namens `azuredeploy.json` im aktuellen Verzeichnis gespeichert haben:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und der Azure-Befehlszeilenschnittstelle](../../azure-resource-manager/resource-group-template-deploy-cli.md) und [Bereitstellen privater Resource Manager-Vorlagen mit SAS-Token und der Azure-Befehlszeilenschnittstelle](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Resource Manager-REST-API](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Erstellen und Bereitstellen von Azure-Ressourcengruppen mit Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
