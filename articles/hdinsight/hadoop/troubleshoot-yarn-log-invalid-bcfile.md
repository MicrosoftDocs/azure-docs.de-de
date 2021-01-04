---
title: Apache Yarn-Protokoll nicht lesbar in Azure HDInsight
description: Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: b6bd7d807916ef53177b11df6ed9ce0b22f530be
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533341"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Szenario: Apache Yarn-Protokoll nicht lesbar in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Die im Speicherkonto aufgefundenen Apache Yarn-Protokolle sind nicht für Menschen lesbar. Der Dateiparser funktioniert nicht und gibt die folgende Fehlermeldung aus:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Ursache

Das Apache Yarn-Protokoll wird im Format `IndexFile` aggregiert, das vom Dateiparser nicht unterstützt wird.

## <a name="resolution"></a>Lösung

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

1. Navigieren Sie über die Ambari-Benutzeroberfläche zu **YARN** > **Configs** > **Advanced** > **Advanced yarn-site** (YARN > Konfigurationen > Erweitert > Erweiterte Yarn-Website).

1. Für WASB-Speicher: Der Standardwert für `yarn.log-aggregation.file-formats` ist `IndexedFormat,TFile`. Ändern Sie den Wert in `TFile`.

1. Für ADLS-Speicher: Der Standardwert für `yarn.nodemanager.log-aggregation.compression-type` ist `gz`. Ändern Sie den Wert in `none`.

1. Speichern Sie die Änderung, und starten Sie alle betroffenen Dienste neu.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.