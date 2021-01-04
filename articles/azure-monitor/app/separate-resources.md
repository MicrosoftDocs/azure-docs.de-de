---
title: 'Entwerfen der Application Insights-Bereitstellung: Eine oder mehrere Ressourcen?'
description: Leiten Sie Telemetriedaten für Entwicklungs-, Test- und Produktionsabläufe an verschiedene Ressourcen.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 49e9b8920af7333e0d95e23e6e5cf0828d448609
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536352"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Wie viele Application Insights-Ressourcen soll ich bereitstellen?

Wenn Sie die nächste Version einer Webanwendung entwickeln, möchten Sie die [Application Insights](../../azure-monitor/app/app-insights-overview.md)-Telemetriedaten der neuen Version nicht mit denen der bereits veröffentlichten Version verwechseln. Um Verwirrung zu vermeiden, senden Sie die Telemetriedaten aus verschiedenen Entwicklungsphasen mit separaten Instumentierungsschlüsseln (ikeys) an getrennte Application Insights-Ressourcen. Um das Ändern des Instrumentierungsschlüssels beim Fortschreiten einer Version von einer Phase zur nächsten zu erleichtern, kann es sinnvoll sein, den ikey im Code statt in der Konfigurationsdatei zu platzieren.

(Wenn Ihr System ein Azure-Clouddienst ist, gibt es [eine weitere Methode zum Festlegen von separaten iKeys](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Informationen zu Ressourcen und Instrumentierungsschlüsseln

Wenn Sie Application Insights-Überwachung für Ihre Web App einrichten, erstellen Sie in Microsoft Azure eine Application Insights-*Ressource*. Sie öffnen diese Ressource im Azure-Portal, um die in Ihrer App gesammelten Telemetriedaten anzuzeigen und zu analysieren. Die Ressource wird durch einen *Instrumentierungsschlüssel* (ikey) identifiziert. Wenn Sie das Application Insights-Paket installieren, um Ihre App zu überwachen, konfigurieren Sie sie mit diesem Instrumentierungsschlüssel und teilen ihr so mit, wohin die Telemetriedaten gesendet werden sollen.

Jede Application Insights-Ressource umfasst Metriken, die standardmäßig verfügbar sind. Wenn vollständig getrennte Komponenten an dieselbe Application Insights-Ressource berichten, ist es möglicherweise nicht sinnvoll, diese Metriken für Dashboards/Warnungen zu verwenden.

### <a name="when-to-use-a-single-application-insights-resource"></a>Verwenden einer einzelnen Application Insights-Ressource

-   Für Anwendungskomponenten, die gemeinsam bereitgestellt werden. Normalerweise von einem einzelnen Team entwickelt und von der gleichen Gruppe von DevOps/ITOps-Benutzern verwaltet.
-   Wenn es sinnvoll ist, KPIs (Key Performance Indicators), wie z. B. Antwortzeiten, Fehlerraten in Dashboards usw., standardmäßig für alle Komponenten zu aggregieren (Sie können auf der Metriken-Explorer-Benutzeroberfläche eine Segmentierung nach Rollenname auswählen).
-   Wenn die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) zwischen den Anwendungskomponenten nicht unterschiedlich verwaltet werden muss.
-   Wenn Sie keine Warnungskriterien für Metriken benötigen, die sich zwischen den Komponenten unterscheiden.
-   Wenn Sie fortlaufende Exporte zwischen den Komponenten nicht unterschiedlich verwalten müssen.
-   Wenn Sie die Abrechnung/Kontingente zwischen den Komponenten nicht unterschiedlich verwalten müssen.
-   Wenn es in Ordnung ist, dass ein API-Schlüssel den gleichen Zugriff auf Daten von allen Komponenten hat. und 10 API-Schlüssel für die Anforderungen aller Komponenten ausreichend sind.
-   Wenn es in Ordnung ist, dass alle Rollen die gleichen Einstellungen für die intelligente Erkennung und Arbeitselementintegration aufweisen.

### <a name="other-things-to-keep-in-mind"></a>Weitere zu beachtende Punkte

-   Möglicherweise müssen Sie benutzerdefinierten Code hinzufügen, um sicherzustellen, dass aussagekräftige Werte im Attribut [Cloud_RoleName](./app-map.md?tabs=net#set-or-override-cloud-role-name) festgelegt werden. Wenn für dieses Attribut keine aussagekräftigen Werte festgelegt werden, funktioniert *KEINE* der Benutzeroberflächen im Portal.
- Bei Service Fabric-Anwendungen und klassischen Clouddiensten liest das SDK automatisch aus der Azure-Rollenumgebung und legt diese fest. Bei allen anderen App-Typen müssen Sie dies wahrscheinlich explizit festlegen.
-   Die Benutzeroberfläche für Livemetriken unterstützt kein Aufteilen nach Rollenname.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Dynamischer Instrumentationsschlüssel

Um das Ändern des iKeys beim Fortschreiten des Codes von einer Produktionsphase zur nächsten zu erleichtern, verweisen Sie im Code dynamisch auf den Schlüssel, statt einen hartcodierten/statischen Wert zu verwenden.

Legen Sie den Schlüssel in einer Initialisierungsmethode fest, wie z. B. "global.aspx.cs" in einem ASP.NET-Dienst:

```csharp
protected void Application_Start()
{
  Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = 
      // - for example -
      WebConfigurationManager.AppSettings["ikey"];
  ...
```

In diesem Beispiel werden die iKeys für die verschiedenen Ressourcen in verschiedenen Versionen der Webkonfigurationsdatei platziert. Wenn Sie die Webkonfigurationsdatei austauschen – z.B. im Rahmen des Releaseskripts – wird die Zielressource ausgetauscht.

### <a name="web-pages"></a>Webseiten
Der iKey wird auch in Webseiten Ihrer App in dem [Skript verwendet, das Sie im Bereich „Schnellstart“ erhalten haben](../../azure-monitor/app/javascript.md). Statt ihn direkt im Skript zu programmieren, generieren Sie ihn über den Serverzustand. Beispielsweise in einer ASP.NET-App:

```javascript
<script type="text/javascript">
// Standard Application Insights web page script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
  // Generate from server property:
  "@Microsoft.ApplicationInsights.Extensibility.
     TelemetryConfiguration.Active.InstrumentationKey"
  }
 )
//...
```

## <a name="create-additional-application-insights-resources"></a>Erstellen zusätzlicher Application Insights-Ressourcen

Zum Erstellen einer Application Insights-Ressource befolgen Sie den [Leitfaden zum Erstellen einer Ressource ](./create-new-resource.md).

### <a name="getting-the-instrumentation-key"></a>Abrufen des Instrumentierungsschlüssels
Der Instrumentierungsschlüssel identifiziert die Ressource, die Sie erstellt haben.

Sie benötigen die Instrumentierungsschlüssel aller Ressourcen, an die Ihre App Daten sendet.

## <a name="filter-on-build-number"></a>Filtern nach Buildnummer
Wenn Sie eine neue Version Ihrer App veröffentlichen, sollten Sie die Telemetrie aus verschiedenen Builds trennen können.

Sie können die Eigenschaft „Anwendungsversion“ festlegen, sodass Sie die Ergebnisse in der [Suche](../../azure-monitor/app/diagnostic-search.md) und im [Metrik-Explorer](../../azure-monitor/platform/metrics-charts.md) filtern können.

Es gibt verschiedene Methoden, um die Eigenschaft "Anwendungsversion" festzulegen.

* Direktes Festlegen:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Umschließen Sie diese Zeile in einem [Telemetrieinitialisierer](../../azure-monitor/app/api-custom-events-metrics.md#defaults) , um sicherzustellen, dass alle TelemetryClient-Instanzen einheitlich festgelegt werden.
* [ASP.NET] Legen Sie die Version in `BuildInfo.config`fest. Das Webmodul übernimmt die Version aus dem Knoten "BuildLabel". Schließen Sie diese Datei in Ihr Projekt ein, und denken Sie daran, die Eigenschaft "Immer kopieren" im Projektmappen-Explorer festzulegen.

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generieren Sie "BuildInfo.config" automatisch in MSBuild. Fügen Sie zu diesem Zweck Ihrer `.csproj`-Datei einige Zeilen hinzu:

    ```XML
    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Hierdurch wird eine Datei mit dem Namen " *yourProjectName*.BuildInfo.config" generiert. Der Veröffentlichungsprozess benennt diese Datei in "BuildInfo.config" um.

    Die Buildbezeichnung enthält einen Platzhalter (AutoGen_...), wenn Sie für die Erstellung Visual Studio verwenden. Bei der Erstellung mit MSBuild wird der Name jedoch mit der richtigen Versionsnummer aufgefüllt.

    Um das Generieren von Versionsnummern in MSBuild zu ermöglichen, legen Sie in "AssemblyReference.cs" die Version fest, z. B. `1.0.*`.

## <a name="version-and-release-tracking"></a>Versionsnachverfolgung
Stellen Sie für die Nachverfolgung der Anwendungsversion sicher, dass `buildinfo.config` über den Prozess Ihrer Microsoft-Build-Engine generiert wird. Fügen Sie in der `.csproj`-Datei Folgendes hinzu:  

```XML
<PropertyGroup>
  <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>
  <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
</PropertyGroup>
```

Wenn das Webmodul Application Insights über die Buildinformationen verfügt, fügt es jedem Telemetrieelement automatisch die **Anwendungsversion** als Eigenschaft hinzu. Dies ermöglicht es Ihnen, nach der Version zu filtern, wenn Sie [Diagnosesuchen](../../azure-monitor/app/diagnostic-search.md) durchführen oder [Metriken untersuchen](../../azure-monitor/platform/metrics-charts.md).

Beachten Sie aber, dass die Buildversionsnummer nur von der Microsoft-Build-Engine generiert wird, und nicht vom Entwicklerbuild aus Visual Studio.

### <a name="release-annotations"></a>Versionsanmerkungen
Bei Verwendung von Azure DevOps können Sie Ihren Diagrammen einen [Anmerkungsmarker](../../azure-monitor/app/annotations.md) hinzufügen lassen, wenn Sie eine neue Version veröffentlichen. 

## <a name="next-steps"></a>Nächste Schritte

* [Freigegebene Ressourcen für mehrere Rollen](../../azure-monitor/app/app-map.md)
* [Erstellen eines Telemetrie-Initialisierers zur Unterscheidung von A|B-Varianten](../../azure-monitor/app/api-filtering-sampling.md#add-properties)