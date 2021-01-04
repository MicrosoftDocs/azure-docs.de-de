---
title: Apache Ambari-Heartbeatprobleme in Azure HDInsight
description: Bewertung verschiedene Gründe für Apache Ambari-Heartbeatprobleme in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: adc5dfcef8cce269b6b6d982178433b8ee163f92
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998266"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Apache Ambari-Heartbeatprobleme in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="scenario-high-cpu-utilization"></a>Szenario: Hohe CPU-Auslastung

### <a name="issue"></a>Problem

Beim Ambari-Agent gibt es eine hohe CPU-Auslastung. Dies führt zu Warnungen von der Ambari-Benutzeroberfläche, dass einige Knoten keinen Heartbeat des Ambari-Agents mehr empfangen. Die Warnung zum Verlust des Heartbeats ist normalerweise vorübergehend.

### <a name="cause"></a>Ursache

In seltenen Fällen kann es aufgrund verschiedener Ambari-Agent-Fehler bei Ihrem Agent eine hohe CPU-Auslastung (nahezu 100 %) geben.

### <a name="resolution"></a>Lösung

1. Identifizieren Sie die Prozess-ID (PID) des Ambari-Agents:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Führen Sie anschließend den folgenden Befehl aus, um die CPU-Auslastung anzuzeigen:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Starten Sie Ambari-Agent neu, um das Problem zu beheben:

    ```bash
    service ambari-agent restart
    ```

1. Wenn der Neustart nicht funktioniert, beenden Sie den Ambari-Agent-Prozess, und starten Sie ihn dann:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Szenario: Ambari-Agent wurde nicht gestartet

### <a name="issue"></a>Problem

Der Ambari-Agent wurde nicht gestartet. Dies führt zu Warnungen von der Ambari-Benutzeroberfläche, dass einige Knoten keinen Heartbeat des Ambari-Agents mehr empfangen.

### <a name="cause"></a>Ursache

Die Warnungen werden dadurch verursacht, dass der Ambari-Agent nicht ausgeführt wird.

### <a name="resolution"></a>Lösung

1. Bestätigen Sie den Status von Ambari-Agent:

    ```bash
    service ambari-agent status
    ```

1. Vergewissern Sie sich, dass die Failovercontrollerdienste ausgeführt werden:

    ```bash
    ps -ef | grep failover
    ```

    Wenn die Failovercontrollerdienste nicht ausgeführt werden, liegt wahrscheinlich ein Problem vor, das den Start des Failovercontrollers durch den HDInsight-Agent verhindert. Überprüfen Sie das Protokoll des HDInsight-Agents aus der Datei `/var/log/hdinsight-agent/hdinsight-agent.out`.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Szenario: Verlust des Heartbeats bei Ambari

### <a name="issue"></a>Problem

Es wird kein Heartbeat des Ambari-Agents mehr empfangen.

### <a name="cause"></a>Ursache

Die OMS-Protokolle führen zu einer hohen CPU-Auslastung.

### <a name="resolution"></a>Lösung

* Deaktivieren Sie die Azure Monitor-Protokollierung über das PowerShell-Cmdlet [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring).
* Löschen Sie die Protokolldatei `mdsd.warn`.

---

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]