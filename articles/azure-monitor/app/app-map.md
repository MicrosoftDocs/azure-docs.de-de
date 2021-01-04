---
title: Anwendungszuordnung in Azure Application Insights | Microsoft-Dokumentation
description: Überwachen von komplexen Anwendungstopologien mit der Anwendungsübersicht
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: devx-track-csharp
ms.reviewer: sdash
ms.openlocfilehash: 3383b4a3c2eab1f62d180c31e278f07b92c649c5
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853514"
---
# <a name="application-map-triage-distributed-applications"></a>Anwendungsübersicht: Selektieren verteilter Anwendungen

Mit der Anwendungsübersicht können Sie Leistungsengpässe oder Fehlerhotspots in allen Komponenten Ihrer verteilten Anwendung erkennen. Jeder Knoten in der Übersicht repräsentiert eine Anwendungskomponente bzw. deren Abhängigkeiten und weist einen Key Performance Indicator (KPI) zur Integrität und einen Warnungsstatus auf. Für jede Komponente können Sie detailliertere Diagnosen anzeigen, z.B. für Application Insights-Ereignisse. Wenn Ihre App Azure-Dienste nutzt, können Sie auch Azure-Diagnosedaten anzeigen, z.B. SQL Database Advisor-Empfehlungen.

## <a name="what-is-a-component"></a>Was ist eine Komponente?

Komponenten sind unabhängig bereitstellbare Teile Ihrer verteilten Anwendung/Microserviceanwendung. Entwickler und Betriebsteams verfügen über Sichtbarkeit oder Zugriff für die von diesen Anwendungskomponenten generierte Telemetrie auf Codeebene. 

* Komponenten unterscheiden sich von „beobachteten“ externen Abhängigkeiten, wie SQL, EventHub usw., auf die Ihr Team/Ihre Organisation möglicherweise keinen Zugriff hat (Code oder Telemetrie).
* Komponenten können in einer beliebigen Anzahl von Server-/Rollen-/Containerinstanzen ausgeführt werden.
* Komponenten können separate Application Insights-Instrumentierungsschlüssel (sogar in verschiedenen Abonnements) oder verschiedene Rollen aufweisen, die alle an einen einzelnen Application Insights-Instrumentierungsschlüssel berichten. In der Vorschau werden die Komponenten unabhängig von der Art ihrer Einrichtung in der Übersicht angezeigt.

## <a name="composite-application-map"></a>Verbundanwendungsübersicht

Sie können die vollständige Anwendungstopologie über mehrere Ebenen verwandter Anwendungskomponenten hinweg anzeigen. Bei Komponenten kann es sich um verschiedene Application Insights-Ressourcen oder verschiedene Rollen in einer einzelnen Ressource handeln. Die App-Übersicht ermittelt Komponenten, indem sie HTTP-Abhängigkeitsaufrufe zwischen Servern verfolgt, auf denen das Application Insights SDK installiert ist. 

Diese Funktionalität beginnt mit einer progressiven Ermittlung der Komponenten. Wenn Sie die Anwendungsübersicht zum ersten Mal laden, wird eine Reihe von Abfragen ausgelöst, um die Komponenten zu ermitteln, die mit dieser Komponente in Zusammenhang stehen. Mit einer Schaltfläche in der oberen linken Ecke wird die Anzahl der Komponenten in Ihrer Anwendung aktualisiert, wenn diese ermittelt werden. 

Durch Klicken auf „Übersichtskomponenten aktualisieren“ wird die Übersicht mit allen bis zu diesem Zeitpunkt ermittelten Komponenten aktualisiert. Der Ladevorgang kann je nach Komplexität der Anwendung etwas dauern.

Wenn es sich bei allen Komponenten um Rollen in einer einzelnen Application Insights-Ressource handelt, ist dieser Ermittlungsschritt nicht erforderlich. Nach dem ersten Ladevorgang für eine solche Anwendung sind alle Komponenten vorhanden.

![Screenshot: Beispiel für eine Anwendungszuordnung](media/app-map/app-map-001.png)

Eines der wichtigsten Ziele dieser Oberfläche ist es, komplexe Topologien mit Hunderten von Komponenten zu visualisieren.

Klicken Sie auf eine beliebige Komponente, um die zugehörigen Details anzuzeigen und zur Selektierung von Leistungs- und Fehleraspekten für diese Komponente zu wechseln.

![Flyout](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Untersuchen von Fehlern

Wählen Sie **Fehler untersuchen**, um den Bereich „Fehler“ zu starten.

![Screenshot: Schaltfläche „Fehler untersuchen“](media/app-map/investigate-failures.png)

![Screenshot: Bereich „Fehler“](media/app-map/failures.png)

### <a name="investigate-performance"></a>Untersuchen der Leistung

Wählen Sie **Leistung untersuchen** aus, um Probleme mit der Leistung zu behandeln.

![Screenshot: Schaltfläche „Leistung untersuchen“](media/app-map/investigate-performance.png)

![Screenshot: Benutzeroberfläche für Leistung](media/app-map/performance.png)

### <a name="go-to-details"></a>Zu Details wechseln

Wählen Sie **Zu Details wechseln** aus, um das End-to-End-Transaktionserlebnis zu untersuchen, das Ansichten bis auf die Aufruflistenebene bereitstellen kann.

![Screenshot: Schaltfläche „Zu Details wechseln“](media/app-map/go-to-details.png)

![Screenshot: Details von End-to-End-Transaktionen](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Anzeigen von Protokollen (Analytics)

Klicken Sie auf **In Protokollen anzeigen (Analytics)** , um Ihre Anwendungsdaten abzufragen und eingehender zu untersuchen.

![Screenshot: Schaltfläche „In Analytics anzeigen“](media/app-map/view-logs.png)

![Screenshot: Analytics-Bereich Liniendiagramm, das die durchschnittliche Antwortdauer einer Anforderung in den letzten 12 Stunden zusammenfasst.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Alerts

Wählen Sie **Warnungen** aus, um aktive Warnungen und die zugrunde liegenden Regeln anzuzeigen, die zur Ausführung der Warnungen führen.

![Screenshot: Schaltfläche „Warnungen“](media/app-map/alerts.png)

![Screenshot: Analytics-Bereich](media/app-map/alerts-view.png)

## <a name="set-or-override-cloud-role-name"></a>Festlegen oder Außerkraftsetzen des Cloudrollennamens

In der Anwendungsübersicht wird die Eigenschaft **Cloudrollenname** verwendet, um die Komponenten in der Zuordnung zu identifizieren. So können Sie den Cloudrollennamen manuell festlegen oder außer Kraft setzen und den Inhalt der Anwendungsübersicht ändern:

> [!NOTE]
> Das Application Insights SDK oder der Application Insights-Agent versieht die von Komponenten in einer Azure App Service-Umgebung ausgegebenen Telemetriedaten automatisch mit der Eigenschaft „Cloudrollenname“.

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

**Schreiben Sie einen benutzerdefinierten Telemetrie-Initialisierer wie nachfolgend gezeigt.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET-Apps: Laden Sie den Initialisierer in die aktive TelemetryConfiguration.**

In „ApplicationInsights.config“:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

Alternativ können Sie für ASP.NET-Web-Apps den Initialisierer im Code instanziieren (beispielsweise in „Global.aspx.cs“):

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Das Hinzufügen eines Initialisierers mit `ApplicationInsights.config` oder `TelemetryConfiguration.Active` ist für ASP.NET Core-Anwendungen nicht zulässig. 

**ASP.NET Core-Apps: Laden Sie den Initialisierer in die aktive TelemetryConfiguration.**

Zum Hinzufügen eines neuen `TelemetryInitializer` für [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers)-Anwendungen wird dieser wie unten gezeigt dem Abhängigkeitsinjektionscontainer hinzugefügt. Dies erfolgt in der `ConfigureServices`-Methode Ihrer `Startup.cs`-Klasse.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

**Java-Agent**

Für [Java-Agent 3.0](./java-in-process-agent.md) ist der Name der Cloudrolle wie folgt festgelegt:

```json
{
  "role": {
    "name": "my cloud role name"
  }
}
```

Sie können den Namen der Cloudrolle auch mithilfe der Umgebungsvariablen ```APPLICATIONINSIGHTS_ROLE_NAME``` festlegen.

**Java SDK**

Ab dem Java SDK 2.5.0 für Application Insights können Sie den Namen der Cloudrolle angeben, indem Sie der Datei `<RoleName>` den Eintrag `ApplicationInsights.xml` hinzufügen. Beispiel:

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Bei Verwendung von Spring Boot mit dem Application Insights Spring Boot-Startprogramm besteht die einzige erforderliche Änderung darin, Ihren benutzerdefinierten Namen für die Anwendung in der Datei „application.properties“ festzulegen.

`spring.application.name=<name-of-app>`

Das Spring Boot-Startprogramm weist dem von Ihnen für die Eigenschaft „spring.application.name“ eingegebenen Wert automatisch „Cloudrollenname“ zu.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternative Methode für Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Grundlegendes zu „Cloudrollenname“ im Kontext der Anwendungsübersicht

Im Hinblick auf **Cloudrollenname** kann es hilfreich sein, sich eine Anwendungsübersicht anzusehen, die mehrere „Cloudrollenname“-Werte enthält:

![Screenshot: Anwendungsübersicht](media/app-map/cloud-rolename.png)

In der Anwendungsübersicht oben sind alle Namen in grünen Kreisen „Cloudrollenname“-Werte für verschiedene Aspekte dieser spezifischen verteilten Anwendung. Somit umfasst diese Anwendung folgende Rollen: `Authentication`, `acmefrontend`, `Inventory Management` und `Payment Processing Worker Role`. 

Bei dieser Anwendung stellt jeder dieser Cloudrollennamen auch eine unterschiedliche eindeutige Application Insights-Ressource mit eigenen Instrumentierungsschlüsseln dar. Da der Besitzer dieser Anwendung Zugriff auf jede dieser vier verschiedenen Application Insights-Ressourcen hat, können in der Anwendungsübersicht die zugrunde liegenden Beziehungen zusammengefügt werden.

[Offizielle Definitionen:](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternativ kann **Cloudrolleninstanz** hilfreich in Szenarien sein, in denen über **Cloudrollenname** angegeben wird, dass das Problem im Web-Front-End aufgetreten ist. Möglicherweise führen Sie das Web-Front-End jedoch auf mehreren Servern mit Lastenausgleich aus, sodass die Möglichkeit, über Kusto-Abfragen die Details der nächsttieferen Ebene anzuzeigen, und die Kenntnis darüber, ob sich das Problem auf alle Server bzw. Instanzen des Web-Front-Ends auswirkt, äußerst wichtig sind.

Ein Fall, bei dem Sie den Wert für „Cloudrolleninstanz“ überschreiben können, ist beispielsweise ein Szenario, in dem die Anwendung in einer Containerumgebung ausgeführt wird und die Kenntnis des einzelnen Servers nicht genügt, um ein bestimmtes Problem zu lokalisieren.

Weitere Informationen zum Überschreiben der Eigenschaft „Cloudrollenname“ mit Telemetrieinitialisierern finden Sie unter [Add properties: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) (Hinzufügen von Eigenschaften: ITelemetryInitializer).

## <a name="troubleshooting"></a>Problembehandlung

Bei Problemen mit der Anwendungsübersicht können Sie Folgendes versuchen:

### <a name="general"></a>Allgemein

1. Vergewissern Sie sich, dass Sie ein offiziell unterstütztes SDK verwenden. Nicht unterstützte SDKs oder Community-SDKs unterstützen möglicherweise keine Korrelation.

    Eine Liste mit unterstützten SDKs finden Sie in [diesem Artikel](./platforms.md).

2. Upgraden Sie alle Komponenten auf die neueste SDK-Version.

3. Wenn Sie Azure Functions mit C# verwenden, führen Sie ein Upgrade auf [Functions V2](../../azure-functions/functions-versions.md) durch.

4. Vergewissern Sie sich, dass [Cloudrollenname](#set-or-override-cloud-role-name) richtig konfiguriert wurde.

5. Sollte eine Abhängigkeit fehlen, stellen Sie sicher, dass sie sich in der Liste [automatisch erfasster Abhängigkeiten](./auto-collect-dependencies.md) befindet. Andernfalls können Sie sie auch manuell mit einem [TrackDependency-Aufruf](./api-custom-events-metrics.md#trackdependency) nachverfolgen.

### <a name="too-many-nodes-on-the-map"></a>Zu viele Knoten in der Übersicht

In der Anwendungsübersicht werden ein Anwendungsknoten für jeden in der Anforderungstelemetrie vorhandenen eindeutigen Cloudrollennamen und ein Abhängigkeitsknoten für jede eindeutige Kombination aus Typ, Ziel und Cloudrollenname in der Abhängigkeitstelemetrie erstellt. Wenn mehr als 10.000 Knoten in der Telemetrie vorhanden sind, können in der Anwendungsübersicht nicht alle Knoten und Links abgerufen werden, sodass die Übersicht unvollständig ist. In diesem Fall wird beim Anzeigen der Übersicht eine Warnmeldung eingeblendet.

Zudem können in der Anwendungsübersicht nur maximal 1.000 separate nicht gruppierte Knoten auf einmal dargestellt werden. In der Anwendungsübersicht wird die visuelle Komplexität dadurch reduziert, dass Abhängigkeiten mit demselben Typ und denselben Aufrufern gruppiert werden. Wenn die Telemetrie jedoch zu viele eindeutige Cloudrollennamen oder zu viele Abhängigkeitstypen enthält, ist diese Gruppierung unzureichend, und die Übersicht kann nicht dargestellt werden.

Um dies zu beheben, müssen Sie die Instrumentierung ändern, um die Felder für den Cloudrollennamen, den Abhängigkeitstyp und das Abhängigkeitsziel ordnungsgemäß festzulegen.

* Das Abhängigkeitsziel muss den logischen Namen einer Abhängigkeit darstellen. In vielen Fällen entspricht es dem Server- oder Ressourcennamen der Abhängigkeit. Im Fall von HTTP-Abhängigkeiten ist es z. B. auf den Hostnamen festgelegt. Es sollte keine eindeutigen IDs oder Parameter enthalten, die sich bei unterschiedlichen Anforderungen ändern.

* Der Abhängigkeitstyp muss den logischen Typ einer Abhängigkeit darstellen. HTTP, SQL oder Azure-Blob sind beispielsweise typische Abhängigkeitstypen. Er sollte keine eindeutigen IDs enthalten.

* Der Zweck des Cloudrollennamens ist im [Abschnitt weiter oben](#set-or-override-cloud-role-name) beschrieben.

## <a name="portal-feedback"></a>Feedback zum Portal

Verwenden Sie für Feedback die Feedbackoption.

![MapLink-1 (Abbildung)](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Funktionsweise der Korrelation in Application Insights finden Sie im [Artikel zur Telemetriekorrelation](correlation.md).
* Die [umfassende Benutzeroberfläche zur Transaktionsdiagnose](transaction-diagnostics.md) setzt die serverseitigen Telemetriedaten von allen Ihren mithilfe von Application Insights überwachten Komponenten automatisch in einer einzelnen Ansicht in Beziehung.
* Informationen zu Szenarien mit erweiterter Korrelation in ASP.NET Core und ASP.NET finden Sie im Artikel zum [Nachverfolgen von benutzerdefinierten Vorgängen](custom-operations-tracking.md).