---
title: Senden von Daten von Azure-Diagnose an Application Insights
description: Aktualisieren Sie die öffentliche Konfiguration von Azure-Diagnose, um Daten an Application Insights zu senden.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: 5af0eb20f9766369caa7351719b63b213c394e5d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008836"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Senden von Cloud Services-, Virtual Machines- oder Service Fabric-Diagnosedaten an Application Insights
Cloud Services, Virtual Machines, VM-Skalierungsgruppen und Service Fabric verwenden die Azure-Diagnoseerweiterung zum Sammeln von Daten.  Die Azure-Diagnose sendet die Daten an Azure Storage-Tabellen.  Sie können jedoch auch alle oder einen Teil der Daten mithilfe der Azure-Diagnoseerweiterung 1.5 oder höher an andere Speicherorte weiterleiten.

Dieser Artikel beschreibt, wie Daten der Azure-Diagnoseerweiterung an Application Insights gesendet werden.

## <a name="diagnostics-configuration-explained"></a>Erläuterung der Diagnosekonfiguration
Mit der Azure-Diagnoseerweiterung 1.5 wurden Senken eingeführt, die zusätzliche Speicherorte darstellen, an die Sie Diagnosedaten senden können.

Beispielkonfiguration einer Senke für Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- Das Attribut **Sink** *Name* ist ein Zeichenfolgenwert, der die Senke eindeutig identifiziert.

- Das Element **ApplicationInsights** gibt den Instrumentierungsschlüssel der Application Insights-Ressource an, an die die Azure-Diagnosedaten gesendet werden.
    - Wenn Sie keine vorhandene Application Insights-Ressource besitzen, finden Sie weitere Informationen zum Erstellen einer Ressource sowie zum Abrufen des Instrumentierungsschlüssels unter [Erstellen einer neuen Application Insights-Ressource](../app/create-new-resource.md).
    - Wenn Sie einen Clouddienst mit dem Azure SDK 2.8 und höher entwickeln, wird dieser Instrumentierungsschlüssel automatisch aufgefüllt. Der Wert basiert auf der Dienstkonfigurationseinstellung **APPINSIGHTS_INSTRUMENTATIONKEY**, die beim Verpacken des Clouddienstprojekts festgelegt wird. Weitere Informationen finden Sie unter [Verwenden von Application Insights mit Cloud Services](../app/cloudservices.md).

- Das Element **Channels** enthält ein oder mehrere **Channel**-Elemente.
    - Das Attribut *Name* gibt diesen Kanal eindeutig an.
    - Mit dem Attribut *logLevel* können Sie den Protokolliergrad angeben, den der Kanal zulässt. Die verfügbaren Protokolliergrade in der Reihenfolge ihrer Ausführlichkeit:
        - Ausführlich
        - Information
        - Warnung
        - Fehler
        - Kritisch

Ein Kanal verhält sich wie ein Filter und ermöglicht es Ihnen, bestimmte Protokolliergrade auszuwählen, die an die Zielsenke gesendet werden. Beispielsweise könnten Sie ausführliche Protokolle sammeln und an den Speicher senden, aber nur Fehler an die Senke.

Die folgende Abbildung zeigt diese Beziehung.

![Öffentliche Diagnosekonfiguration](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

Die folgende Abbildung fasst die Konfigurationswerte und deren Funktionsweise zusammen. Sie können in die Konfiguration mehrere Senken auf unterschiedlichen Ebenen der Hierarchie einschließen. Die Senke der obersten Ebene verhält sich wie eine globale Einstellung, und die für das einzelne Element angegebene Senke verhält sich wie eine Korrektur der globalen Einstellung.

![Konfiguration von Diagnosesenken mit Application Insights](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Vollständiges Beispiel für eine Senkenkonfiguration
Hier sehen Sie ein vollständiges Beispiel für eine öffentliche Konfigurationsdatei, die Folgendes ausführt:
1. Sendet alle Fehler an Application Insights (angegeben im Knoten **DiagnosticMonitorConfiguration**)
2. Sendet zudem ausführliche Protokolle für die Anwendungsprotokolle (angegeben im Knoten **Logs**).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
In der vorherigen Konfiguration haben die folgenden Zeilen die folgende Bedeutung:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Senden aller Daten, die von der Azure-Diagnose gesammelt werden

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Ausschließliches Senden von Fehlerprotokollen an die Application Insights-Senke

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Senden von ausführlichen Anwendungsprotokollen an Application Insights

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Einschränkungen

- **Nur Kanäle werden protokolliert, keine Leistungsindikatoren.** Wenn Sie einen Kanal mit einem Leistungsindikatorelement angeben, wird es ignoriert.
- **Der Protokolliergrad für einen Kanal darf den Protokolliergrad für die von der Azure-Diagnose erfassten Daten nicht überschreiten.** Beispiel: Sie können keine Anwendungsprotokollfehler im Element „Logs“ sammeln und können nicht versuchen, ausführliche Protokolle an die Application Insight-Senke zu senden. Das *scheduledTransferLogLevelFilter*-Attribut muss immer mindestens genauso viele Protokolle sammeln wie die Protokolle, die Sie an eine Senke senden möchten.
- **Sie können keine von der Azure-Diagnoseerweiterung gesammelten Blobdaten an Application Insights senden.** Beispiel: Alle Daten unter dem Knoten *Directories*. Bei Absturzabbildern wird das tatsächliche Absturzabbild weiterhin an den Blobspeicher gesendet, und es wird nur eine Benachrichtigung, dass das Absturzabbild generiert wurde, an Application Insights gesendet.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie etwas über das [Anzeigen von Azure-Diagnoseinformationen](../app/cloudservices.md) in Application Insights.
* Verwenden Sie [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) , um die Azure-Diagnose-Erweiterung für Ihre Anwendung zu aktivieren.
* Verwenden Sie [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) , um die Azure-Diagnose-Erweiterung für Ihre Anwendung zu aktivieren.

