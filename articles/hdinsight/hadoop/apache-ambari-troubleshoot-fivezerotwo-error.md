---
title: 'Apache Ambari-Benutzeroberfläche: Fehler 502 in Azure HDInsight'
description: 'Apache Ambari-Benutzeroberfläche: Fehler 502 beim Zugriff auf den Azure HDInsight-Cluster'
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: a512afa719c29976a9126afb67de4a0e6c80763d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998293"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Szenario: Apache Ambari-Benutzeroberfläche: Fehler 502 in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Wenn Sie versuchen, auf die Apache Ambari-Benutzeroberfläche für Ihren HDInsight-Cluster zuzugreifen, erhalten Sie eine Meldung ähnlich der folgenden: „502 – Webserver hat als Gateway oder Proxyserver eine ungültige Antwort erhalten.“

## <a name="cause"></a>Ursache

Im Allgemeinen bedeutet der HTTP-Statuscode 502, dass der Ambari-Server nicht ordnungsgemäß auf dem aktiven Hauptknoten ausgeführt wird. Dafür gibt es verschiedene mögliche Ursachen.

## <a name="resolution"></a>Lösung

In den meisten Fällen können Sie den aktiven Hauptknoten neu starten, um das Problem zu beheben. Sie können auch eine größere VM-Größe für Ihren Hauptknoten auswählen.

### <a name="ambari-server-failed-to-start"></a>Ambari-Server konnte nicht gestartet werden.

Sie können die Ambari-Serverprotokolle überprüfen, um herauszufinden, warum der Ambari-Server nicht gestartet werden konnte. Ein häufiger Grund ist ein Fehler bei der Konsistenzprüfung der Datenbank. Sie können dies in der Protokolldatei `/var/log/ambari-server/ambari-server-check-database.log` überprüfen.

Wenn Sie Änderungen am Clusterknoten vorgenommen haben, machen Sie diese rückgängig. Verwenden Sie immer die Ambari-Benutzeroberfläche, um Konfigurationen für Hadoop/Spark zu ändern.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari-Server verbraucht 100 % der CPU-Auslastung.

In seltenen Fällen nutzt der Ambari-Serverprozess dauerhaft fast 100 % der CPU-Auslastung. Als Abhilfemaßnahme können Sie per SSH auf den aktiven Hauptknoten zugreifen und den Ambari-Serverprozess beenden und erneut starten.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari-Server von oom-killer beendet

In einigen Fällen steht dem Hauptknoten nicht mehr genügend Arbeitsspeicher zur Verfügung, und oom-killer unter Linux beginnt mit der Auswahl der zu beendenden Prozesse. Sie können diese Situation überprüfen, indem Sie nach der Prozess-ID des Ambari-Servers suchen, die in diesem Fall nicht gefunden werden sollte. Suchen Sie dann in `/var/log/syslog` etwa nach Folgendem:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Ermitteln Sie dann, welche Prozesse Speicher verwenden, und versuchen Sie, die Grundursache zu finden.

### <a name="other-issues-with-ambari-server"></a>Andere Probleme mit dem Ambari-Server

In seltenen Fällen kann der Ambari-Server die eingehende Anforderung nicht verarbeiten. Weitere Informationen erhalten Sie, indem Sie in den Ambari-Serverprotokollen nach Fehlern suchen. Ein solcher Fall ist z. B. ein Fehler wie der folgende:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]