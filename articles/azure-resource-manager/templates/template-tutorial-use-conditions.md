---
title: Verwenden von Bedingungen in Vorlagen
description: Es wird beschrieben, wie Sie Azure-Ressourcen basierend auf Bedingungen bereitstellen. Hier wird gezeigt, wie Sie entweder eine neue Ressource bereitstellen oder eine vorhandene Ressource verwenden.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1f4e8c0bc6a066e0d82d393474bfc804be5e3fb3
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931366"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Tutorial: Verwenden von Bedingungen in ARM-Vorlagen

Hier wird beschrieben, wie Sie Azure-Ressourcen basierend auf den Bedingungen in einer ARM-Vorlage (Azure Resource Manager-Vorlage) bereitstellen.

Im Tutorial [Festlegen der Reihenfolge für die Ressourcenbereitstellung](./template-tutorial-create-templates-with-dependent-resources.md) erstellen Sie einen virtuellen Computer, ein virtuelles Netzwerk und einige andere abhängige Ressourcen, z.B. ein Speicherkonto. Anstatt jedes Mal ein neues Speicherkonto zu erstellen, können Benutzer zwischen dem Erstellen eines neuen Speicherkontos und dem Verwenden eines vorhandenen Speicherkontos wählen. Sie definieren einen zusätzlichen Parameter, um dieses Ziel zu erreichen. Wenn der Wert des Parameters „new“ lautet, wird ein neues Speicherkonto erstellt. Andernfalls wird ein vorhandenes Speicherkonto mit dem angegebenen Namen verwendet.

![Diagramm der Bedingungen zum Verwenden der Resource Manager-Vorlage](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Öffnen einer Schnellstartvorlage
> * Ändern der Vorlage
> * Bereitstellen der Vorlage
> * Bereinigen von Ressourcen

In diesem Tutorial wird nur ein grundlegendes Szenario der Verwendung von Bedingungen behandelt. Weitere Informationen finden Sie unter

* [Vorlagendateistruktur: Bedingung](conditional-resource-deployment.md).
* [Bedingtes Bereitstellen einer Ressource in einer ARM-Vorlage](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Vorlagenfunktion: If](./template-functions-logical.md#if) (Wenn).
* [Vergleichsfunktionen für ARM-Vorlagen](./template-functions-comparison.md)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

* Visual Studio Code mit der Erweiterung „Azure Resource Manager-Tools“. Weitere Informationen finden Sie unter [Schnellstart: Erstellen von ARM-Vorlagen mit Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Verwenden Sie aus Sicherheitsgründen ein generiertes Kennwort für das Administratorkonto des virtuellen Computers. Hier sehen Sie ein Beispiel für die Kennwortgenerierung:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault dient zum Schützen von kryptografischen Schlüsseln und anderen Geheimnissen. Weitere Informationen finden Sie im [Tutorial: Integrieren von Azure Key Vault in Ihre Bereitstellung einer ARM-Vorlage](./template-tutorial-use-key-vault.md). Wir empfehlen Ihnen auch, Ihr Kennwort alle drei Monate zu aktualisieren.

## <a name="open-a-quickstart-template"></a>Öffnen einer Schnellstartvorlage

„Azure-Schnellstartvorlagen“ ist ein Repository für ARM-Vorlagen. Statt eine Vorlage von Grund auf neu zu erstellen, können Sie eine Beispielvorlage verwenden und diese anpassen. Die in diesem Tutorial verwendete Vorlage heißt [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Bereitstellen eines einfachen virtuellen Windows-Computers).

1. Wählen Sie in Visual Studio Code **Datei**>**Datei öffnen** aus.
1. Fügen Sie in **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Wählen Sie **Öffnen** aus, um die Datei zu öffnen.
1. Es gibt sechs Ressourcen, die von der Vorlage definiert werden:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    Es empfiehlt sich, vor dem Anpassen einer Vorlage die Referenz zu dieser Vorlage zu lesen.

1. Wählen Sie **Datei**>**Speichern unter** aus, um eine Kopie der Datei als **azuredeploy.json** auf dem lokalen Computer zu speichern.

## <a name="modify-the-template"></a>Ändern der Vorlage

Nehmen Sie zwei Änderungen an der vorhandenen Vorlage vor:

* Fügen Sie einen Parameter für den Speicherkontonamen hinzu. Benutzer können entweder einen neuen oder einen vorhandenen Speicherkontonamen angeben.
* Fügen Sie einen neuen Parameter mit dem Namen **newOrExisting** hinzu. Die Bereitstellung nutzt diesen Parameter, um zu ermitteln, ob ein neues Speicherkonto erstellt oder ein vorhandenes Speicherkonto verwendet werden soll.

Hier ist das Verfahren zum Vornehmen von Änderungen angegeben:

1. Öffnen Sie **azuredeploy.json** in Visual Studio Code.
1. Ersetzen Sie die drei **variables('storageAccountName')** in der gesamten Vorlage durch **parameters('storageAccountName')** .
1. Entfernen Sie die folgende Variablendefinition:

    ![Der Screenshot hebt die Variablendefinitionen hervor, die Sie entfernen müssen.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Fügen Sie am Anfang des Parameterabschnitts die folgenden beiden Parameter hinzu:

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

    Drücken Sie **[ALT]+[UMSCHALT]+ F**, um die Vorlage in Visual Studio Code zu formatieren.

    Die aktualisierte Parameterdefinition sieht wie folgt aus:

    ![Resource Manager-Nutzungsbedingung](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Fügen Sie am Anfang der Speicherkontodefinition die folgende Zeile hinzu.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Mit der Bedingung wird der Wert des Parameters **newOrExisting** überprüft. Wenn der Parameterwert **new** lautet, erstellt die Bereitstellung das Speicherkonto.

    Die aktualisierte Speicherkontodefinition sieht wie folgt aus:

    ![Der Screenshot zeigt die aktualisierte Speicherkontodefinition.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Aktualisieren Sie die Eigenschaft **storageUri** der VM-Ressourcendefinition mit dem folgenden Wert:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Diese Änderung ist erforderlich, wenn Sie ein vorhandenes Speicherkonto unter einer anderen Ressourcengruppe verwenden.

1. Speichern Sie die Änderungen.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Melden Sie sich bei [Azure Cloud Shell](https://shell.azure.com) an.

1. Wählen Sie Ihre bevorzugte Umgebung aus, indem Sie links oben **PowerShell** oder **Bash** (für die CLI) auswählen.  Bei einem Wechsel ist ein Neustart der Shell erforderlich.

    ![Azure-Portal, Cloud Shell, Datei hochladen](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Wählen Sie **Dateien hochladen/herunterladen** und dann **Hochladen** aus. Betrachten Sie hierzu den vorherigen Screenshot. Wählen Sie die Datei aus, die Sie im vorherigen Abschnitt gespeichert haben. Nach dem Hochladen der Datei können Sie den Befehl **ls** und den Befehl **cat** verwenden, um zu überprüfen, ob die Datei hochgeladen wurde.

1. Führen Sie das folgende PowerShell-Skript aus, um die Vorlage bereitzustellen.

    > [!IMPORTANT]
    > Der Name des Speicherkontos muss innerhalb von Azure eindeutig sein. Der Name darf nur Kleinbuchstaben und Ziffern enthalten, und er darf maximal 24 Zeichen lang sein. Zur Bildung des Speicherkontonamens wird dem Projektnamen „store“ angehängt. Stellen Sie sicher, dass der Projektname und der generierte Speicherkontoname den Anforderungen an den Speicherkontonamen entsprechen.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > Für die Bereitstellung tritt ein Fehler auf, wenn **newOrExisting** auf **new** festgelegt, aber das Speicherkonto mit dem angegebenen Speicherkontonamen bereits vorhanden ist.

Versuchen Sie, eine andere Bereitstellung zu erstellen, bei der **newOrExisting** auf „existing“ festgelegt ist, und geben Sie ein vorhandenes Speicherkonto an. Informationen zum Erstellen eines Speicherkontos im Voraus finden Sie unter [Speicherkonto erstellen](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen. Um die Ressourcengruppe zu löschen, wählen Sie **Ausprobieren** aus, um die Cloud Shell zu öffnen. Um das PowerShell-Skript einzufügen, klicken Sie mit der rechten Maustaste auf den Shellberich, und wählen Sie **Einfügen** aus.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Vorlage entwickelt, bei der Benutzer zwischen dem Erstellen eines neuen Speicherkontos und dem Verwenden eines vorhandenen Speicherkontos wählen können. Informationen zum Abrufen von Geheimnissen aus Azure Key Vault und Verwenden der Geheimnisse als Kennwörter bei der Vorlagenbereitstellung finden Sie hier:

> [!div class="nextstepaction"]
> [Tutorial: Integrate Azure Key Vault in Resource Manager Template deployment](./template-tutorial-use-key-vault.md) (Tutorial: Integrieren von Azure Key Vault in die Resource Manager-Vorlagenbereitstellung)
