---
title: Aktivieren der Diagnose auf einem virtuellen Windows-Computer mithilfe von Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
description: Erfahren Sie, wie Sie die Azure-Diagnose auf einer virtuellen Azure-Maschine unter Windows mithilfe von PowerShell aktivieren
author: mimckitt
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2015
ms.author: mimckitt
ms.openlocfilehash: 9561f96dafd936244831cff2ed05cda4d915d27d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955972"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Aktivieren der Azure-Diagnose auf einer virtuellen Azure-Maschine unter Windows mithilfe von PowerShell

Die Azure-Diagnose ist eine Funktion in Azure, mit der Diagnosedaten für eine bereitgestellte Anwendung erfasst werden können. Mithilfe der Diagnoseerweiterung können Sie Diagnosedaten wie Anwendungsprotokolle oder Leistungsindikatoren von einem virtuellen Azure-Computer unter Windows sammeln. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Aktivieren der Diagnoseerweiterung bei Verwendung des Ressourcen-Manager-Bereitstellungsmodells
Die Diagnoseerweiterung kann im Zuge der Erstellung einer Windows-VM mit dem Azure-Ressourcen-Manager-Bereitstellungsmodell durch Hinzufügen der Erweiterungskonfiguration zur Ressourcen-Manager-Vorlage aktiviert werden. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Windows-Computers mit Überwachung und Diagnose mithilfe von Azure Resource Manager-Vorlagen](diagnostics-template.md).

Wenn Sie die Diagnoseerweiterung auf einem bereits vorhandenen virtuellen Computer aktivieren möchten, der mithilfe des Resource Manager-Bereitstellungsmodells erstellt wurde, können Sie wie weiter unten gezeigt das PowerShell-Cmdlet [Set-AzVMDiagnosticsExtension](/powershell/module/az.compute/set-azvmdiagnosticsextension) verwenden.

```azurepowershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path
```


*$diagnosticsconfig_path* ist der Pfad zu der Datei mit der XML-Diagnosekonfiguration, wie im [Beispiel](#sample-diagnostics-configuration) weiter unten beschrieben.  

Wenn in der Diagnosekonfigurationsdatei ein Element vom Typ **StorageAccount** mit einem Speicherkontennamen angegeben ist, konfiguriert das Skript *Set-AzVMDiagnosticsExtension* die Diagnoseerweiterung automatisch so, dass die Diagnosedaten an dieses Speicherkonto gesendet werden. Dazu muss das Speicherkonto allerdings dem gleichen Abonnement angehören wie die VM.

Wenn in der Diagnosekonfiguration kein **StorageAccount** angegeben wurde, müssen Sie den *StorageAccountName* -Parameter an das Cmdlet übergeben. Bei Angabe des Parameters *StorageAccountName* verwendet das Cmdlet immer das im Parameter angegebene Speicherkonto und nicht das in der Diagnosekonfigurationsdatei angegebene.

Wenn das Diagnosespeicherkonto einem anderen Abonnement angehört als die VM, müssen Sie die Parameter *StorageAccountName* und *StorageAccountKey* explizit an das Cmdlet übergeben. Der Parameter *StorageAccountKey* ist nicht erforderlich, wenn das Diagnosespeicherkonto dem gleichen Abonnement angehört, da das Cmdlet beim Aktivieren der Diagnoseerweiterung den Schlüsselwert automatisch abfragen und festlegen kann. Gehört das Diagnosespeicherkonto dagegen einem anderen Abonnement an, kann das Cmdlet den Schlüssel unter Umständen nicht automatisch abrufen, weshalb er in diesem Fall explizit über den Parameter *StorageAccountKey* angegeben werden muss.  

```azurepowershell
Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

Nachdem die Diagnoseerweiterung auf einem virtuellen Computer aktiviert wurde, können Sie die aktuellen Einstellungen mithilfe des Cmdlets [Get-AzVmDiagnosticsExtension](/powershell/module/az.compute/get-azvmdiagnosticsextension) abrufen.

```azurepowershell
Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name
```

Das Cmdlet gibt *PublicSettings* zurück, das die Diagnosekonfiguration enthält. Es werden zwei Arten der Konfiguration unterstützt, WadCfg und xmlCfg. WadCfg ist eine JSON-Konfiguration und xmlCfg eine XML-Konfiguration in einem Base64-codierten Format. Zum Lesen des XML-Codes muss dieser zunächst decodiert werden.

```azurepowershell
$publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
Write-Host $xmlconfig
```

Mit dem Cmdlet [Remove-AzVmDiagnosticsExtension](/powershell/module/az.compute/remove-azvmdiagnosticsextension) können Sie die Diagnoseerweiterung von einer VM entfernen.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Aktivieren der Diagnoseerweiterung bei Verwendung des klassischen Bereitstellungsmodells

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Mit dem [Set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) -Cmdlet können Sie eine Diagnoseerweiterung auf einem virtuellen Computer aktivieren, den Sie mit dem klassischen Bereitstellungsmodell erstellt haben. Das folgende Beispiel zeigt, wie Sie mit dem klassischen Bereitstellungsmodell eine neue VM mit aktivierter Diagnoseerweiterung erstellen.

```azurepowershell
$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM
```

Wenn Sie die Diagnoseerweiterung auf einem bereits vorhandenen virtuellen Computer aktivieren möchten, der mit dem klassischen Bereitstellungsmodell erstellt wurde, rufen Sie zunächst die VM-Konfiguration mit dem [Get-AzureVM](/powershell/module/servicemanagement/azure.service/get-azurevm) -Cmdlet ab. Aktualisieren Sie anschließend mithilfe des [Set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) -Cmdlets die VM-Konfiguration, sodass diese die Diagnoseerweiterung beinhaltet. Wenden Sie abschließend mithilfe von [Update-AzureVM](/powershell/module/servicemanagement/azure.service/update-azurevm)die aktualisierte Konfiguration auf die VM an.

```azurepowershell
$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
$VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM
```

## <a name="sample-diagnostics-configuration"></a>Beispielhafte Diagnosekonfiguration
Der folgende XML-Code kann für die öffentliche Konfiguration der Diagnose mit den obigen Skripts verwendet werden. Diese Beispielkonfiguration überträgt neben verschiedenen Leistungsindikatoren auch Fehler aus dem Anwendungs-, Sicherheits- und Systemkanal der Windows-Ereignisprotokolle sowie sämtliche Fehler aus den Diagnoseinfrastrukturprotokollen an das Diagnosespeicherkonto.

Die Konfiguration muss mit Folgendem aktualisiert werden:

* Das Attribut *resourceID* des Elements **Metrics** muss mit der Ressourcen-ID für die VM aktualisiert werden.
  
  * Die Ressourcen-ID kann nach folgendem Muster erstellt werden: „/subscriptions/{*Abonnement-ID für das Abonnement mit der VM*}/resourceGroups/{*Ressourcengruppenname für die VM*}/providers/Microsoft.Compute/virtualMachines/{*VM-Name*}“.
  * Wenn die Abonnement-ID für das Abonnement mit der VM also beispielsweise **11111111-1111-1111-1111-111111111111**, der Ressourcengruppenname **MyResourceGroup** und der VM-Name **MyWindowsVM** lautet, hat *resourceID* folgenden Wert:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Weitere Informationen zur Generierung von Metriken auf der Grundlage der Konfiguration von Leistungsindikatoren und Metriken finden Sie in der [Metriktabelle für Azure-Diagnose im Speicher](diagnostics-template.md#wadmetrics-tables-in-storage).
* Das Element **StorageAccount** muss mit dem Namen des Diagnosespeicherkontos aktualisiert werden.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Nächste Schritte
* Weitere Anleitungen zur Verwendung von Azure-Diagnosefunktionen und zu anderen Techniken zur Problembehandlung finden Sie unter [Aktivieren der Azure-Diagnose in Azure Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Diagnosekonfigurationsschema](../../azure-monitor/platform/diagnostics-extension-versions.md) werden die verschiedenen Optionen der XML-Konfigurationen für die Diagnoseerweiterung erläutert.