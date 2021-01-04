---
title: Schema der Diagnoseerweiterung für Windows
description: Konfigurationsschemareferenz für die Azure-Diagnoseerweiterung für Windows in Azure Monitor.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: d2b1afea746410e966b43bef01a039a8471d4ae7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008819"
---
# <a name="windows-diagnostics-extension-schema"></a>Schema der Diagnoseerweiterung für Windows
Die Azure-Diagnoseerweiterung ist ein Agent in Azure Monitor, der Überwachungsdaten vom Gastbetriebssystem und den Workloads von Azure-Computeressourcen sammelt. In diesem Artikel wird das Schema erläutert, das zur Konfiguration der Diagnoseerweiterung auf virtuellen Windows-Computern und anderen Computeressourcen verwendet wird.

> [!NOTE]
> Das Schema in diesem Artikel gilt für Version 1.3 und höher (Azure SDK 2.4 und höher). Neuere Konfigurationsabschnitte enthalten einen Kommentar mit der Version, in der sie hinzugefügt wurden. Version 1.0 und 1.2 des Schemas wurden archiviert und sind nicht mehr verfügbar. 

## <a name="public-configuration-file-schema"></a>Schema der öffentlichen Konfigurationsdatei

Laden Sie die Schemadefinition für die öffentliche Konfigurationsdatei mit dem folgenden PowerShell-Befehl herunter:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Allgemeine Attributtypen  
 Das **scheduledTransferPeriod**-Attribut wird in mehreren Elementen angezeigt. Es handelt sich um das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet). Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp).


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration-Element  
 *Tree: Root – DiagnosticsConfiguration*

Hinzugefügt in Version 1.3.  

Das Element der obersten Ebene der Diagnosekonfigurationsdatei  

**Attribute** xmlns – Der XML-Namespace für die Diagnosekonfigurationsdatei ist:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**PublicConfig**|Erforderlich. Siehe Beschreibung an anderer Stelle auf dieser Seite.|  
|**PrivateConfig**|Optional. Siehe Beschreibung an anderer Stelle auf dieser Seite.|  
|**IsEnabled**|Boolesch. Siehe Beschreibung an anderer Stelle auf dieser Seite.|  

## <a name="publicconfig-element"></a>PublicConfig-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig*

 Beschreibt die öffentliche Diagnosekonfiguration  

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**WadCfg**|Erforderlich. Siehe Beschreibung an anderer Stelle auf dieser Seite.|  
|**StorageAccount**|Der Name des Azure Storage-Kontos zum Speichern der Daten. Kann auch als Parameter angegeben werden, wenn das Cmdlet „Set-AzureServiceDiagnosticsExtension“ ausgeführt wird.|  
|**StorageType**|Kann *Table*, *Blob* oder *TableAndBlob* sein. Table ist der Standardwert. Wenn TableAndBlob gewählt wird, werden die Diagnosedaten zwei Mal geschrieben – ein Mal in jeden Typ.|  
|**LocalResourceDirectory**|Das Verzeichnis auf dem virtuellen Computer, auf dem der Überwachungs-Agent Ereignisdaten speichert. Falls nicht anders festgelegt, wird das Standardverzeichnis verwendet:<br /><br /> Für eine Worker-/Webrolle: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Für einen virtuellen Computer: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Erforderliche Attribute:<br /><br /> - **path**: Das Verzeichnis auf dem System, das von der Azure-Diagnose verwendet wird<br /><br /> - **expandEnvironment**: Steuert, ob Umgebungsvariablen im Pfadnamen erweitert werden|  

## <a name="wadcfg-element"></a>WadCFG-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG*

 Identifiziert und konfiguriert die Telemetriedaten, die gesammelt werden sollen.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration-Element
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration*

 Erforderlich

|Attributes|BESCHREIBUNG|  
|----------------|-----------------|  
| **overallQuotaInMB** | Der maximale lokale Speicherplatz, der von den verschiedenen Typen von Diagnosedaten genutzt werden kann, die von der Azure-Diagnose erfasst werden. Die Standardeinstellung lautet 4.096 MB.<br />
|**useProxyServer** | Konfigurieren Sie die Azure-Diagnose, um die Proxyservereinstellungen den IE-Einstellungen entsprechend zu verwenden.|
|**Senken** | Hinzugefügt in 1.5. Optional. Verweist auf einen Senkenspeicherort, um auch Diagnosedaten für alle untergeordneten Elemente zu senden, die Senken unterstützen. Senkenbeispiele sind Application Insights oder Event Hubs. Beachten Sie, dass Sie die Eigenschaft *resourceId* unter dem Element *Metrics* hinzufügen müssen, wenn in Event Hubs hochgeladene Ereignisse über eine Ressourcen-ID verfügen sollen. |  


<br /> <br />

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**CrashDumps**|Siehe Beschreibung an anderer Stelle auf dieser Seite.|  
|**DiagnosticInfrastructureLogs**|Aktivieren Sie die Sammlung der Protokolle, die von der Azure-Diagnose generiert wurde. Die Protokolle der Diagnoseinfrastruktur sind hilfreich für die Problembehandlung des Diagnosesystems selbst. Optionale Attribute sind:<br /><br /> - **scheduledTransferLogLevelFilter**: Konfiguriert den minimalen Schweregrad der erfassten Protokolle.<br /><br /> - **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet). Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp). |  
|**Verzeichnisse**|Siehe Beschreibung an anderer Stelle auf dieser Seite.|  
|**EtwProviders**|Siehe Beschreibung an anderer Stelle auf dieser Seite.|  
|**Metriken**|Siehe Beschreibung an anderer Stelle auf dieser Seite.|  
|**PerformanceCounters**|Siehe Beschreibung an anderer Stelle auf dieser Seite.|  
|**WindowsEventLog**|Siehe Beschreibung an anderer Stelle auf dieser Seite.|
|**DockerSources**|Siehe Beschreibung an anderer Stelle auf dieser Seite. |



## <a name="crashdumps-element"></a>CrashDumps-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – CrashDumps*

 Aktivieren Sie die Sammlung der Absturzabbilder.  

|Attributes|BESCHREIBUNG|  
|----------------|-----------------|  
|**containerName**|Optional. Der Name des Blobcontainers in Ihrem Azure Storage-Konto, der zum Speichern der Absturzabbilder verwendet wird|  
|**crashDumpType**|Optional.  Konfiguriert die Azure-Diagnose für die Erfassung von kleinen oder vollständigen Absturzabbildern.|  
|**directoryQuotaPercentage**|Optional.  Konfiguriert den Prozentsatz der **overallQuotaInMB**, der für Absturzabbilder auf dem virtuellen Computer reserviert werden soll.|  

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Erforderlich. Definiert die Konfigurationswerte für jeden Prozess.<br /><br /> Das folgende Attribut ist ebenso erforderlich:<br /><br /> **processName**: Der Name des Prozesses, für den die Azure-Diagnose ein Absturzabbild erfassen soll|  

## <a name="directories-element"></a>Directories-Element
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – Directories*

 Ermöglicht das Sammeln der Inhalte eines Verzeichnisses, der IIS-Zugriffsfehlerprotokolle und/oder der IIS-Protokolle.  

 Optionales **scheduledTransferPeriod**-Attribut. Siehe Erklärung weiter oben.  

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**IISLogs**|Das Einbeziehen dieses Elements in die Konfiguration ermöglicht die Erfassung von IIS-Protokollen:<br /><br /> **containerName**: Der Name des Blobcontainers in Ihrem Azure Storage-Konto, der zum Speichern der IIS-Protokolle verwendet wird|   
|**FailedRequestLogs**|Das Einbeziehen dieses Elements in die Konfiguration ermöglicht die Erfassung der Protokolle zu fehlgeschlagenen Anfragen an die IIS-Website oder -Anwendung. Zur Aktivierung müssen Sie auch die Verfolgungsoptionen in **Web.config** unter **system.WebServer** festlegen.|  
|**DataSources**|Eine Liste der zu überwachenden Verzeichnisse|




## <a name="datasources-element"></a>DataSources-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – Directories – DataSources*

 Eine Liste der zu überwachenden Verzeichnisse  

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Erforderlich. Erforderliches Attribut:<br /><br /> **containerName**: Der Name des Blobcontainers in Ihrem Azure Storage-Konto, der zum Speichern der Protokolldateien verwendet wird|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – Directories – DataSources – DirectoryConfiguration*

 Kann das Element **Absolute** oder **LocalResource** enthalten, aber nicht beide.  

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**Absolute**|Der absolute Pfad zum Verzeichnis, das überwacht werden soll Die folgenden Attribute sind erforderlich:<br /><br /> - **Path**: Der absolute Pfad zum Verzeichnis, das überwacht werden soll<br /><br /> - **expandEnvironment**: Konfiguriert, ob die Umgebungsvariablen im Pfad erweitert werden.|  
|**LocalResource**|Der Pfad in Relation zu einer lokalen Ressource, die überwacht werden soll. Erforderliche Attribute:<br /><br /> - **Name**: Die lokale Ressource, die das zu überwachende Verzeichnis enthält<br /><br /> - **relativePath**: Der Pfad in Relation zum Namen, der das zu überwachende Verzeichnis enthält|  



## <a name="etwproviders-element"></a>EtwProviders-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – EtwProviders*

 Konfiguriert die Erfassung der ETW-Ereignisse von EventSource und/oder der ETW-Manifest-basierten Anbieter  

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfiguriert die Erfassung von Ereignissen, die über die [EventSource-Klasse](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1) generiert wurden. Erforderliches Attribut:<br /><br /> **provider**: Der Klassenname des EventSource-Ereignisses<br /><br /> Optionale Attribute sind:<br /><br /> - **scheduledTransferLogLevelFilter**: Der minimale Schweregrad, der in Ihr Speicherkonto übertragen wird<br /><br /> - **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet). Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp). |  
|**EtwManifestProviderConfiguration**|Erforderliches Attribut:<br /><br /> **provider**: Die GUID des Ereignisanbieters<br /><br /> Optionale Attribute sind:<br /><br /> - **scheduledTransferLogLevelFilter**: Der minimale Schweregrad, der in Ihr Speicherkonto übertragen wird<br /><br /> - **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet). Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp). |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – EtwProviders – EtwEventSourceProviderConfiguration*

 Konfiguriert die Erfassung von Ereignissen, die über die [EventSource-Klasse](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1) generiert wurden.  

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**DefaultEvents**|Optionales Attribut:<br/><br/> **eventDestination**: Der Name der Tabelle zum Speichern der Ereignisse|  
|**Event**|Erforderliches Attribut:<br /><br /> **id**: Die ID des Ereignisses<br /><br /> Optionales Attribut:<br /><br /> **eventDestination**: Der Name der Tabelle zum Speichern der Ereignisse|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – EtwProviders – EtwManifestProviderConfiguration*

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**DefaultEvents**|Optionales Attribut:<br /><br /> **eventDestination**: Der Name der Tabelle zum Speichern der Ereignisse|  
|**Event**|Erforderliches Attribut:<br /><br /> **id**: Die ID des Ereignisses<br /><br /> Optionales Attribut:<br /><br /> **eventDestination**: Der Name der Tabelle zum Speichern der Ereignisse|  



## <a name="metrics-element"></a>Metrics-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – Metrics*

 Ermöglicht Ihnen das Generieren einer Leistungsindikatortabelle, die für schnelle Abfragen optimiert ist. Jeder Leistungsindikator, der im **PerformanceCounters**-Element definiert ist, wird in der Metriktabelle zusätzlich zur Leistungsindikatortabelle gespeichert.  

 Das **resourceId**-Attribut muss angegeben werden.  Die Ressourcen-ID des virtuellen Computers oder der VM-Skalierungsgruppe, auf dem bzw. in der Sie die Azure-Diagnose bereitstellen. Rufen Sie **resourceID** im [Azure-Portal](https://portal.azure.com) ab. Wählen Sie **Durchsuchen** -> **Ressourcengruppen** ->  **<Name\>** aus. Klicken Sie auf die Kachel **Eigenschaften**, und kopieren Sie den Wert aus dem Feld **ID**.  Die Eigenschaft „resourceID“ wird sowohl zum Senden von benutzerdefinierten Metriken als auch zum Hinzufügen einer resourceID-Eigenschaft zu Daten verwendet, die an Event Hubs gesendet werden. Beachten Sie, dass Sie die Eigenschaft *resourceId* unter dem Element *Metrics* hinzufügen müssen, wenn in Event Hubs hochgeladene Ereignisse über eine Ressourcen-ID verfügen sollen.

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**MetricAggregation**|Erforderliches Attribut:<br /><br /> **scheduledTransferPeriod**: Das Intervall zwischen geplanten Datenübertragungen an den Speicher (minutengenau gerundet). Der Wert ist ein [Dauer-Datentyp im XML-Format](https://www.w3schools.com/xml/schema_dtypes_date.asp). |  



## <a name="performancecounters-element"></a>PerformanceCounters-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – PerformanceCounters*

 Ermöglicht das Sammeln von Leistungsindikatoren.  

 Optionales Attribut:  

 Optionales **scheduledTransferPeriod**-Attribut. Siehe Erklärung weiter oben.

|Untergeordnetes Element|BESCHREIBUNG|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Die folgenden Attribute sind erforderlich:<br /><br /> - **counterSpecifier**: Der Name des Leistungsindikators Beispiel: `\Processor(_Total)\% Processor Time`. Führen Sie zum Abrufen einer Liste der Leistungsindikatoren auf Ihrem Host den Befehl `typeperf` aus.<br /><br /> - **sampleRate**: Gibt an, wie oft Stichproben für den Indikator erstellt werden.<br /><br /> Optionales Attribut:<br /><br /> **unit**: Die Maßeinheit des Indikators Die Werte sind in der [UnitType-Klasse](/dotnet/api/microsoft.azure.management.sql.models.unittype?view=azure-dotnet) verfügbar. |
|**Senken** | Hinzugefügt in 1.5. Optional. Verweist auf einen Senkenspeicherort, um auch Diagnosedaten zu senden. Beispiel: Azure Monitor oder Event Hubs. Beachten Sie, dass Sie die Eigenschaft *resourceId* unter dem Element *Metrics* hinzufügen müssen, wenn in Event Hubs hochgeladene Ereignisse über eine Ressourcen-ID verfügen sollen.|    




## <a name="windowseventlog-element"></a>WindowsEventLog-Element
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – WindowsEventLog*

 Ermöglicht das Sammeln von Windows-Ereignisprotokollen.  

 Optionales **scheduledTransferPeriod**-Attribut. Siehe Erklärung weiter oben.  

|Untergeordnetes Element|BESCHREIBUNG|  
|-------------------|-----------------|  
|**DataSource**|Die Windows-Ereignisprotokolle, die erfasst werden sollen. Erforderliches Attribut:<br /><br /> **name**: Die XPath-Abfrage, die die zu erfassenden Windows-Ereignisse beschreibt. Beispiel:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Zum Erfassen aller Ereignisse geben Sie „*“ ein. |
|**Senken** | Hinzugefügt in 1.5. Optional. Verweist auf einen Senkenspeicherort, um auch Diagnosedaten für alle untergeordneten Elemente zu senden, die Senken unterstützen. Senkenbeispiele sind Application Insights oder Event Hubs.|  


## <a name="logs-element"></a>Logs-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – Logs*

 Vorhanden in Version 1.0 und 1.1. Nicht vorhanden in 1.2. Wieder vorhanden in 1.3.  

 Definiert die Pufferkonfiguration für grundlegende Azure-Protokolle.  

|attribute|type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Optional. Gibt die Höchstmenge des Dateisystemspeichers an, der für die angegebenen Daten verfügbar ist.<br /><br /> Die Standardeinstellung ist 0.|  
|**scheduledTransferLogLevelFilter**|**string**|Optional. Gibt den minimalen Schweregrad für Protokolleinträge an, die übertragen werden. Der Standardwert ist **Undefined**, der alle Protokolle überträgt. Weitere mögliche Werte (meiste Informationen bis wenigste Informationen) sind **Verbose**, **Information**, **Warning**, **Error** und **Critical**.|  
|**scheduledTransferPeriod**|**duration**|Optional. Gibt das Intervall zwischen geplanten Datenübertragungen an (minutengenau gerundet).<br /><br /> Die Standardeinstellung lautet „PT0S“.|  
|**Senken** |**string**| Hinzugefügt in 1.5. Optional. Verweist auf einen Senkenspeicherort, um auch Diagnosedaten zu senden. Beispielsweise Application Insights oder Event Hubs. Beachten Sie, dass Sie die Eigenschaft *resourceId* unter dem Element *Metrics* hinzufügen müssen, wenn in Event Hubs hochgeladene Ereignisse über eine Ressourcen-ID verfügen sollen.|  

## <a name="dockersources"></a>DockerSources
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – DiagnosticMonitorConfiguration – DockerSources*

 In 1.9 hinzugefügt.

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**Stats**|Weist das System an, Statistikinformationen für Docker-Container zu sammeln.|  

## <a name="sinksconfig-element"></a>SinksConfig-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – SinksConfig*

 Eine Liste mit Standorten, an die die Diagnosedaten und die diesen Standorten zugeordnete Konfiguration gesendet werden soll.  

|Elementname|BESCHREIBUNG|  
|------------------|-----------------|  
|**Senke**|Siehe Beschreibung an anderer Stelle auf dieser Seite.|  

## <a name="sink-element"></a>Sink-Element
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – SinksConfig – Sink*

 Hinzugefügt in Version 1.5.  

 Definiert die Standorte, an die die Diagnosedaten gesendet werden sollen. Beispiel: der Application Insights-Dienst.  

|attribute|type|BESCHREIBUNG|  
|---------------|----------|-----------------|  
|**name**|Zeichenfolge|Eine Zeichenfolge für den Senkennamen.|  

|Element|type|BESCHREIBUNG|  
|-------------|----------|-----------------|  
|**Application Insights**|Zeichenfolge|Wird nur beim Senden von Daten an Application Insights verwendet. Enthält den Instrumentationsschlüssel für ein aktives Application Insights-Konto, für das Sie Zugriff besitzen.|  
|**Channels**|Zeichenfolge|Einer für jeden zusätzlichen Filter, den Sie streamen|  

## <a name="channels-element"></a>Channels-Element  
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – SinksConfig – Sink – Channels*

 Hinzugefügt in Version 1.5.  

 Definiert die Filter für Datenströme von Protokolldaten, die durch eine Senke übergeben werden.  

|Element|type|BESCHREIBUNG|  
|-------------|----------|-----------------|  
|**Channel**|Zeichenfolge|Siehe Beschreibung an anderer Stelle auf dieser Seite.|  

## <a name="channel-element"></a>Channel-Element
 *Tree: Root – DiagnosticsConfiguration – PublicConfig – WadCFG – SinksConfig – Sink – Channels – Channel*

 Hinzugefügt in Version 1.5.  

 Definiert die Standorte, an die die Diagnosedaten gesendet werden sollen. Beispiel: der Application Insights-Dienst.  

|Attributes|type|BESCHREIBUNG|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Gibt den minimalen Schweregrad für Protokolleinträge an, die übertragen werden. Der Standardwert ist **Undefined**, der alle Protokolle überträgt. Weitere mögliche Werte (meiste Informationen bis wenigste Informationen) sind **Verbose**, **Information**, **Warning**, **Error** und **Critical**.|  
|**name**|**string**|Ein eindeutiger Name des Kanals, auf den verwiesen wird|  


## <a name="privateconfig-element"></a>PrivateConfig-Element
 *Tree: Root – DiagnosticsConfiguration – PrivateConfig*

 Hinzugefügt in Version 1.3.  

 Optional  

 Speichert die privaten Details des Speicherkontos (Name, Schlüssel und Endpunkt). Diese Informationen werden an den virtuellen Computer gesendet, können aber nicht daraus abgerufen werden.  

|Untergeordnete Elemente|BESCHREIBUNG|  
|--------------------|-----------------|  
|**StorageAccount**|Das zu verwendende Speicherkonto. Die folgenden Attribute sind erforderlich:<br /><br /> - **name**: Der Name des Speicherkontos<br /><br /> - **key**: Der Schlüssel des Speicherkontos<br /><br /> - **endpoint**: Der Endpunkt für den Zugriff auf das Speicherkonto <br /><br /> -**sasToken** (in 1.8.1) – Sie können in der privaten Konfiguration ein SAS-Token anstelle eines Speicherkontoschlüssels angeben. Wenn er angegeben wird, wird der Speicherkontoschlüssel ignoriert. <br />Anforderungen für das SAS-Token: <br />– Es werden nur SAS-Kontotoken unterstützt <br />Die Diensttypen - *b*, *t* sind erforderlich. <br /> Die Berechtigungen - *a*, *c*, *u*, *w* sind erforderlich. <br /> Die Ressourcentypen - *c*, *o* sind erforderlich. <br /> – Unterstützt nur das HTTPS-Protokoll <br /> – Start und Ablaufzeit müssen gültig sein.|  


## <a name="isenabled-element"></a>IsEnabled-Element  
 *Tree: Root – DiagnosticsConfiguration – IsEnabled*

 Boolesch. Verwenden Sie `true`, um die Diagnose zu aktivieren, oder `false`, um die Diagnose zu deaktivieren.

## <a name="example-configuration"></a>Beispielkonfiguration
 Im Folgenden finden Sie eine vollständige Beispielkonfiguration für die Diagnoseerweiterung für Windows sowohl in JSON als auch in XML.

 
### <a name="json"></a>JSON

*PublicConfig* und *PrivateConfig* sind getrennt, da sie in den meisten JSON-Nutzungsszenarien als unterschiedliche Variablen übergeben werden. Zu diesen Szenarien gehören Resource Manager-Vorlagen, PowerShell und Visual Studio.

> [!NOTE]
> Die öffentliche Konfiguration der Azure Monitor-Senkendefinition verfügt über zwei Eigenschaften: *resourceId* und *region*. Diese sind nur für klassische virtuelle Computer und klassische Clouddienste erforderlich. Die Eigenschaft *region* sollte nicht für andere Ressourcen verwendet werden. Die Eigenschaft *resourceId* wird auf virtuellen ARM-Computern genutzt, um das Feld „resourceID“ in den in Event Hubs hochgeladenen Protokollen aufzufüllen.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> Die private Konfiguration der Azure Monitor-Senkendefinition verfügt über zwei Eigenschaften: *PrincipalId* und *Secret*. Diese sind nur für klassische virtuelle Computer und klassische Clouddienste erforderlich. Diese Eigenschaften sollten für andere Ressourcen nicht verwendet werden.


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
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
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
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

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> Die öffentliche Konfiguration der Azure Monitor-Senkendefinition verfügt über zwei Eigenschaften, „resourceId“ und „region“. Diese sind nur für klassische virtuelle Computer und klassische Clouddienste erforderlich. Diese Eigenschaften sollten nicht für Resource Manager Virtual Machines oder VM-Skalierungsgruppen verwendet werden.
> Es gibt auch ein zusätzliches Element der privaten Konfiguration für die Azure Monitor-Senke, das eine Prinzipal-ID und ein Geheimnis übergibt. Dies ist nur für klassische virtuelle Computer und klassische Clouddienste erforderlich. Für Resource Manager Virtual Machines und VMSS kann die Azure Monitor-Definition im privaten Konfigurationselement ausgeschlossen werden.
>
