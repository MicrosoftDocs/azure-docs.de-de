---
title: 'Erstellen einer Python-Funktion über die Befehlszeile: Azure Functions'
description: Erfahren Sie, wie Sie eine Python-Funktion über die Befehlszeile erstellen und anschließend das lokale Projekt für das serverlose Hosten in Azure Functions veröffentlichen.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 5f8bf6fb6f464bcb74d34e2d99fac16c24b12615
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175711"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Schnellstart: Erstellen einer Python-Funktion über die Befehlszeile in Azure

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In diesem Artikel verwenden Sie Befehlszeilentools zum Erstellen einer Python-Funktion, die auf HTTP-Anforderungen antwortet. Der Code wird lokal getestet und anschließend in der serverlosen Umgebung von Azure Functions bereitgestellt.

Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

Es gibt auch eine [Visual Studio Code-basierte Version](create-first-function-vs-code-python.md) dieses Artikels.

## <a name="configure-your-local-environment"></a>Konfigurieren Ihrer lokalen Umgebung

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2), Version 3.x.
  
+ Eines der folgenden Tools zum Erstellen von Azure-Ressourcen:

    + [Azure CLI, Version  2.4 oder höher](/cli/azure/install-azure-cli).

    + [Azure PowerShell](/powershell/azure/install-az-ps), Version 5.0 oder höher.

+ [Python 3.8 (64 Bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 Bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 Bit)](https://www.python.org/downloads/release/python-368/). Diese Versionen werden alle von Version 3.x von Azure Functions unterstützt.

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

Überprüfen Sie Ihre Voraussetzungen, die davon abhängig sind, ob Sie die Azure CLI oder Azure PowerShell zum Erstellen von Azure-Ressourcen verwenden:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

+ Führen Sie in einem Terminal oder Befehlsfenster `func --version` aus, um zu überprüfen, ob mindestens Version 3.x der Azure Functions Core Tools verwendet wird.

+ Führen Sie `az --version` aus, um zu überprüfen, ob die Version 2.4 oder höher der Azure CLI verwendet wird.

+ Führen Sie `az login` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

+ Führen Sie `python --version` (Linux/macOS) oder `py --version` (Windows) aus, um zu überprüfen, ob Python-Version 3.8.x, 3.7.x oder 3.6.x verwendet wird.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Führen Sie in einem Terminal oder Befehlsfenster `func --version` aus, um zu überprüfen, ob mindestens Version 3.x der Azure Functions Core Tools verwendet wird.

+ Führen Sie `(Get-Module -ListAvailable Az).Version` aus, und stellen Sie sicher, dass Version 5.0 oder höher ausgeführt wird. 

+ Führen Sie `Connect-AzAccount` aus, um sich bei Azure anzumelden und zu überprüfen, ob ein aktives Abonnement vorhanden ist.

+ Führen Sie `python --version` (Linux/macOS) oder `py --version` (Windows) aus, um zu überprüfen, ob Python-Version 3.8.x, 3.7.x oder 3.6.x verwendet wird.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Erstellen und Aktivieren einer virtuellen Umgebung

Führen Sie die folgenden Befehle in einem geeigneten Ordner aus, um eine virtuelle Umgebung mit dem Namen `.venv` zu erstellen und zu aktivieren. Achten Sie darauf, dass Sie Python 3.8, 3.7 oder 3.6 verwenden. Diese Versionen werden von Azure Functions unterstützt.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Führen Sie den folgenden Befehl aus, wenn über Python das venv-Paket auf Ihrer Linux-Distribution nicht installiert wurde:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Sie führen alle nachfolgenden Befehle in dieser aktivierten virtuellen Umgebung aus. 

## <a name="create-a-local-function-project"></a>Erstellen eines lokalen Funktionsprojekts

In Azure Functions handelt es sich bei einem Funktionsprojekt um einen Container für eine oder mehrere individuelle Funktionen, die jeweils auf einen bestimmten Trigger reagieren. Für alle Funktionen eines Projekts werden die gleichen lokalen Konfigurationen und Hostkonfigurationen gemeinsam genutzt. In diesem Abschnitt erstellen Sie ein Funktionsprojekt, das nur eine Funktion enthält.

1. Führen Sie den Befehl `func init` wie folgt aus, um in einem Ordner mit dem Namen *LocalFunctionProj* ein Funktionsprojekt mit der angegebenen Runtime zu erstellen:  

    ```console
    func init LocalFunctionProj --python
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

#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* enthält eine Python-Funktion vom Typ `main()`, die gemäß der Konfiguration in *function.json* ausgelöst wird.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

Für einen HTTP-Trigger empfängt die Funktion Anforderungsdaten in der Variablen `req`, wie dies in *function.json* definiert ist. `req` ist eine Instanz der [azure.functions.HttpRequest-Klasse](/python/api/azure-functions/azure.functions.httprequest). Das Rückgabeobjekt, das in *function.json* als `$return` definiert ist, ist eine Instanz der [azure.functions.HttpResponse-Klasse](/python/api/azure-functions/azure.functions.httpresponse). Weitere Informationen finden Sie unter [HTTP-Trigger und -Bindungen in Azure Functions](./functions-bindings-http-webhook.md?tabs=python).

#### <a name="functionjson"></a>function.json

*function.json* ist eine Konfigurationsdatei, in der die Eingabe- und Ausgabebindungen (`bindings`) für die Funktion, einschließlich Triggertyp, definiert sind.

Falls erforderlich, können Sie `scriptFile` auch so ändern, dass eine andere Python-Datei aufgerufen wird.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Für jede Bindung sind eine Richtung, ein Typ und ein eindeutiger Name erforderlich. Der HTTP-Trigger weist eine Eingabebindung vom Typ [`httpTrigger`](functions-bindings-http-webhook-trigger.md) und eine Ausgabebindung vom Typ [`http`](functions-bindings-http-webhook-output.md) auf.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Erstellen von unterstützenden Azure-Ressourcen für Ihre Funktion

Zum Bereitstellen Ihres Funktionscodes in Azure müssen Sie drei Ressourcen erstellen:

- Eine Ressourcengruppe als logischen Container für verwandte Ressourcen.
- Ein Storage-Konto, unter dem Status- und andere Informationen zu Ihren Projekten verwaltet werden.
- Eine Funktions-App, die als Umgebung zum Ausführen Ihres Funktionscodes dient. Eine Funktions-App ist Ihrem lokalen Funktionsprojekt zugeordnet und ermöglicht Ihnen das Gruppieren von Funktionen als logische Einheit, um die Verwaltung, Bereitstellung und Freigabe von Ressourcen zu vereinfachen.

Verwenden Sie die folgenden Befehle, um diese Elemente zu erstellen. Sowohl die Azure CLI als auch PowerShell werden unterstützt.

1. Melden Sie sich bei Azure an, falls dies noch nicht geschehen ist:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Mit dem Befehl [az login](/cli/azure/reference-index#az-login) werden Sie bei Ihrem Azure-Konto angemeldet.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Das Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) meldet Sie bei Ihrem Konto an.

    ---

1. Erstellen Sie eine Ressourcengruppe mit dem Namen `AzureFunctionsQuickstart-rg` in der Region `westeurope`. 

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Mit dem Befehl [az group create](/cli/azure/group#az-group-create) wird eine Ressourcengruppe erstellt. Im Allgemeinen erstellen Sie Ihre Ressourcengruppe und die Ressourcen in einer Region in Ihrer Nähe, indem Sie eine verfügbare Region verwenden, die vom Befehl `az account list-locations` zurückgegeben wird.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Der Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) erstellt eine Ressourcengruppe. Im Allgemeinen erstellen Sie Ihre Ressourcengruppe und die Ressourcen in einer Region in Ihrer Nähe, indem Sie eine verfügbare Region verwenden, die vom Cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) zurückgegeben wird.

    ---

    > [!NOTE]
    > In derselben Ressourcengruppe können nicht gleichzeitig Linux- und Windows-Apps gehostet werden. Wenn Sie über eine bestehende Ressourcengruppe mit dem Namen `AzureFunctionsQuickstart-rg` und einer Windows-Funktions-App oder -Web-App verfügen, müssen Sie eine andere Ressourcengruppe verwenden.

1. Erstellen Sie in Ihrer Ressourcengruppe und Region ein universelles Speicherkonto:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Der Befehl [az storage account create](/cli/azure/storage/account#az-storage-account-create) erstellt ein Speicherkonto. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Das Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) erstellt das Speicherkonto.

    ---

    Ersetzen Sie im vorherigen Beispiel `<STORAGE_NAME>` durch einen Namen, der für Sie geeignet und eindeutig in Azure Storage ist. Namen dürfen nur 3 bis 24 Zeichen und ausschließlich Kleinbuchstaben enthalten. Mit `Standard_LRS` wird ein universelles Konto angegeben, das [von Functions unterstützt](storage-considerations.md#storage-account-requirements) wird.
    
    Mit diesem Speicherkonto fallen für diese Schnellstartanleitung nur Kosten in Höhe von wenigen Cent (USD) an.

1. Erstellen Sie die Funktions-App in Azure:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Der Befehl [az functionapp create](/cli/azure/functionapp#az_functionapp_create) erstellt die Funktions-App in Azure. Wenn Sie Python 3.7 oder 3.6 verwenden, ändern Sie `--runtime-version` in `3.7` bzw. `3.6`.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    Das Cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) erstellt die Funktions-App in Azure. Wenn Sie Python 3.7 oder 3.6 verwenden, ändern Sie `-RuntimeVersion` in `3.7` bzw. `3.6`.

    ---
    
    Ersetzen Sie im vorherigen Beispiel `<STORAGE_NAME>` durch den Namen des Kontos, das Sie im vorherigen Schritt verwendet haben, und `<APP_NAME>` durch einen global eindeutigen Namen, der für Sie geeignet ist.  `<APP_NAME>` ist gleichzeitig die DNS-Standarddomäne für die Funktions-App. 
    
    Mit diesem Befehl wird eine Funktions-App erstellt, für die die von Ihnen angegebene Language Runtime unter dem [Azure Functions-Verbrauchstarif](functions-scale.md#consumption-plan) ausgeführt wird. Dies ist für die Nutzungsmenge, die in diesem Fall anfällt, kostenlos. Darüber hinaus wird mit dem Befehl auch eine zugeordnete Azure Application Insights-Instanz in derselben Ressourcengruppe bereitgestellt, mit der Sie Ihre Funktions-App überwachen und Protokolle anzeigen können. Weitere Informationen finden Sie unter [Überwachen von Azure Functions](functions-monitoring.md). Für die Instanz fallen erst Kosten an, wenn Sie sie aktivieren.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Führen Sie den folgenden Befehl aus, um [Streamingprotokolle](functions-run-local.md#enable-streaming-logs) nahezu in Echtzeit in Application Insights im Azure-Portal anzuzeigen:

```console
func azure functionapp logstream <APP_NAME> --browser
```

In einem separaten Terminalfenster oder im Browser rufen Sie die Remotefunktion erneut auf. Ein ausführliches Protokoll der Funktionsausführung in Azure wird im Terminal angezeigt. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einer Azure Storage-Warteschlange](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/python-functions-qs-survey)