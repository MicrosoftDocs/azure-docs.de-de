---
title: Überwachen von Vorgängen, Ereignissen und Leistungsindikatoren für öffentliche Load Balancer vom Typ „Basic“
titleSuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie die Protokollierung für den Azure Load Balancer aktivieren.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572778"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>Azure Monitor-Protokolle für den Azure Load Balancer Standard

Sie können in Azure verschiedene Protokolltypen verwenden, um Load Balancer Standard-Instanzen zu verwalten und eventuelle Fehler zu beheben. Protokolle können an einen Event Hub oder einen Log Analytics-Arbeitsbereich gestreamt werden. Alle Protokolle können aus Azure Blob Storage extrahiert und in anderen Tools wie Excel und PowerBI angezeigt werden.  In der unten stehenden Liste finden Sie weitere Informationen über die verschiedenen Typen von Protokollen.

* **Aktivitätsprotokolle**: Mithilfe von [Anzeigen von Aktivitätsprotokollen zur Überwachung von Aktionen in Ressourcen](../azure-resource-manager/management/view-activity-logs.md) können Sie alle Aktivitäten, die an Ihre Azure-Abonnements übermittelt werden, und ihren Status anzeigen. Aktivitätsprotokolle sind standardmäßig aktiviert und können im Azure-Portal angezeigt werden. Diese Protokolle sind sowohl für den Load Balancer vom Typ „Basic“ als auch für „Standard“ verfügbar.
* **Load Balancer Standard-Metriken:** Sie können dieses Protokoll verwenden, um die als Protokolle für Ihre Load Balancer Standard-Instanz exportierten Metriken abzufragen. Diese Protokolle sind nur für Load Balancer Standard-Instanzen verfügbar.

> [!IMPORTANT]
> **Integritätstest- und Load Balancer-Warnung-Ereignisprotokolle funktionieren zurzeit nicht und werden unter [bekannte Probleme für den Azure Load Balancer](whats-new.md#known-issues).** aufgeführt. 

> [!IMPORTANT]
> Protokolle sind nur für Ressourcen verfügbar, die im Ressourcen-Manager-Bereitstellungsmodell bereitgestellt werden. Sie können Protokolle nicht für Ressourcen im klassischen Bereitstellungsmodell verwenden. Weitere Informationen zu diesen Bereitstellungsmodellen finden Sie unter [Grundlegendes zur Bereitstellung über Resource Manager im Vergleich zur klassischen Bereitstellung](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Aktivieren der Protokollierung

Die Aktivitätsprotokollierung ist automatisch für alle Resource Manager-Ressourcen aktiviert. Aktivieren Sie die Ereignis- und Integritätstestprotokollierung, um mit der Erfassung von Daten aus diesen Protokollen zu beginnen. Führen Sie die folgenden Schritte aus, um die Protokollierung zu aktivieren:

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wenn Sie noch nicht über einen Load Balancer verfügen, [erstellen Sie einen Load Balancer](./quickstart-load-balancer-standard-public-portal.md) , bevor Sie fortfahren.

1. Klicken Sie im Portal auf **Ressourcengruppen**.
2. Wählen Sie den **\<resource-group-name>** der Ressourcengruppe aus, in der sich Ihr Lastenausgleichsmodul befindet.
3. Wählen Sie Ihren Load Balancer aus.
4. Wählen Sie **Aktivitätsprotokoll** > **Diagnoseeinstellungen** aus.
5. Wählen Sie im Bereich **Diagnoseeinstellungen** unter **Diagnoseeinstellungen**, den Eintrag **Diagnoseeinstellung hinzufügen** aus.
6. Geben Sie im Bereich zum Erstellen von **Diagnoseeinstellungen** im Feld **Name** den Wert **myLBDiagnostics** ein.
7. Sie haben die Wahl unter drei Optionen für die **Diagnoseeinstellungen**.  Sie können eine, zwei oder alle drei auswählen und jede nach Ihren Anforderungen konfigurieren:
   * **In einem Speicherkonto archivieren**
   * **An einen Event Hub streamen**
   * **An Log Analytics senden**

    ### <a name="archive-to-a-storage-account"></a>In einem Speicherkonto archivieren
    Für diesen Vorgang benötigen Sie ein bereits erstelltes Speicherkonto.  Informationen zum Erstellen eines Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md?tabs=azure-portal).

    1. Aktivieren Sie das Kontrollkästchen neben **In einem Speicherkonto archivieren**.
    2. Wählen Sie **Konfigurieren** aus, um den Bereich **Speicherkonto auswählen** zu öffnen.
    3. Wählen Sie im Pulldownfeld das **Abonnement** aus, in dem Ihr Speicherkonto erstellt wurde.
    4. Wählen Sie im Pulldownfeld den Namen Ihres Speicherkontos unter **Speicherkonto** aus.
    5. Wählen Sie „OK“ aus.

    ### <a name="stream-to-an-event-hub"></a>An einen Event Hub streamen
    Für diesen Vorgang benötigen Sie einen bereits erstellten Event Hub.  Informationen zum Erstellen eines Event Hubs finden Sie unter [Schnellstart: Erstellen eines Event Hubs mithilfe des Azure-Portals](../event-hubs/event-hubs-create.md).

    1. Aktivieren Sie das Kontrollkästchen neben **An einen Event Hub streamen**
    2. Wählen Sie **Konfigurieren** aus, um den Bereich **Event Hub auswählen** zu öffnen.
    3. Wählen Sie im Pulldownfeld das **Abonnement** aus, unter dem Ihr Event Hub erstellt wurde.
    4. Wählen Sie im Pulldownfeld **Event Hub-Namespace auswählen** aus.
    5. Wählen Sie im Pulldownfeld **Event Hub-Richtlinienname auswählen** aus.
    6. Wählen Sie „OK“ aus.

    ### <a name="send-to-log-analytics"></a>An Log Analytics senden
    Für diesen Prozess benötigen Sie einen bereits erstellten und konfigurierten Log Analytics-Arbeitsbereich.  Eine Anleitung zum Erstellen eines Log Analytics-Arbeitsbereichs finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/learn/quick-create-workspace.md).

    1. Aktivieren Sie das Kontrollkästchen neben **An Log Analytics senden**.
    2. Wählen Sie im Pulldownfeld das **Abonnement** aus, in dem sich Ihr Log Analytics-Arbeitsbereich befindet.
    3. Wählen Sie im Pulldownfeld den **Log Analytics-Arbeitsbereich** aus.


8.  Aktivieren Sie im Abschnitt **METRIK** im Bereich **Diagnoseeinstellungen** das Kontrollkästchen neben diesem Eintrag: **AllMetrics**.

9. Überprüfen Sie, ob alles richtig aussieht, und klicken Sie oben im Bereich zum Erstellen der **Diagnoseeinstellungen** auf **Speichern**.

## <a name="activity-log"></a>Aktivitätsprotokoll

Das Aktivitätsprotokoll wird standardmäßig generiert. Es kann dazu konfiguriert werden, auf Abonnementebene [gemäß der folgenden Anweisungen in diesem Artikel](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log) exportiert zu werden. Mehr zu diesen Protokollen können Sie im Artikel [Anzeigen von Aktivitätsprotokollen zur Überwachung von Aktionen in Ressourcen](../azure-resource-manager/management/view-activity-logs.md) erfahren.

### <a name="view-and-analyze-the-activity-log"></a>Anzeigen und Analysieren des Aktivitätsprotokolls

Mit einer der folgenden Methoden können Sie die Aktivitätsprotokolldaten anzeigen und analysieren:

* **Azure-Tools:** Rufen Sie Informationen aus den Aktivitätsprotokollen über Azure PowerShell, die Azure-Befehlszeilenschnittstelle, die Azure REST-API oder über das Azure-Portal ab. Schrittweise Anleitungen für die einzelnen Methoden finden Sie im Artikel [Überwachen von Vorgängen mit dem Ressourcen-Manager](../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI:** Falls Sie noch kein [Power BI](https://powerbi.microsoft.com/pricing)-Konto besitzen, können Sie es kostenlos testen. Mithilfe der [Azure-Überwachungsprotokolle-Integration mit Power BI](https://powerbi.microsoft.com/integrations/azure-audit-logs/) können Sie Ihre Daten mit vorkonfigurierten Dashboards analysieren oder Ansichten Ihren Anforderungen anpassen.

## <a name="metrics-as-logs"></a>Metriken als Protokolle
Mit der Funktionalität zum Exportieren von Metriken in Protokolle von Azure Monitor können Sie Ihre Load Balancer-Metriken exportieren. Diese Metriken generieren für jedes Samplingintervall von einer Minute einen Protokolleintrag.

Der Export von Metriken in Protokolle ist auf Ressourcenebene aktiviert. Sie können diese Protokolle aktivieren, indem Sie auf dem Blatt „Diagnoseeinstellungen“ nach „Ressourcengruppe“ filtern und die Load Balancer-Instanz auswählen, für die Sie den Metrikexport aktivieren möchten. Wenn die Load Balancer-Diagnoseeinstellungenseite angezeigt wird, wählen Sie AllMetrics aus, um geeignete Metriken als Protokolle zu exportieren.

Informationen zu Einschränkungen beim Export finden Sie im Abschnitt [Einschränkungen](#limitations) dieses Artikels.

### <a name="view-and-analyze-metrics-as-logs"></a>Anzeigen und Analysieren von Metriken als Protokolle
Wenn Sie AllMetrics in den Diagnoseeinstellungen Ihrer Load Balancer Standard-Instanz aktivieren, wird die Tabelle AzureMonitor mit diesen Protokollen aufgefüllt, wenn ein Event Hub oder ein Log Analytics-Arbeitsbereich verwendet wird. Zum Exportieren in den Speicher müssen Sie eine Verbindung mit Ihrem Speicherkonto herstellen und die JSON-Protokolleinträge für das Integritätstest- und Ereignisprotokoll abrufen. Sobald Sie die JSON-Dateien heruntergeladen haben, können Sie diese in das CSV-Format konvertieren oder in Excel, PowerBI oder einem anderen Datenvisualisierungstool anzeigen. 

> [!TIP]
> Wenn Sie mit Visual Studio und den grundlegenden Konzepten zum Ändern der Werte für Konstanten und Variablen in C# vertraut sind, können Sie die [Protokollkonvertierungstools](https://github.com/Azure-Samples/networking-dotnet-log-converter) von GitHub verwenden.

## <a name="stream-to-an-event-hub"></a>An einen Event Hub streamen
Wenn Diagnoseinformationen an einen Event Hub gestreamt werden, kann er für die zentrale Protokollanalyse in einem SIEM-Tool eines Drittanbieters mit Azure Monitor-Integration verwendet werden. Weitere Informationen finden Sie unter [Streamen von Azure-Überwachungsdaten an einen Event Hub](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>An Log Analytics senden
Ressourcen in Azure können ihre Diagnoseinformationen direkt an einen Log Analytics-Arbeitsbereich senden lassen, in dem zwecks Problembehandlung und Analyse komplexe Abfragen für die Informationen ausgeführt werden können.  Weitere Informationen finden Sie unter [Erfassen von Azure-Ressourcenprotokollen im Log Analytics-Arbeitsbereich in Azure Monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="limitations"></a>Einschränkungen
Bei der Verwendung der Funktion zum Exportieren von Metriken in Protokolle für Load Balancer gelten derzeit folgende Einschränkungen:
* Metriken werden derzeit mithilfe interner Namen angezeigt, wenn sie als Protokolle exportiert werden. Sie finden die Zuordnung in der folgenden Tabelle.
* Die Dimensionalität von Metriken wird nicht beibehalten. Beispielsweise können Sie mit Metriken wie z. B. DipAvailability (Integritätsteststatus) die IP-Adresse des Back-Ends nicht aufteilen oder anzeigen.
* Verwendete SNAT-Ports und zugeordnete SNAT-Ports sind zurzeit nicht für den Export als Protokolle verfügbar.

## <a name="next-steps"></a>Nächste Schritte
* [Load Balancer Standard-Diagnose mit Metriken, Warnungen und Ressourcenintegrität](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Protokollabfragen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* Geben Sie mithilfe der folgenden Links Feedback zu diesem Artikel oder der Load Balancer-Funktionalität.
