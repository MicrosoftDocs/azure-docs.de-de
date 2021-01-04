---
title: Importieren von SQL-BACPAC-Dateien mit Vorlagen
description: Hier erfahren Sie, wie Sie die Azure SQL-Datenbank-Erweiterung verwenden, um SQL-BACPAC-Dateien mit Azure Resource Manager-Vorlagen (ARM-Vorlagen) zu importieren.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b798e5ceb72ece3989fb81014555f2bc0fea5926
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931400"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Tutorial: Importieren von SQL-BACPAC-Dateien mit ARM-Vorlagen

Hier erfahren Sie, wie Sie die Azure SQL-Datenbank-Erweiterung verwenden, um eine BACPAC-Datei mit Azure Resource Manager-Vorlagen (ARM-Vorlagen) zu importieren. Bereitstellungsartefakte sind sämtliche Dateien, die zusätzlich zu Hauptvorlagendateien für eine Bereitstellung benötigt werden. Die BACPAC-Datei ist ein Artefakt.

In diesem Tutorial erstellen Sie eine Vorlage zum Bereitstellen eines [logischen SQL-Servers](../../azure-sql/database/logical-servers.md) und einer einzelnen Datenbank, und Sie importieren eine BACPAC-Datei. Informationen zum Bereitstellen von Azure-VM-Erweiterungen unter Verwendung von ARM-Vorlagen finden Sie unter [Tutorial: Bereitstellen von VM-Erweiterungen mit ARM-Vorlagen](./template-tutorial-deploy-vm-extensions.md).

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
>
> * Vorbereiten einer BACPAC-Datei
> * Öffnen einer Schnellstartvorlage
> * Bearbeiten der Vorlage
> * Stellen Sie die Vorlage bereit.
> * Überprüfen der Bereitstellung

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

* Visual Studio Code mit der Erweiterung „Azure Resource Manager-Tools“. Weitere Informationen finden Sie unter [Schnellstart: Erstellen von ARM-Vorlagen mit Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).
* Verwenden Sie aus Sicherheitsgründen ein generiertes Kennwort für das Serveradministratorkonto. Sie können das folgende Beispiel zum Generieren eines Kennworts verwenden:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault dient zum Schützen von kryptografischen Schlüsseln und anderen Geheimnissen. Weitere Informationen finden Sie im [Tutorial: Integrieren von Azure Key Vault in Ihre Bereitstellung einer ARM-Vorlage](./template-tutorial-use-key-vault.md). Wir empfehlen Ihnen auch, Ihr Kennwort alle drei Monate zu aktualisieren.

## <a name="prepare-a-bacpac-file"></a>Vorbereiten einer BACPAC-Datei

Auf [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) ist eine BACPAC-Datei verfügbar. Informationen zum Erstellen einer eigenen Datei finden Sie unter [Exportieren einer Datenbank aus Azure SQL-Datenbank in eine BACPAC-Datei](../../azure-sql/database/database-export.md). Wenn Sie die Datei an Ihrem eigenen Standort veröffentlichen möchten, müssen Sie die Vorlage später in diesem Tutorial aktualisieren.

Die BACPAC-Datei muss in einem Azure Storage-Konto gespeichert werden, damit sie mithilfe einer ARM-Vorlage importiert werden kann. Das folgende PowerShell-Skript bereitet die BACPAC-Datei mit den folgenden Schritten vor:

* Herunterladen der BACPAC-Datei
* Erstellen eines Azure-Speicherkontos.
* Erstellen eines Speicherkonto-Blobcontainers
* Hochladen der BACPAC-Datei in den Container
* Zeigen Sie den Speicherkontoschlüssel und die Blob-URL an.

1. Wählen Sie **Ausprobieren** aus, um Cloud Shell zu öffnen. Fügen Sie dann das folgende PowerShell-Skript im Shell-Fenster ein:

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Notieren Sie sich den Speicherkontoschlüssel und die URL der BACPAC-Datei. Sie benötigen diese Werte beim Bereitstellen der Vorlage.

## <a name="open-a-quickstart-template"></a>Öffnen einer Schnellstartvorlage

Die in diesem Tutorial verwendete Vorlage befindet sich auf [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Wählen Sie in Visual Studio Code **Datei** > **Datei öffnen** aus.
1. Fügen Sie in **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Wählen Sie **Öffnen** aus, um die Datei zu öffnen.

    In der Vorlage sind zwei Ressourcen definiert:

   * `Microsoft.Sql/servers`. Informationen finden Sie in der [Vorlagenreferenz](/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Informationen finden Sie in der [Vorlagenreferenz](/azure/templates/microsoft.sql/servers/databases).

        Bevor Sie die Vorlage anpassen, sollten Sie sich zunächst grundlegend damit vertraut machen.
1. Wählen Sie **Datei** > **Speichern unter** aus, um eine Kopie der Datei als *azuredeploy.json* auf dem lokalen Computer zu speichern.

## <a name="edit-the-template"></a>Bearbeiten der Vorlage

1. Fügen Sie am Ende des Abschnitts **parameters** zwei weitere Parameter hinzu, um den Speicherkontoschlüssel und die BACPAC-URL festzulegen:

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    Fügen Sie nach **adminPassword** ein Komma ein. Drücken Sie zum Formatieren der JSON-Datei in Visual Studio Code UMSCHALT+ALT+F.

    Informationen zum Abrufen dieser beiden Werte finden Sie unter [Vorbereiten einer BACPAC-Datei](#prepare-a-bacpac-file).

1. Fügen Sie der Vorlage zwei weitere Ressourcen hinzu.

    * Damit die SQL-Datenbank-Erweiterung BACPAC-Dateien importieren kann, müssen Sie Datenverkehr von Azure-Diensten zulassen. Fügen Sie unterhalb der Serverdefinition die folgende Firewallregeldefinition hinzu:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        Die Vorlage sieht so aus:

        ![Vorlage mit Firewallregeldefinition](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Fügen Sie der Datenbankdefinition mithilfe des folgenden JSON-Codes eine SQL-Datenbank-Erweiterungsressource hinzu:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        Die Vorlage sieht so aus:

        ![Vorlage mit SQL-Datenbank-Erweiterung](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Informationen zur Ressourcendefinition finden Sie in der [Referenz zur SQL-Datenbank-Erweiterung](/azure/templates/microsoft.sql/servers/databases/extensions). Im Anschluss sind einige zentrale Elemente aufgeführt:

        * **dependsOn:** Die Erweiterungsressource muss nach der Erstellung der Datenbank erstellt werden.
        * **storageKeyType:** Geben Sie die Art des zu verwendenden Speicherschlüssels an. Der Wert kann entweder `StorageAccessKey` oder `SharedAccessKey` sein. Verwenden Sie in diesem Tutorial `StorageAccessKey`.
        * **storageKey:** Geben Sie den Schlüssel für das Speicherkonto an, in dem die BACPAC-Datei gespeichert ist. Bei Verwendung des Speicherschlüsseltyps `SharedAccessKey` muss ein Fragezeichen (?) vorangestellt werden.
        * **storageUri:** Geben Sie die URL der in einem Speicherkonto gespeicherten BACPAC-Datei an.
        * **administratorLoginPassword:** Das Kennwort des SQL-Administrators. Verwenden Sie ein generiertes Kennwort. Siehe [Voraussetzungen](#prerequisites).

Die fertige Vorlage sieht folgendermaßen aus:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Informationen zum Bereitstellungsverfahren finden Sie im Abschnitt [Bereitstellen der Vorlage](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Verwenden Sie stattdessen das folgende PowerShell-Bereitstellungsskript:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Verwenden Sie den gleichen Projektnamen wie beim Vorbereiten der BACPAC-Datei, damit alle Ressourcen in der gleichen Ressourcengruppe gespeichert werden. Dies vereinfacht die Verwaltung von Ressourcenaufgaben, etwa das Bereinigen der Ressourcen. Wenn Sie den gleichen Projektnamen verwenden, können Sie entweder den Befehl `New-AzResourceGroup` aus dem Skript entfernen oder mit „y“ (für „Ja“) bzw. „n“ (für „Nein“) antworten, wenn Sie gefragt werden, ob Sie die vorhandene Ressourcengruppe aktualisieren möchten.

Verwenden Sie ein generiertes Kennwort. Siehe [Voraussetzungen](#prerequisites).

## <a name="verify-the-deployment"></a>Überprüfen der Bereitstellung

Wenn Sie über Ihren Clientcomputer auf den Server zugreifen möchten, müssen Sie eine weitere Firewallregel hinzufügen. Weitere Informationen finden Sie unter [IP-Firewallregeln für Azure SQL-Datenbank und Azure SQL Data Warehouse](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules).

Wählen Sie im Azure-Portal die Datenbank aus der neu bereitgestellten Ressourcengruppe aus. Wählen Sie **Abfrage-Editor (Vorschau)** aus, und geben Sie dann die Administratoranmeldeinformationen ein. Wie Sie sehen, wurden zwei Tabellen in die Datenbank importiert:

![Abfrage-Editor (Vorschau)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü **Ressourcengruppe** aus.
1. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
1. Klicken Sie auf den Namen der Ressourcengruppe. Es werden insgesamt sechs Ressourcen in der Ressourcengruppe angezeigt.
1. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Server und eine Datenbank bereitgestellt und eine BACPAC-Datei importiert. Informationen zur Behandlung von Problemen bei der Bereitstellung von Vorlagen finden Sie unter:

> [!div class="nextstepaction"]
> [Problembehandlung bei Bereitstellungen von ARM-Vorlagen](./template-tutorial-troubleshoot.md)
