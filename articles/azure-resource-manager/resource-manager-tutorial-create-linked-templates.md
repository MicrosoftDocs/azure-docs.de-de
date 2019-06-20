---
title: Erstellen verknüpfter Azure Resource Manager-Vorlagen | Microsoft-Dokumentation
description: Informationen zum Erstellen verknüpfter Azure Resource Manager-Vorlagen zum Erstellen virtueller Computer
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/18/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: de2e848bd587f3b9bf2efe3fa8df3710e24243e4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241381"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Tutorial: Erstellen verknüpfter Azure Resource Manager-Vorlagen

Informationen zum Erstellen verknüpfter Azure Resource Manager-Vorlagen. Mit verknüpften Vorlagen können Sie eine Vorlage durch eine andere Vorlage aufrufen lassen. Dies eignet sich hervorragend zum Modularisieren von Vorlagen. In diesem Tutorial verwenden Sie die gleiche Vorlage wie unter [Tutorial: Erstellen von Azure Resource Manager-Vorlagen mit abhängigen Ressourcen](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Mit dieser Vorlage werden ein virtueller Computer, ein virtuelles Netzwerk und andere abhängige Ressourcen (einschließlich eines Speicherkontos) erstellt. Sie separieren die Erstellung der Speicherkontoressource in einer verknüpften Vorlage.

Das Aufrufen einer verknüpften Vorlage ähnelt einem Funktionsaufruf.  Außerdem erfahren Sie, wie Sie Parameterwerte an die verknüpfte Vorlage übergeben und „Rückgabewerte“ aus der verknüpften Vorlage abrufen.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Öffnen einer Schnellstartvorlage
> * Erstellen der verknüpften Vorlage
> * Hochladen der verknüpften Vorlage
> * Verknüpfen mit der verknüpften Vorlage
> * Konfigurieren der Abhängigkeit
> * Bereitstellen der Vorlage
> * Weitere Übungen

Weitere Informationen finden Sie unter [Verwenden von verknüpften und geschachtelten Vorlagen bei der Bereitstellung von Azure-Ressourcen](./resource-group-linked-templates.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

* [Visual Studio Code](https://code.visualstudio.com/) mit der [Erweiterung „Azure Resource Manager-Tools“](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* Verwenden Sie aus Sicherheitsgründen ein generiertes Kennwort für das Administratorkonto des virtuellen Computers. Hier sehen Sie ein Beispiel für die Kennwortgenerierung:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault dient zum Schützen von kryptografischen Schlüsseln und anderen Geheimnissen. Weitere Informationen finden Sie unter [Tutorial: Integrieren von Azure Key Vault in die Resource Manager-Vorlagenbereitstellung](./resource-manager-tutorial-use-key-vault.md). Wir empfehlen Ihnen auch, Ihr Kennwort alle drei Monate zu aktualisieren.

## <a name="open-a-quickstart-template"></a>Öffnen einer Schnellstartvorlage

„Azure-Schnellstartvorlagen“ ist ein Repository für Resource Manager-Vorlagen. Statt eine Vorlage von Grund auf neu zu erstellen, können Sie eine Beispielvorlage verwenden und diese anpassen. Die in diesem Tutorial verwendete Vorlage heißt [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Bereitstellen eines einfachen virtuellen Windows-Computers). Hierbei handelt es sich um die gleiche Vorlage, die auch unter [Tutorial: Erstellen von Azure Resource Manager-Vorlagen mit abhängigen Ressourcen](./resource-manager-tutorial-create-templates-with-dependent-resources.md) verwendet wird. Sie speichern zwei Kopien der gleichen Vorlage, die verwendet werden sollen als:

* **Die Hauptvorlage**: Erstellen aller Ressourcen mit Ausnahme des Speicherkontos.
* **Die verknüpfte Vorlage**: Erstellen des Speicherkontos.

1. Wählen Sie in Visual Studio Code **Datei**>**Datei öffnen** aus.
2. Fügen Sie in **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Wählen Sie **Öffnen** aus, um die Datei zu öffnen.
4. Es gibt fünf Ressourcen, die von der Vorlage definiert werden:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     Bevor Sie die Vorlage anpassen, sollten Sie sich zunächst grundlegend mit dem Vorlagenschema vertraut machen.
5. Wählen Sie **Datei**>**Speichern unter** aus, um eine Kopie der Datei als **azuredeploy.json** auf dem lokalen Computer zu speichern.
6. Wählen Sie **Datei**>**Speichern unter** aus, um eine andere Kopie der Datei mit dem Namen **linkedTemplate.json** zu erstellen.

## <a name="create-the-linked-template"></a>Erstellen der verknüpften Vorlage

Die verknüpfte Vorlage erstellt ein Speicherkonto. Die verknüpfte Vorlage kann als eigenständige Vorlage verwendet werden, um ein Speicherkonto zu erstellen. In diesem Tutorial nimmt die verknüpfte Vorlage zwei Parameter an und gibt einen Wert an die Hauptvorlage zurück. Dieser „Rückgabewert“ wird im `outputs`-Element definiert.

1. Öffnen Sie **linkedTemplate.json** in Visual Studio Code, falls die Datei nicht geöffnet ist.
2. Nehmen Sie die folgenden Änderungen vor:

    * Entfernen Sie alle Parameter außer **location**.
    * Fügen Sie einen Parameter namens **storageAccountName** hinzu.
        ```json
        "storageAccountName":{
          "type": "string",
          "metadata": {
              "description": "Azure Storage account name."
          }
        },
        ```
        Der Name des Speicherkontos und der Speicherort werden aus der Hauptvorlage als Parameter an die verknüpfte Vorlage übergeben.

    * Entfernen Sie das **variables**-Element und alle Variablendefinitionen.
    * Entfernen Sie alle Ressourcen mit Ausnahme des Speicherkontos. Sie entfernen insgesamt vier Ressourcen.
    * Aktualisieren Sie den Wert des **name**-Elements der Speicherkontoressource in Folgendes:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Aktualisieren Sie das **outputs**-Element, sodass es so aussieht:

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```
       Die VM-Ressourcendefinition in der Hauptvorlage benötigt **storageUri**.  Sie geben den Wert als Ausgabewert an die Hauptvorlage zurück.

        Wenn Sie fertig sind, sollte die Vorlage etwa so aussehen:

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
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
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```
3. Speichern Sie die Änderungen.

## <a name="upload-the-linked-template"></a>Hochladen der verknüpften Vorlage

Von dort aus, wo Sie die Bereitstellung ausführen, muss der Zugriff auf die Hauptvorlage und die verknüpfte Vorlage möglich sein. In diesem Tutorial verwenden Sie die Cloud Shell-Bereitstellungsmethode, die Sie auch unter [Tutorial: Erstellen von Azure Resource Manager-Vorlagen mit abhängigen Ressourcen](./resource-manager-tutorial-create-templates-with-dependent-resources.md) verwendet haben. Die Hauptvorlage (azuredeploy.json) wird in die Shell hochgeladen. Die verknüpfte Vorlage (linkedTemplate.json) muss an einem sicheren Ort freigegeben werden. Das folgende PowerShell-Skript erstellt ein Azure Storage-Konto, lädt die Vorlage in das Storage-Konto hoch und generiert dann ein SAS-Token, um eingeschränkten Zugriff auf die Vorlagendatei zu gewähren. Zur Vereinfachung des Tutorials lädt das Skript eine vollständige verknüpfte Vorlage aus einem freigegebenen Speicherort herunter. Wenn Sie die von Ihnen erstellte verknüpfte Vorlage verwenden möchten, können Sie sie mit [Cloud Shell](https://shell.azure.com) hochladen und das Skript zur Verwendung Ihrer eigenen verknüpften Vorlage anpassen.

> [!NOTE]
> Das Skript gibt vor, dass das SAS-Token innerhalb von acht Stunden verwendet werden muss. Falls Sie mehr Zeit zum Absolvieren dieses Tutorials benötigen, verlängern Sie die Ablaufzeit.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. Wählen Sie die grüne Schaltfläche **Jetzt testen** aus, um den Azure Cloud Shell-Bereich zu öffnen.
2. Wählen Sie **Kopieren**, um das PowerShell-Skript zu kopieren.
3. Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle im Shell-Bereich (dunkelblauer Bereich), und wählen Sie dann **Einfügen** aus.
4. Notieren Sie sich die beiden Werte (Name der Ressourcengruppe und URI der verknüpften Vorlage) am Ende des Shell-Bereichs. Diese werden im weiteren Verlauf des Tutorials benötigt.
5. Wählen Sie **Fokusmodus beenden** aus, um den Shell-Bereich zu schließen.

In der Praxis generieren Sie bei der Bereitstellung der Hauptvorlage ein SAS-Token und legen einen kürzeren Ablaufzeitraum fest, um es sicherer zu machen. Weitere Informationen finden Sie unter [Bereitstellen privater Resource Manager-Vorlagen mit SAS-Token und Azure PowerShell](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Aufrufen der verknüpften Vorlage

Die Hauptvorlage hat den Namen „azuredeploy.json“.

1. Öffnen Sie **azuredeploy.json** in Visual Studio Code, falls die Datei nicht geöffnet ist.
2. Löschen Sie die Ressourcendefinition des Speicherkontos aus der Vorlage:

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. Fügen Sie dort, wo sich die Definition des Speicherkontos befand, den folgenden JSON-Codeausschnitt hinzu:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Berücksichtigen Sie dabei folgende Details:

    * Eine `Microsoft.Resources/deployments`-Ressource in der Hauptvorlage wird zum Verknüpfen mit einer anderen Vorlage verwendet.
    * Die `deployments`-Ressource hat den Namen `linkedTemplate`. Dieser Name wird zum [Konfigurieren der Abhängigkeit](#configure-dependency) verwendet.
    * Beim Aufrufen verknüpfter Vorlagen können Sie nur den Bereitstellungsmodus [Inkrementell](./deployment-modes.md) verwenden.
    * `templateLink/uri` enthält den URI der verknüpften Vorlage. Ersetzen Sie den Wert durch den URI, den Sie beim Hochladen der verknüpften Vorlage (also der Vorlage mit SAS-Token) erhalten.
    * Übergeben Sie mit `parameters` Werte aus der Hauptvorlage an die verknüpfte Vorlage.
4. Vergewissern Sie sich, dass Sie den Wert des `uri`-Elements durch den Wert ersetzt haben, den Sie beim Hochladen der verknüpften Vorlage (also der Vorlage mit SAS-Token) erhalten haben. In der Praxis sollten Sie den URI mit einem Parameter angeben.
5. Speichern Sie die geänderte Vorlage.

## <a name="configure-dependency"></a>Konfigurieren der Abhängigkeit

Wie unter [Tutorial: Erstellen von Azure Resource Manager-Vorlagen mit abhängigen Ressourcen](./resource-manager-tutorial-create-templates-with-dependent-resources.md) beschrieben, hängt die VM-Ressource vom Speicherkonto ab:

![Abhängigkeitsdiagramm für Azure Resource Manager-Vorlagen](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Da das Speicherkonto nun in der verknüpften Vorlage definiert ist, müssen Sie die folgenden zwei Elemente der `Microsoft.Compute/virtualMachines`-Ressource aktualisieren.

* Konfigurieren Sie das `dependOn`-Element neu. Die Definition des Speicherkontos wird in die verknüpfte Vorlage verschoben.
* Konfigurieren Sie das `properties/diagnosticsProfile/bootDiagnostics/storageUri`-Element neu. In [Verknüpfte Vorlage erstellen](#create-the-linked-template) haben Sie einen Ausgabewert hinzugefügt:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Die Hauptvorlage benötigt diesen Wert.

1. Öffnen Sie „azuredeploy.json“ in Visual Studio Code, falls die Datei noch nicht geöffnet ist.
2. Erweitern Sie die VM-Ressourcendefinition, aktualisieren Sie **dependsOn** wie im folgenden Screenshot gezeigt:

    ![Azure Resource Manager: verknüpfte Vorlagen – Abhängigkeit konfigurieren](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* ist der Name der Ressource der Bereitstellungen.
3. Aktualisieren Sie **properties/diagnosticsProfile/bootDiagnostics/storageUri** wie im vorherigen Screenshot dargestellt.
4. Speichern Sie die geänderte Vorlage.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Informationen zum Bereitstellungsverfahren finden Sie im Abschnitt [Bereitstellen der Vorlage](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Verwenden Sie den gleichen Ressourcengruppennamen wie für das Speicherkonto zum Speichern der verknüpften Vorlage. Das erleichtert das Löschen der Ressourcen im nächsten Abschnitt. Verwenden Sie aus Sicherheitsgründen ein generiertes Kennwort für das Administratorkonto des virtuellen Computers. Siehe [Voraussetzungen](#prerequisites).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Wählen Sie den Namen der Ressourcengruppe aus.  Es werden insgesamt sechs Ressourcen in der Ressourcengruppe angezeigt.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="additional-practice"></a>Zusätzliche Übung

Nehmen Sie zur Verbesserung des Projekts die folgenden zusätzlichen Änderungen am abgeschlossenen Projekt vor:

1. Ändern Sie die Hauptvorlage (azuredeploy.json), sodass Sie den URI-Wert der verknüpften Vorlage über einen Parameter akzeptiert.
2. Generieren Sie ein SAS-Token beim Bereitstellen der Hauptvorlage und nicht beim Hochladen der verknüpften Vorlage. Weitere Informationen finden Sie unter [Bereitstellen privater Resource Manager-Vorlagen mit SAS-Token und Azure PowerShell](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Vorlage in eine Hauptvorlage und eine verknüpfte Vorlage modularisiert. Informationen zum Ausführen von Aufgaben nach der Bereitstellung mithilfe von VM-Erweiterungen finden Sie unter

> [!div class="nextstepaction"]
> [Bereitstellen von Erweiterungen für virtuelle Computer](./resource-manager-tutorial-deploy-vm-extensions.md)
