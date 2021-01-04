---
title: 'Tutorial: Senden von E-Mails mit Logic Apps'
description: Es wird beschrieben, wie Sie Geschäftsprozesse über Ihre App Service-App aufrufen. Beispiele hierfür sind das Senden von E-Mails, Tweets und Facebook-Posts, das Hinzufügen zu Verteilerlisten und vieles mehr.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 8072a941cd89290af3e25cc63c4fccccce705df9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014661"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Tutorial: Senden von E-Mails und Aufrufen anderer Geschäftsprozesse aus App Service

In diesem Tutorial wird beschrieben, wie Sie Ihre App Service-App in Ihre Geschäftsprozesse integrieren. Dies gilt für alle Web-App-Szenarien, z. B.:

- Senden einer Bestätigungs-E-Mail für eine Transaktion
- Hinzufügen eines Benutzers zu einer Facebook-Gruppe
- Herstellen einer Verbindung mit Drittanbietersystemen, z. B. SAP, Salesforce usw.
- Austauschen von B2B-Standardnachrichten

In diesem Tutorial senden Sie E-Mails per Gmail aus Ihrer App Service-App, indem Sie [Azure Logic Apps](../logic-apps/logic-apps-overview.md) verwenden. Es gibt weitere Möglichkeiten, E-Mails aus einer Web-App zu senden, z. B. über die SMTP-Konfiguration Ihres Sprachframeworks. Mit Logic Apps können Sie aber die Leistungsfähigkeit Ihrer App Service-App steigern, ohne Ihren Code komplexer zu machen. Logic Apps verfügt über eine einfache Konfigurationsschnittstelle für die meisten gängigen geschäftlichen Integrationen, die von Ihrer App jederzeit per HTTP-Anforderung aufgerufen werden können.

## <a name="prerequisite"></a>Voraussetzung

Stellen Sie eine App über das Sprachframework Ihrer Wahl für App Service bereit. Informationen zu einem Tutorial zur Bereitstellung einer Beispiel-App finden Sie hier:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Tutorial: Erstellen einer ASP.NET-App in Azure mit SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Tutorial: Erstellen einer ASP.NET Core- und SQL-Datenbank-App in Azure App Service](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Tutorial: Erstellen einer Node.js- und MongoDB-App in Azure](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Tutorial: Erstellen einer PHP- und MySQL-App in Azure](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Tutorial: Ausführen einer Python-Web-App (Django) mit PostgreSQL in Azure App Service](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Erstellen einer Ruby- und Postgres-App in Azure App Service für Linux](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Erstellen der Logik-App

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) eine leere Logik-App, indem Sie die Anleitung unter [Erstellen Ihrer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app) befolgen. Kehren Sie zu diesem Tutorial zurück, wenn der **Designer für Logik-Apps** angezeigt wird.
1. Wählen Sie auf der Begrüßungsseite des Designers für Logik-Apps unter **Starten Sie mit einem gängigen Trigger** die Option **Beim Empfang einer HTTP-Anforderung** aus.

    ![Screenshot der Begrüßungsseite des Designers für Logik-Apps mit hervorgehobener Option „Beim Empfang einer H T T P-Anforderung“.](./media/tutorial-send-email/receive-http-request.png)
1. Wählen Sie im Dialogfeld unter **Beim Empfang einer HTTP-Anforderung** die Option **Beispielnutzlast zum Generieren eines Schemas verwenden** aus.

    ![Screenshot mit dem Dialogfeld „Beim Empfang einer H T T P-Anforderung“ und der ausgewählten Option „Beispielnutzlast zum Generieren eines Schemas verwenden“. ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Kopieren Sie den folgenden JSON-Beispielcode in das Textfeld, und wählen Sie **Fertig** aus.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Das Schema wird jetzt für die gewünschten Anforderungsdaten generiert. In der Praxis müssen Sie nur die tatsächlichen Anforderungsdaten erfassen, die von Ihrem Anwendungscode generiert werden, und können das JSON-Schema von Azure generieren lassen. 
1. Wählen Sie oben im Designer für Logik-Apps die Option **Speichern** aus. 

    Nun wird die URL Ihres HTTP-Anforderungstriggers angezeigt. Wählen Sie das Kopiersymbol aus, um die URL zur späteren Verwendung zu kopieren.

    ![Screenshot mit hervorgehobenem Kopieren-Symbol zum Kopieren der U R L Ihres H T T P-Anforderungstriggers.](./media/tutorial-send-email/http-request-url.png)

    Diese HTTP-Anforderungsdefinition ist der Ausgangspunkt für alle Schritte, die Sie in dieser Logik-App ausführen möchten, z. B. Gmail oder etwas anderes. Später rufen Sie diese URL dann in Ihrer App Service-App auf. Weitere Informationen zum Anforderungstrigger finden Sie in der [Referenz zu HTTP-Anforderung/-Antwort](../connectors/connectors-native-reqres.md).

1. Klicken Sie unten im Designer auf **Neuer Schritt**, geben Sie im Suchfeld für Aktionen **Gmail** ein, und wählen Sie **E-Mail senden (V2)** aus, nachdem diese Option gefunden wurde.
    
    > [!TIP]
    > Sie können auch nach anderen Arten von Integrationen suchen, z. B. SendGrid, MailChimp, Microsoft 365 und Salesforce. Weitere Informationen hierzu finden Sie in der [Logic Apps-Dokumentation](../logic-apps/index.yml).

1. Wählen Sie im Dialogfeld **Gmail** die Option **Anmelden** aus, und melden Sie sich bei dem Gmail-Konto an, über das Sie die E-Mail senden möchten.

    ![Screenshot des Dialogfelds „Gmail“, das Sie verwenden, um sich bei dem Gmail-Konto anzumelden, von dem aus Sie E-Mail senden möchten.](./media/tutorial-send-email/gmail-sign-in.png)

1. Klicken Sie nach dem Anmelden in das Textfeld **An**. Das Dialogfeld für dynamische Inhalte wird automatisch geöffnet.

1. Wählen Sie neben der Aktion **Beim Empfang einer HTTP-Anforderung** die Option **Weitere anzeigen** aus.

    ![Screenshot der Schaltfläche „Mehr anzeigen“ neben der Aktion „Beim Empfang einer H T T P-Anforderung“.](./media/tutorial-send-email/expand-dynamic-content.png)

    Nun sollten die drei Eigenschaften aus Ihren JSON-Beispieldaten angezeigt werden, die Sie weiter oben verwendet haben. In diesem Schritt verwenden Sie diese Eigenschaften aus der HTTP-Anforderung, um eine E-Mail zu erstellen.
1. Wählen Sie **E-Mail** aus, da der Wert für das Feld **An** bestimmt ist. Bei Bedarf können Sie das Dialogfeld für dynamische Inhalte deaktivieren, indem Sie auf den Umschalter für **Dynamischen Inhalt hinzufügen** klicken.

    ![Screenshot mit den hervorgehobenen Optionen „E-Mail“ und „Dynamischen Konflikt hinzufügen“.](./media/tutorial-send-email/hide-dynamic-content.png)

1. Wählen Sie in der Dropdownliste **Neuen Parameter hinzufügen** die Optionen **Betreff** und **Text** aus.

1. Klicken Sie in das Textfeld **Betreff**, und wählen Sie auf die gleiche Weise **Aufgabe** aus. Geben Sie *Erstellt* ein, während sich der Cursor noch im Feld **Betreff** befindet. 

1. Klicken Sie in das Feld **Text**, und wählen Sie auf die gleiche Weise **Fällig** aus. Bewegen Sie den Cursor vor das Wort **Fällig**, und geben Sie *Dieses Arbeitselement ist fällig am* ein.

    > [!TIP]
    > Falls Sie den HTML-Inhalt direkt im Text der E-Mail ändern möchten, können Sie oben im Fenster des Designers für Logik-Apps die Option **Codeansicht** auswählen. Achten Sie hierbei darauf, dass Sie den dynamischen Inhaltscode beibehalten (z. B. `@{triggerBody()?['due']}`).
    >
    > ![Screenshot der Codeansicht zum Anzeigen von H T M L-Inhalt direkt im E-Mail-Text.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Fügen Sie als Nächstes dem HTTP-Trigger eine asynchrone HTTP-Antwort hinzu. Klicken Sie zwischen dem HTTP-Trigger und der Gmail-Aktion auf das Pluszeichen ( **+** ), und wählen Sie **Parallelen Branch hinzufügen** aus.

    ![Screenshot mit dem Pluszeichen (+) und der hervorgehobenen Option „Parallelen Branch hinzufügen“.](./media/tutorial-send-email/add-http-response.png)

1. Suchen Sie im Suchfeld nach **Antwort**, und wählen Sie dann die Aktion **Antwort** aus.

    ![Screenshot der Suchleiste mit hervorgehobener Aktion „Antwort“.](./media/tutorial-send-email/choose-response-action.png)

    Standardmäßig wird von der Aktion „Antwort“ der Text „HTTP 200“ gesendet. Dies ist für dieses Tutorial ausreichend. Weitere Informationen finden Sie in der [Referenz zu HTTP-Anforderung/-Antwort](../connectors/connectors-native-reqres.md).

1. Wählen Sie oben im Designer für Logik-Apps erneut die Option **Speichern** aus. 

## <a name="add-http-request-code-to-app"></a>Hinzufügen von HTTP-Anforderungscode zur App

Vergewissern Sie sich, dass Sie oben die URL des HTTP-Anforderungstriggers kopiert haben. Weil darin vertrauliche Informationen enthalten sind, besteht die bewährte Methode darin, sie nicht direkt in den Code einzufügen. Bei App Service können Sie stattdessen über die App-Einstellungen als Umgebungsvariable darauf verweisen. 

Erstellen Sie die App-Einstellung in [Cloud Shell](https://shell.azure.com) mit dem folgenden Befehl (ersetzen Sie *\<app-name>* , *\<resource-group-name>* und *\<logic-app-url>* entsprechend):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

Führen Sie in Ihrem Code einen HTTP POST-Standardvorgang für die URL durch, indem Sie eine beliebige HTTP-Clientsprache, die für Ihr Sprachframework verfügbar ist, mit der folgenden Konfiguration verwenden:

- Der Anforderungstext weist das gleiche JSON-Format auf, das Sie für Ihre Logik-App angegeben haben:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- Die Anforderung enthält die Überschrift `Content-Type: application/json`. 
- Senden Sie die Anforderung nach Möglichkeit asynchron, um die Leistung zu optimieren.

Klicken Sie unten auf die Registerkarte für die bevorzugte Sprache bzw. das Framework, um ein Beispiel anzuzeigen.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

In ASP.NET können Sie HTTP POST mit der [System.Net.Http.HttpClient](/dotnet/api/system.net.http.httpclient)-Klasse senden. Beispiel:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Wenn Sie diesen Code für die Beispiel-App aus [Tutorial: Erstellen einer ASP.NET-App in Azure mit SQL-Datenbank](app-service-web-tutorial-dotnet-sqldatabase.md) testen, können Sie hiermit eine E-Mail-Bestätigung per [Create-Aktion](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63) senden, nachdem das `Todo`-Element hinzugefügt wurde. Konvertieren Sie die Create-Aktion in eine asynchrone Aktion, um den obigen asynchronen Code zu verwenden.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

In ASP.NET Core können Sie HTTP POST mit der [System.Net.Http.HttpClient](/dotnet/api/system.net.http.httpclient)-Klasse senden. Beispiel:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Dieser Code wurde zu Demonstrationszwecken vereinfacht. In der Praxis sollten Sie nicht für jede Anforderung ein `HttpClient`-Objekt instanziieren. Befolgen Sie die Anleitung unter [Verwenden von IHttpClientFactory zur Implementierung robuster HTTP-Anforderungen](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

Wenn Sie diesen Code für die Beispiel-App aus [Tutorial: Erstellen einer ASP.NET Core- und SQL-Datenbank-App in Azure App Service](tutorial-dotnetcore-sqldb-app.md) testen, können Sie hiermit eine E-Mail-Bestätigung per [Create-Aktion](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65) senden, nachdem das `Todo`-Element hinzugefügt wurde.

### <a name="nodejs"></a>[Node.js](#tab/node)

In Node.js können Sie HTTP POST auf einfache Weise mit einem npm-Paket wie [axios](https://www.npmjs.com/package/axios) senden. Beispiel:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Wenn Sie diesen Code für die Beispiel-App aus [Tutorial: Erstellen einer Node.js- und MongoDB-App in Azure](tutorial-nodejs-mongodb-app.md) testen, können Sie hiermit eine E-Mail-Bestätigung per [Create-Funktion](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27) senden, nachdem der [Artikel erfolgreich gespeichert wurde](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

### <a name="php"></a>[PHP](#tab/php)

In PHP können Sie HTTP POST auf einfache Weise mit [Guzzle](http://docs.guzzlephp.org/en/stable/index.html) senden. Beispiel:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Wenn Sie diesen Code für die Beispiel-App aus [Tutorial: Erstellen einer PHP- und MySQL-App in Azure](tutorial-php-mysql-app.md) testen, können Sie hiermit eine E-Mail-Bestätigung per [Route::post-Funktion](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48) direkt vor der return-Anweisung senden.

### <a name="python"></a>[Python](#tab/python)

In Python können Sie HTTP POST auf einfache Weise mit [requests](https://pypi.org/project/requests/) senden. Beispiel:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Wenn Sie diesen Code für die Beispiel-App aus [Tutorial: Ausführen einer Python-Web-App (Django) mit PostgreSQL in Azure App Service](tutorial-python-postgresql-app.md) testen, können Sie hiermit eine E-Mail-Bestätigung per [Route::post-Funktion](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48) direkt vor der return-Anweisung senden.

### <a name="ruby"></a>[Ruby](#tab/ruby)

In Ruby können Sie HTTP POST auf einfache Weise mit JSONClient senden. Beispiel:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Wenn Sie diesen Code für die Beispiel-App aus [Erstellen einer Ruby- und Postgres-App in Azure App Service für Linux](tutorial-ruby-postgres-app.md) testen, können Sie hiermit eine E-Mail-Bestätigung per [Create](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38)-Aktion senden, [nachdem @task.save erfolgreich abgeschlossen wurde](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

## <a name="more-resources"></a>Weitere Ressourcen

[Tutorial: Hosten einer RESTful-API mit CORS in Azure App Service](app-service-web-tutorial-rest-api.md)  
[Referenz zu HTTP-Anforderung/-Antwort für Logic Apps](../connectors/connectors-native-reqres.md)  
[Schnellstart: Erstellen Ihres ersten Workflows mithilfe von Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)