---
title: Verwenden der Azure-Diagnose (.NET) mit Cloud Services | Microsoft Docs
description: Verwenden von Azure-Diagnose zum Sammeln von Daten von Azure Cloud Services für Fehlerbehebung, Leistungsmessung, Überwachung, Datenverkehrsanalysen und mehr.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 89623a0e-4e78-4b67-a446-7d19a35a44be
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: jeconnoc
ms.openlocfilehash: ba69a5aaffb39c26731ffd209587a8c8223b032a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337370"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Aktivieren der Azure-Diagnose in Azure Cloud Services
Hintergrundinformationen über Azure-Diagnose finden Sie unter [Übersicht über Azure-Diagnose](../azure-diagnostics.md) .

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Aktivieren der Diagnose in einer Workerrolle
In dieser Anleitung wird beschrieben, wie Sie eine Azure-Workerrolle implementieren, die mithilfe der .NET-EventSource-Klasse Telemetriedaten ausgibt. Die Telemetriedaten werden mit der Azure-Diagnose erfasst und in einem Azure-Speicherkonto gespeichert. Wenn Sie eine Workerrolle erstellen, aktiviert Visual Studio automatisch Diagnose 1.0 als Teil der Projektmappe in Azure SDKs für .NET 2.4 und früher. In den folgenden Anweisungen wird beschrieben, wie Sie die Workerrolle erstellen, die Diagnose 1.0 in der Projektmappe deaktivieren und die Diagnose 1.2 oder 1.3 in Ihrer Workerrolle bereitstellen.

### <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie über ein Azure-Abonnement verfügen und Visual Studio mit dem Azure-SDK verwenden. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion][Free Trial] registrieren. [Installieren und konfigurieren Sie Azure PowerShell Version 0.8.7 oder höher][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Schritt 1: Erstellen einer Workerrolle
1. Starten Sie **Visual Studio**.
2. Erstellen Sie ein **Azure Cloud Service**-Projekt mithilfe der **Cloudvorlage** für .NET Framework 4.5.  Geben Sie dem Projekt den Namen "WadExample", und klicken Sie auf "OK".
3. Wählen Sie **Workerrolle** aus, und klicken Sie auf "OK". Das Projekt wird erstellt.
4. Doppelklicken Sie im **Projektmappen-Explorer** auf die Eigenschaftendatei **WorkerRole1**.
5. Deaktivieren Sie auf der Registerkarte **Konfiguration** die Option **Diagnose aktivieren**, um Diagnose 1.0 (Azure SDK 2.4 und früher) zu deaktivieren.
6. Erstellen Sie die Projektmappe, um zu überprüfen, ob Fehler vorliegen.

### <a name="step-2-instrument-your-code"></a>Schritt 2: Instrumentieren des Codes
Ersetzen Sie den Inhalt von "WorkerRole.cs" durch den folgenden Code. Die von der [EventSource-Klasse][EventSource Class] geerbte SampleEventSourceWriter-Klasse implementiert vier Protokollierungsmethoden: **SendEnums**, **MessageMethod**, **SetOther** und **HighFreq**. Der erste Parameter für die **WriteEvent** -Methode definiert die ID für das betreffende Ereignis. Die Run-Methode implementiert eine Endlosschleife, die jede der in der **SampleEventSourceWriter** -Klasse implementierten Protokollierungsmethoden alle 10 Sekunden aufruft.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Schritt 3: Bereitstellen der Workerrolle

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Stellen Sie Ihre Workerrolle über Visual Studio in Azure bereit. Wählen Sie dazu das Projekt **WadExample** aus, und klicken Sie anschließend im Menü **Build** auf **Veröffentlichen**.
2. Wählen Sie Ihr Abonnement aus.
3. Wählen Sie im Dialogfeld **Microsoft Azure-Veröffentlichungseinstellungen** die Option **Neu erstellen** aus.
4. Geben Sie im Dialogfeld **Clouddienst und Speicherkonto erstellen** im Feld **Name** einen Namen ein (z.B. „WadExample“), und wählen Sie eine Region oder Affinitätsgruppe aus.
5. Legen Sie **Umgebung** auf **Staging** fest.
6. Ändern Sie unter **Einstellungen** weitere Einstellungen nach Bedarf, und klicken Sie dann auf **Veröffentlichen**.
7. Überprüfen Sie nach Abschluss der Bereitstellung im Azure-Portal, ob Ihr Clouddienst den Status **Wird ausgeführt** aufweist.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Schritt 4: Erstellen der Diagnosekonfigurationsdatei und Installieren der Erweiterung
1. Laden Sie die Schemadefinition für die öffentliche Konfigurationsdatei mit dem folgenden PowerShell-Befehl herunter:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Fügen Sie Ihrem Projekt **WorkerRole1** eine XML-Datei hinzu. Klicken Sie dazu mit der rechten Maustaste auf das Projekt **WorkerRole1**, und wählen Sie **Hinzufügen** -> **Neues Element…** -> **Visual C# Elemente**  -> **Daten**  -> **XML Datei** aus. Nennen Sie die Datei "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Ordnen Sie die Datei "WadConfig.xsd" der Konfigurationsdatei zu. Stellen Sie sicher, dass das Editorfenster mit "WadExample.xml" das aktive Fenster ist. Drücken Sie die Taste **F4**, um das Fenster **Eigenschaften** zu öffnen. Klicken Sie im Fenster **Eigenschaften** auf die Eigenschaft **Schemas**. Klicken Sie auf **...** . in the **…** . Klicken Sie in der Eigenschaft **Hinzufügen…** , navigieren Sie zum Speicherort der XSD-Datei, und wählen Sie die Datei "WadConfig.xsd" aus. Klicken Sie auf **OK**.

4. Ersetzen Sie den Inhalt der Konfigurationsdatei "WadExample.xml" durch den folgenden XML-Code, und speichern Sie die Datei. Diese Konfigurationsdatei definiert ein Paar zu erfassender Leistungsindikatoren: einen für die CPU-Auslastung und einen für die Speicherauslastung. Außerdem werden in der Konfiguration vier Ereignisse definiert, die den Methoden in der SampleEventSourceWriter-Klasse entsprechen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Schritt 5: Installieren von Diagnose für die Workerrolle
Die folgenden PowerShell-Cmdlets stehen zum Verwalten der Diagnose für eine Web- oder Workerrolle zur Verfügung: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension und Remove-AzureServiceDiagnosticsExtension.

1. Öffnen Sie Azure PowerShell.
2. Führen Sie das Skript zum Installieren der Diagnose in Ihrer Workerrolle aus. Ersetzen Sie dabei *StorageAccountKey* durch den Speicherkontoschlüssel für Ihr wadexample-Speicherkonto und *config_path* durch den Pfad zur Datei *WadExample.xml*:

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Schritt 6: Anzeigen der Telemetriedaten
Navigieren Sie in Visual Studio im **Server-Explorer** zum Speicherkonto „wadexample“. Wenn der Clouddienst etwa fünf (5) Minuten lang ausgeführt wurde, sollten die Tabellen **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** und **WADSetOtherTable** angezeigt werden. Doppelklicken Sie auf eine der Tabellen, um die erfassten Telemetriedaten anzuzeigen.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schema der Konfigurationsdatei
Die Diagnosekonfigurationsdatei definiert Werte, die verwendet werden, um beim Start des Diagnoseagenten die Diagnosekonfigurationseinstellungen zu initialisieren. Gültige Werte und Beispiele finden Sie unter [Azure-Diagnose-Konfigurationsschemas](/azure/azure-monitor/platform/diagnostics-extension-schema) .

## <a name="troubleshooting"></a>Problembehandlung
Unter [Problembehandlung bei Azure-Diagnose](../azure-diagnostics-troubleshooting.md) finden Sie Informationen zur Behebung von häufig auftretenden Problemen.

## <a name="next-steps"></a>Nächste Schritte
[In der Liste der Artikel zur Azure-Diagnose im Zusammenhang mit virtuellen Computern](../azure-monitor/platform/diagnostics-extension-overview.md#cloud-services-using-azure-diagnostics) finden Sie eine Anleitung zum Ändern erfasster Daten und zur Fehlerbehebung und erfahren mehr über Diagnose im Allgemeinen.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/
