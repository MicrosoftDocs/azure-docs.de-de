---
title: 'Azure Service Fabric: Leistungsüberwachung mit Azure Monitor-Protokolle | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie den Log Analytics-Agent zur Überwachung von Containern und Leistungsindikatoren für Ihre Azure Service Fabric-Cluster einrichten.
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
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 819f6ee4ab079361279a567bceeb74c33fe14186
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60952356"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Leistungsüberwachung mit Azure Monitor-Protokolle

Dieser Artikel beschreibt, wie Sie Ihrem Cluster den Log Analytics-Agent als VM-Skalierungsgruppenerweiterung hinzufügen und mit Ihrem vorhandenen Azure Log Analytics-Arbeitsbereich verbinden. Dies ermöglicht die Erfassung von Diagnosedaten zu Containern, Anwendungen und zur Leistungsüberwachung. Durch Hinzufügen als Erweiterung der VM-Skalierungsgruppenressource stellt Azure Resource Manager sicher, dass der Agent auf jedem Knoten installiert wird – auch beim Skalieren des Clusters.

> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass Sie bereits einen Azure Log Analytics-Arbeitsbereich eingerichtet haben. Fahren Sie andernfalls mit [Einrichten von Azure Monitor-Protokolle für einen Cluster](service-fabric-diagnostics-oms-setup.md) fort.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Hinzufügen der Agent-Erweiterung über die Azure-Befehlszeilenschnittstelle

Den Log Analytics-Agent fügen Sie Ihrem Cluster am besten über die VM-Skalierungsgruppen-APIs hinzu, die für die Azure-Befehlszeilenschnittstelle zur Verfügung stehen. Falls Sie die Azure-Befehlszeilenschnittstelle noch nicht eingerichtet haben, wechseln Sie zum Azure-Portal, und öffnen Sie eine [Cloud Shell](../cloud-shell/overview.md)-Instanz, oder [installieren Sie die Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Vergewissern Sie sich nach dem Anfordern Ihrer Cloud Shell, dass Sie in dem Abonnement arbeiten, in dem sich auch Ihre Ressource befindet. Verwenden Sie für diese Überprüfung `az account show`, und vergewissern Sie sich, dass der Wert für „name“ dem Namen des Abonnements Ihres Clusters entspricht.

2. Navigieren Sie im Portal zu der Ressourcengruppe, in der sich Ihr Log Analytics-Arbeitsbereich befindet. Klicken Sie in die Log Analytics-Ressource (der Typ der Ressource ist Log Analytics-Arbeitsbereich). Klicken Sie auf der Ressourcenübersichtsseite im linken Menü im Abschnitt „Einstellungen“ auf **Erweiterte Einstellungen**.

    ![Eigenschaftenseite von Log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Klicken Sie auf **Windows-Server**, wenn Sie einen Windows-Cluster einrichten möchten, bzw. auf **Linux-Server**, wenn Sie einen Linux-Cluster erstellen möchten. Auf dieser Seite werden Ihre `workspace ID` und `workspace key` angezeigt (als Primärschlüssel im Portal aufgeführt). Sie benötigen beide im nächsten Schritt.

4. Führen Sie den Befehl aus, mit dem der Log Analytics-Agent in Ihrem Cluster installiert wird. Verwenden Sie dazu die `vmss extension set`-API in Ihrer Cloud Shell:

    Windows-Cluster:

    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Linux-Cluster:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Hier sehen Sie ein Beispiel für das Hinzufügen des Log Analytics-Agents zu einem Windows-Cluster.

    ![Log Analytics-Agent – CLI-Befehl](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Das Hinzufügen des Agents zu Ihren Knoten sollte in weniger als 15 Minuten erfolgreich abgeschlossen sein. Mit der `az vmss extension list`-API können Sie überprüfen, ob die Agents hinzugefügt wurden:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Hinzufügen des Agents über die Resource Manager-Vorlage

Resource Manager-Beispielvorlagen zum Bereitstellen eines Azure Log Analytics-Arbeitsbereichs sowie zum Hinzufügen eines Agents zu den einzelnen Knoten stehen für [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) und [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) zur Verfügung.

Sie können diese Vorlage herunterladen und anpassen, um einen Cluster bereitzustellen, der Ihre Anforderungen am besten erfüllt.

## <a name="view-performance-counters"></a>Anzeigen von Leistungsindikatoren

Jetzt haben Sie den Log Analytics-Agent hinzugefügt und können im Log Analytics-Portal die Leistungsindikatoren auswählen, die Sie erfassen möchten.

1. Navigieren Sie im Azure-Portal zu der Ressourcengruppe, in der Sie die Service Fabric Analytics-Lösung erstellt haben. Wählen Sie **ServiceFabric\<nameOfLog AnalyticsWorkspace\>** aus.

2. Klicken Sie auf **Log Analytics**.

3. Klicken Sie auf **Erweiterte Einstellungen**.

4. Klicken Sie auf **Daten** und dann auf **Windows- oder Linux-Leistungsindikatoren**. Es gibt eine Liste von Standardindikatoren, deren Aktivierung Sie auswählen und für die Sie auch das Sammlungsintervall festlegen können. Sie können auch [weitere Leistungsindikatoren](service-fabric-diagnostics-event-generation-perf.md) hinzufügen, die erfasst werden sollen. Auf das richtige Format wird in diesem [Artikel](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx) verwiesen.

5. Klicken Sie auf **Speichern** und dann auf **OK**.

6. Schließen Sie das Blatt „Erweiterte Einstellungen“.

7. Klicken Sie unter der Überschrift „Allgemein“ auf **Zusammenfassung des Arbeitsbereichs**.

8. Sie sehen Kacheln in Form eines Graphs für jede der aktivierten Lösungen, auch eine für Service Fabric. Klicken Sie auf den Graph **Service Fabric**, um mit der Service Fabric-Analyse-Lösung fortzufahren.

9. Sie sehen einige Kacheln mit Graphen zu Betriebskanal- und Reliable Services-Ereignissen. Die grafische Darstellung der Daten, die für die ausgewählten Leistungsindikatoren eingehen, werden unter „Knotenmetriken“ angezeigt.

10. Klicken Sie auf den Graph einer Containermetrik, um weitere Details anzuzeigen. Sie können Leistungsindikatordaten ähnlich wie Clusterereignisse abfragen und mithilfe der Abfragesprache Kusto nach Knoten, Name des Leistungsindikators und Werten filtern.

![Log Analytics-Leistungsindikatorabfrage](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Nächste Schritte

* Erfassen Sie relevante [Leistungsindikatoren](service-fabric-diagnostics-event-generation-perf.md). Lesen Sie zum Konfigurieren des Log Analytics-Agents für das Erfassen von bestimmten Leistungsindikatoren [Konfigurieren von Datenquellen](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources).
* Konfigurieren von Azure Monitor-Protokolle für die Einrichtung von [automatisierten Warnungen](../log-analytics/log-analytics-alerts.md) zur Unterstützung bei der Erkennung und Diagnose
* Alternativ können Sie Leistungsindikatoren über die [Azure Diagnostics-Erweiterung erfassen und an Application Insights senden](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).
