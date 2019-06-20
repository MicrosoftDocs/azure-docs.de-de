---
title: Azure-Diagnoseerweiterung – Versionsverlauf des Konfigurationsschemas
description: Relevant für die Erfassung von Leistungsindikatoren in Azure Virtual Machines, VM Scale Sets, Service Fabric und Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 1230a9bcea01ef394a6299c50b8d5537850cfee5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60527312"
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Azure-Diagnoseerweiterung – Versionen und Verlauf des Konfigurationsschemas
Diese Seite enthält einen Index für die Schemaversionen der Azure-Diagnoseerweiterung, die zusammen mit dem Microsoft Azure SDK bereitgestellt werden.  

> [!NOTE]
> Die Azure-Diagnoseerweiterung ist die Komponente, die zum Sammeln von Leistungsindikatoren und anderen Statistiken aus diesen Produkten verwendet wird:
> - Azure Virtual Machines
> - Virtual Machine Scale Sets
> - Service Fabric
> - Cloud Services
> - Netzwerksicherheitsgruppen
>
> Diese Seite ist nur relevant, wenn Sie einen dieser Dienste verwenden.

Die Azure-Diagnoseerweiterung wird zusammen mit anderen Microsoft-Diagnoseprodukten wie Azure Monitor verwendet, in dem Application Insights und Log Analytics enthalten sind. Weitere Informationen finden Sie unter [Überwachen von Azure-Anwendungen und -Ressourcen](../../azure-monitor/overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK- und -Diagnoseversionen – Bereitstellungsdiagramm  

|Azure SDK-Version | Version der Diagnoseerweiterung | Modell|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |Plug-In|  
|2.0–2.4         |1.0                            |Plug-In|  
|2.5               |1.2                            |Erweiterung|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2,9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Version 1.0 der Azure-Diagnose wurde zunächst über ein Plug-In-Modell bereitgestellt. Bei der Installation des Azure SDKs haben Sie also auch die Version der Azure-Diagnose erhalten.  

 Ab SDK 2.5 (Diagnose-Version 1.2) wurde für die Azure-Diagnose ein Erweiterungsmodell verwendet. Die Tools für die Nutzung neuer Features standen zwar erst in neueren Azure SDKs zur Verfügung, trotzdem erhielt Dienst, der Azure-Diagnose verwendete, die neueste vertriebene Version direkt von Azure. Beispielsweise laden alle Benutzer, die noch mit SDK 2.5 arbeiten, die neueste in der letzten Tabelle angegebene Version, unabhängig davon, ob sie die neueren Funktionen verwenden.  

## <a name="schemas-index"></a>Schemaindex  
Verschiedene Versionen der Azure-Diagnose verwenden unterschiedliche Konfigurationsschemas.

[Konfigurationsschema für Diagnoseversion 1.0](diagnostics-extension-schema-1dot0.md)  

[Konfigurationsschema für Diagnoseversion 1.2](diagnostics-extension-schema-1dot2.md)  

[Konfigurationsschema für Diagnose 1.3 und höher](diagnostics-extension-schema-1dot3.md)  

## <a name="version-history"></a>Versionsverlauf

### <a name="diagnostics-extension-111"></a>Diagnoseerweiterung 1.11
Unterstützung für die Azure Monitor-Senke wurde hinzugefügt. Diese Senke ist nur auf Leistungsindikatoren anwendbar. Sie ermöglicht es, Leistungsindikatoren, die auf Ihrem virtuellen Computer, Ihrer VMSS oder in Ihrem Clouddienst gesammelt wurden, als benutzerdefinierte Metriken an Azure Monitor zu senden. Die Azure Monitor-Senke unterstützt Folgendes:
* Abrufen aller Leistungsindikatoren, die über die [APIs der Azure Monitor-Metriken](https://docs.microsoft.com/rest/api/monitor/metrics/list) an Azure Monitor gesendet wurden.
* Benachrichtigung für alle Leistungsindikatoren, die über die neue [einheitliche Oberfläche für Warnungen](../../azure-monitor/platform/alerts-overview.md) in Azure Monitor an Azure Monitor gesendet werden.
* Behandeln von Platzhalteroperatoren in Leistungsindikatoren als die Dimension „Instanz“ in Ihrer Metrik. Wenn Sie z. B. den Indikator „LogicalDisk(\*)/DiskWrites/Sek.“ erfasst haben, können Sie die Dimension „Instanz“ filtern und aufteilen, um für jeden logischen Datenträger (C:, D: usw.) Schreibvorgänge pro Sekunde darzustellen oder hinsichtlich dieser Vorgänge Warnungen auszugeben.

Definieren von Azure Monitor als neue Senke in Ihrer Konfiguration der Diagnoseerweiterung
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Die Konfiguration der Azure Monitor-Senke für klassische virtuelle Computer und klassische Clouddienste erfordert die Definition weiterer Parameter in der privaten Konfiguration der Diagnoseerweiterung.
>
> Weitere Informationen finden Sie in der Dokumentation zum [detaillierten Diagnoseerweiterungsschema.](diagnostics-extension-schema-1dot3.md)

Als nächstes können Sie Ihre Leistungsindikatoren so konfigurieren, dass sie an die Azure Monitor-Senke weitergeleitet werden.
```json
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
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Diagnoseerweiterung 1.9
Docker-Unterstützung hinzugefügt.


### <a name="diagnostics-extension-181"></a>Diagnoseerweiterung 1.8.1
Sie können in der privaten Konfiguration ein SAS-Token anstelle eines Speicherkontoschlüssels angeben. Wenn ein SAS-Token angegeben wird, wird der Speicherkontoschlüssel ignoriert.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Diagnoseerweiterung 1.8
Speichertyp zu PublicConfig hinzugefügt. StorageType kann *Table*, *Blob* oder *TableAndBlob* sein. *Table* ist die Standardeinstellung.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Diagnoseerweiterung 1.7
Möglichkeit zum Routen an EventHub hinzugefügt.

### <a name="diagnostics-extension-15"></a>Diagnoseerweiterung 1.5
Das Element „sinks“ wurde hinzugefügt, ebenso die Möglichkeit, Diagnosedaten an [Application Insights](../../azure-monitor/app/cloudservices.md) zu senden. Dies vereinfacht die Diagnose von Problemen sowohl in Ihrer gesamten Anwendung als auch auf System- und Infrastrukturebene.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 und Diagnoseerweiterung 1.3
Für Clouddienstprojekte in Visual Studio wurden die folgenden Änderungen vorgenommen. (Diese Änderungen gelten auch für spätere Versionen des Azure SDK.)

* Der lokale Emulator unterstützt jetzt die Diagnose. Diese Änderung bedeutet, dass Sie Diagnosedaten erfassen und sicherstellen können, dass Ihre Anwendung die richtigen Ablaufverfolgungen erstellt, während Sie Ihre Entwicklung und Tests in Visual Studio durchführen. Die Verbindungszeichenfolge `UseDevelopmentStorage=true` aktiviert das Sammeln von Diagnosedaten, während Sie Ihr Clouddienstprojekt in Visual Studio mithilfe des Azure-Speicheremulators ausführen. Alle Diagnosedaten werden im Speicherkonto (Development Storage) gesammelt.
* Die Verbindungszeichenfolge für das Diagnosespeicherkonto (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) wird erneut in der Dienstkonfigurationsdatei (.cscfg) gespeichert. In Azure SDK 2.5 wurde das Diagnosespeicherkonto in der Datei "diagnostics.wadcfgx" angegeben.

Es gibt einige wichtige Unterschiede zwischen der Funktion der Verbindungszeichenfolge in Azure SDK 2.4 und früher und in Azure SDK 2.6 und später.

* Bis Azure SDK 2.4 wurde die Verbindungszeichenfolge vom Diagnose-Plug-In zur Laufzeit verwendet, um Informationen zum Speicherkonto für die Übertragung der Diagnoseprotokolle abzurufen.
* Ab Azure SDK 2.6 wird die Diagnoseverbindungszeichenfolge von Visual Studio während der Veröffentlichung zum Konfigurieren der Diagnoseerweiterung mit den entsprechenden Speicherkontoinformationen verwendet. Mit der Verbindungszeichenfolge können Sie die verschiedenen Speicherkonten für verschiedene Dienstkonfigurationen definieren, die Visual Studio beim Veröffentlichen verwendet. Da das Diagnose-Plug-In (nach Azure SDK 2.5) nicht mehr verfügbar ist, kann jedoch die CSCFG-Datei allein die Diagnoseerweiterung nicht aktivieren. Sie müssen die Erweiterung mithilfe von Tools wie Visual Studio oder PowerShell separat aktivieren.
* Um die Konfiguration der Diagnoseerweiterung mit PowerShell zu vereinfachen, enthält die Paketausgabe von Visual Studio auch die öffentliche Konfigurations-XML für die Diagnoseerweiterung jeder Rolle. Visual Studio verwendet die Diagnoseverbindungszeichenfolge zum Ausfüllen der Speicherkontoinformationen, die in der öffentlichen Konfiguration vorhanden sind. Die öffentlichen Konfigurationsdateien werden im Ordner „Extensions“ erstellt und haben das folgende Namensgebungsmuster: `PaaSDiagnostics.<RoleName>.PubConfig.xml`. Dieses Muster kann von allen PowerShell-basierten Bereitstellungen verwendet werden, um die einzelnen Konfigurationen einer Rolle zuzuordnen.
* Die Verbindungszeichenfolge in der CSCFG-Datei wird auch im Azure-Portal verwendet, um auf die Diagnosedaten zuzugreifen, sodass sie in der Registerkarte **Überwachung** angezeigt werden kann. Die Verbindungszeichenfolge ist erforderlich, um den Dienst so zu konfigurieren, dass ausführliche Überwachungsdaten im Portal angezeigt werden.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrieren von Projekten zu Azure SDK 2.6 und höher
Wenn beim Migrieren von Azure SDK 2.5 zu Azure SDK 2.6 oder höher in der WADCFGX-Datei ein Diagnosespeicherkonto angegeben ist, dann bleibt der Speicherort unverändert. Um die Flexibilität, die sich aus der Verwendung verschiedener Speicherkonten für unterschiedliche Speicherkonfigurationen ergibt, nutzen zu können, müssen Sie Ihrem Projekt die Verbindungszeichenfolge manuell hinzufügen. Wenn Sie ein Projekt aus Azure SDK 2.4 oder früher zu Azure SDK 2.6 migrieren, werden die Diagnoseverbindungszeichenfolgen beibehalten. Beachten Sie aber die Änderungen im Hinblick auf die Handhabung von Verbindungszeichenfolgen in Azure SDK 2.6, wie im vorherigen Abschnitt beschrieben.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Bestimmung des Diagnosespeicherkontos durch Visual Studio
* Wenn eine Diagnoseverbindungszeichenfolge in der CSCFG-Datei angegeben ist, wird es von Visual Studio beim Veröffentlichen und beim Generieren der öffentlichen XML-Konfigurationsdateien beim Verpacken zur Konfiguration der Diagnoseerweiterung verwendet.
* Wenn keine Diagnoseverbindungszeichenfolge in der CSCFG-Datei angegeben ist, verwendet Visual Studio beim Veröffentlichen und beim Generieren der öffentlichen XML-Konfigurationsdateien beim Verpacken wieder das in der WADCFGX-Datei angegebene Speicherkonto zur Konfiguration der Diagnoseerweiterung.
* Die Diagnoseverbindungszeichenfolge in der CSCFG-Datei hat Vorrang vor dem in der WADCFGSX-Datei angegebenen Speicherkonto. Wenn eine Diagnoseverbindungszeichenfolge in der CSCFG-Datei angegeben ist, verwendet Visual Studio diese und ignoriert das Speicherkonto in der WADCFGX-Datei.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Wozu dient das Kontrollkästchen „Verbindungszeichenfolgen für Entwicklungsspeicher…“?
Das Kontrollkästchen **Verbindungszeichenfolgen für Entwicklungsspeicher zur Diagnose und zum Zwischenspeichern beim Veröffentlichen in Microsoft Azure mit Anmeldeinformationen für Microsoft Azure-Speicherkonto aktualisieren** ist eine bequeme Möglichkeit, um alle Verbindungszeichenfolgen für ein Entwicklungsspeicherkonto mit dem Azure-Speicherkonto zu aktualisieren, das während der Veröffentlichung angegeben wird.

Beispiel: Sie aktivieren dieses Kontrollkästchen, und die Diagnoseverbindungszeichenfolge gibt `UseDevelopmentStorage=true`an. Wenn Sie das Projekt in Azure veröffentlichen, aktualisiert Visual Studio automatisch die Diagnoseverbindungszeichenfolge mit dem Speicherkonto, das Sie im Webpublishing-Assistenten angegeben haben. Wenn jedoch ein echtes Speicherkonto als Diagnoseverbindungszeichenfolge angegeben wurde, wird stattdessen dieses Konto verwendet.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Unterschiede zwischen Diagnosefunktionen in Azure SDK 2.4 und früher und Azure SDK 2.5 und später
Wenn Sie für Ihr Projekt ein Upgrade von Azure SDK 2.4 auf Azure SDK 2.5 oder höher durchführen, sollten Sie die folgenden Diagnosefunktionsunterschiede beachten.

* **Konfigurations-APIs sind veraltet** – In Azure SDK 2.4 oder früheren Versionen ist eine programmgesteuerte Konfiguration der Diagnose verfügbar; diese Funktion ist jedoch in Azure SDK 2.5 und höher veraltet. Wenn die Diagnosekonfiguration derzeit im Code definiert ist, müssen Sie diese Einstellungen im Projekt von Grund auf neu konfigurieren, damit die Diagnose weiterhin funktioniert. Die Diagnosekonfigurationsdatei für Azure SDK 2.4 ist "diagnostics.wadcfg"; in Azure SDK 2.5 oder höher hat sie den Namen "diagnostics.wadcfgx".
* **Diagnose für Clouddienstanwendungen kann nur auf Rollenebene, nicht auf der Instanzebene konfiguriert werden.**
* **Jedes Mal, wenn Sie Ihre App bereitstellen, wird die Diagnosekonfiguration aktualisiert** – Dies kann Paritätsprobleme verursachen, wenn Sie die Diagnosekonfiguration im Server-Explorer ändern und anschließend die App erneut bereitstellen.
* **In Azure SDK 2.5 und höher werden Absturzabbilder in der Diagnosekonfigurationsdatei und nicht im Code konfiguriert** – Wenn Sie Absturzabbilder im Code konfiguriert haben, müssen Sie die Konfiguration manuell vom Code in die Konfigurationsdatei übertragen, da die Absturzabbilder während der Migration zu Azure SDK 2.6 nicht übertragen werden.

