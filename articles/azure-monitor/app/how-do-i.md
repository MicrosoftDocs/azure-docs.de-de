---
title: Gewusst wie ... in Azure Application Insights | Microsoft-Dokumentation
description: Häufig gestellte Fragen in Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: mbullwin
ms.openlocfilehash: 5e22a3f3b362811fd87460ec41b61a990f4d83fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60902058"
---
# <a name="how-do-i--in-application-insights"></a>Gewusst wie – in Application Insights
## <a name="get-an-email-when-"></a>Wie erhalte ich eine E-Mail-Nachricht, wenn...
### <a name="email-if-my-site-goes-down"></a>E-Mail, wenn die Website nicht mehr funktioniert
Richten Sie einen [Webtest zur Verfügbarkeit](../../azure-monitor/app/monitor-web-app-availability.md)ein.

### <a name="email-if-my-site-is-overloaded"></a>E-Mail, wenn meine Website überlastet ist
Richten Sie eine [Warnung](../../azure-monitor/app/alerts.md) zur **Serverantwortzeit**ein. Geeignet ist in der Regel ein Schwellenwert zwischen 1 und 2 Sekunden.

![](./media/how-do-i/030-server.png)

Auch die Rückgabe von Fehlercodes kann ein Hinweis auf die Belastung Ihrer App sein. Richten Sie eine Warnung über **Anforderungsfehler**ein.

Wenn Sie eine Warnung für **Serverausnahmen**einrichten möchten, müssen Sie möglicherweise [einige zusätzliche Konfigurationsschritte](../../azure-monitor/app/asp-net-exceptions.md) vornehmen, damit die Daten einsehbar sind.

### <a name="email-on-exceptions"></a>E-Mail für Ausnahmen
1. [Einrichten der Ausnahmeüberwachung](../../azure-monitor/app/asp-net-exceptions.md)
2. [Einrichten einer Warnung](../../azure-monitor/app/alerts.md) für die Metrik der Anzahl von Ausnahmen

### <a name="email-on-an-event-in-my-app"></a>E-Mail über ein Ereignis in meiner App
Angenommen, Sie möchten eine E-Mail erhalten, wenn ein bestimmtes Ereignis eintritt. Diese Funktion ist in Application Insights nicht in dieser Form verfügbar, aber es kann [eine Warnung gesendet werden, wenn eine Metrik einen Schwellenwert überschreitet](../../azure-monitor/app/alerts.md).

Warnungen können für [benutzerdefinierte Metriken](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), jedoch nicht für benutzerdefinierte Ereignisse eingerichtet werden. Mithilfe von Code können Sie eine Metrik erhöhen, wenn das Ereignis eintritt:

    telemetry.TrackMetric("Alarm", 10);

oder:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Da Warnungen zwei Zustände aufweisen, müssen Sie einen niedrigen Wert senden, wenn das Ende der Warnung erreicht ist:

    telemetry.TrackMetric("Alarm", 0.5);

Erstellen Sie ein Diagramm im [Metrik-Explorer](../../azure-monitor/app/metrics-explorer.md) , um Ihre Warnung anzuzeigen:

![](./media/how-do-i/010-alarm.png)

Richten Sie jetzt eine Warnung ein, die ausgelöst wird, wenn die Metrik für kurze Zeit einen mittleren Wert überschreitet:

![](./media/how-do-i/020-threshold.png)

Legen Sie den Durchschnittszeitraum auf den Mindestwert fest.

Sie erhalten E-Mails, wenn die Metrik den Schwellenwert überschreitet und auch unterschreitet.

Zu berücksichtigende Punkte:

* Eine Warnung verfügt über zwei Zustände "Warnung" und "Fehlerfrei". Der Status wird nur ausgewertet, wenn eine Metrik empfangen wird.
* Nur bei einer Änderung des Status wird eine E-Mail gesendet. Aus diesem Grund müssen Sie Metriken mit sowohl hohen als auch niedrigen Werten senden.
* Zur Auswertung der Warnung wird der Durchschnitt der empfangenen Werte über den vorherigen Zeitraum zugrunde gelegt. Dies erfolgt immer dann, wenn eine Metrik empfangen wird, sodass E-Mails öfter gesendet werden können, als durch den angegebenen Zeitraum festgelegt.
* Da sowohl für den Zustand "Warnung" als auch für den Zustand "Fehlerfrei" E-Mails gesendet werden, sollten Sie das einmalige Ereignis als Vorfall mit zwei Zuständen betrachten. Beispiel: Statt eines Ereignisses mit dem Status "Abgeschlossen" verwenden Sie den Zustand "In Bearbeitung" und erhalten E-Mails am Anfang und Ende des betreffenden Auftrags.

### <a name="set-up-alerts-automatically"></a>Automatisches Einrichten von Warnungen
[Erstellen neuer Warnungen mithilfe von PowerShell](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Verwalten von Application Insights mithilfe von PowerShell
* [Erstellen neuer Ressourcen](../../azure-monitor/app/powershell-script-create-resource.md)
* [Erstellen neuer Warnungen](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Trennen der Telemetriedaten von verschiedenen Versionen

* Mehrere Rollen in einer App: Verwenden Sie eine einzelne Application Insights-Ressource, und filtern Sie nach [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Trennen von Entwicklungs-, Test- und Produktionsversionen: Verwenden Sie verschiedene Application Insights-Ressourcen. Übernehmen Sie die Instrumentierungsschlüssel aus „web.config“. [Weitere Informationen](../../azure-monitor/app/separate-resources.md)
* Berichterstellung für Buildversionen: Fügen Sie mithilfe eines Telemetrieinitialisierers eine Eigenschaft hinzu. [Weitere Informationen](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Überwachen von Back-End-Servern und Desktop-Apps
[Verwenden Sie das Windows Server-SDK-Modul](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Visualisieren von Daten
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Dashboard mit Metriken aus mehreren Apps
* Passen Sie Ihr Diagramm im [Metrik-Explorer](../../azure-monitor/app/metrics-explorer.md)an, und speichern Sie es als Favorit. Heften Sie es an das Azure-Dashboard an.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Dashboard mit Daten aus anderen Quellen und Application Insights
* [Exportieren Sie die Telemetrie in Power BI](../../azure-monitor/app/export-power-bi.md ).

oder

* Verwenden Sie SharePoint als Dashboard, und zeigen Sie Daten in SharePoint-Webparts an. [Nutzen Sie fortlaufenden Export und Stream Analytics für den Export in SQL](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Überprüfen Sie die Datenbank mithilfe von PowerView, und erstellen Sie ein SharePoint-Webpart für PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Herausfiltern anonymer oder authentifizierter Benutzer
Wenn die Benutzer sich anmelden, können Sie die [ID für authentifizierte Benutzer](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)festlegen. (Dies erfolgt nicht automatisch.)

Anschließend können Sie folgende Aktionen ausführen:

* Suchen nach bestimmten Benutzer-IDs

![](./media/how-do-i/110-search.png)

* Filtern von Metriken für anonyme oder authentifizierte Benutzer

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Ändern von Eigenschaftennamen oder -werten
Erstellen Sie einen [Filter](../../azure-monitor/app/api-filtering-sampling.md#filtering). So können Sie Telemetrie ändern oder filtern, bevor sie von Ihrer App aus an Application Insights gesendet wird.

## <a name="list-specific-users-and-their-usage"></a>Auflisten bestimmter Benutzer und deren Nutzung
Wenn Sie nur [nach bestimmten Benutzern suchen möchten](#search-specific-users), können Sie die [ID für authentifizierte Benutzer](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) festlegen.

Wenn Sie eine Liste von Benutzern mit bestimmten Daten erstellen möchten, z. B., welche Seiten sich die Benutzer ansehen oder wie oft sie sich anmelden, haben Sie zwei Möglichkeiten:

* [Legen Sie die ID für authentifizierte Benutzer fest](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), [exportieren Sie die Daten in eine Datenbank](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md), und analysieren Sie dort die Benutzerdaten mit den entsprechenden Tools.
* Wenn Sie nur über eine kleine Anzahl von Benutzern verfügen, senden Sie benutzerdefinierte Ereignisse oder Metriken, verwenden Sie dabei die relevanten Daten als Metrikwert oder Ereignisname, und legen Sie die Benutzer-ID als Eigenschaft fest. Um Seitenansichten zu analysieren, ersetzen Sie den standardmäßigen JavaScript-trackPageView-Aufruf. Um die serverseitige Telemetrie zu analysieren, fügen Sie die Benutzer-ID mithilfe eines Telemetrieinitialisierers allen Servertelemetriedaten hinzu. Sie können dann Metriken und Suchvorgänge nach der Benutzer-ID filtern und segmentieren.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Reduzierung des Datenverkehrs von einer App an Application Insights
* Deaktivieren Sie in [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) alle nicht benötigten Module, z.B. die Leistungsindikatorsammlung.
* Verwenden Sie im SDK [Stichprobenerstellung und Filterung](../../azure-monitor/app/api-filtering-sampling.md) .
* Begrenzen Sie für Ihre Webseiten die Anzahl der AJAX-Aufrufe, die für jede Seitenansicht gemeldet werden. Fügen Sie im Skriptausschnitt hinter `instrumentationKey:...` Folgendes ein: `,maxAjaxCallsPerView:3` (oder eine geeignete Anzahl).
* Berechnen Sie bei Verwendung von [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)das Aggregat der Batches von Metrikwerten vor dem Senden des Ergebnisses. Eine Überladung von TrackMetric() ist dafür vorgesehen.

Erfahren Sie mehr über [Preise und Kontingente](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Telemetrie deaktivieren
So können Sie die Sammlung und Übermittlung von Telemetriedaten aus dem Server **dynamisch beenden und starten** :

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



Um **ausgewählte Standardsammlungsmodule zu deaktivieren**, beispielsweise Leistungsindikatoren, HTTP-Anforderungen oder Abhängigkeiten, löschen Sie die entsprechenden Zeilen in [ApplicationInsights.config](../../azure-monitor/app/api-custom-events-metrics.md), oder kommentieren Sie sie aus. Diese Vorgehensweise bietet sich z. B. an, wenn Sie Ihre eigenen TrackRequest-Daten senden möchten.

## <a name="view-system-performance-counters"></a>Anzeigen von Systemleistungsindikatoren
Zu den Metriken, die Sie im Metrik-Explorer anzeigen können, zählt u. a. eine Gruppe von Systemleistungsindikatoren. Mehrere dieser Leistungsindikatoren werden auf dem vordefinierten Blatt **Server** angezeigt.

![Öffnen Sie die Application Insights-Ressource, und klicken Sie auf "Server".](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Wenn keine Leistungsindikatordaten angezeigt werden
* **IIS-Server** auf Ihrem eigenen oder auf einem virtuellen Computer. [Installieren Sie den Statusmonitor](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Azure-Website** – Leistungsindikatoren werden noch nicht unterstützt. Sie können jedoch mehrere Metriken über die Systemsteuerung der Azure-Website standardmäßig abrufen.
* **Unix-Server** - [Installieren Sie collectd](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Anzeigen weiterer Leistungsindikatoren
* Fügen Sie zunächst [ein neues Diagramm hinzu](../../azure-monitor/app/metrics-explorer.md) , und prüfen Sie dann, ob der gewünschte Leistungsindikator im angebotenen grundlegenden Satz enthalten ist.
* Wenn dies nicht der Fall ist, [fügen Sie den Leistungsindikator dem über das Leistungsindikatormodul erfassten Satz hinzu](../../azure-monitor/app/performance-counters.md).
