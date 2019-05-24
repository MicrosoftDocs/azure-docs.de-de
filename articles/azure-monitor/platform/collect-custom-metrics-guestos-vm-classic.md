---
title: Senden von Metriken des Gastbetriebssystems an den Azure Monitor-Datenspeicher für einen virtuellen Windows-Computer (klassisch)
description: Senden von Metriken des Gastbetriebssystems an den Azure Monitor-Datenspeicher für einen virtuellen Windows-Computer (klassisch)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 57212da1a8da7ee6c57faf2413b88a413df04817
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129570"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Senden von Metriken des Gastbetriebssystems an den Azure Monitor-Datenspeicher für einen virtuellen Windows-Computer (klassisch)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Die [Diagnoseerweiterung](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) von Azure Monitor (auch „WAD“ oder „Diagnose“ genannt) ermöglicht es Ihnen, Metriken und Protokolle vom Gastbetriebssystem zu erfassen, das als Teil eines virtuellen Computers, eines Clouddiensts oder eines Service Fabric-Clusters ausgeführt wird. Die Erweiterung kann Telemetriedaten an [viele verschiedene Orte](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json) senden.

In diesem Artikel erfahren Sie, wie Sie Leistungsmetriken des Gastbetriebssystems für einen virtuellen Windows-Computer (klassisch) an den Azure Monitor-Metrikspeicher senden. Ab Version 1.11 der Diagnoseerweiterung können Sie Metriken direkt in den Azure Monitor-Metrikspeicher schreiben, in dem bereits Metriken der Standardplattformen gesammelt werden. 

Durch die Speicherung an diesem Ort stehen Ihnen die gleichen Aktionen zur Verfügung wie für Plattformmetriken. Dazu zählen unter anderem zeitnahe Benachrichtigungen, Diagrammerstellung, Routing und Zugriff über eine REST-API. In der Vergangenheit hat die Diagnoseerweiterung zwar in Azure Storage geschrieben, aber nicht in den Azure Monitor-Datenspeicher. 

Der in diesem Artikel beschriebene Prozess funktioniert nur mit klassischen virtuellen Computern unter dem Windows-Betriebssystem.

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen [Dienstadministrator oder Co-Administrator](../../billing/billing-add-change-azure-subscription-administrator.md) für Ihr Azure-Abonnement sein. 

- Ihr Abonnement muss bei [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) registriert sein. 

- [Azure PowerShell](/powershell/azure) oder [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) muss installiert sein.

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Erstellen eines klassischen virtuellen Computers und eines Speicherkontos

1. Erstellen Sie einen klassischen virtuellen Computer über das Azure-Portal.
   ![Erstellen eines klassischen virtuellen Computers](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Wählen Sie beim Erstellen dieses virtuellen Computers aus, dass Sie ein neues klassisches Speicherkonto erstellen möchten. Wir verwenden dieses Speicherkonto in späteren Schritten.

1. Navigieren Sie im Azure-Portal zum Ressourcenblatt **Speicherkonten**. Wählen Sie **Schlüssel** aus, und notieren Sie sich den Speicherkontonamen und den Speicherkontoschlüssel. Sie benötigen diese Informationen in den späteren Schritten.
   ![Speicherzugriffsschlüssel](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Erstellen Sie anhand der Anleitung unter [Erstellen eines Dienstprinzipals](../../active-directory/develop/howto-create-service-principal-portal.md) einen Dienstprinzipal in Ihrem Azure Active Directory-Mandanten. Beachten Sie Folgendes, während Sie diesen Prozess durchlaufen: 
- Erstellen Sie einen neuen geheimen Clientschlüssel für diese App.
- Speichern Sie den Schlüssel und die Client-ID für die Verwendung in späteren Schritten.

Erteilen Sie dieser App für die Ressource, für die Sie Metriken ausgeben möchten, Berechtigungen vom Typ „Überwachungsmetriken veröffentlichen“. Sie können eine Ressourcengruppe oder ein vollständiges Abonnement verwenden.  

> [!NOTE]
> Die Diagnoseerweiterung verwendet den Dienstprinzipal, um sich gegenüber Azure Monitor zu authentifizieren und Metriken für Ihren klassischen virtuellen Computer auszugeben.

## <a name="author-diagnostics-extension-configuration"></a>Erstellen der Konfiguration der Diagnoseerweiterung

1. Bereiten Sie Ihre Konfigurationsdatei für die Diagnoseerweiterung vor. Diese Datei bestimmt, welche Protokolle und Leistungsindikatoren die Diagnoseerweiterung für Ihren klassischen virtuellen Computer erfassen soll. Dies ist ein Beispiel:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
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
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
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
1. Definieren Sie im Abschnitt „SinksConfig“ Ihrer Diagnosedatei wie folgt eine neue Azure Monitor-Senke:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. Leiten Sie in dem Abschnitt Ihrer Konfigurationsdatei, in dem Sie die zu erfassenden Leistungsindikatoren auflisten, die Leistungsindikatoren an die Azure Monitor-Senke „AzMonSink“ weiter.

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Definieren Sie in der privaten Konfiguration das Azure Monitor-Konto. Fügen Sie anschließend die Dienstprinzipalinformationen hinzu, die für die Metrikausgabe verwendet werden sollen.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Speichern Sie diese Datei lokal.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Bereitstellen der Diagnoseerweiterung für Ihren Clouddienst

1. Starten Sie PowerShell, und melden Sie sich an.

    ```powershell
    Login-AzAccount
    ```

1. Legen Sie zunächst den Kontext für Ihren klassischen virtuellen Computer fest.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Legen Sie den Kontext des klassischen Speicherkontos fest, das mit dem virtuellen Computer erstellt wurde.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Legen Sie mit dem folgenden Befehl den Diagnosedateipfad auf eine Variable fest:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Bereiten Sie das Update für Ihren klassischen virtuellen Computer mit der Diagnosedatei vor, die die konfigurierte Azure Monitor-Senke enthält.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Stellen Sie das Update mithilfe des folgenden Befehls auf Ihrem virtuellen Computer bereit:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Im Rahmen der Installation der Diagnoseerweiterung muss weiterhin ein Speicherkonto angegeben werden. Alle Protokolle und/oder Leistungsindikatoren, die in der Diagnosekonfigurationsdatei angegeben sind, werden in das angegebene Speicherkonto geschrieben.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Darstellen der Metriken im Azure-Portal

1.  Öffnen Sie das Azure-Portal. 

1.  Wählen Sie im Menü auf der linken Seite **Monitor** aus.

1.  Wählen Sie auf dem Blatt **Monitor** die Option **Metriken** aus.

    ![Navigieren zu „Metriken“](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. Wählen Sie im Dropdownmenü mit den Ressourcen Ihren klassischen virtuellen Computer aus.

1. Wählen Sie im Dropdownmenü mit den Namespaces **azure.vm.windows.guest** aus.

1. Wählen Sie im Dropdownmenü mit den Metriken **Arbeitsspeicher\Verwendete zugesicherte Bytes** aus.
   ![Darstellen von Metriken](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [benutzerdefinierte Metriken](metrics-custom-overview.md).

