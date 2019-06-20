---
title: Azure Application Insights – häufig gestellte Fragen | Microsoft-Dokumentation
description: Häufig gestellte Fragen zu Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mbullwin
ms.openlocfilehash: ab1327b42a76a6e76183d84cb1750cce8b85228f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65604285"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: Häufig gestellte Fragen

## <a name="configuration-problems"></a>Konfigurationsprobleme
*Ich benötige Hilfe beim Einrichten von Folgendem:*

* [.NET-App](asp-net-troubleshoot-no-data.md)
* [Überwachen einer bereits ausgeführten App](monitor-performance-live-website-now.md#troubleshoot)
* [Azure-Diagnose](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Java-Web-App](java-troubleshoot.md)

*Ich erhalte keine Daten von meinem Server.*

* [Festlegen von Firewallausnahmen](ip-addresses.md)
* [Einrichten eines ASP.NET-Servers](monitor-performance-live-website-now.md)
* [Einrichten eines Java-Servers](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Kann ich Application Insights mit ... verwenden?

* [Web-Apps auf einem IIS-Server – lokal oder auf einem virtuellen Computer](asp-net.md)
* [Java-Web-Apps](java-get-started.md)
* [Node.js-Apps](nodejs.md)
* [Web-Apps in Azure](azure-web-apps.md)
* [Cloud Services in Azure](cloudservices.md)
* [In Docker ausgeführte App-Server](docker.md)
* [Einzelseiten-Web-Apps](javascript.md)
* [SharePoint](sharepoint.md)
* [Windows-Desktop-App](windows-desktop.md)
* [Andere Plattformen](platforms.md)

## <a name="is-it-free"></a>Ist es kostenlos?

Ja, zur experimentellen Verwendung. Im Basic-Tarif kann Ihre Anwendung kostenlos ein bestimmtes monatliches Datenkontingent senden. Das kostenlose Kontingent ist groß genug, um Entwicklungsaufgaben auszuführen und eine App für eine geringe Anzahl von Benutzern zu veröffentlichen. Sie können eine Obergrenze festlegen, um sicherzustellen, dass nicht mehr als die angegebene Datenmenge verarbeitet wird.

Größere Mengen von Telemetriedaten werden nach GB in Rechnung gestellt. Wir bieten einige Tipps zum [Begrenzen der Gebühren](pricing.md).

Im Enterprise-Plan fallen für jeden Tag, an dem die einzelnen Web-Serverknoten Telemetriedaten senden, Gebühren an. Er ist geeignet, wenn Sie in großem Umfang fortlaufenden Export verwenden möchten.

Preisinformationen finden Sie [hier](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-is-it-costing"></a>Wie hoch sind die Kosten?

* Öffnen Sie die Seite **Nutzung und geschätzte Kosten** in einer Application Insights-Ressource. Dort finden Sie ein Diagramm der kürzlichen Nutzung. Sie können ggf. eine Obergrenze für das Datenvolumen festlegen.
* Öffnen Sie das Blatt für die [Azure-Abrechnung](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview), um Ihre Rechnungen für alle Ressourcen anzuzeigen.

## <a name="q14"></a>Welche Änderungen nimmt Application Insights in meinem Projekt vor?
Die Details hängen von der Art des Projekts ab. Für eine Webanwendung:

* Fügen Sie folgende Dateien zu Ihrem Projekt hinzu:

  * ApplicationInsights.config
  * ai.js
* Diese NuGet-Pakete werden installiert:

  * *Application Insights API* - die Haupt-API
  * *Application Insights API for Web Applications* - zum Senden von Telemetrie vom Server
  * *Application Insights API for JavaScript Applications* - zum Senden von Telemetrie vom Client

    Die Pakete umfassen folgende Assemblys:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Fügt Elemente ein in:

  * Web.config
  * packages.config
* (Nur neue Projekte – wenn Sie [Application Insights zu einem vorhandenen Webprojekt hinzufügen][start], müssen Sie diesen Schritt manuell ausführen.) Fügen Sie Codeausschnitte in den Client- und Servercode ein, um diese mit der Application Insights-Ressourcen-ID zu initialisieren. Beispielsweise wird in einer MVC-App Code auf der Masterseite "Views/Shared/_Layout.cshtml" eingefügt.

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Wie aktualisiere ich von älteren SDK-Versionen?
Informationen hierzu finden Sie in den [Versionshinweisen](release-notes.md) für das SDK, das für Ihren Anwendungstyp geeignet ist.

## <a name="update"></a>Wie kann ich ändern, an welche Azure-Ressource mein Projekt Daten sendet?
Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Sie können die Daten an eine vorhandene oder neue Ressource in Azure senden. Der Assistent ändert den Instrumentationsschlüssel in ApplicationInsights.config. Dadurch wird bestimmt, wohin das Server-SDK die Daten sendet. Wenn Sie "Alle aktualisieren" deaktivieren, wird auch der Anzeigeort des Schlüssels auf Ihren Webseiten geändert.

## <a name="what-is-status-monitor"></a>Was ist der Statusmonitor?

Eine Desktop-App, die Sie auf Ihrem IIS-Webserver zum Konfigurieren von Application Insights in Web-Apps verwenden können. Sie sammelt keine Telemetriedaten; Sie können sie beenden, wenn Sie keine App konfigurieren. 

[Weitere Informationen](monitor-performance-live-website-now.md#questions)

## <a name="what-telemetry-is-collected-by-application-insights"></a>Welche Telemetriedaten werden von Application Insights gesammelt?

Von Server-Web-Apps:

* HTTP-Anforderungen
* [Abhängigkeiten](asp-net-dependencies.md) Aufrufe an: SQL-Datenbanken, HTTP-Aufrufe an externe Dienste, Aufrufe an Azure Cosmos DB, Tabellen, Blob Storage und Warteschlangen. 
* [Ausnahmen](asp-net-exceptions.md) und Stapelüberwachungen.
* [Leistungsindikatoren](performance-counters.md): Bei Verwendung von [Statusmonitor](monitor-performance-live-website-now.md), [Azure-Überwachung](azure-web-apps.md) oder [collectd-Writer von Application Insights](java-collectd.md).
* [Benutzerdefinierte Ereignisse und Metriken](api-custom-events-metrics.md), die Sie codieren.
* [Ablaufverfolgungsprotokolle](asp-net-trace-logs.md), wenn Sie den entsprechenden Sammler konfigurieren.

Über [Clientwebseiten](javascript.md):

* [Anzahl von Seitenaufrufen](usage-overview.md)
* [AJAX-Aufrufe](asp-net-dependencies.md) – Anforderungen aus einem derzeit ausgeführten Skript.
* Daten zum Laden von Seitenansichten
* Anzahl von Benutzern und Sitzungen
* [IDs authentifizierter Benutzer](api-custom-events-metrics.md#authenticated-users)

Aus anderen Quellen, sofern Sie sie konfigurieren:

* [Azure-Diagnose](../platform/diagnostics-extension-to-application-insights.md)
* [Import in Analytics](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kann ich Telemetriedaten filtern oder ändern?

Ja, auf dem Server können Sie Folgendes schreiben:

* Einen Telemetrieprozessor, um Eigenschaften zu filtern oder ausgewählten Telemetrieelementen hinzuzufügen, bevor sie von der App gesendet werden
* Eine Telemetrieinitialisierer, um allen Telemetrieelementen Eigenschaften hinzuzufügen

Weitere Informationen über [ASP.NET](api-filtering-sampling.md) und [Java](java-filter-telemetry.md).

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Wie werden Daten zu Ort, Land/Region und andere Geolocation-Daten berechnet?

Mit [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) wird die IP-Adresse (IPv4 oder IPv6) des Webclients gesucht.

* Browsertelemetrie: Die IP-Adresse des Absenders wird erfasst.
* Servertelemetrie: Das Application Insights-Modul erfasst die Client-IP-Adresse. Sie wird nicht gesammelt, wenn `X-Forwarded-For` festgelegt ist.

Sie können in der Konfiguration festlegen, dass `ClientIpHeaderTelemetryInitializer` die IP-Adresse aus einem anderen Header akzeptiert. In manchen Systemen wird sie z.B. von einem Proxy, durch den Lastenausgleich oder das CDN nach `X-Originating-IP` verschoben. [Weitere Informationen](https://apmtips.com/blog/2016/07/05/client-ip-address/)

Sie können [mit Power BI](export-power-bi.md ) die Anforderungstelemetriedaten auf einer Karte anzeigen.


## <a name="data"></a>Wie lange werden Daten im Portal aufbewahrt? Ist Sicherheit gewährleistet?
Informationen hierzu finden Sie unter [Datensammlung, -aufbewahrung und -speicherung in Application Insights][data].

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Können beim Senden von Telemetriedaten auch personenbezogene Daten übertragen werden?

Dies ist möglich, wenn der Code solche Daten sendet. Es kann auch vorkommen, wenn Variablen in den Stapelüberwachungen personenbezogene Daten enthalten. Ihr Entwicklungsteam sollte Risikobewertungen durchführen, um sicherzustellen, dass personenbezogene Daten ordnungsgemäß behandelt werden. [Weitere Informationen zu Datenaufbewahrung und Datenschutz](data-retention-privacy.md).

**Alle** Oktette der Clientwebadresse werden immer auf 0 festgelegt, nachdem die Attribute für den geografischen Standort nachgeschlagen wurden.

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Mein Instrumentierungsschlüssel ist in meiner Webseitenquelle sichtbar. 

* Dies ist in Überwachungslösungen üblich.
* Er kann nicht zum Diebstahl Ihrer Daten verwendet werden.
* Er kann verwendet werden, um Datenschiefe zu bewirken oder Warnungen auszulösen.
* Wir wissen von keinem Kunden, bei dem solche Probleme aufgetreten sind.

Sie haben folgende Möglichkeiten, um das Problem auszuschließen:

* Verwenden Sie zwei verschiedene Instrumentierungsschlüssel (unterschiedliche Application Insights-Ressourcen) für Client- und Serverdaten. oder
* Schreiben Sie einen Proxy, der auf dem Server ausgeführt wird, und lassen Sie den Webclient Daten über diesen Proxy senden.

## <a name="post"></a>Wie zeige ich POST-Daten in der Diagnosesuche an?
POST-Daten werden nicht automatisch protokolliert, Sie können jedoch einen TrackTrace-Aufruf verwenden und die Daten in den Nachrichtenparameter einfügen. Die Größenbegrenzung hierfür ist höher als bei Zeichenfolgeneigenschaften, Sie können jedoch nicht danach filtern.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Sollte ich eine einzelne oder mehrere Application Insights-Ressourcen verwenden?

Verwenden Sie eine einzelne Ressource für alle Komponenten oder Rollen in einem einzelnen Geschäftssystem. Verwenden Sie separate Ressourcen für Entwicklungs-, Test- und Releaseversionen und für unabhängige Anwendungen.

* [Konsultieren Sie die Erörterung hier](separate-resources.md).
* [Beispiel – Clouddienst mit Worker- und Webrolle](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Wie kann ich den Instrumentationsschlüssel dynamisch ändern?

* [Die Erläuterung finden Sie hier](separate-resources.md).
* [Beispiel – Clouddienst mit Worker- und Webrolle](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Was sind Benutzer- und Sitzungszähler?

* Das JavaScript-SDK legt im Webclient ein Benutzercookie zum Identifizieren wiederkehrender Benutzer und ein Sitzungscookie zum Gruppieren von Aktivitäten fest.
* Wenn kein clientseitiges Skript vorhanden ist, können Sie [Cookies auf dem Server festlegen](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Wenn ein realer Benutzer Ihre Website in verschiedenen Browsern, beim InPrivate- oder Inkognito-Browsing oder auf unterschiedlichen Computern verwendet, wird er mehrmals gezählt.
* Um einen angemeldeten Benutzer auf verschiedenen Computern und in verschiedenen Browsern zu identifizieren, fügen Sie einen Aufruf von [setAuthenticatedUserContext()](api-custom-events-metrics.md#authenticated-users) hinzu.

## <a name="q17"></a> Habe ich alles in Application Insights aktiviert?
| Diese Daten sollten angezeigt werden | So erhalten Sie die Daten | Deshalb benötigen Sie die Daten |
| --- | --- | --- |
| Verfügbarkeitsdiagramme |[Webtests](monitor-web-app-availability.md) |Information, ob Ihre Web-App verfügbar ist |
| Leistung der Server-App: Antwortzeiten usw. ... |[Fügen Sie Ihrem Projekt Application Insights hinzu](asp-net.md) oder [installieren Sie den AI-Statusmonitor auf dem Server](monitor-performance-live-website-now.md) (oder schreiben Sie Ihren eigenen Code zum [Nachverfolgung von Aufrufen](api-custom-events-metrics.md#trackdependency)) |Erkennen von Leistungsproblemen |
| Telemetriedaten zu Abhängigkeiten |[Installieren des AI-Statusmonitors auf dem Server](monitor-performance-live-website-now.md) |Diagnostizieren von Problemen mit Datenbanken oder anderen externen Komponenten |
| Abrufen von Stapelüberwachungen aus Ausnahmen |[Einfügen von TrackException-Aufrufen in Ihren Code](asp-net-exceptions.md) (einige werden jedoch automatisch gemeldet) |Erkennen und Diagnostizieren von Ausnahmen |
| Durchsuchen von Ablaufprotokollen |[Hinzufügen eines Protokollierungsadapters](asp-net-trace-logs.md) |Diagnostizieren von Ausnahmen und Leistungsproblemen |
| Grundlegende Clientnutzung: Seitenansichten, Sitzungen, ... |[JavaScript-Initialisierer in Webseiten](javascript.md) |Nutzungsanalyse |
| Benutzerdefinierte Metriken auf Clients |[Nachverfolgen von Aufrufen in Webseiten](api-custom-events-metrics.md) |Verbessern der Benutzerfreundlichkeit |
| Benutzerdefinierte Metriken auf Servern |[Nachverfolgen von Aufrufen auf dem Server](api-custom-events-metrics.md) |Business Intelligence |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Warum sind die Zahlen in den Diagrammen „Suchen“ und „Metriken“ unterschiedlich?

Durch [Sampling](sampling.md) wird die Anzahl der Telemetrieelemente (Anforderungen, benutzerdefinierte Ereignisse usw.), die tatsächlich von der App an das Portal gesendet werden, verringert. In „Suchen“ wir die Anzahl der tatsächlich empfangenen Elemente angezeigt. In Metrikdiagrammen, in denen die Anzahl der Ereignisse angezeigt wird, wird die Anzahl der ursprünglich eingetretenen Ereignisse angezeigt. 

Jedes übertragene Element verfügt über eine `itemCount`-Eigenschaft, die angibt, wie viele ursprüngliche Ereignisse das Element darstellt. Sie können die folgende Abfrage in Analytics ausführen, um zu sehen, wie Sampling erfolgt:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Automation

### <a name="configuring-application-insights"></a>Konfigurieren von Application Insights

Sie können mit dem Azure-Ressourcenmonitor [PowerShell-Skripts schreiben](powershell.md), um folgende Aufgaben auszuführen:

* Erstellen und Aktualisieren von Application Insights-Ressourcen
* Festlegen des Tarifs
* Abrufen des Instrumentierungsschlüssels
* Hinzufügen einer Metrikwarnung
* Hinzufügen eines Verfügbarkeitstests

Sie können keinen Metrik-Explorer-Bericht oder fortlaufende Exporte einrichten.

### <a name="querying-the-telemetry"></a>Abfragen der Telemetriedaten

Verwenden Sie die [REST-API](https://dev.applicationinsights.io/) zum Ausführen von [Analytics](analytics.md)-Abfragen.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Wie kann ich eine Warnung für ein Ereignis festlegen?

Azure-Warnungen gelten nur für Metriken. Erstellen Sie eine benutzerdefinierte Metrik, die immer einen Schwellenwert überschreitet, wenn das Ereignis eintritt. Legen Sie dann eine Warnung für die Metrik fest. Beachten Sie Folgendes: Sie erhalten immer eine Benachrichtigung, wenn die Metrik den Schwellenwert in einer der beiden Richtungen überschreitet. Vor der ersten Überschreitung erhalten Sie keine Benachrichtigung, unabhängig von der Höhe des Anfangswerts. Vor der Benachrichtigung liegt immer eine Wartezeit von einigen Minuten.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Fallen Gebühren für die Datenübertragung zwischen einer Azure-Web-App und Application Insights an?

* Wenn Ihre Azure-Web-App in einem Rechenzentrum gehostet wird, die einen Application Insights-Sammlungsendpunkt enthält, fallen keine Gebühren an. 
* Wenn das Hostrechenzentrum keinen Sammlungsendpunkt enthält, fallen für die Telemetriedaten [Azure-Gebühren für ausgehende Daten](https://azure.microsoft.com/pricing/details/bandwidth/) an.

Dies gilt unabhängig davon, wo Ihre Application Insights-Ressource gehostet wird. Es hängt nur von der Verteilung unserer Endpunkte ab.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kann ich Telemetriedaten an das Application Insights-Portal senden?

Es wird empfohlen, unsere SDKs und die [SDK-API](api-custom-events-metrics.md) zu verwenden. Es gibt Varianten des SDK für verschiedene [Plattformen](platforms.md). Diese SDKs behandeln Pufferung, Komprimierung, Drosselung, Wiederholungen usw. Das [Erfassungsschema](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) und das [Endpunktprotokoll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) sind jedoch öffentlich.

## <a name="can-i-monitor-an-intranet-web-server"></a>Kann ich einen Intranetwebserver überwachen?

Ja, aber Sie müssen den Datenverkehr zu unseren Diensten entweder über Firewallausnahmen oder Proxyweiterleitungen zulassen.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Die vollständige Liste der Dienste und IP-Adressen finden Sie [hier](../../azure-monitor/app/ip-addresses.md).

### <a name="firewall-exception"></a>Firewallausnahme

Ermöglichen Sie Ihrem Webserver das Senden von Telemetriedaten an unsere Endpunkte. 

### <a name="gateway-redirect"></a>Gateway-Umleitung

Leiten Sie Datenverkehr von Ihrem Server an ein Gateway im Intranet weiter, indem Sie Endpunkte in Ihrer Konfiguration überschreiben.
Wenn diese „Endpoint“-Eigenschaften in Ihrer Konfiguration nicht vorhanden sind, verwenden diese Klassen die in der Beispieldatei „ApplicationInsights.config“ unten gezeigten Standardwerte. 

Ihr Gateway muss Datenverkehr an die Basisadresse unseres Endpunkts weiterleiten. Ersetzen Sie in Ihrer Konfiguration die Standardwerte durch `http://<your.gateway.address>/<relative path>`.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Beispieldatei „ApplicationInsights.config“ mit Standardendpunkten:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

_Beachten Sie, dass ApplicationIdProvider ab v2.6.0 verfügbar ist_

### <a name="proxy-passthrough"></a>Proxy-Passthrough

Ein Proxy-Passthrough kann erreicht werden, indem entweder ein Proxy auf Computerebene oder auf Anwendungsebene konfiguriert wird.
Weitere Informationen finden Sie im Dotnet-Artikel zu [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Beispieldatei „Web.config“:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kann ich auf einem Intranetserver Verfügbarkeitswebtests ausführen?

Unsere [Webtests](monitor-web-app-availability.md) werden auf Points of Presence aufgeführt, die über den gesamten Globus verteilt sind. Es gibt zwei Lösungen:

* Tür in der Firewall – Lassen Sie Anforderungen von der [umfangreichen und änderbaren Liste von Webtest-Agents](ip-addresses.md) an Ihren Server zu.
* Schreiben Sie eigenen Code, um aus dem Intranet regelmäßig Anforderungen an den Server zu senden. Sie können zu diesem Zweck Visual Studio-Webtests ausführen. Der Tester kann die Ergebnisse mithilfe der TrackAvailability()-API an Application Insights senden.

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Wie lange dauert das Sammeln von Telemetriedaten?

Die meisten Application Insights-Daten weisen eine Wartezeit von weniger als 5 Minuten auf. Bei einigen Daten kann der Zeitraum länger sein. Ein typisches Beispiel hierfür sind größere Protokolldateien. Weitere Informationen finden Sie in der [SLA für Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Weitere Antworten
* [Application Insights-Forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
