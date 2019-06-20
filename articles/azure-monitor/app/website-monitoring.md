---
title: 'Schnellstart: Überwachen von Websites mit Azure Monitor Application Insights | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie mit Azure Monitor Application Insights schnell eine client-/browserseitige Websiteüberwachung einrichten.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/29/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: ece51892522721f7be3cdcadbb3b79705c9f83e4
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384094"
---
# <a name="start-monitoring-your-website"></a>Starten der Überwachung Ihrer Website

Mit Azure Monitor Application Insights können Sie ganz einfach die Verfügbarkeit, Leistung und Nutzung Ihrer Website überwachen. Sie können auch Fehler in Ihrer Anwendung schnell erkennen und diagnostizieren, ohne darauf warten zu müssen, dass diese Fehler von Benutzern gemeldet werden. Application Insights bietet sowohl Funktionen für die serverseitige Überwachung als auch Funktionen für die client-/browserseitige Überwachung.

Diese Schnellstartanleitung zeigt Schritt für Schritt, wie Sie das [Open-Source-basierte JavaScript SDK für Application Insights](https://github.com/Microsoft/ApplicationInsights-JS) hinzufügen, um sich mit der client-/browserseitigen Erfahrung der Besucher Ihrer Website vertraut zu machen.

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

- Sie benötigen ein Azure-Abonnement.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="enable-application-insights"></a>Aktivieren von Application Insights

Application Insights kann Telemetriedaten aus jeder mit dem Internet verbundenen Anwendung erfassen – ob lokal ausgeführt oder in der Cloud. Geben Sie folgendermaßen vor, um diese Daten anzuzeigen.

1. Klicken Sie auf **Ressource erstellen** > **Verwaltungstools** > **Application Insights**.

   Ein Dialogfeld für die Konfiguration wird geöffnet. Füllen Sie die Eingabefelder anhand der Informationen in der folgenden Tabelle aus:

    | Einstellungen        | Wert           | BESCHREIBUNG  |
   | ------------- |:-------------|:-----|
   | **Name**      | Global eindeutiger Wert | Der Name, der die zu überwachende App identifiziert. |
   | **Anwendungstyp** | Allgemeine Anwendung | Der Typ der zu überwachenden App. |
   | **Ressourcengruppe**     | myResourceGroup      | Der Name der neuen Ressourcengruppe, die Application Insights-Daten hosten soll. |
   | **Location** | USA (Ost) | Wählen Sie einen Standort in Ihrer Nähe oder in der Nähe des Standorts, in dem Ihre App gehostet wird. |

2. Klicken Sie auf **Create**.

## <a name="create-an-html-file"></a>Erstellen einer HTML-Datei

1. Erstellen Sie auf Ihrem lokalen Computer eine Datei namens ``hello_world.html``. In diesem Beispiel wird die Datei im Stammverzeichnis von Laufwerk C: unter ``C:\hello_world.html`` abgelegt.
2. Kopieren Sie das folgende Skript in ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-app-insights-sdk"></a>Konfigurieren des Application Insights SDK

1. Wählen Sie **Übersicht** > **Grundlagen** aus, und kopieren Sie den**Instrumentierungsschlüssel** Ihrer Anwendung.

   ![Formular für neue App Insights-Ressource](media/website-monitoring/instrumentation-key-001.png)

2. Fügen Sie der Datei ``hello_world.html`` das folgende Skript vor dem schließenden Tag ``</head>`` hinzu:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
         function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
      }({
         instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      });

      window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

3. Bearbeiten Sie ``hello_world.html``, und fügen Sie Ihren Instrumentierungsschlüssel hinzu.

4. Öffnen Sie ``hello_world.html`` in einer lokalen Browsersitzung. Dadurch wird ein einzelner Seitenaufruf generiert. Sie können Ihren Browser aktualisieren, um mehrere Testseitenaufrufe zu generieren.

## <a name="start-monitoring-in-the-azure-portal"></a>Starten der Überwachung im Azure-Portal

1. Sie können jetzt im Azure-Portal erneut die Seite **Übersicht** für Application Insights öffnen, von der Sie den Instrumentierungsschlüssel abgerufen haben, um Details zu Ihrer aktuell ausgeführten Anwendung anzuzeigen. Die vier Standarddiagramme auf der Übersichtsseite sind auf serverseitige Anwendungsdaten ausgelegt. Da wir die client-/browserseitigen Interaktionen mit dem JavaScript SDK instrumentieren, ist diese spezielle Ansicht nicht relevant (es sei denn, es ist auch ein serverseitiges SDK installiert).

2. Klicken Sie auf ![Symbol für die Anwendungsübersicht](media/website-monitoring/006.png) **Analytics**.  Dadurch wird **Analytics** geöffnet. Hier steht eine erweiterte Abfragesprache zum Analysieren aller Daten zur Verfügung, die von Application Insights gesammelt werden. Führen Sie die folgende Abfrage aus, um Daten im Zusammenhang mit den clientseitigen Browseranforderungen anzuzeigen:

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

3. Kehren Sie zur Seite **Übersicht** zurück. Klicken Sie unter der Überschrift **Untersuchen** auf **Browser**, und wählen Sie anschließend **Leistung** aus. Hier finden Sie Metriken im Zusammenhang mit der Leistung Ihrer Website. Es gibt auch eine Ansicht zum Analysieren von Fehlern und Ausnahmen für Ihre Website. Durch Klicken auf **Beispiele** gelangen Sie zu den Details einzelner Transaktionen. Von dort aus können Sie auf die [End-to-End-Transaktionsdetails](../../azure-monitor/app/transaction-diagnostics.md) zugreifen.

   ![Servermetriken – Diagramm](./media/website-monitoring/browser-performance.png)

4. Klicken Sie im Hauptmenü von Application Insights unter der Überschrift **Nutzung** auf [**Benutzer**](../../azure-monitor/app/usage-segmentation.md), um die [Analysetools für Benutzerverhalten](../../azure-monitor/app/usage-overview.md) zu erkunden. Da wir zum Testen einen einzelnen Computer verwenden, werden nur Daten für einen einzelnen Benutzer angezeigt. Bei einer Livewebsite kann die Benutzerverteilung beispielsweise wie folgt aussehen:

     ![Benutzerdiagramm](./media/website-monitoring/usage-users.png)

5. Ein anderes praktisches Tool für die Instrumentierung komplexerer Websites mit mehreren Seiten ist [**Benutzerabläufe**](../../azure-monitor/app/usage-flows.md). Mit **Benutzerabläufe** können Sie den Pfad nachverfolgen, den Besucher in verschiedenen Bereichen Ihrer Website durchlaufen.

   ![Visualisierung von Benutzerabläufen](./media/website-monitoring/user-flows.png)

Informationen zu komplexeren Konfigurationen für die Websiteüberwachung finden Sie in der [API-Referenz für das JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie planen, mit den nachfolgenden Schnellstarts oder Tutorials fortzufahren, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen. Falls Sie nicht fortfahren möchten, können Sie die folgenden Schritte ausführen, um alle erstellten Ressourcen dieser Schnellstartanleitung im Azure-Portal zu löschen.

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen** und dann auf **myResourceGroup**.
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld **myResourceGroup** ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Suchen nach und Diagnostizieren von Leistungsproblemen](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
