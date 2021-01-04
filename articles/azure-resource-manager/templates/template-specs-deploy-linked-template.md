---
title: Bereitstellen einer Vorlagenspezifikation als verknüpfte Vorlage
description: Erfahren Sie, wie Sie eine vorhandene Vorlagenspezifikation in einer verknüpften Bereitstellung bereitstellen.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: b30a99a469ec010dcc2e128bbeb446b493631608
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518871"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Tutorial: Bereitstellen einer Vorlagenspezifikation als verknüpfte Vorlage (Vorschau)

Erfahren Sie, wie Sie eine vorhandene [Vorlagenspezifikation](template-specs.md) mithilfe einer [verknüpften Bereitstellung](linked-templates.md#linked-template) bereitstellen. Mithilfe von Vorlagenspezifikationen teilen Sie ARM-Vorlagen mit anderen Benutzer in Ihrer Organisation. Nachdem Sie eine Vorlagenspezifikation erstellt haben, können Sie die Vorlagenspezifikation mithilfe von Azure PowerShell oder Azure CLI bereitstellen. Sie können die Vorlagenspezifikation auch als Teil ihrer Projektmappe bereitstellen, indem Sie eine verknüpfte Vorlage verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Vorlagenspezifikationen befinden sich derzeit in der Vorschauphase. Zur Verwendung mit Azure PowerShell müssen Sie [mindestens Version 5.0.0](/powershell/azure/install-az-ps) installieren. Zur Verwendung mit der Azure-Befehlszeilenschnittstelle verwenden Sie [mindestens Version 2.14.2](/cli/azure/install-azure-cli).

## <a name="create-a-template-spec"></a>Erstellen einer Vorlagenspezifikation

Gehen Sie wie unter [Schnellstart: Erstellen und Bereitstellen von Vorlagenspezifikationen](quickstart-create-template-specs.md) vor, um eine Vorlagenspezifikation für die Bereitstellung eines Speicherkontos zu erstellen. Im nächsten Abschnitt benötigen Sie den Ressourcengruppennamen der Vorlagenspezifikation, den Namen der Vorlagenspezifikation und die Version der Vorlagenspezifikation.

## <a name="create-the-main-template"></a>Erstellen der Hauptvorlage

Zum Bereitstellen einer Vorlagenspezifikation in einer ARM-Vorlage fügen Sie Ihrer Hauptvorlage eine [Bereitstellungsressource](/azure/templates/microsoft.resources/deployments) hinzu. Geben Sie in der `templateLink`-Eigenschaft die Ressourcen-ID einer Vorlagenspezifikation an. Erstellen Sie eine Vorlage mit dem folgenden JSON-Code namens **azuredeploy.json**. In diesem Tutorial wird davon ausgegangen, dass die Speicherung im Pfad **c:\Templates\deployTS\azuredeploy.json** erfolgt ist, doch Sie können einen beliebigen Pfad verwenden.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

Die Vorlagenspezifikations-ID wird mithilfe der [`resourceID()`](template-functions-resource.md#resourceid)-Funktion generiert. Das Ressourcengruppenargument in der resourceID()-Funktion ist optional, wenn sich die Vorlagenspezifikation (templateSpec) in derselben Ressourcengruppe der aktuellen Bereitstellung befindet.  Sie können die Ressourcen-ID auch direkt als Parameter übergeben. Zum Abrufen der ID verwenden Sie:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Ein Problem beim Abrufen der Vorlagenspezifikations-ID und deren Zuweisung zu einer Variablen in Windows PowerShell ist bekannt.

---

Die Syntax zum Übergeben von Parametern an die Vorlagenspezifikation lautet:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> Die apiVersion von `Microsoft.Resources/deployments` muss 2020-06-01 oder höher sein.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Wenn Sie die verknüpfte Vorlage bereitstellen, stellt sie sowohl die Webanwendung als auch das Speicherkonto bereit. Die Bereitstellung ist mit der Bereitstellung anderer ARM-Vorlagen identisch.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen einer Vorlagenspezifikation, die verknüpfte Vorlagen umfasst, finden Sie unter [Erstellen einer Vorlagenspezifikation einer verknüpften Vorlage](template-specs-create-linked.md).
