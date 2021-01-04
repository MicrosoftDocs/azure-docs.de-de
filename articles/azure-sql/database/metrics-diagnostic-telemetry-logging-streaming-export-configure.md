---
title: Konfigurieren des Streamingexports von Metriken und Ressourcenprotokollen
description: Erfahren Sie, wie Sie den Streamingexport von Metriken und Ressourcenprotokollen – einschließlich intelligenter Diagnoseanalysen aus Azure SQL-Datenbank und Azure SQL Managed Instance – als Ziel Ihrer Wahl konfigurieren, um Informationen zu Ressourcennutzung und Statistiken zur Abfrageausführung zu speichern.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 04/06/2020
ms.openlocfilehash: 999bb83af6937d4a7b3d7ee8207e2fd689a23d35
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490805"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>Konfigurieren des Streamingexports von Diagnosetelemetrie aus Azure SQL Database und SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Dieser Artikel bietet Informationen über die Leistungsmetriken und Ressourcenprotokolle für Azure SQL-Datenbank, die Sie für die Analyse in eines von mehreren Zielen exportieren können. Sie erfahren, wie Sie den Streamingexport dieser Diagnosetelemetriedaten über das Azure-Portal, PowerShell, die Azure CLI, die REST-API und Azure Resource Manager-Vorlagen konfigurieren.

Außerdem erhalten Sie Informationen über die Ziele, an die Sie die Diagnosetelemetriedaten streamen können, und wie Sie zwischen diesen Optionen wählen können. Die Optionen für die Ziele lauten:

- [Log Analytics und SQL-Analyse](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure Storage (in englischer Sprache)](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>Diagnosetelemetrie für den Export

Der wichtigste Teil der Diagnosetelemetriedaten, die Sie exportieren können, ist das Intelligent Insights-Protokoll (SQLInsights). Die in [Intelligent Insights](intelligent-insights-overview.md) integrierte Logik überwacht kontinuierlich die Datenbanknutzung durch künstliche Intelligenz und ermittelt Störungen, die zu mangelhafter Leistung führen. Nach dem Ermitteln wird eine detaillierte Analyse durchgeführt, die ein Intelligent Insights-Protokoll mit einer intelligenten Bewertung des Problems generiert. Die Bewertung besteht aus einer Analyse der Grundursache des Leistungsproblems der Datenbank und nach Möglichkeit Empfehlungen für Leistungsverbesserungen. Sie müssen den Streamingexport dieses Protokolls konfigurieren, um seinen Inhalt anzuzeigen.

Zusätzlich zum Streamen des Exports des Intelligent Insights-Protokolls können Sie auch eine Vielzahl von Leistungsmetriken und zusätzlichen Datenbankprotokollen exportieren. In der folgenden Tabelle werden die Leistungsmetriken und Ressourcenprotokolle beschrieben, die Sie für den Streamingexport zu einem von mehreren Zielen konfigurieren können. Die Diagnosetelemetriedaten können für einzelne Datenbanken, Pools für elastische Datenbanken und Pooldatenbanken sowie verwaltete Instanzen und Instanzdatenbanken konfiguriert werden.

| Diagnosetelemetriedaten für Datenbanken | Unterstützung für Azure-SQL-Datenbank | Unterstützung verwalteter Azure SQL-Instanzen |
| :------------------- | ----- | ----- |
| [Grundlegende Metriken](#basic-metrics): Enthält DTU-/CPU-Prozentsatz, DTU/CPU-Limit, gelesene physische Daten in Prozent, Protokollschreibvorgänge in Prozent, Verbindungen mit dem Status „Erfolgreich“, „Fehler“ und „Durch Firewall blockiert“, Sitzungen in Prozent, Worker in Prozent, Speicher, Speicher in Prozent und XTP-Speicher in Prozent. | Ja | Nein |
| [Instanz und App, Erweitert](#advanced-metrics): Enthält die Daten der tempdb-Systemdatenbank, die Größe der Protokolldatei und den verwendeten Anteil der tempdb-Protokolldatei. | Ja | Nein |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): Enthält Informationen zur Laufzeitstatistik der Abfrage, z. B. CPU-Nutzung und Abfragedauer. | Ja | Ja |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): Enthält Informationen zur Wartestatistik der Abfrage (worauf Ihre Abfragen gewartet haben), z.B. CPU, LOG und LOCKING. | Ja | Ja |
| [Fehler:](#errors-dataset) Enthält Informationen zu SQL-Fehlern für eine Datenbank. | Ja | Ja |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Enthält Informationen zur Wartezeit einer Datenbank für die verschiedenen Wartezeittypen. | Ja | Nein |
| [Timeouts](#time-outs-dataset): Enthält Informationen zu SQL-Timeouts für eine Datenbank. | Ja | Nein |
| [Blocks](#blockings-dataset): Enthält Informationen zu blockierenden Ereignissen für eine Datenbank. | Ja | Nein |
| [Deadlocks](#deadlocks-dataset): Enthält Informationen zu Deadlockereignissen für eine Datenbank. | Ja | Nein |
| [AutomaticTuning](#automatic-tuning-dataset): Enthält Informationen zu automatischen Optimierungsempfehlungen für eine Datenbank. | Ja | Nein |
| [SQLInsights](#intelligent-insights-dataset): Enthält Intelligent Insights-Informationen zur Leistung für eine Datenbank. Weitere Informationen finden Sie unter [Intelligent Insights](intelligent-insights-overview.md). | Ja | Ja |

> [!NOTE]
> Diagnoseeinstellungen können nicht für die **Systemdatenbanken** wie master-, msdb-, model-, resource- und tempdb-Datenbanken konfiguriert werden.

## <a name="streaming-export-destinations"></a>Streamingexportziele

Die Diagnosetelemetriedaten können für die Analyse an eine der folgenden Azure-Ressourcen gestreamt werden.

- **[Log Analytics-Arbeitsbereich](#stream-into-sql-analytics)** :

  An einen [Log Analytics-Arbeitsbereich](../../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) gestreamte Daten können von [SQL Analytics](../../azure-monitor/insights/azure-sql.md) verwendet werden. SQL Analytics ist eine auf die Cloud beschränkte Überwachungslösung. Sie ermöglicht die intelligente Überwachung Ihrer Datenbanken mit Leistungsberichten, Warnungen und Empfehlungen zur Risikominderung. Die an einen Log Analytics-Arbeitsbereich gestreamten Daten können zusammen mit anderen gesammelten Überwachungsdaten analysiert werden. Sie ermöglichen Ihnen außerdem die Nutzung anderer Azure Monitor-Features wie Warnungen und Visualisierungen.
- **[Azure Event Hubs](#stream-into-event-hubs)** :

  Daten, die an einen [Azure Event Hub](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs) gestreamt werden, bieten die folgenden Funktionen:

  - **Streamen von Protokollen an Protokollierungs- und Telemetriedatensysteme von Drittanbietern**: Streamen Sie alle Ihre Metriken und Ressourcenprotokolle an einen einzelnen Event Hub, um Protokolldaten an ein SIEM- oder Protokollanalysetool eines Drittanbieters weiterzureichen.
  - **Erstellen einer benutzerdefinierten Telemetrie- und Protokollierungsplattform:** Event Hubs ermöglichen dank des hochgradig skalierbaren Veröffentlichen-Abonnieren-Musters eine flexible Erfassung der Metriken und Ressourcenprotokolle auf einer benutzerdefinierten Telemetrieplattform. Ausführliche Informationen finden Sie unter [Designing and Sizing a Global Scale Telemetry Platform on Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) (Entwerfen und Dimensionieren einer globalen Telemetrieplattform in Azure Event Hubs).
  - **Anzeigen der Dienstintegrität durch Streamen von Daten an Power BI**: Verwenden Sie Event Hubs, Stream Analytics und Power BI, um sich anhand Ihrer Diagnosedaten nahezu in Echtzeit einen Einblick in Ihre Azure-Dienste verschaffen. Details zu dieser Lösung finden Sie unter [Stream Analytics und Power BI: Ein Dashboard zur Echtzeitanalyse von Streamingdaten](../../stream-analytics/stream-analytics-power-bi-dashboard.md).
- **[Azure Storage](#stream-into-azure-storage)** :

  Durch das Streamen von Daten an [Azure Storage](../../azure-monitor/platform/resource-logs.md#send-to-azure-storage) können Sie immense Mengen von Diagnosetelemetriedaten archivieren, und zwar zu einem Bruchteil der Kosten der beiden vorherigen Streamingoptionen.

Die an eines dieser Ziele gestreamten Diagnosetelemetriedaten können für die Messung der Ressourcenverwendung und der Abfrageausführungsstatistiken verwendet werden, um die Leistungsüberwachung zu vereinfachen.

![Das Diagramm zeigt viele SQL-Datenbanken und Datenbanken in verwalteten Instanzen, die Telemetriedaten an den Azure-Diagnose-Agent senden, der Informationen an Azure SQL-Analyse, den Event Hub und den Speicher weiterleitet.](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>Aktivieren und Konfigurieren des Streamingexports von Diagnosetelemetriedaten

Sie können die Protokollierung von Metriken und Diagnosetelemetriedaten mit einer der folgenden Methoden aktivieren und verwalten:

- Azure-Portal
- PowerShell
- Azure CLI
- Azure Monitor-REST-API
- Azure Resource Manager-Vorlage

> [!NOTE]
> Informationen zum Aktivieren des Streamens von Sicherheitstelemetriedaten finden Sie unter [Einrichten der Überwachung für Ihre Datenbank](./auditing-overview.md#setup-auditing) sowie unter [Überwachungsprotokolle in Azure Monitor-Protokollen und Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>Konfigurieren des Streamingexports von Diagnosetelemetriedaten

Das Menü **Diagnoseeinstellungen** im Azure-Portal kann für das Aktivieren und Konfigurieren des Streamings von Diagnosetelemetriedaten verwendet werden. Darüber hinaus können Sie PowerShell, die Azure-Befehlszeilenschnittstelle, die [REST-API](/rest/api/monitor/diagnosticsettings) und [Resource Manager-Vorlagen](../../azure-monitor/samples/resource-manager-diagnostic-settings.md) verwenden, um das Streaming von Diagnosetelemetriedaten zu konfigurieren. Für das Streaming der Diagnosetelemetriedaten können folgende Ziele festgelegt werden: Azure Storage, Azure Event Hubs und Azure Monitor-Protokolle.

> [!IMPORTANT]
> Der Streamingexport von Diagnosetelemetriedaten ist standardmäßig nicht aktiviert.

Wählen Sie eine der folgenden Registerkarten aus, um eine Schritt-für-Schritt-Anleitung zum Konfigurieren des Streamingexports von Diagnosetelemetriedaten im Azure-Portal und Skripts für die Ausführung der gleichen Aufgabe mit PowerShell und der Azure CLI zu erhalten.

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Pools für elastische Datenbanken in Azure SQL-Datenbank

Sie können eine Ressource des Pools für elastische Datenbanken zum Erfassen der folgenden Diagnosetelemetriedaten einrichten:

| Resource | Überwachte Telemetriedaten |
| :------------------- | ------------------- |
| **Pool für elastische Datenbanken** | [Grundlegende Metriken](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) enthält eDTU-/CPU-Prozentsatz, eDTU-/CPU-Limit, Prozentsatz der gelesenen physischen Daten, Prozentsatz für Protokollschreibvorgang, Sitzungen in Prozent, Worker in Prozent, Speicher, Speicher in Prozent, Speicherbegrenzung und XTP-Speicher in Prozent. |

Um das Streaming der Diagnosetelemetriedaten für Pools für elastische Datenbanken und Pooldatenbanken zu konfigurieren, müssen Sie die beiden separat konfigurieren:

- Aktivieren Sie das Streaming von Diagnosetelemetriedaten für einen Pool für elastische Datenbanken.
- Aktivieren Sie das Streaming von Diagnosetelemetriedaten für jede Datenbank im Pool für elastische Datenbanken.

Der Container für Pools für elastische Datenbanken verfügt über eigene Telemetriedaten, die sich von den Telemetriedaten der einzelnen Pooldatenbanken unterscheiden.

Um das Streaming von Diagnosetelemetriedaten für eine Ressource des Pools für elastische Datenbanken zu aktivieren, gehen Sie folgendermaßen vor:

1. Navigieren Sie im Azure-Portal zur Ressource des **Pools für elastische Datenbanken**.
2. Wählen Sie **Diagnoseeinstellungen** aus.
3. Klicken Sie auf **Diagnose aktivieren**, wenn noch keine Einstellungen vorgenommen wurden, oder auf **Einstellung bearbeiten**, um eine vorhandene Einstellung zu bearbeiten.

   ![Aktivieren der Diagnose für Pools für elastische Datenbanken](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. Geben Sie einen Einstellungsnamen zu Referenzzwecken ein.
5. Wählen Sie eine Zielressource für das Streaming von Diagnosedaten aus: **In einem Speicherkonto archivieren**, **An einen Event Hub streamen** oder **An Log Analytics senden**.
6. Wählen Sie für die Protokollanalyse **Konfigurieren** aus, und erstellen Sie einen neuen Arbeitsbereich, indem Sie auf **+ Neuen Arbeitsbereich erstellen** klicken, oder wählen Sie einen vorhandenen Arbeitsbereich aus.
7. Aktivieren Sie das Kontrollkästchen für Diagnosetelemetrie des Pools für elastische Datenbanken: **Grundlegende** Metriken.
   ![Konfigurieren der Diagnose für Pools für elastische Datenbanken](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. Wählen Sie **Speichern** aus.
9. Konfigurieren Sie außerdem das Streaming der Diagnosetelemetriedaten für jede Datenbank innerhalb des Pools für elastische Datenbanken, den Sie überwachen möchten, indem Sie die im nächsten Abschnitt beschriebenen Schritte ausführen.

> [!IMPORTANT]
> Neben Diagnosetelemetriedaten für einen Pool für elastische Datenbanken müssen Sie auch Diagnosetelemetriedaten für jede Datenbank in diesem Pool konfigurieren.

### <a name="databases-in-azure-sql-database"></a>Datenbanken in Azure SQL-Datenbank

Sie können eine Datenbankressource zum Erfassen der folgenden Diagnosetelemetriedaten einrichten:

| Resource | Überwachte Telemetriedaten |
| :------------------- | ------------------- |
| **Einzel- oder Pooldatenbank** | Zu den [grundlegenden Metriken](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) gehören DTU-Prozentsatz, DTU-Verwendung, DTU-Limit, CPU-Prozentsatz, Prozentsatz der gelesen physischen Daten, Prozentsatz der Protokollschreibvorgänge, Verbindungen mit Status Erfolgreich/Fehlerhaft/Durch Firewall blockiert, Sitzungen in Prozent, Worker in Prozent, Speicher, Speicher in Prozent, XTP-Speicher in Prozent und Deadlocks. |

Führen Sie zum Aktivieren des Streamings von Diagnosetelemetriedaten für Einzel- oder Pooldatenbanken die folgenden Schritte aus:

1. Navigieren Sie zur **Azure SQL-Datenbank**-Ressource.
2. Wählen Sie **Diagnoseeinstellungen** aus.
3. Klicken Sie auf **Diagnose aktivieren**, wenn noch keine Einstellungen vorgenommen wurden, oder auf **Einstellung bearbeiten**, um eine vorhandene Einstellung zu bearbeiten. Sie können bis zu drei parallele Verbindungen zum Streamen von Diagnosetelemetriedaten erstellen.
4. Klicken Sie auf **Add diagnostic setting** (Diagnoseeinstellung hinzufügen), um das parallele Streaming von Diagnosedaten in mehrere Ressourcen zu konfigurieren.

   ![Aktivieren der Diagnose für Einzel- und Pooldatenbanken](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. Geben Sie einen Einstellungsnamen zu Referenzzwecken ein.
6. Wählen Sie eine Zielressource für das Streaming von Diagnosedaten aus: **In einem Speicherkonto archivieren**, **An einen Event Hub streamen** oder **An Log Analytics senden**.
7. Aktivieren Sie für die standardmäßige, ereignisbasierte Überwachungsoberfläche die folgenden Kontrollkästchen für Datenbankdiagnoseprotokoll-Telemetrie: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks** und **Deadlocks**.
8. Aktivieren Sie für eine erweiterte Überwachungsoberfläche mit einem Aktualisierungsintervall von einer Minute das Kontrollkästchen für **grundlegende** Metriken.

   ![Konfigurieren der Diagnose für Azure SQL-Datenbank](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. Wählen Sie **Speichern** aus.
10. Wiederholen Sie diese Schritte für jede Datenbank, die überwacht werden soll.

> [!TIP]
> Wiederholen Sie diese Schritte für jede Einzel- oder Pooldatenbank, die überwacht werden soll.

### <a name="instances-in-azure-sql-managed-instance"></a>Instanzen in Azure SQL Managed Instance

Sie können eine Ressource der verwalteten Instanz zum Erfassen der folgenden Diagnosetelemetriedaten einrichten:

| Resource | Überwachte Telemetriedaten |
| :------------------- | ------------------- |
| **Verwaltete Instanz** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) enthält die Anzahl virtueller Kerne, den durchschnittlichen CPU-Prozentsatz, E/A-Anforderungen, gelesene/geschriebene Bytes, den reservierten und genutzten Speicherplatz. |

Um das Streaming von Diagnosetelemetriedaten für die verwaltete Instanz und Instanzdatenbanken zu konfigurieren, müssen Sie beides separat konfigurieren:

- Aktivieren Sie das Streaming von Diagnosetelemetriedaten für verwaltete Instanzen.
- Aktivieren Sie das Streaming von Diagnosetelemetriedaten für jede Instanzdatenbank.

Der Container für die verwaltete Instanz verfügt über eigene Telemetriedaten, die sich von denen der einzelnen Instanzdatenbanken unterscheiden.

Führen Sie die folgenden Schritte aus, um das Streaming von Diagnosetelemetriedaten für eine Ressource der verwalteten Instanz zu aktivieren:

1. Navigieren Sie im Azure-Portal zur Ressource der **verwalteten Instanz**.
2. Wählen Sie **Diagnoseeinstellungen** aus.
3. Klicken Sie auf **Diagnose aktivieren**, wenn noch keine Einstellungen vorgenommen wurden, oder auf **Einstellung bearbeiten**, um eine vorhandene Einstellung zu bearbeiten.

   ![Aktivieren der Diagnose für die verwaltete Instanz](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. Geben Sie einen Einstellungsnamen zu Referenzzwecken ein.
5. Wählen Sie eine Zielressource für das Streaming von Diagnosedaten aus: **In einem Speicherkonto archivieren**, **An einen Event Hub streamen** oder **An Log Analytics senden**.
6. Wählen Sie für die Protokollanalyse **Konfigurieren** aus, und erstellen Sie einen neuen Arbeitsbereich, indem Sie auf **+ Neuen Arbeitsbereich erstellen** klicken, oder verwenden Sie einen vorhandenen Arbeitsbereich.
7. Aktivieren Sie das Kontrollkästchen für Diagnosetelemetrie der Instanz: **ResourceUsageStats**.

   ![Konfigurieren der Diagnose für die verwaltete Instanz](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. Wählen Sie **Speichern** aus.
9. Konfigurieren Sie außerdem das Streaming der Diagnosetelemetriedaten für jede Instanzdatenbank innerhalb der verwalteten Instanz, die Sie überwachen möchten, indem Sie die im nächsten Abschnitt beschriebenen Schritte ausführen.

> [!IMPORTANT]
> Neben Diagnosetelemetriedaten für eine verwaltete Instanz müssen auch Diagnosetelemetriedaten für jede Instanzdatenbank konfiguriert werden.

### <a name="databases-in-azure-sql-managed-instance"></a>Datenbanken in Azure SQL Managed Instance

Sie können eine Ressource der Instanzdatenbank zum Erfassen der folgenden Diagnosetelemetriedaten einrichten:

| Resource | Überwachte Telemetriedaten |
| :------------------- | ------------------- |
| **Instanzdatenbank** | [ResourceUsageStats](#resource-usage-stats-for-managed-instances) enthält die Anzahl virtueller Kerne, den durchschnittlichen CPU-Prozentsatz, E/A-Anforderungen, gelesene/geschriebene Bytes, den reservierten und genutzten Speicherplatz. |

Führen Sie zum Aktivieren des Streamings von Diagnosetelemetriedaten für eine Instanzdatenbank die folgenden Schritte aus:

1. Navigieren Sie zur Ressource der **Instanzdatenbank** in der verwalteten Instanz.
2. Wählen Sie **Diagnoseeinstellungen** aus.
3. Klicken Sie auf **Diagnose aktivieren**, wenn noch keine Einstellungen vorgenommen wurden, oder auf **Einstellung bearbeiten**, um eine vorhandene Einstellung zu bearbeiten.
   - Sie können bis zu drei (3) parallele Verbindungen zum Streamen von Diagnosetelemetriedaten erstellen.
   - Wählen Sie **+ Diagnoseeinstellung hinzufügen** aus, um das parallele Streaming von Diagnosedaten in mehrere Ressourcen zu konfigurieren.

   ![Aktivieren der Diagnose für Instanzdatenbanken](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. Geben Sie einen Einstellungsnamen zu Referenzzwecken ein.
5. Wählen Sie eine Zielressource für das Streaming von Diagnosedaten aus: **In einem Speicherkonto archivieren**, **An einen Event Hub streamen** oder **An Log Analytics senden**.
6. Aktivieren Sie die Kontrollkästchen für die Datenbankdiagnosetelemetrie: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** und **Errors**.
   ![Konfigurieren der Diagnose für Instanzdatenbanken](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. Wählen Sie **Speichern** aus.
8. Wiederholen Sie diese Schritte für jede Instanzdatenbank, die überwacht werden soll.

> [!TIP]
> Wiederholen Sie diese Schritte für jede Instanzdatenbank, die überwacht werden soll.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Sie können die Protokollierung von Metriken und Diagnosen mit PowerShell aktivieren.

- Verwenden Sie den folgenden Befehl, um das Speichern von Metriken und Ressourcenprotokollen in einem Speicherkonto zu aktivieren:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  Die Speicherkonto-ID ist die Ressourcen-ID für das Zielspeicherkonto.

- Verwenden Sie den folgenden Befehl, um das Streamen von Metriken und Ressourcenprotokollen an einen Event Hub zu aktivieren:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Die Azure Service Bus-Regel-ID ist eine Zeichenfolge mit dem folgenden Format:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Verwenden Sie den folgenden Befehl, um das Senden von Metriken und Ressourcenprotokollen an einen Log Analytics-Arbeitsbereich zu aktivieren:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- Sie können die Ressourcen-ID mit dem folgenden Befehl aus Ihrem Log Analytics-Arbeitsbereich abrufen:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Sie können diese Parameter miteinander kombinieren, um mehrere Ausgabeoptionen zu aktivieren.

**So konfigurieren Sie mehrere Azure-Ressourcen**

Wenn mehrere Abonnements unterstützt werden, verwenden Sie das PowerShell-Skript aus [Enable Azure resource metrics logging using PowerShell](/archive/blogs/msoms/enable-azure-resource-metrics-logging-using-powershell) (Aktivieren der Protokollierung der Azure-Ressourcenmetriken mit PowerShell).

Geben Sie die Arbeitsbereichsressourcen-ID \<$WSID\> als Parameter an, wenn Sie das Skript `Enable-AzureRMDiagnostics.ps1` ausführen, um Diagnosedaten von mehreren Ressourcen an den Arbeitsbereich zu senden.

- Verwenden Sie zum Abrufen der Arbeitsbereichs-ID \<$WSID\> des Ziels für Ihre Diagnosedaten das folgende Skript:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Ersetzen Sie \<subID\> durch die Abonnement-ID, \<RG_NAME\> durch den Namen der Ressourcengruppe und \<WS_NAME\> durch den Namen des Arbeitsbereichs.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können die Protokollierung von Metriken und Diagnosen mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) aktivieren.

> [!IMPORTANT]
> Skripts zum Aktivieren der Diagnoseprotokollierung werden für die Version 1.0 der Azure-Befehlszeilenschnittstelle unterstützt. Version 2.0 der Azure-Befehlszeilenschnittstelle wird derzeit nicht unterstützt.

- Verwenden Sie den folgenden Befehl, um das Speichern von Metriken und Ressourcenprotokollen in einem Speicherkonto zu aktivieren:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  Die Speicherkonto-ID ist die Ressourcen-ID für das Zielspeicherkonto.

- Verwenden Sie den folgenden Befehl, um das Streamen von Metriken und Ressourcenprotokollen an einen Event Hub zu aktivieren:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  Die Service Bus-Regel-ID ist eine Zeichenfolge mit dem folgenden Format:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Verwenden Sie den folgenden Befehl, um das Senden von Metriken und Ressourcenprotokollen an einen Log Analytics-Arbeitsbereich zu aktivieren:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Sie können diese Parameter miteinander kombinieren, um mehrere Ausgabeoptionen zu aktivieren.

---

## <a name="stream-into-sql-analytics"></a>Streamen in Azure SQL-Analyse

Azure SQL-Datenbank und Azure SQL Managed Instance-Metriken und -Ressourcenprotokolle, die in einen Log Analytics-Arbeitsbereich gestreamt werden, können von Azure SQL-Analyse genutzt werden. Azure SQL-Analyse ist eine Cloudlösung, die die Leistung von Singletons, Pools für elastische Datenbanken, Pooldatenbanken, verwaltete Instanzen und Instanzdatenbanken in großem Maßstab abonnementübergreifend überwacht. Die Lösung ermöglicht Ihnen das Erfassen und Visualisieren von Leistungsmetriken und verfügt über integrierte intelligente Funktionen für die Behandlung von Leistungsproblemen.

![Azure SQL-Analyse – Übersicht](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>Übersicht über die Installation

Sie können eine Sammlung von Datenbanken mit Azure SQL-Analyse überwachen, indem Sie die folgenden Schritte ausführen:

1. Erstellen Sie im Azure Marketplace eine Azure SQL-Analyselösung.
2. Erstellen Sie in der Lösung einen Log Analytics-Arbeitsbereich.
3. Konfigurieren Sie Datenbanken zum Streamen von Diagnosetelemetriedaten in den Arbeitsbereich.

Sie können den Streamingexport der Diagnosetelemetriedaten mithilfe der integrierten Option **An Log Analytics senden** auf der Registerkarte „Diagnoseeinstellungen“ im Azure-Portal konfigurieren. Sie können das Streaming in den Log Analytics-Arbeitsbereich anhand von Diagnoseeinstellungen auch mit [PowerShell-Cmdlets](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), der [Azure CLI](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), der [Azure Monitor-REST-API](/rest/api/monitor/diagnosticsettings) oder [Resource Manager-Vorlagen](../../azure-monitor/samples/resource-manager-diagnostic-settings.md) aktivieren.

### <a name="create-an-azure-sql-analytics-resource"></a>Erstellen einer Azure SQL-Analyse-Ressource

1. Suchen Sie im Azure Marketplace nach „Azure SQL-Analyse“, und wählen Sie die Lösung aus.

   ![Suchen von Azure SQL-Analyse im Portal](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. Wählen Sie auf dem Übersichtsbildschirm der Lösung die Option **Erstellen** aus.

3. Tragen Sie in das Azure SQL-Analyse-Formular die erforderlichen zusätzlichen Informationen ein: Name des Arbeitsbereichs, Abonnement, Ressourcengruppe, Ort und Tarif.

   ![Konfigurieren von Azure SQL-Analyse im Portal](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. Klicken Sie zum Bestätigen auf **OK** und anschließend auf **Erstellen**.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Konfigurieren der Ressource zum Aufzeichnen von Metriken und Ressourcenprotokollen

Sie müssen das Streaming von Diagnosetelemetriedaten für einzelne Datenbanken, Pooldatenbanken, Pools für elastische Datenbanken, verwaltete Instanzen und Instanzdatenbanken getrennt konfigurieren. Die Verwendung des Azure-Portals ist die einfachste Möglichkeit zum Konfigurieren des Ziels, in dem eine Ressource Metriken aufzeichnet. Ausführliche Schritte finden Sie unter [Konfigurieren des Streamingexports von Diagnosetelemetriedaten](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Verwenden von Azure SQL-Analyse für Überwachung und Warnungen

Sie können SQL-Analyse als ein hierarchisches Dashboard verwenden, um Ihre Datenbankressourcen anzuzeigen.

- Informationen zum Verwenden von Azure SQL-Analyse finden Sie unter [Überwachen von SQL-Datenbank mithilfe von SQL-Analyse](../../azure-monitor/insights/azure-sql.md).
- Informationen zum Einrichten von Warnungen in SQL-Analyse finden Sie unter [Erstellen von Warnungen für Datenbanken, Pools für elastische Datenbanken und verwaltete Instanzen](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts).

## <a name="stream-into-event-hubs"></a>Streamen an Event Hubs

Sie können Metriken und Ressourcenprotokolle für Azure SQL-Datenbank und Azure SQL Managed Instance mit der integrierten Option **An einen Event Hub streamen** im Azure-Portal in Event Hubs streamen. Sie können die Service Bus-Regel-ID auch aktivieren, indem Sie Diagnoseeinstellungen über PowerShell-Cmdlets, die Azure-Befehlszeilenschnittstelle oder die Azure Monitor-REST-API verwenden. Achten Sie darauf, dass sich der Event Hub in derselben Region wie Ihre Datenbank und der Server befindet.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Welche Vorgänge können mit Metriken und Ressourcenprotokollen in Event Hubs ausgeführt werden?

Nachdem die ausgewählten Daten an Event Hubs gestreamt wurden, sind Sie der Einrichtung fortgeschrittener Überwachungsszenarien ein ganzes Stück näher gekommen. Event Hubs fungiert als Eingangsportal für eine Ereignispipeline. Nachdem Daten in einem Event Hub erfasst wurden, können sie mit einem beliebigen Echtzeitanalyseanbieter oder Speicheradapter transformiert und gespeichert werden. Event Hubs entkoppelt die Produktion eines Ereignisdatenstroms von der Nutzung dieser Ereignisse. Auf diese Weise können Ereignisconsumer auf die Ereignisse gemäß ihrem eigenen Zeitplan zugreifen. Weitere Informationen zu Event Hubs finden Sie hier:

- [Was sind Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Erste Schritte mit Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Sie können gestreamte Metriken in Event Hubs für folgende Zwecke verwenden:

- **Anzeigen der Dienstintegrität durch Streamen von Daten zum langsamsten Pfad an PowerBI**

  Mithilfe von Event Hubs, Stream Analytics und PowerBI können Sie sich anhand Ihrer Metrik- und Diagnosedaten problemlos und nahezu in Echtzeit einen Einblick in Ihre Azure-Dienste verschaffen. [Stream Analytics und Power BI](../../stream-analytics/stream-analytics-power-bi-dashboard.md) bietet eine gute Übersicht über die Einrichtung von Event Hubs, die Verarbeitung von Daten mit Stream Analytics und die Verwendung von PowerBI als Ausgabe.

- **Streamen von Protokollen in Protokollierungs- und Telemetriedatenströme von Drittanbietern**

  Mithilfe des Event Hubs-Streamings können Sie Ihre Metriken und Ressourcenprotokolle in verschiedene Überwachungs- und Protokollanalyselösungen von Drittanbietern übertragen.

- **Erstellen einer benutzerdefinierten Telemetrie- und Protokollierungsplattform**

  Haben Sie bereits eine benutzerdefinierte Telemetrieplattform, oder erwägen Sie die Erstellung einer solchen Plattform? Der Event Hubs-Dienst beruht auf dem hochgradig skalierbaren Veröffentlichen-Abonnieren-Muster und ermöglicht Ihnen daher die flexible Erfassung von Metriken und Ressourcenprotokollen. Informationen zur Verwendung von Event Hubs für eine globale Telemetrieplattform finden Sie in der [Anleitung von Dan Rosanova](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Streamen nach Azure Storage

Sie können Metriken und Ressourcenprotokolle mithilfe der integrierten Option **In ein Speicherkonto archivieren** im Azure-Portal in Azure Storage speichern. Sie können Storage auch mithilfe von Diagnoseeinstellungen über PowerShell-Cmdlets, die Azure-Befehlszeilenschnittstelle oder die Azure Monitor-REST-API aktivieren.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Schema der Metriken und Ressourcenprotokolle im Speicherkonto

Nachdem Sie die Sammlung von Metriken und Ressourcenprotokollen eingerichtet haben, wird ein Speichercontainer im ausgewählten Speicherkonto erstellt, wenn die ersten Datenzeilen verfügbar sind. Die Struktur der Blobs sieht wie folgt aus:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Einfacher ausgedrückt:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ein Blobname für grundlegende Metriken kann beispielsweise wie folgt lauten:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ein Blobname für das Speichern von Daten aus einem Pool für elastische Datenbanken sieht wie folgt aus:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Datenaufbewahrungsrichtlinie und Preisübersicht

Wenn Sie Event Hubs oder ein Speicherkonto auswählen, können Sie eine Aufbewahrungsrichtlinie angeben. Diese Richtlinie löscht Daten, die älter als ein ausgewählter Zeitraums sind. Wenn Sie Log Analytics angeben, hängt die Aufbewahrungsrichtlinie vom ausgewählten Tarif ab. In diesem Fall können Sie mit den verfügbaren kostenlosen Datenerfassungseinheiten jeden Monat mehrere Datenbanken kostenlos überwachen. Für die Nutzung von Diagnosetelemetriedaten, die über die kostenlosen Einheiten hinausgeht, können Kosten anfallen.

> [!IMPORTANT]
> Bei aktiven Datenbanken mit umfangreicheren Workloads werden mehr Daten als bei Datenbanken im Leerlauf erfasst. Weitere Informationen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).

Wenn Sie die Azure SQL-Analyse verwenden, können Sie Ihre Datenerfassungsnutzung überwachen, indem Sie im Navigationsmenü von Azure SQL-Analyse die Option **OMS-Arbeitsbereich** und dann **Nutzung** und **Geschätzte Kosten** auswählen.

## <a name="metrics-and-logs-available"></a>Verfügbare Metriken und Protokolle

Informationen zum Überwachen von Telemetriedaten, die für Singletons, Pooldatenbanken, Pools für elastische Datenbanken, verwaltete Instanzen und Instanzdatenbanken verfügbar sind, finden Sie in diesem Abschnitt dieses Artikels. Mit der [Protokollabfragesprache von Azure Monitor](../../azure-monitor/log-query/get-started-queries.md) können Sie die in SQL Analytics gesammelten Überwachungstelemetriedaten für Ihre eigene benutzerdefinierte Analyse und Anwendungsentwicklung verwenden.

### <a name="basic-metrics"></a>Grundlegende Metriken

In den folgenden Tabellen finden Sie Details zu „Grundlegende Metriken“ für die verschiedenen Ressourcen.

> [!NOTE]
> Die Option „Grundlegende Metriken“ wurde früher als „Alle Metriken“ bezeichnet. Die jetzt vorgenommene Änderung betraf nur den Namen und nicht die überwachten Metriken. Sie wurde initiiert, um die zukünftige Einführung weiterer Metrikkategorien zu ermöglichen.

#### <a name="basic-metrics-for-elastic-pools"></a>Grundlegende Metriken für Pools für elastische Datenbanken

|**Ressource**|**Metriken**|
|---|---|
|Pool für elastische Datenbanken|eDTU-Prozentsatz, eDTU-Verwendung, eDTU-Limit, CPU-Prozentsatz, Prozentsatz der gelesen physischen Daten, Prozentsatz für Protokollschreibvorgang, Sitzungen in Prozent, Worker in Prozent, Speicher, Speicher in Prozent, Speicherbegrenzung, XTP-Speicher in Prozent |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Grundlegende Metriken bei Einzel- und Pooldatenbanken

|**Ressource**|**Metriken**|
|---|---|
|Einzel- und Pooldatenbank|DTU-Prozentsatz, DTU-Verwendung, DTU-Limit, CPU-Prozentsatz, Prozentsatz der gelesen physischen Daten, Prozentsatz für Protokollschreibvorgang, Verbindungen mit Status Erfolgreich/Fehlgeschlagen/Durch Firewall blockiert, Sitzungen in Prozent, Worker in Prozent, Speicher, Speicher in Prozent, XTP-Speicher in Prozent und Deadlocks |

### <a name="advanced-metrics"></a>Erweiterte Metriken

In der folgenden Tabelle finden Sie Details zu den erweiterten Metriken.

|**Metrik**|**Metrikanzeigename**|**Beschreibung**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|SQL-Prozess: Kern (in Prozent)|Prozentsatz der CPU-Auslastung für den SQL-Prozess, gemessen vom Betriebssystem.|
|sqlserver_process_memory_percent<sup>1</sup> |SQL-Prozess: Arbeitsspeicher (in Prozent)|Prozentsatz der Arbeitsspeicherauslastung für den SQL-Prozess, gemessen vom Betriebssystem.|
|tempdb_data_size<sup>2</sup>| Größe der tempdb-Datendatei in Kilobytes |Die Größe der tempdb-Datendatei in Kilobytes.|
|tempdb_log_size<sup>2</sup>| Größe der tempdb-Protokolldatei in Kilobytes |Die Größe der tempdb-Protokolldatei in Kilobytes.|
|tempdb_log_used_percent<sup>2</sup>| Nutzung des tempdb-Protokolls in Prozent |Die Nutzung des tempdb-Protokolls in Prozent.|

<sup>1</sup> Diese Metrik steht für Datenbanken zur Verfügung, die das vCore-Kaufmodell mit mindestens 2 virtuellen Kernen verwenden, oder für Datenbanken mit mindestens 200 DTUs (bei DTU-basierten Kaufmodellen).

<sup>2</sup> Diese Metrik steht für Datenbanken zur Verfügung, die das vCore-Kaufmodell mit mindestens 2 virtuellen Kernen verwenden, oder für Datenbanken mit mindestens 200 DTUs (bei DTU-basierten Kaufmodellen). Diese Metrik ist derzeit nicht für Hyperscale-Datenbanken oder Data Warehouses verfügbar.

### <a name="basic-logs"></a>Grundlegende-Protokolle

Die folgenden Tabellen enthalten Details zu Telemetriedaten, die für alle Protokolle verfügbar sind. Weitere Informationen finden Sie unter [Unterstützte Diagnosetelemetrie](#diagnostic-telemetry-for-export).

#### <a name="resource-usage-stats-for-managed-instances"></a>Ressourcennutzungsstatistik für verwaltete Instanzen

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure|
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|type|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category|Name der Kategorie Immer: ResourceUsageStats |
|Resource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: MANAGEDINSTANCES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name der verwalteten Instanz |
|resourceId|Ressourcen-URI |
|SKU_s|Produkt-SKU für SQL Managed Instance |
|virtual_core_count_s|Anzahl verfügbarer virtueller Kerne |
|avg_cpu_percent_s|Durchschnittlicher CPU-Prozentsatz |
|reserved_storage_mb_s|Reservierte Speicherkapazität für die verwaltete Instanz |
|storage_space_used_mb_s|Verwendeter Speicher für die verwaltete Instanz |
|io_requests_s|Anzahl von IOPS |
|io_bytes_read_s|Gelesene IOPS-Bytes |
|io_bytes_written_s|Geschriebene IOPS-Bytes |

#### <a name="query-store-runtime-statistics"></a>Laufzeitstatistiken für den Abfragespeicher

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|type|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category|Name der Kategorie Immer: QueryStoreRuntimeStatistics |
|Vorgangsname|Name des Vorgangs. Immer: QueryStoreRuntimeStatisticsEvent |
|Resource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|resourceId|Ressourcen-URI |
|query_hash_s|Abfragehash |
|query_plan_hash_s|Hash des Abfrageplans |
|statement_sql_handle_s|SQL-Anweisungshandle |
|interval_start_time_d|Beginn des DateTimeOffsets des Intervalls in der Anzahl von Takten ab 1900-1-1 |
|interval_end_time_d|Ende des DateTimeOffsets des Intervalls in der Anzahl von Takten ab 1900-1-1 |
|logical_io_writes_d|Gesamtanzahl von logischen E/A-Schreibvorgängen |
|max_logical_io_writes_d|Maximale Anzahl von logischen E/A-Schreibvorgängen pro Ausführung |
|physical_io_reads_d|Gesamtanzahl von physischen E/A-Lesevorgängen |
|max_physical_io_reads_d|Maximale Anzahl von logischen E/A-Lesevorgängen pro Ausführung |
|logical_io_reads_d|Gesamtanzahl von logischen E/A-Lesevorgängen |
|max_logical_io_reads_d|Maximale Anzahl von logischen E/A-Lesevorgängen pro Ausführung |
|execution_type_d|Ausführungstyp |
|count_executions_d|Anzahl von Ausführungen der Abfrage |
|cpu_time_d|Gesamte von der Abfrage verbrauchte CPU-Zeit in Mikrosekunden |
|max_cpu_time_d|Maximale von einer einzelnen Ausführung verbrauchte CPU-Zeit in Mikrosekunden |
|dop_d|Summe des Grads an Parallelität |
|max_dop_d|Maximaler Grad an Parallelität für eine einzelne Ausführung |
|rowcount_d|Gesamtanzahl zurückgegebener Zeilen |
|max_rowcount_d|Maximale Anzahl zurückgegebener Zeilen in einer einzelnen Ausführung |
|query_max_used_memory_d|Gesamtmenge des verwendeten Arbeitsspeichers in KB |
|max_query_max_used_memory_d|Maximale von einer einzelnen Ausführung verwendete Menge an Arbeitsspeicher in KB |
|duration_d|Gesamtausführungszeit in Mikrosekunden |
|max_duration_d|Maximale Ausführungszeit einer einzelnen Ausführung |
|num_physical_io_reads_d|Gesamtanzahl von physischen Lesevorgängen |
|max_num_physical_io_reads_d|Maximale Anzahl von physischen Lesevorgängen pro Ausführung |
|log_bytes_used_d|Gesamtanzahl verwendeter Protokollbytes |
|max_log_bytes_used_d|Maximale Anzahl verwendeter Protokollbytes pro Ausführung |
|query_id_d|ID der Abfrage im Abfragespeicher |
|plan_id_d|ID des Plans im Abfragespeicher |

Weitere Informationen zu [Laufzeitstatistikdaten des Abfragespeichers](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Wartestatistiken des Abfragespeichers

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|type|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category|Name der Kategorie Immer: QueryStoreWaitStatistics |
|Vorgangsname|Name des Vorgangs. Immer: QueryStoreWaitStatisticsEvent |
|Resource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|resourceId|Ressourcen-URI |
|wait_category_s|Kategorie des Wartevorgangs |
|is_parameterizable_s|Parametrisierbarkeit der Abfrage |
|statement_type_s|Anweisungstyp |
|statement_key_hash_s|Schlüsselhash der Anweisung |
|exec_type_d|Ausführungstyp |
|total_query_wait_time_ms_d|Gesamte Wartezeit der Abfrage für die spezifische Wartekategorie |
|max_query_wait_time_ms_d|Maximale Wartezeit der Abfrage in einer einzelnen Ausführung der bestimmten Wartekategorie |
|query_param_type_d|0 |
|query_hash_s|Abfragehash im Abfragespeicher |
|query_plan_hash_s|Hash des Abfrageplans im Abfragespeicher |
|statement_sql_handle_s|Anweisungshandle im Abfragespeicher |
|interval_start_time_d|Beginn des DateTimeOffsets des Intervalls in der Anzahl von Takten ab 1900-1-1 |
|interval_end_time_d|Ende des DateTimeOffsets des Intervalls in der Anzahl von Takten ab 1900-1-1 |
|count_executions_d|Anzahl von Ausführungen der Abfrage |
|query_id_d|ID der Abfrage im Abfragespeicher |
|plan_id_d|ID des Plans im Abfragespeicher |

Weitere Informationen zu [Wartestatistikdaten des Abfragespeichers](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Fehlerdataset

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|type|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category|Name der Kategorie Immer: Errors |
|Vorgangsname|Name des Vorgangs. Immer: ErrorEvent |
|Resource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|resourceId|Ressourcen-URI |
|`Message`|Fehlermeldungen in Nur-Text |
|user_defined_b|Benutzerdefiniertes Fehlerbit |
|error_number_d|Fehlercode |
|severity|Schweregrad des Fehlers |
|state_d|Status des Fehlers |
|query_hash_s|Der Abfragehash der fehlerhaften Abfrage (falls verfügbar) |
|query_plan_hash_s|Hash des Abfrageplans der fehlerhaften Abfrage (falls verfügbar) |

Weitere Informationen zu [SQL-Fehlermeldungen](/sql/relational-databases/errors-events/database-engine-events-and-errors).

#### <a name="database-wait-statistics-dataset"></a>Dataset zur Wartestatistik der Datenbank

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|type|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category|Name der Kategorie Immer: DatabaseWaitStatistics |
|Vorgangsname|Name des Vorgangs. Immer: DatabaseWaitStatisticsEvent |
|Resource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|resourceId|Ressourcen-URI |
|wait_type_s|Name des Wartetyps |
|start_utc_date_t [UTC]|Startzeit des gemessenen Zeitraums |
|end_utc_date_t [UTC]|Endzeit des gemessenen Zeitraums |
|delta_max_wait_time_ms_d|Maximale Wartezeit pro Ausführung |
|delta_signal_wait_time_ms_d|Gesamte Signalwartezeit |
|delta_wait_time_ms_d|Gesamtwartezeit im Zeitraum |
|delta_waiting_tasks_count_d|Anzahl von wartenden Aufgaben |

Weitere Informationen zu [Datenbankwartestatistiken](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Dataset zu Timeouts

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|type|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category|Name der Kategorie Immer: Zeitlimits |
|Vorgangsname|Name des Vorgangs. Immer: TimeoutEvent |
|Resource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|resourceId|Ressourcen-URI |
|error_state_d|Fehlerstatuscode |
|query_hash_s|Abfragehash (falls verfügbar) |
|query_plan_hash_s|Hash des Abfrageplans (falls verfügbar) |

#### <a name="blockings-dataset"></a>Dataset der Blockierungen

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|type|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category|Name der Kategorie Immer: Blöcke |
|Vorgangsname|Name des Vorgangs. Immer: BlockEvent |
|Resource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|resourceId|Ressourcen-URI |
|lock_mode_s|Von der Abfrage verwendeter Sperrmodus |
|resource_owner_type_s|Besitzer der Sperre |
|blocked_process_filtered_s|XML-Bericht über blockierte Prozesse |
|duration_d|Die Dauer der Sperre in Millisekunden |

#### <a name="deadlocks-dataset"></a>Dataset zu Deadlocks

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC] |Zeitstempel für die Aufzeichnung des Protokolls |
|type|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category|Name der Kategorie Immer: Deadlocks |
|Vorgangsname|Name des Vorgangs. Immer: DeadlockEvent |
|Resource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|resourceId|Ressourcen-URI |
|deadlock_xml_s|XML-Deadlockbericht |

#### <a name="automatic-tuning-dataset"></a>Dataset zur automatischen Optimierung

|Eigenschaft|BESCHREIBUNG|
|---|---|
|TenantId|Ihre Mandanten-ID |
|SourceSystem|Immer: Azure |
|TimeGenerated [UTC]|Zeitstempel für die Aufzeichnung des Protokolls |
|type|Immer: AzureDiagnostics |
|ResourceProvider|Name des Ressourcenanbieters Immer: MICROSOFT.SQL |
|Category|Name der Kategorie Immer: AutomaticTuning |
|Resource|Name der Ressource |
|ResourceType|Name des Ressourcentyps Immer: SERVERS/DATABASES |
|SubscriptionId|Abonnement-GUID für die Datenbank |
|ResourceGroup|Name der Ressourcengruppe für die Datenbank |
|LogicalServerName_s|Name des Servers für die Datenbank |
|LogicalDatabaseName_s|Name der Datenbank |
|ElasticPoolName_s|Name des Pools für elastische Datenbanken für die Datenbank (sofern vorhanden) |
|DatabaseName_s|Name der Datenbank |
|resourceId|Ressourcen-URI |
|RecommendationHash_s|Eindeutiger Hash der Empfehlung zur automatischen Optimierung |
|OptionName_s|Vorgang der automatischen Optimierung |
|Schema_s|Datenbankschema |
|Table_s|Betroffene Tabelle |
|IndexName_s|Indexname |
|IndexColumns_s|Spaltenname |
|IncludedColumns_s|Enthaltene Spalten |
|EstimatedImpact_s|Geschätzte Auswirkung der JSON der Empfehlung zur automatischen Optimierung |
|Event_s|Typ des Ereignisses der automatischen Optimierung |
|Timestamp_t|Zeitstempel der letzten Aktualisierung |

#### <a name="intelligent-insights-dataset"></a>Dataset von Intelligent Insights

Weitere Informationen zum [Protokollformat von Intelligent Insights](intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Aktivieren der Protokollierung und zu den Metriken und Protokollkategorien, die von den verschiedenen Azure-Diensten unterstützt werden, finden Sie in den folgenden Themen:

- [Überblick über Metriken in Microsoft Azure](../../azure-monitor/platform/data-platform.md)
- [Übersicht über Protokolle der Azure-Plattform](../../azure-monitor/platform/platform-logs-overview.md)

Weitere Informationen zu Event Hubs finden Sie hier:

- [Was ist Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Erste Schritte mit Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

Informationen zum Einrichten von Warnungen auf der Grundlage von Telemetriedaten aus Log Analytics finden Sie hier:

- [Erstellen von Warnungen für Azure SQL-Datenbank und Azure SQL Managed Instance](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)