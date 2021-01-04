---
title: Vorlage mit abhängigen Ressourcen
description: Hier erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) mit mehreren Ressourcen erstellen und über das Azure-Portal bereitstellen.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a43fa12e72484e97b828648cd7d610f5cf15ea4e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931587"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Tutorial: Erstellen von ARM-Vorlagen mit abhängigen Ressourcen

Hier erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) erstellen, um mehrere Ressourcen bereitzustellen und die Bereitstellungsreihenfolge zu konfigurieren. Nach der Erstellung der Vorlage stellen Sie sie mithilfe der Cloud Shell über das Azure-Portal bereit.

In diesem Tutorial erstellen Sie ein Speicherkonto, eine VM, ein virtuelles Netzwerk und einige andere abhängigen Ressourcen. Einige der Ressourcen können erst bereitgestellt werden, wenn eine andere Ressource vorhanden ist. Sie können den virtuellen Computer beispielsweise erst erstellen, wenn sein Speicherkonto und seine Netzwerkschnittstelle vorhanden sind. Diese Beziehung definieren Sie, indem Sie eine Ressource von den anderen Ressourcen abhängig machen. Resource Manager wertet die Abhängigkeiten zwischen den Ressourcen aus und stellt sie in der Reihenfolge ihrer Abhängigkeiten bereit. Wenn Ressourcen nicht voneinander abhängig sind, stellt Resource Manager sie parallel bereit. Weitere Informationen finden Sie unter [Definieren der Reihenfolge für die Bereitstellung von Ressourcen in ARM-Vorlagen](./define-resource-dependency.md).

![Diagramm mit der Bereitstellungsreihenfolge von Ressourcen, die von der Resource Manager-Vorlage abhängig sind](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Öffnen einer Schnellstartvorlage
> * Untersuchen der Vorlage
> * Bereitstellen der Vorlage

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
2. Fügen Sie in **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Wählen Sie **Öffnen** aus, um die Datei zu öffnen.
4. Wählen Sie **Datei**>**Speichern unter** aus, um eine Kopie der Datei als **azuredeploy.json** auf dem lokalen Computer zu speichern.

## <a name="explore-the-template"></a>Untersuchen der Vorlage

Sehen Sie sich die Vorlage in diesem Abschnitt an, und versuchen Sie, die folgenden Fragen zu beantworten:

* Wie viele Azure-Ressourcen sind in dieser Vorlage definiert?
* Bei einer der Ressourcen handelt es sich um ein Azure-Speicherkonto.  Sieht die Definition wie die Definition im letzten Tutorial aus?
* Finden Sie die Vorlagenreferenzen für die in dieser Vorlage definierten Ressourcen?
* Finden Sie die Abhängigkeiten der Ressourcen?

1. Reduzieren Sie in Visual Studio Code die Elemente, bis unter **resources** nur die Elemente der ersten und zweiten Ebene angezeigt werden:

    ![ARM-Vorlagen in Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Es gibt sechs Ressourcen, die von der Vorlage definiert werden:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Es empfiehlt sich, vor dem Anpassen einer Vorlage die Referenz zu dieser Vorlage zu lesen.

1. Erweitern Sie die erste Ressource. Es handelt sich um ein Speicherkonto. Vergleichen Sie die Ressourcendefinition mit der [Vorlagenreferenz](/azure/templates/Microsoft.Storage/storageAccounts).

    ![ARM-Vorlagen in Visual Studio Code: Speicherkontodefinition](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Erweitern Sie die zweite Ressource. Der Ressourcentyp lautet `Microsoft.Network/publicIPAddresses`. Vergleichen Sie die Ressourcendefinition mit der [Vorlagenreferenz](/azure/templates/microsoft.network/publicipaddresses).

    ![ARM-Vorlagen in Visual Studio Code: Definition der öffentlichen IP-Adresse](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Erweitern Sie die dritte Ressource. Der Ressourcentyp lautet `Microsoft.Network/networkSecurityGroups`. Vergleichen Sie die Ressourcendefinition mit der [Vorlagenreferenz](/azure/templates/microsoft.network/networksecuritygroups).

    ![ARM-Vorlagen in Visual Studio Code: Definition der Netzwerksicherheitsgruppe](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Erweitern Sie die vierte Ressource. Der Ressourcentyp lautet `Microsoft.Network/virtualNetworks`:

    ![ARM-Vorlagen in Visual Studio Code: „dependsOn“ für virtuelles Netzwerk](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    Das Element „dependsOn“ bietet die Möglichkeit, eine Ressource als von einer oder mehreren Ressourcen abhängig zu definieren. Diese Ressource hängt von einer weiteren Ressource ab:

    * `Microsoft.Network/networkSecurityGroups`

1. Erweitern Sie die fünfte Ressource. Der Ressourcentyp lautet `Microsoft.Network/networkInterfaces`. Die Ressource hängt von zwei weiteren Ressourcen ab:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Erweitern Sie die sechste Ressource. Diese Ressource ist ein virtueller Computer. Sie hängt von zwei weiteren Ressourcen ab:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Das folgende Diagramm veranschaulicht die Ressourcen und die Abhängigkeitsinformationen für diese Vorlage:

![Abhängigkeitsdiagramm für ARM-Vorlagen in Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Durch die Angabe der Abhängigkeiten wird die Lösung von Resource Manager effizient bereitgestellt. Mit Resource Manager werden das Speicherkonto, die öffentliche IP-Adresse und das virtuelle Netzwerk parallel bereitgestellt, da sie über keine Abhängigkeiten verfügen. Nach der Bereitstellung der öffentlichen IP-Adresse und des virtuellen Netzwerks wird die Netzwerkschnittstelle erstellt. Wenn alle anderen Ressourcen bereitgestellt wurden, stellt Resource Manager den virtuellen Computer bereit.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Melden Sie sich bei [Azure Cloud Shell](https://shell.azure.com) an.

1. Wählen Sie Ihre bevorzugte Umgebung aus, indem Sie links oben **PowerShell** oder **Bash** (für die CLI) auswählen.  Bei einem Wechsel ist ein Neustart der Shell erforderlich.

    ![Azure-Portal, Cloud Shell, Datei hochladen](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Wählen Sie **Dateien hochladen/herunterladen** und dann **Hochladen** aus. Betrachten Sie hierzu den vorherigen Screenshot. Wählen Sie die Datei aus, die Sie zuvor gespeichert haben. Nach dem Hochladen der Datei können Sie den Befehl **ls** und den Befehl **cat** verwenden, um zu überprüfen, ob die Datei hochgeladen wurde.

1. Führen Sie das folgende PowerShell-Skript aus, um die Vorlage bereitzustellen.

    # <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

1. Stellen Sie per RDP eine Verbindung mit dem virtuellen Computer her, um sich zu vergewissern, dass der virtuelle Computer erstellt wurde.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe. Es werden insgesamt sechs Ressourcen in der Ressourcengruppe angezeigt.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Vorlage entwickelt und bereitgestellt, um einen virtuellen Computer, ein virtuelles Netzwerk und die abhängigen Ressourcen zu erstellen. Informationen zur Verwendung von Bereitstellungsskripts zum Ausführen von Vorgängen vor bzw. nach der Bereitstellung finden Sie in dem folgenden Artikel:

> [!div class="nextstepaction"]
> [Tutorial: Verwenden von Bereitstellungsskripts zum Erstellen eines selbstsignierten Zertifikats (Vorschau)](./template-tutorial-deployment-script.md)
