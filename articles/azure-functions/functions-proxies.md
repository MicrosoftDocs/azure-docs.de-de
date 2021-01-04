---
title: Verwenden von Proxys in Azure Functions
description: Übersicht zum Verwenden von Azure Functions-Proxys
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: fb263239f99bcb4ec4c893b700d5c1cce078659f
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601372"
---
# <a name="work-with-azure-functions-proxies"></a>Verwenden von Azure-Funktionsproxys

In diesem Artikel werden das Konfigurieren von Azure Functions-Proxys und das Arbeiten mit ihnen erläutert. Mit diesem Feature können Sie in Ihrer Funktionen-App Endpunkte angeben, die von einer anderen Ressource implementiert werden. Sie können mit diesen Proxys eine große API in mehrere Funktionen-Apps aufteilen (wie in einer Microservicearchitektur), wobei Clients dennoch eine einzelne API-Oberfläche präsentiert wird.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Für Proxyausführungen gilt die standardmäßige Functions-Abrechnung. Weitere Informationen finden Sie unter [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Erstellen eines Proxys

In diesem Abschnitt erfahren Sie, wie Sie einen Proxy im Functions-Portal erstellen.

1. Öffnen Sie das [Azure portal], und navigieren Sie dann zu Ihrer Funktionen-App.
2. Wählen Sie im linken Bereich **Neuer Proxy** aus.
3. Geben Sie einen Namen für Ihren Proxy an.
4. Konfigurieren Sie den in dieser Funktionen-App verfügbar gemachten Endpunkt durch Angeben von **Routenvorlage** und **HTTP-Methoden**. Diese Parameter verhalten sich entsprechend den Regeln für [HTTP-Trigger].
5. Legen Sie für die **Back-End-URL** einen anderen Endpunkt fest. Dieser Endpunkt könnte eine Funktion in einer anderen Funktionen-App oder eine beliebige andere API sein. Der Wert muss nicht statisch sein und kann auf [Anwendungseinstellungen] und [Parameter von der ursprünglichen Clientanforderung] verweisen.
6. Klicken Sie auf **Erstellen**.

Ihr Proxy ist jetzt als neuer Endpunkt in Ihrer Funktionen-App vorhanden. Aus Sicht eines Clients entspricht dies einem HttpTrigger in Azure Functions. Sie können Ihren neuen Proxy ausprobieren, indem Sie die Proxy-URL kopieren und mit Ihrem bevorzugten HTTP-Client testen.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Ändern von Anforderungen und Antworten

Mit Azure Functions-Proxys können Sie Anforderungen an und Antworten vom Back-End ändern. Diese Transformationen können Variablen gemäß Definition in [Verwenden von Variablen] verwenden.

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Ändern der Back-End-Anforderung

Standardmäßig wird die Back-End-Anforderung als Kopie der ursprünglichen Anforderung initialisiert. Zusätzlich zum Festlegen der Back-End-URL können Sie die HTTP-Methode, Header und Abfragezeichenfolgen-Parameter ändern. Die geänderten Werte können auf [Anwendungseinstellungen] und [Parameter von der ursprünglichen Clientanforderung] verweisen.

Back-End-Anforderungen können im Portal geändert werden, indem Sie auf der Proxydetailseite den Abschnitt *Außerkraftsetzung von Anforderung* erweitern. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Ändern der Antwort

Standardmäßig wird die Clientantwort als Kopie der Back-End-Antwort initialisiert. Sie können Änderungen an Statuscode, Ursachentext, Headern und Text der Antwort vornehmen. Die geänderten Werte können auf [Anwendungseinstellungen], [Parameter von der ursprünglichen Clientanforderung] und [Parameter der Back-End-Antwort] verweisen.

Back-End-Anforderungen können im Portal geändert werden, indem Sie auf der Proxydetailseite den Abschnitt *Außerkraftsetzung von Antwort* erweitern. 

## <a name="use-variables"></a><a name="using-variables"></a>Verwenden von Variablen

Die Konfiguration für einen Proxy muss nicht statisch sein. Sie können ihn zur Verwendung von Variablen aus der ursprünglichen Clientanforderung, der Back-End-Antwort oder Anwendungseinstellungen konditionieren.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Verweisen auf lokale Funktionen
Verwenden Sie `localhost`, um direkt auf eine Funktion innerhalb der gleichen Funktions-App zu verweisen, ohne eine Roundtrip-Proxyanforderung.

`"backendUri": "https://localhost/api/httptriggerC#1"` verweist auf eine lokale, durch HTTP ausgelöste Funktion auf der Route `/api/httptriggerC#1`.

 
>[!Note]  
>Wenn Ihre Funktion die Autorisierungsstufe *function, admin oder sys* verwendet, müssen Sie den Code und die Client-ID gemäß der ursprünglichen Funktions-URL angeben. In diesem Fall sieht der Verweis wie folgt aus: `"backendUri": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` Sie sollten diese Schlüssel in den [Anwendungseinstellungen] speichern und in Ihren Proxys auf diese verweisen. Dadurch wird das Speichern von Geheimnissen im Quellcode vermieden. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Verweisen auf Anforderungsparameter

Sie können Anforderungsparameter als Eingaben für die Back-End-URL-Eigenschaft oder im Rahmen der Änderung von Anforderungen und Antworten verwenden. Einige Parameter können der Routenvorlage entnommen werden, die in der Basisproxykonfiguration angegeben wird, und andere können von Eigenschaften der eingehenden Anforderung stammen.

#### <a name="route-template-parameters"></a>Routenvorlagenparameter
Auf Parameter, die in der Routenvorlage verwendet werden, kann mit dem Namen verwiesen werden. Die Parameternamen werden in geschweifte Klammern ({}) eingeschlossen.

Wenn ein Proxy etwa eine Routenvorlage wie `/pets/{petId}` hat, kann die Back-End-URL den Wert von `{petId}` enthalten, wie in `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Wenn die Routenvorlage mit einem Platzhalter wie z.B. `/api/{*restOfPath}` endet, ist der Wert `{restOfPath}` eine Zeichenfolgendarstellung der verbleibenden Pfadsegmente aus der eingehenden Anforderung.

#### <a name="additional-request-parameters"></a>Zusätzliche Anforderungsparameter
Zusätzlich zu den Routenvorlagenparametern können die folgenden Werte in Konfigurationswerten verwendet werden:

* **{request.method}** : Die HTTP-Methode, die in der ursprünglichen Anforderung verwendet wird.
* **{request.headers.\<HeaderName\>}** : Ein Header, der aus der ursprünglichen Anforderung gelesen werden kann. Ersetzen Sie *\<HeaderName\>* durch den Namen des Headers, den Sie lesen möchten. Wenn der Header nicht in der Anforderung enthalten ist, wird der Wert eine leere Zeichenfolge sein.
* **{request.querystring.\<ParameterName\>}** : Ein Abfragezeichenfolgen-Parameter, der aus der ursprünglichen Anforderung gelesen werden kann. Ersetzen Sie *\<ParameterName\>* durch den Namen des Parameters, den Sie lesen möchten. Wenn der Parameter nicht in der Anforderung enthalten ist, wird der Wert eine leere Zeichenfolge sein.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Verweisen auf Back-End-Antwortparameter

Antwortparameter können als Teil der Änderung der Antwort an den Client verwendet werden. Die folgenden Werte können in Konfigurationswerten verwendet werden:

* **{backend.response.statusCode}** : Der HTTP-Statuscode, der in der Back-End-Antwort zurückgegeben wird.
* **{backend.response.statusReason}** : Der HTTP-Ursachentext, der in der Back-End-Antwort zurückgegeben wird.
* **{backend.response.headers.\<HeaderName\>}** : Ein Header, der aus der Back-End-Antwort gelesen werden kann. Ersetzen Sie *\<HeaderName\>* durch den Namen des Headers, den Sie lesen möchten. Wenn der Header nicht in der Antwort enthalten ist, wird der Wert eine leere Zeichenfolge sein.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Verweisen auf Anwendungseinstellungen

Sie können auch auf [Anwendungseinstellungen verweisen, die für die Funktionen-App definiert sind](./functions-how-to-use-azure-function-app-settings.md), indem Sie den Namen der Einstellung in Prozentzeichen (%) einschließen.

Bei der Back-End-URL *https://%ORDER_PROCESSING_HOST%/api/orders* würde „%ORDER_PROCESSING_HOST%“ z. B. durch den Wert der Einstellung ORDER_PROCESSING_HOST ersetzt.

> [!TIP] 
> Verwenden Sie Anwendungseinstellungen für Back-End-Hosts, wenn Sie mehrere Bereitstellungen oder Testumgebungen haben. Auf diese Weise können Sie sicherstellen, dass Sie immer mit dem richtigen Back-End für die jeweilige Umgebung kommunizieren.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Problembehandlung bei Proxys

Wenn Sie das Flag `"debug":true` zu einem Proxy in `proxies.json` hinzufügen, aktivieren Sie die Debugprotokollierung. Die Protokolle werden unter `D:\home\LogFiles\Application\Proxies\DetailedTrace` gespeichert. Auf sie kann über die erweiterten Tools (Kudu) zugegriffen werden. HTTP-Antworten enthalten auch einen `Proxy-Trace-Location`-Header mit einer URL für den Zugriff auf die Protokolldatei.

Sie können einen Proxy von der Clientseite debuggen, indem Sie einen `Proxy-Trace-Enabled`-Header hinzufügen, der auf `true` festgelegt ist. Dadurch wird auch eine Ablaufverfolgung für das Dateisystem protokolliert, und die Ablaufverfolgungs-URL wird als Header in der Antwort zurückgegeben.

### <a name="block-proxy-traces"></a>Blockieren von Proxy-Ablaufverfolgungen

Aus Sicherheitsgründen möchten Sie es unter Umständen nicht zulassen, dass Ihr Dienst zum Generieren einer Ablaufverfolgung aufgerufen wird. Andere können dann ohne Ihre Anmeldeinformationen nicht auf die Ablaufverfolgungsinhalte zugreifen. Das Erstellen der Ablaufverfolgung verbraucht jedoch Ressourcen und legt offen, dass Sie Funktionsproxys verwenden.

Deaktivieren Sie Ablaufverfolgungen vollständig, indem Sie `"debug":false` zu einem bestimmten Proxy in `proxies.json` hinzufügen.

## <a name="advanced-configuration"></a>Erweiterte Konfiguration

Die Proxys, die Sie konfigurieren, werden in einer Datei *proxies.json* gespeichert, die sich im Stamm eines Funktionen-App-Verzeichnisses befindet. Sie können diese Datei manuell bearbeiten und als Teil Ihrer App bereitstellen, wenn Sie eine der [Bereitstellungsmethoden](./functions-continuous-deployment.md) verwenden, die Functions unterstützt. 

> [!TIP] 
> Wenn Sie keine der Bereitstellungsmethoden eingerichtet haben, können Sie auch mit der Datei *proxies.json* im Portal arbeiten. Wechseln Sie zu Ihrer Funktionen-App, und wählen Sie **Plattformfeatures** und dann **App Service-Editor** aus. So können Sie die gesamte Dateistruktur Ihrer Funktionen-App anzeigen und Änderungen vornehmen.

*Proxies.json* wird von einem Proxys-Objekt definiert, das aus benannten Proxys und ihren Definitionen besteht. Wenn Ihr Editor dies unterstützt, können Sie zur Codevervollständigung optional auf ein [JSON-Schema](http://json.schemastore.org/proxies) verweisen. Eine Beispieldatei könnte folgendermaßen aussehen:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Jeder Proxy hat einen Anzeigenamen, wie z.B. *proxy1* im vorherigen Beispiel. Das entsprechende Proxydefinitionsobjekt wird durch die folgenden Eigenschaften definiert:

* **matchCondition**: Erforderlich – ein Objekt, das die Anforderungen definiert, die die Ausführung dieses Proxys auslösen. Es enthält zwei Eigenschaften, die es mit [HTTP-Trigger] gemeinsam hat:
    * _methods_: Ein Array der HTTP-Methoden, auf die der Proxy antwortet. Wenn es nicht angegeben wird, antwortet der Proxy auf alle HTTP-Methoden in der Route.
    * _route_: Erforderlich – definiert die Routenvorlage, mit der gesteuert wird, auf welche Anforderungs-URLs Ihr Proxy antwortet. Im Gegensatz zu HTTP-Triggern ist dies kein Standardwert.
* **backendUri**: Die URL der Back-End-Ressource, zur der die Anforderung über einen Proxy gesendet werden soll. Dieser Wert kann auf Anwendungseinstellungen und Parameter aus der ursprünglichen Clientanforderung verweisen. Wenn diese Eigenschaft nicht enthalten ist, antwortet Azure Functions mit „HTTP 200 OK“.
* **requestOverrides**: Ein Objekt, das Transformationen der Back-End-Anforderung definiert. Siehe [Definieren eines requestOverrides-Objekts].
* **responseOverrides**: Ein Objekt, das Transformationen der Clientantwort definiert. Siehe [Definieren eines responseOverrides-Objekts].

> [!NOTE] 
> Die Eigenschaft *route* in Azure-Funktionsproxys berücksichtigt die Eigenschaft *routePrefix* der Funktionen-App-Hostkonfiguration nicht. Wenn Sie ein Präfix wie `/api` einschließen möchten, muss es in der Eigenschaft *route* enthalten sein.

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a> Deaktivieren einzelner Proxys

Sie können einzelne Proxys deaktivieren, indem Sie `"disabled": true` zum Proxy in der `proxies.json`-Datei hinzufügen. Dies bewirkt, dass alle Anforderungen, die mit matchCondition übereinstimmen, den Fehler 404 zurückgeben.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a> Anwendungseinstellungen

Das Proxyverhalten kann über verschiedene App-Einstellungen gesteuert werden. Eine Beschreibung dieser Funktionen finden Sie in der [Referenz zu den Einstellungen der Funktionen-App](./functions-app-settings.md).

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a> Reservierte Zeichen (Formatierung von Zeichenfolgen)

Proxys lesen alle Zeichenfolgen aus einer JSON-Datei und verwenden \ als Escapesymbol. Proxys interpretieren auch geschweifte Klammern. Alle Beispiele hierfür finden Sie im Folgenden.

|Zeichen|Escapezeichen|Beispiel|
|-|-|-|
|{ or }|{{ or }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Definieren eines requestOverrides-Objekts

Das requestOverrides-Objekt definiert Änderungen an der Anforderung, wenn die Back-End-Ressource aufgerufen wird. Das Objekt wird durch folgende Eigenschaften definiert:

* **backend.request.method**: Die HTTP-Methode, mit der das Back-End aufgerufen wird.
* **backend.request.querystring.\<ParameterName\>** : Ein Abfragezeichenfolgen-Parameter, der für den Aufruf des Back-Ends festgelegt werden kann. Ersetzen Sie *\<ParameterName\>* durch den Namen des Parameters, den Sie festlegen möchten. Beachten Sie Folgendes: Wenn eine leere Zeichenfolge angegeben wird, wird der Parameter weiterhin in die Back-End-Anforderung einbezogen.
* **backend.request.headers.\<HeaderName\>** : Ein Header, der für den Aufruf des Back-Ends festgelegt werden kann. Ersetzen Sie *\<HeaderName\>* durch den Namen des Headers, den Sie festlegen möchten. Beachten Sie Folgendes: Wenn eine leere Zeichenfolge angegeben wird, wird der Parameter weiterhin in die Back-End-Anforderung einbezogen.

Die Werte können auf Anwendungseinstellungen und Parameter aus der ursprünglichen Clientanforderung verweisen.

Eine Beispielkonfiguration könnte folgendermaßen aussehen:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Definieren eines responseOverrides-Objekts

Das responseOverrides-Objekt definiert Änderungen an der Antwort, die an den Client zurückgegeben wird. Das Objekt wird durch folgende Eigenschaften definiert:

* **response.statusCode**: Der HTTP-Statuscode, der an den Client zurückgegeben werden soll.
* **response.statusReason**: Der HTTP-Ursachentext, der an den Client zurückgegeben werden soll.
* **response.body**: Die Zeichenfolgendarstellung des Texts, der an den Client zurückgegeben werden soll.
* **response.headers.\<HeaderName\>** : Ein Header, der für die Antwort an den Client festgelegt werden kann. Ersetzen Sie *\<HeaderName\>* durch den Namen des Headers, den Sie festlegen möchten. Wenn Sie die leere Zeichenfolge angeben, wird der Header nicht in die Antwort einbezogen.

Die Werte können auf Anwendungseinstellungen, Parameter aus der ursprünglichen Clientanforderung und Parameter aus der Back-End-Antwort verweisen.

Eine Beispielkonfiguration könnte folgendermaßen aussehen:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> In diesem Beispiel wird der Antworttext direkt festgelegt, sodass keine `backendUri`-Eigenschaft erforderlich ist. Das Beispiel zeigt, wie Sie mit Azure Functions-Proxys APIs imitieren können.

[Azure portal]: https://portal.azure.com
[HTTP-Trigger]: ./functions-bindings-http-webhook.md
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definieren eines requestOverrides-Objekts]: #requestOverrides
[Definieren eines responseOverrides-Objekts]: #responseOverrides
[Anwendungseinstellungen]: #use-appsettings
[Verwenden von Variablen]: #using-variables
[Parameter von der ursprünglichen Clientanforderung]: #request-parameters
[Parameter der Back-End-Antwort]: #response-parameters
