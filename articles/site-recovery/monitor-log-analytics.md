---
title: Überwachen von Azure Site Recovery mit Azure Monitor-Protokollen
description: Erfahren Sie, wie Sie Azure Site Recovery mit Azure Monitor-Protokollen (Log Analytics) überwachen.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: e3d3ce8218030bc8ba6c59b26b7360bf2299e02a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499814"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Überwachen von Site Recovery mit Azure Monitor-Protokollen

In diesem Artikel wird beschrieben, wie Sie die von [Azure Site Recovery](site-recovery-overview.md) replizierten Computer mithilfe von [Azure Monitor-Protokollen](../azure-monitor/platform/data-platform-logs.md) und [Log Analytics](../azure-monitor/log-query/log-query-overview.md) überwachen.

Azure Monitor-Protokolle bieten eine Protokolldatenplattform, die Aktivitäts- und Ressourcenprotokolle zusammen mit anderen Überwachungsdaten sammelt. Sie können in Azure Monitor-Protokollen Protokollabfragen mit Log Analytics schreiben und testen und Protokolldaten interaktiv analysieren. Sie können die Protokollergebnisse visualisieren und abfragen, und Sie können Warnungen konfigurieren, damit Aktionen basierend auf überwachten Daten durchgeführt werden.

Für Site Recovery können Sie Azure Monitor-Protokolle für folgende Aufgaben verwenden:

- **Überwachen von Integrität und Status von Site Recovery:** Sie können z. B. die Replikationsintegrität, den Testfailoverstatus, Site Recovery-Ereignisse, RPOs (Recovery Point Objectives, Wiederherstellungspunktziele) für geschützte Computer und die Änderungsraten von Datenträgern/Daten überwachen.
- **Einrichten von Warnungen für Site Recovery:** Beispielsweise können Sie Warnungen für die Computerintegrität oder für den Testfailoverstatus oder Site Recovery-Aufträge konfigurieren.

Die Verwendung von Azure Monitor-Protokollen mit Site Recovery wird für die **Azure-zu-Azure-Replikation** und die **Replikation von VMware-VMs/physischen Servern zu Azure** unterstützt.

> [!NOTE]
> Um die Änderungsdaten- und Uploadratenprotokolle für VMware- und physische Computer zu erhalten, müssen Sie einen Microsoft Monitoring Agent auf dem Prozessserver installieren. Dieser Agent sendet die Protokolle der replizierenden Computer an den Arbeitsbereich. Diese Funktion ist erst ab der Version 9.30 des des Mobilitäts-Agents verfügbar.

## <a name="before-you-start"></a>Vorbereitung

Sie benötigen Folgendes:

- Mindestens einen Computer, der in einem Recovery Services-Tresor geschützt ist
- Einen Log Analytics-Arbeitsbereich zum Speichern von Site Recovery-Protokollen. Erfahren Sie mehr über das [Einrichten eines Arbeitsbereichs](../azure-monitor/learn/quick-create-workspace.md).
- Grundlegende Kenntnisse zum Schreiben, Ausführen und Analysieren von Protokollabfragen in Log Analytics. [Weitere Informationen](../azure-monitor/log-query/log-analytics-tutorial.md)

Es wird empfohlen, vor dem Start die [allgemeinen Fragen zur Überwachung](monitoring-common-questions.md) zu lesen.

## <a name="configure-site-recovery-to-send-logs"></a>Konfigurieren von Site Recovery für das Senden von Protokollen

1. Klicken Sie im Tresor auf **Diagnoseeinstellungen** > **Diagnoseeinstellung hinzufügen**.

    ![Screenshot: Option „Diagnoseeinstellung hinzufügen“](./media/monitoring-log-analytics/add-diagnostic.png)

2. Geben Sie unter **Diagnoseeinstellungen** einen Namen an, und aktivieren Sie das Kontrollkästchen **An Log Analytics senden**.
3. Wählen Sie das Abonnement der Azure Monitor-Protokolle und den Log Analytics-Arbeitsbereich aus.
4. Wählen mithilfe des Umschalters die Option **Azure-Diagnose** aus.
5. Wählen Sie in der Protokollliste alle Protokolle mit dem Präfix **AzureSiteRecovery** aus. Klicken Sie dann auf **OK**.

    ![Screenshot: Bildschirm für Diagnoseeinstellung](./media/monitoring-log-analytics/select-workspace.png)

Die Site Recovery-Protokolle werden nun in eine Tabelle (**AzureDiagnostics**) im ausgewählten Arbeitsbereich übertragen.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Konfigurieren des Microsoft Monitoring Agents auf dem Prozessserver zum Senden von Änderungsdaten- und Uploadratenprotokollen

Sie können die Änderungsdateninformationen und die Quelldaten-Uploadrateninformationen für Ihre VMware-/physischen Computer lokal erfassen. Dazu muss ein Microsoft Monitoring Agent auf dem Prozessserver installiert sein.

1. Wechseln Sie zum Log Analytics-Arbeitsbereich, und klicken Sie auf **Erweiterte Einstellungen**.
2. Klicken Sie auf die Seite **Verbundene Quellen**, und wählen Sie dann **Windows-Server** aus.
3. Laden Sie den Windows-Agent (64 Bit) auf den Prozessserver herunter. 
4. [Abrufen von Arbeitsbereichs-ID und Schlüssel](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)
5. [Konfigurieren des Agents für die Verwendung von TLS 1.2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. [Schließen Sie die Installation des Agents ab](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard), indem Sie die erhaltene Arbeitsbereichs-ID und den Schlüssel angeben.
7. Nachdem die Installation abgeschlossen ist, wechseln Sie in den Log Analytics-Arbeitsbereich, und klicken Sie auf **Erweiterte Einstellungen**. Wechseln Sie zur Seite **Daten**, und klicken Sie dann auf **Windows-Leistungsindikatoren**. 
8. Klicken Sie auf **„+“** , um die folgenden beiden Indikatoren mit dem Stichprobenintervall von 300 Sekunden hinzuzufügen:

    - ASRAnalytics(*)\SourceVmChurnRate
    - ASRAnalytics(*)\SourceVmThrpRate

Die Änderungs- und Uploadratendaten werden in den Arbeitsbereich eingegeben.


## <a name="query-the-logs---examples"></a>Abfragen der Protokolle – Beispiele

Daten aus Protokollen werden mithilfe von Protokollabfragen abgerufen, die mit der [Abfragesprache Kusto](../azure-monitor/log-query/get-started-queries.md) geschrieben werden. Dieser Abschnitt enthält mehrere Beispiele für allgemeine Abfragen, die Sie für die Site Recovery-Überwachung verwenden können.

> [!NOTE]
> In einigen Beispielen wird **replicationProviderName_s** auf **A2A** festgelegt. Hierdurch werden Azure-VMs abgerufen, die mithilfe von Site Recovery in eine sekundäre Azure-Region repliziert werden. In diesen Beispielen können Sie **A2A** durch **InMageAzureV2** ersetzen, wenn Sie lokale virtuelle VMware-Computer oder physische Server abrufen möchten, die mithilfe von Site Recovery nach Azure repliziert werden.


### <a name="query-replication-health"></a>Abfragen der Replikationsintegrität

Diese Abfrage zeichnet ein Kreisdiagramm der derzeitigen Replikationsintegrität aller geschützten Azure-VMs, das in drei Zustände unterteilt ist: Normal, Warnung und Kritisch.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Abfragen der Mobility Service-Version

Diese Abfrage zeichnet ein Kreisdiagramm der Azure-VMs, die mit Site Recovery repliziert wurden, unterteilt nach der ausgeführten Version des Mobility-Agents.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Abfragen der RPO-Zeit

Diese Abfrage zeichnet ein Balkendiagramm der Azure-VMs, die mit Site Recovery repliziert wurden, unterteilt nach RPO (Recovery Point Objective, Wiederherstellungspunktziel): Weniger als 15 Minuten, 15–30 Minuten, mehr als 30 Minuten.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Screenshot: Balkendiagramm der Azure-VMs, die mit Site Recovery repliziert wurden](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Abfragen von Site Recovery-Aufträgen

Diese Abfrage ruft alle Site Recovery-Aufträge (für alle Notfallwiederherstellungs-Szenarien) ab, die in den letzten 72 Stunden ausgelöst wurden, sowie deren Abschlussstatus.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Abfragen von Site Recovery-Ereignissen

Diese Abfrage ruft alle Site Recovery-Ereignisse (für alle Notfallwiederherstellungs-Szenarien) ab, die in den letzten 72 Stunden ausgelöst wurden, sowie deren Schweregrad. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Abfragen des Testfailoverstatus (Kreisdiagramm)

Diese Abfrage zeichnet ein Kreisdiagramm des Testfailoverstatus von Azure-VMs, die mit Site Recovery repliziert wurden.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Abfragen des Testfailoverstatus (Tabelle)

Diese Abfrage erstellt eine Tabelle des Testfailoverstatus von Azure-VMs, die mit Site Recovery repliziert wurden.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Abfragen des Computer-RPO

Diese Abfrage zeichnet ein Trenddiagramm, das das RPO einer bestimmten Azure-VM (ContosoVM123) für die letzten 72 Stunden nachverfolgt.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Screenshot: Trenddiagramm zur Nachverfolgung des RPO einer bestimmten Azure-VM](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Abfragen der Datenänderungsrate (Churn) und der Uploadrate für eine Azure-VM

Diese Abfrage zeichnet ein Trenddiagramm für eine bestimmte Azure-VM (ContosoVM123), das die Datenänderungsrate (geschriebene Bytes pro Sekunde) und die Datenuploadrate darstellt. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Screenshot: Trenddiagramm für eine bestimmte Azure-VM](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Abfragen der Datenänderungsrate (Churn) und der Uploadrate für einen VMware- oder physischen Computer

> [!Note]
> Stellen Sie sicher, dass Sie den Überwachungs-Agent auf dem Prozessserver einrichten, um diese Protokolle abzurufen. Weitere Informationen finden Sie unter [Schritte zum Konfigurieren des Überwachungs-Agents](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Diese Abfrage zeichnet ein Trenddiagramm für einen bestimmten Datenträger (**disk0**) eines replizierten Elements (**win-9r7sfh9qlru**), das die Datenänderungsrate (geschriebene Bytes pro Sekunde) und die Datenuploadrate darstellt. Sie finden den Datenträgernamen auf dem Blatt **Datenträger** des replizierten Elements im Recovery Services-Tresor. Der in der Abfrage zu verwendende Instanzenname ist der DNS-Name des Computers, gefolgt von „_“ und dem Datenträgernamen wie in diesem Beispiel.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
Der Prozessserver pusht diese Daten alle 5 Minuten in den Log Analytics-Arbeitsbereich. Diese Datenpunkte stellen den Durchschnitt dar, der für 5 Minuten berechnet wurde.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Abfragen der Zusammenfassung zur Notfallwiederherstellung (Azure-zu-Azure)

Diese Abfrage zeichnet eine Tabelle mit einer Zusammenfassung von Azure-VMs, die in eine sekundäre Azure-Region repliziert werden.  Diese enthält den Namen des virtuellen Computers, den Replikationsstatus, das RPO, den Testfailoverstatus, die Version des Mobility-Agents, alle aktiven Replikationsfehler und den Ausgangsstandort.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Abfragen der Zusammenfassung einer Notfallwiederherstellung (VMware/physische Server)

Diese Abfrage zeichnet eine Tabelle mit der Zusammenfassung für VMware-VMs und physische Server, die nach Azure repliziert wurden.  Sie enthält den Namen des Computers, den Replikations- und Schutzstatus, das RPO, den Testfailoverstatus, die Version des Mobility-Agents, alle aktiven Replikationsfehler und den zugehörigen Prozessserver.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Einrichten von Warnungen – Beispiele

Sie können Site Recovery Warnungen basierend auf Azure Monitor-Daten einrichten. Informieren Sie sich über das [Einrichten von Protokollwarnungen](../azure-monitor/platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal). 

> [!NOTE]
> In einigen Beispielen wird **replicationProviderName_s** auf **A2A** festgelegt. Hierdurch werden Warnungen für Azure-VMs eingerichtet, die in eine sekundäre Azure-Region repliziert werden. In diesen Beispielen können Sie **A2A** durch **InMageAzureV2** ersetzen, wenn Sie Warnungen für lokale VMware-VMs oder physische Server einrichten möchten, die nach Azure repliziert werden.

### <a name="multiple-machines-in-a-critical-state"></a>Mehrere Computer mit kritischem Status

Richten Sie eine Warnung ein, wenn mehr als 20 replizierte Azure-VMs einen kritischen Status aufweisen.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Legen Sie für die Warnung den **Schwellenwert** auf „20“ fest.

### <a name="single-machine-in-a-critical-state"></a>Einzelner Computer mit kritischem Status

Richten Sie eine Warnung ein, wenn eine bestimmte replizierte Azure-VM einen kritischen Status aufweist.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Legen Sie für die Warnung den **Schwellenwert** auf „1“ fest.

### <a name="multiple-machines-exceed-rpo"></a>Mehrere Computer über RPO

Richten Sie eine Warnung ein, wenn der RPO-Wert für mehr als 20 Azure-VMs 30 Minuten überschreitet.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Legen Sie für die Warnung den **Schwellenwert** auf „20“ fest.

### <a name="single-machine-exceeds-rpo"></a>Einzelner Computer über RPO

Richten Sie eine Warnung ein, wenn der RPO-Wert für eine einzelne Azure-VM 30 Minuten überschreitet.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Legen Sie für die Warnung den **Schwellenwert** auf „1“ fest.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Testfailover für mehrere Computer überschreitet 90 Tage

Richten Sie eine Warnung ein, wenn das letzte erfolgreiche Testfailover für mehr als 20 virtuelle Computer länger als 90 Tage zurück liegt. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Legen Sie für die Warnung den **Schwellenwert** auf „20“ fest.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Testfailover für einen Computer überschreitet 90 Tage

Richten Sie eine Warnung ein, wenn das letzte erfolgreiche Testfailover für einen bestimmten virtuellen Computer länger als 90 Tage zurück liegt.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Legen Sie für die Warnung den **Schwellenwert** auf „1“ fest.

### <a name="site-recovery-job-fails"></a>Fehler bei Site Recovery-Auftrag

Richten Sie eine Warnung ein, wenn ein Site Recovery-Auftrag (in diesem Fall der Auftrag zum erneuten Schützen) für ein beliebiges Site Recovery-Szenario während des letzten Tages zu einem Fehler geführt hat. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Legen Sie für die Warnung den **Schwellenwert** auf „1“ und den **Zeitraum** auf 1.440 Minuten fest, um alle Fehler des letzten Tags zu überprüfen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die integrierte [Site Recovery Überwachung](site-recovery-monitor-and-troubleshoot.md).