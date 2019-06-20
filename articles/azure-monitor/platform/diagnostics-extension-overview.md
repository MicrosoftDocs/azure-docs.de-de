---
title: Übersicht zur Azure-Diagnoseerweiterung
description: Verwenden Sie die Azure-Diagnose zur Problembehandlung, zur Leistungsmessung, zur Überwachung und zur Datenverkehrsanalyse in Clouddiensten, virtuellen Maschinen und Service Fabric.
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 8a287f118c126967d2cf8cad77a434cfecc098eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60236232"
---
# <a name="what-is-azure-diagnostics-extension"></a>Was ist die Azure-Diagnoseerweiterung?
Die Azure-Diagnoseerweiterung ist ein Agent innerhalb von Azure, mit dem Diagnosedaten für eine bereitgestellte Anwendung erfasst werden können. Sie können die Diagnoseerweiterung von einer Reihe verschiedener Quellen aus verwenden. Derzeit werden die Web- und Workerrollen des Azure-Clouddiensts (klassisch), Virtual Machines, Skalierungsgruppen von Virtual Machines und Service Fabric unterstützt. Andere Azure-Dienste haben unterschiedliche Diagnosemethoden. Siehe [Übersicht über die Überwachung in Microsoft Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Linux-Agent
Für virtuelle Computer mit Linux ist eine [Linux-Version der Erweiterung](../../virtual-machines/extensions/diagnostics-linux.md) verfügbar. Die gesammelten Statistiken und das Verhalten unterscheiden sich von der Windows-Version.

## <a name="data-you-can-collect"></a>Erfassbare Daten
Die Azure-Diagnoseerweiterung kann die folgenden Datentypen erfassen:

| Data source | BESCHREIBUNG |
| --- | --- |
| Leistungsindikatormetriken |Leistungsindikatoren des Betriebssystems und benutzerdefinierte Leistungsindikatoren |
| Anwendungsprotokolle |Von Ihrer Anwendung geschriebene Ablaufverfolgungsmeldungen |
| Windows-Ereignisprotokolle |An das Windows-System für die Ereignisprotokollierung gesendete Informationen |
| .NET EventSource-Protokolle |Code zum Schreiben von Ereignissen mit der .NET-Klasse [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| IIS-Protokolle |Informationen zu IIS-Websites |
| [Manifestbasierte ETW-Protokolle](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Von einem beliebigen Prozess generierte Ereignisse der Ereignisablaufverfolgung für Windows.(1) |
| Absturzabbilder (Protokolle) |Informationen zum Status des Prozesses bei Absturz einer Anwendung |
| Benutzerdefinierte Fehlerprotokolle |Von Ihrer Anwendung oder Ihrem Dienst erstellte Protokolle |
| Infrastrukturprotokolle der Azure-Diagnose |Informationen zur Azure-Diagnose selbst |

(1) Um eine Liste der ETW-Anbieter zu erhalten, führen Sie `c:\Windows\System32\logman.exe query providers` in einem Konsolenfenster auf dem Computer aus, von dem Sie die Informationen sammeln möchten.

## <a name="data-storage"></a>Datenspeicher
Die Erweiterung speichert ihre Daten in einem von Ihnen angegebenen [Azure Storage-Konto](diagnostics-extension-to-storage.md).

Sie können auch an [Application Insights](../../azure-monitor/app/cloudservices.md) gesendet werden. 

Als weitere Option können sie auch an [Event Hub](../../event-hubs/event-hubs-about.md) gestreamt werden, was Ihnen wiederum ermöglicht, sie an Überwachungsdienste außerhalb von Azure zu senden.

Sie können Ihre Daten auch an die Datenbank für metrische Zeitreihen von Azure Monitor senden. Zur Zeit ist diese Senke nur für Leistungsindikatoren anwendbar. Hiermit können Sie Leistungsindikatoren als benutzerdefinierte Metriken senden. Dieses Feature befindet sich in der Vorschau. Die Azure Monitor-Senke unterstützt Folgendes:
* Abrufen aller Leistungsindikatoren, die über die [APIs der Azure Monitor-Metriken](https://docs.microsoft.com/rest/api/monitor/) an Azure Monitor gesendet wurden.
* Benachrichtigung für alle Leistungsindikatoren, die über die [Metrikwarnungen](../../azure-monitor/platform/alerts-overview.md) in Azure Monitor an Azure Monitor gesendet werden.
* Behandeln von Platzhalteroperatoren in Leistungsindikatoren als die Dimension „Instanz“ in Ihrer Metrik.  Wenn Sie z.B. den Indikator „LogicalDisk(\*)/DiskWrites/Sek.“ erfasst haben, können Sie die Dimension „Instanz“ filtern und aufteilen, um für jeden logischen Datenträger auf dem virtuellen Computer (z.B. C:) Schreibvorgänge pro Sekunde darzustellen oder hinsichtlich dieser Vorgänge Warnungen auszugeben.

Weitere Informationen zur Konfiguration dieser Senke finden Sie in der [Dokumentation zum Azure-Diagnoseschema](diagnostics-extension-schema-1dot3.md).

## <a name="costs"></a>Kosten
Für jede der oben genannten Optionen können Kosten anfallen. Prüfen Sie diese daher genau, um unerwartet hohe Rechnungen zu vermeiden.  Bei Application Insights, Event Hub und Azure Storage fallen separate Kosten für die Datenerfassung und den Zeitraum der Speicherung an. Insbesondere werden in Azure Storage Daten unbegrenzt aufbewahrt – daher empfiehlt es sich, ältere Daten nach einem bestimmten Zeitraum zu bereinigen, um die Kosten niedrig zu halten.    

## <a name="versioning-and-configuration-schema"></a>Schema für Versionsverwaltung und Konfiguration
Siehe [Azure-Diagnoseerweiterung – Versionen und Verlauf des Konfigurationsschemas](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Nächste Schritte
Wählen Sie, zu welchem Dienst Sie Diagnoseinformationen erfassen möchten, und lesen Sie zum Einstieg die folgenden Artikel. Verwenden Sie die allgemeinen Links zur Azure-Diagnose als Referenz für bestimmte Aufgaben.

## <a name="cloud-services-using-azure-diagnostics"></a>Clouddienste mit der Azure-Diagnose
* Wenn Sie Visual Studio verwenden, finden Sie unter [Verfolgen einer Cloud Services-Anwendung mit Visual Studio](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) Informationen für den Einstieg. Andernfalls finden Sie weitere Informationen in folgenden Artikeln:
* [Überwachen von Clouddiensten mit der Azure-Diagnose](../../cloud-services/cloud-services-how-to-monitor.md)
* [Einrichten der Azure-Diagnose in einer Cloud Services-Anwendung](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Fortgeschrittenere Themen finden Sie in folgenden Artikeln:

* [Verwenden der Azure-Diagnose mit Application Insights für Cloud Services](../../azure-monitor/app/cloudservices.md)
* [Verfolgen des Ablaufs einer Cloud Services-Anwendung mit der Azure-Diagnose](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Verwenden von PowerShell zum Einrichten der Diagnose für Cloud Services](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Wenn Sie Visual Studio verwenden, finden Sie unter [Verfolgen von Azure Virtual Machines mit Visual Studio](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) Informationen für den Einstieg. Andernfalls finden Sie weitere Informationen in folgenden Artikeln:
* [Einrichten der Azure-Diagnose auf einem virtuellen Azure-Computer](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

Fortgeschrittenere Themen finden Sie in folgenden Artikeln:

* [Verwenden von PowerShell zum Einrichten der Diagnose für Azure Virtual Machines](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Erstellen eines virtuellen Windows-Computers mit Überwachung und Diagnose mithilfe von Azure-Ressourcen-Manager-Vorlagen](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Erste Schritte finden Sie unter [Überwachen einer Service Fabric-Anwendung](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Viele weitere Artikel zur Service Fabric-Diagnose stehen in der Navigationsstruktur auf der linken Seite zur Verfügung, nachdem Sie diesen Artikel aufgerufen haben.

## <a name="general-articles"></a>Allgemeine Artikel
* Hier erhalten Sie Informationen zur Verwendung von [Leistungsindikatoren in der Azure-Diagnose](../../cloud-services/diagnostics-performance-counters.md).
* Wenn bei der Diagnose Probleme mit dem Starten oder dem Finden von Daten in Azure-Speichertabellen auftreten, finden Sie weitere Informationen unter [Problembehandlung mit Azure-Diagnose](diagnostics-extension-troubleshooting.md).

