---
title: Häufige Diagnoseszenarien in Azure Service Fabric | Microsoft-Dokumentation
description: Lernen Sie die Problembehandlung bei häufigen Szenarien mit Azure Service Fabric kennen
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 265aea1b8873d812859b39175c732c3e7118cbb5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394210"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnostizieren häufiger Szenarien mit Service Fabric

Dieser Artikel veranschaulicht allgemeine Szenarien, die bei Benutzern im Bereich der Überwachung und Diagnose mit Service Fabric aufgetreten sind. Die vorgestellten Szenarien behandeln alle 3 Ebenen von Service Fabric: Anwendung, Cluster und Infrastruktur. Bei jeder Lösung kommen Application Insights und Azure Monitor-Protokolle sowie Azure-Überwachungstools zum Einsatz, um die einzelnen Szenarien abzuschließen. Die Schritte in den einzelnen Lösungen geben Benutzern eine Einführung in die Verwendung von Application Insights und Azure Monitor-Protokolle im Zusammenhang mit Service Fabric.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Voraussetzungen und Empfehlungen

Für die Lösungen in diesem Artikel werden die folgenden Tools verwendet. Es wird empfohlen, diese Tools vorher einzurichten und zu konfigurieren:

* [Application Insights mit Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Aktivieren der Azure-Diagnose in Ihrem Cluster](service-fabric-diagnostics-event-aggregation-wad.md)
* [Einrichten eines Log Analytics-Arbeitsbereichs](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics-Agent zum Nachverfolgen von Leistungsindikatoren](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Wie kann ich nicht behandelte Ausnahmen in meiner Anwendung anzeigen?

1. Navigieren Sie zu Ihrer Application Insights-Ressource, mit der die Anwendung konfiguriert ist.
2. Klicken Sie oben links auf *Suchen*. Klicken Sie dann im nächsten Fenster auf „Filter“.

    ![AI-Übersicht](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Viele Arten von Ereignissen (Ablaufverfolgungen, Anforderungen, benutzerdefinierte Ereignisse) werden angezeigt. Wählen Sie „Ausnahme“ als Filter aus.

    ![AI-Filterliste](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Durch Klicken auf eine Ausnahme in der Liste können Sie weitere Einzelheiten, einschließlich des Dienstkontexts anzeigen, wenn Sie das Service Fabric Application Insights SDK verwenden.

    ![AI-Ausnahme](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Wie kann ich sehen, welche HTTP-Aufrufe in meinen Diensten verwendet werden?

1. In der gleichen Application Insights-Ressource können Sie anstatt nach „Ausnahme“ nach „Anforderungen“ filtern und alle vorgenommenen Anforderungen anzeigen.
2. Wenn Sie das Service Fabric Application Insights SDK verwenden, können Sie eine visuelle Darstellung der miteinander verbundenen Dienste sowie die Anzahl der erfolgreichen und fehlgeschlagenen Anforderungen anzeigen. Klicken Sie auf der linken Seite auf „App-Übersicht“.

    ![Blatt „AI-App-Übersicht“](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI-App-Übersicht](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Weitere Informationen zur Anwendungsübersicht finden Sie in der [Dokumentation zur Anwendungsübersicht](../azure-monitor/app/app-map.md).

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Wie erstelle ich eine Warnung, wenn ein Knoten ausfällt?

1. Knotenereignisse werden von Ihrem Service Fabric-Cluster nachverfolgt. Navigieren Sie zur Service Fabric Analytics-Lösungsressource mit dem Namen **ServiceFabric(Name_der_Ressourcengruppe)** .
2. Klicken Sie unten auf dem Blatt auf das Diagramm mit dem Titel „Zusammenfassung“.

    ![Lösung mit Azure Monitor-Protokolle](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Hier finden Sie viele Diagramme und Kacheln, die verschiedene Metriken anzeigen. Wenn Sie auf eines der Diagramms klicken, gelangen Sie zur Protokollsuche. Hier können Sie alle Clusterereignisse oder Leistungsindikatoren abfragen.
4. Geben Sie die folgende Abfrage ein. Diese Ereignis-IDs finden Sie in der [Referenz zu Knotenereignissen](service-fabric-diagnostics-event-generation-operational.md#application-events).

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Klicken Sie oben auf „Neue Warnungsregel“. Ab jetzt erhalten Sie jedes Mal, wenn ein Ereignis basierend auf dieser Abfrage eingeht, eine Warnung mit der von Ihnen ausgewählten Kommunikationsmethode.

    ![Azure Monitor-Protokolle – neue Warnung](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Wie kann ich Warnungen zu Rollbacks von Anwendungsupgrades erhalten?

1. Geben Sie im gleichen Fenster der Protokollsuche, das Sie zuvor verwendet haben, die folgende Abfrage nach Upgraderollbacks ein. Diese Ereignis-IDs finden Sie in der [Referenz zu Anwendungsereignissen](service-fabric-diagnostics-event-generation-operational.md#application-events).

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Klicken Sie oben auf „Neue Warnungsregel“. Ab jetzt erhalten Sie jedes Mal, wenn ein Ereignis basierend auf dieser Abfrage eingeht, eine Warnung.

## <a name="how-do-i-see-container-metrics"></a>Wie zeige ich Containermetriken an?

In der gleichen Ansicht mit allen Diagrammen sehen Sie einige Kacheln für die Leistung Ihrer Container. Sie benötigen den Log Analytics-Agent und die [Containerüberwachungslösung](service-fabric-diagnostics-oms-containers.md), damit diese Kacheln aufgefüllt werden.

![Log Analytics-Containermetriken](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Um Telemetriedaten von **innerhalb** Ihres Containers zu instrumentieren, müssen Sie das [Application Insights NuGet-Paket für Container](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios) hinzufügen.

## <a name="how-can-i-monitor-performance-counters"></a>Wie kann ich Leistungsindikatoren überwachen?

1. Nachdem Sie den Log Analytics-Agent dem Cluster hinzugefügt haben, müssen Sie die spezifischen Leistungsindikatoren hinzufügen, die Sie nachverfolgen möchten. Navigieren Sie im Portal zur Seite des Log Analytics-Arbeitsbereichs – von der Lösungsseite aus gesehen befindet sich die Registerkarte für den Arbeitsbereich im linken Menü.

    ![Registerkarte „Log Analytics-Arbeitsbereich“](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Sobald Sie die Seite des Arbeitsbereichs aufgerufen haben, klicken Sie im gleichen linken Menü auf „Erweiterte Einstellungen“.

    ![Log Analytics: erweiterte Einstellungen](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Klicken Sie auf „Daten“ > „Windows-Leistungsindikatoren“ (bzw. „Daten“ > „Linux-Leistungsindikatoren“ bei Linux-Computern), um damit zu beginnen, über den Log Analytics-Agent bestimmte Leistungsindikatoren von Ihren Knoten zu sammeln. Es folgen Beispiele für das Format, in dem Leistungsindikatoren hinzugefügt werden müssen

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     In der Schnellstartanleitung werden VotingData und VotingWeb als Prozessnamen verwendet. Die Nachverfolgung dieser Leistungsindikatoren würde wie folgt aussehen:

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Log Analytics: Leistungsindikatoren](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Anhand dieser Indikatoren können Sie sehen, wie Ihre Infrastruktur die Arbeitslasten verarbeitet, und anhand der Ressourcenauslastung entsprechende Warnungen festlegen. Beispiel: Sie möchten eine Warnung festlegen, wenn die Prozessorgesamtauslastung über 90 % steigt oder unter 5 % fällt. Der Name des Leistungsindikators, den Sie dafür verwenden, lautet „% Prozessorzeit“. Dazu können Sie eine Warnungsregel für die folgende Abfrage erstellen:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Wie kann ich die Leistung meiner Reliable Services und Reliable Actors nachverfolgen?

Um die Leistung von Reliable Services oder Reliable Actors in Ihren Anwendungen nachzuverfolgen, sollten Sie auch die Leistungsindikatoren für Service Fabric Actor, Actor-Methode, Dienst und Dienstmethode erfassen. Beispiele für zu erfassende Leistungsindikatoren für Reliable Services und Reliable Actors

>[!NOTE]
>Service Fabric-Leistungsindikatoren können derzeit nicht vom Log Analytics-Agent gesammelt werden, aber von [anderen Diagnoselösungen](service-fabric-diagnostics-partners.md).

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Nutzen Sie diese Links, um die vollständige Liste der Leistungsindikatoren für Reliable [Services](service-fabric-reliable-serviceremoting-diagnostics.md) und Reliable [Actors](service-fabric-reliable-actors-diagnostics.md) abzurufen.

## <a name="next-steps"></a>Nächste Schritte

* [Richten Sie Warnungen in AI ein](../azure-monitor/app/alerts.md), um Benachrichtigungen zu Änderungen der Leistung oder Nutzung zu erhalten.
* Die [intelligente Erkennung in Application Insights](../azure-monitor/app/proactive-diagnostics.md) führt eine proaktive Analyse der an AI gesendeten Telemetriedaten aus, um Sie vor potenziellen Leistungsproblemen zu warnen.
* Erfahren Sie mehr über die [Warnungen](../log-analytics/log-analytics-alerts.md) von Azure Monitor-Protokolle, die bei der Erkennung und Diagnose hilfreich sein können.
* Für lokale Cluster bietet Azure Monitor-Protokolle ein Gateway (HTTP-Weiterleitungsproxy), über das Daten an Azure Monitor-Protokolle gesendet werden können. Weitere Informationen dazu finden Sie unter [Verbinden von Computern ohne Internetzugriff mit Azure Monitor-Protokolle über das Log Analytics-Gateway](../azure-monitor/platform/gateway.md).
* Machen Sie sich mit den Funktionen zur [Protokollsuche und -abfrage](../log-analytics/log-analytics-log-searches.md) in Azure Monitor-Protokolle vertraut.
* Eine ausführlichere Übersicht über Azure Monitor-Protokolle und die zugehörigen Optionen finden Sie unter [Was ist Azure Monitor-Protokolle?](../operations-management-suite/operations-management-suite-overview.md).
