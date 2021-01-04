---
title: Timeouts mit dem Befehl „hbase hbck“ in Azure HDInsight
description: Timeoutproblem mit dem Befehl „hbase hbck“ beim Korrigieren von Regionszuweisungen
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: c1269f159a23d5fd23e08e7abfbd9a05ddd533ba
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539937"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Szenario: Timeouts mit dem Befehl „hbase hbck“ in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Timeouts mit dem Befehl `hbase hbck` beim Korrigieren von Regionszuweisungen.

## <a name="cause"></a>Ursache

Eine mögliche Ursache für Timeoutprobleme beim Verwenden des Befehls `hbck` kann darin bestehen, dass sich verschiedene Regionen über einen langen Zeitraum im „Übergangszustand“ befinden. Diese Regionen werden in der HBase-Master-Benutzeroberfläche als offline angezeigt. Aufgrund der großen Anzahl von Regionen, die den Übergang versuchen, könnte ein HBase Master-Timeout aufgetreten sein, sodass HBase Master diese Regionen nicht wieder online schalten kann.

## <a name="resolution"></a>Lösung

1. Melden Sie sich mithilfe von SSH beim HDInsight-HBase-Cluster an.

1. Führen Sie den Befehl `hbase zkcli` aus, um eine Verbindung mit der Apache ZooKeeper-Shell herzustellen.

1. Führen Sie den Befehl `rmr /hbase/regions-in-transition` oder `rmr /hbase-unsecure/regions-in-transition` aus.

1. Verwenden Sie den Befehl `exit`, um die Shell `hbase zkcli` zu beenden.

1. Starten Sie den Active HBase Master-Dienst aus der Apache Ambari-Benutzeroberfläche neu.

1. Führen Sie den Befehl `hbase hbck -fixAssignments` aus.

1. Überwachen Sie auf der HBase Master-Benutzeroberfläche den Abschnitt mit den Regionen, für die ein Übergang vorgesehen ist, um sich zu vergewissern, dass keine Region hängen bleibt.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

- Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

- Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

- Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.