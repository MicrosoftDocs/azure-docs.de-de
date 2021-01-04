---
title: 'Erstellen einer PowerShell-Funktion über die Befehlszeile: Azure Functions'
description: Erfahren Sie, wie Sie eine PowerShell-Funktion über die Befehlszeile erstellen und anschließend das lokale Projekt für serverloses Hosten in Azure Functions veröffentlichen.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
ms.openlocfilehash: 45745419932a40a34abe65f54c61c87ab245f487
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424757"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>Schnellstart: Erstellen einer PowerShell-Funktion über die Befehlszeile in Azure

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In diesem Artikel verwenden Sie Befehlszeilentools zum Erstellen einer PowerShell-Funktion, die auf HTTP-Anforderungen reagiert. Der Code wird lokal getestet und anschließend in der serverlosen Umgebung von Azure Functions bereitgestellt.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

Es gibt auch eine [Visual Studio Code-basierte Version](create-first-function-vs-code-powershell.md) dieses Artikels.

## <a name="configure-your-local-environment"></a>Konfigurieren Ihrer lokalen Umgebung

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.x.

+ Eines der folgenden Tools zum Erstellen von Azure-Ressourcen:

    + [Azure PowerShell](/powershell/azure/install-az-ps), Version 5.0 oder höher.

    + [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).

+ [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

Überprüfen Sie Ihre Voraussetzungen, die davon abhängig sind, ob Sie die Azure CLI oder Azure PowerShell zum Erstellen von Azure-Ressourcen verwenden:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

+ Führen Sie in einem Terminal oder Befehlsfenster `func --version` aus, um zu überprüfen, ob mindestens Version 3.x der Azure Functions Core Tools verwendet wird.

+ Führen Sie `az --version` aus, um zu überprüfen, ob die Version 2.4 oder höher der Azure CLI verwendet wird.

+ Führen Sie `az login` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Führen Sie in einem Terminal oder Befehlsfenster `func --version` aus, um zu überprüfen, ob mindestens Version 3.x der Azure Functions Core Tools verwendet wird.

+ Führen Sie `(Get-Module -ListAvailable Az).Version` aus, und stellen Sie sicher, dass Version 5.0 oder höher ausgeführt wird. 

+ Führen Sie `Connect-AzAccount` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

---

## <a name="create-a-local-function-project"></a>Erstellen eines lokalen Funktionsprojekts

In Azure Functions handelt es sich bei einem Funktionsprojekt um einen Container für eine oder mehrere individuelle Funktionen, die jeweils auf einen bestimmten Trigger reagieren. Für alle Funktionen eines Projekts werden die gleichen lokalen Konfigurationen und Hostkonfigurationen gemeinsam genutzt. In diesem Abschnitt erstellen Sie ein Funktionsprojekt, das nur eine Funktion enthält.

1. Führen Sie den Befehl `func init` wie folgt aus, um in einem Ordner mit dem Namen *LocalFunctionProj* ein Funktionsprojekt mit der angegebenen Runtime zu erstellen:  

    ```console
    func init LocalFunctionProj --powershell
    ```

1. Navigieren Sie zum Projektordner:

    ```console
    cd LocalFunctionProj
    ```
    
    Dieser Ordner enthält verschiedene Dateien für das Projekt, z. B. die Konfigurationsdateien [local.settings.json](functions-run-local.md#local-settings-file) und [host.json](functions-host-json.md). Da *local.settings.json* aus Azure heruntergeladene Geheimnisse enthalten kann, wird die Datei in der *GITIGNORE* -Datei standardmäßig aus der Quellcodeverwaltung ausgeschlossen.
    
1. Fügen Sie dem Projekt über den unten gezeigten Befehl eine Funktion hinzu. Hierbei ist das `--name`-Argument der eindeutige Name Ihrer Funktion (HttpExample), mit dem `--template`-Argument wird der Trigger der Funktion (HTTP) angegeben. 

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    Mit `func new` wird ein Unterordner passend zum Funktionsnamen erstellt. Er enthält eine geeignete Codedatei für die gewählte Sprache des Projekts und eine Konfigurationsdatei mit dem Namen *function.json*.

### <a name="optional-examine-the-file-contents"></a>(Optional) Untersuchen des Dateiinhalts

Bei Bedarf können Sie [Lokales Ausführen der Funktion](#run-the-function-locally) überspringen und den Dateiinhalt später untersuchen.

#### <a name="runps1"></a>run.ps1

*run.ps1* definiert ein Funktionsskript, das gemäß der Konfiguration in *function.json* ausgelöst wird.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Für einen HTTP-Trigger empfängt die Funktion Anforderungsdaten, die an den Parameter `$Request` übergeben werden, der in *function.json* definiert ist. Das Rückgabeobjekt, das in *function.json* als `Response` definiert ist, wird als Antwort an das Cmdlet `Push-OutputBinding` übergeben. 

#### <a name="functionjson"></a>function.json

*function.json* ist eine Konfigurationsdatei, in der die Eingabe- und Ausgabebindungen (`bindings`) für die Funktion, einschließlich Triggertyp, definiert sind. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

Für jede Bindung sind eine Richtung, ein Typ und ein eindeutiger Name erforderlich. Der HTTP-Trigger weist eine Eingabebindung vom Typ [`httpTrigger`](functions-bindings-http-webhook-trigger.md) und eine Ausgabebindung vom Typ [`http`](functions-bindings-http-webhook-output.md) auf.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Erstellen Sie die Funktions-App in Azure:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Der Befehl [az functionapp create](/cli/azure/functionapp#az_functionapp_create) erstellt die Funktions-App in Azure. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Das Cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) erstellt die Funktions-App in Azure. 
    
    ---
    
    Ersetzen Sie im vorherigen Beispiel `<STORAGE_NAME>` durch den Namen des Kontos, das Sie im vorherigen Schritt verwendet haben, und `<APP_NAME>` durch einen global eindeutigen Namen, der für Sie geeignet ist. `<APP_NAME>` ist gleichzeitig die DNS-Standarddomäne für die Funktions-App. 
    
    Mit diesem Befehl wird eine Funktions-App erstellt, für die die von Ihnen angegebene Language Runtime unter dem [Azure Functions-Verbrauchstarif](functions-scale.md#consumption-plan) ausgeführt wird. Dies ist für die Nutzungsmenge, die in diesem Fall anfällt, kostenlos. Darüber hinaus wird mit dem Befehl auch eine zugeordnete Azure Application Insights-Instanz in derselben Ressourcengruppe bereitgestellt, mit der Sie Ihre Funktions-App überwachen und Protokolle anzeigen können. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md). Für die Instanz fallen erst Kosten an, wenn Sie sie aktivieren.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange]

[Herstellen einer Verbindung mit einer Azure Storage-Warteschlange]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell
