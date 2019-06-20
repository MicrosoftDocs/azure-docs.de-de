---
title: Schnellstart mit Azure Application Insights | Microsoft-Dokumentation
description: Dieser Artikel bietet Informationen zum schnellen Einrichten einer Java-Web-App für die Überwachung mit Application Insights.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.reviewer: lagayhar
ms.date: 04/18/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: dd1644ad9b7fcee951b31997ab549f117530f635
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808378"
---
# <a name="start-monitoring-your-java-web-application"></a>Starten der Überwachung Ihrer Java-Webanwendung

Mit Azure Application Insights können Sie die Verfügbarkeit, Leistung und Nutzung Ihrer Webanwendung ganz einfach überwachen. Sie können auch Fehler in Ihrer Anwendung schnell erkennen und diagnostizieren, ohne darauf warten zu müssen, dass diese Fehler von Benutzern gemeldet werden. Mit dem Application Insights Java SDK können Sie allgemeine Drittanbieterpakete einschließlich MongoDB, MySQL und Redis überwachen.

Diese Schnellstartanleitung führt Sie durch die notwendigen Schritte, um das Application Insights SDK zu einem vorhandenen Java Dynamic Web Project hinzuzufügen.

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie diesen Schnellstart durch:

- Installieren Sie JRE 1.7 oder 1.8.
- Installieren Sie die [kostenlose Eclipse-IDE für Java EE-Entwickler](https://www.eclipse.org/downloads/). In diesem Schnellstart wird Eclipse Oxygen (4.7) verwendet.
- Sie benötigen ein Azure-Abonnement und ein vorhandenes Java Dynamic Web Project.
 
Wenn Sie noch nicht über ein Java Dynamic Web Project verfügen, können Sie mithilfe der Schnellstartanleitung unter [Erstellen einer Java-Web-App](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-java) eines erstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Wenn Sie das Spring-Framework bevorzugen, lesen Sie den Leitfaden zum [Konfigurieren einer Spring Boot Initializer-App für die Verwendung von Application Insights](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="enable-application-insights"></a>Aktivieren von Application Insights

Application Insights kann Telemetriedaten von jeder mit dem Internet verbundenen Anwendung erfassen, unabhängig davon, ob die Anwendung lokal oder in der Cloud ausgeführt wird. Geben Sie folgendermaßen vor, um diese Daten anzuzeigen.

1. Klicken Sie auf **Ressource erstellen** > **Entwicklertools** > **Application Insights**.

   ![Hinzufügen einer Application Insights-Ressource](./media/java-quick-start/1createresourseappinsights.png)

   ![Hinzufügen einer Application Insights-Ressource](./media/java-quick-start/2createjavaapp.png)

   Ein Dialogfeld für die Konfiguration wird geöffnet. Füllen Sie die Eingabefelder anhand der Informationen in der folgenden Tabelle aus:

    | Einstellungen        | Wert           | BESCHREIBUNG  |
   | ------------- |:-------------|:-----|
   | **Name**      | Global eindeutiger Wert | Der Name, der die zu überwachende App identifiziert. |
   | **Anwendungstyp** | Java-Webanwendung | Der Typ der zu überwachenden App. |
   | **Ressourcengruppe**     | myResourceGroup      | Der Name der neuen Ressourcengruppe, die Application Insights-Daten hosten soll. |
   | **Location** | USA (Ost) | Wählen Sie einen Standort in Ihrer Nähe oder in der Nähe des Standorts, in dem Ihre App gehostet wird. |

2. Klicken Sie auf **Create**.

## <a name="install-app-insights-plugin"></a>Installieren des Application Insights-Plug-Ins

1. Starten Sie **Eclipse**. Klicken Sie auf **Help** (Hilfe), und wählen Sie **Install New Software** (Neue Software installieren) aus.

   ![Formular für neue App Insights-Ressource](./media/java-quick-start/000-j.png)

2. Kopieren Sie ```https://dl.microsoft.com/eclipse``` in das Feld „Work With“ (Arbeiten mit). Aktivieren Sie das Kontrollkästchen **Azure Toolkit for Java** (Azure-Toolkit für Java). Wählen Sie **Application Insights Plugin for Java**  >  (Application Insights-Plug-In für Java), und **deaktivieren** Sie das Kontrollkästchen „Contact all update sites during install to find required software“ (Während der Installation alle Updatesites kontaktieren, um erforderliche Software zu finden).

3. Sobald die Installation abgeschlossen ist, wird die Aufforderung **Restart Eclipse** (Eclipse neu starten) angezeigt.

## <a name="configure-app-insights-plugin"></a>Konfigurieren des Application Insights-Plug-Ins

1. Starten Sie **Eclipse**. Öffnen Sie Ihr **Project** (Projekt), und klicken Sie mit der rechten Maustaste auf den **Project Explorer** (Projekt-Explorer). Wählen Sie **Azure** aus, und klicken Sie auf **Sign In** (Anmelden).

2. Wählen Sie die Authentifizierungsmethode **Interactive** (Interaktiv) aus, und klicken Sie auf **Sign In** (Anmelden). Wenn Sie dazu aufgefordert werden, geben Sie Ihre Azure-Anmeldeinformationen (**Azure credentials**) ein, und wählen Sie Ihr Azure-Abonnement (**Azure Subscription**) aus.

3. Klicken Sie mit der rechten Maustaste im **Project Explorer** (Projekt-Explorer) auf den Namen Ihres Projekts, und wählen Sie **Azure** aus. Klicken Sie auf **Configure Application Insights** (Application Insights konfigurieren).

4. Aktivieren Sie das Kontrollkästchen **Enable telemetry with Application Insights** (Telemetrie mit Application Insights aktivieren). Wählen Sie die App Insights-Ressource und den zugehörigen **Instrumentation Key** (Instrumentierungsschlüssel) aus, die bzw. den Sie mit Ihrer Java-App verknüpfen möchten.

   ![Eclipse-Konfigurationsmenü für Azure](./media/java-quick-start/0007-j.png)

5. Nach dem Konfigurieren des Application Insights-Plug-Ins müssen Sie Ihre Anwendung [veröffentlichen/erneut veröffentlichen](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-java#deploy-the-app), damit sie Telemetriedaten senden kann.

> [!NOTE]
> Das Application Insights SDK für Java kann Livemetriken sammeln und visualisieren. Bei der ersten Aktivierung der Telemetriedatensammlung kann es jedoch einige Minuten dauern, bis Daten im Portal angezeigt werden. Wenn es sich um eine Test-App mit geringem Datenverkehr handelt, denken Sie daran, dass die meisten Metriken nur erfasst werden, wenn aktive Anforderungen oder Vorgänge vorhanden sind.

## <a name="start-monitoring-in-the-azure-portal"></a>Starten der Überwachung im Azure-Portal

1. Sie können jetzt im Azure-Portal erneut die Seite **Übersicht** für Application Insights öffnen, um Details zu Ihrer aktuell ausgeführten Anwendung anzuzeigen.

   ![Application Insights – Menü „Übersicht“](./media/java-quick-start/3overview.png)

2. Klicken Sie auf **Anwendungsübersicht**, um ein visuelles Layout der Abhängigkeitsbeziehungen zwischen den Komponenten Ihrer Anwendung zu erhalten. Jede Komponente zeigt KPIs wie z.B. Last, Leistung, Fehler und Warnungen an.

   ![Anwendungszuordnung](./media/java-quick-start/4appmap.png)

3.  Klicken Sie auf das Symbol für die **App-Analyse**: ![Symbol „Anwendungszuordnung“](./media/java-quick-start/006.png) **In Analytics anzeigen**.  Dadurch wird die **Application Insights-Analyse** geöffnet, die eine erweiterte Abfragesprache zum Analysieren aller Daten bereitstellt, die von Application Insights gesammelt werden. In diesem Fall wird eine Abfrage für Sie generiert, die die Anzahl von Anforderungen als Diagramm darstellt. Sie können selbst Abfragen zum Analysieren anderer Daten schreiben.

   ![Analysediagramm der Benutzeranforderungen in einem bestimmten Zeitraum](./media/java-quick-start/5analytics.png)

4. Kehren Sie zur Seite **Übersicht** zurück, und untersuchen Sie die KPI-Graphen. Dieses Dashboard zeigt Statistiken zur Integrität Ihrer Anwendung, einschließlich der Anzahl von eingehenden Anforderungen, der Dauer dieser Anforderungen und aller auftretenden Fehler.

   ![Diagramm der Übersichtszeitachse für die Integrität](./media/java-quick-start/6kpidashboards.png)

   Damit das Diagramm **Ladezeit der Seitenansicht** mit **clientseitigen Telemetriedaten** aufgefüllt werden kann, fügen Sie dieses Skript zu jeder Seite hinzu, die Sie nachverfolgen möchten:

   ```HTML
   <!-- 
   To collect user behavior analytics about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
     function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
    }({
        instrumentationKey:"<instrumentation key>"
    });

    window.appInsights=appInsights;
    appInsights.trackPageView();
   </script>
    ```

5. Klicken Sie auf **Live Stream**. Hier finden Sie Livemetriken in Zusammenhang mit der Leistung Ihrer Java-Web-App. Der **Live Metrics Stream** enthält Daten wie die Anzahl von eingehenden Anforderungen, die Dauer dieser Anforderungen sowie jegliche auftretenden Fehler. Sie können auch kritische Leistungsmetriken wie Prozessor und Arbeitsspeicher in Echtzeit überwachen.

   ![Servermetriken – Diagramm](./media/java-quick-start/7livemetrics.png)

Weitere Informationen zum Überwachen von Java finden Sie in der [weiteren Dokumentation zu Application Insights und Java](./../../azure-monitor/app/java-get-started.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Tests abgeschlossen haben, können Sie die Ressourcengruppe und alle dazugehörigen Ressourcen löschen. Gehen Sie dazu wie folgt vor:

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen** und dann auf **myResourceGroup**.
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld **myResourceGroup** ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Suchen nach und Diagnostizieren von Leistungsproblemen](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)