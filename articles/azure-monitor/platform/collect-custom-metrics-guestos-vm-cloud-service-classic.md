---
title: Senden von Metriken des Gastbetriebssystems an den Azure Monitor-Metrikspeicher – Cloud Services (klassisch)
description: Senden von Metriken des Gastbetriebssystems an den Azure Monitor-Metrikspeicher (Cloud Services)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 90e841628d989a16f504d2efd7a2c7b18335ff48
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129510"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Senden von Metriken des Gastbetriebssystems an den Azure Monitor-Metrikspeicher – Cloud Services (klassisch) 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Die [Diagnoseerweiterung](diagnostics-extension-overview.md) von Azure Monitor ermöglicht es Ihnen, Metriken und Protokolle von einem Gastbetriebssystem zu erfassen, das als Teil eines virtuellen Computers, eines Clouddiensts oder eines Service Fabric-Clusters ausgeführt wird. Die Erweiterung kann Telemetriedaten an [viele verschiedene Orte](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json) senden.

In diesem Artikel erfahren Sie, wie Sie Leistungsmetriken des Gastbetriebssystems für klassische Azure-Clouddienste an den Azure Monitor-Metrikspeicher senden. Ab Version 1.11 der Diagnoseerweiterung können Sie Metriken direkt in den Azure Monitor-Metrikspeicher schreiben, in dem bereits Metriken der Standardplattformen gesammelt werden. 

Durch die Speicherung an diesem Ort stehen Ihnen die gleichen Aktionen zur Verfügung, die auch für Plattformmetriken verfügbar sind. Dazu zählen unter anderem zeitnahe Benachrichtigungen, Diagrammerstellung, Routing und Zugriff über eine REST-API.  In der Vergangenheit hat die Diagnoseerweiterung zwar in Azure Storage geschrieben, aber nicht in den Azure Monitor-Datenspeicher.  

Der in diesem Artikel beschriebene Prozess funktioniert nur mit Leistungsindikatoren in Azure Cloud Services. Er kann nicht für andere benutzerdefinierte Metriken verwendet werden. 

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen [Dienstadministrator oder Co-Administrator](~/articles/billing/billing-add-change-azure-subscription-administrator.md) für Ihr Azure-Abonnement sein. 

- Ihr Abonnement muss bei [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) registriert sein. 

- Bei Ihnen muss entweder [Azure PowerShell](/powershell/azure) oder [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) installiert sein.

## <a name="provision-a-cloud-service-and-storage-account"></a>Bereitstellen eines Clouddiensts und eines Speicherkontos 

1. Erstellen Sie einen klassischen Clouddienst, und stellen Sie ihn bereit. Eine exemplarische klassische Clouddienstanwendung und -bereitstellung finden Sie unter [Erste Schritte mit Azure-Clouddiensten und ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Sie können ein vorhandenes Speicherkonto verwenden oder ein neues Speicherkonto bereitstellen. Das Speicherkonto sollte sich idealerweise in der gleichen Region befinden wie der klassische Clouddienst, den Sie erstellt haben. Navigieren Sie im Azure-Portal zum Ressourcenblatt **Speicherkonten**, und wählen Sie **Schlüssel** aus. Notieren Sie sich den Speicherkontonamen und den Speicherkontoschlüssel. Sie benötigen diese Informationen in den späteren Schritten.

   ![Speicherkontoschlüssel](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals 

Erstellen Sie anhand der Anleitung unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) einen Dienstprinzipal in Ihrem Azure Active Directory-Mandanten. Beachten Sie dabei Folgendes: 

- Sie können für die Anmelde-URL eine beliebige URL eingeben.  
- Erstellen Sie einen neuen geheimen Clientschlüssel für diese App.  
- Speichern Sie den Schlüssel und die Client-ID für die Verwendung in späteren Schritten.  

Erteilen Sie der im vorherigen Schritt erstellten App Berechtigungen vom Typ *Überwachungsmetriken veröffentlichen* für die Ressource, für die Sie Metriken ausgeben möchten. Wenn Sie planen, die App zu verwenden, um benutzerdefinierte Metriken für viele Ressourcen auszugeben, können Sie diese Berechtigungen auf der Ebene der Ressourcengruppe oder des Abonnements vergeben.  

> [!NOTE]
> Die Diagnoseerweiterung verwendet den Dienstprinzipal, um sich gegenüber Azure Monitor zu authentifizieren und Metriken für Ihren Clouddienst auszugeben.

## <a name="author-diagnostics-extension-configuration"></a>Erstellen der Konfiguration der Diagnoseerweiterung 

Bereiten Sie Ihre Konfigurationsdatei für die Diagnoseerweiterung vor. Diese Datei bestimmt, welche Protokolle und Leistungsindikatoren die Diagnoseerweiterung für Ihren Clouddienst erfassen soll. Nachfolgend finden Sie eine Beispieldatei für die Diagnosekonfiguration:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

Definieren Sie im Abschnitt „SinksConfig“ Ihrer Diagnosedatei eine neue Azure Monitor-Senke: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

Fügen Sie die Azure Monitor-Senke dem Abschnitt Ihrer Konfigurationsdatei hinzu, in dem die zu erfassenden Leistungsindikatoren aufgelistet werden. Dieser Eintrag stellt sicher, dass alle angegebenen Leistungsindikatoren als Metriken an Azure Monitor weitergeleitet werden. Sie können nach Bedarf Leistungsindikatoren hinzufügen oder entfernen. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Fügen Sie schließlich in der privaten Konfiguration den Abschnitt *Azure Monitor-Konto* hinzu. Geben Sie die Client-ID des Dienstprinzipals und das Geheimnis ein, die Sie in einem früheren Schritt erstellt haben. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Speichern Sie diese Diagnosedatei lokal.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Bereitstellen der Diagnoseerweiterung für Ihren Clouddienst 

Starten Sie PowerShell, und melden Sie sich bei Azure an. 

```powershell
Login-AzAccount 
```

Verwenden Sie die folgenden Befehle, um die Details des zuvor erstellten Speicherkontos zu speichern: 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Legen Sie mit dem folgenden Befehl den Diagnosedateipfad auf eine Variable fest:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Stellen Sie die Diagnoseerweiterung mithilfe des folgenden Befehls unter Verwendung der Diagnosedatei mit der konfigurierten Azure Monitor-Senke für Ihren Clouddienst bereit:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Im Rahmen der Installation der Diagnoseerweiterung muss weiterhin ein Speicherkonto angegeben werden. Alle Protokolle und/oder Leistungsindikatoren, die in der Diagnosekonfigurationsdatei angegeben sind, werden in das angegebene Speicherkonto geschrieben.  

## <a name="plot-metrics-in-the-azure-portal"></a>Darstellen von Metriken im Azure-Portal 

1. Öffnen Sie das Azure-Portal. 

   ![Metriken – Azure-Portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. Wählen Sie im Menü auf der linken Seite **Monitor** aus.

3. Wählen Sie auf dem Blatt **Monitor** die Registerkarte **Metriken (Vorschau)** aus.

4. Wählen Sie im Dropdownmenü mit den Ressourcen Ihren klassischen Clouddienst aus.

5. Wählen Sie im Dropdownmenü mit den Namespaces **azure.vm.windows.guest** aus. 

6. Wählen Sie im Dropdownmenü mit den Metriken **Arbeitsspeicher\Verwendete zugesicherte Bytes** aus. 

Mithilfe der Funktionen zum Filtern und Aufteilen von Dimensionen können Sie den gesamten Arbeitsspeicher anzeigen, der von einer bestimmten Rolle oder Rolleninstanz verwendet wird. 

 ![Metriken – Azure-Portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [benutzerdefinierte Metriken](metrics-custom-overview.md).

