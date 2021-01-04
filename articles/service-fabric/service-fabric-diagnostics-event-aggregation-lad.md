---
title: Ereignisaggregation mit der Linux Azure-Diagnose
description: Erfahren Sie, wie Sie Ereignisse unter Verwendung von LAD zur Überwachung und Diagnose von Azure Service Fabric-Clustern aggregieren und sammeln.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: 453258bb5768a2faa8f4e42cce86d02125283026
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002169"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Ereignisaggregation und -sammlung mit Linux-Azure-Diagnose
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Bei Verwendung eines Azure Service Fabric-Clusters empfiehlt es sich, die Protokolle aller Knoten an einem zentralen Ort zu sammeln. Das Sammeln der Protokolle an einem zentralen Ort hilft Ihnen bei Analyse und Behandlung von Problemen, die ggf. in Ihrem Cluster oder in den Anwendungen und Diensten des Clusters auftreten.

Eine Möglichkeit zum Hochladen und Sammeln von Protokollen ist die Verwendung der Erweiterung Linux-Azure-Diagnose (LAD), mit der Protokolle in Azure Storage hochgeladen werden und an Azure Application Insights oder Event Hubs gesendet werden können. Sie können zudem einen externen Prozess verwenden, um die Ereignisse aus dem Speicher zu lesen und in einem Analyseplattformprodukt wie [Azure Monitor-Protokolle](./service-fabric-diagnostics-oms-setup.md) oder in einer anderen Protokollanalyselösung zu verwenden.

## <a name="log-and-event-sources"></a>Protokoll- und Ereignisquellen

### <a name="service-fabric-platform-events"></a>Service Fabric-Plattformereignisse
Service Fabric gibt über [LTTng](https://lttng.org) einige vordefinierte Protokolle aus, einschließlich Betriebs- oder Laufzeitereignissen. Diese Protokolle werden an dem in der Resource Manager-Vorlage des Clusters angegebenen Speicherort gespeichert. Um Informationen zum Speicherkonto abzurufen oder festzulegen, suchen Sie nach dem Tag **AzureTableWinFabETWQueryable** und dort nach **StoreConnectionString**.

### <a name="application-events"></a>Anwendungsereignisse
 Ereignisse, die über den Code Ihrer Anwendungen und Dienste so ausgegeben werden, wie Sie dies beim Instrumentieren der Software angegeben haben. Sie können eine beliebige Protokollierungslösung verwenden, die textbasierte Protokolldateien schreibt (beispielsweise LTTng). Weitere Informationen finden Sie in der LTTng-Dokumentation zur Ablaufverfolgung für Ihre Anwendung.

[Überwachen und Diagnostizieren von Diensten in einer Entwicklungsumgebung auf einem lokalen Computer](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## <a name="deploy-the-diagnostics-extension"></a>Bereitstellen der Diagnoseerweiterung
Zum Sammeln von Protokollen muss zunächst die Diagnoseerweiterung auf allen VMs des Service Fabric-Clusters bereitgestellt werden. Die Diagnoseerweiterung sammelt Protokolle auf allen VMs und lädt sie in das angegebene Speicherkonto hoch. 

Wenn Sie die Diagnoseerweiterung im Rahmen der Clustererstellung für die im Cluster enthaltenen virtuellen Computer bereitstellen möchten, legen Sie **Diagnose** auf **Ein** fest. Nachdem Sie den Cluster erstellt haben, können Sie diese Einstellung nicht mithilfe des Portals ändern, sodass Sie die entsprechenden Änderungen in der Resource Manager-Vorlage vornehmen müssen.

Dadurch wird der LAD-Agent konfiguriert, um bestimmte Protokolldateien zu überwachen. Sobald bei einer Datei eine neue Zeile hinzukommt, erstellt der Agent einen Syslog-Eintrag, der an den von Ihnen angegebenen Speicher (Tabelle) gesendet wird.


## <a name="next-steps"></a>Nächste Schritte

1. Ausführlichere Informationen zu relevanten Ereignissen für die Problembehandlung finden Sie in der [LTTng-Dokumentation](https://lttng.org/docs) sowie unter [Verwenden der Linux-Diagnoseerweiterung zur Überwachung der Leistungs- und Diagnosedaten einer Linux-VM](../virtual-machines/extensions/diagnostics-linux.md).
2. [Richten Sie den Log Analytics-Agent ein](service-fabric-diagnostics-event-analysis-oms.md), um bei der Sammlung von Metriken, bei der Überwachung der in Ihrem Cluster bereitgestellten Container und bei der Visualisierung Ihrer Protokolle zu helfen. 
