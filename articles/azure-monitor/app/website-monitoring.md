---
title: 'Schnellstart: Überwachen von Websites mit Azure Monitor Application Insights'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Azure Monitor Application Insights eine client-/browserseitige Websiteüberwachung einrichten.
ms.topic: quickstart
ms.date: 08/19/2020
ms.custom: mvc
ms.openlocfilehash: 0a30fb12e93058690fbd7611ae64fc0a7dd96f64
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583435"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Schnellstart: Beginnen der Überwachung Ihrer Website mit Azure Monitor Application Insights

In dieser Schnellstartanleitung erfahren Sie, wie Sie das Application Insights JavaScript SDK (Open Source) zu Ihrer Website hinzufügen. Darüber hinaus wird gezeigt, wie Sie Erkenntnisse hinsichtlich der client-/browserseitigen Erfahrung Ihrer Websitebesucher gewinnen.

Mit Azure Monitor Application Insights können Sie ganz einfach die Verfügbarkeit, Leistung und Nutzung Ihrer Website überwachen. Sie können auch Fehler in Ihrer Anwendung schnell erkennen und diagnostizieren, ohne darauf warten zu müssen, dass diese Fehler von Benutzern gemeldet werden. Application Insights bietet Funktionen für die serverseitige Überwachung und Funktionen für die client-/browserseitige Überwachung.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Eine Website, der Sie das Application Insights JavaScript SDK hinzufügen können

## <a name="enable-application-insights"></a>Aktivieren von Application Insights

Application Insights kann Telemetriedaten aus jeder mit dem Internet verbundenen Anwendung erfassen, die lokal oder in der Cloud ausgeführt wird. Führen Sie die folgenden Schritte aus, um diese Daten anzuzeigen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Klicken Sie auf **Ressource erstellen** > **Verwaltungstools** > **Application Insights**.

   > [!NOTE]
   >Wenn Sie zum ersten Mal eine Application Insights-Ressource erstellen, finden Sie unter [Erstellen einer Application Insights-Ressource](./create-new-resource.md) weitere Informationen dazu.
1. Wenn das Dialogfeld für die Konfiguration angezeigt wird, füllen Sie die Eingabefelder anhand der Informationen in der folgenden Tabelle aus:

    | Einstellungen        | Wert           | BESCHREIBUNG  |
   | ------------- |:-------------|:-----|
   | **Name**      | Global eindeutiger Wert | Der Name, der die zu überwachende App identifiziert |
   | **Ressourcengruppe**     | myResourceGroup      | Der Name der neuen Ressourcengruppe, die Application Insights-Daten hosten soll. Sie können eine neue Ressourcengruppe erstellen oder eine bereits vorhandene Ressourcengruppe verwenden. |
   | **Location** | East US | Wählen Sie einen Standort in Ihrer Nähe oder in der Nähe des Standorts aus, an dem Ihre App gehostet wird. |
1. Klicken Sie auf **Erstellen**.

## <a name="create-an-html-file"></a>Erstellen einer HTML-Datei

1. Erstellen Sie auf Ihrem lokalen Computer eine Datei namens ``hello_world.html``. Erstellen Sie in diesem Beispiel die Datei im Stammverzeichnis von Laufwerk C, sodass das Ergebnis etwa wie folgt aussieht: ``C:\hello_world.html``.
1. Kopieren Sie das folgende Skript, und fügen Sie es in ``hello_world.html`` ein:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Konfigurieren des Application Insights SDK

1. Wählen Sie **Übersicht** > **Grundlagen** aus, und kopieren Sie den **Instrumentierungsschlüssel** Ihrer Anwendung.

   ![Formular „Neue Application Insights-Ressource“](media/website-monitoring/instrumentation-key-001.png)

1. Fügen Sie der Datei ``hello_world.html`` das folgende Skript vor dem schließenden Tag ``</head>`` hinzu:

    ```javascript
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,u="script",k="instrumentationKey",D="ingestionendpoint",C="disableExceptionTracking",E="ai.device.",I="toLowerCase",b="crossOrigin",w="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"4",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[I](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,p,l,u;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][I]()]=i[1])}if(!e[D]){var r=e.endpointsuffix,o=r?e.location:null;e[D]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[k]||d[k]||"",p=s[D],l=p?p+"/v2/track":config.endpointUrl,(u=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=l,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),u.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,l)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:w,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(w,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(u,l))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(u);n.src=h;var e=y[b];return!e&&""!==e||"undefined"==n[b]||(n[b]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(u)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
    src: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js", // The SDK URL Source
    //name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    //ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    //useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    //crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag 
    cfg: { // Application Insights Configuration
        instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
        /* ...Other Configuration Options... */
    }});
    </script>
    ```
    
1. Bearbeiten Sie ``hello_world.html``, und fügen Sie Ihren Instrumentierungsschlüssel hinzu.

1. Öffnen Sie ``hello_world.html`` in einer lokalen Browsersitzung. Mit dieser Aktion wird ein einzelner Seitenaufruf generiert. Sie können Ihren Browser aktualisieren, um mehrere Testseitenaufrufe zu generieren.

## <a name="monitor-your-website-in-the-azure-portal"></a>Überwachen Ihrer Website im Azure-Portal

1. Öffnen Sie jetzt im Azure-Portal erneut die Seite **Übersicht** für Application Insights, um Details zu Ihrer aktuell ausgeführten Anwendung anzuzeigen. Auf der Seite **Übersicht** haben Sie den Instrumentierungsschlüssel abgerufen.

   Die vier Standarddiagramme auf der Übersichtsseite sind auf serverseitige Anwendungsdaten ausgelegt. Da Sie die client-/browserseitigen Interaktionen mit dem JavaScript SDK instrumentieren, ist diese spezielle Ansicht nicht relevant (es sei denn, es ist auch ein serverseitiges SDK installiert).

1. Wählen Sie **Analytics** ![Symbol Anwendungsübersicht“](media/website-monitoring/006.png) aus.  Mit dieser Aktion wird **Analytics** geöffnet. Hier steht eine erweiterte Abfragesprache zum Analysieren aller Daten zur Verfügung, die von Application Insights gesammelt werden. Führen Sie die folgende Abfrage aus, um Daten im Zusammenhang mit den clientseitigen Browseranforderungen anzuzeigen:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![Analysediagramm der Benutzeranforderungen in einem bestimmten Zeitraum](./media/website-monitoring/analytics-query.png)

1. Kehren Sie zur Seite **Übersicht** zurück. Wählen Sie unter der Überschrift **Untersuchen** die Option **Browser** und anschließend **Leistung** aus.  Metriken im Zusammenhang mit der Leistung Ihrer Website werden angezeigt. Es gibt eine Ansicht zum Analysieren von Fehlern und Ausnahmen für Ihre Website. Sie können **Beispiele** auswählen, um auf die [End-to-End-Transaktionsdetails](./transaction-diagnostics.md) zuzugreifen.

   ![Servermetriken – Diagramm](./media/website-monitoring/browser-performance.png)

1. Wählen Sie im Hauptmenü von Application Insights unter der Überschrift **Nutzung** die Option [**Benutzer**](./usage-segmentation.md) aus, um die [Analysetools für Benutzerverhalten](./usage-overview.md) zu erkunden. Da wir zum Testen einen einzelnen Computer verwenden, werden nur Daten für einen einzelnen Benutzer angezeigt. Bei einer Livewebsite kann die Benutzerverteilung beispielsweise wie folgt aussehen:

     ![Benutzerdiagramm](./media/website-monitoring/usage-users.png)

1. Für eine komplexere Website mit mehreren Seiten können Sie das Tool [**Benutzerabläufe**](./usage-flows.md) verwenden, um die Navigation der Besucher auf Ihrer Website nachzuvollziehen.

   ![Visualisierung von Benutzerabläufen](./media/website-monitoring/user-flows.png)

Informationen zu komplexeren Konfigurationen für die Websiteüberwachung finden Sie in der [API-Referenz für das JavaScript SDK](./javascript.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie planen, mit zusätzlichen Schnellstarts oder Tutorials fortzufahren, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen. Führen Sie andernfalls die folgenden Schritte aus, um alle erstellten Ressourcen dieses Schnellstarts im Azure-Portal zu löschen.

> [!NOTE]
> Wenn Sie eine vorhandene Ressourcengruppe verwendet haben, funktionieren die folgenden Anweisungen nicht. Stattdessen können Sie einfach die einzelne Application Insights-Ressource löschen. Denken Sie daran: Wenn Sie eine Ressourcengruppe löschen, werden auch alle zugrunde liegenden Ressourcen gelöscht, die Mitglieder dieser Gruppe sind.

1. Wählen Sie im Azure-Portal im Menü auf der linken Seite **Ressourcengruppen** und dann **myResourceGroup** oder den Namen Ihrer temporären Ressourcengruppe aus.
1. Wählen Sie auf der Seite mit Ihrer Ressourcengruppe die Option **Löschen** aus, geben Sie in das Textfeld **myResourceGroup** ein, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Suchen nach und Diagnostizieren von Leistungsproblemen](../logs/log-query-overview.md)

