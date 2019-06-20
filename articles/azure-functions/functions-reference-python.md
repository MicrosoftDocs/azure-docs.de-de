---
title: Python-Entwicklerreferenz für Azure Functions
description: Entwickeln von Funktionen mit Python
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure Functions, Funktionen, Ereignisverarbeitung, dynamisches Compute, serverlose Architektur, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 039b0951484a6bf57703d9a91d604c9c5e5c9a66
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571182"
---
# <a name="azure-functions-python-developer-guide"></a>Python-Entwicklerhandbuch für Azure Functions

Dieser Artikel ist eine Einführung in die Entwicklung von Azure Functions mithilfe von Python. Der folgende Inhalt geht davon aus, dass Sie das [Azure Functions: Entwicklerhandbuch](functions-reference.md) bereits gelesen haben.

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Programmiermodell

Eine Azure-Funktion sollte eine zustandslose Methode in Ihrem Python-Skript sein, die Eingaben verarbeitet und Ausgaben erzeugt. Standardmäßig erwartet die Runtime, dass die Methode als globale Methode namens `main()` in der `__init__.py`-Datei implementiert ist.

Sie können die Standardkonfiguration ändern, indem Sie die Eigenschaften `scriptFile` und `entryPoint` in der `function.json`-Datei angeben. Beispielsweise weist die unten stehende _function.json_ die Runtime an, die Methode _customentry()_ in der _main.py_-Datei als Einstiegspunkt für Ihre Azure-Funktion zu verwenden.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Daten von Triggern und Bindungen werden an die Funktion mittels Methodenattributen gebunden, die die in der `function.json`-Konfigurationsdatei definierte Eigenschaft `name` verwenden. Beispielsweise beschreibt die unten stehende _function.json_ eine einfache Funktion, die von einer HTTP-Anforderung namens `req` ausgelöst wird:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

Die Datei `__init__.py` enthält folgenden Funktionscode:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Optional können Sie die Parametertypen und den Rückgabetyp auch in der Funktion mithilfe von Python-Typanmerkungen deklarieren. Beispielsweise kann dieselbe Funktion unter Verwendung von Anmerkungen wie folgt geschrieben werden:

```python
import azure.functions

def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```  

Verwenden Sie die Python-Anmerkungen im Paket [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python), um Eingaben und Ausgaben an Ihre Methoden zu binden. 

## <a name="folder-structure"></a>Ordnerstruktur

Die Ordnerstruktur für ein Python Functions-Projekt sieht wie folgt aus:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 | - extensions.csproj
 | - bin
```

Sie können die freigegebene Datei [host.json](functions-host-json.md) zum Konfigurieren der Funktions-App verwenden. Jede Funktion verfügt über eine eigene Codedatei sowie über eine eigene Bindungskonfigurationsdatei (function.json). 

Freigegebener Code sollte in einem separaten Ordner gespeichert werden. Um auf Module im Ordner „SharedCode“ zu verweisen, können Sie die folgende Syntax verwenden:

```
from ..SharedCode import myFirstHelperFunction
```

Die von der Functions-Runtime verwendeten Bindungserweiterungen sind in der Datei `extensions.csproj` definiert, wobei sich die eigentlichen Bibliotheksdateien im Ordner `bin` befinden. Wenn Sie lokal entwickeln, müssen Sie [Bindungserweiterungen registrieren](./functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles), indem Sie Azure Functions Core Tools verwenden. 

Wenn Sie ein Functions-Projekt in Ihrer Funktions-App in Azure bereitstellen, sollte der gesamte Inhalt des Ordners „FunctionApp“ in das Paket aufgenommen werden, aber nicht der Ordner selbst.

## <a name="triggers-and-inputs"></a>Trigger und Eingaben

Eingaben werden in Azure Functions in zwei Kategorien unterteilt: Triggereingaben und zusätzliche Eingaben. Obwohl sie sich in `function.json` unterscheiden, ist ihre Verwendung im Python-Code identisch.  Verbindungszeichenfolgen für Trigger- und Eingabequellen sollten Werten in der Datei `local.settings.json` lokal sowie Anwendungseinstellungen bei der Ausführung in Azure zugeordnet werden. Der folgende Codeausschnitt soll als Beispiel dienen:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Bei Aufruf der Funktion wird die HTTP-Anforderung als `req` an die Funktion übergeben. Es wird ein Eintrag, der auf der _ID_ in der Routen-URL basiert, aus Azure Blob Storage abgerufen und als `obj` im Funktionstext verfügbar gemacht.  Hier ist das angegebene Speicherkonto die Verbindungszeichenfolge, die sich in `AzureWebJobsStorage` befindet, wobei es sich um dasselbe Speicherkonto handelt, das von der Funktions-App verwendet wird.


## <a name="outputs"></a>Ausgaben

Ausgaben können sowohl im Rückgabewert als auch in Ausgabeparametern angegeben werden. Wenn es nur eine Ausgabe gibt, empfehlen wir, den Rückgabewert zu verwenden. Bei mehreren Ausgaben müssen Sie Ausgabeparameter verwenden.

Um den Rückgabewert einer Funktion als Wert für eine Ausgabebindung zu verwenden, sollte die `name`-Eigenschaft der Bindung in `function.json` auf `$return` festgelegt werden.

Um mehrere Ausgaben zu erzeugen, verwenden Sie die `set()`-Methode, die von der `azure.functions.Out`-Schnittstelle bereitgestellt wird, um der Bindung einen Wert zuzuweisen. Die folgende Funktion kann z. B. mithilfe von Push eine Nachricht an eine Warteschlange übertragen und auch eine HTTP-Antwort zurückgeben.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func

def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Protokollierung

Zugriff auf die Azure Functions-Runtimeprotokollierung ist über einen Stamm-[`logging`](https://docs.python.org/3/library/logging.html#module-logging)-Handler in Ihrer Funktions-App verfügbar. Diese Protokollierung ist an Application Insights gebunden und ermöglicht es Ihnen, während der Funktionsausführung aufgetretene Warnungen und Fehler zu kennzeichnen.

Im folgenden Beispiel wird eine Informationsmeldung protokolliert, wenn die Funktion über einen HTTP-Trigger aufgerufen wird.

```python
import logging

def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Es sind zusätzliche Protokollierungsmethoden verfügbar, mit denen Sie auf anderen Ablaufverfolgungsebenen in die Konsole schreiben können:

| Methode                 | BESCHREIBUNG                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_message_)**   | Schreibt eine Meldung mit der Stufe KRITISCH in die Stammprotokollierung.  |
| logging.**error(_message_)**   | Schreibt eine Meldung mit der Stufe ERROR in die Stammprotokollierung.    |
| logging.**warning(_message_)**    | Schreibt eine Meldung mit der Stufe WARNUNG in die Stammprotokollierung.  |
| logging.**info(_message_)**    | Schreibt eine Meldung mit der Stufe INFO in die Stammprotokollierung.  |
| logging.**debug(_message_)** | Schreibt eine Meldung mit der Stufe DEBUG in die Stammprotokollierung.  |

## <a name="importing-shared-code-into-a-function-module"></a>Importieren von freigegebenem Code in ein Funktionsmodul

Python-Module, die zusammen mit Funktionsmodulen veröffentlicht werden, müssen mithilfe der relativen Importsyntax importiert werden:

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

Nehmen Sie alternativ freigegebenen Code in ein eigenständiges Paket auf, veröffentlichen Sie es für eine öffentliche oder private PyPI-Instanz, und geben Sie es als normale Abhängigkeit an.

## <a name="async"></a>Async

Da nur ein einzelner Python-Prozess pro Funktions-App vorhanden sein kann, wird empfohlen, Ihre Azure-Funktion als asynchrone Coroutine mithilfe der `async def`-Anweisung zu implementieren.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Wenn die main()-Funktion synchron ist (ohne `async`-Qualifizierer), führen wir sie automatisch in einem `asyncio`-Threadpool aus.

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Kontext

Um den Aufrufkontext einer Funktion während der Ausführung abzurufen, nehmen Sie das `context`-Argument in ihre Signatur auf. 

Beispiel: 

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Die **Context**-Klasse enthält die folgenden Methoden:

`function_directory`  
Das Verzeichnis, in dem die Funktion ausgeführt wird.

`function_name`  
Name der Funktion.

`invocation_id`  
ID des aktuellen Funktionsaufrufs.

## <a name="python-version-and-package-management"></a>Python-Version und Paketverwaltung

Zurzeit unterstützt Azure Functions nur Python 3.6.x (offizielle CPython-Verteilung).

Bei der lokalen Entwicklung mithilfe der Azure Functions Core Tools oder von Visual Studio Code fügen Sie die Namen und Versionen der erforderlichen Pakete in der `requirements.txt`-Datei hinzu, und installieren Sie sie mithilfe von `pip`.

Beispielsweise können die folgende Datei mit Anforderungen und der pip-Befehl verwendet werden, um das `requests`-Paket aus PyPI zu installieren.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

Wenn Sie zur Veröffentlichung bereit sind, stellen Sie sicher, dass alle Ihre Abhängigkeiten in der `requirements.txt`-Datei aufgeführt sind, die sich im Stamm Ihres Projektverzeichnisses befindet. Um Ihre Azure Functions erfolgreich auszuführen, sollte die Datei mit den Anforderungen mindestens die folgenden Pakete enthalten:

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Veröffentlichen in Azure

Wenn Sie ein Paket verwenden, das einen Compiler erfordert und nicht die Installation von manylinux-kompatiblen Wheels aus PyPI unterstützt, schlägt die Veröffentlichung in Azure mit folgendem Fehler fehl: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Um die erforderlichen Binärdateien automatisch zu erstellen und zu konfigurieren, [installieren Sie Docker](https://docs.docker.com/install/) auf Ihrem lokalen Computer, und führen Sie den folgenden Befehl aus, um mithilfe der [Azure Functions Core Tools](functions-run-local.md#v2) (func) zu veröffentlichen. Denken Sie daran, `<app name>` durch den Namen Ihrer Funktions-App in Azure zu ersetzen. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Im Hintergrund verwenden die Core Tools Docker, um das [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/)-Image als Container auf Ihrem lokalen Computer auszuführen. Unter Verwendung diese Umgebung werden dann die erforderlichen Module aus der Quellverteilung erstellt und installiert, bevor sie zur endgültigen Bereitstellung in Azure gepackt werden.

> [!NOTE]
> Die Core Tools (func) verwenden das PyInstaller-Programm, um den Code und die Abhängigkeiten des Benutzers in einer einzelnen, eigenständigen ausführbaren Datei zur Ausführung in Azure zu fixieren. Diese Funktion befindet sich zurzeit in der Vorschauphase (Preview) und ist möglicherweise nicht für alle Typen von Python-Paketen verfügbar. Wenn Sie Ihre Module nicht importieren können, versuchen Sie eine erneute Veröffentlichung mithilfe der Option `--no-bundler`. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> Wenn weiterhin Probleme auftreten, informieren Sie uns darüber, indem Sie [ein Problem öffnen](https://github.com/Azure/azure-functions-core-tools/issues/new) und eine Beschreibung des Problems einschließen. 


Um Ihre Abhängigkeiten zu erstellen und mithilfe eines Continuous Integration (CI)- und Continuous Delivery (CD)-Systems zu veröffentlichen, können Sie eine [Azure Pipeline](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts) oder ein [benutzerdefiniertes Travis CI-Skript](https://docs.travis-ci.com/user/deployment/script/) verwenden. 

Im Folgenden finden Sie ein `azure-pipelines.yml`-Beispielskript für den Erstellungs- und Veröffentlichungsprozess.
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

Im Folgenden finden Sie ein `.travis.yaml`-Beispielskript für den Erstellungs- und Veröffentlichungsprozess.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

```

## <a name="known-issues-and-faq"></a>Bekannte Probleme und FAQ

Alle bekannten Probleme und Funktionsanfragen werden mithilfe der [GitHub-Probleme](https://github.com/Azure/azure-functions-python-worker/issues)liste nachverfolgt. Wenn Sie auf ein Problem stoßen, das in GitHub nicht zu finden ist, öffnen Sie ein neues Problem mit einer ausführlichen Problembeschreibung.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Bewährte Methoden für Azure Functions](functions-best-practices.md)
* [Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md)
* [Blobspeicherbindungen](functions-bindings-storage-blob.md)
* [HTTP- und Webhook-Bindungen](functions-bindings-http-webhook.md)
* [Warteschlangenspeicherbindungen](functions-bindings-storage-queue.md)
* [Trigger mit Timer](functions-bindings-timer.md)
