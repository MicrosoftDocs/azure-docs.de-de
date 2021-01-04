---
title: Erstellen Ihrer ersten Funktion mithilfe von Azure Resource Manager-Vorlagen
description: Erstellen und Bereitstellen einer einfachen, mittels HTTP ausgelösten serverlosen Funktion unter Verwendung einer Azure Resource Manager-Vorlage (ARM-Vorlage).
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: 1e623405faa89ff41eccdaa57578bc8ac94cd78c
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422823"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>Schnellstart: Erstellen und Bereitstellen von Azure Functions-Ressourcen aus einer ARM-Vorlage

In diesem Artikel verwenden Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage), um eine Funktion zu erstellen, die auf HTTP-Anforderungen reagiert. 

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-account"></a>Azure-Konto 

Bevor Sie beginnen, müssen Sie über ein Azure-Konto mit einem aktiven Abonnement verfügen. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).

### <a name="create-a-local-functions-project"></a>Erstellen eines lokalen Funktionsprojekts

Für diesen Artikel muss ein lokales Funktionscodeprojekt mit den Azure-Ressourcen, die Sie erstellen, ausgeführt werden. Wenn Sie nicht zuerst ein Projekt für die Veröffentlichung erstellen, können Sie den Bereitstellungsabschnitt in diesem Artikel nicht ausführen. 

Wählen Sie eine der folgenden Registerkarten aus, folgen Sie dem Link, und führen Sie den Abschnitt aus, um eine Funktions-App in der Sprache Ihrer Wahl zu erstellen:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Erstellen des lokalen Funktionsprojekts in Ihrer ausgewählten Sprache in Visual Studio Code:  

+ [C#](create-first-function-vs-code-csharp.md)
+ [Java](create-first-function-vs-code-java.md)
+ [JavaScript](create-first-function-vs-code-node.md)
+ [PowerShell](create-first-function-vs-code-powershell.md)
+ [Python](create-first-function-vs-code-python.md)
+ [TypeScript](create-first-function-vs-code-typescript.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Erstellen Ihres lokalen Funktionsprojekts in Visual Studio](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[Befehlszeile](#tab/command-line)

Erstellen Ihres lokalen Funktionsprojekts in Ihrer ausgewählten Sprache über die Befehlszeile:

+ [C#](create-first-function-cli-csharp.md)
+ [Java](create-first-function-cli-java.md)
+ [JavaScript](create-first-function-cli-node.md)
+ [PowerShell](create-first-function-cli-powershell.md)
+ [Python](create-first-function-cli-python.md)
+ [TypeScript](create-first-function-cli-typescript.md)

---

Nachdem Sie Ihr Projekt lokal erstellt haben, erstellen Sie die Ressourcen, die für die Ausführung Ihrer neuen Funktion in Azure erforderlich sind. 

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/).

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

Die folgenden vier Azure-Ressourcen werden von dieser Vorlage erstellt:

+ [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): Erstellen eines für Functions erforderlichen Azure Storage-Kontos.
+ [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): Erstellen eines serverlosen Verbrauchshostingplans für die Funktions-App.
+ [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): Erstellen einer Funktions-App.
+ [**microsoft.insights/components**](/azure/templates/microsoft.insights/components): Erstellen einer Application Insights-Instanz für die Überwachung.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Als Nächstes überprüfen Sie die Hostingressourcen der Funktions-App, die Sie erstellt haben, indem Sie Ihr Projekt in Azure veröffentlichen und den HTTP-Endpunkt der Funktion aufrufen.

### <a name="publish-the-function-project-to-azure"></a>Veröffentlichen des Funktionsprojekts in Azure

Verwenden Sie die folgenden Schritte, um Ihr Projekt in den neuen Azure-Ressourcen zu veröffentlichen:

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

Kopieren Sie in der Ausgabe die URL des HTTP-Triggers. Mit dieser testen Sie die Ausführung Ihrer Funktion in Azure. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Wählen Sie in **Veröffentlichungsziel auswählen** die Option **Azure Functions-Verbrauch** mit **Vorhandenen auswählen** , und wählen Sie dann **Profil erstellen** aus.

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="Vorhandenes Veröffentlichungsziel auswählen":::

1. Wählen Sie Ihr **Abonnement** aus, erweitern Sie die Ressourcengruppe, wählen Sie Ihre Funktions-App aus, und wählen Sie **OK** aus.

1. Nachdem die Veröffentlichung abgeschlossen ist, kopieren Sie die **Site-URL**.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="Kopieren der Site-URL aus der Veröffentlichungszusammenfassung":::

1. Fügen Sie den Pfad `/api/<FUNCTION_NAME>?name=Functions` an, wobei `<FUNCTION_NAME>` der Name Ihrer Funktion ist. Die URL, über die Ihre Funktion mit HTTP-Trigger aufgerufen wird, hat das folgende Format:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

Mit dieser URL testen Sie die Ausführung Ihrer HTTP-Triggerfunktion in Azure.

# <a name="command-line"></a>[Befehlszeile](#tab/command-line)

Um Ihren lokalen Code in einer Funktions-App in Azure zu veröffentlichen, verwenden Sie den `publish`-Befehl:

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

In diesem Beispiel ersetzen Sie `<FUNCTION_APP_NAME>` durch den Namen Ihrer Funktions-App. Sie müssen sich möglicherweise unter Verwendung von `az login` erneut anmelden. 

Kopieren Sie in der Ausgabe die URL des HTTP-Triggers. Mit dieser testen Sie die Ausführung Ihrer Funktion in Azure.

---

### <a name="invoke-the-function-on-azure"></a>Aufrufen der Funktion in Azure

Fügen Sie die URL, die Sie für die HTTP-Anforderung kopiert haben, in die Adressleiste Ihres Browsers ein, stellen Sie sicher, dass die Abfragezeichenfolge `name` als `?name=Functions` am Ende dieser URL angefügt wurde, und führen Sie dann die Anforderung aus. 

Sie sollten in etwa folgende Rückgabe erhalten:

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Gehen Sie wie folgt vor, falls Sie mit dem nächsten Schritt fortfahren möchten: Fügen Sie eine Ausgabebindung für die Azure Storage-Warteschlange hinzu, und behalten Sie alle Ressourcen bei, weil der nächste Schritt auf der bisherigen Arbeit aufbaut.

Verwenden Sie andernfalls den unten angegebenen Befehl, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen, damit keine weiteren Kosten anfallen.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

Ersetzen Sie `<RESOURCE_GROUP_NAME>` durch den Namen Ihrer Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun Ihre erste Funktion veröffentlicht haben, erfahren Sie mehr, indem Sie Ihrer Funktion eine Ausgabebindung hinzufügen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[Befehlszeile](#tab/command-line)

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-cli.md)

---
