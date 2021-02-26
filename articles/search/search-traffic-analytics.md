---
title: Telemetrie für „Datenverkehrsanalyse durchsuchen“
titleSuffix: Azure Cognitive Search
description: Aktivieren Sie „Datenverkehrsanalyse durchsuchen“ für Azure Cognitive Search, sammeln Sie Telemetriedaten und vom Benutzer initiierte Ereignisse mithilfe von Application Insights, und analysieren Sie die Ergebnisse anschließend in einem Power BI-Bericht.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 2ad6f5bf18d362f846e12720e00584b854729366
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515664"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Sammeln von Telemetriedaten für „Datenverkehrsanalyse durchsuchen“

„Datenverkehrsanalyse durchsuchen“ ist ein Muster für das Sammeln von Telemetriedaten zu Benutzerinteraktionen mit ihrer Azure Cognitive Search-Anwendung, z B. vom Benutzer initiierte Klickereignisse und Tastatureingaben. Mithilfe dieser Informationen können Sie die Effektivität ihrer Suchlösung ermitteln, einschließlich beliebter Suchbegriffe, der Klickraten (CTR) und der Abfrageeingaben, die keine Ergebnisse zurückgeben.

Dieses Muster ist abhängig von [Application Insights](../azure-monitor/app/app-insights-overview.md) (einem Feature von [Azure Monitor](../azure-monitor/index.yml)) zur Sammlung von Benutzerdaten. Es erfordert, dass Sie Ihrem Clientcode Instrumentierung hinzufügen, wie in diesem Artikel beschrieben wird. Abschließend benötigen Sie einen Berichtserstellungsmechanismus, um die Daten zu analysieren. Wir empfehlen Power BI, aber Sie können das Anwendungsdashboard oder ein beliebiges Tool verwenden, das eine Verbindung mit Application Insights herstellt.

> [!NOTE]
> Das in diesem Artikel beschriebene Muster gilt für erweiterte Szenarien und Clickstreamdaten, die von den Ihrem Client hinzugefügten Code generiert werden. Im Gegensatz dazu lassen sich Dienstprotokolle auf einfache Weise einrichten, bieten einen Bereich von Metriken und können im Portal ohne erforderlichen Code ausgeführt werden. Eine Aktivierung der Protokollierung wird für alle Szenarios empfohlen. Weitere Informationen finden Sie unter [Collect and analyze log data](search-monitor-logs.md) (Sammeln und Analysieren von Protokolldaten).

## <a name="identify-relevant-search-data"></a>Identifizieren von relevanten Suchdaten

Wenn Sie nützliche Metriken für „Datenverkehrsanalyse durchsuchen“ erhalten möchten, ist es erforderlich, einige Signale von den Benutzern Ihrer Suchanwendung zu protokollieren. Diese Signale kennzeichnen Inhalte, die Benutzer interessieren und die sie für relevant halten. Bei „Datenverkehrsanalyse durchsuchen“ umfassen sie Folgendes:

+ Vom Benutzer generierte Suchereignisse: Nur Suchabfragen, die von einem Benutzer initiiert wurden, sind interessant. Andere Suchanforderungen, z. B. die zum Auffüllen von Facetten oder zum Abrufen interner Informationen verwendeten, sind nicht von Bedeutung. Stellen Sie sicher, dass Sie nur vom Benutzer initiierte Ereignisse instrumentieren, um Schiefe oder Verzerrungen in Ihren Ergebnissen zu vermeiden.

+ Vom Benutzer generierte Klickereignisse: Auf einer Seite „Suchergebnisse“ bedeutet ein Klickereignis generell, dass ein Dokument ein relevantes Ergebnis für eine bestimmte Suchabfrage ist.

Durch das Verknüpfen von Such- und Klickereignissen mit einer Korrelations-ID können Sie besser verstehen, wie gut die Suchfunktion Ihrer Anwendung funktioniert.

## <a name="add-search-traffic-analytics"></a>Hinzufügen von „Datenverkehrsanalyse durchsuchen“

Öffnen Sie auf der Seite des [Portals](https://portal.azure.com) für Ihren Azure Cognitive Search-Dienst die Seite „Datenverkehrsanalyse durchsuchen“, um auf einen Spickzettel zuzugreifen, anhand dessen Sie diesem Telemetriemuster folgen können. Auf dieser Seite können Sie eine Application Insights-Ressource auswählen oder erstellen, den Instrumentierungsschlüssel abrufen, Ausschnitte kopieren, die Sie für Ihre Lösung anpassen können, und einen Power BI-Bericht herunterladen, der über dem im Muster wiedergegebenen Schema erstellt wurde.

![Seite „Datenverkehrsanalyse durchsuchen“ im Portal](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Seite „Datenverkehrsanalyse durchsuchen“ im Portal")

## <a name="1---set-up-application-insights"></a>1 – Einrichten von Application Insights

Wählen Sie eine zu verwendende Application Insights-Ressource aus, oder [erstellen Sie eine](../azure-monitor/app/create-new-resource.md), wenn Sie noch keine besitzen. Wenn Sie die Seite „Datenverkehrsanalyse durchsuchen“ verwenden, können Sie den Instrumentierungsschlüssel kopieren, den Ihre Anwendung zum Herstellen einer Verbindung mit Application Insights benötigt.

Sobald Sie über eine Application Insights-Ressource verfügen, können Sie Ihre App anhand der [Anweisungen für unterstützte Sprachen und Plattformen](../azure-monitor/app/platforms.md) registrieren. Bei der Registrierung wird einfach der Instrumentierungsschlüssel aus Application Insights Ihrem Code, der die Zuordnung einrichtet, hinzugefügt. Sie finden den Schlüssel im Portal oder auf der Seite „Datenverkehrsanalyse durchsuchen“, wenn Sie eine vorhandene Ressource auswählen.

Eine Verknüpfung, die bei einigen Visual Studio-Projekttypen funktioniert, wird in den folgenden Schritten gezeigt. Sie erstellt eine Ressource und registriert Ihre App mit nur wenigen Klicks.

1. Öffnen Sie bei der Visual Studio- und ASP.NET-Entwicklung Ihre Lösung, und wählen Sie **Projekt** > **Application Insights-Telemetrie hinzufügen** aus.

1. Klicken Sie auf **Erste Schritte**.

1. Registrieren Sie Ihre App durch Angabe eines Microsoft-Kontos, eines Azure-Abonnements und einer Application Insights-Ressource (standardmäßig eine neue Ressource). Klicken Sie auf **Registrieren**.

Jetzt ist Ihre Anwendung für die Anwendungsüberwachung eingerichtet. Dies bedeutet, dass alle Ladevorgänge der Seite mit Standardmetriken nachverfolgt werden. Weitere Informationen zu den vorherigen Schritten finden Sie unter [Aktivieren der serverseitigen Telemetrie für Application Insights](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 – Hinzufügen der Instrumentierung

In diesem Schritt instrumentieren Sie Ihre eigene Suchanwendung mit der Application Insights-Ressource, die Sie im vorherigen Schritt erstellt haben. Dieser Prozess besteht aus vier Schritten, und er beginnt mit dem Erstellen eines Telemetrieclients.

### <a name="step-1-create-a-telemetry-client"></a>Schritt 1: Erstellen eines Telemetrieclients

Erstellen Sie ein Objekt, das Ereignisse an Application Insights sendet. Sie können Ihrem serverseitigen Anwendungscode oder clientseitigen Code, der in einem Browser ausgeführt wird, Instrumentierung hinzufügen. Dieser Code wird hier in Form von C# -und JavaScript-Varianten dargestellt. (Informationen zu weiteren Sprachen finden Sie in der vollständigen Liste der [unterstützten Plattformen und Frameworks](../azure-monitor/app/platforms.md).) Wählen Sie den Ansatz aus, mit dem Sie die gewünschte Informationstiefe erhalten.

Die serverseitige Telemetrie erfasst Metriken auf Anwendungsebene, z. B. in Anwendungen, die als Webdienst in der Cloud ausgeführt werden, oder als lokale App in einem Unternehmensnetzwerk. Die serverseitige Telemetrie sammelt Such- und Klickereignisse, die Position eines Dokuments in Ergebnissen und Abfrageinformationen. Allerdings umfasst die Datensammlung nur die Informationen, die auf dieser Ebene verfügbar sind.

Auf dem Client haben Sie möglicherweise zusätzlichen Code, der Abfrageeingaben bearbeitet, Navigationselemente hinzufügt oder Kontext enthält (z. B. von einer Startseite initiierte Abfragen und eine Produktseite). Wenn damit Ihre Lösung beschrieben wird, könnten Sie sich für die clientseitige Instrumentierung entscheiden, sodass Ihre Telemetrie die zusätzlichen Details widerspiegelt. Wie diese zusätzlichen Details gesammelt werden, geht über den Rahmen dieses Musters hinaus; in [Application Insights für Webseiten](../azure-monitor/app/javascript.md#explore-browserclient-side-data) können Sie aber weitere Informationen erhalten. 

**Verwenden von C#**

Bei C# sollte der Wert für **InstrumentationKey** in ihrer Anwendungskonfiguration zu finden sein, z. B. „appsettings.json“, wenn Ihr Projekt ASP.NET ist. Wenn Sie nicht genau wissen, wo sich der Speicherort für den Schlüssel befindet, lesen Sie die Registrierungsanweisungen.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**Verwenden von JavaScript**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Schritt 2: Anfordern einer Such-ID für die Korrelation

Um Suchanforderungen mit Klicks zu korrelieren, ist eine Korrelations-ID erforderlich, die diese zwei unterschiedlichen Ereignisse miteinander in Beziehung setzt. Azure Cognitive Search bietet Ihnen eine Such-ID, wenn Sie sie mit einem HTTP-Header anfordern.

Die Such-ID ermöglicht die Korrelation der Metriken, die von Azure Cognitive Search für die Anforderung selbst ausgegeben werden, mit den benutzerdefinierten Metriken, die Sie in Application Insights protokollieren.

**Verwenden von C# (neueres v11 SDK)**

Für das aktuelle SDK muss eine HTTP-Pipeline verwendet werden, um den Header wie in diesem [Beispiel](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy) festzulegen.

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Verwenden von C# (älteres v10 SDK)**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Verwenden von JavaScript (Aufrufen von REST-APIs)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Schritt 3: Protokollieren von Suchereignissen

Jede Suchanforderung eines Benutzers sollten Sie in einem benutzerdefinierten Application Insights-Ereignis mit dem nachstehenden Schema als Suchereignis protokollieren. Denken Sie daran, nur benutzergenerierte Suchabfragen zu protokollieren.

+ **SearchServiceName**: (String) Suchdienstname
+ **SearchId**: (GUID) eindeutiger Bezeichner der Suchabfrage (in der Suchantwort enthalten)
+ **IndexName**: (String) abzufragender Suchdienstindex
+ **QueryTerms**: (String) vom Benutzer eingegebene Suchbegriffe
+ **ResultCount**: (Int) Anzahl der zurückgegebenen Dokumente (in der Suchantwort enthalten)
+ **ScoringProfile**: (String) Name des verwendeten Bewertungsprofils, falls vorhanden

> [!NOTE]
> Fordern Sie die Anzahl der vom Benutzer generierten Abfragen an, indem Sie Ihrer Suchabfrage „$count=true“ hinzufügen. Weitere Informationen finden Sie unter [Durchsuchen von Dokumenten (REST).](/rest/api/searchservice/search-documents#query-parameters)
>

**Verwenden von C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**Verwenden von JavaScript**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Schritt 4: Protokollieren von Klickereignissen

Jedes Mal, wenn ein Benutzer auf ein Dokument klickt, ist dies ein Signal, dass für Suchanalysezwecke protokolliert werden muss. Verwenden Sie benutzerdefinierte Application Insights-Ereignisse, um diese Ereignisse mit dem folgenden Schema zu protokollieren:

+ **ServiceName**: (String) Suchdienstname
+ **SearchId**: (GUID) eindeutiger Bezeichner der zugehörigen Suchabfrage
+ **DocId**: (String) Dokumentbezeichner
+ **Position**: (Int) Rang des Dokuments auf der Seite „Suchergebnisse“

> [!NOTE]
> Die Position bezieht sich auf die Hauptreihenfolge in der Anwendung. Sie können diese Zahl frei festlegen, solange sie immer identisch ist, um den Vergleich zu ermöglichen.
>

**Verwenden von C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**Verwenden von JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 – Analysieren in Power BI

Nachdem Sie Ihre App instrumentiert und sichergestellt haben, dass die Anwendung ordnungsgemäß mit Application Insights verbunden ist, laden Sie eine vordefinierte Berichtsvorlage herunter, um Daten in Power BI Desktop zu analysieren. Der Bericht enthält vordefinierte Diagramme und Tabellen, die nützlich für die Analyse der zusätzlichen Daten sind, die für die Analyse des Suchdatenverkehrs erfasst wurden.

1. Klicken Sie im linken Navigationsbereich des Azure Cognitive Search-Dashboards unter **Einstellungen** auf **Datenverkehrsanalyse durchsuchen**.

1. Klicken Sie auf der Seite **Datenverkehrsanalyse durchsuchen** in Schritt 3 auf **Power BI Desktop abrufen**, um Power BI zu installieren.

   ![Abrufen von Power BI-Berichten](./media/search-traffic-analytics/get-use-power-bi.png "Abrufen von Power BI-Berichten")

1. Klicken Sie auf derselben Seite auf **Power BI-Bericht herunterladen**.

1. Der Bericht wird in Power BI Desktop geöffnet, und Sie werden aufgefordert, eine Verbindung mit Application Insights herzustellen und Anmeldeinformationen anzugeben. Sie finden Verbindungsinformationen auf den Seiten des Microsoft Azure-Portals für Ihre Application Insights-Ressource. Geben Sie für Anmeldeinformationen denselben Benutzernamen und dasselbe Kennwort wie für die Anmeldung beim Portal an.

   ![Herstellen einer Verbindung zu Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Herstellen einer Verbindung zu Application Insights")

1. Klicken Sie auf **Laden**.

Der Bericht enthält Diagramme und Tabellen, mit denen Sie fundiertere Entscheidungen treffen können, um Leistung und Relevanz der Suche zu verbessern.

Die Metriken umfassen die folgenden Elemente:

+ Das Suchvolumen und die beliebtesten Begriff-Dokument-Paare: Begriffe, die dazu führen, dass auf das gleiche Dokument geklickt wird, geordnet nach Klicks.
+ Suchvorgänge ohne Klicks: Begriffe für Spitzenabfragen, die keine Klicks registrieren.

Der nachstehende Screenshot zeigt, wie ein integrierter Bericht aussehen könnte, wenn Sie alle Schemaelemente verwendet haben.

![Power BI-Dashboard für Azure Cognitive Search](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Power BI-Dashboard für Azure Cognitive Search")

## <a name="next-steps"></a>Nächste Schritte

Instrumentieren Sie Ihre Suchanwendung, um aussagekräftige Daten über Ihren Suchdienst zu erhalten.

Weitere Informationen finden Sie unter [Application Insights](../azure-monitor/app/app-insights-overview.md). Besuchen Sie auch die [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/application-insights/), um mehr über die verschiedenen Dienstebenen zu erfahren.

Erfahren Sie hier mehr über das Erstellen erstaunlicher Berichte. Weitere Informationen finden Sie unter [Erste Schritte mit Power BI Desktop](/power-bi/fundamentals/desktop-getting-started).
