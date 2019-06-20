---
title: Konfigurationsschema für die Azure-Diagnose 1.0
description: NUR relevant, wenn Sie Azure SDK 2.4 und früher mit Azure Virtual Machines, VM-Skalierungsgruppen, Service Fabric oder Cloud Services verwenden.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: ac2b79d670b803573a359dfc9f8738f972f2d9b5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237861"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Konfigurationsschema für die Azure-Diagnose 1.0
> [!NOTE]
> Azure-Diagnose ist die Komponente, die zum Erfassen von Leistungsindikatoren und anderen Statistiken aus Azure Virtual Machines, VM-Skalierungsgruppen, Service Fabric und Cloud Services verwendet wird.  Diese Seite ist nur relevant, wenn Sie einen dieser Dienste verwenden.
>

Die Azure-Diagnose wird zusammen mit anderen Microsoft-Diagnoseprodukten wie der Plattform Azure Monitor verwendet, die Application Insights und Log Analytics einschließt.

Die Azure-Diagnosekonfigurationsdatei definiert Werte, die verwendet werden, um den Diagnosemonitor zu initialisieren. Diese Datei wird verwendet, um beim Start des Diagnosemonitors Diagnosekonfigurationseinstellungen zu initialisieren.  

 Standardmäßig wird die Konfigurationsschemadatei der Azure-Diagnose im `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas`-Verzeichnis installiert. Ersetzen Sie `<version>` durch die installierte Version des [Azure SDK](https://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Die Diagnosekonfigurationsdatei wird in der Regel mit Starttasks verwendet, die das Sammeln von Diagnosedaten zu einem früheren Zeitpunkt im Startprozess erfordern. Weitere Informationen über die Verwendung der Azure-Diagnose finden Sie unter [Sammeln von Protokollierungsdaten mit der Azure-Diagnose](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Beispiel der Diagnosekonfigurationsdatei  
 Das folgende Beispiel zeigt eine typische Diagnosekonfigurationsdatei:  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>DiagnosticsConfiguration-Namespace  
 Der XML-Namespace für die Diagnosekonfigurationsdatei lautet wie folgt:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Schema-Elemente  
 Die Diagnosekonfigurationsdatei enthält die folgenden Elemente:


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration-Element  
Das Element der obersten Ebene der Diagnosekonfigurationsdatei  

Attribute:

|Attribut  |Type   |Erforderlich| Standard | BESCHREIBUNG|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|Optional | PT1M| Gibt das Intervall an, in dem der Diagnosemonitor Diagnosekonfigurationsänderungen abruft.|  
|**overallQuotaInMB**|unsignedInt|Optional| 4\.000 MB. Wenn Sie einen Wert angeben, darf er diese Menge nicht übersteigen. |Die Gesamtmenge des Dateisystemspeichers, die für alle Protokollierungspuffer zugewiesen wurde|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs-Element  
Definiert die Pufferkonfiguration für die Protokolle, die von der zugrunde liegenden Diagnoseinfrastruktur generiert werden.

Übergeordnetes Element: DiagnosticMonitorConfiguration-Element.  

Attribute:

|Attribut|Type|BESCHREIBUNG|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Optional. Gibt die Höchstmenge des Dateisystemspeichers an, der für die angegebenen Daten verfügbar ist.<br /><br /> Der Standardwert ist 0.|  
|**scheduledTransferLogLevelFilter**|Zeichenfolge|Optional. Gibt den minimalen Schweregrad für Protokolleinträge an, die übertragen werden. Der Standardwert lautet **Undefined**. Weitere mögliche Werte sind **Verbose**, **Information**, **Warning**, **Error** und **Critical**.|  
|**scheduledTransferPeriod**|duration|Optional. Gibt das Intervall zwischen geplanten Datenübertragungen an (minutengenau gerundet).<br /><br /> Die Standardeinstellung lautet „PT0S“.|  

## <a name="logs-element"></a>Logs-Element  
 Definiert die Pufferkonfiguration für grundlegende Azure-Protokolle.

 Übergeordnetes Element: DiagnosticMonitorConfiguration-Element.  

Attribute:  

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Optional. Gibt die Höchstmenge des Dateisystemspeichers an, der für die angegebenen Daten verfügbar ist.<br /><br /> Der Standardwert ist 0.|  
|**scheduledTransferLogLevelFilter**|Zeichenfolge|Optional. Gibt den minimalen Schweregrad für Protokolleinträge an, die übertragen werden. Der Standardwert lautet **Undefined**. Weitere mögliche Werte sind **Verbose**, **Information**, **Warning**, **Error** und **Critical**.|  
|**scheduledTransferPeriod**|duration|Optional. Gibt das Intervall zwischen geplanten Datenübertragungen an (minutengenau gerundet).<br /><br /> Die Standardeinstellung lautet „PT0S“.|  

## <a name="directories-element"></a>Directories-Element  
Definiert die Pufferkonfiguration für dateibasierte Protokolle, die Sie definieren können.

Übergeordnetes Element: DiagnosticMonitorConfiguration-Element.  


Attribute:  

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Optional. Gibt die Höchstmenge des Dateisystemspeichers an, der für die angegebenen Daten verfügbar ist.<br /><br /> Der Standardwert ist 0.|  
|**scheduledTransferPeriod**|duration|Optional. Gibt das Intervall zwischen geplanten Datenübertragungen an (minutengenau gerundet).<br /><br /> Die Standardeinstellung lautet „PT0S“.|  

## <a name="crashdumps-element"></a>CrashDumps-Element  
 Definiert das Absturzabbildverzeichnis.

 Übergeordnetes Element: Directories-Element.  

Attribute:  

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**container**|Zeichenfolge|Der Name des Containers, an den der Inhalt des Verzeichnisses übertragen werden soll.|  
|**directoryQuotaInMB**|unsignedInt|Optional. Gibt die maximale Größe des Verzeichnisses in Megabyte an.<br /><br /> Der Standardwert ist 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs-Element  
 Definiert das Protokollverzeichnis für fehlerhafte Anfragen.

 Übergeordnetes Element: Directories-Element.  

Attribute:  

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**container**|Zeichenfolge|Der Name des Containers, an den der Inhalt des Verzeichnisses übertragen werden soll.|  
|**directoryQuotaInMB**|unsignedInt|Optional. Gibt die maximale Größe des Verzeichnisses in Megabyte an.<br /><br /> Der Standardwert ist 0.|  

##  <a name="iislogs-element"></a>IISLogs-Element  
 Definiert das IIS-Protokollverzeichnis.

 Übergeordnetes Element: Directories-Element.  

Attribute:  

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**container**|Zeichenfolge|Der Name des Containers, an den der Inhalt des Verzeichnisses übertragen werden soll.|  
|**directoryQuotaInMB**|unsignedInt|Optional. Gibt die maximale Größe des Verzeichnisses in Megabyte an.<br /><br /> Der Standardwert ist 0.|  

## <a name="datasources-element"></a>DataSources-Element  
 Definiert zusätzliche Protokollverzeichnisse.

 Übergeordnetes Element: Directories-Element.

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration-Element  
 Definiert das Verzeichnis der zu überwachenden Protokolldateien.

 Übergeordnetes Element: DataSources-Element.

Attribute:

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**container**|Zeichenfolge|Der Name des Containers, an den der Inhalt des Verzeichnisses übertragen werden soll.|  
|**directoryQuotaInMB**|unsignedInt|Optional. Gibt die maximale Größe des Verzeichnisses in Megabyte an.<br /><br /> Der Standardwert ist 0.|  

## <a name="absolute-element"></a>Absolute-Element  
 Definiert einen absoluten Pfad des Verzeichnisses zur Überwachung mit optionaler Umgebungserweiterung.

 Übergeordnetes Element: DirectoryConfiguration-Element.  

Attribute:  

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**path**|Zeichenfolge|Erforderlich. Der absolute Pfad zum Verzeichnis, das überwacht werden soll|  
|**expandEnvironment**|boolean|Erforderlich. Umgebungsvariablen im Pfad werden erweitert, wenn für sie **true** festgelegt ist.|  

## <a name="localresource-element"></a>LocalResource-Element  
 Definiert einen Pfad in Relation zu einer lokalen Ressource, die in der Dienstdefinition definiert ist.

 Übergeordnetes Element: DirectoryConfiguration-Element.  

Attribute:  

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**name**|Zeichenfolge|Erforderlich. Der Name der lokalen Ressource, die das zu überwachende Verzeichnis enthält|  
|**relativePath**|Zeichenfolge|Erforderlich. Der Pfad in Relation zur lokalen Ressource, die überwacht werden soll|  

## <a name="performancecounters-element"></a>PerformanceCounters-Element  
 Definiert den Pfad zum zu erfassenden Leistungsindikator.

 Übergeordnetes Element: DiagnosticMonitorConfiguration-Element.


 Attribute:  

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Optional. Gibt die Höchstmenge des Dateisystemspeichers an, der für die angegebenen Daten verfügbar ist.<br /><br /> Der Standardwert ist 0.|  
|**scheduledTransferPeriod**|duration|Optional. Gibt das Intervall zwischen geplanten Datenübertragungen an (minutengenau gerundet).<br /><br /> Die Standardeinstellung lautet „PT0S“.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration-Element  
 Definiert den zu erfassenden Leistungsindikator.

 Übergeordnetes Element: PerformanceCounters-Element.  

 Attribute:  

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**counterSpecifier**|Zeichenfolge|Erforderlich. Der Pfad zum zu erfassenden Leistungsindikator|  
|**sampleRate**|duration|Erforderlich. Die Rate, mit der der Leistungsindikator erfasst werden soll|  

## <a name="windowseventlog-element"></a>WindowsEventLog-Element  
 Definiert die zu überwachenden Ereignisprotokolle.

 Übergeordnetes Element: DiagnosticMonitorConfiguration-Element.

  Attribute:

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Optional. Gibt die Höchstmenge des Dateisystemspeichers an, der für die angegebenen Daten verfügbar ist.<br /><br /> Der Standardwert ist 0.|  
|**scheduledTransferLogLevelFilter**|Zeichenfolge|Optional. Gibt den minimalen Schweregrad für Protokolleinträge an, die übertragen werden. Der Standardwert lautet **Undefined**. Weitere mögliche Werte sind **Verbose**, **Information**, **Warning**, **Error** und **Critical**.|  
|**scheduledTransferPeriod**|duration|Optional. Gibt das Intervall zwischen geplanten Datenübertragungen an (minutengenau gerundet).<br /><br /> Die Standardeinstellung lautet „PT0S“.|  

## <a name="datasource-element"></a>DataSource-Element  
 Definiert das zu überwachende Ereignisprotokoll.

 Übergeordnetes Element: WindowsEventLog-Element.  

 Attribute:

|Attribut|Type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**name**|Zeichenfolge|Erforderlich. Ein XPath-Ausdruck, der das zu erfassende Protokoll angibt|  

