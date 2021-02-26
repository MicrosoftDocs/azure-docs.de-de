---
title: Überwachen von Data Factorys mit Azure Monitor
description: Erfahren Sie, wie Sie Azure Data Factory-Pipelines mit Azure Monitor überwachen können, indem Sie Diagnoseprotokolle mit Informationen aus Data Factory aktivieren.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 389c0b1fd5a2fde33c2bf19ac2807cca45691523
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373146"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Durchführen der Überwachung und Verwenden von Warnungen für Data Factory mit Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cloudanwendungen sind komplex und verfügen über zahlreiche Variablen. Monitore stellen Daten bereit, die dabei helfen, die ordnungsgemäße Ausführung der Anwendung sicherzustellen. Monitore helfen Ihnen auch, potenzielle Probleme zu vermeiden und aufgetretene Probleme zu beheben. Sie können anhand der Überwachungsdaten umfassende Erkenntnisse zu Ihren Anwendungen gewinnen. Mit diesem Wissen können Sie die Leistung und Wartbarkeit von Anwendungen verbessern. Außerdem können Sie Aktionen automatisieren, die andernfalls einen manuellen Eingriff erfordern.

Azure Monitor stellt grundlegende Metriken und Protokolle der Infrastruktur für die meisten Azure-Dienste bereit. Azure-Diagnoseprotokolle werden von einer Ressource ausgegeben und stellen umfangreiche und in kurzen Abständen erfasste Betriebsdaten der Ressource bereit. Mit Azure Data Factory (ADF) können Diagnoseprotokolle in Azure Monitor geschrieben werden. Das folgende Video enthält eine siebenminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Weitere Informationen finden Sie unter [Übersicht über Azure Monitor](../azure-monitor/overview.md).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Aufbewahren von Azure Data Factory-Metriken und -Pipelineausführungsdaten

Data Factory speichert Pipelineausführungsdaten nur 45 Tage lang. Verwenden Sie Azure Monitor, wenn Sie diese Daten für einen längeren Zeitraum aufbewahren möchten. Mit Monitor können Sie Diagnoseprotokolle zu Analysezwecken an mehrere unterschiedliche Ziele weiterleiten.

* **Storage Account** (Speicherkonto): Speichern Sie Diagnoseprotokolle zur Überwachung oder manuellen Überprüfung in einem Speicherkonto. In den Diagnoseeinstellungen können Sie eine Aufbewahrungsdauer (in Tagen) angeben.
* **Event Hub**: Streamen Sie die Protokolle in Azure Event Hubs. Die Protokolle werden zu Eingaben für einen Partnerdienst oder eine benutzerdefinierte Analyselösung wie Power BI.
* **Log Analytics**: Analysieren Sie die Protokolle mit Log Analytics. Die Data Factory-Integration in Azure Monitor ist für die folgenden Szenarien hilfreich:
  * Sie möchten komplexe Abfragen für einen umfangreichen Satz von Metriken schreiben, die von Data Factory für Monitor veröffentlicht werden. Sie können eigene Benachrichtigungen zu diesen Abfragen über Monitor erstellen.
  * Sie möchten Data Factorys übergreifend überwachen. Sie können Daten aus mehreren Data Factorys an einen einzigen Monitor-Arbeitsbereich weiterleiten.

Sie können auch ein Speicherkonto oder einen Event Hub-Namespace verwenden, das bzw. der sich nicht im Abonnement der Ressource befindet, von der Protokolle ausgegeben werden. Der Benutzer, der die Einstellung konfiguriert, benötigt entsprechenden Zugriff gemäß der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) auf beide Abonnements.

## <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurieren von Diagnoseeinstellungen und Arbeitsbereich

Erstellen Sie Diagnoseeinstellungen für Ihre Data Factory, oder fügen Sie diese hinzu.

1. Navigieren Sie im Portal zu „Monitor“. Wählen Sie **Einstellungen** > **Diagnoseeinstellungen** aus.

1. Wählen Sie die Data Factory aus, für die Sie eine Diagnoseeinstellung festlegen möchten.

1. Sollten für die ausgewählte Data Factory keine Einstellungen vorhanden sein, werden Sie aufgefordert, eine Einstellung zu erstellen. Wählen Sie **Diagnose aktivieren**.

   ![Erstellen einer Diagnoseeinstellung, wenn keine Einstellungen vorhanden sind](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Sind Einstellungen für die Data Factory vorhanden, wird eine Liste mit den Einstellungen angezeigt, die bereits für die Data Factory konfiguriert sind. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.

   ![Hinzufügen einer Diagnoseeinstellung, wenn Einstellungen vorhanden sind](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Legen Sie einen Namen für Ihre Einstellung fest, wählen Sie **An Log Analytics senden** und dann unter **Log Analytics-Arbeitsbereich** einen Arbeitsbereich aus.

    * Im Modus _Azure-Diagnose_ werden Diagnoseprotokolle in die Tabelle _AzureDiagnostics_ übernommen.

    * Im _ressourcenspezifischen_ Modus werden Diagnoseprotokolle von Azure Data Factory in die folgenden Tabellen geleitet:
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Sie können verschiedene Protokolle, die für Ihre Workloads relevant sind, für das Senden an Log Analytics-Tabellen auswählen. Falls Sie SQL Server Integration Services (SSIS) beispielsweise überhaupt nicht nutzen, müssen Sie keine SSIS-Protokolle auswählen. Wenn Sie Azure-SSIS Integration Runtime-Vorgänge (IR) starten, beenden oder warten möchten, können Sie SSIS IR-Protokolle auswählen. Wenn Sie SSIS-Paketausführungen über T-SQL in SQL Server Management Studio (SSMS), SQL Server-Agent oder anderen dafür vorgesehenen Tools aufrufen, können Sie SSIS-Paketprotokolle auswählen. Sie können alle Protokolle auswählen, wenn Sie Ausführungen von SSIS-Paketen über die entsprechenden Aktivitäten vom Typ „SSIS-Paket ausführen“ in ADF-Pipelines aufrufen.

    * Wenn Sie _AllMetrics_ auswählen, werden verschiedene ADF-Metriken zur Verfügung gestellt, die Sie überwachen oder mit Warnungen versehen können. Dies umfasst auch Metriken für Ausführungen von ADF-Aktivitäten, -Pipelines und -Triggern sowie für SSIS IR-Vorgänge und SSIS-Paketausführungen.

   ![Benennen Ihrer Einstellungen und Auswählen eines Log Analytics-Arbeitsbereichs](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Da eine Azure-Protokolltabelle nicht mehr als 500 Spalten enthalten darf, **empfehlen wir Ihnen dringend**, den _Modus „Ressourcenspezifisch“_ auszuwählen. Weitere Informationen finden Sie unter [Bekannte Einschränkung: Spaltenlimit in Azure-Diagnose](../azure-monitor/platform/resource-logs.md#column-limit-in-azurediagnostics).

1. Wählen Sie **Speichern** aus.

Nach einigen Augenblicken wird die neue Einstellung in der Liste der Einstellungen für diese Data Factory angezeigt. Diagnoseprotokolle werden in diesen Arbeitsbereich gestreamt, sobald neue Ereignisdaten generiert werden. Zwischen der Ausgabe eines Ereignisses und dessen Anzeige in Log Analytics können bis zu 15 Minuten vergehen.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Installieren der Lösung „Azure Data Factory-Analyse“ über den Azure Marketplace

Diese Lösung liefert Ihnen eine Zusammenfassung der Gesamtintegrität Ihrer Data Factory-Instanz mit Optionen zum Durchführen eines Drilldowns zu den Details und zum Beheben von Problemen mit unerwarteten Verhaltensmustern. Anhand von umfassenden Ansichten, die standardmäßig vorhanden sind, können Sie Erkenntnisse zu wichtigen Verarbeitungsschritten gewinnen, z. B.:

* Zentrale Übersicht über Pipeline-, Aktivitäts- und Triggerausführungen einer Data Factory-Instanz
* Drilldown in Data Factory-Aktivitätsausführungen nach Typ
* Zusammenfassung mit Informationen zur wichtigsten Data Factory-Pipeline und zu Aktivitätsfehlern

1. Navigieren Sie zu **Azure Marketplace**, wählen Sie den Filter **Analysen** aus, und suchen Sie nach **Azure Data Factory-Analyse (Vorschau)** .

   ![Navigieren Sie zu „Azure Marketplace“, geben Sie „Analysefilter“ ein, und wählen Sie „Azure Data Factory-Analyse (Vorschau)“ aus.](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. Details zu **Azure Data Factory-Analyse (Vorschau)**

   ![Details zu „Azure Data Factory-Analyse (Vorschau)“](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. Wählen Sie **Erstellen** aus, und erstellen Sie dann den **Log Analytics-Arbeitsbereich**.

   ![Erstellen einer neuen Lösung](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Überwachen von Data Factory-Metriken

Beim Installieren dieser Lösung wird im Arbeitsmappenabschnitt des ausgewählten Log Analytics-Arbeitsbereichs ein Standardsatz von Sichten erstellt. Dadurch werden die folgenden Metriken aktiviert:

* ADF-Ausführungen – 1) Pipelineausführungen nach Data Factory
* ADF-Ausführungen – 2) Aktivitätsausführungen nach Data Factory
* ADF-Ausführungen – 3) Triggerausführungen nach Data Factory
* ADF-Fehler – 1) Top 10-Pipelinefehler nach Data Factory
* ADF-Fehler – 2) Top 10-Aktivitätsausführungen nach Data Factory
* ADF-Fehler – 3) Top 10-Triggerfehler nach Data Factory
* ADF-Statistik – 1) Aktivitätsausführungen nach Typ
* ADF-Statistik – 2) Triggerausführungen nach Typ
* ADF-Statistik – 3) Höchstdauer der Pipelineausführungen

![Fenster mit den hervorgehobenen Optionen „Arbeitsmappen (Vorschau)“ und „AzureDataFactoryAnalytics“](media/data-factory-monitor-oms/monitor-oms-image6.png)

Sie können u.a. die voranstehenden Metriken visualisieren, die Abfragen hinter diesen Metriken betrachten, die Abfragen bearbeiten Benachrichtigungen erstellen oder andere Aktionen ausführen.

![Grafische Darstellung von Pipelineausführungen durch Data Factory](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (Vorschau) sendet Diagnoseprotokolle an _ressourcenspezifische_ Zieltabellen. Sie können Abfragen für die folgenden Tabellen schreiben: _ADFPipelineRun_, _ADFTriggerRun_ und _ADFActivityRun_.

## <a name="data-factory-metrics"></a>Data Factory-Metriken

Mit Monitor erhalten Sie Einblicke in die Leistung und Integrität ihrer Azure-Workloads. Metriken sind die wichtigsten Monitor-Datentypen. Sie werden auch als Leistungsindikatoren bezeichnet. Metriken werden von den meisten Azure-Ressourcen ausgegeben. Azure Monitor bietet Ihnen verschiedene Möglichkeiten, diese Metriken für die Überwachung und Problembehandlung zu konfigurieren und zu nutzen.

Hier sind einige Metriken aufgeführt, die von Azure Data Factory Version 2 ausgegeben werden:

| **Metrik**                           | **Anzeigename der Metrik**                  | **Einheit** | **Aggregationstyp** | **Beschreibung**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Metriken zu abgebrochenen Aktivitätsausführungen           | Anzahl    | Gesamt                | Die Gesamtanzahl von Aktivitätsausführungen, die innerhalb eines Minutenfensters abgebrochen wurden. |
| ActivityFailedRuns                   | Metriken zu fehlerhaften Aktivitätsausführungen             | Anzahl    | Gesamt                | Die Gesamtanzahl der fehlgeschlagenen Aktivitätsausführungen innerhalb eines Minutenfensters. |
| ActivitySucceededRuns                | Metriken zu erfolgreichen Aktivitätsausführungen          | Anzahl    | Gesamt                | Die Gesamtanzahl der erfolgreichen Aktivitätsausführungen innerhalb eines Minutenfensters. |
| PipelineCancelledRuns                 | Metriken zu abgebrochenen Pipelineausführungen           | Anzahl    | Gesamt                | Die Gesamtanzahl von Pipelineausführungen, die innerhalb eines Minutenfensters abgebrochen wurden. |
| PipelineFailedRuns                   | Metriken zu fehlerhaften Pipelineausführungen             | Anzahl    | Gesamt                | Die Gesamtanzahl der fehlgeschlagenen Pipelineausführungen innerhalb eines Minutenfensters. |
| PipelineSucceededRuns                | Metriken zu erfolgreichen Pipelineausführungen          | Anzahl    | Gesamt                | Die Gesamtanzahl der erfolgreichen Pipelineausführungen innerhalb eines Minutenfensters. |
| TriggerCancelledRuns                  | Metriken zu abgebrochenen Triggerausführungen            | Anzahl    | Gesamt                | Die Gesamtanzahl von Triggerausführungen, die innerhalb eines Minutenfensters abgebrochen wurden. |
| TriggerFailedRuns                    | Metriken zu fehlerhaften Triggerausführungen              | Anzahl    | Gesamt                | Die Gesamtanzahl der fehlgeschlagenen Triggerausführungen innerhalb eines Minutenfensters. |
| TriggerSucceededRuns                 | Metriken zu erfolgreichen Triggerausführungen           | Anzahl    | Gesamt                | Die Gesamtanzahl der erfolgreichen Triggerausführungen innerhalb eines Minutenfensters. |
| SSISIntegrationRuntimeStartCancelled  | Metriken zu abgebrochenen SSIS-Integration Runtime-Startvorgängen           | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Integration Runtime-Startvorgängen, die innerhalb eines Fensters von einer Minute abgebrochen wurden. |
| SSISIntegrationRuntimeStartFailed    | Metriken zu fehlerhaften SSIS-Integration Runtime-Startvorgängen             | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Integration Runtime-Startvorgängen, die innerhalb eines Fensters von einer Minute zu einem Fehler führten. |
| SSISIntegrationRuntimeStartSucceeded | Metriken zu erfolgreichen SSIS-Integration Runtime-Startvorgängen          | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Integration Runtime-Startvorgängen, die innerhalb eines Fensters von einer Minute erfolgreich abgeschlossen wurden. |
| SSISIntegrationRuntimeStopStuck      | Metriken zu unterbrochenen SSIS-Integration Runtime-Beendigungsvorgängen               | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Integration Runtime-Beendigungsvorgängen, die innerhalb eines Fensters von einer Minute unterbrochen wurden. |
| SSISIntegrationRuntimeStopSucceeded  | Metriken zu erfolgreichen SSIS-Integration Runtime-Beendigungsvorgängen           | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Integration Runtime-Beendigungsvorgängen, die innerhalb eines Fensters von einer Minute erfolgreich abgeschlossen wurden. |
| SSISPackageExecutionCancelled         | Metriken zu abgebrochenen SSIS-Paketausführungen  | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Paketausführungen, die innerhalb eines Minutenfensters abgebrochen wurden. |
| SSISPackageExecutionFailed           | Metriken zu fehlgeschlagenen SSIS-Paketausführungen    | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Paketausführungen, die innerhalb eines Minutenfensters fehlgeschlagen sind. |
| SSISPackageExecutionSucceeded        | Metriken zu erfolgreichen SSIS-Paketausführungen | Anzahl    | Gesamt                | Die Gesamtanzahl von SSIS-Paketausführungen, die innerhalb eines Minutenfensters erfolgreich waren. |

Folgen Sie den Anweisungen unter [Azure Monitor-Datenplattform](../azure-monitor/platform/data-platform.md), um auf die Metriken zuzugreifen.

> [!NOTE]
> Es werden nur Ereignisse für abgeschlossene ausgelöste Aktivitäts- und Pipelineausführungen ausgegeben. Aktive Ausführungen und Debugausführungen werden **nicht** ausgegeben. Andererseits werden unabhängig von den Aufrufmethoden Ereignisse für **alle** SSIS-Paketausführungen ausgegeben, einschließlich abgeschlossener und in Bearbeitung befindlicher. Beispielsweise können Sie Paketausführungen in den für Azure aktivierten SQL Server Data Tools (SSDT), über T-SQL für SSMS, SQL Server-Agent oder andere festgelegte Tools und als ausgelöste oder Debugausführungen von SSIS-Paketausführungsaktivitäten in ADF-Pipelines aufrufen.

## <a name="data-factory-alerts"></a>Data Factory-Warnungen

Melden Sie sich beim Azure-Portal an, und wählen Sie **Überwachen** > **Warnungen** aus, um Warnungen zu erstellen.

![Warnungen im Portalmenü](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Erstellen von Warnungen

1. Wählen Sie **+ Neue Warnungsregel** aus, um eine neue Warnung zu erstellen.

    ![Neue Warnungsregel](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definieren Sie die Warnungsbedingung.

    > [!NOTE]
    > Achten Sie darauf, dass in der Dropdownliste **Nach Ressourcentyp filtern** die Option **Alle** ausgewählt ist.

    ![„Warnungsbedingung definieren“ > „Ziel auswählen“ öffnet den Bereich „Ressource auswählen“ ](media/monitor-using-azure-monitor/alerts_image5.png)

    ![„Warnungsbedingung definieren“ > „Kriterien hinzufügen“ öffnet den Bereich „Signallogik konfigurieren“](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Bereich „Signallogik konfigurieren“](media/monitor-using-azure-monitor/alerts_image7.png)

1. Definieren Sie die Warnungsdetails.

    ![Warnungsdetails](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definieren Sie die Aktionsgruppe.

    ![Erstellen einer Regel, mit hervorgehobener Option „Neue Aktionsgruppe“](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Erstellen einer neuen Aktionsgruppe](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Konfigurieren von E-Mail, SMS, Push und Voice](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definieren einer Aktionsgruppe](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Einrichten von Diagnoseprotokollen über die Azure Monitor-REST-API

### <a name="diagnostic-settings"></a>Diagnoseeinstellungen

Verwenden Sie Diagnoseeinstellungen, um Diagnoseprotokolle für Nicht-Computeressourcen zu konfigurieren. Die Einstellungen für eine Ressourcensteuerung verfügen über die folgenden Features:

* Sie geben an, wohin Diagnoseprotokolle gesendet werden, z. B. an ein Azure Storage-Konto, an einen Azure Event Hub oder an Monitor-Protokolle.
* Sie geben an, welche Protokollkategorien gesendet werden.
* Sie geben an, wie lange die einzelnen Protokollkategorien in einem Speicherkonto aufbewahrt werden.
* Wenn für die Beibehaltungsdauer 0 Tage festgelegt sind, bedeutet dies, dass Protokolle unbegrenzt beibehalten werden. Andernfalls kann als Wert die Anzahl von Tagen (1 bis 2.147.483.647) festgelegt werden.
* Wenn Aufbewahrungsrichtlinien festgelegt werden, aber das Speichern von Protokollen in einem Speicherkonto deaktiviert ist, werden die Aufbewahrungsrichtlinien ignoriert. Diese Bedingung kann z.B. auftreten, wenn nur die Optionen „Event Hubs“ oder „Überwachungsprotokolle“ ausgewählt sind.
* Aufbewahrungsrichtlinien werden pro Tag angewendet. Die Grenze zwischen Tagen liegt um Mitternacht (Koordinierte Weltzeit, UTC). Jeweils am Ende eines Tages werden die Protokolle der Tage gelöscht, deren Aufbewahrungsdauer abgelaufen ist. Beispiel: Wenn Sie eine Aufbewahrungsrichtlinie für einen Tag verwenden, werden heute am Anfang des Tages die Protokolle von vorgestern gelöscht.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Aktivieren von Diagnoseprotokollen über die Azure Monitor-REST-API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Erstellen oder Aktualisieren einer Diagnoseeinstellung in der REST-API von Azure Monitor

##### <a name="request"></a>Anforderung

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Header

* Ersetzen Sie `{api-version}` durch `2016-09-01`.
* Ersetzen Sie `{resource-id}` durch die ID der Ressource, deren Diagnoseeinstellungen Sie bearbeiten möchten. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Legen Sie den Header `Content-Type` auf `application/json` fest.
* Legen Sie den Autorisierungsheader auf ein JSON-Webtoken fest, das Sie aus Azure Active Directory (Azure AD) abgerufen haben. Weitere Informationen finden Sie unter [Authentifizieren von Anforderungen](../active-directory/develop/authentication-vs-authorization.md).

##### <a name="body"></a>Body

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Eigenschaft | Typ | BESCHREIBUNG |
| --- | --- | --- |
| **storageAccountId** |String | Die Ressourcen-ID des Speicherkontos, an das Diagnoseprotokolle gesendet werden sollen. |
| **serviceBusRuleId** |String | Die Service Bus-Regel-ID des Service Bus-Namespace, in dem Event Hubs für das Streaming von Diagnoseprotokollen erstellt werden sollen. Die Regel-ID weist das Format `{service bus resource ID}/authorizationrules/{key name}` auf.|
| **workspaceId** | Komplexer Typ | Ein Array metrischer Zeiteinheiten und ihrer Aufbewahrungsrichtlinien. Der Wert dieser Eigenschaft ist leer. |
|**metrics**| Parameterwerte der Pipelineausführung, die an die aufgerufene Pipeline übergeben werden sollen| Ein JSON-Objekt, das Parameternamen Argumentwerten zuordnet. |
| **logs**| Komplexer Typ| Der Name einer Diagnoseprotokollkategorie für einen Ressourcentyp. Um die Liste der Diagnoseprotokollkategorien für eine Ressource zu erhalten, führen Sie einen GET-Vorgang zum Abrufen von Diagnoseeinstellungen aus. |
| **category**| String| Ein Array von Protokollkategorien und ihrer Aufbewahrungsrichtlinien. |
| **timeGrain** | String | Die Granularität von Metriken, die im Zeitformat ISO 8601 erfasst werden. Der Eigenschaftswert muss `PT1M` sein, was eine Minute angibt. |
| **enabled**| Boolean | Gibt an, ob die Sammlung der Metrik- oder Protokollkategorie für diese Ressource aktiviert ist. |
| **retentionPolicy**| Komplexer Typ| Beschreibt die Aufbewahrungsrichtlinie für eine Metrik- oder Protokollkategorie. Diese Eigenschaft wird nur für Speicherkonten verwendet. |
|**days**| Int| Die Anzahl der Tage, die Metriken oder Protokolle aufbewahrt werden sollen. Wenn der Eigenschaftswert „0“ ist, werden die Protokolle dauerhaft aufbewahrt. Diese Eigenschaft wird nur für Speicherkonten verwendet. |

##### <a name="response"></a>Antwort

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Abrufen von Informationen zu Diagnoseeinstellungen in der Monitor-REST-API

##### <a name="request"></a>Anforderung

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Header

* Ersetzen Sie `{api-version}` durch `2016-09-01`.
* Ersetzen Sie `{resource-id}` durch die ID der Ressource, deren Diagnoseeinstellungen Sie bearbeiten möchten. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Legen Sie den Header `Content-Type` auf `application/json` fest.
* Legen Sie den Autorisierungsheader auf ein JSON-Webtoken fest, das Sie aus Azure AD abgerufen haben. Weitere Informationen finden Sie unter [Authentifizieren von Anforderungen](../active-directory/develop/authentication-vs-authorization.md).

##### <a name="response"></a>Antwort

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Weitere Informationen finden Sie unter [Diagnoseeinstellungen](/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schema von Protokollen und Ereignissen

### <a name="monitor-schema"></a>Monitor-Schema

#### <a name="activity-run-log-attributes"></a>Attribute des Aktivitätsausführungsprotokolls

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Eigenschaft | Typ | Beschreibung | Beispiel |
| --- | --- | --- | --- |
| **Level** |String | Die Ebene der Diagnoseprotokolle. Legen Sie für Aktivitätsausführungsprotokolle den Eigenschaftswert auf „4“ fest. | `4` |
| **correlationId** |String | Die eindeutige ID für die Nachverfolgung einer bestimmten Anforderung. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Die Zeitpunkt des Ereignisses im TimeSpan-UTC-Format `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| Die ID der Aktivitätsausführung. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| Die ID der Pipelineausführung. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Ressourcen-ID**| String | Die mit der Data Factory-Ressource verknüpfte ID. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Die Kategorie der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `ActivityRuns` fest. | `ActivityRuns` |
|**level**| String | Die Ebene der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `Informational` fest. | `Informational` |
|**operationName**| String | Der Name der Aktivität zusammen mit ihrem Status. Wenn die Aktivität der Startheartbeat ist, lautet der Eigenschaftswert `MyActivity -`. Wenn die Aktivität der Endheartbeat ist, lautet der Eigenschaftswert `MyActivity - Succeeded`. | `MyActivity - Succeeded` |
|**pipelineName**| String | Der Name der Pipeline. | `MyPipeline` |
|**activityName**| String | Der Name der Aktivität. | `MyActivity` |
|**start**| String | Die Startzeit der Aktivitätsausführungen im TimeSpan-UTC-Format. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | Die Endzeit der Aktivitätsausführungen im TimeSpan-UTC-Format. Wenn das Diagnoseprotokoll anzeigt, dass eine Aktivität gestartet, aber noch nicht beendet wurde, lautet der Eigenschaftswert `1601-01-01T00:00:00Z`. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Attribute des Pipelineausführungsprotokolls

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Eigenschaft | Typ | BESCHREIBUNG | Beispiel |
| --- | --- | --- | --- |
| **Level** |String | Die Ebene der Diagnoseprotokolle. Legen Sie für Aktivitätsausführungsprotokolle den Eigenschaftswert auf „4“ fest. | `4` |
| **correlationId** |String | Die eindeutige ID für die Nachverfolgung einer bestimmten Anforderung. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Die Zeitpunkt des Ereignisses im TimeSpan-UTC-Format `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| Die ID der Pipelineausführung. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Ressourcen-ID**| String | Die mit der Data Factory-Ressource verknüpfte ID. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Die Kategorie der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `PipelineRuns` fest. | `PipelineRuns` |
|**level**| String | Die Ebene der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `Informational` fest. | `Informational` |
|**operationName**| String | Der Name der Pipeline zusammen mit ihrem Status. Nachdem die Pipelineausführung abgeschlossen ist, lautet der Eigenschaftswert `Pipeline - Succeeded`. | `MyPipeline - Succeeded`. |
|**pipelineName**| String | Der Name der Pipeline. | `MyPipeline` |
|**start**| String | Die Startzeit der Aktivitätsausführungen im TimeSpan-UTC-Format. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| String | Die Endzeit der Aktivitätsausführungen im TimeSpan-UTC-Format. Wenn das Diagnoseprotokoll anzeigt, dass eine Aktivität gestartet, aber noch nicht beendet wurde, lautet der Eigenschaftswert `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | Der abschließende Status der Pipelineausführung. Mögliche Eigenschaftswerte sind `Succeeded` und `Failed`. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Attribute des Triggerausführungsprotokolls

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Eigenschaft | Typ | BESCHREIBUNG | Beispiel |
| --- | --- | --- | --- |
| **Level** |String | Die Ebene der Diagnoseprotokolle. Legen Sie für Aktivitätsausführungsprotokolle den Eigenschaftswert auf „4“ fest. | `4` |
| **correlationId** |String | Die eindeutige ID für die Nachverfolgung einer bestimmten Anforderung. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Die Zeitpunkt des Ereignisses im TimeSpan-UTC-Format `YYYY-MM-DDTHH:MM:SS.00000Z`. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| Die ID der Triggerausführung. | `08587023010602533858661257311` |
|**Ressourcen-ID**| String | Die mit der Data Factory-Ressource verknüpfte ID. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | Die Kategorie der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `PipelineRuns` fest. | `PipelineRuns` |
|**level**| String | Die Ebene der Diagnoseprotokolle. Legen Sie den Eigenschaftswert auf `Informational` fest. | `Informational` |
|**operationName**| String | Der Name des Triggers mit seinem abschließenden Status, der angibt, ob der Trigger erfolgreich ausgelöst wurde. Wenn der Heartbeat erfolgreich war, lautet der Eigenschaftswert `MyTrigger - Succeeded`. | `MyTrigger - Succeeded` |
|**triggerName**| String | Der Name des Triggers. | `MyTrigger` |
|**triggerType**| String | Der Typ des Triggers. Mögliche Eigenschaftswerte sind `Manual Trigger` und `Schedule Trigger`. | `ScheduleTrigger` |
|**triggerEvent**| String | Das Ereignis des Triggers. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | Die Startzeit der Triggerauslösung im TimeSpan-UTC-Format. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | Der abschließende Status, der anzeigt, ob der Trigger erfolgreich ausgelöst wurde. Mögliche Eigenschaftswerte sind `Succeeded` und `Failed`. | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>Protokollattribute der SSIS Integration Runtime

Nachstehend finden Sie die Protokollattribute der Start-/Beendigungs-/Wartungsvorgänge für SSIS IR.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Eigenschaft                   | Typ   | BESCHREIBUNG                                                   | Beispiel                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Name Ihres SSIS IR-Vorgangs                            | `Start/Stop/Maintenance` |
| **category**               | String | Kategorie der Diagnoseprotokolle                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | Name Ihrer ADF-Instanz                                          | `MyADFv2` |
| **integrationRuntimeName** | String | Name Ihrer SSIS IR                                      | `MySSISIR` |
| **level**                  | String | Ebene mit Diagnoseprotokollen                                  | `Informational` |
| **resultType**             | String | Ergebnis Ihres SSIS IR-Vorgangs                          | `Started/InProgress/Succeeded/Failed` |
| **Nachricht**                | String | Ausgabemeldung Ihres SSIS IR-Vorgangs                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **Ressourcen-ID**             | String | Eindeutige ID Ihrer ADF-Ressource                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>Protokollattribute des Meldungskontexts von SSIS-Ereignissen

Nachstehend finden Sie die Protokollattribute von Bedingungen der Ereignismeldungen, die von SSIS-Paketausführungen Ihrer SSIS IR generiert werden. Sie liefern ähnliche Informationen wie [Tabellen oder Sichten zum Kontext von Ereignismeldungen im SSIS-Katalog (SSISDB)](/sql/integration-services/system-views/catalog-event-message-context), in denen Runtimewerte für viele SSIS-Paketeigenschaften angezeigt werden. Sie werden generiert, wenn Sie den Protokolliergrad `Basic/Verbose` auswählen, und sind hilfreich beim Debuggen und Überprüfen der Konformität.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Eigenschaft                   | Typ   | BESCHREIBUNG                                                          | Beispiel                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Ist auf `YourSSISIRName-SSISPackageEventMessageContext` festgelegt.       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | String | Kategorie der Diagnoseprotokolle                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Name Ihrer ADF-Instanz                                                 | `MyADFv2` |
| **integrationRuntimeName** | String | Name Ihrer SSIS IR                                             | `MySSISIR` |
| **level**                  | String | Ebene mit Diagnoseprotokollen                                         | `Informational` |
| **operationId**            | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs in der SSISDB          | `1` (1 steht für Vorgänge von Paketen, die **nicht** in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden.) |
| **contextDepth**           | String | Tiefe Ihres Kontexts von Ereignismeldungen                              | `0` (0 steht für den Kontext vor dem Start der Paketausführung, und 1 steht für den Kontext beim Auftreten eines Fehlers. Dieser Wert wird erhöht, je weiter der Kontext vom Fehler entfernt ist.) |
| **packagePath**            | String | Pfad des Paketobjekts als Quelle des Kontexts Ihrer Ereignismeldung      | `\Package` |
| **contextType**            | String | Typ des Paketobjekts als Quelle des Kontexts Ihrer Ereignismeldung      | `60` ([weitere Kontexttypen](/sql/integration-services/system-views/catalog-event-message-context#remarks)) |
| **contextSourceName**      | String | Name des Paketobjekts als Quelle des Kontexts Ihrer Ereignismeldung      | `MyPackage` |
| **contextSourceId**        | String | Eindeutige ID des Paketobjekts als Quelle des Kontexts Ihrer Ereignismeldung | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | String | Name der Paketeigenschaft für die Quelle des Kontexts Ihrer Ereignismeldung   | `DelayValidation` |
| **propertyValue**          | String | Wert der Paketeigenschaft für die Quelle des Kontexts Ihrer Ereignismeldung  | `False` |
| **Ressourcen-ID**             | String | Eindeutige ID Ihrer ADF-Ressource                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>Protokollattribute von SSIS-Ereignismeldungen

Nachstehend finden Sie die Protokollattribute von Ereignismeldungen, die von SSIS-Paketausführungen Ihrer SSIS IR generiert werden. Sie liefern ähnliche Informationen wie [Tabellen oder Sichten für SSISDB-Ereignismeldungen](/sql/integration-services/system-views/catalog-event-messages), in denen die ausführlichen Text- bzw. Metadaten von Ereignismeldungen angezeigt werden. Sie werden für alle Protokolliergrade mit Ausnahme von `None` generiert.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Eigenschaft                   | Typ   | BESCHREIBUNG                                                        | Beispiel                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Ist auf `YourSSISIRName-SSISPackageEventMessages` festgelegt.           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | String | Kategorie der Diagnoseprotokolle                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Name Ihrer ADF-Instanz                                               | `MyADFv2` |
| **integrationRuntimeName** | String | Name Ihrer SSIS IR                                           | `MySSISIR` |
| **level**                  | String | Ebene mit Diagnoseprotokollen                                       | `Informational` |
| **operationId**            | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs in der SSISDB        | `1` (1 steht für Vorgänge von Paketen, die **nicht** in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden.) |
| **messageTime**            | String | Zeitpunkt, zum dem Ihre Ereignismeldung im UTC-Format erstellt wird          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | String | Typ Ihrer Ereignismeldung                                     | `70` ([weitere Meldungstypen](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)) |
| **messageSourceType**      | String | Typ Ihrer Quelle für Ereignismeldungen                              | `20` ([weitere Typen von Meldungsquellen](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)) |
| **Nachricht**                | String | Text Ihrer Ereignismeldung                                     | `MyPackage:Validation has started.` |
| **packageName**            | String | Name Ihrer ausgeführten Paketdatei                             | `MyPackage.dtsx` |
| **eventName**              | String | Name des zugehörigen Runtimeereignisses                                 | `OnPreValidate` |
| **messageSourceName**      | String | Name der Paketkomponente als Ihre Quelle für Ereignismeldungen         | `Data Flow Task` |
| **messageSourceId**        | String | Eindeutige ID der Paketkomponente als Ihre Quelle für Ereignismeldungen    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | String | Name der Datenflusskomponente als Ihre Quelle für Ereignismeldungen       | `SSIS.Pipeline` |
| **packagePath**            | String | Pfad des Paketobjekts als Ihre Quelle für Ereignismeldungen            | `\Package\Data Flow Task` |
| **executionPath**          | String | Vollständiger Pfad vom übergeordneten Paket zur ausgeführten Komponente            | `\Transformation\Data Flow Task` (Über diesen Pfad werden auch Komponenteniterationen erfasst.) |
| **threadId**               | String | Eindeutige ID des Threads, der bei der Ausführung Ihrer Ereignismeldung protokolliert wird | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>Protokollattribute der Statistik zu ausführbaren SSIS-Dateien

Nachstehend finden Sie die Protokollattribute der Statistiken von ausführbaren Dateien, die über SSIS-Paketausführungen Ihrer SSIS IR generiert werden. Hierbei sind ausführbare Dateien Container oder Tasks in Paketablaufsteuerungen. Sie liefern ähnliche Informationen wie [Tabellen oder Sichten für Statistiken zu ausführbaren SSISDB-Dateien](/sql/integration-services/system-views/catalog-executable-statistics), in denen eine Zeile für jede aktive ausführbare Datei angezeigt wird, einschließlich Iterationen. Sie werden bei allen Protokolliergraden mit Ausnahme von `None` generiert und sind nützlich zum Identifizieren von Engpässen bzw. Fehlern auf Taskebene.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Eigenschaft                   | Typ   | BESCHREIBUNG                                                      | Beispiel                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Ist auf `YourSSISIRName-SSISPackageExecutableStatistics` festgelegt.  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | String | Kategorie der Diagnoseprotokolle                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Name Ihrer ADF-Instanz                                             | `MyADFv2` |
| **integrationRuntimeName** | String | Name Ihrer SSIS IR                                         | `MySSISIR` |
| **level**                  | String | Ebene mit Diagnoseprotokollen                                     | `Informational` |
| **executionId**            | String | Eindeutige ID für die Nachverfolgung einer bestimmten Ausführung in der SSISDB      | `1` (1 steht für Ausführungen von Paketen, die **nicht** in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden.) |
| **executionPath**          | String | Vollständiger Pfad vom übergeordneten Paket zur ausgeführten Komponente          | `\Transformation\Data Flow Task` (Über diesen Pfad werden auch Komponenteniterationen erfasst.) |
| **startTime**              | String | Zeitpunkt im UTC-Format, zu dem die ausführbare Datei in die Phase vor der Ausführung eintritt  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | Zeitpunkt im UTC-Format, zu dem die ausführbare Datei in die Phase nach der Ausführung eintritt | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | String | Ausführungszeit der ausführbaren Datei in Millisekunden                   | `1,125` |
| **executionResult**        | String | Ergebnis der Ausführung der ausführbaren Datei                                 | `0` (0 steht für „Erfolg“, 1 für „Fehler“, 2 für „Abschluss“ und 3 für „Abbruch“) |
| **executionValue**         | String | Von der aktiven ausführbaren Datei zurückgegebener benutzerdefinierter Wert            | `1` |
| **Ressourcen-ID**             | String | Eindeutige ID Ihrer ADF-Ressource                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>Protokollattribute der Komponentenphasen von SSIS-Ausführungen

Nachstehend finden Sie die Protokollattribute von Runtimestatistiken für Datenflusskomponenten, die von SSIS-Paketausführungen Ihrer SSIS IR generiert werden. Sie liefern ähnliche Informationen wie [Tabellen und Sichten der Komponentenphasen von SSISDB-Ausführungen](/sql/integration-services/system-views/catalog-execution-component-phases), in denen die Zeit angezeigt wird, die die Datenflusskomponenten in den einzelnen Ausführungsphasen verbringen. Sie werden generiert, wenn Sie den Protokolliergrad `Performance/Verbose` auswählen, und sind hilfreich zum Erfassen von Statistiken zur Datenflussausführung.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Eigenschaft                   | Typ   | BESCHREIBUNG                                                         | Beispiel                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | String | Ist auf `YourSSISIRName-SSISPackageExecutionComponentPhases` festgelegt. | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | String | Kategorie der Diagnoseprotokolle                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | Name Ihrer ADF-Instanz                                                | `MyADFv2` |
| **integrationRuntimeName** | String | Name Ihrer SSIS IR                                            | `MySSISIR` |
| **level**                  | String | Ebene mit Diagnoseprotokollen                                        | `Informational` |
| **executionId**            | String | Eindeutige ID für die Nachverfolgung einer bestimmten Ausführung in der SSISDB         | `1` (1 steht für Ausführungen von Paketen, die **nicht** in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden.) |
| **packageName**            | String | Name Ihrer ausgeführten Paketdatei                              | `MyPackage.dtsx` |
| **taskName**               | String | Name des ausgeführten Datenflusstasks                                 | `Data Flow Task` |
| **subcomponentName**       | String | Name der Datenflusskomponente                                     | `Derived Column` |
| **phase**                  | String | Name der Ausführungsphase                                         | `AcquireConnections` |
| **startTime**              | String | Zeitpunkt im UTC-Format, zu dem die Ausführungsphase gestartet wird                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | Zeitpunkt im UTC-Format, zu dem die Ausführungsphase beendet wird                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | String | Ausführungspfad für den Datenflusstask                            | `\Transformation\Data Flow Task` |
| **Ressourcen-ID**             | String | Eindeutige ID Ihrer ADF-Ressource                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>Protokollattribute für Statistiken von SSIS-Ausführungsdaten

Nachstehend finden Sie die Protokollattribute von Datenverschiebungen durch die einzelnen Abschnitte von Datenflusspipelines (von Upstream- zu Downstream-Komponenten), die von SSIS-Paketausführungen Ihrer SSIS IR generiert werden. Sie liefern ähnliche Informationen wie [Tabellen und Sichten mit Statistiken für SSISDB-Ausführungsdaten](/sql/integration-services/system-views/catalog-execution-data-statistics), in denen die Zeilenanzahl für Daten angezeigt wird, die Datenflusstasks durchlaufen. Sie werden generiert, wenn Sie den Protokolliergrad `Verbose` auswählen, und sind hilfreich zum Berechnen des Datenflussdurchsatzes.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Eigenschaft                     | Typ   | BESCHREIBUNG                                                        | Beispiel                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | String | Ereigniszeitpunkt im UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | String | Ist auf `YourSSISIRName-SSISPackageExecutionDataStatistics` festgelegt. | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | String | Kategorie der Diagnoseprotokolle                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | Eindeutige ID für die Nachverfolgung eines bestimmten Vorgangs                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | Name Ihrer ADF-Instanz                                               | `MyADFv2` |
| **integrationRuntimeName**   | String | Name Ihrer SSIS IR                                           | `MySSISIR` |
| **level**                    | String | Ebene mit Diagnoseprotokollen                                       | `Informational` |
| **executionId**              | String | Eindeutige ID für die Nachverfolgung einer bestimmten Ausführung in der SSISDB        | `1` (1 steht für Ausführungen von Paketen, die **nicht** in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden.) |
| **packageName**              | String | Name Ihrer ausgeführten Paketdatei                             | `MyPackage.dtsx` |
| **taskName**                 | String | Name des ausgeführten Datenflusstasks                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | Eindeutige ID für die Nachverfolgung des Datenflusspfads                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | String | Name des Datenflusspfads                                         | `ADO NET Source Output` |
| **sourceComponentName**      | String | Name der Datenflusskomponente, die Daten sendet                    | `SQLDB Table3` |
| **destinationComponentName** | String | Name der Datenflusskomponente, die Daten empfängt                 | `Derived Column` |
| **rowsSent**                 | String | Anzahl von Zeilen, die von der Quellkomponente gesendet wurden                        | `500` |
| **createdTime**              | String | Zeitpunkt im UTC-Format, zu dem Zeilenwerte abgerufen werden                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | String | Ausführungspfad für den Datenflusstask                           | `\Transformation\Data Flow Task` |
| **Ressourcen-ID**               | String | Eindeutige ID Ihrer ADF-Ressource                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics-Schema

Log Analytics erbt das Schema von Monitor. Dabei gelten jedoch folgende Ausnahmen:

* Der erste Buchstabe in den einzelnen Spaltennamen wird in Großbuchstaben geschrieben. Der Spaltenname „correlationId“ in Monitor lautet in Log Analytics beispielsweise „CorrelationId“.
* Es gibt keine Spalte „Level“.
* Die dynamische Spalte „Eigenschaften“ wird als der folgende dynamische JSON-Blob-Typ beibehalten:

    | Azure Monitor-Spalte | Log Analytics-Spalte | type |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | Dynamisch |
    | $.properties.Annotations | Anmerkungen | Dynamisch |
    | $.properties.Input | Eingabe | Dynamisch |
    | $.properties.Output | Output | Dynamisch |
    | $.properties.Error.errorCode | ErrorCode | INT |
    | $.properties.Error.message | ErrorMessage | Zeichenfolge |
    | $.properties.Error | Fehler | Dynamisch |
    | $.properties.Predecessors | Predecessors | Dynamisch |
    | $.properties.Parameters | Parameter | Dynamisch |
    | $.properties.SystemParameters | SystemParameters | Dynamisch |
    | $.properties.Tags | `Tags` | Dynamisch |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Überwachen von SSIS-Vorgängen mit Azure Monitor

Um Ihre SSIS-Workloads per Lift & Shift zu verschieben, können Sie [die SSIS IR in ADF bereitstellen](./tutorial-deploy-ssis-packages-azure.md) und somit Folgendes unterstützen:

- Ausführen von Paketen, die im SSIS-Katalog (SSISDB) bereitgestellt werden, wobei zum Hosten ein Azure SQL-Datenbank-Server/eine verwaltete Instanz verwendet wird (Projektbereitstellungsmodell)
- Ausführen von Paketen, die im Dateisystem, in Azure Files oder in SQL Server-Datenbank (MSDB) bereitgestellt werden, wobei zum Hosten Azure SQL Managed Instance verwendet wird (Paketbereitstellungsmodell)

Nach der Bereitstellung können Sie den [SSIS IR-Betriebsstatus mit Azure PowerShell oder im **Monitor**-Hub des ADF-Portals überprüfen](./monitor-integration-runtime.md#azure-ssis-integration-runtime). Beim Projektbereitstellungsmodell werden Protokolle für SSIS-Paketausführungen in internen SSISDB-Tabellen oder -Sichten gespeichert, damit sie mit geeigneten Tools wie SSMS abgefragt, analysiert und visuell dargestellt werden können. Beim Paketbereitstellungsmodell können Protokolle für SSIS-Paketausführungen im Dateisystem oder in Azure Files als CSV-Dateien gespeichert werden, die noch mit anderen geeigneten Tools analysiert und verarbeitet werden müssen, bevor sie abgefragt, analysiert und visuell dargestellt werden können.

Mit der [Azure Monitor](../azure-monitor/platform/data-platform.md)-Integration können nun alle Metriken und Protokolle, die bei SSIS IR-Vorgängen und SSIS-Paketausführungen generiert werden, über das Azure-Portal abgefragt, analysiert und visuell dargestellt werden. Darüber hinaus können Sie auch Warnungen für sie ausgeben.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Konfigurieren von Diagnoseeinstellungen und des Arbeitsbereichs für SSIS-Vorgänge

Um alle Metriken und Protokolle an Azure Monitor zu senden, die bei SSIS IR-Vorgängen und SSIS-Paketausführungen generiert werden, müssen Sie [Diagnoseeinstellungen und einen Arbeitsbereich für ADF konfigurieren](#configure-diagnostic-settings-and-workspace).

### <a name="ssis-operational-metrics"></a>Metriken zum SSIS-Betrieb

[Metriken](../azure-monitor/platform/data-platform-metrics.md) zum SSIS-Betrieb sind Leistungsindikatoren oder numerische Werte, die den Status von SSIS IR-Start- und -Beendigungsvorgängen sowie SSIS-Paketausführungen zu einem bestimmten Zeitpunkt beschreiben. Sie sind Teil der [ADF-Metriken in Azure Monitor](#data-factory-metrics).

Beim Konfigurieren der Diagnoseeinstellungen und des Arbeitsbereichs für Ihre ADF-Instanz unter Azure Monitor können Sie das Kontrollkästchen _AllMetrics_ aktivieren, um die Metriken zum SSIS-Betrieb für die [interaktive Analyse per Azure-Metrik-Explorer](../azure-monitor/platform/metrics-getting-started.md), die [Darstellung in Azure-Dashboards](../azure-monitor/learn/tutorial-app-dashboards.md) und [Benachrichtigungen nahezu in Echtzeit](../azure-monitor/platform/alerts-metric.md) verfügbar zu machen.

![Benennen Ihrer Einstellungen und Auswählen eines Log Analytics-Arbeitsbereichs](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>Warnungen zum SSIS-Betrieb

[Wählen Sie die Seite **Warnungen und Metriken** im **Monitor**-Hub von ADF aus, und befolgen Sie die angegebene Schritt-für-Schritt-Anleitung](./monitor-visually.md#alerts), um über das ADF-Portal Warnungen zu SSIS-Betriebsmetriken auszulösen.

![Auslösen von Warnungen zum SSIS-Betrieb über das ADF-Portal](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

[Wählen Sie die Seite **Warnungen** im **Azure Monitor**-Hub aus, und befolgen Sie die angegebene Schritt-für-Schritt-Anleitung](#data-factory-alerts), um über das Azure-Portal Warnungen zu SSIS-Betriebsmetriken auszulösen.

![Auslösen von Warnungen zum SSIS-Betrieb über das Azure-Portal](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>Protokolle zum SSIS-Betrieb

[Protokolle](../azure-monitor/platform/data-platform-logs.md) zum SSIS-Betrieb sind Ereignisse, die bei SSIS IR-Vorgängen und SSIS-Paketausführungen generiert werden. Sie enthalten genügend Kontext zu den identifizierten Problemen und sind für die Grundursachenanalyse nützlich. 

Beim Konfigurieren der Diagnoseeinstellungen und des Arbeitsbereichs für Ihre ADF-Instanz in Azure Monitor können Sie die relevanten Protokolle zum SSIS-Betrieb auswählen und an die Log Analytics-Instanz senden, die auf Azure Data Explorer basiert. Dort werden diese Daten für die [Analyse mit einer leistungsstarken Abfragesprache](../azure-monitor/log-query/log-query-overview.md), die [Darstellung in Azure-Dashboards](../azure-monitor/learn/tutorial-app-dashboards.md) und [Benachrichtigungen nahezu in Echtzeit](../azure-monitor/platform/alerts-log.md) verfügbar gemacht.

![Benennen Ihrer Einstellungen und Auswählen eines Log Analytics-Arbeitsbereichs](media/data-factory-monitor-oms/monitor-oms-image2.png)

Die Schemas und der Inhalt von Protokollen für SSIS-Paketausführungen in Azure Monitor und Log Analytics ähneln den Schemas der internen Tabellen und Sichten der SSISDB.

| Azure Monitor-Protokollkategorien          | Log Analytics-Tabellen                     | Interne SSISDB-Tabellen/-Sichten              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Weitere Informationen zu den Attributen bzw. Eigenschaften von SSIS-Betriebsprotokollen finden Sie unter [Schema von Protokollen und Ereignissen](#schema-of-logs-and-events).

Die ausgewählten Protokolle zur SSIS-Paketausführung werden unabhängig von den Aufrufmethoden immer an Log Analytics gesendet. Beispielsweise können Sie Paketausführungen in den für Azure aktivierten SSDT, über T-SQL für SSMS, SQL Server-Agent oder andere spezielle Tools und als ausgelöste oder Debugausführungen von SSIS-Paketausführungsaktivitäten in ADF-Pipelines aufrufen.

Beim Abfragen von Protokollen für SSIS IR-Vorgänge in Log Analytics können Sie die Eigenschaften **OperationName** und **ResultType** verwenden, die auf `Start/Stop/Maintenance` bzw. `Started/InProgress/Succeeded/Failed` festgelegt sind. 

![Abfragen von Protokollen zum SSIS IR-Betrieb in Log Analytics](media/data-factory-monitor-oms/log-analytics-query.png)

Beim Abfragen von Protokollen zu SSIS-Paketausführungen in Logs Analytics können Sie diese mit den Eigenschaften **OperationId**/**ExecutionId**/**CorrelationId** verknüpfen. **OperationId**/**ExecutionId** sind für alle Vorgänge bzw. Ausführungen im Zusammenhang mit Paketen, die **nicht** in der SSISDB gespeichert sind oder über T-SQL aufgerufen werden, immer auf `1` festgelegt.

![Abfragen von Protokollen zu SSIS-Paketausführungen in Log Analytics](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>Nächste Schritte
[Programmseitiges Überwachen und Verwalten von Pipelines](monitor-programmatically.md)