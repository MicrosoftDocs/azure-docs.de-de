---
title: 'Erstellen einer TypeScript-Funktion über die Befehlszeile: Azure Functions'
description: Erfahren Sie, wie Sie eine TypeScript-Funktion über die Befehlszeile erstellen und anschließend das lokale Projekt für das serverlose Hosten in Azure Functions veröffentlichen.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 981c96bb8775a3fdd3f951d079cd7ad285d09680
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637029"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>Schnellstart: Erstellen einer TypeScript-Funktion über die Befehlszeile in Azure

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In diesem Artikel verwenden Sie Befehlszeilentools zum Erstellen einer TypeScript-Funktion, die auf HTTP-Anforderungen reagiert. Der Code wird lokal getestet und anschließend in der serverlosen Umgebung von Azure Functions bereitgestellt.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

Es gibt auch eine [Visual Studio Code-basierte Version](create-first-function-vs-code-typescript.md) dieses Artikels.

## <a name="configure-your-local-environment"></a>Konfigurieren Ihrer lokalen Umgebung

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.x.

+ Eines der folgenden Tools zum Erstellen von Azure-Ressourcen:

    + [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).

    + [Azure PowerShell](/powershell/azure/install-az-ps), Version 5.0 oder höher.

+ [Node.js](https://nodejs.org/), Active LTS- und Maintenance LTS-Versionen (8.11.1 und 10.14.1 empfohlen)

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
    func init LocalFunctionProj --typescript
    ```

1. Navigieren Sie zum Projektordner:

    ```console
    cd LocalFunctionProj
    ```
    
    Dieser Ordner enthält verschiedene Dateien für das Projekt, z. B. die Konfigurationsdateien [local.settings.json](functions-run-local.md#local-settings-file) und [host.json](functions-host-json.md). Da *local.settings.json* aus Azure heruntergeladene Geheimnisse enthalten kann, wird die Datei in der *GITIGNORE*-Datei standardmäßig aus der Quellcodeverwaltung ausgeschlossen.

1. Fügen Sie dem Projekt über den unten gezeigten Befehl eine Funktion hinzu. Hierbei ist das `--name`-Argument der eindeutige Name Ihrer Funktion (HttpExample), mit dem `--template`-Argument wird der Trigger der Funktion (HTTP) angegeben.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    
    Mit `func new` wird ein Unterordner passend zum Funktionsnamen erstellt. Er enthält eine geeignete Codedatei für die gewählte Sprache des Projekts und eine Konfigurationsdatei mit dem Namen *function.json*.

### <a name="optional-examine-the-file-contents"></a>(Optional) Untersuchen des Dateiinhalts

Bei Bedarf können Sie [Lokales Ausführen der Funktion](#run-the-function-locally) überspringen und den Dateiinhalt später untersuchen.

#### <a name="indexts"></a>index.ts

*index.ts* exportiert eine Funktion, die gemäß der Konfiguration in *function.json* ausgelöst wird.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

Für einen HTTP-Trigger empfängt die Funktion Anforderungsdaten in der Variablen `req` vom Typ **HttpRequest**, wie dies in *function.json* definiert ist. Das Rückgabeobjekt, das in *function.json* als `$return` definiert ist, ist die Antwort. 

#### <a name="functionjson"></a>function.json

*function.json* ist eine Konfigurationsdatei, in der die Eingabe- und Ausgabebindungen (`bindings`) für die Funktion, einschließlich Triggertyp, definiert sind. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Für jede Bindung sind eine Richtung, ein Typ und ein eindeutiger Name erforderlich. Der HTTP-Trigger weist eine Eingabebindung vom Typ [`httpTrigger`](functions-bindings-http-webhook-trigger.md) und eine Ausgabebindung vom Typ [`http`](functions-bindings-http-webhook-output.md) auf.

## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

1. Führen Sie Ihre Funktion aus, indem Sie den lokalen Azure Functions-Runtimehost im Ordner *LocalFunctionProj* starten:

    ```console
    npm install
    npm start
    ```
    
    Gegen Ende der Ausgabe sollten die folgenden Zeilen angezeigt werden:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Sollte „HttpExample“ nicht wie unten dargestellt angezeigt werden, haben Sie den Host wahrscheinlich außerhalb des Stammordners des Projekts gestartet. Drücken Sie in diesem Fall **STRG**+**C**, um den Host zu beenden. Navigieren Sie anschließend zum Stammordner des Projekts, und führen Sie den vorherigen Befehl erneut aus.

1. Kopieren Sie die URL Ihrer `HttpExample`-Funktion aus dieser Ausgabe in einen Browser, und fügen Sie die Abfragezeichenfolge `?name=<your-name>` an. Die vollständige URL lautet dann wie folgt: `http://localhost:7071/api/HttpExample?name=Functions`. Im Browser sollte eine Meldung wie `Hello Functions` angezeigt werden:

    ![Ergebnis der lokal im Browser ausgeführten Funktion](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Im Terminal, in dem Sie Ihr Projekt gestartet haben, wird beim Senden von Anforderungen auch die Protokollausgabe angezeigt.

1. Wenn Sie so weit sind, drücken Sie **STRG**+**C**, und wählen Sie `y` aus, um den Funktionshost zu beenden.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Erstellen Sie die Funktions-App in Azure:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Der Befehl [az functionapp create](/cli/azure/functionapp#az_functionapp_create) erstellt die Funktions-App in Azure. Ändern Sie bei Verwendung von Node.js 10 auch `--runtime-version` in `10`.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Das Cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) erstellt die Funktions-App in Azure. Wenn Sie Node.js 10 verwenden, ändern Sie `-RuntimeVersion` in `10`.

    ---
        
    Ersetzen Sie im vorherigen Beispiel `<STORAGE_NAME>` durch den Namen des Kontos, das Sie im vorherigen Schritt verwendet haben, und `<APP_NAME>` durch einen global eindeutigen Namen, der für Sie geeignet ist. `<APP_NAME>` ist gleichzeitig die DNS-Standarddomäne für die Funktions-App. 
    
    Mit diesem Befehl wird eine Funktions-App erstellt, für die die von Ihnen angegebene Language Runtime unter dem [Azure Functions-Verbrauchstarif](functions-scale.md#consumption-plan) ausgeführt wird. Dies ist für die Nutzungsmenge, die in diesem Fall anfällt, kostenlos. Darüber hinaus wird mit dem Befehl auch eine zugeordnete Azure Application Insights-Instanz in derselben Ressourcengruppe bereitgestellt, mit der Sie Ihre Funktions-App überwachen und Protokolle anzeigen können. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md). Für die Instanz fallen erst Kosten an, wenn Sie sie aktivieren.

## <a name="deploy-the-function-project-to-azure"></a>Bereitstellen des Funktionsprojekts in Azure

Bevor Sie Core Tools zum Bereitstellen Ihres Projekts in Azure verwenden, erstellen Sie aus den TypeScript-Quelldateien einen produktionsbereiten Build aus JavaScript-Dateien.

1. Verwenden Sieden folgenden Befehl, um Ihr TypeScript-Projekt für die Bereitstellung vorzubereiten:

    ```console
    npm run build:production 
    ```

1. Da nun die benötigten Ressourcen vorhanden sind, können Sie Ihr lokales Funktionsprojekt für die Funktions-App in Azure bereitstellen, indem Sie den Befehl [func azure functionapp publish](functions-run-local.md#project-file-deployment) verwenden. Ersetzen Sie im folgenden Beispiel `<APP_NAME>` durch den Namen Ihrer App.

    ```console
    func azure functionapp publish <APP_NAME>
    ```
    
    Wenn ein Fehler der Art „App mit dem Namen ... kann nicht gefunden werden“ angezeigt wird, sollten Sie einige Sekunden warten und es dann erneut versuchen. Es kann sein, dass Azure die App nach dem vorherigen `az functionapp create`-Befehl nicht richtig initialisiert hat.
    
    Mit dem Befehl zum Veröffentlichen (publish) werden Ergebnisse wie in der folgenden (gekürzten) Ausgabe angezeigt:
    
    <pre>
    ...
    
    Getting site publishing info...
    Creating archive for current directory...
    Performing remote build for functions project.
    
    ...
    
    Deployment successful.
    Remote build succeeded!
    Syncing triggers...
    Functions in msdocs-azurefunctions-qs:
        HttpExample - [httpTrigger]
            Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
    </pre>

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)
