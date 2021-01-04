---
title: Verwalten von Speicherplatz in Azure HDInsight
description: Problembehandlungsschritte und mögliche Lösungen für Probleme beim Verwalten von Speicherplatz, wenn mit Azure HDInsight-Clustern gearbeitet wird.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 82044796cca3e135c15e750d6706fe504622ebdb
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540345"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Verwalten von Speicherplatz in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="hive-log-configurations"></a>Hive-Protokollkonfigurationen

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

1. Navigieren Sie zu **Hive** > **Configs** > **Advanced** > **Advanced hive-log4j** . Überprüfen Sie die folgenden Einstellungen:

    * `hive.root.logger=DEBUG,RFA`. Dies ist der Standardwert. Ändern Sie die [Protokollebene](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) in `INFO`, um weniger Protokolleinträge zu drucken.

    * `log4jhive.log.maxfilesize=1024MB`. Dies ist der Standardwert. Ändern Sie ihn nach Bedarf.

    * `log4jhive.log.maxbackupindex=10`. Dies ist der Standardwert. Ändern Sie ihn nach Bedarf. Wenn der Parameter ausgelassen wurde, werden endlose Protokolldateien generiert.

## <a name="yarn-log-configurations"></a>YARN-Protokollkonfigurationen

Überprüfen Sie die folgenden Konfigurationen:

* Apache Ambari

    1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

    1. Navigieren Sie zu **Hive** > **Configs** > **Advanced** > **Resource Manager** . Stellen Sie sicher, dass **Enable Log Aggregation** (Protokollaggregation aktivieren) aktiviert ist. Wenn diese Option deaktiviert ist, speichern Namenknoten die Protokolle lokal und aggregieren diese bei der Anwendungsbeendigung nicht in einem Remotespeicher.

* Stellen Sie sicher, dass die Clustergröße für die Workload geeignet ist. Möglicherweise hat sich die Workload oder die Größe des Cluster vor Kurzem geändert. [Skalieren](../hdinsight-scaling-best-practices.md) Sie den Cluster für eine höhere Workload hoch.

* Möglicherweise ist `/mnt/resource` mit verwaisten Dateien gefüllt (wie beim Resource Manager-Neustart). Führen Sie bei Bedarf eine manuelle Bereinigung von `/mnt/resource/hadoop/yarn/log` und `/mnt/resource/hadoop/yarn/local` durch.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.