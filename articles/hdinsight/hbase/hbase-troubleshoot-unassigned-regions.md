---
title: Probleme mit Regionsservern in Azure HDInsight
description: Probleme mit Regionsservern in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 6225e51a7707a926df91e68fbb27bfb56f89a3ee
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539920"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Probleme mit Regionsservern in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="scenario-unassigned-regions"></a>Szenario: Nicht zugewiesene Regionen

### <a name="issue"></a>Problem

Bei der Ausführung des Befehls `hbase hbck` wird in etwa die folgende Fehlermeldung angezeigt:

```
multiple regions being unassigned or holes in the chain of regions
```

In der HBase Master-Benutzeroberfläche von Apache wird die Anzahl der Regionen angezeigt, die über alle Regionsserver hinweg nicht ausgeglichen sind. Sie können dann den Befehl `hbase hbck` ausführen, um Lücken in der Kette der Regionen anzuzeigen.

### <a name="cause"></a>Ursache

Der Grund für Lücken können Regionen im Offlinezustand sein.

### <a name="resolution"></a>Lösung

Korrigieren Sie die Zuweisungen. Bringen Sie die nicht zugewiesenen Regionen mit folgenden Schritten wieder in den normalen Zustand:

1. Melden Sie sich mithilfe von SSH beim HDInsight-HBase-Cluster an.

1. Führen Sie den Befehl `hbase zkcli` aus, um eine Verbindung mit der ZooKeeper-Shell herzustellen.

1. Führen Sie den Befehl `rmr /hbase/regions-in-transition` oder `rmr /hbase-unsecure/regions-in-transition` aus.

1. Beenden Sie die ZooKeeper-Shell mit dem Befehl `exit`.

1. Öffnen Sie die Apache Ambari-Benutzeroberfläche, und starten Sie anschließend den Active HBase-Master-Dienst neu.

1. Führen Sie den Befehl `hbase hbck` erneut aus (ohne weitere Optionen). Überprüfen Sie die Ausgabe, und vergewissern Sie sich, dass alle Regionen zugewiesen sind.

---

## <a name="scenario-dead-region-servers"></a>Szenario: Inaktive Regionsserver

### <a name="issue"></a>Problem

Regionsserver können nicht gestartet werden.

### <a name="cause"></a>Ursache

Mehrere WAL-Aufteilungsverzeichnisse.

1. Liste der aktuellen WAL-Verzeichnisse abrufen: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. Sehen Sie sich die Datei `wals.out` an. Wenn zu viele Aufteilungsverzeichnisse vorhanden sind (beginnen mit „*-splitting“), tritt für den Regionsserver vermutlich aufgrund dieser Verzeichnisse ein Fehler auf.

### <a name="resolution"></a>Lösung

1. Beenden Sie HBase über das Ambari-Portal.

1. Führen Sie `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` aus, um eine aktualisierte Liste mit WAL-Verzeichnissen zu erhalten.

1. Verschieben Sie die „*-splitting“-Verzeichnisse in den temporären Ordner `splitWAL`, und löschen Sie die „*-splitting“-Verzeichnisse.

1. Führen Sie den Befehl `hbase zkcli` aus, um eine Verbindung mit der ZooKeeper-Shell herzustellen.

1. Führen Sie `rmr /hbase-unsecure/splitWAL` aus.

1. Starten Sie den HBase-Dienst neu.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.