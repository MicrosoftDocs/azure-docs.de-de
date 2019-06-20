---
title: Verwenden von Bedingungen in Azure Resource Manager-Vorlagen | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Azure-Ressourcen basierend auf Bedingungen bereitstellen.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0d27eef8ee2c70449bacaced0de89bdc5e6989ff
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357809"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Tutorial: Verwenden von Bedingungen in Azure Resource Manager-Vorlagen

Es wird beschrieben, wie Sie Azure-Ressourcen basierend auf Bedingungen bereitstellen.

Im Tutorial [Festlegen der Reihenfolge für die Ressourcenbereitstellung](./resource-manager-tutorial-create-templates-with-dependent-resources.md) erstellen Sie einen virtuellen Computer, ein virtuelles Netzwerk und einige andere abhängige Ressourcen, z.B. ein Speicherkonto. Anstatt jedes Mal ein neues Speicherkonto zu erstellen, können Benutzer zwischen dem Erstellen eines neuen Speicherkontos und dem Verwenden eines vorhandenen Speicherkontos wählen. Sie definieren einen zusätzlichen Parameter, um dieses Ziel zu erreichen. Wenn der Wert des Parameters „new“ lautet, wird ein neues Speicherkonto erstellt. Andernfalls wird ein vorhandenes Speicherkonto mit dem angegebenen Namen verwendet.

![Diagramm der Bedingungen zum Verwenden der Resource Manager-Vorlage](./media/resource-manager-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Öffnen einer Schnellstartvorlage
> * Ändern der Vorlage
> * Bereitstellen der Vorlage
> * Bereinigen von Ressourcen

In diesem Tutorial wird nur ein grundlegendes Szenario der Verwendung von Bedingungen behandelt. Weitere Informationen finden Sie unter

* [Vorlagendateistruktur: Bedingung](./resource-group-authoring-templates.md#condition).
* [Bedingtes Bereitstellen einer Ressource in einer Azure Resource Manager-Vorlage](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Vorlagenfunktion: If](./resource-group-template-functions-logical.md#if) (Wenn).
* [Vergleichsfunktionen für Azure Resource Manager-Vorlagen](./resource-group-template-functions-comparison.md)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

* [Visual Studio Code](https://code.visualstudio.com/) mit der [Erweiterung „Azure Resource Manager-Tools“](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* Verwenden Sie aus Sicherheitsgründen ein generiertes Kennwort für das Administratorkonto des virtuellen Computers. Hier sehen Sie ein Beispiel für die Kennwortgenerierung:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault dient zum Schützen von kryptografischen Schlüsseln und anderen Geheimnissen. Weitere Informationen finden Sie unter [Tutorial: Integrieren von Azure Key Vault in die Resource Manager-Vorlagenbereitstellung](./resource-manager-tutorial-use-key-vault.md). Wir empfehlen Ihnen auch, Ihr Kennwort alle drei Monate zu aktualisieren.

## <a name="open-a-quickstart-template"></a>Öffnen einer Schnellstartvorlage

„Azure-Schnellstartvorlagen“ ist ein Repository für Resource Manager-Vorlagen. Statt eine Vorlage von Grund auf neu zu erstellen, können Sie eine Beispielvorlage verwenden und diese anpassen. Die in diesem Tutorial verwendete Vorlage heißt [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Bereitstellen eines einfachen virtuellen Windows-Computers).

1. Wählen Sie in Visual Studio Code **Datei**>**Datei öffnen** aus.
2. Fügen Sie in **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Wählen Sie **Öffnen** aus, um die Datei zu öffnen.
4. Es gibt fünf Ressourcen, die von der Vorlage definiert werden:

   * `Microsoft.Storage/storageAccounts`. Informationen finden Sie in der [Vorlagenreferenz](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Informationen finden Sie in der [Vorlagenreferenz](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Informationen finden Sie in der [Vorlagenreferenz](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Informationen finden Sie in der [Vorlagenreferenz](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Informationen finden Sie in der [Vorlagenreferenz](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Bevor Sie die Vorlage anpassen, sollten Sie sich zunächst grundlegend damit vertraut machen.
5. Wählen Sie **Datei**>**Speichern unter** aus, um eine Kopie der Datei als **azuredeploy.json** auf dem lokalen Computer zu speichern.

## <a name="modify-the-template"></a>Ändern der Vorlage

Nehmen Sie zwei Änderungen an der vorhandenen Vorlage vor:

* Fügen Sie einen Parameter für den Speicherkontonamen hinzu. Benutzer können entweder einen neuen oder einen vorhandenen Speicherkontonamen angeben.
* Fügen Sie einen neuen Parameter mit dem Namen **newOrExisting** hinzu. Die Bereitstellung nutzt diesen Parameter, um zu ermitteln, wann ein neues Speicherkonto erstellt oder ein vorhandenes Speicherkonto verwendet werden soll.

Hier ist das Verfahren zum Vornehmen von Änderungen angegeben:

1. Öffnen Sie **azuredeploy.json** in Visual Studio Code.
2. Ersetzen Sie die drei **variables('storageAccountName')** in der gesamten Vorlage durch **parameters('storageAccountName')** .
3. Entfernen Sie die folgende Variablendefinition:

    ![Diagramm der Bedingungen zum Verwenden der Resource Manager-Vorlage](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

4. Fügen Sie der Vorlage die folgenden beiden Parameter hinzu:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Die aktualisierte Parameterdefinition sieht wie folgt aus:

    ![Resource Manager-Nutzungsbedingung](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Fügen Sie am Anfang der Speicherkontodefinition die folgende Zeile hinzu.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Mit der Bedingung wird der Wert des Parameters **newOrExisting** überprüft. Wenn der Parameterwert **new** lautet, erstellt die Bereitstellung das Speicherkonto.

    Die aktualisierte Speicherkontodefinition sieht wie folgt aus:

    ![Resource Manager-Nutzungsbedingung](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Aktualisieren Sie die Eigenschaft **storageUri** der VM-Ressourcendefinition mit dem folgenden Wert:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Diese Änderung ist erforderlich, wenn Sie ein vorhandenes Speicherkonto unter einer anderen Ressourcengruppe verwenden.

7. Speichern Sie die Änderungen.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Befolgen Sie die Anweisungen in [Bereitstellen der Vorlage](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), um die Cloud Shell zu öffnen und die geänderte Vorlage hochzuladen, und führen Sie dann das folgende PowerShell-Skript aus, um die Vorlage bereitzustellen.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"
```

> [!NOTE]
> Für die Bereitstellung tritt ein Fehler auf, wenn **newOrExisting** auf **new** festgelegt, aber das Speicherkonto mit dem angegebenen Speicherkontonamen bereits vorhanden ist.

Versuchen Sie, eine andere Bereitstellung zu erstellen, bei der **newOrExisting** auf „existing“ festgelegt ist, und geben Sie ein vorhandenes Speicherkonto an. Informationen zum Erstellen eines Speicherkontos im Voraus finden Sie unter [Speicherkonto erstellen](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen. Um die Ressourcengruppe zu löschen, wählen Sie **Testen Sie es.** aus, um die Cloud Shell zu öffnen. Um das PowerShell-Skript einzufügen, klicken Sie mit der rechten Maustaste auf den Shellberich, und wählen Sie **Einfügen** aus.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Vorlage entwickelt, bei der Benutzer zwischen dem Erstellen eines neuen Speicherkontos und dem Verwenden eines vorhandenen Speicherkontos wählen können. Informationen zum Abrufen von Geheimnissen aus Azure Key Vault und Verwenden der Geheimnisse als Kennwörter bei der Vorlagenbereitstellung finden Sie hier:

> [!div class="nextstepaction"]
> [Tutorial: Integrate Azure Key Vault in Resource Manager Template deployment](./resource-manager-tutorial-use-key-vault.md) (Tutorial: Integrieren von Azure Key Vault in die Resource Manager-Vorlagenbereitstellung)
