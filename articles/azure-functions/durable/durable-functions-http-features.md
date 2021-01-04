---
title: HTTP-Features in Durable Functions – Azure Functions
description: Erfahren Sie mehr über die integrierten HTTP-Features in der Durable Functions-Erweiterung für Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 64d40de50f21811a56318971de1836abc8fbf8c9
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027260"
---
# <a name="http-features"></a>HTTP-Features

Durable Functions verfügt über mehrere Features, die die Einbindung dauerhafter Orchestrierungen und Entitäten in HTTP-Workflows vereinfachen. In diesem Artikel werden einige dieser Features ausführlich erläutert.

## <a name="exposing-http-apis"></a>Verfügbarmachen von HTTP-APIs

Orchestrierungen und Entitäten können mithilfe von HTTP-Anforderungen aufgerufen und verwaltet werden. Die Durable Functions-Erweiterung macht die integrierten HTTP-APIs verfügbar. Außerdem stellt sie APIs für die Interaktion mit Orchestrierungen und Entitäten aus über HTTP ausgelösten Funktionen heraus bereit.

### <a name="built-in-http-apis"></a>Integrierte HTTP-APIs

Die Durable Functions-Erweiterung fügt dem Azure Functions-Host automatisch eine Reihe von HTTP-APIs hinzu. Diese APIs ermöglichen es Ihnen, mit Orchestrierungen und Entitäten zu interagieren und diese zu verwalten, ohne dass hierfür Programmieraufwand erforderlich ist.

Die folgenden integrierten HTTP-APIs werden unterstützt.

* [Starten einer neuen Orchestrierung](durable-functions-http-api.md#start-orchestration)
* [Abfragen der Orchestrierungsinstanz](durable-functions-http-api.md#get-instance-status)
* [Beenden der Orchestrierungsinstanz](durable-functions-http-api.md#terminate-instance)
* [Senden eines externen Ereignisses an eine Orchestrierung](durable-functions-http-api.md#raise-event)
* [Löschen des Orchestrierungsverlaufs](durable-functions-http-api.md#purge-single-instance-history)
* [Senden eines Vorgangsereignisses an eine Entität](durable-functions-http-api.md#signal-entity)
* [Abrufen des Zustands einer Entität](durable-functions-http-api.md#get-entity)
* [Abfragen der Liste der Entitäten](durable-functions-http-api.md#list-entities)

Im Artikel [HTTP-APIs](durable-functions-http-api.md) finden Sie eine vollständige Beschreibung aller integrierten HTTP-APIs, die von der Durable Functions-Erweiterung verfügbar gemacht werden.

### <a name="http-api-url-discovery"></a>Ermittlung der HTTP-API-URL

Die [Orchestrierungsclientbindung](durable-functions-bindings.md#orchestration-client) macht APIs verfügbar, die zum Generieren praktischer HTTP-Antwortnutzlasten verwendet werden können. Sie kann beispielsweise eine Antwort mit Links zu Verwaltungs-APIs für eine bestimmte Orchestrierungsinstanz erstellen. Im Folgenden finden Sie Beispiele für eine HTTP-Triggerfunktion, die zeigt, wie diese API für eine neue Orchestrierungsinstanz verwendet wird:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

# <a name="python"></a>[Python](#tab/python)

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    function_name = req.route_params['functionName']
    event_data = req.get_body()

    instance_id = await client.start_new(function_name, instance_id, event_data)
    
    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)
```

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}",
      "methods": [
        "post",
        "get"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

---

Das Starten einer Orchestratorfunktion mithilfe der oben gezeigten HTTP-Triggerfunktionen kann über einen beliebigen HTTP-Client erfolgen. Der folgende cURL-Befehl startet die Orchestratorfunktion `DoWork`:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Im Folgenden finden Sie eine Beispielantwort für eine Orchestrierung, die `abc123` als ID aufweist. Einige Details wurden aus Gründen der Übersichtlichkeit entfernt.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Im vorherigen Beispiel entsprechen alle Felder, die auf `Uri` enden, einer integrierten HTTP-API. Sie können diese APIs verwenden, um die Zielorchestrierungsinstanz zu verwalten.

> [!NOTE]
> Das Format der Webhook-URLs hängt davon ab, welche Version des Azure Functions-Hosts Sie ausführen. Das obige Beispiel gilt für den Azure Functions 2.0-Host.

Eine Beschreibung aller integrierten HTTP-APIs finden Sie in der [HTTP-API-Referenz](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Nachverfolgen von asynchronen Vorgängen

Die oben erwähnte HTTP-Antwort wurde als Hilfe bei der Implementierung von asynchronen HTTP-APIs mit langer Ausführungsdauer per Durable Functions konzipiert. Dieses Muster wird manchmal auch als *Polling Consumer Pattern* (Consumerabrufmuster) bezeichnet. Der Client/Server-Datenfluss funktioniert wie folgt:

1. Der Client übermittelt eine HTTP-Anforderung, um einen Prozess mit langer Ausführungsdauer zu starten, z. B. eine Orchestratorfunktion.
1. Der HTTP-Zieltrigger gibt eine HTTP 202-Antwort mit einem Location-Header zurück, der den Wert „statusQueryGetUri“ aufweist.
1. Der Client fragt die URL im Location-Header ab. Der Client erhält weiterhin die HTTP 202-Antworten mit dem Location-Header.
1. Wenn die Instanz abgeschlossen wurde (oder zu einem Fehler geführt hat), gibt der Endpunkt im Location-Header als Antwort „HTTP 200“ zurück.

Dieses Protokoll ermöglicht die Koordination von Prozessen mit langer Ausführungsdauer mit externen Clients oder Diensten, die einen HTTP-Endpunkt abfragen können und den Location-Header befolgen. Die Client- und Serverimplementierungen dieses Musters sind in die Durable Functions-HTTP-APIs integriert.

> [!NOTE]
> Standardmäßig unterstützen alle HTTP-basierten Aktionen, die von [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) bereitgestellt werden, das Standardmuster für asynchrone Vorgänge. Diese Funktion ermöglicht das Einbetten eines Durable Functions-Elements als Teil eines Logic Apps-Workflows. Weitere Informationen zur Unterstützung von Logik-Apps für asynchrone HTTP-Muster finden Sie in der [Dokumentation zu Workflowaktionen und -triggern für Azure Logic Apps](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Die Interaktion mit Orchestrierungen kann von jedem Funktionstyp aus erfolgen, nicht nur durch über HTTP ausgelöste Funktionen.

Weitere Informationen zum Verwalten von Orchestrierungen und Entitäten mithilfe von Client-APIs finden Sie im Artikel zum [Verwalten von Instanzen](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Verarbeiten von HTTP-APIs

Wie unter [Codeeinschränkungen für Orchestratorfunktionen](durable-functions-code-constraints.md) beschrieben, können Orchestratorfunktionen E/A-Vorgänge nicht direkt ausführen. Stattdessen werden in der Regel [Aktivitätsfunktionen](durable-functions-types-features-overview.md#activity-functions) aufgerufen, die die E/A-Vorgänge ausführen.

Ab Durable Functions 2.0 können Orchestrierungen mithilfe der [Orchestrierungstriggerbindung](durable-functions-bindings.md#orchestration-trigger) HTTP-APIs nativ nutzen.

Der folgende Beispielcode zeigt eine Orchestratorfunktion, die eine ausgehende HTTP-Anforderung vornimmt:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = yield context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    response = yield context.call_http('GET', url)
    
    if response["statusCode"] >= 400:
        # handling of error codes goes here

main = df.Orchestrator.create(orchestrator_function)
```

---

Mit der Aktion „call HTTP“ sind in Ihren Orchestratorfunktionen folgende Aktionen möglich:

* Direktes Aufrufen von HTTP-APIs aus Orchestrierungsfunktionen mit einigen später beschriebenen Einschränkungen
* Automatisches Unterstützen clientseitiger HTTP 202-Statusabrufmuster
* Verwenden [verwalteter Azure-Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) für autorisierte HTTP-Aufrufe an andere Azure-Endpunkte

Die Möglichkeit, HTTP-APIs direkt von Orchestratorfunktionen zu nutzen, ist bei einigen häufigen Szenarien sehr praktisch. Sie können alle diese Features selbst mithilfe von Aktivitätsfunktionen implementieren. In vielen Fällen bieten Ihnen Aktivitätsfunktionen mehr Flexibilität.

### <a name="http-202-handling"></a>HTTP 202-Verarbeitung

Die API „call HTTP“ kann die Clientseite des Polling Consumer Pattern automatisch implementieren. Wenn eine aufgerufene API eine HTTP 202-Antwort mit einem Location-Header zurückgibt, ruft die Orchestratorfunktion automatisch die Location-Ressource ab, bis eine Antwort zurückgegeben wird, die nicht 202 ist. Dies ist die Antwort, die an den Orchestratorfunktionscode zurückgegeben wird.

> [!NOTE]
> Orchestratorfunktionen bieten auch native Unterstützung für das serverseitige Polling Consumer Pattern, wie unter [Nachverfolgen von asynchronen Vorgängen](#async-operation-tracking) beschrieben. Diese Unterstützung bedeutet, dass Orchestrierungen in einer Funktions-App die Orchestratorfunktionen in anderen Funktions-Apps problemlos koordinieren können. Dies ähnelt dem Konzept der [untergeordneten Orchestrierung](durable-functions-sub-orchestrations.md), jedoch mit Unterstützung für die App-übergreifende Kommunikation. Diese Unterstützung ist insbesondere beim Entwickeln von Apps im Stil von Microservices praktisch.

### <a name="managed-identities"></a>Verwaltete Identitäten

Durable Functions bietet native Unterstützung für das Aufrufen von APIs, die Azure AD-Token (Azure Active Directory) für die Autorisierung akzeptieren. Diese Unterstützung verwendet [verwaltete Azure -Identitäten](../../active-directory/managed-identities-azure-resources/overview.md), um diese Token abzurufen.

Der folgende Code ist ein Beispiel für eine .NET-Orchestratorfunktion. Die Funktion nimmt authentifizierte Aufrufe zum Neustart eines virtuellen Computers mithilfe der [Virtual Machines-REST-API](/rest/api/compute/virtualmachines) von Azure Resource Manager vor.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net/.default
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net/.default"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net/.default");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    subscription_id = "mySubId"
    resource_group = "myRg"
    vm_name = "myVM"
    api_version = "2019-03-01"
    token_source = df.ManagedIdentityTokenSource("https://management.core.windows.net/.default")

    # get a list of the Azure subscriptions that I have access to
    restart_response = yield context.call_http("POST", 
        f"https://management.azure.com/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}/restart?api-version={api_version}",
        None,
        None,
        token_source)
    return restart_response

main = df.Orchestrator.create(orchestrator_function)
```

---

Im vorherigen Beispiel ist der `tokenSource`-Parameter so konfiguriert, dass Azure AD-Token für [Azure Resource Manager](../../azure-resource-manager/management/overview.md) abgerufen werden. Die Token werden durch den Ressourcen-URI `https://management.core.windows.net/.default` identifiziert. Im Beispiel wird davon ausgegangen, dass die aktuelle Funktions-App entweder lokal ausgeführt wird oder als eine Funktions-App mit einer verwalteten Identität bereitgestellt wurde. Es wird davon ausgegangen, dass die lokale Identität oder die verwaltete Identität über Berechtigungen zum Verwalten virtueller Computer in der angegebenen Ressourcengruppe `myRG` verfügt.

Zur Laufzeit gibt die konfigurierte Tokenquelle automatisch ein OAuth 2.0-Zugriffstoken zurück. Die Quelle fügt das Token dann als Bearertoken dem Autorisierungsheader der ausgehenden Anforderung hinzu. Dieses Modell ist aus folgenden Gründen besser als das manuelle Hinzufügen eines Autorisierungsheaders zu HTTP-Anforderungen:

* Die Tokenaktualisierung wird automatisch durchgeführt. Sie müssen sich nicht um abgelaufene Token kümmern.
* Token werden niemals im Zustand der dauerhaften Orchestrierung gespeichert.
* Sie müssen keinen Code schreiben, um den Tokenabruf zu verwalten.

Ein ausführlicheres Beispiel finden Sie in den [vorkompilierten C#-Beispielen für „RestartVMs“](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs).

Verwaltete Identitäten sind nicht auf die Azure-Ressourcenverwaltung beschränkt. Sie können verwaltete Identitäten für den Zugriff auf beliebige APIs verwenden, die Azure AD-Bearertoken akzeptieren, einschließlich der Azure-Dienste von Microsoft oder Web-Apps von Partnern. Die Web-App eines Partners kann sogar eine andere Funktions-App sein. Eine Liste mit Azure-Diensten von Microsoft, die die Authentifizierung mit Azure AD unterstützen, finden Sie unter [Azure-Dienste, die die Azure AD-Authentifizierung unterstützen](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Einschränkungen

Die integrierte Unterstützung für das Aufrufen von HTTP-APIs ist ein praktisches Feature. Es ist nicht für alle Szenarien geeignet.

Von Orchestratorfunktionen gesendete HTTP-Anforderungen und ihre Antworten werden serialisiert und als Warteschlangennachrichten dauerhaft gespeichert. Dieses Warteschlangenverhalten stellt sicher, dass HTTP-Aufrufe [zuverlässig und beim Wiedergeben der Orchestrierung sicher sind](durable-functions-orchestrations.md#reliability). Für das Queuingverhalten gelten jedoch ebenfalls Einschränkungen:

* Jede HTTP-Anforderung umfasst eine zusätzliche Wartezeit im Vergleich zu einem nativen HTTP-Client.
* Umfangreiche Anforderungs- oder Antwortnachrichten, die nicht in eine Warteschlangennachricht passen, können die Orchestrierungsleistung erheblich beeinträchtigen. Der Mehraufwand für die Abladung von Nachrichtennutzlasten im Blobspeicher kann zu einer Leistungsbeeinträchtigung führen.
* Streaming und segmentierte und binäre Nutzlasten werden nicht unterstützt.
* Die Möglichkeit, das Verhalten des HTTP-Clients anzupassen, ist begrenzt.

Wenn Ihr Anwendungsfall durch eine dieser Einschränkungen beeinträchtigt wird, nutzen Sie stattdessen ggf. Aktivitätsfunktionen und sprachspezifische HTTP-Clientbibliotheken, um ausgehende HTTP-Aufrufe vorzunehmen.

> [!NOTE]
> Wenn Sie .NET-Entwickler sind, fragen Sie sich vielleicht, warum dieses Feature die Typen **DurableHttpRequest** und **DurableHttpResponse** anstelle der integrierten .NET-Typen **HttpRequestMessage** und **HttpResponseMessage** nutzt.
>
> Dies ist eine bewusste Entscheidung. In erster Linie stellen benutzerdefinierte Typen sicher, dass Benutzer keine falschen Annahmen über unterstützte Verhaltensweisen des internen HTTP-Clients treffen. Typen, die spezifisch für Durable Functions sind, tragen auch zu einer Vereinfachung des API-Designs bei. Außerdem können Sie auf einfachere Weise spezielle Features wie die [Integration verwalteter Identitäten](#managed-identities) und das [Polling Consumer Pattern](#http-202-handling) verfügbar machen. 

### <a name="extensibility-net-only"></a>Erweiterbarkeit (nur .NET)

Das Anpassen des Verhaltens des internen HTTP-Clients der Orchestrierung kann mithilfe von [Azure Functions .NET-Abhängigkeitsinjektion](../functions-dotnet-dependency-injection.md) erfolgen. Diese Funktion kann nützlich sein, um geringfügige Verhaltensänderungen vorzunehmen. Sie kann auch hilfreich sein, um Komponententests für den HTTP-Client durch Einfügen von Pseudoobjekten durchzuführen.

Das folgende Beispiel veranschaulicht die Verwendung der Abhängigkeitsinjektion, um die TLS/SSL-Zertifikatüberprüfung für Orchestratorfunktionen zu deaktivieren, die externe HTTP-Endpunkte aufrufen.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über dauerhafte Entitäten](durable-functions-entities.md)
