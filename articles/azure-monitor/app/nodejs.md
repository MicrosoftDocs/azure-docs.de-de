---
title: Überwachen von Node.js-Diensten per Azure Application Insights | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Leistung überwachen und Probleme in Node.js-Diensten mit Application Insights diagnostizieren.
services: application-insights
documentationcenter: nodejs
author: mrbullwinkle
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: f2a30d5a040c2713f04173e83732cea5fa19af3b
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255282"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Überwachen Ihrer Node.js-Dienste und -Apps mit Application Insights

Mit [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) werden Ihre Back-End-Dienste und -Komponenten nach der Bereitstellung überwacht, damit Sie Leistungsprobleme und andere Probleme erkennen und schnell diagnostizieren können. Sie können Application Insights für Node.js-Dienste verwenden, die in Ihrem Datencenter, auf Azure-VMs und in Web-Apps und sogar in anderen öffentlichen Clouds gehostet werden.

Fügen Sie das SDK in Ihren Code ein, und richten Sie dann in Azure eine entsprechende Application Insights-Ressource ein, um Ihre Überwachungsdaten zu empfangen, zu speichern und zu untersuchen. Das SDK sendet Daten zur weiteren Analyse und Untersuchung an diese Ressource.

Das Node.js SDK kann ein- und ausgehende HTTP-Anforderungen, Ausnahmen und einige Systemmetriken automatisch überwachen. Ab Version 0.20 kann das SDK auch einige Drittanbieterpakete überwachen, z.B. MongoDB, MySQL und Redis. Alle Ereignisse, die sich auf eine eingehende HTTP-Anforderung beziehen, werden zur Beschleunigung der Problembehandlung korreliert.

Sie können die TelemetryClient-API verwenden, um weitere Aspekte Ihrer App und Ihres Systems manuell zu instrumentieren und zu überwachen. Die TelemetryClient-API wird weiter unten in diesem Artikel näher beschrieben.

## <a name="get-started"></a>Erste Schritte

Führen Sie die folgenden Aufgaben durch, um die Überwachung für eine App oder einen Dienst einzurichten.

### <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zuerst sicher, dass Sie über ein Azure-Abonnement verfügen, oder [beschaffen Sie sich kostenlos ein neues Abonnement][azure-free-offer]. Falls Ihre Organisation bereits über ein Azure-Abonnement verfügt, kann Ihr Administrator Sie anhand [dieser Anleitung][add-aad-user] hinzufügen.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="resource"></a>Einrichten einer Application Insights-Ressource


1. Melden Sie sich beim [Azure-Portal][portal] an.
2. Klicken Sie auf **Ressource erstellen** > **Entwicklertools** > **Application Insights**. Die Ressource enthält u.a. einen Endpunkt zum Empfangen von Telemetriedaten, Speicherplatz für diese Daten, gespeicherte Berichte und Dashboards, eine Regel- und Warnungskonfiguration.

3. Wählen Sie auf der Seite für die Ressourcenerstellung im Feld **Anwendungstyp** die Option **Node.js-Anwendung**. Der App-Typ bestimmt, welche Dashboards und Berichte erstellt werden. (Alle Application Insights-Ressourcen können Daten für jede Sprache und Plattform sammeln.)

### <a name="sdk"></a>Einrichten des Node.js-SDK

Fügen Sie das SDK in Ihre App ein, damit Daten gesammelt werden können. 

1. Kopieren Sie den Instrumentierungsschlüssel der Ressource (auch als *ikey* bezeichnet) aus dem Azure-Portal. Der ikey wird von Application Insights verwendet, um Ihrer Azure-Ressource Daten zuzuordnen. Bevor Ihr ikey vom SDK verwendet werden kann, müssen Sie ihn in einer Umgebungsvariablen oder im Code angeben.  

   ![Kopieren des Instrumentierungsschlüssels](./media/nodejs/instrumentation-key-001.png)

2. Fügen Sie die Node.js-SDK-Bibliothek per „package.json“ den Abhängigkeiten Ihrer App hinzu. Führen Sie im Stammordner Ihrer App Folgendes aus:

   ```bash
   npm install applicationinsights --save
   ```

3. Laden Sie die Bibliothek explizit im Code. Da das SDK die Instrumentierung auch in viele andere Bibliotheken einbettet, sollten Sie die Bibliothek so früh wie möglich laden – noch vor anderen `require`-Anweisungen. 

   Fügen Sie oben in Ihrer ersten JS-Datei den folgenden Code hinzu. Mit der `setup`-Methode wird der ikey (und somit die Azure-Ressource) konfiguriert, der standardmäßig für alle nachverfolgten Elemente verwendet werden soll.

   ```javascript
   const appInsights = require("applicationinsights");
   appInsights.setup("<instrumentation_key>");
   appInsights.start();
   ```
   
   Sie können einen ikey auch über die Umgebungsvariable „APPINSIGHTS\_INSTRUMENTATIONKEY“ bereitstellen, anstatt ihn manuell an `setup()` oder `new appInsights.TelemetryClient()` zu übergeben. Mit dieser Vorgehensweise können Sie ikeys aus festgelegtem Quellcode heraushalten und unterschiedliche ikeys für unterschiedliche Umgebungen angeben.

   Weitere Konfigurationsoptionen finden Sie in den folgenden Abschnitten.

   Sie können das SDK auch ohne das Senden von Telemetriedaten testen, indem Sie `appInsights.defaultClient.config.disableAppInsights = true` festlegen.

### <a name="monitor"></a> Überwachen Ihrer App

Das SDK sammelt automatisch Telemetriedaten zur Node.js-Laufzeit und zu einigen Drittanbietermodulen. Verwenden Sie Ihre Anwendung, um einige dieser Daten zu generieren.

Navigieren Sie im [Azure-Portal][portal] dann zu der zuvor erstellten Application Insights-Ressource. Suchen Sie in der **Übersichtszeitachse** nach Ihren ersten Datenpunkten. Wählen Sie in den Diagrammen verschiedene Komponenten aus, um detailliertere Daten anzuzeigen.

Wählen Sie zum Anzeigen der Topologie, die für Ihre App ermittelt wird, die Schaltfläche **Anwendungszuordnung**. Wählen Sie Komponenten in der Karte aus, um weitere Details anzuzeigen.

![Einfache App-Zuordnung](./media/nodejs/application-map-002.png)

Weitere Informationen zu Ihrer App und zur Problembehandlung erhalten Sie, indem Sie im Abschnitt **UNTERSUCHEN** die anderen verfügbaren Ansichten auswählen.

![Abschnitt „Untersuchen“](./media/nodejs/007-investigate-pane.png)

#### <a name="no-data"></a>Sie sehen keine Daten?

Da das SDK Daten zur Übermittlung in Batches zusammenfasst, kann es ggf. eine Weile dauern, bis die Elemente im Portal angezeigt werden. Falls in Ihrer Ressource keine Daten angezeigt werden, können Sie folgende Lösungsmöglichkeiten ausprobieren:

* Fahren Sie mit der Verwendung der Anwendung fort. Führen Sie mehr Aktionen durch, um mehr Telemetriedaten zu generieren.
* Klicken Sie in der Ressourcenansicht des Portals auf **Aktualisieren**. Diagramme aktualisieren sich regelmäßig selbst, aber bei der manuellen Aktualisierung wird der Vorgang sofort erzwungen.
* Stellen Sie sicher, dass die [erforderlichen ausgehenden Ports](../../azure-monitor/app/ip-addresses.md) geöffnet sind.
* Verwenden Sie [Suchen](../../azure-monitor/app/diagnostic-search.md), um nach bestimmten Ereignissen zu suchen.
* Lesen Sie die [häufig gestellten Fragen][FAQ].


## <a name="sdk-configuration"></a>SDK-Konfiguration

Die Konfigurationsmethoden und Standardwerte des SDK sind im folgenden Codebeispiel aufgelistet.

Legen Sie `.setAutoDependencyCorrelation(true)` fest, um die Ereignisse für einen Dienst vollständig zu korrelieren. Wenn diese Option festgelegt ist, kann das SDK den Kontext übergreifend für asynchrone Rückrufe in Node.js nachverfolgen.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>TelemetryClient-API

Eine vollständige Beschreibung der TelemetryClient-API finden Sie unter [Application Insights-API für benutzerdefinierte Ereignisse und Metriken](../../azure-monitor/app/api-custom-events-metrics.md).

Sie können alle Anforderungen, Ereignisse, Metriken oder Ausnahmen mit dem Application Insights-Node.js-SDK nachverfolgen. Im folgenden Codebeispiel werden einige APIs veranschaulicht, die Sie verwenden können:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
let client = appInsights.defaultClient;

client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Nachverfolgen Ihrer Abhängigkeiten

Nutzen Sie den folgenden Code, um Ihre Abhängigkeiten nachzuverfolgen:

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>Hinzufügen einer benutzerdefinierten Eigenschaft zu allen Ereignissen

Verwenden Sie den folgenden Code, um allen Ereignissen eine benutzerdefinierte Eigenschaft hinzuzufügen:

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Nachverfolgen von HTTP GET-Anforderungen

Verwenden Sie den folgenden Code, um HTTP GET-Anforderungen nachzuverfolgen:

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
    res.end();
});
```

### <a name="track-server-startup-time"></a>Nachverfolgen der Serverstartzeit

Verwenden Sie den folgenden Code, um die Serverstartzeit nachzuverfolgen:

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen Ihrer Telemetriedaten im Portal](../../azure-monitor/app/overview-dashboard.md)
* [Schreiben von Analytics-Abfragen über Ihre Telemetriedaten](../../azure-monitor/log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../../azure-monitor/app/troubleshoot-faq.md

