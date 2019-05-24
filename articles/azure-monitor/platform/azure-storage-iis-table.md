---
title: Verwenden von Blob Storage für IIS und Table Storage für Ereignisse in Azure Monitor | Microsoft-Dokumentation
description: Azure Monitor kann die Protokolle für Azure-Dienste, die Diagnosedaten in Table Storage schreiben, sowie die IIS-Protokolle, die in Blob Storage geschrieben werden, lesen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: a4e0432260cfb9ee11ed318305fb967d160de835
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129686"
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-azure-monitor"></a>Verwenden von Azure Blob Storage für IIS und Azure Table Storage für Ereignisse mit Azure Monitor

Azure Monitor kann die Protokolle für die folgenden Dienste, die Diagnosedaten in Table Storage schreiben, sowie die IIS-Protokolle, die in Blob Storage geschrieben werden, lesen:

* Service Fabric-Cluster (Vorschau)
* Virtual Machines
* Web-/Workerrollen

Bevor Azure Monitor Daten für diese Ressourcen in einem Log Analytics-Arbeitsbereich sammeln kann, muss Azure-Diagnose aktiviert sein.

Nachdem die Diagnose aktiviert wurde, können Sie das Azure-Portal oder PowerShell verwenden, um den Arbeitsbereich für das Sammeln der Protokolle zu konfigurieren.

Die Azure-Diagnose ist eine Azure-Erweiterung, mit der Sie Diagnosedaten von einer Worker- oder Webrolle oder einem virtuellen Computer erfassen können, die bzw. der in Azure ausgeführt wird. Die Daten werden in einem Azure-Speicherkonto gespeichert und können dann von Azure Monitor gesammelt werden.

Damit Azure Monitor die Protokolle von Azure-Diagnose sammeln kann, müssen diese sich an den folgenden Speicherorten befinden:

| Protokolltyp | Ressourcentyp | Location |
| --- | --- | --- |
| IIS-Protokolle |Virtual Machines <br> Webrollen <br> Workerrollen |wad-iis-logfiles (Blob Storage) |
| syslog |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric – operative Ereignisse |Service Fabric-Knoten |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor-Ereignisse |Service Fabric-Knoten |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service-Ereignisse |Service Fabric-Knoten |WADServiceFabricReliableServiceEventTable |
| Windows-Ereignisprotokolle |Service Fabric-Knoten <br> Virtual Machines <br> Webrollen <br> Workerrollen |WADWindowsEventLogsTable (Table Storage) |
| Windows-ETW-Protokolle |Service Fabric-Knoten <br> Virtual Machines <br> Webrollen <br> Workerrollen |WADETWEventTable (Table Storage) |

> [!NOTE]
> IIS-Protokolle von Azure-Websites werden derzeit nicht unterstützt.
>
>

Bei virtuellen Computern haben Sie die Möglichkeit, den [Log Analytics-Agent](../../azure-monitor/learn/quick-collect-azurevm.md) auf Ihrem virtuellen Computer zu installieren, um weitere Einblicke zu aktivieren. Sie können nicht nur IIS-Protokolle und Ereignisprotokolle analysieren, sondern auch zusätzliche Analysen durchführen, einschließlich der Nachverfolgung von Konfigurationsänderungen, SQL-Bewertung und der Bewertung von Updates.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Aktivieren der Azure-Diagnose auf einem virtuellen Computer zur Erfassung von Ereignis- und IIS-Protokollen

Gehen Sie folgendermaßen vor, um die Azure-Diagnose mithilfe des Microsoft Azure-Portals auf einem virtuellen Computer für die Erfassung von Ereignis- und IIS-Protokollen zu aktivieren.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Aktivieren der Azure-Diagnose auf einem virtuellen Computer mithilfe des Azure-Portals

1. Installieren Sie den VM-Agent, wenn Sie einen virtuellen Computer erstellen. Ist der virtuelle Computer bereits vorhanden, stellen Sie sicher, dass der VM-Agent bereits installiert ist.

   * Navigieren Sie im Azure-Portal zu dem virtuellen Computer, wählen Sie **Optionale Konfiguration** und dann **Diagnose** aus, und legen Sie den **Status** auf **Ein** fest.

     Anschließend ist die Azure-Diagnoseerweiterung auf dem virtuellen Computer installiert und wird ausgeführt. Diese Erweiterung ist für das Sammeln von Diagnosedaten zuständig.
2. Aktivieren Sie die Überwachung, und konfigurieren Sie die Ereignisprotokollierung für einen vorhandenen virtuellen Computer. Sie können die Diagnose auf VM-Ebene aktivieren. Zum Aktivieren der Diagnose und zur anschließenden Konfiguration der Ereignisprotokollierung führen Sie die folgenden Schritte aus:

   1. Wählen Sie den virtuellen Computer aus.
   2. Klicken Sie auf **Überwachen**.
   3. Klicken Sie auf **Diagnose**.
   4. Legen Sie den **Status** auf **EIN** fest.
   5. Wählen Sie alle Diagnoseprotokolle aus, die Sie sammeln möchten.
   6. Klicken Sie auf **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Aktivieren der Azure-Diagnose in einer Webrolle für die Sammlung von IIS-Protokollen und -Ereignissen

Allgemeine Schritte zur Aktivierung der Azure-Diagnose finden Sie unter [Aktivieren der Diagnose in einem Clouddienst](../../cloud-services/cloud-services-dotnet-diagnostics.md). Diese Informationen werden in den unten stehenden Anweisungen verwendet und für den Einsatz mit Log Analytics angepasst.

Bei aktivierter Azure-Diagnose:

* IIS-Protokolle werden standardmäßig gespeichert, und Protokolldaten werden im Übertragungsintervall ScheduledTransferPeriod übertragen.
* Windows-Ereignisprotokolle werden standardmäßig nicht übertragen.

### <a name="to-enable-diagnostics"></a>So aktivieren Sie die Diagnose

Zum Aktivieren der Windows-Ereignisprotokolle oder zum Ändern von scheduledTransferPeriod konfigurieren Sie Azure-Diagnose mithilfe der XML-Konfigurationsdatei („diagnostics.wadcfg“), wie in [Schritt 4: Erstellen der Diagnosekonfigurationsdatei und Installieren der Erweiterung](../../cloud-services/cloud-services-dotnet-diagnostics.md) beschrieben.

Die folgende Beispielkonfigurationsdatei sammelt IIS-Protokolle und alle Ereignisse aus dem Anwendungs- und dem Systemprotokoll:

```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Stellen Sie sicher, dass ConfigurationSettings ein Speicherkonto angibt, wie im folgenden Beispiel gezeigt:

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Sie finden die Werte für **AccountName** und **AccountKey** im Azure-Portal im Speicherkontodashboard unter „Zugriffsschlüssel verwalten“. Als Protokoll für die Verbindungszeichenfolge muss **https**verwendet werden.

Sobald die aktualisierte Diagnosekonfiguration auf den Clouddienst angewendet wurde und die Diagnosedaten in Azure Storage geschrieben werden, können Sie mit der Konfiguration des Log Analytics-Arbeitsbereichs beginnen.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Verwenden des Azure-Portals zum Sammeln von Protokollen aus Azure Storage

Sie können einen Log Analytics-Arbeitsbereich in Azure Monitor über das Azure-Portal konfigurieren, um Protokolle für die folgenden Azure-Dienste zu sammeln:

* Service Fabric-Cluster
* Virtual Machines
* Web-/Workerrollen

Navigieren Sie im Azure-Portal zum Log Analytics-Arbeitsbereich, und führen Sie die folgenden Aufgaben aus:

1. Klicken Sie auf *Storage accounts logs* (Speicherkontoprotokolle).
2. Klicken Sie auf die Aufgabe *Hinzufügen*.
3. Wählen Sie das Speicherkonto mit den Diagnoseprotokollen aus.
   * Dies kann ein klassisches Speicherkonto oder ein Azure Resource Manager-Speicherkonto sein.
4. Wählen Sie den Datentyp aus, für den Sie Protokolle sammeln möchten.
   * Dies können IIS-Protokolle, Ereignisse, Syslog-Daten (Linux), ETW-Protokolle oder Service Fabric-Ereignisse sein.
5. Der Wert für die Quelle wird automatisch basierend auf dem Datentyp aufgefüllt und kann nicht geändert werden.
6. Klicken Sie auf „OK“, um die Konfiguration zu speichern.

Wiederholen Sie die Schritte 2 bis 6 für weitere Speicherkonten und Datentypen, die Sie im Arbeitsbereich sammeln möchten.

Nach etwa 30 Minuten können Sie Daten aus dem Speicherkonto im Log Analytics-Arbeitsbereich sehen. Es werden nur Daten angezeigt, die nach dem Anwenden der Konfiguration in den Speicher geschrieben wurden. Der Arbeitsbereich liest keine bereits vorher im Speicherkonto vorhandenen Daten.

> [!NOTE]
> Das Portal überprüft nicht, ob die Quelle im Speicherkonto vorhanden ist oder ob neue Daten geschrieben werden.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Aktivieren der Azure-Diagnose auf einem virtuellen Computer zur Erfassung von Ereignis- und IIS-Protokollen mit PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Führen Sie die Schritte unter [Konfigurieren von Azure Monitor zum Indizieren der Azure-Diagnose](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) aus, um PowerShell zum Lesen von Azure-Diagnosedaten zu verwenden, die in einen Tabellenspeicher geschrieben wurden.

Mit Azure PowerShell können Sie die Ereignisse genauer angeben, die in Azure Storage geschrieben werden.
Weitere Informationen finden Sie unter [Aktivieren der Diagnose auf virtuellen Azure-Computern](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Sie können die Azure-Diagnose mit dem folgenden PowerShell-Skript aktivieren und aktualisieren.
Sie können dieses Skript auch mit benutzerdefinierter Protokollierungskonfiguration verwenden.
Ändern Sie das Skript, und legen Sie das Speicherkonto, den Dienstnamen und den Namen des virtuellen Computers fest.
Das Skript verwendet Cmdlets für klassische virtuelle Computer.

Überprüfen Sie das folgende Beispielskript, kopieren Sie es, ändern Sie es nach Bedarf, speichern Sie das Beispiel als PowerShell-Skriptdatei, und führen Sie das Skript aus.

```powershell
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Nächste Schritte

* [Sammeln von Protokollen und Metriken für Azure-Dienste](collect-azure-metrics-logs.md) für unterstützte Azure-Dienste.
* [Aktivieren Sie Lösungen](../../azure-monitor/insights/solutions.md) , um Einblick in die Daten bereitzustellen.
* [Erstellen Sie Suchabfragen](../../azure-monitor/log-query/log-query-overview.md) , um die Daten zu analysieren.