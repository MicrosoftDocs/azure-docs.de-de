---
title: Profilerstellung für Web-Apps in einer Azure-VM mit Application Insights Profiler | Microsoft-Dokumentation
description: Profilerstellung für Web-Apps in einer Azure-VM mit Application Insights Profiler
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: ab30351bfff9c5bbf070a1e8a54a4919e4d2231a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226263"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profilerstellung von Web-Apps, die auf einem virtuellen Azure-Computer oder in einer VM-Skalierungsgruppe mit Application Insights Profiler ausgeführt werden

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Application Insights Profiler kann auch für diese Dienste bereitgestellt werden:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Bereitstellen von Profiler auf einem virtuellen Computer oder in einer VM-Skalierungsgruppe
Dieser Artikel zeigt, wie Sie Application Insights Profiler auf einem virtuellen Azure-Computer oder in einer Azure-VM-Skalierungsgruppe ausführen. Profiler wird mit der Azure-Diagnose-Erweiterung für VMs installiert. Konfigurieren Sie die Erweiterung für die Ausführung von Profiler, und integrieren Sie das Application Insights SDK in Ihre Anwendung.

1. Fügen Sie das Application Insights SDK Ihrer [ASP.NET-Anwendung](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) hinzu.

   Sie müssen Anforderungstelemetriedaten an Application Insights senden, um Profile für Ihre Anforderungen anzuzeigen.

1. Installieren Sie die Erweiterung „Azure-Diagnose“ auf Ihrem virtuellen Computer. Vollständige Beispiele für Resource Manager-Vorlagen finden Sie hier:  
   * [Virtueller Computer](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [VM-Skalierungsgruppe](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     Der Schlüsselteil ist die Senke des Application Insights-Profilers (ApplicationInsightsProfilerSink) in der WAD-Konfiguration (WadCfg). Damit die Azure-Diagnose dem Profiler das Senden von Daten an Ihre iKey-Instanz ermöglicht, fügen Sie diesem Abschnitt eine weitere Senke hinzu.
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. Stellen Sie die geänderte Definition für die Umgebungsbereitstellung bereit.  

   In der Regel wird eine vollständige Vorlagenbereitstellung oder eine Cloud Services-basierte Veröffentlichung über PowerShell-Cmdlets oder Visual Studio verwendet, um die Änderungen anzuwenden.  

   Die folgenden PowerShell-Befehle sind eine Alternative für vorhandene virtuelle Computer und betreffen nur die Azure-Diagnose-Erweiterung. Fügen Sie die zuvor erwähnte Senke „ProfilerSink“ in der Konfiguration hinzu, die mit dem Befehl „Get-AzVMDiagnosticsExtension“ zurückgegeben wird. Übergeben Sie anschließend die aktualisierte Konfiguration an den Befehl „Set-AzVMDiagnosticsExtension“.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Wenn die betroffene Anwendung über [IIS](https://www.microsoft.com/web/downloads/platform.aspx) ausgeführt wird, aktivieren Sie das Windows-Feature `IIS Http Tracing`.

   a. Richten Sie den Remotezugriff auf die Umgebung ein, und verwenden Sie dann das Fenster [Hinzufügen von Windows-Features]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/), oder führen Sie den folgenden Befehl in PowerShell (als Administrator) aus:  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Wenn das Einrichten des Remotezugriffs ein Problem darstellt, können Sie folgenden Befehl über die [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ausführen:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Stellen Sie Ihre Anwendung bereit.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Festlegen der Profiler-Senke mit dem Azure-Ressourcen-Explorer
Derzeit besteht noch keine Möglichkeit, die Application Insights Profiler-Senke über das Portal festzulegen. Anstatt über PowerShell (entsprechend der Beschreibung weiter oben) können Sie die Senke auch mit dem Azure-Ressourcen-Explorer festlegen. Dabei ist jedoch zu beachten, dass die Senke verloren geht, wenn Sie den virtuellen Computer erneut bereitstellen. Sie müssen die verwendete Konfiguration bei der Bereitstellung des virtuellen Computers aktualisieren, damit diese Einstellung beibehalten wird.

1. Überprüfen Sie, ob die Microsoft Azure-Diagnoseerweiterung installiert ist, indem Sie die für den virtuellen Computer installierten Erweiterungen anzeigen.  

    ![Überprüfen, ob WAD-Erweiterung installiert ist][wadextension]

1. Suchen Sie die VM-Diagnoseerweiterung für Ihren virtuellen Computer. Erweitern Sie Ihre Ressourcengruppe, „Microsoft.Compute“, „virtualMachines“, den Namen des virtuellen Computers und „Erweiterungen“.  

    ![Navigieren zur WAD-Konfiguration im Azure-Ressourcen-Explorer][azureresourceexplorer]

1. Fügen Sie dem Knoten „SinksConfig“ unter „WadCfg“ die Application Insights Profiler-Senke hinzu. Wenn der Abschnitt „SinksConfig“ noch nicht vorhanden ist, müssen Sie ihn hinzufügen. Achten Sie darauf, in den Einstellungen den richtigen Application Insights-iKey anzugeben. Sie müssen in der oberen rechten Ecke in den Explorer-Modus zum Lesen und Schreiben wechseln, klicken Sie dann auf die blaue Schaltfläche „Bearbeiten“.

    ![Hinzufügen der Application Insights Profiler-Senke][resourceexplorersinksconfig]

1. Klicken Sie auf „Put“, nachdem Sie die Bearbeitung der Konfiguration abgeschlossen haben. Wenn der Put-Vorgang erfolgreich durchgeführt wurde, wird in der Mitte des Bildschirms ein grünes Häkchen angezeigt.

    ![Senden einer Put-Anforderung zum Übernehmen von Änderungen][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Kann Profiler auf lokalen Servern ausgeführt werden?
Die Unterstützung von Application Insights Profiler auf lokalen Servern ist nicht geplant.

## <a name="next-steps"></a>Nächste Schritte

- Generieren Sie Datenverkehr zu Ihrer Anwendung (starten Sie z.B. einen [Verfügbarkeitstest](monitor-web-app-availability.md)). Warten Sie dann 10 bis 15 Minuten, bis das Senden der Ablaufverfolgungen an die Application Insights-Instanz beginnt.
- Weitere Informationen finden Sie unter [Profiler-Ablaufverfolgungen](profiler-overview.md?toc=/azure/azure-monitor/toc.json) im Azure-Portal.
- Hilfe bei der Behandlung von Profiler-Problemen finden Sie unter [Problembehandlung für den Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
