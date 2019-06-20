---
title: Erfassen von Leistungsindikatoren in Azure-Clouddiensten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure-Diagnose und Application Insights Leistungsindikatoren in Clouddiensten ermitteln, verwenden und erstellen.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: jeconnoc
ms.openlocfilehash: 68101be211335d51eb4bf99361ea36b73fa19218
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60653738"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Erfassen von Leistungsindikatoren für Ihren Azure-Clouddienst

Leistungsindikatoren ermöglichen es Ihnen, die Leistung Ihrer Anwendungen und Hosts nachzuverfolgen. Windows Server stellt viele unterschiedliche Leistungsindikatoren zu Hardware, Anwendungen, Betriebssystem uvm. zur Verfügung. Durch das Erfassen und Senden von Leistungsindikatoren an Azure können Sie diese Informationen analysieren, um bessere Entscheidungen zu treffen. 

## <a name="discover-available-counters"></a>Ermitteln verfügbarer Indikatoren

Ein Leistungsindikator setzt sich aus zwei Teilen zusammen: dem Namen des Satzes (auch Kategorie genannt) und mindestens einem Indikator. Sie können mithilfe von PowerShell eine Liste der verfügbaren Leistungsindikatoren abrufen:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

Die Eigenschaft `CounterSetName` repräsentiert einen Satz (oder eine Kategorie) und ist ein guter Hinweis auf die Quelle des Leistungsindikators. Die Eigenschaft `Paths` repräsentiert eine Sammlung von Indikatoren für einen Satz. Sie können auch die Eigenschaft `Description` abrufen, um weitere Informationen zum Leistungsindikatorsatz zu erhalten.

Um alle Indikatoren eines Satzes abzurufen, verwenden Sie den Wert `CounterSetName` und erweitern die Sammlung `Paths`. Jedes Pfadelement ist ein Indikator, den Sie abfragen können. Um beispielsweise die verfügbaren Indikatoren für den Satz `Processor` abzurufen, erweitern Sie die `Paths`-Sammlung:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Die individuellen Indikatorpfade können zum verwendeten Diagnoseframework Ihres Diensts hinzugefügt werden. Weitere Informationen dazu, wie ein Leistungsindikatorpfad zusammengesetzt wird, finden Sie unter [Angeben eines Indikatorpfads](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Erfassen eines Leistungsindikators

Ein Leistungsindikator kann Ihrem Clouddienst entweder für die Azure-Diagnose oder für Application Insights hinzugefügt werden.

### <a name="application-insights"></a>Application Insights

In Azure Application Insights für Clouddienste können Sie angeben, welche Leistungsindikatoren Sie erfassen möchten. Nachdem Sie [Application Insights zu Ihrem Projekt hinzugefügt](../azure-monitor/app/cloudservices.md#sdk) haben, wird Ihrem Visual Studio-Projekt eine Konfigurationsdatei namens **ApplicationInsights.config** hinzugefügt. Diese Konfigurationsdatei definiert, welche Arten von Informationen Application Insights erfasst und an Azure sendet.

Öffnen Sie die Datei **ApplicationInsights.config**, und suchen Sie nach dem Element **ApplicationInsights** > **TelemetryModules**. Jedes Unterelement `<Add>` definiert einen Telemetrietyp, der erfasst werden soll, gemeinsam mit der zugehörigen Konfiguration. Der Leistungsindikator-Telemetriemodultyp lautet `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Wenn dieses Element bereits definiert ist, fügen Sie es nicht noch mal hinzu. Jeder zu erfassende Leistungsindikator ist unter einem Knoten namens `<Counters>` definiert. Hier sehen Sie ein Beispiel zur Erfassung von Leistungsindikatoren zu einem Laufwerk:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Jeder Leistungsindikator wird als `<Add>`-Element unterhalb von `<Counters>` repräsentiert. Das Attribut `PerformanceCounter` definiert, welcher Leistungsindikator erfasst wird. Das Attribut `ReportAs` gibt den Titel an, der im Azure-Portal für den Leistungsindikator angezeigt wird. Jeder Leistungsindikator, den Sie erfassen, wird in einer Kategorie namens **Benutzerdefiniert** im Portal platziert. Im Gegensatz zur Azure-Diagnose können Sie nicht festlegen, in welchem Intervall diese Leistungsindikatoren erfasst und an Azure gesendet werden. Bei Verwendung von Application Insights werden Leistungsindikatoren jede Minute erfasst und gesendet. 

Application Insights erfasst die folgenden Leistungsindikatoren automatisch:

* \Process(??APP_WIN32_PROC??)\% Prozessorzeit
* \Memory\Verfügbare Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\%Prozessorzeit

Weitere Informationen finden Sie unter [Systemleistungsindikatoren in Application Insights](../azure-monitor/app/performance-counters.md) und [Application Insights für Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Azure-Diagnose

> [!IMPORTANT]
> Die Daten werden zwar vollständig unter dem Speicherkonto aggregiert, allerdings bietet das Portal **keine** native Möglichkeit, die Daten in einem Diagramm darzustellen. Es wird dringend empfohlen, dass Sie einen weiteren Diagnosedienst, z.B. Application Insights, in Ihre Anwendung integrieren.

Mit der Azure-Diagnoseerweiterung für Clouddienste können Sie angeben, welche Leistungsindikatoren Sie erfassen möchten. Informationen zum Einrichten der Azure-Diagnose finden Sie unter [Einführung in die Überwachung von Clouddiensten](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Die zu erfassenden Leistungsindikatoren werden in der Datei **diagnostics.wadcfgx** definiert. Öffnen Sie diese Datei (sie ist basierend auf der Rolle definiert) in Visual Studio, und suchen Sie nach dem Element **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters**. Fügen Sie ein neues untergeordnetes Element **PerformanceCounterConfiguration** hinzu. Dieses Element umfasst zwei Attribute: `counterSpecifier` und `sampleRate`. Das Attribut `counterSpecifier` definiert, welcher Satz an Systemleistungsindikatoren (siehe vorheriger Abschnitt) erfasst werden soll. Der Wert `sampleRate` gibt an, wie oft dieser Wert abgerufen wird. Alle Leistungsindikatoren werden gemäß Attributwert `scheduledTransferPeriod` des übergeordneten Elements `PerformanceCounters` an Azure übertragen.

Weitere Informationen zum Schemaelement `PerformanceCounters` finden Sie unter [Schema der Azure-Diagnose](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element).

Der durch das Attribut `sampleRate` definierte Zeitraum verwendet den XML-Zeitraumdatentyp, um anzugeben, wie häufig der Leistungsindikator abgerufen wird. Im nachstehenden Beispiel ist die Rate auf `PT3M` festgelegt, dies bedeutet `[P]eriod[T]ime[3][M]inutes`: alle drei Minuten.

Weitere Informationen zur Definition von `sampleRate` und `scheduledTransferPeriod` finden Sie im Abschnitt **Duration Data Type** (Zeitraumdatentyp) im Tutorial[W3 XML Date and Time Date Types](https://www.w3schools.com/XML/schema_dtypes_date.asp) (W3 XML-Zeitraum- und Uhrzeitdatentypen).

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Erstellen eines neuen Leistungsindikators

Sie können in Ihrem Code einen neuen Leistungsindikator erstellen und verwenden. Ihr Code muss zum Erstellen eines neuen Leistungsindikators mit erhöhten Rechten ausgeführt werden, ansonsten kommt es zu einem Fehler. Der `OnStart`-Startcode für Ihren Clouddienst kann den Leistungsindikator erstellen, hierbei muss die Rolle in einem Kontext mit erhöhten Rechten ausgeführt werden. Alternativ können Sie eine Startaufgabe erstellen, die mit erhöhten Rechten ausgeführt wird und den Leistungsindikator erstellt. Weitere Informationen zu Startaufgaben finden Sie unter [Konfigurieren und Ausführen von Startaufgaben für einen Clouddienst](cloud-services-startup-tasks.md).

Um Ihre Rolle für die Ausführung mit erhöhten Rechten zu konfigurieren, fügen Sie ein `<Runtime>`-Element in die Datei [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) ein.

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

Sie können mit einigen wenigen Codezeilen einen neuen Leistungsindikator erstellen und registrieren. Verwenden Sie die `System.Diagnostics.PerformanceCounterCategory.Create`-Methodenüberladung, mit der sowohl die Kategorie als auch der Indikator erstellt wird. Im folgenden Code wird zunächst überprüft, ob die Kategorie vorhanden ist. Falls nicht, wird sowohl die Kategorie als auch der Indikator erstellt.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Wenn Sie den Indikator verwenden möchten, rufen Sie die Methode `Increment` oder `IncrementBy` auf.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Jetzt, da Ihre Anwendung den benutzerdefinierten Indikator verwendet, müssen Sie die Azure-Diagnose oder Application Insights zur Nachverfolgung des Indikators konfigurieren.


### <a name="application-insights"></a>Application Insights

Wie zuvor erwähnt, werden die Leistungsindikatoren für Application Insights in der Datei **ApplicationInsights.config** definiert. Öffnen Sie die Datei **ApplicationInsights.config**, und suchen Sie nach dem Element **ApplicationInsights** > **TelemetryModules** > **Add** > **Counters**. Erstellen Sie ein untergeordnetes Element `<Add>`, und legen Sie die Attribut `PerformanceCounter` auf die Kategorie und den Namen des Leistungsindikators fest, den Sie in Ihrem Code erstellt haben. Legen Sie das Attribut `ReportAs` auf einen benutzerfreundlichen Namen fest, der im Portal angezeigt werden soll.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure-Diagnose

Wie zuvor erwähnt, werden die zu erfassenden Leistungsindikatoren in der Datei **diagnostics.wadcfgx** definiert. Öffnen Sie diese Datei (sie ist basierend auf der Rolle definiert) in Visual Studio, und suchen Sie nach dem Element **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters**. Fügen Sie ein neues untergeordnetes Element **PerformanceCounterConfiguration** hinzu. Legen Sie das Attribut `counterSpecifier` auf die Kategorie und den Namen des Leistungsindikators fest, den Sie in Ihrem Code erstellt haben. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Weitere Informationen

- [Application Insights für Azure Cloud Services](../azure-monitor/app/cloudservices.md#performance-counters)
- [Systemleistungsindikatoren in Application Insights](../azure-monitor/app/performance-counters.md)
- [Angeben eines Indikatorpfads](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Schema der Azure-Diagnose – Leistungsindikatoren](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)
