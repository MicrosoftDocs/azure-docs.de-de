---
title: 'Erstellen einer C#-Funktion über die Befehlszeile: Azure Functions'
description: Es wird beschrieben, wie Sie eine C#-Funktion über die Befehlszeile erstellen und anschließend das lokale Projekt für serverloses Hosten in Azure Functions veröffentlichen.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ms.openlocfilehash: b87408669dbb9268d6e3cd47b410b888ef1a19c2
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637148"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Schnellstart: Erstellen einer C#-Funktion über die Befehlszeile in Azure

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In diesem Artikel wird mithilfe von Befehlszeilentools eine auf der C#-Klassenbibliothek basierende Funktion erstellt, die auf HTTP-Anforderungen reagiert. Der Code wird lokal getestet und anschließend in der serverlosen Umgebung von Azure Functions bereitgestellt.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

Es gibt auch eine [Visual Studio Code-basierte Version](create-first-function-vs-code-csharp.md) dieses Artikels.

## <a name="configure-your-local-environment"></a>Konfigurieren Ihrer lokalen Umgebung

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.x.

+ Eines der folgenden Tools zum Erstellen von Azure-Ressourcen:

    + [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).

    + [Azure PowerShell](/powershell/azure/install-az-ps), Version 5.0 oder höher.

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

Überprüfen Sie Ihre Voraussetzungen, die davon abhängig sind, ob Sie die Azure CLI oder Azure PowerShell zum Erstellen von Azure-Ressourcen verwenden:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

+ Führen Sie in einem Terminal oder Befehlsfenster `func --version` aus, um zu überprüfen, ob mindestens Version 3.x der Azure Functions Core Tools verwendet wird.

+ Führen Sie `az --version` aus, um zu überprüfen, ob die Version 2.4 oder höher der Azure CLI verwendet wird.

+ Führen Sie `az login` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

+ Führen Sie `dotnet --list-sdks` aus, um zu überprüfen, ob Version 3.1.x des .NET Core SDK installiert ist.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Führen Sie in einem Terminal oder Befehlsfenster `func --version` aus, um zu überprüfen, ob mindestens Version 3.x der Azure Functions Core Tools verwendet wird.

+ Führen Sie `(Get-Module -ListAvailable Az).Version` aus, und stellen Sie sicher, dass Version 5.0 oder höher ausgeführt wird. 

+ Führen Sie `Connect-AzAccount` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

+ Führen Sie `dotnet --list-sdks` aus, um zu überprüfen, ob Version 3.1.x des .NET Core SDK installiert ist.

---

## <a name="create-a-local-function-project"></a>Erstellen eines lokalen Funktionsprojekts

In Azure Functions handelt es sich bei einem Funktionsprojekt um einen Container für eine oder mehrere individuelle Funktionen, die jeweils auf einen bestimmten Trigger reagieren. Für alle Funktionen eines Projekts werden die gleichen lokalen Konfigurationen und Hostkonfigurationen gemeinsam genutzt. In diesem Abschnitt erstellen Sie ein Funktionsprojekt, das nur eine Funktion enthält.

1. Führen Sie den Befehl `func init` wie folgt aus, um in einem Ordner mit dem Namen *LocalFunctionProj* ein Funktionsprojekt mit der angegebenen Runtime zu erstellen:  

    ```csharp
    func init LocalFunctionProj --dotnet
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

    `func new` erstellt die Codedatei „HttpExample.cs“.

### <a name="optional-examine-the-file-contents"></a>(Optional) Untersuchen des Dateiinhalts

Bei Bedarf können Sie [Lokales Ausführen der Funktion](#run-the-function-locally) überspringen und den Dateiinhalt später untersuchen.

#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* enthält eine `Run`-Methode, die Anforderungsdaten über die Variable `req` empfängt. Hier ist dies eine Anforderung vom Typ [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest), die durch das **HttpTriggerAttribute** zum Definieren des Triggerverhaltens ergänzt wird.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Das Rückgabeobjekt ist ein [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)-Element, das eine Antwortnachricht entweder als [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) oder [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) zurückgibt. Weitere Informationen finden Sie unter [HTTP-Trigger und -Bindungen in Azure Functions](./functions-bindings-http-webhook.md?tabs=csharp).

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Erstellen Sie die Funktions-App in Azure:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Der Befehl [az functionapp create](/cli/azure/functionapp#az_functionapp_create) erstellt die Funktions-App in Azure. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
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

[Herstellen einer Verbindung mit einer Azure Storage-Warteschlange]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp
