---
title: Konfigurationsschema für Erweiterung 1.2 der Azure-Diagnose
description: NUR relevant, wenn Sie Azure SDK 2.5 mit Azure Virtual Machines, VM-Skalierungsgruppen, Service Fabric oder Cloud Services verwenden.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: dae74e730d6e175fa3e447150adce4caecd3d7a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237839"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Konfigurationsschema für die Azure-Diagnose 1.2
> [!NOTE]
> Azure-Diagnose ist die Komponente, die zum Erfassen von Leistungsindikatoren und anderen Statistiken aus Azure Virtual Machines, VM-Skalierungsgruppen, Service Fabric und Cloud Services verwendet wird.  Diese Seite ist nur relevant, wenn Sie einen dieser Dienste verwenden.
>

Die Azure-Diagnose wird zusammen mit anderen Microsoft-Diagnoseprodukten wie der Plattform Azure Monitor verwendet, die Application Insights und Log Analytics einschließt.

Dieses Schema definiert die möglichen Werte, die Sie verwenden können, um beim Start des Diagnosemonitors Diagnosekonfigurationseinstellungen zu initialisieren.  


 Laden Sie die Schemadefinition für die öffentliche Konfigurationsdatei mit dem folgenden PowerShell-Befehl herunter:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Weitere Informationen zur Verwendung der Azure-Diagnose erhalten Sie unter [Aktivieren der Diagnose in Azure Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Beispiel der Diagnosekonfigurationsdatei  
 Das folgende Beispiel zeigt eine typische Diagnosekonfigurationsdatei:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
  <WadCfg>  
    <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  
      <PerformanceCounters scheduledTransferPeriod="PT1M">  
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
      </PerformanceCounters>  
      <Directories scheduledTransferPeriod="PT5M">  
        <IISLogs containerName="iislogs" />  
        <FailedRequestLogs containerName="iisfailed" />  
        <DataSources>  
          <DirectoryConfiguration containerName="mynewprocess">  
            <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="badapp">  
            <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="goodapp">  
            <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
          </DirectoryConfiguration>  
        </DataSources>  
      </Directories>  
      <EtwProviders>  
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
          <Event id="0"/>  
          <Event id="1" eventDestination="errorTable"/>  
          <DefaultEvents />  
        </EtwEventSourceProviderConfiguration>  
        <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
          <Event id="0"/>  
          <DefaultEvents eventDestination="defaultTable"/>  
        </EtwManifestProviderConfiguration>  
      </EtwProviders>  
      <WindowsEventLog scheduledTransferPeriod="PT5M">  
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
        <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
        <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
      </WindowsEventLog>  
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Namespace der Diagnosekonfiguration  
 Der XML-Namespace für die Diagnosekonfigurationsdatei lautet wie folgt:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig-Element  
 Hierbei handelt es sich um das Element der obersten Ebene der Diagnosekonfigurationsdatei. In der folgenden Tabelle werden die Elemente der Konfigurationsdatei beschrieben:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**WadCfg**|Erforderlich. Konfigurationseinstellungen für die Telemetriedaten, die gesammelt werden|  
|**StorageAccount**|Der Name des Azure Storage-Kontos zum Speichern der Daten. Dieser kann auch als Parameter angegeben werden, wenn das Cmdlet „Set-AzureServiceDiagnosticsExtension“ ausgeführt wird.|  
|**LocalResourceDirectory**|Das Verzeichnis auf dem virtuellen Computer, das von Monitoring Agent zum Speichern von Ereignisdaten verwendet werden soll. Falls nicht anders festgelegt, wird das Standardverzeichnis verwendet:<br /><br /> Für eine Worker-/Webrolle: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Für einen virtuellen Computer: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Erforderliche Attribute:<br /><br /> -                      **path**: Das Verzeichnis auf dem System, das von der Azure-Diagnose verwendet wird<br /><br /> -                      **expandEnvironment**: Steuert, ob Umgebungsvariablen im Pfadnamen erweitert werden|  

## <a name="wadcfg-element"></a>WadCFG-Element  
Definiert Konfigurationseinstellungen für die Telemetriedaten, die gesammelt werden. Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Erforderlich. Optionale Attribute sind:<br /><br /> -                     **overallQuotaInMB**: Der maximale lokale Speicherplatz, der von den verschiedenen Typen von Diagnosedaten genutzt werden kann, die von der Azure-Diagnose erfasst werden. Die Standardeinstellung lautet 5.120 MB.<br /><br /> -                     **useProxyServer**: Konfigurieren Sie die Azure-Diagnose, um die Proxyservereinstellungen den IE-Einstellungen entsprechend zu verwenden.|  
|**CrashDumps**|Aktivieren Sie die Sammlung der Absturzabbilder. Optionale Attribute sind:<br /><br /> -                     **containerName**: Der Name des Blobcontainers in Ihrem Azure Storage-Konto, der zum Speichern der Absturzbilder verwendet wird<br /><br /> -                     **crashDumpType**: Konfiguriert die Azure-Diagnose für die Erfassung von kleinen oder vollständigen Absturzabbildern<br /><br /> -                     **directoryQuotaPercentage**: Konfiguriert den Prozentsatz von **overallQuotaInMB**, der für Absturzabbilder auf dem virtuellen Computer reserviert werden soll|  
|**DiagnosticInfrastructureLogs**|Aktivieren Sie die Sammlung der Protokolle, die von der Azure-Diagnose generiert wurde. Die Protokolle der Diagnoseinfrastruktur sind hilfreich für die Problembehandlung des Diagnosesystems selbst. Optionale Attribute sind:<br /><br /> -                     **scheduledTransferLogLevelFilter**: Konfiguriert den minimalen Schweregrad der erfassten Protokolle.<br /><br /> -                     **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet). Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**Directories**|Ermöglicht das Sammeln der Inhalte eines Verzeichnisses, der IIS-Zugriffsfehlerprotokolle und/oder der IIS-Protokolle. Optionales Attribut:<br /><br /> **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet). Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwProviders**|Konfiguriert die Erfassung der ETW-Ereignisse von EventSource und/oder der ETW-Manifest-basierten Anbieter|  
|**Metriken**|Dieses Element ermöglicht Ihnen das Generieren einer Leistungsindikatortabelle, die für schnelle Abfragen optimiert ist. Jeder Leistungsindikator, der im **PerformanceCounters**-Element definiert ist, wird in der Metriktabelle zusätzlich zur Leistungsindikatortabelle gespeichert. Erforderliches Attribut:<br /><br /> **resourceId**: Dies ist die Ressourcen-ID des virtuellen Computers, auf dem Sie die Azure-Diagnose bereitstellen. Rufen Sie **resourceID** im [Azure-Portal](https://portal.azure.com) ab. Wählen Sie **Durchsuchen** -> **Ressourcengruppen** ->  **<Name\>** aus. Klicken Sie auf die Kachel **Eigenschaften**, und kopieren Sie den Wert aus dem Feld **ID**.|  
|**PerformanceCounters**|Ermöglicht das Sammeln von Leistungsindikatoren. Optionales Attribut:<br /><br /> **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet). Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**WindowsEventLog**|Ermöglicht das Sammeln von Windows-Ereignisprotokollen. Optionales Attribut:<br /><br /> **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet). Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="crashdumps-element"></a>CrashDumps-Element  
 Aktiviert die Sammlung der Absturzabbilder Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Erforderlich. Erforderliches Attribut:<br /><br /> **processName**: Der Name des Prozesses, für den die Azure-Diagnose ein Absturzabbild erfassen soll|  
|**crashDumpType**|Konfiguriert die Azure-Diagnose für die Erfassung von kleinen oder vollständigen Absturzabbildern|  
|**directoryQuotaPercentage**|Konfiguriert den Prozentsatz der **overallQuotaInMB**, der für Absturzabbilder auf dem virtuellen Computer reserviert werden soll|  

## <a name="directories-element"></a>Directories-Element  
 Ermöglicht das Sammeln der Inhalte eines Verzeichnisses, der IIS-Zugriffsfehlerprotokolle und/oder der IIS-Protokolle. Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**DataSources**|Eine Liste der zu überwachenden Verzeichnisse|  
|**FailedRequestLogs**|Das Einbeziehen dieses Elements in die Konfiguration ermöglicht die Erfassung der Protokolle zu fehlgeschlagenen Anfragen an die IIS-Website oder -Anwendung. Zur Aktivierung müssen Sie auch die Verfolgungsoptionen in **Web.config** unter **system.WebServer** festlegen.|  
|**IISLogs**|Das Einbeziehen dieses Elements in die Konfiguration ermöglicht die Erfassung von IIS-Protokollen:<br /><br /> **containerName**: Der Name des Blobcontainers in Ihrem Azure Storage-Konto, der zum Speichern der IIS-Protokolle verwendet wird|  

## <a name="datasources-element"></a>DataSources-Element  
 Eine Liste der zu überwachenden Verzeichnisse Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Erforderlich. Erforderliches Attribut:<br /><br /> **containerName**: Der Name des Blobcontainers in Ihrem Azure Storage-Konto, der zum Speichern der Protokolldateien verwendet wird|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration-Element  
 **DirectoryConfiguration** kann das Element **Absolute** oder **LocalResource** enthalten, aber nicht beide. Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**Absolute**|Der absolute Pfad zum Verzeichnis, das überwacht werden soll Die folgenden Attribute sind erforderlich:<br /><br /> -                     **Path**: Der absolute Pfad zum Verzeichnis, das überwacht werden soll<br /><br /> -                      **expandEnvironment**: Konfiguriert, ob die Umgebungsvariablen im Pfad erweitert werden.|  
|**LocalResource**|Der Pfad in Relation zu einer lokalen Ressource, die überwacht werden soll. Erforderliche Attribute:<br /><br /> -                     **Name**: Die lokale Ressource, die das zu überwachende Verzeichnis enthält<br /><br /> -                     **relativePath**: Der Pfad in Relation zum Namen, der das zu überwachende Verzeichnis enthält|  

## <a name="etwproviders-element"></a>EtwProviders-Element  
 Konfiguriert die Erfassung der ETW-Ereignisse von EventSource und/oder der ETW-Manifest-basierten Anbieter Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfiguriert die Erfassung von Ereignissen, die über die [EventSource-Klasse](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx) generiert wurden. Erforderliches Attribut:<br /><br /> **provider**: Der Klassenname des EventSource-Ereignisses<br /><br /> Optionale Attribute sind:<br /><br /> -                     **scheduledTransferLogLevelFilter**: Der minimale Schweregrad, der in Ihr Speicherkonto übertragen wird<br /><br /> -                     **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet) Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Erforderliches Attribut:<br /><br /> **provider**: Die GUID des Ereignisanbieters<br /><br /> Optionale Attribute sind:<br /><br /> - **scheduledTransferLogLevelFilter**: Der minimale Schweregrad, der in Ihr Speicherkonto übertragen wird<br /><br /> -                     **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet) Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration-Element  
 Konfiguriert die Erfassung von Ereignissen, die über die [EventSource-Klasse](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx) generiert wurden. Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**DefaultEvents**|Optionales Attribut:<br /><br /> **eventDestination**: Der Name der Tabelle zum Speichern der Ereignisse|  
|**Event**|Erforderliches Attribut:<br /><br /> **id**: Die ID des Ereignisses<br /><br /> Optionales Attribut:<br /><br /> **eventDestination**: Der Name der Tabelle zum Speichern der Ereignisse|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration-Element  
 Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**DefaultEvents**|Optionales Attribut:<br /><br /> **eventDestination**: Der Name der Tabelle zum Speichern der Ereignisse|  
|**Event**|Erforderliches Attribut:<br /><br /> **id**: Die ID des Ereignisses<br /><br /> Optionales Attribut:<br /><br /> **eventDestination**: Der Name der Tabelle zum Speichern der Ereignisse|  

## <a name="metrics-element"></a>Metrics-Element  
 Ermöglicht Ihnen das Generieren einer Leistungsindikatortabelle, die für schnelle Abfragen optimiert ist. Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**MetricAggregation**|Erforderliches Attribut:<br /><br /> **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet) Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters-Element  
 Ermöglicht das Sammeln von Leistungsindikatoren. Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Die folgenden Attribute sind erforderlich:<br /><br /> -                     **counterSpecifier**: Der Name des Leistungsindikators Beispiel: `\Processor(_Total)\% Processor Time`. Führen Sie zum Abrufen einer Liste der Leistungsindikatoren auf Ihrem Host den Befehl `typeperf` aus.<br /><br /> -                     **sampleRate**: Gibt an, wie oft Stichproben für den Indikator erstellt werden.<br /><br /> Optionales Attribut:<br /><br /> **unit**: Die Maßeinheit des Indikators|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration-Element  
 Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**Anmerkung**|Erforderliches Attribut:<br /><br /> **displayName**: Der Anzeigename für den Indikator<br /><br /> Optionales Attribut:<br /><br /> **locale**: Das Gebietsschema, das bei der Anzeige des Indikatornamens verwendet werden soll|  

## <a name="windowseventlog-element"></a>WindowsEventLog-Element  
 Die folgende Tabelle beschreibt die untergeordneten Elemente:  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**DataSource**|Die Windows-Ereignisprotokolle, die erfasst werden sollen. Erforderliches Attribut:<br /><br /> **name**: Die XPath-Abfrage, die die zu erfassenden Windows-Ereignisse beschreibt. Beispiel:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Zum Erfassen aller Ereignisse geben Sie „*“ ein.|

