---
title: 'Schnellstart: Überwachen von Node.js mit Azure Monitor Application Insights'
description: Dieser Artikel enthält Informationen zum schnellen Einrichten einer Node.js-Web-App für die Überwachung mit Azure Monitor Application Insights.
ms.subservice: application-insights
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 75ac0cb2616eb86026218d2971ad192c1a557e5f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100626504"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Schnellstart: Starten der Überwachung Ihrer Node.js-Webanwendung mit Azure Application Insights

In dieser Schnellstartanleitung fügen Sie Version 0.22 des Application Insights SDK für Node.js zu einer vorhandenen Node.js-Webanwendung hinzu.

Mit Azure Application Insights können Sie die Verfügbarkeit, Leistung und Nutzung Ihrer Webanwendung ganz einfach überwachen. Sie können auch Fehler in Ihrer Anwendung schnell erkennen und diagnostizieren, ohne darauf warten zu müssen, dass diese Fehler von Benutzern gemeldet werden. Ab der SDK-Version 0.20 können Sie allgemeine Drittanbieterpakete einschließlich MongoDB, MySQL und Redis überwachen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Eine funktionierende Node.js-Anwendung

## <a name="enable-application-insights"></a>Aktivieren von Application Insights

Application Insights kann Telemetriedaten von jeder mit dem Internet verbundenen Anwendung erfassen, unabhängig davon, ob die Anwendung lokal oder in der Cloud ausgeführt wird. Geben Sie folgendermaßen vor, um diese Daten anzuzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie auf **Ressource erstellen** > **Entwicklertools** > **Application Insights**.

   ![Hinzufügen einer Azure Application Insights-Ressource](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Wenn Sie zum ersten Mal eine Application Insights-Ressource erstellen, können Sie mehr dazu im Dokument [Erstellen einer Application Insights-Ressource](../app/create-new-resource.md) erfahren.

   Eine Seite für die Konfiguration wird geöffnet. Füllen Sie die Eingabefelder anhand der Informationen in der folgenden Tabelle aus. 

    | Einstellungen        | Wert           | BESCHREIBUNG  |
   | ------------- |:-------------|:-----|
   | **Name**      | Global eindeutiger Wert | Der Name, der die zu überwachende App identifiziert. |
   | **Ressourcengruppe**     | myResourceGroup      | Der Name der neuen Ressourcengruppe, die Application Insights-Daten hosten soll. Sie können eine neue Ressourcengruppe erstellen oder eine bereits vorhandene Ressourcengruppe verwenden. |
   | **Location** | East US | Wählen Sie einen Standort in Ihrer Nähe oder in der Nähe des Standorts, in dem Ihre App gehostet wird. |

3. Klicken Sie auf **Erstellen**.

## <a name="configure-appinsights-sdk"></a>Konfigurieren des AppInsights SDK

1. Wählen Sie **Übersicht** aus, und kopieren Sie den **Instrumentierungsschlüssel** Ihrer Anwendung.

   ![Anzeigen des Application Insights-Instrumentierungsschlüssels](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Fügen Sie das Application Insights SDK für Node.js zu Ihrer Anwendung hinzu. Führen Sie folgenden Befehl im Stammverzeichnis Ihrer App aus:

   ```bash
   npm install applicationinsights --save
   ```

3. Bearbeiten Sie die erste *JS*-Datei Ihrer App, und fügen Sie dem obersten Teil Ihres Skripts die beiden unten stehenden Zeilen hinzu. Wenn Sie die [Node.js-Schnellstart-App](../../app-service/quickstart-nodejs.md) verwenden, bearbeiten Sie die Datei *index.js*. Ersetzen Sie `<instrumentation_key>` durch den Instrumentierungsschlüssel von Application Insights. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Starten Sie die App neu.

> [!NOTE]
> Es dauert ca. 3-5 Minuten, bis die ersten Daten im Portal angezeigt werden. Wenn es sich um eine Test-App mit geringem Datenverkehr handelt, denken Sie daran, dass die meisten Metriken nur erfasst werden, wenn aktive Anforderungen oder Vorgänge vorhanden sind.

## <a name="start-monitoring-in-the-azure-portal"></a>Starten der Überwachung im Azure-Portal

1. Sie können jetzt im Azure-Portal erneut die Seite **Übersicht** für Application Insights öffnen, von der Sie den Instrumentierungsschlüssel abgerufen haben, um Details zu Ihrer aktuell ausgeführten Anwendung anzuzeigen.

   ![Application Insights: Menü „Übersicht“](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Wählen Sie **Anwendungsübersicht** aus, um ein visuelles Layout der Abhängigkeitsbeziehungen zwischen den Komponenten Ihrer Anwendung zu erhalten. Jede Komponente zeigt KPIs wie z.B. Last, Leistung, Fehler und Warnungen an.

   ![Application Insights-Anwendungsübersicht](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Wählen Sie das **App-Analyse**-Symbol ![Symbol „Anwendungsübersicht“](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **In Analytics anzeigen** aus.  Dadurch wird die **Application Insights-Analyse** geöffnet, die eine erweiterte Abfragesprache zum Analysieren aller Daten bereitstellt, die von Application Insights gesammelt werden. In diesem Fall wird eine Abfrage für Sie generiert, die die Anzahl von Anforderungen als Diagramm darstellt. Sie können selbst Abfragen zum Analysieren anderer Daten schreiben.

   ![Application Insights-Analysediagramme](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Kehren Sie zur Seite **Übersicht** zurück, und untersuchen Sie die KPI-Graphen.  Dieses Dashboard zeigt Statistiken zur Integrität Ihrer Anwendung, einschließlich der Anzahl von eingehenden Anforderungen, der Dauer dieser Anforderungen und aller auftretenden Fehler.

   ![Application Insights-Diagramm der Übersichtszeitachse für die Integrität](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   Damit das Diagramm **Ladezeit der Seitenansicht** mit **clientseitigen Telemetriedaten** aufgefüllt werden kann, fügen Sie dieses Skript zu jeder Seite hinzu, die Sie nachverfolgen möchten:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. Wählen Sie links die Option **Metrik** aus. Untersuchen Sie mithilfe des Metrik-Explorers die Integrität und Auslastung Ihrer Ressource. Sie können **Neues Diagramm hinzufügen** auswählen, um zusätzliche benutzerdefinierte Ansichten zu erstellen, oder **Bearbeiten** auswählen, um Typ, Höhe, Farbpalette, Gruppierungen und Metriken der vorhandenen Diagramme zu ändern. Sie können beispielsweise ein Diagramm für die durchschnittliche Seitenladezeit des Browsers erstellen, indem Sie in der Metrik-Dropdownliste die Option für die Browser-Seitenladezeit und unter „Aggregation“ die Durchschnittsoption auswählen. Weitere Informationen zum Azure-Metrik-Explorer finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../essentials/metrics-getting-started.md).

   ![Application Insights-Diagramm der Servermetriken](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Weitere Informationen zum Überwachen von Node.js finden Sie in der [weiteren Dokumentation zu AppInsights und Node.js](../app/nodejs.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Tests abgeschlossen haben, können Sie die Ressourcengruppe und alle dazugehörigen Ressourcen löschen. Gehen Sie dazu wie folgt vor:

> [!NOTE]
> Wenn Sie eine vorhandene Ressourcengruppe verwendet haben, funktionieren die unten aufgeführten Anweisungen nicht, und Sie müssen einfach die einzelne Application Insights-Ressource löschen. Denken Sie daran: Wenn Sie eine Ressourcengruppe löschen, werden alle zugrunde liegenden Ressourcen, die Mitglieder dieser Gruppe sind, gelöscht.

1. Wählen Sie im Azure-Portal im Menü auf der linken Seite die Option **Ressourcengruppen** und dann **myResourceGroup**.
2. Wählen Sie auf der Seite mit Ihrer Ressourcengruppe die Option **Löschen** aus, geben Sie in das Textfeld **myResourceGroup** ein, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Suchen nach und Diagnostizieren von Leistungsproblemen](../logs/log-query-overview.md)

