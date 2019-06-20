---
title: Trennen der Telemetrie von Entwicklung, Testen und Release in Azure Application Insights | Microsoft Docs
description: Leiten Sie Telemetriedaten für Entwicklungs-, Test- und Produktionsabläufe an verschiedene Ressourcen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 2e9c599c12ed10327d352baee02500d2284d98d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60713422"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Trennen der Telemetriedaten von Entwicklung, Test und Produktion

Wenn Sie die nächste Version einer Webanwendung entwickeln, möchten Sie die [Application Insights](../../azure-monitor/app/app-insights-overview.md)-Telemetriedaten der neuen Version nicht mit denen der bereits veröffentlichten Version verwechseln. Um Verwirrung zu vermeiden, senden Sie die Telemetriedaten aus verschiedenen Entwicklungsphasen mit separaten Instumentierungsschlüsseln (ikeys) an getrennte Application Insights-Ressourcen. Um das Ändern des Instrumentierungsschlüssels beim Fortschreiten einer Version von einer Phase zur nächsten zu erleichtern, kann es sinnvoll sein, den ikey im Code statt in der Konfigurationsdatei zu platzieren. 

(Wenn Ihr System ein Azure-Clouddienst ist, gibt es [eine weitere Methode zum Festlegen von separaten iKeys](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Informationen zu Ressourcen und Instrumentierungsschlüsseln

Wenn Sie Application Insights-Überwachung für Ihre Web App einrichten, erstellen Sie in Microsoft Azure eine Application Insights-*Ressource*. Sie öffnen diese Ressource im Azure-Portal, um die in Ihrer App gesammelten Telemetriedaten anzuzeigen und zu analysieren. Die Ressource wird durch einen *Instrumentierungsschlüssel* (ikey) identifiziert. Wenn Sie das Application Insights-Paket installieren, um Ihre App zu überwachen, konfigurieren Sie sie mit diesem Instrumentierungsschlüssel und teilen ihr so mit, wohin die Telemetriedaten gesendet werden sollen.

Normalerweise entscheiden Sie sich in unterschiedlichen Szenarien für die Verwendung von getrennten Ressourcen oder einer einzelnen, geteilten Ressource:

* Verschiedene, unabhängige Anwendungen: Verwenden Sie eine separate Ressource und einen separaten ikey für jede App.
* Mehrere Komponenten oder Rollen der gleichen Geschäftsanwendung – Verwenden Sie eine [einzelne geteilte Ressource](../../azure-monitor/app/app-map.md) für alle Komponentenanwendungen. Die Telemetrie kann anhand der cloud_RoleName-Eigenschaft gefiltert oder aufgeteilt werden.
* Entwicklung, Testen und Release: Verwenden Sie eine separate Ressource und separate ikeys für Versionen des Systems je nach Produktionsabschnitt oder -phase.
* A | B-Tests: Verwenden Sie eine einzelne Ressource. Erstellen Sie einen Telemetrie-Initialisierer, um den Telemetriedaten eine Eigenschaft zum Identifizieren der Varianten hinzuzufügen.


## <a name="dynamic-ikey"></a> Dynamischer Instrumentationsschlüssel

Um das Ändern des ikeys beim Fortschreiten des Codes von einer Produktionsphase zur nächsten zu erleichtern, legen Sie ihn im Code statt in der Konfigurationsdatei fest.

Legen Sie den Schlüssel in einer Initialisierungsmethode fest, wie z. B. "global.aspx.cs" in einem ASP.NET-Dienst:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

In diesem Beispiel werden die iKeys für die verschiedenen Ressourcen in verschiedenen Versionen der Webkonfigurationsdatei platziert. Wenn Sie die Webkonfigurationsdatei austauschen – z.B. im Rahmen des Releaseskripts – wird die Zielressource ausgetauscht.

### <a name="web-pages"></a>Webseiten
Der iKey wird auch in Webseiten Ihrer App in dem [Skript verwendet, das Sie auf dem Blatt "Schnellstart" erhalten haben](../../azure-monitor/app/javascript.md). Statt ihn direkt im Skript zu programmieren, generieren Sie ihn über den Serverzustand. Beispielsweise in einer ASP.NET-App:

*JavaScript in Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Erstellen zusätzlicher Application Insights-Ressourcen
Um die Telemetriedaten für verschiedene Anwendungskomponenten oder für verschiedene Einsatzzwecke der gleichen Komponente (Entwicklung/Test und Produktion) zu trennen, müssen Sie eine neue Application Insights-Ressource erstellen.

Fügen Sie unter [portal.azure.com](https://portal.azure.com)eine neue Application Insights-Ressource hinzu:

![Klicken Sie auf "Neu > Application Insights"](./media/separate-resources/01-new.png)

* **Anwendungstyp** bestimmt den Inhalt des Blatts "Übersicht" und die im [Metrik-Explorer](../../azure-monitor/app/metrics-explorer.md)verfügbaren Eigenschaften. Wenn Ihr App-Typ nicht angezeigt wird, wählen Sie einen der Webtypen für Webseiten aus.
* **Ressourcengruppe** ist eine benutzerfreundliche Möglichkeit zum Verwalten von Eigenschaften wie der [Zugriffssteuerung](../../azure-monitor/app/resources-roles-access-control.md)gespeichert und verarbeitet. Sie können separate Ressourcengruppen für Entwicklungs-, Test- und Produktionsumgebungen verwenden.
* **Abonnement** ist Ihr Zahlungskonto in Azure.
* **Speicherort** ist der Ort, an dem Ihre Daten aufbewahrt werden. Dieser kann derzeit nicht geändert werden. 
* **Zu Dashboard hinzufügen** legt eine Schnellzugriffskachel für die Ressource auf Ihrer Azure-Startseite ab. 

Das Erstellen der Ressource dauert einige Sekunden. Wenn es abgeschlossen ist, sehen Sie eine Warnung.

(Sie können ein [PowerShell-Skript](../../azure-monitor/app/powershell-script-create-resource.md) schreiben, um eine Ressource automatisch zu erstellen.)

### <a name="getting-the-instrumentation-key"></a>Abrufen des Instrumentierungsschlüssels
Der Instrumentierungsschlüssel identifiziert die Ressource, die Sie erstellt haben. 

![Klicken Sie auf "Essentials", klicken Sie auf den Instrumentierungsschlüssel, STRG+C.](./media/separate-resources/02-props.png)

Sie benötigen die Instrumentierungsschlüssel aller Ressourcen, an die Ihre App Daten sendet.

## <a name="filter-on-build-number"></a>Filtern nach Buildnummer
Wenn Sie eine neue Version Ihrer App veröffentlichen, sollten Sie die Telemetrie aus verschiedenen Builds trennen können.

Sie können die Eigenschaft „Anwendungsversion“ festlegen, sodass Sie die Ergebnisse in der [Suche](../../azure-monitor/app/diagnostic-search.md) und im [Metrik-Explorer](../../azure-monitor/app/metrics-explorer.md) filtern können.

![Filtern nach einer Eigenschaft](./media/separate-resources/050-filter.png)

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
Stellen Sie für die Nachverfolgung der Anwendungsversion sicher, dass `buildinfo.config` über den Prozess Ihrer Microsoft-Build-Engine generiert wird. Fügen Sie in Ihrer CSPROJ-Datei Folgendes hinzu:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Wenn das Webmodul Application Insights über die Buildinformationen verfügt, fügt es jedem Telemetrieelement automatisch die **Anwendungsversion** als Eigenschaft hinzu. Dies ermöglicht es Ihnen, nach der Version zu filtern, wenn Sie [Diagnosesuchen](../../azure-monitor/app/diagnostic-search.md) durchführen oder [Metriken untersuchen](../../azure-monitor/app/metrics-explorer.md).

Beachten Sie aber, dass die Buildversionsnummer nur von der Microsoft-Build-Engine generiert wird, und nicht vom Entwicklerbuild in Visual Studio.

### <a name="release-annotations"></a>Versionsanmerkungen
Bei Verwendung von Azure DevOps können Sie Ihren Diagrammen einen [Anmerkungsmarker](../../azure-monitor/app/annotations.md) hinzufügen lassen, wenn Sie eine neue Version veröffentlichen. In der folgenden Abbildung ist dargestellt, wie dieser Marker angezeigt wird.

![Screenshot eines Beispiels für eine Versionsanmerkung in einem Diagramm](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Nächste Schritte

* [Freigegebene Ressourcen für mehrere Rollen](../../azure-monitor/app/app-map.md)
* [Erstellen eines Telemetrie-Initialisierers zur Unterscheidung von A|B-Varianten](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
