---
title: Azure Service Fabric-Ereignisanalyse mit Azure Monitor-Protokollen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ereignisse visualisieren und analysieren, indem Sie Azure Monitor-Protokolle zur Überwachung und Diagnose von Azure Service Fabric-Clustern verwenden.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: ba4923edbc59f0e6650fda1a71e1c4f79b884cf2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393407"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Ereignisanalyse und -visualisierung mit Azure Monitor-Protokollen
 Azure Monitor-Protokolle sammeln und analysieren Telemetriedaten von Anwendungen und Diensten, die in der Cloud gehostet werden. Darüber hinaus sind Analysetools verfügbar, um die Verfügbarkeit und Leistung dieser Anwendungen und Dienste maximieren zu können. In diesem Artikel wird beschrieben, wie Sie Abfragen in Azure Monitor-Protokolle ausführen, um einen Einblick in den Cluster zu erhalten und Probleme zu beheben. Folgende allgemeine Fragen werden berücksichtigt:

* Wie behebe ich Probleme bei Integritätsereignissen?
* Wie weiß ich, dass ein Knoten ausfällt?
* Wie finde ich heraus, ob die Dienste der Anwendung gestartet oder beendet wurden?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Übersicht über den Log Analytics-Arbeitsbereich

>[!NOTE] 
>Obwohl der Diagnosespeicher bei der Clustererstellung standardmäßig aktiviert wird, müssen Sie den Log Analytics-Arbeitsbereich dennoch so einrichten, dass er aus dem Diagnosespeicher gelesen wird.

Azure Monitor-Protokolle sammeln Daten von verwalteten Ressourcen, z.B. von einer Azure Storage-Tabelle oder einem Agent, und verwalten sie in einem zentralen Repository. Die Daten können dann für Analyse, Warnungen und Visualisierung oder für den weiteren Export verwendet werden. Azure Monitor-Protokolle unterstützen Ereignisse, Leistungsdaten oder jegliche anderen benutzerdefinierten Daten. Überprüfen Sie die [Schritte zum Konfigurieren der Diagnoseerweiterung zum Aggregieren von Ereignissen](service-fabric-diagnostics-event-aggregation-wad.md) und die [Schritte zum Erstellen eines Log Analytics-Arbeitsbereichs zum Lesen der Ereignisse im Speicher](service-fabric-diagnostics-oms-setup.md), um sicherzustellen, dass Daten in Azure Monitor-Protokolle geschrieben werden.

Nachdem Daten von Azure Monitor-Protokollen empfangen wurden, verfügt Azure über mehrere *Überwachungslösungen*. Dabei handelt es sich um vorkonfigurierte Lösungen oder operative Dashboards zum Überwachen eingehender Daten, die an verschiedene Szenarien angepasst sind. Dazu gehören eine *Service Fabric-Analyselösung* und eine *Containerlösung*. Dies sind die beiden wichtigsten Lösungen für die Diagnose und Überwachung bei Verwendung von Service Fabric-Clustern. In diesem Artikel wird die Verwendung der Service Fabric-Analyse-Lösung beschrieben, die im Arbeitsbereich erstellt wird.

## <a name="access-the-service-fabric-analytics-solution"></a>Zugriff auf die Service Fabric-Analyse-Lösung

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Ressourcengruppe, in der Sie die Service Fabric-Analyse-Lösung erstellt haben.

Wählen Sie die Ressource **ServiceFabric\<OMS-Arbeitsbereichsname\>** aus.

In `Summary` werden Kacheln in Form eines Graphs für jede der aktivierten Lösungen angezeigt, dies schließt Service Fabric ein. Klicken Sie auf den Graph **Service Fabric**, um mit der Service Fabric-Analyse-Lösung fortzufahren.

![Service Fabric-Lösung](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

In der folgenden Abbildung ist die Startseite der Service Fabric-Analyse-Lösung dargestellt. Diese Startseite bietet eine Momentaufnahmeansicht der Vorgänge im Cluster.

![Service Fabric-Lösung](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Wenn Sie die Diagnose bei der Clustererstellung aktiviert haben, werden folgende Ereignisse angezeigt: 

* [Service Fabric-Clusterereignisse](service-fabric-diagnostics-event-generation-operational.md)
* [Ereignisse des Reliable Actors-Programmiermodells](service-fabric-reliable-actors-diagnostics.md)
* [Ereignisse des Reliable Services-Programmiermodells](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Zusätzlich zu den Service Fabric-Ereignissen können durch das [Aktualisieren der Konfiguration der Diagnoseerweiterung](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations) detailliertere Systemereignisse erfasst werden.

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Anzeigen von Service Fabric-Ereignissen einschließlich Aktionen auf Knoten

Klicken Sie auf der Seite „Service Fabric-Analyse“ auf den Graphen für **Service Fabric-Ereignisse**.

![Service Fabric-Lösung – Betriebskanal](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Klicken Sie auf **Liste**, um die Ereignisse in einer Liste anzuzeigen. In der Liste werden alle gesammelten Systemereignisse angezeigt. Zur Referenz: Diese stammen aus **WADServiceFabricSystemEventsTable** im Azure Storage-Konto. Auch die Reliable Services- und Reliable Actors-Ereignisse, die als Nächstes angezeigt werden, stammen aus diesen entsprechenden Tabellen.
    
![Abfrage – Betriebskanal](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Alternativ können Sie auf die Lupe links klicken und mit der Abfragesprache Kusto die gewünschten Ereignisse suchen. Um beispielsweise alle Aktionen zu suchen, die im Cluster für Knoten durchgeführt wurden, können Sie die folgende Abfrage verwenden. Die unten verwendeten Ereignis-IDs finden Sie in der [Referenz der Betriebskanalprotokolle](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Sie können Abfragen für viele weitere Felder durchführen, z. B. für bestimmte Knoten (Computer), den Systemdienst (TaskName) usw.

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Anzeigen von Service Fabric Reliable Service- und Reliable Actor-Ereignissen

Klicken Sie auf der Seite „Service Fabric-Analyse“ auf den Graphen für **Reliable Services**.

![Service Fabric-Lösung – Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Klicken Sie auf **Liste**, um die Ereignisse in einer Liste anzuzeigen. Hier werden Ereignisse aus den zuverlässigen Diensten angezeigt. Es werden unterschiedliche Ereignisse für den Start und das Ende des runasync-Diensts angezeigt. Dies erfolgt normalerweise bei Bereitstellungen und Upgrades. 

![Abfrage – Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Reliable Actor-Ereignisse können auf ähnliche Weise angezeigt werden. Um ausführlichere Ereignisse für Reliable Actors zu konfigurieren, müssen Sie `scheduledTransferKeywordFilter` in der Konfiguration für die Diagnoseerweiterung ändern (siehe unten). Details zu den entsprechenden Werten finden Sie in der [Referenz der Reliable Actors-Ereignisse](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Die Abfragesprache Kusto ist leistungsstark. Eine weitere nützliche Abfrage ist die Suche der Knoten, die die meisten Ereignisse generieren. In der Abfrage im folgenden Screenshot sind mit anhand des spezifischen Diensts und Knotens aggregierte Reliable Services-Ereignisse zu sehen.

![Abfrage – Ereignisse pro Knoten](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Nächste Schritte

* Um die Infrastrukturüberwachung, d.h. Leistungsindikatoren, zu aktivieren, wechseln Sie zu [Hinzufügen des Log Analytics-Agents](service-fabric-diagnostics-oms-agent.md). Der Agent sammelt Leistungsindikatoren und fügt sie dem vorhandenen Arbeitsbereich zu.
* Für lokale Cluster bieten Azure Monitor-Protokolle ein Gateway (HTTP-Weiterleitungsproxy), über das Daten an Azure Monitor-Protokolle gesendet werden können. Weitere Informationen dazu finden Sie unter [Verbinden von Computern ohne Internetzugriff über das Log Analytics-Gateway](../azure-monitor/platform/gateway.md).
* Konfigurieren Sie [automatisierte Warnungen](../log-analytics/log-analytics-alerts.md) für die Erkennung und Diagnose.
* Machen Sie sich mit den Features zur [Protokollsuche und -abfrage](../log-analytics/log-analytics-log-searches.md) in Azure Monitor-Protokollen vertraut.
* Eine ausführlichere Übersicht über Azure Monitor-Protokolle und die zugehörigen Optionen finden Sie unter [Was ist Azure Monitor-Protokolle?](../operations-management-suite/operations-management-suite-overview.md).
