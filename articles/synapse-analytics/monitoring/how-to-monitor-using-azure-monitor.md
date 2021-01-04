---
title: Überwachen von Synapse Analytics mit Azure Monitor
description: Hier erfahren Sie, wie Sie Ihren Synapse Analytics-Arbeitsbereich mithilfe von Azure Monitor-Metriken, -Warnungen und -Protokollen überwachen.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 9032fcaf35265c791913f5b69fb0972bada6885f
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602460"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Verwenden von Azure Monitor mit Ihrem Azure Synapse Analytics-Arbeitsbereich

Cloudanwendungen sind komplex und verfügen über zahlreiche Variablen. Monitore stellen Daten bereit, die dabei helfen, die ordnungsgemäße Ausführung der Anwendung sicherzustellen. Monitore helfen Ihnen auch, potenzielle Probleme zu vermeiden und aufgetretene Probleme zu beheben. Sie können anhand der Überwachungsdaten umfassende Erkenntnisse zu Ihren Anwendungen gewinnen. Mit diesem Wissen können Sie die Leistung und Wartbarkeit von Anwendungen verbessern. Außerdem können Sie Aktionen automatisieren, die andernfalls einen manuellen Eingriff erfordern.

Azure Monitor stellt grundlegende Metriken, Warnungen und Protokolle der Infrastruktur für die meisten Azure-Dienste bereit. Azure-Diagnoseprotokolle werden von einer Ressource ausgegeben und stellen umfangreiche und in kurzen Abständen erfasste Betriebsdaten der Ressource bereit. Azure Synapse Analytics kann Diagnoseprotokolle in Azure Monitor schreiben.

Weitere Informationen finden Sie unter [Übersicht über Azure Monitor](../../azure-monitor/overview.md).

## <a name="metrics"></a>Metriken

Mit Monitor erhalten Sie Einblicke in die Leistung und Integrität ihrer Azure-Workloads. Metriken sind die wichtigsten Monitor-Datentypen. Sie werden auch als Leistungsindikatoren bezeichnet. Metriken werden von den meisten Azure-Ressourcen ausgegeben. Azure Monitor bietet Ihnen verschiedene Möglichkeiten, diese Metriken für die Überwachung und Problembehandlung zu konfigurieren und zu nutzen.

Folgen Sie den Anweisungen unter [Azure Monitor-Datenplattform](../../azure-monitor/platform/data-platform.md), um auf diese Metriken zuzugreifen.

### <a name="workspace-level-metrics"></a>Metriken auf Arbeitsbereichsebene

Nachfolgend finden Sie einige der von Arbeitsbereichen ausgegebenen Metriken:

| **Metrik**                           | **Metrikkategorie, Anzeigename**                  | **Einheit** | **Aggregationstypen** | **Beschreibung**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | Integration, Metrik zu Aktivitätsausführungen                     | Anzahl    | Summe (Standard), Anzahl                | Die Gesamtanzahl der innerhalb eines 1-minütigen Zeitfensters erfolgten/beendeten Aktivitätsausführungen. </br></br> Verwenden Sie die Dimension „Ergebnis“ dieser Metrik, um nach dem Endzustand „Erfolgreich“, „Fehlerhaft“ oder „Abgebrochen“ zu filtern.|
| IntegrationPipelineRunsEnded         | Integration, Metrik zu Pipelineausführungen                     | Anzahl    | Summe (Standard), Anzahl                | Die Gesamtanzahl der innerhalb eines 1-minütigen Zeitfensters erfolgten/beendeten Pipelineausführungen. </br></br> Verwenden Sie die Dimension „Ergebnis“ dieser Metrik, um nach dem Endzustand „Erfolgreich“, „Fehlerhaft“ oder „Abgebrochen“ zu filtern. |
| IntegrationTriggerRunsEnded          | Integration, Metrik zu Triggerausführungen                      | Anzahl    | Summe (Standard), Anzahl                | Die Gesamtanzahl der innerhalb eines 1-minütigen Zeitfensters erfolgten/beendeten Triggerausführungen. </br></br> Verwenden Sie die Dimension „Ergebnis“ dieser Metrik, um nach dem Endzustand „Erfolgreich“, „Fehlerhaft“ oder „Abgebrochen“ zu filtern. |
| BuiltinSqlPoolDataProcessedBytes     | Integrierter SQL-Pool, Verarbeitete Daten (Bytes) | Byte | Summe (Standard) | Die vom integrierten serverlosen SQL-Pool verarbeitete Datenmenge. |
| BuiltinSqlPoolLoginAttempts          | Integrierter SQL-Pool, Anmeldeversuche | Anzahl | Summe (Standard) | Die Anzahl von Anmeldeversuchen für den integrierten serverlosen SQL-Pool. |
| BuiltinSqlPoolDataRequestsEnded      | Integrierter SQL-Pool, Beendete Anforderungen (Bytes) | Anzahl | Summe (Standard) | Die Anzahl der beendeten SQL-Anforderungen für den integrierten serverlosen SQL-Pool. </br></br> Verwenden Sie die Dimension „Ergebnis“ dieser Metrik, um nach dem Endzustand zu filtern. |

### <a name="dedicated-sql-pool-metrics"></a>Metriken dedizierter SQL-Pools

Nachfolgend finden Sie einige der von dedizierten SQL-Pools ausgegebenen Metriken:

| **Metrik**                           | **Anzeigename**                  | **Einheit** | **Aggregationstypen** | **Beschreibung**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | DWU-Grenzwert                       | Anzahl   | Max (Standard), Min, Durchschnitt | Konfigurierte Größe des SQL-Pools |
| DWUUsed                             | DWU-Verbrauch                        | Anzahl   | Max (Standard), Min, Durchschnitt | Eine allgemeine Darstellung der Nutzung für den SQL-Pool. Wird anhand von DWU-Limit × DWU-Prozentsatz berechnet |
| DWUUsedPercent                      | DWU-Verbrauch in Prozent             | Percent | Max (Standard), Min, Durchschnitt | Eine allgemeine Darstellung der Nutzung für den SQL-Pool. Wird anhand des Höchstwerts zwischen CPU-Prozentsatz und E/A-Prozentsatz für Daten berechnet |
| ConnectionsBlockedByFirewall        | Durch die Firewall blockierte Verbindungen | Anzahl   | Summe (Standard)   | Anzahl von Verbindungen, die durch die Firewall blockiert wurden. Überprüfen Sie die Zugriffssteuerungsrichtlinien für Ihren SQL-Pool, und überwachen Sie diese Verbindungen bei einer hohen Anzahl |
| AdaptiveCacheHitPercent             | Prozentsatz der Treffer für adaptiven Cache   | Percent | Max (Standard), Min, Durchschnitt | Misst, wie gut Arbeitsauslastungen den adaptiven Cache nutzen. Verwenden Sie diese Metrik mit der Metrik für den Prozentsatz der Cachetreffer, um zu ermitteln, ob eine Skalierung zur Bereitstellung zusätzlicher Kapazität oder eine erneute Ausführung von Arbeitsauslastungen zum Aktualisieren des Caches erforderlich ist |
| AdaptiveCacheUsedPercent            | Verwendung des adaptiven Cache in Prozent  | Percent | Max (Standard), Min, Durchschnitt | Misst, wie gut Arbeitsauslastungen den adaptiven Cache nutzen. Verwenden Sie diese Metrik mit der Metrik zur Cacheverwendung in Prozent, um zu ermitteln, ob eine Skalierung zur Bereitstellung zusätzlicher Kapazität oder eine erneute Ausführung von Arbeitsauslastungen zum Aktualisieren des Caches erforderlich ist |
| LocalTempDBUsedPercent              | Lokale tempdb-Auslastung in Prozent    | Percent | Max (Standard), Min, Durchschnitt | Lokale tempdb-Auslastung für alle Computeknoten, Werte werden alle fünf Minuten ausgegeben |
| MemoryUsedPercent                   | Verwendeter Arbeitsspeicher in Prozent          | Percent | Max (Standard), Min, Durchschnitt | Arbeitsspeichernutzung aller Knoten im SQL-Pool |
| CPUPercent                          | CPU-Auslastung in Prozent             | Percent | Max (Standard), Min, Durchschnitt | CPU-Auslastung für alle Knoten im SQL-Pool |
| Verbindungen                         | Verbindungen                     | Anzahl   | Summe (Standard)  | Gesamtanzahl der Anmeldungen beim SQL-Pool |
| ActiveQueries                      | Aktive Abfragen                 | Anzahl   | Summe (Standard)   | Die aktiven Abfragen. Bei Verwenden dieser Metrik ohne Filterung und Aufteilung werden alle aktiven Abfragen angezeigt, die im System ausgeführt werden |
| QueuedQueries                      | Abfragen in Warteschlange                  | Anzahl   | Summe (Standard)   | Kumulierte Anzahl von Anforderungen, die in die Warteschlange gestellt wurden, nachdem das Parallelitätslimit erreicht wurde |
| WLGActiveQueries                   | Aktive Abfragen von Arbeitsauslastungsgruppen   | Anzahl   | Summe (Standard)   | Die aktiven Abfragen in der Arbeitsauslastungsgruppe. Bei Verwenden dieser Metrik ohne Filterung und Aufteilung werden alle aktiven Abfragen angezeigt, die im System ausgeführt werden |
| WLGActiveQueriesTimeouts           | Abfragetimeouts für Arbeitsauslastungsgruppen   | Anzahl   | Summe (Standard)   | Abfragen für die Arbeitsauslastungsgruppe, für die ein Timeout aufgetreten ist. Die von dieser Metrik gemeldeten Abfragetimeouts sind erst nach dem Start der Abfrage aufgetreten (Wartezeiten aufgrund von Sperren oder Ressourcenwartezeiten sind nicht enthalten). |
| WLGQueuedQueries                   | In der Warteschlange befindliche Abfragen der Arbeitsauslastungsgruppe   | Anzahl   | Summe (Standard)   | Kumulierte Anzahl von Anforderungen, die in die Warteschlange gestellt wurden, nachdem das Parallelitätslimit erreicht wurde |
| WLGAllocationBySystemPercent        | Zuordnung von Arbeitsauslastungsgruppen nach Systemprozentsatz | Percent | Max (Standard), Min, Durchschnitt, Summe | Die prozentuale Zuordnung von Ressourcen in Relation zum gesamten System |
| WLGAllocationByEffectiveCapResourcePercent   | Zuordnung von Arbeitsauslastungsgruppen nach maximalem Ressourcenprozentsatz | Percent | Max (Standard), Min, Durchschnitt | Zeigt die prozentuale Zuordnung von Ressourcen in Relation zum effektiven Ressourcenlimit in Prozent pro Arbeitsauslastungsgruppe an. Diese Metrik informiert über die effektive Auslastung der Arbeitsauslastungsgruppe |
| WLGEffectiveCapResourcePercent      | Effektives Ressourcenlimit (Prozent)  | Percent | Max (Standard), Min, Durchschnitt | Das effektive Ressourcenlimit in Prozent für die Arbeitsauslastungsgruppe. Wenn andere Arbeitsauslastungsgruppen mit „min_percentage_resource“ > 0 vorhanden sind, wird „effective_cap_percentage_resource“ proportional gesenkt |
| WLGEffectiveMinResourcePercent      | Effektive Mindestanzahl von Ressourcen (Prozent)  | Percent | Max (Standard), Min, Durchschnitt, Summe | Die Einstellung für die effektive Mindestressourcenmenge in Prozent, die unter Berücksichtigung des Servicelevels und der Einstellungen der Arbeitsauslastungsgruppe zulässig ist. „effective min_percentage_resource“ kann bei niedrigeren Servicelevels erhöht werden |

### <a name="apache-spark-pool-metrics"></a>Metriken von Apache Spark-Pools

Nachfolgend finden Sie einige der von Apache Spark-Pools ausgegebenen Metriken:

| **Metrik**                           | **Metrikkategorie, Anzeigename**                  | **Einheit** | **Aggregationstypen** | **Beschreibung**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Beendete Apache Spark-Anwendungen  | Anzahl | Summe (Standard) | Die Anzahl der beendeten Apache Spark-Poolanwendungen |
| BigDataPoolAllocatedCores     | Anzahl der dem Apache Spark-Pool zugeordneten virtuellen Kerne                 | Anzahl | Max (Standard), Min, Durchschnitt | Die einem Apache Spark-Pool zugeordneten virtuellen Kerne |
| BigDataPoolAllocatedMemory    | Größe des Arbeitsspeichers (GB), der dem Apache Spark-Pool zugeordnet ist            | Anzahl | Max (Standard), Min, Durchschnitt | Der dem Apache Spark-Pool zugeordnete Arbeitsspeicher (GB) |
| BigDataPoolApplicationsActive | Aktive Apache Spark-Anwendungen | Anzahl | Max (Standard), Min, Durchschnitt | Die Anzahl der aktiven Apache Spark-Poolanwendungen |

## <a name="alerts"></a>Benachrichtigungen

Melden Sie sich beim Azure-Portal an, und wählen Sie **Überwachen** > **Warnungen** aus, um Warnungen zu erstellen.

### <a name="create-alerts"></a>Erstellen von Warnungen

1. Wählen Sie **+ Neue Warnungsregel** aus, um eine neue Warnung zu erstellen.

1. Definieren Sie die **Warnungsbedingung**, um anzugeben, wann die Warnung ausgelöst werden soll.

    > [!NOTE]
    > Achten Sie darauf, dass in der Dropdownliste **Nach Ressourcentyp filtern** die Option **Alle** ausgewählt ist.

1. Definieren Sie die **Warnungsdetails**, um anzugeben, wie die Warnung konfiguriert werden soll.

1. Definieren Sie die **Aktionsgruppe**, um festzulegen, wer die Warnungen erhalten soll (und wie).

## <a name="logs"></a>Protokolle

### <a name="workspace-level-logs"></a>Protokolle auf Arbeitsbereichsebene

Nachfolgend finden Sie die von Azure Synapse Analytics-Arbeitsbereichen ausgegebenen Protokolle:

| Name der Log Analytics-Tabelle | Name der Protokollkategorie                 | BESCHREIBUNG |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiRequests     | GatewayApiRequests             | Azure Synapse-Gateway-API-Anforderungen. |
| SynapseRbacOperations         | SynapseRbacOperations          | Vorgänge der rollenbasierten Azure Synapse-Zugriffssteuerung (Synapse Role-Based Access Control, SRBAC). |

### <a name="dedicated-sql-pool-logs"></a>Protokolle dedizierter SQL-Pools

Nachfolgend finden Sie die von dedizierten SQL-Pools ausgegebenen Protokolle:

| Name der Log Analytics-Tabelle        | Name der Protokollkategorie             | BESCHREIBUNG |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Informationen zu SQL-Anforderungen/-Abfragen in einem dedizierten Azure Synapse-SQL-Pool.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Informationen zu Workern, die DMS-Schritte in einem dedizierten Azure Synapse-SQL-Pool ausführen.
| SynapseSqlPoolRequestSteps  | RequestSteps | Informationen zu Anforderungsschritten, mit denen eine bestimmte SQL-Anforderung/-Abfrage in einem dedizierten Azure Synapse-SQL-Pool erstellt wird.
| SynapseSqlPoolSqlRequests   | SqlRequests  | Informationen zu Abfrageverteilungen der Schritte von SQL-Anforderungen/-Abfragen in einem dedizierten Azure Synapse-SQL-Pool.
| SynapseSqlPoolWaits         | Wartevorgänge        | Informationen zu den Wartezuständen, die während der Ausführung einer SQL-Anforderung/-Abfrage in einem dedizierten Azure Synapse-SQL-Pool aufgetreten sind, einschließlich Sperren und Wartevorgänge für Übertragungswarteschlangen.

Weitere Informationen zu diesen Protokollen finden Sie unter den folgenden Links:
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="apache-spark-pool-log"></a>Protokoll von Apache Spark-Pools

Nachfolgend finden Sie das von Apache Spark-Pools ausgegebene Protokoll:

| Name der Log Analytics-Tabelle               | Name der Protokollkategorie              | BESCHREIBUNG                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | Informationen zu den beendeten Apache Spark-Anwendungen |

### <a name="diagnostic-settings"></a>Diagnoseeinstellungen

Verwenden Sie Diagnoseeinstellungen, um Diagnoseprotokolle für Nicht-Computeressourcen zu konfigurieren. Die Einstellungen für eine Ressourcensteuerung verfügen über die folgenden Features:

* Sie geben an, wohin Diagnoseprotokolle gesendet werden, z. B. an ein Azure Storage-Konto, an einen Azure Event Hub oder an Monitor-Protokolle.
* Sie geben an, welche Protokollkategorien gesendet werden.
* Sie geben an, wie lange die einzelnen Protokollkategorien in einem Speicherkonto aufbewahrt werden.
* Wenn für die Beibehaltungsdauer 0 Tage festgelegt sind, bedeutet dies, dass Protokolle unbegrenzt beibehalten werden. Andernfalls kann als Wert die Anzahl von Tagen (1 bis 2.147.483.647) festgelegt werden.
* Wenn Aufbewahrungsrichtlinien festgelegt werden, aber das Speichern von Protokollen in einem Speicherkonto deaktiviert ist, werden die Aufbewahrungsrichtlinien ignoriert. Diese Bedingung kann z.B. auftreten, wenn nur die Optionen „Event Hubs“ oder „Überwachungsprotokolle“ ausgewählt sind.
* Aufbewahrungsrichtlinien werden pro Tag angewendet. Die Grenze zwischen Tagen liegt um Mitternacht (Koordinierte Weltzeit, UTC). Jeweils am Ende eines Tages werden die Protokolle der Tage gelöscht, deren Aufbewahrungsdauer abgelaufen ist. Beispiel: Wenn Sie eine Aufbewahrungsrichtlinie für einen Tag verwenden, werden heute am Anfang des Tages die Protokolle von vorgestern gelöscht.

Mit den Azure Monitor-Diagnoseeinstellungen können Sie Diagnoseprotokolle zu Analysezwecken an mehrere unterschiedliche Ziele weiterleiten.

* **Speicherkonto**: Speichern Sie Diagnoseprotokolle zur Überwachung oder manuellen Überprüfung in einem Speicherkonto. In den Diagnoseeinstellungen können Sie eine Aufbewahrungsdauer (in Tagen) angeben.
* **Event Hub**: Streamen Sie die Protokolle in Azure Event Hubs. Die Protokolle werden zu Eingaben für einen Partnerdienst oder eine benutzerdefinierte Analyselösung wie Power BI.
* **Log Analytics-Arbeitsbereich**: Analysieren Sie die Protokolle mit Log Analytics. Die Integration von Azure Synapse in Log Analytics ist in den folgenden Szenarien nützlich:
  * Sie möchten komplexe Abfragen für einen umfangreichen Satz von Metriken schreiben, die von Azure Synapse in Log Analytics veröffentlicht werden. Sie können benutzerdefinierte Warnungen für diese Abfragen über Azure Monitor erstellen.
  * Sie möchten Arbeitsbereiche übergreifend überwachen. Sie können Daten aus mehreren Arbeitsbereichen an einen einzigen Log Analytics-Arbeitsbereich weiterleiten.

Sie können auch ein Speicherkonto oder einen Event Hub-Namespace verwenden, das/der sich nicht im Abonnement der Ressource befindet, von der Protokolle ausgegeben werden. Der Benutzer, der die Einstellung konfiguriert, benötigt entsprechenden Zugriff gemäß der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) auf beide Abonnements.

#### <a name="configure-diagnostic-settings"></a>Konfigurieren von Diagnoseeinstellungen

Sie können Diagnoseeinstellungen für Ihren Arbeitsbereich, Ihren dedizierten SQL-Pool oder Ihren Apache Spark-Pool erstellen oder hinzufügen.

1. Navigieren Sie im Portal zu „Monitor“. Wählen Sie **Einstellungen** > **Diagnoseeinstellungen** aus.

1. Wählen Sie den Synapse-Arbeitsbereich, den dedizierten SQL-Pool oder den Apache Spark-Pool aus, für den Sie eine Diagnoseeinstellung erstellen möchten.

1. Wenn für den ausgewählten Arbeitsbereich keine Diagnoseeinstellungen vorhanden sind, werden Sie aufgefordert, eine Einstellung zu erstellen. Wählen Sie **Diagnose aktivieren**.

   Wenn Diagnoseeinstellungen für den Arbeitsbereich vorhanden sind, wird eine Liste der bereits für die Ressource konfigurierten Einstellungen angezeigt. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.

1. Legen Sie einen Namen für Ihre Einstellung fest, wählen Sie **An Log Analytics senden** aus, und wählen Sie dann unter **Log Analytics-Arbeitsbereich** einen Arbeitsbereich aus.

    > [!NOTE]
    > Da eine Azure-Protokolltabelle nicht mehr als 500 Spalten enthalten darf, **empfehlen wir Ihnen dringend**, den _Modus „Ressourcenspezifisch“_ auszuwählen. Weitere Informationen finden Sie unter [Bekannte Einschränkung: Spaltenlimit in Azure-Diagnose](../../azure-monitor/platform/resource-logs.md#column-limit-in-azurediagnostics).

1. Wählen Sie **Speichern** aus.

Nach wenigen Augenblicken wird die neue Einstellung in Ihrer Liste der Einstellungen für Ihren Arbeitsbereich, Ihren dedizierten SQL-Pool oder Ihren Apache Spark-Pool angezeigt. Diagnoseprotokolle werden in diesen Arbeitsbereich gestreamt, sobald neue Ereignisdaten generiert werden. Zwischen der Ausgabe eines Ereignisses und dessen Anzeige in Log Analytics können bis zu 15 Minuten vergehen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Überwachen von Pipelineausführungen finden Sie im Artikel [Überwachen von Pipelineausführungen mithilfe von Synapse Studio](how-to-monitor-pipeline-runs.md). 

Weitere Informationen zum Überwachen von Apache Spark-Anwendungen finden Sie im Artikel [Verwenden von Synapse Studio zum Überwachen Ihrer Apache Spark-Anwendungen](apache-spark-applications.md).

Weitere Informationen zum Überwachen von SQL-Anforderungen finden Sie im Artikel [Verwenden von Synapse Studio zum Überwachen Ihrer SQL-Anforderungen](how-to-monitor-sql-requests.md).
