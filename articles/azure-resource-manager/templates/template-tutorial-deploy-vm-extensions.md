---
title: Bereitstellen von VM-Erweiterungen mit einer Vorlage
description: Hier erfahren Sie, wie Sie VM-Erweiterungen mithilfe von Azure Resource Manager-Vorlagen (ARM-Vorlagen) bereitstellen.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49bc1a77e2e25cb069a89812603ff562b8a4c1cd
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931451"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Tutorial: Bereitstellen von VM-Erweiterungen mit ARM-Vorlagen

Hier erfahren Sie, wie Sie [Azure-VM-Erweiterungen](../../virtual-machines/extensions/features-windows.md) verwenden, um nach der Bereitstellung Konfigurations- und Automatisierungsaufgaben für virtuelle Azure-Computer auszuführen. Für die Verwendung mit virtuellen Azure-Computern stehen viele verschiedene VM-Erweiterungen zur Verfügung. In diesem Tutorial stellen Sie eine benutzerdefinierte Skripterweiterung über eine Azure Resource Manager-Vorlage (ARM-Vorlage) bereit, um ein PowerShell-Skript auf einem virtuellen Windows-Computer auszuführen. Das Skript installiert einen Webserver auf dem virtuellen Computer.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Vorbereiten eines PowerShell-Skripts
> * Öffnen einer Schnellstartvorlage
> * Bearbeiten der Vorlage
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

## <a name="prepare-a-powershell-script"></a>Vorbereiten eines PowerShell-Skripts

Sie können ein PowerShell-Inlineskript oder eine Skriptdatei verwenden.  In diesem Tutorial wird die Verwendung einer Skriptdatei erläutert. Ein PowerShell-Skript mit folgendem Inhalt ist auf [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1) verfügbar:

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Wenn Sie die Datei an Ihrem eigenen Standort veröffentlichen möchten, aktualisieren Sie später in diesem Tutorial das Element `fileUri` in der Vorlage.

## <a name="open-a-quickstart-template"></a>Öffnen einer Schnellstartvorlage

„Azure-Schnellstartvorlagen“ ist ein Repository für ARM-Vorlagen. Statt eine Vorlage von Grund auf neu zu erstellen, können Sie eine Beispielvorlage verwenden und diese anpassen. Die in diesem Tutorial verwendete Vorlage heißt [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Bereitstellen eines einfachen virtuellen Windows-Computers).

1. Wählen Sie in Visual Studio Code **Datei** > **Datei öffnen** aus.
1. Fügen Sie im Feld **Dateiname** die folgende URL ein:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Wählen Sie **Öffnen** aus, um die Datei zu öffnen.
    Die Vorlage definiert fünf Ressourcen:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Bevor Sie die Vorlage anpassen, sollten Sie sich zunächst grundlegend damit vertraut machen.

1. Wählen Sie **Datei** > **Speichern unter** aus, um eine Kopie der Datei als *azuredeploy.json* auf dem lokalen Computer zu speichern.

## <a name="edit-the-template"></a>Bearbeiten der Vorlage

Fügen Sie der vorhandenen Vorlage eine VM-Erweiterungsressource mit folgendem Inhalt hinzu:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2019-12-01",
  "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
  "location": "[parameters('location')]",
  "dependsOn": [
      "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
  ],
  "properties": {
      "publisher": "Microsoft.Compute",
      "type": "CustomScriptExtension",
      "typeHandlerVersion": "1.7",
      "autoUpgradeMinorVersion":true,
      "settings": {
        "fileUris": [
          "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
        ],
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
      }
  }
}
```

Weitere Informationen zu dieser Ressourcendefinition finden Sie in der [Erweiterungsreferenz](/azure/templates/microsoft.compute/virtualmachines/extensions). Im Anschluss sind einige zentrale Elemente aufgeführt:

* **name:** Da es sich bei der Erweiterungsressource um eine untergeordnete Ressource des VM-Objekts handelt, muss der Name mit dem VM-Namenspräfix versehen werden. Weitere Informationen finden Sie unter [Festlegen von Name und Typ für untergeordnete Ressourcen](child-resource-name-type.md).
* **dependsOn:** Erstellen Sie die Erweiterungsressource nach der Erstellung des virtuellen Computers.
* **fileUris:** Die Speicherorte der Skriptdateien. Falls Sie sich gegen die Verwendung des angegebenen Speicherorts entscheiden, müssen Sie die Werte aktualisieren.
* **commandToExecute:** Dieser Befehl ruft das Skript auf.

Wenn Sie ein Inlineskript verwenden möchten, entfernen Sie **fileUris**, und aktualisieren Sie **commandToExecute** wie folgt:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Dieses Inlineskript aktualisiert auch den Inhalt der Datei „iisstart.html“.

Außerdem müssen Sie den HTTP-Port öffnen, um auf den Webserver zugreifen zu können.

1. Suchen Sie in der Vorlage nach **securityRules**.
1. Fügen Sie die folgende Regel neben **default-allow-3389** hinzu.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Informationen zum Bereitstellungsverfahren finden Sie im Abschnitt „Bereitstellen der Vorlage“ unter [Tutorial: Erstellen von ARM-Vorlagen mit abhängigen Ressourcen](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Es empfiehlt sich, ein generiertes Kennwort für das Administratorkonto des virtuellen Computers zu verwenden. Informationen finden Sie in diesem Artikel im Abschnitt [Voraussetzungen](#prerequisites).

Rufen Sie mithilfe des folgenden Befehls in Cloud Shell die öffentliche IP-Adresse des virtuellen Computers ab:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Fügen Sie die IP-Adresse in einen Webbrowser ein. Die Standardbegrüßungsseite für IIS (Internet Information Services, Internetinformationsdienste) wird geöffnet:

![Die Begrüßungsseite von Internetinformationsdienste](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die bereitgestellten Azure-Ressourcen nicht mehr benötigen, bereinigen Sie sie, indem Sie die Ressourcengruppe löschen.

1. Klicken Sie im Azure-Portal im linken Bereich auf **Ressourcengruppe**.
2. Geben Sie in das Feld **Nach Name filtern** den Namen der Ressourcengruppe ein.
3. Klicken Sie auf den Namen der Ressourcengruppe.
    Sechs Ressourcen werden in der Ressourcengruppe angezeigt.
4. Wählen Sie im oberen Menü **Ressourcengruppe löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen virtuellen Computer und eine VM-Erweiterung bereitgestellt. Die Erweiterung hat den IIS-Webserver auf dem virtuellen Computer installiert. Informationen zum Importieren einer BACPAC-Datei unter Verwendung der Azure SQL-Datenbank-Erweiterung finden Sie hier:

> [!div class="nextstepaction"]
> [Bereitstellen von SQL-Erweiterungen](./template-tutorial-deploy-sql-extensions-bacpac.md)
