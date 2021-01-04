---
title: Azure Service Fabric-Ereignisaggregation mit EventFlow
description: Erfahren Sie, wie Sie Ereignisse unter Verwendung von EventFlow zur Überwachung und Diagnose von Azure Service Fabric-Clustern aggregieren und sammeln.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a80eea5a50aa7b1e441049eeb2cae381994cd3ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006335"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Ereignisaggregation und -sammlung mithilfe von EventFlow

Mit dem [EventFlow-Element der Microsoft-Diagnose](https://github.com/Azure/diagnostics-eventflow) können Ereignisse von einem Knoten an mindestens ein Überwachungsziel geleitet werden. Da die entsprechenden Daten als NuGet-Paket in Ihrem Dienstprojekt enthalten sind, werden der EventFlow-Code und die -Konfiguration mit dem Dienst übertragen, sodass das oben erwähnte Problem mit der Konfiguration pro Knoten für die Azure-Diagnose nicht besteht. EventFlow wird im Dienstprozess ausgeführt und direkt mit den konfigurierten Ausgaben verbunden. Aufgrund der direkten Verbindung funktioniert EventFlow für Azure-, Container- und lokale Bereitstellungen von Diensten. Gehen Sie mit Bedacht vor, wenn Sie EventFlow in Szenarien mit hoher Dichte ausführen, z.B. in einem Container, da für jede EventFlow-Pipeline eine externe Verbindung hergestellt wird. Wenn Sie also mehrere Prozesse hosten, ergeben sich auch mehrere ausgehende Verbindungen! Dies ist für Service Fabric-Anwendungen kein größeres Problem, da alle Replikate eines `ServiceType`-Elements in demselben Prozess ausgeführt werden, sodass die Anzahl von ausgehenden Verbindungen begrenzt wird. EventFlow ermöglicht auch die Ereignisfilterung, sodass nur die Ereignisse gesendet werden, die dem angegebenen Filter entsprechen.

## <a name="set-up-eventflow"></a>Einrichten von EventFlow

EventFlow-Binärdateien sind als Gruppe von NuGet-Paketen verfügbar. Klicken Sie zum Hinzufügen von EventFlow zu einem Service Fabric-Dienstprojekt im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie die Option „NuGet-Pakete verwalten“. Wechseln Sie zur Registerkarte „Durchsuchen“, und suchen Sie nach „`Diagnostics.EventFlow`“:

![EventFlow-NuGet-Pakete in der NuGet-Paket-Manager-UI von Visual Studio](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Eine Liste verschiedener Pakete mit der Bezeichnung „Inputs“ und „Outputs“ wird angezeigt. EventFlow unterstützt mehrere verschiedene Protokollanbieter und Analysen. Der Dienst, von dem EventFlow gehostet wird, sollte – je nach Quelle und Ziel für die Anwendungsprotokolle – die richtigen Pakete enthalten. Neben dem ServiceFabric-Kernpaket wird mindestens auch ein konfiguriertes Input- und Output-Paket benötigt. Beispielsweise können Sie die folgenden Pakete hinzufügen, um EventSource-Ereignisse an Application Insights zu senden:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` (zur Erfassung von Daten über die EventSource-Klasse des Diensts und über standardmäßige EventSources, z.B. *Microsoft-ServiceFabric-Services* und *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (Wir senden die Protokolle an eine Azure Application Insights-Ressource.)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` (Ermöglicht die Initialisierung der EventFlow-Pipeline über die Service Fabric-Dienstkonfiguration und meldet Probleme in Bezug auf das Senden von Diagnosedaten als Service Fabric-Integritätsberichte.)

>[!NOTE]
>Für das `Microsoft.Diagnostics.EventFlow.Inputs.EventSource`-Paket muss das Dienstprojekt auf .NET Framework 4.6 oder höher ausgerichtet sein. Achten Sie darauf, in den Projekteigenschaften das richtige Zielframework festzulegen, bevor Sie dieses Paket installieren.

Nachdem alle Pakete installiert wurde, ist der nächste Schritt das Konfigurieren und Aktivieren von EventFlow im Dienst.

## <a name="configure-and-enable-log-collection"></a>Konfigurieren und Aktivieren der Protokollsammlung
Die EventFlow-Pipeline, die für das Senden der Protokolle zuständig ist, wird aus einer Spezifikation erstellt, die in einer Konfigurationsdatei gespeichert wird. Mit dem `Microsoft.Diagnostics.EventFlow.ServiceFabric`-Paket wird eine anfängliche EventFlow-Konfigurationsdatei mit dem Namen `eventFlowConfig.json` im Projektmappenordner `PackageRoot\Config` installiert. Diese Konfigurationsdatei muss so geändert werden, dass Daten aus der `EventSource`-Standarddienstklasse und allen anderen Eingaben, die Sie konfigurieren möchten, erfasst und Daten an den entsprechenden Ort gesendet werden.

>[!NOTE]
>Wenn die Projektdatei das Format von VisualStudio 2017 hat, wird die Datei `eventFlowConfig.json` nicht automatisch hinzugefügt. Erstellen Sie daher die Datei im Ordner `Config`, und legen Sie die Buildaktion auf `Copy if newer` fest. 

Hier eine Beispieldatei *eventFlowConfig.json*, die auf den oben genannten NuGet-Paketen basiert:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

Der Name des ServiceEventSource-Elements für den Dienst ist der Wert der Name-Eigenschaft des `EventSourceAttribute`-Elements, das auf die ServiceEventSource-Klasse angewendet wird. Alle Angaben sind in der Datei `ServiceEventSource.cs` enthalten, die Teil des Dienstcodes ist. Im folgenden Codeausschnitt lautet der Name des ServiceEventSource-Elements *MyCompany-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Beachten Sie, dass die Datei `eventFlowConfig.json` Teil des Dienstkonfigurationspakets ist. Änderungen an dieser Datei können in vollständigen oder auf die Konfiguration beschränkten Upgrades des Diensts enthalten sein. Dies ist von Integritätsprüfungen des Service Fabric-Upgrades und dem automatischen Rollback, falls ein Upgradefehler auftritt, abhängig. Weitere Informationen finden Sie unter [Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md).

Im Abschnitt *filters* der Konfigurationsdatei können Sie die Informationen weiter anpassen, die über die EventFlow-Pipeline an die Ausgaben weitergeleitet werden. So können Sie bestimmte Informationen löschen oder hinzufügen oder die Struktur der Ereignisdaten ändern. Weitere Informationen zum Filtern finden Sie unter [EventFlow-Filter](https://github.com/Azure/diagnostics-eventflow#filters).

Der letzte Schritt ist die Instanziierung der EventFlow-Pipeline im Startcode Ihres Diensts in der Datei `Program.cs`:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

Der Name, der als Parameter der `CreatePipeline`-Methode von `ServiceFabricDiagnosticsPipelineFactory` übergeben wird, ist der Name der *Integritätsentität*, der für die Pipeline der EventFlow-Protokollsammlung steht. Dieser Name wird verwendet, wenn für EventFlow ein Fehler auftritt, der über das Service Fabric-Integritätssubsystem gemeldet wird.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Verwenden von Service Fabric-Einstellungen und -Anwendungsparametern in eventFlowConfig

EventFlow unterstützt die Verwendung von Service Fabric-Einstellungen und -Anwendungsparametern für die Konfiguration von EventFlow-Einstellungen. Sie können mit der folgenden spezifischen Syntax für Werte auf Service Fabric-Einstellungsparameter verweisen:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` ist der Name des Abschnitts für die Service Fabric-Konfiguration und `<setting-name>` die Konfigurationseinstellung, die den zum Konfigurieren einer EventFlow-Einstellung verwendeten Wert angibt. Weitere Informationen dazu finden Sie unter [Support for Service Fabric settings and application parameters](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters) (Unterstützung für Service Fabric-Einstellungen und -Anwendungsparameter).

## <a name="verification"></a>Überprüfung

Starten Sie Ihren Dienst, und sehen Sie sich die Informationen im Fenster für die Debugausgabe von Visual Studio an. Nachdem der Dienst gestartet wurde, sollte angezeigt werden, dass der Dienst Datensätze an die konfigurierte Ausgabe sendet. Navigieren Sie zu Ihrer Ereignisanalyse- und Visualisierungsplattform, und vergewissern Sie sich, dass Protokolle angezeigt werden (dies kann einige Minuten dauern).

## <a name="next-steps"></a>Nächste Schritte

* [Ereignisanalyse und Visualisierung mit Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Ereignisanalyse und -visualisierung mit Azure Monitor-Protokollen](service-fabric-diagnostics-event-analysis-oms.md)
* [EventFlow-Dokumentation](https://github.com/Azure/diagnostics-eventflow)
