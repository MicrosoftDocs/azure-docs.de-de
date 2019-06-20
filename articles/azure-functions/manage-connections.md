---
title: Verwalten von Verbindungen in Azure Functions
description: Erfahren Sie, wie Sie durch die Verwendung statischer Verbindungsclients Leistungsprobleme in Azure Functions vermeiden.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 69425129d5f049254a60032283ddc6ca2ab84d5c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872689"
---
# <a name="manage-connections-in-azure-functions"></a>Verwalten von Verbindungen in Azure Functions

Funktionen innerhalb einer Funktions-App nutzen Ressourcen gemeinsam. Unter diesen geteilten Ressourcen bestehen Verbindungen: HTTP-Verbindungen, Datenbankverbindungen und Verbindungen mit Diensten, wie etwa Azure Storage. Wenn viele Funktionen gleichzeitig ausgeführt werden, kann es sein, dass nicht mehr genügend Verbindungen zur Verfügung stehen. In diesem Artikel erfahren Sie, wie Sie Ihre Funktionen codieren, um zu vermeiden, dass Sie mehr Verbindungen als erforderlich verwenden.

## <a name="connection-limit"></a>Verbindungsgrenzwert

Die Anzahl der verfügbaren Verbindungen ist teilweise begrenzt, da eine Funktions-App in einer [Sandboxumgebung](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) ausgeführt wird. Eine Einschränkung, die die Sandbox Ihrem Code auferlegt, ist eine Grenze für die Anzahl der ausgehenden Verbindungen, die derzeit 600 aktive (insgesamt 1.200) Verbindungen pro Instanz beträgt. Wenn diese Grenze erreicht ist, schreibt die Functions-Runtime die folgende Meldung in die Protokolle: `Host thresholds exceeded: Connections`. Weitere Informationen finden Sie unter [Funktions-Apps: Diensteinschränkungen](functions-scale.md#service-limits).

Dieser Grenzwert gilt pro Instanz. Wenn der [Skalierungscontroller Funktions-App-Instanzen hinzufügt](functions-scale.md#how-the-consumption-and-premium-plans-work), um mehr Anforderungen zu verarbeiten, weist jede Instanz einen unabhängigen Verbindungsgrenzwert auf. Das heißt, es gibt keinen globalen Verbindungsgrenzwert, um sie können über alle aktiven Instanzen weit mehr als 600 aktive Verbindungen verwenden.

Stellen Sie bei der Problembehandlung sicher, dass Sie Application Insights für Ihre Funktions-App aktiviert haben. Mit Application Insights können Sie Metriken für Ihre Funktions-Apps wie Ausführungen anzeigen. Weitere Informationen finden Sie unter [Anzeigen von Telemetriedaten in Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statische Clients

Um zu vermeiden, dass mehr Verbindungen als nötig gehalten werden, erstellen Sie bei jedem Funktionsaufruf keine neuen Instanzen, sondern verwenden die Clientinstanzen erneut. Wir empfehlen, Clientverbindungen für jede Sprache, in der Sie Ihre Funktion erstellen, wiederzuverwenden. Beispielsweise können .NET-Clients wie [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) und [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) sowie Azure Storage-Clients Verbindungen verwalten, wenn Sie einen einzelnen, statischen Client verwenden.

Es folgen einige Richtlinien, die zu beachten sind, wenn Sie einen dienstspezifischen Client in einer Azure Functions-Anwendung verwenden:

- *NEIN* Erstellen Sie keinen neuen Client bei jedem Funktionsaufruf.
- *JA* Erstellen Sie einen einzelnen, statischen Client, der von jedem Funktionsaufruf verwendet werden kann.
- *VIELLEICHT* Denken Sie über die Erstellung eines einzelnen, statischen Clients in einer gemeinsamen Hilfsprogrammklasse nach, wenn verschiedene Funktionen den gleichen Dienst verwenden.

## <a name="client-code-examples"></a>Clientcodebeispiele

Dieser Abschnitt veranschaulicht Best Practices für die Erstellung und Verwendung von Clients über den Funktionscode.

### <a name="httpclient-example-c"></a>HttpClient-Beispiel (C#)

Es folgt ein Beispiel für einen C#-Funktionscode, der eine statische [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)-Instanz erstellt:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Eine häufig gestellte Frage zum [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) in .NET lautet: „Soll ich meinen Client löschen?“. Im Allgemeinen löschen Sie Objekte, die `IDisposable` implementieren, wenn Sie sie nicht mehr verwenden. Sie löschen jedoch keinen statischen Client, da dessen Verwendung mit dem Funktionsende nicht abgeschlossen ist. Der statische Client soll für die Dauer der Anwendung gültig sein.

### <a name="http-agent-examples-javascript"></a>HTTP-Agent-Beispiele (JavaScript)

Da die native [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent)-Klasse bessere Optionen für die Verbindungsverwaltung bietet, sollten Sie diese anstelle von nicht nativen Methoden wie etwa dem `node-fetch`-Modul verwenden. Verbindungsparameter werden mithilfe von Optionen in der `http.agent`-Klasse konfiguriert. Die genauen Optionen, die für den HTTP-Agent verfügbar sind, finden Sie unter [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Für die globale `http.globalAgent`-Klasse, die von `http.request()` verwendet wird, sind all diese Werte auf ihre jeweiligen Standardwerte festgelegt. Die empfohlene Vorgehensweise zum Konfigurieren von Verbindungsbeschränkungen in Functions besteht darin, global eine maximale Anzahl festzulegen. Das folgende Beispiel legt die maximale Anzahl von Sockets für die Funktions-App fest:

```js
http.globalAgent.maxSockets = 200;
```

 Das folgende Beispiel erstellt eine neue HTTP-Anforderung mit einem benutzerdefinierten HTTP-Agent nur für diese Anforderung:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Codebeispiel für DocumentClient (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) stellt eine Verbindung mit einer Azure Cosmos DB-Instanz her. In der Azure Cosmos DB-Dokumentation wird empfohlen, dass Sie [einen Singleton-Azure Cosmos DB-Client für die Lebensdauer der Anwendung verwenden](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). Im folgenden Beispiel wird ein Muster dafür in einer Funktion gezeigt:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient-Codebeispiel (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) stellt eine Verbindung mit einer Azure Cosmos DB-Instanz her. In der Azure Cosmos DB-Dokumentation wird empfohlen, dass Sie [einen Singleton-Azure Cosmos DB-Client für die Lebensdauer der Anwendung verwenden](../cosmos-db/performance-tips.md#sdk-usage). Im folgenden Beispiel wird ein Muster dafür in einer Funktion gezeigt:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient-Verbindungen

Funktionscode kann den .NET Framework-Datenanbieter für SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) verwenden, um Verbindungen zu einer relationalen SQL-Datenbank herzustellen. Dies ist auch der zugrunde liegende Anbieter für Daten-Frameworks, die ADO.NET verwenden, z.B. [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Im Gegensatz zu [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)- und [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)-Verbindungen implementiert ADO.NET standardmäßig das Verbindungspooling. Da jedoch noch immer nicht genügend Verbindungen verfügbar sein können, sollten Sie die Verbindungen mit der Datenbank optimieren. Weitere Informationen finden Sie unter [SQL Server-Verbindungspooling (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Einige Daten-Frameworks, z.B. Entity Framework, rufen Verbindungszeichenfolgen üblicherweise aus dem Abschnitt **ConnectionStrings** einer Konfigurationsdatei ab. In diesem Fall müssen Sie der Sammlung **Verbindungszeichenfolgen** der Funktions-App-Einstellungen und der Datei [local.settings.json](functions-run-local.md#local-settings-file) im lokalen Projekt explizit SQL-Datenbank-Verbindungszeichenfolgen hinzufügen. Bei der Erstellung einer Instanz von [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) in Ihrem Funktionscode sollten Sie den Verbindungszeichenfolgenwert zusammen mit den anderen Verbindungen in den **Anwendungseinstellungen** speichern.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Empfehlung von statischen Clients finden Sie unter [Antimuster für ungeeignete Instanziierung](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Weitere Tipps zur Leistungssteigerung von Azure Functions finden Sie unter [Optimieren der Leistung und Zuverlässigkeit von Azure Functions](functions-best-practices.md).
