---
title: Erstellen einer Vorlagenspezifikation mit verknüpften Vorlagen
description: In diesem Artikel erfahren Sie, wie Sie eine Vorlagenspezifikation mit verknüpften Vorlagen erstellen.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 038fb3e6bbb6086535b83a69de7a3fe49a8e60bb
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518888"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Tutorial: Erstellen einer Vorlagenspezifikation mit verknüpften Vorlagen (Vorschau)

Erfahren Sie, wie Sie eine [Vorlagenspezifikation](template-specs.md) mit einer Hauptvorlage und einer [verknüpften Vorlage](linked-templates.md#linked-template) erstellen. Mithilfe von Vorlagenspezifikationen teilen Sie ARM-Vorlagen mit anderen Benutzer in Ihrer Organisation. In diesem Artikel erfahren Sie, wie Sie eine Vorlagenspezifikation erstellen, um mithilfe der Eigenschaft `relativePath` der [Bereitstellungsressource](/azure/templates/microsoft.resources/deployments) ein Paket mit einer Hauptvorlage und ihren verknüpften Vorlagen zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Vorlagenspezifikationen befinden sich derzeit in der Vorschauphase. Zur Verwendung mit Azure PowerShell müssen Sie [mindestens Version 5.0.0](/powershell/azure/install-az-ps) installieren. Zur Nutzung mit der Azure-Befehlszeilenschnittstelle muss [mindestens Version 2.14.2](/cli/azure/install-azure-cli) verwendet werden.

## <a name="create-linked-templates"></a>Erstellen verknüpfter Vorlagen

Erstellen Sie die Hauptvorlage und die verknüpfte Vorlage.

Zum Verknüpfen einer Vorlage fügen Sie der Hauptvorlage eine [Bereitstellungsressource](/azure/templates/microsoft.resources/deployments) hinzu. Geben Sie bei der Eigenschaft `templateLink` den relativen Pfad der verknüpften Vorlage basierend auf dem Pfad der übergeordneten Vorlage an.

Die verknüpfte Vorlage heißt **linkedTemplate.json** und ist in einem Unterordner namens **artifacts** unter dem Pfad gespeichert, unter dem auch die Hauptvorlage gespeichert ist.  Sie können einen der folgenden Werte für relativePath verwenden:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

Die Eigenschaft `relativePath` ist immer relativ zu der Vorlagendatei, in der `relativePath` deklariert wird. Wenn also eine andere Datei namens „linkedTemplate2.json“ in linkedTemplate.json aufgerufen wird und linkedTemplate2.json ebenfalls im Unterordner „artifacts“ gespeichert ist, lautet der in linkedTemplate.json angegebene Wert für relativePath nur `linkedTemplate2.json`.

1. Erstellen Sie die Hauptvorlage mit dem folgenden JSON-Code. Speichern Sie sie anschließend als **azuredeploy.json** auf Ihrem lokalen Computer. In diesem Tutorial wird davon ausgegangen, dass die Speicherung unter dem Pfad **c:\Templates\linkedTS\azuredeploy.json** erfolgt ist. Sie können jedoch einen beliebigen Pfad verwenden.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
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
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > Die apiVersion von `Microsoft.Resources/deployments` muss 2020-06-01 oder höher sein.

1. Erstellen Sie ein Verzeichnis namens **artifacts** in dem Ordner, in dem die Hauptvorlage gespeichert ist.
1. Erstellen Sie die verknüpfte Vorlage mit dem folgenden JSON-Code:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Speichern Sie die Vorlage als **linkedTemplate.json** im Ordner **artifacts**.

## <a name="create-template-spec"></a>Erstellen von Vorlagenspezifikationen

Vorlagenspezifikationen werden in Ressourcengruppen gespeichert.  Erstellen Sie mit dem folgenden Skript eine Ressourcengruppe und anschließend eine Vorlagenspezifikation. Der Name der Vorlagenspezifikation ist **webSpec**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "c:\Templates\linkedTS\azuredeploy.json"
```

---

Wenn Sie fertig sind, können Sie die Vorlagenspezifikation über das Azure-Portal oder das folgende Cmdlet anzeigen:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>Bereitstellen von Vorlagenspezifikationen

Jetzt können Sie die Vorlagenspezifikation bereitstellen. Das Bereitstellen der Vorlagenspezifikation gestaltet sich genauso wie die Bereitstellung der enthaltenen Vorlage, mit der Ausnahme, dass Sie die Ressourcen-ID der Vorlagenspezifikation übergeben. Sie verwenden dieselben Bereitstellungsbefehle und übergeben bei Bedarf Parameterwerte für die Vorlagenspezifikation.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az template-specs show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> Es gibt ein bekanntes Problem beim Abrufen der Vorlagenspezifikations-ID und deren Zuweisung zu einer Variablen in Windows PowerShell.

---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Bereitstellung einer Vorlagenspezifikation als verknüpfte Vorlage finden Sie unter [Tutorial: Bereitstellen einer Vorlagenspezifikation als verknüpfte Vorlage](template-specs-deploy-linked-template.md).
