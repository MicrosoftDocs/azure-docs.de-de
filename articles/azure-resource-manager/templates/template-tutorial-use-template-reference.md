---
title: Verwenden der Vorlagenreferenz
description: Hier erfahren Sie, wie Sie die Referenz zu Azure Resource Manager-Vorlagen (ARM-Vorlagen) verwenden, um eine Vorlage zu erstellen.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: dfd6311fab8d9c65fa7c82d2f707ac96549a32a9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931417"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Tutorial: Verwenden der Referenz für ARM-Vorlagen

Hier erfahren Sie, wie Sie die Vorlagenschemainformationen ermitteln und basierend darauf ARM-Vorlagen (Azure Resource Manager-Vorlagen) erstellen.

In diesem Tutorial verwenden Sie eine Basisvorlage aus Azure-Schnellstartvorlagen. Sie nutzen die Referenzdokumentation für Vorlagen, um die Vorlage anzupassen.

![Referenz zu Resource Manager-Vorlagen: Bereitstellen eines Speicherkontos](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Öffnen einer Schnellstartvorlage
> * Kennenlernen der Vorlage
> * Suchen der Vorlagenreferenz
> * Bearbeiten der Vorlage
> * Bereitstellen der Vorlage

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

* Visual Studio Code mit der Erweiterung „Azure Resource Manager-Tools“. Weitere Informationen finden Sie unter [Schnellstart: Erstellen von ARM-Vorlagen mit Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Öffnen einer Schnellstartvorlage

[Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/) ist ein Repository für ARM-Vorlagen. Statt eine Vorlage von Grund auf neu zu erstellen, können Sie eine Beispielvorlage verwenden und diese anpassen. Die in dieser Schnellstartanleitung verwendete Vorlage heißt [Standardspeicherkonto erstellen](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Die Vorlage definiert eine Azure Storage-Kontoressource.

1. Wählen Sie in Visual Studio Code **Datei**>**Datei öffnen** aus.
1. Fügen Sie in **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Wählen Sie **Öffnen** aus, um die Datei zu öffnen.
1. Wählen Sie **Datei**>**Speichern unter** aus, um die Datei als **azuredeploy.json** auf dem lokalen Computer zu speichern.

## <a name="understand-the-schema"></a>Grundlegendes zum Schema

1. Reduzieren Sie in VS Code die Vorlage auf die Stammebene. Sie besitzen die einfachste Struktur mit den folgenden Elementen:

    ![Einfachste Struktur der Resource Manager-Vorlage](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: Geben Sie den Speicherort der JSON-Schemadatei an, die die Version der Vorlagensprache beschreibt.
    * **contentVersion**: Geben Sie einen beliebigen Wert für dieses Element an, um wichtige Änderungen an der Vorlage zu dokumentieren.
    * **parameters**: Geben Sie die Werte an, die bei der Bereitstellung angegeben werden, um die Bereitstellung der Ressourcen anzupassen.
    * **variables**: Geben Sie die Werte an, die als JSON-Fragmente in der Vorlage verwendet werden, um Vorlagensprachausdrücke zu vereinfachen.
    * **resources**: Geben Sie die Ressourcentypen an, die in einer Ressourcengruppe bereitgestellt oder aktualisiert werden.
    * **outputs**: Geben Sie die Werte an, die nach der Bereitstellung zurückgegeben werden.

1. Erweitern Sie **Ressourcen**. Dort ist eine Ressource vom Typ `Microsoft.Storage/storageAccounts` definiert. Der SKU-Name verfügt über einen Parameterwert.  Der Parameter heißt **storageAccountType**.

    ![Resource Manager-Vorlage, Speicherkontodefinition](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Erweitern Sie **parameters**, um zu sehen, wie **storageAccountType** definiert ist. Für den Parameter gibt es vier mögliche Werte. Sie ermitteln die anderen zulässigen Werte und ändern dann die Parameterdefinition.

    ![Resource Manager-Vorlage: Speicherkontoressourcen-SKUs](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Suchen der Vorlagenreferenz

1. Navigieren Sie zu [Azure-Vorlagenreferenz](/azure/templates/).
1. Geben Sie im Feld **Nach Titel filtern** den Text **Speicherkonten** ein, und wählen Sie unter **Referenz > Storage** die ersten **Speicherkonten** aus.

    ![Resource Manager: Vorlagenreferenz -> Speicherkonto](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Ein Ressourcenanbieter verfügt normalerweise über mehrere API-Versionen:

    ![Referenz zu Resource Manager-Vorlagen: Speicherkontoversionen](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Wählen Sie im linken Bereich unter **Storage** die Option **Alle Ressourcen** aus. Auf dieser Seite sind die Ressourcentypen und Versionen des Speicherressourcenanbieters aufgelistet. Wir empfehlen Ihnen, für die in Ihrer Vorlage definierten Ressourcentypen die aktuellen API-Versionen zu verwenden.

    ![Referenz zu Resource Manager-Vorlagen: Speicherkontotypen/-versionen](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Wählen Sie die aktuelle Version des Ressourcentyps **storageAccount** aus. Zum Zeitpunkt der Erstellung dieses Artikels ist **2019-06-01** die aktuelle Version. Stellen Sie sicher, dass diese Version der Version entspricht, die für die Speicherkontoressource in Ihrer Vorlage verwendet wird. Vergewissern Sie sich beim Aktualisieren der API-Version, dass die Ressourcendefinition der Vorlagenreferenz entspricht.

1. Auf dieser Seite werden die Details des Ressourcentyps „storageAccount“ aufgelistet.  Beispielsweise sind die zulässigen Werte für das **Sku**-Objekt aufgeführt. Es sind mehr SKUs vorhanden, als für die weiter oben geöffnete Schnellstartvorlage aufgeführt wurden. Sie können die Schnellstartvorlage so anpassen, dass sie alle verfügbaren Speichertypen enthält.

    ![Referenz zu Resource Manager-Vorlagen: Speicherkonto-SKUs](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Bearbeiten der Vorlage

Fügen Sie über Visual Studio Code wie im folgenden Screenshot die zusätzlichen Speicherkontotypen hinzu:

![Resource Manager-Vorlage: Speicherkontoressourcen](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Melden Sie sich bei [Azure Cloud Shell](https://shell.azure.com) an.

1. Wählen Sie Ihre bevorzugte Umgebung aus, indem Sie links oben **PowerShell** oder **Bash** (für die CLI) auswählen.  Bei einem Wechsel ist ein Neustart der Shell erforderlich.

    ![Azure-Portal, Cloud Shell, Datei hochladen](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Wählen Sie **Dateien hochladen/herunterladen** und dann **Hochladen** aus. Betrachten Sie hierzu den vorherigen Screenshot. Wählen Sie die Datei aus, die Sie im vorherigen Abschnitt gespeichert haben. Nach dem Hochladen der Datei können Sie den Befehl **ls** und den Befehl **cat** verwenden, um zu überprüfen, ob die Datei hochgeladen wurde.

1. Führen Sie in Cloud Shell die folgenden Befehle aus. Klicken Sie auf die Registerkarte, um den PowerShell-Code oder den CLI-Code anzuzeigen.

    # <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

 Geben Sie beim Bereitstellen der Vorlage den Parameter **storageAccountType** mit einem neu hinzugefügten Wert an, z. B. **Standard_RAGRS**. Die Bereitstellung ist nicht erfolgreich, wenn Sie die ursprüngliche Schnellstartvorlage verwenden, weil **Standard_RAGRS** kein zulässiger Wert ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.  Es werden insgesamt sechs Ressourcen in der Ressourcengruppe angezeigt.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie die Vorlagenreferenz zum Anpassen einer vorhandenen Vorlage verwenden. Wie Sie mehrere Speicherkontoinstanzen erstellen, erfahren Sie unter:

> [!div class="nextstepaction"]
> [Erstellen mehrerer Instanzen](./template-tutorial-create-multiple-instances.md)
