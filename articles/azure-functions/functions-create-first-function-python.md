---
title: Erstellen einer durch HTTP ausgelösten Funktion in Azure
description: Hier erfahren Sie, wie Sie unter Verwendung von Azure Functions Core Tools und der Azure CLI Ihre erste Python-Funktion in Azure erstellen.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: ea890a49fc7b6a153ece183b153f5bc2a49760b5
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496570"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Erstellen einer durch HTTP ausgelösten Funktion in Azure

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Dieser Artikel demonstriert die Verwendung von Befehlszeilentools zum Erstellen eines Python-Projekts, das in Azure Functions ausgeführt wird. Die von Ihnen erstellte Funktion wird durch HTTP-Anforderungen ausgelöst. Abschließend veröffentlichen Sie Ihr Projekt zur Ausführung als [serverlose Funktion](functions-scale.md#consumption-plan) in Azure.

Dieser Artikel ist der erste von zwei Schnellstarts für Azure Functions. Nach dem Durcharbeiten dieses Artikels können Sie Ihrer Funktion [eine Ausgabebindung an eine Azure Storage-Warteschlange hinzufügen](functions-add-output-binding-storage-queue-python.md).

## <a name="prerequisites"></a>Voraussetzungen

Für den Einstieg müssen Sie über Folgendes verfügen:

+ Installieren von [Python 3.6](https://www.python.org/downloads/)

+ Installieren von [Azure Functions Core Tools](./functions-run-local.md#v2), Version 2.6.666 oder höher

+ Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli) (Version 2.x oder höher).

+ Ein aktives Azure-Abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Erstellen und Aktivieren einer virtuellen Umgebung

Um Python-Funktionen lokal entwickeln und testen zu können, müssen Sie in einer Python 3.6-Umgebung arbeiten. Führen Sie die folgenden Befehle aus, um eine virtuelle Umgebung mit dem Namen `.env` zu erstellen und zu aktivieren.

### <a name="bash-or-a-terminal-window"></a>Bash oder Terminalfenster:

```bash
python3.6 -m venv .env
source .env/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell oder eine Windows-Eingabeaufforderung:

```powershell
py -3.6 -m venv .env
.env\scripts\activate
```

Die verbleibenden Befehle werden in der virtuellen Umgebung ausgeführt.

## <a name="create-a-local-functions-project"></a>Erstellen eines lokalen Functions-Projekts

Ein Functions-Projekt stellt das Äquivalent einer Funktions-App in Azure dar. Es kann mehrere Funktionen aufweisen, die die gleichen lokalen und Hostingkonfigurationen gemein haben.

Führen Sie in der virtuellen Umgebung den folgenden Befehl aus, und wählen Sie dabei **python** als Workerruntime aus.

```command
func init MyFunctionProj
```

Es wird ein Ordner mit dem Namen _MyFunctionProj_ erstellt, der die folgenden drei Dateien enthält:

* `local.settings.json` wird verwendet, um bei der lokalen Ausführung App-Einstellungen und Verbindungszeichenfolgen zu speichern. Diese Datei wird nicht in Azure veröffentlicht.
* `requirements.txt` enthält die Liste der bei der Veröffentlichung in Azure zu installierenden Pakete.
* `host.json` enthält globale Konfigurationsoptionen, die sich auf alle Funktionen in einer Funktions-App auswirken. Diese Datei wird in Azure veröffentlicht.

Navigieren Sie zum neuen MyFunctionProj-Ordner:

```command
cd MyFunctionProj
```

Aktualisieren Sie als Nächstes die host.json-Datei, um Erweiterungsbundles zu aktivieren.  

## <a name="reference-bindings"></a>Verweisbindungen

Erweiterungsbundles vereinfachen im weiteren Verlauf das Hinzufügen von Bindungserweiterungen. Durch sie entfällt außerdem die Notwendigkeit, das .NET Core 2.x SDK zu installieren. Für Erweiterungsbundles ist Version 2.6.1071 der Core Tools oder eine neuere Version erforderlich. 

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Jetzt können Sie Ihrem Projekt eine Funktion hinzufügen.

## <a name="create-a-function"></a>Erstellen einer Funktion

Führen Sie den folgenden Befehl aus, um Ihrem Projekt eine Funktion hinzuzufügen:

```command
func new
```

Wählen Sie die **HTTP-Trigger**-Vorlage aus, geben Sie `HttpTrigger` als Namen der Funktion ein, und drücken Sie dann die EINGABETASTE.

Es wird ein Unterordner mit dem Namen _HttpTrigger_ erstellt, der die folgenden Dateien enthält:

* **function.json**: Konfigurationsdatei, die die Funktion, Trigger und weitere Bindungen definiert. Überprüfen Sie diese Datei, und beachten Sie, dass der Wert für `scriptFile` auf die Datei verweist, die die Funktion enthält, während der Aufruftrigger und die Bindungen im Array `bindings` definiert sind.

  Für jede Bindung sind eine Richtung, ein Typ und ein eindeutiger Name erforderlich. Der HTTP-Trigger weist eine Eingabebindung vom Typ [`httpTrigger`](functions-bindings-http-webhook.md#trigger) und eine Ausgabebindung vom Typ [`http`](functions-bindings-http-webhook.md#output) auf.

* **\_\_Init\_\_.py**: Skriptdatei, die Ihre per HTTP ausgelöste Funktion darstellt. Überprüfen Sie dieses Skript, und beachten Sie, dass es eine `main()`-Standardfunktion enthält. HTTP-Daten aus dem Trigger werden mithilfe des benannten Bindungsparameters `req` an diese Funktion übergeben. `req` ist in function.json definiert und stellt eine Instanz der [azure.functions.HttpRequest-Klasse](/python/api/azure-functions/azure.functions.httprequest) dar. 

    Das Rückgabeobjekt, das in function.json als `$return` definiert ist, ist eine Instanz der [azure.functions.HttpResponse-Klasse](/python/api/azure-functions/azure.functions.httpresponse). Weitere Informationen finden Sie unter [HTTP-Trigger und -Bindungen in Azure Functions](functions-bindings-http-webhook.md).

## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

Der folgende Befehl startet die Funktions-App, die lokal mithilfe der gleichen Azure Functions-Runtime ausgeführt wird, die in Azure vorhanden ist.

```bash
func host start
```

Wenn der Functions-Host startet, schreibt er eine Ausgabe ähnlich der folgenden, die zur besseren Lesbarkeit beschnitten wurde:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Kopieren Sie die URL Ihrer `HttpTrigger`-Funktion aus der Runtimeausgabe, und fügen Sie sie in die Adressleiste Ihres Browsers ein. Hängen Sie anschließend die Abfragezeichenfolge `?name=<yourname>` an diese URL an, und führen Sie die Anforderung aus. Hier ist die Antwort des Browsers auf die von der lokalen Funktion zurückgegebenen GET-Anforderung abgebildet:

![Lokales Testen im Browser](./media/functions-create-first-function-python/function-test-local-browser.png)

Jetzt haben Sie die Funktion lokal ausgeführt und können die Funktions-App und andere erforderliche Ressourcen in Azure erstellen.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Erstellen einer Funktions-App in Azure

Eine Funktions-App stellt eine Umgebung für die Ausführung Ihres Funktionscodes bereit. Mit ihr können Sie Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung und Freigabe von Ressourcen.

Führen Sie den folgenden Befehl aus, indem Sie den Platzhalter `<APP_NAME>` durch einen eindeutigen Namen für die Funktions-App und `<STORAGE_NAME>` durch den Speicherkontonamen ersetzen. `<APP_NAME>` ist gleichzeitig die DNS-Standarddomäne für die Funktions-App. Dieser Name muss für alle Apps in Azure eindeutig sein.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

> [!NOTE]
> Azure Functions: Der Verbrauchsplan für Linux befindet sich derzeit in der Vorschau und steht nur in folgenden Regionen zur Verfügung: „USA, Westen“, „USA, Osten“, „Europa, Westen“, „Asien, Osten“. Linux- und Windows-Apps können zudem nicht in derselben Ressourcengruppe gehostet werden. Wenn Sie über eine bestehende Ressourcengruppe mit dem Namen `myResourceGroup` und einer Windows-Funktions-App oder -Web-App verfügen, müssen Sie eine andere Ressourcengruppe verwenden.

Nun können Sie Ihr lokales Funktions-Projekt in der Funktions-App in Azure veröffentlichen.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben ein Python-Funktionsprojekt mit einer per HTTP ausgelösten Funktion erstellt, es auf Ihrem lokalen Computer ausgeführt und es in Azure bereitgestellt. Erweitern Sie nun Ihre Funktion durch...

> [!div class="nextstepaction"]
> [Hinzufügen einer Azure Storage-Warteschlangen-Ausgabebindung](functions-add-output-binding-storage-queue-python.md)
