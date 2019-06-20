---
title: Hinzufügen einer Azure Storage-Warteschlangenbindung zu Ihrer Python-Funktion
description: Erfahren Sie, wie Sie Ihrer Python-Funktion mithilfe der Azure CLI und Functions Core Tools eine Azure Storage-Warteschlangenausgabebindung hinzufügen.
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
ms.openlocfilehash: aaeee4238110faa7a842073af8431b30b885db3c
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870021"
---
# <a name="add-an-azure-storage-queue-binding-to-your-function"></a>Hinzufügen einer Azure Storage-Warteschlangenbindung zu Ihrer Funktion

Azure Functions gestattet Ihnen das Verbinden von Azure-Diensten und anderen Ressourcen mit Funktionen, ohne dass Sie Ihren eigenen Integrationscode schreiben müssen. Diese *Bindungen*, die sowohl Eingabe als auch Ausgabe darstellen, werden innerhalb der Funktionsdefinition deklariert. Daten von Bindungen werden der Funktion als Parameter bereitgestellt. Ein Trigger ist ein spezieller Typ Eingabebindung. Während eine Funktion nur einen Trigger hat, kann sie mehrere Ein- und Ausgabebindungen haben. Weitere Informationen finden Sie unter [Konzepte der Trigger und Bindungen in Azure Functions](functions-triggers-bindings.md).

In diesem Artikel erfahren Sie, wie Sie die Funktion, die Sie im [vorherigen Schnellstartartikel](functions-create-first-function-python.md) erstellt haben, mit einer Azure Storage-Warteschlange integrieren. Die Ausgabebindung, die Sie dieser Funktion hinzufügen, schreibt Daten aus der HTTP-Anforderung in eine Nachricht in der Warteschlange. 

Die meisten Bindungen erfordern eine gespeicherte Verbindungszeichenfolge, die Functions verwendet, um auf den gebundenen Dienst zuzugreifen. Um dies zu vereinfachen, verwenden Sie das Speicherkonto, das Sie mit Ihrer Funktions-App erstellt haben. Die Verbindung mit diesem Konto ist bereits in einer App-Einstellung namens `AzureWebJobsStorage` gespeichert.  

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Artikel beginnen, führen Sie die Schritte in [Teil 1 des Python-Schnellstarts](functions-create-first-function-python.md) aus.

## <a name="download-the-function-app-settings"></a>Herunterladen der Funktions-App-Einstellungen

Im vorherigen Schnellstartartikel haben Sie eine Funktions-App in Azure zusammen mit einem Speicherkonto erstellt. Die Verbindungszeichenfolge für dieses Konto wird sicher in App-Einstellungen in Azure gespeichert. In diesem Artikel schreiben Sie Nachrichten in eine Speicherwarteschlange in demselben Konto. Um eine Verbindung mit Ihrem Speicherkonto herzustellen, wenn die Funktion lokal ausgeführt wird, müssen Sie App-Einstellungen in die Datei „local.settings.json“ herunterladen. Führen Sie den folgenden Azure Functions Core Tools-Befehl aus, um Einstellungen in „local.settings.json“ herunterzuladen, und ersetzen Sie dabei `<APP_NAME>` durch den Namen Ihrer Funktions-App aus dem vorherigen Artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Möglicherweise müssen Sie sich bei Ihrem Azure-Konto anmelden.

> [!IMPORTANT]  
> Da sie Geheimnisse enthält, wird die lokale Datei „local.settings.json“ nie veröffentlicht, und sie sollte auch aus der Quellcodeverwaltung ausgeschlossen werden.

Sie benötigen den Wert `AzureWebJobsStorage`, bei dem es sich um die Verbindungszeichenfolge des Speicherkontos handelt. Sie verwenden diese Verbindung, um sicherzustellen, dass die Ausgabebindung wie erwartet funktioniert.

## <a name="add-an-output-binding"></a>Hinzufügen einer Ausgabebindung

In Functions muss für jeden Typ von Bindung eine `direction`, ein `type` und ein eindeutiger `name` in der Datei „function.json“ definiert werden. Abhängig vom Bindungstyp sind möglicherweise zusätzliche Eigenschaften erforderlich. Die [Warteschlangenausgabekonfiguration](functions-bindings-storage-queue.md#output---configuration) beschreibt die Felder, die für eine Azure Storage-Warteschlangenbindung erforderlich sind.

Um eine Bindung zu erstellen, fügen Sie der Datei `function.json` ein Bindungskonfigurationsobjekt hinzu. Bearbeiten Sie die Datei „function.json“ in Ihrem Ordner „HttpTrigger“, um dem Array `bindings` ein Objekt hinzuzufügen, das die folgenden Eigenschaften besitzt:

| Eigenschaft | Wert | BESCHREIBUNG |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Name, der den Bindungsparameter identifiziert, auf den in Ihrem Code verwiesen wird. |
| **`type`** | `queue` | Die Bindung ist eine Azure Storage-Warteschlangenbindung. |
| **`direction`** | `out` | Die Bindung ist eine Ausgabebindung. |
| **`queueName`** | `outqueue` | Der Name der Warteschlange, in den die Bindung schreibt. Wenn der *queueName* nicht vorhanden ist, erstellt die Bindung ihn bei der ersten Verwendung. |
| **`connection`** | `AzureWebJobsStorage` | Der Name einer App-Einstellung, die die Verbindungszeichenfolge für das Speicherkonto enthält. Die Einstellung `AzureWebJobsStorage` enthält die Verbindungszeichenfolge für das Speicherkonto, das Sie mit der Funktions-App erstellt haben. |

Ihre Datei „function.json“ sollte jetzt wie im folgenden Beispiel aussehen:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
  {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Hinzufügen von Code, der die Ausgabebindung verwendet

Sobald sie konfiguriert ist, können Sie beginnen, den `name` der Bindung zu verwenden, um auf sie als Methodenattribut in der Funktionssignatur zuzugreifen. Im folgenden Beispiel ist `msg` eine Instanz der [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest).

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Durch die Verwendung einer Ausgabebindung müssen Sie weder den Azure Storage-SDK-Code für die Authentifizierung verwenden, noch einen Warteschlangenverweis abrufen oder Daten schreiben. Die Functions-Runtime und die Warteschlangenausgabebindung übernehmen diese Aufgaben für Sie.

## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

Wie zuvor verwenden Sie den folgenden Befehl, um die Functions-Runtime lokal zu starten:

```bash
func host start
```

> [!NOTE]  
> Da Sie im vorherige Artikel Erweiterungsbündel in der Datei „host.json“ aktivieren mussten, wurde die [Storage-Bindungserweiterung](functions-bindings-storage-blob.md#packages---functions-2x) während des Starts für Sie heruntergeladen und installiert.

Kopieren Sie die URL Ihrer `HttpTrigger`-Funktion aus der Runtimeausgabe, und fügen Sie sie in die Adressleiste Ihres Browsers ein. Hängen Sie anschließend die Abfragezeichenfolge `?name=<yourname>` an diese URL an, und führen Sie die Anforderung aus. Sie sollten dieselbe Antwort im Browser sehen, wie bereits im vorherigen Artikel.

Diesmal erstellt die Ausgabebindung außerdem eine Warteschlange namens `outqueue` in Ihrem Speicherkonto und fügt eine Nachricht mit dieser selben Zeichenfolge hinzu.

Als Nächstes verwenden Sie die Azure CLI, um die neue Warteschlange anzuzeigen und sicherzustellen, dass eine Nachricht hinzugefügt wurde. Sie können Ihre Warteschlange auch mithilfe des [Microsoft Azure Storage-Explorers][Azure Storage Explorer] oder im [Azure-Portal](https://portal.azure.com) anzeigen.

### <a name="set-the-storage-account-connection"></a>Festlegen der Speicherkontoverbindung

Öffnen Sie die Datei „local.settings.json“, und kopieren Sie den Wert von `AzureWebJobsStorage`, wobei es sich um die Verbindungszeichenfolge des Speicherkontos handelt. Legen Sie die Umgebungsvariable `AZURE_STORAGE_CONNECTION_STRING` auf die Verbindungszeichenfolge fest, indem Sie den folgenden Bash-Befehl verwenden:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Mit der in der Umgebungsvariablen `AZURE_STORAGE_CONNECTION_STRING` festgelegten Verbindungszeichenfolge können Sie auf Ihr Speicherkonto zugreifen, ohne sich jedes Mal authentifizieren zu müssen.

### <a name="query-the-storage-queue"></a>Abfragen der Speicherwarteschlange

Sie können den Befehl [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) verwenden, um die Speicherwarteschlangen in Ihrem Konto anzuzeigen, wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az storage queue list --output tsv
```

Die Ausgabe dieses Befehls umfasst eine Warteschlange namens `outqueue`, wobei es sich um die Warteschlange handelt, die bei Ausführung der Funktion erstellt wurde.

Als Nächstes verwenden Sie den Befehl [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek), um die Nachrichten in dieser Warteschlange anzuzeigen, wie im folgenden Beispiel zu sehen.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Die zurückgegebene Zeichenfolge sollte mit der Nachricht, die Sie zum Testen der Funktion gesendet haben, identisch sein.

> [!NOTE]  
> Im vorherigen Beispiel wird die zurückgegebene Zeichenfolge aus base64 decodiert. Der Grund hierfür ist, dass die Warteschlangenspeicherbindungen als [base64-Zeichenfolgen](functions-bindings-storage-queue.md#encoding) in Azure Storage geschrieben und daraus gelesen werden.

Nun ist es an der Zeit, die aktualisierte Funktions-App erneut in Azure zu veröffentlichen.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Wieder können Sie cURL oder einen Browser verwenden, um die bereitgestellte Funktion zu testen. Fügen Sie wie zuvor die Abfragezeichenfolge `&name=<yourname>` an die URL an, wie im folgenden Beispiel zu sehen:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Sie können [die Speicherwarteschlangennachricht untersuchen](#query-the-storage-queue), um zu überprüfen, ob die Ausgabebindung erneut eine neue Nachricht in der Warteschlange generiert.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben Ihre mittels HTTP ausgelöste Funktion so aktualisiert, dass sie Daten in eine Speicherwarteschlange schreibt. Weitere Informationen zum Entwickeln von Azure Functions mithilfe von Python finden Sie im [Python-Entwicklerhandbuch für Azure Functions](functions-reference-python.md) und unter [Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md).

Als Nächstes sollten Sie die Application Insights-Überwachung für Ihre Funktions-App aktivieren:

> [!div class="nextstepaction"]
> [Aktivieren der Application Insights-Integration](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/