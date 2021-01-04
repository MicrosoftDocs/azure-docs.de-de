---
title: Portkonflikt beim Starten von Diensten in Azure HDInsight
description: Problembehandlungsschritte und mögliche Lösungen für Portkonflikte, wenn Azure HDInsight-Cluster beteiligt sind.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: c07cddb0999e6f3424527828be4b10ed0168a2ff
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533324"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>Szenario: Portkonflikt beim Starten von Diensten in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Ein Dienst kann nicht gestartet werden.

## <a name="cause"></a>Ursache

Es besteht ein Portkonflikt.

## <a name="resolution"></a>Lösung

### <a name="method-1"></a>Methode 1

Verwenden Sie die folgenden Befehle, um alle laufenden Prozesse, die von dem Portproblem betroffen sind, abzurufen bzw. zu beenden.

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

Starten Sie dann den Dienst.

### <a name="method-2"></a>Methode 2

Starten Sie den Knoten neu.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.