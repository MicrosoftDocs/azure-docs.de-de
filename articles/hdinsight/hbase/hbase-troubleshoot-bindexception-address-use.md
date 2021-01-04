---
title: 'BindException: Adresse wird in Azure HDInsight bereits verwendet.'
description: 'BindException: Adresse wird in Azure HDInsight bereits verwendet.'
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 60f95292d517277f607c968629c55641e0eecce1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540260"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Szenario: BindException: Adresse wird in Azure HDInsight bereits verwendet.

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Der Neustartvorgang auf einem Apache HBase-Regionsserver kann nicht abgeschlossen werden. In `region-server.log` im Verzeichnis `/var/log/hbase` auf den Workerknoten, bei denen der Start des Regionsservers fehlschlägt, können Sie eine Fehlermeldung ähnlich der folgenden finden:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Ursache

Neustarten von Apache HBase-Regionsservern bei hoher Workloadaktivität. Nachstehend wird erklärt, was passiert, wenn ein Benutzer in der Apache Ambari-Benutzeroberfläche den Neustartvorgang auf HBase-Regionsservern auslöst:

1. Der Ambari-Agent sendet eine Beendigungsanforderung an den Regionsserver.

1. Der Ambari-Agent wartet 30 Sekunden auf das ordnungsgemäße Herunterfahren des Regionsservers.

1. Wenn Ihre Anwendung weiterhin eine Verbindung mit dem Regionsserver unterhält, wird der Server nicht sofort heruntergefahren. Das Zeitlimit von 30 Sekunden läuft ab, bevor der Server heruntergefahren wird.

1. Nach 30 Sekunden sendet der Ambari-Agent einen Befehl zum Erzwingen des Beendens (`kill -9`) an den Regionsserver.

1. Aufgrund dieses abrupten Herunterfahrens, wenngleich der Regionsserverprozess beendet wird, wird der mit dem Prozess verbundene Port möglicherweise nicht freigegeben, was schließlich zu `AddressBindException` führt.

## <a name="resolution"></a>Lösung

Reduzieren Sie die Last auf den HBase-Regionsservern, bevor Sie einen Neustart einleiten. Zudem empfiehlt es sich, zuvor alle Tabellen zu leeren. Eine Referenz zum Leeren von Tabellen finden Sie unter [HDInsight HBase: Verbessern der Neustartzeit für den Apache HBase-Cluster durch Leeren der Tabellen](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Alternativ können Sie versuchen, Regionsserver auf den Workerknoten mithilfe folgender Befehle manuell neu zu starten:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.