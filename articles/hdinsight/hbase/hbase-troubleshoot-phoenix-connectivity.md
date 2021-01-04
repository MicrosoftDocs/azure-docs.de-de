---
title: Konnektivitätsprobleme bei Apache Phoenix in Azure HDInsight
description: Konnektivitätsprobleme zwischen Apache HBase und Apache Phoenix in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: ed12c9629506fa8defb23b987fe672bb3b384418
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540090"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Szenario: Konnektivitätsprobleme bei Apache Phoenix in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Verbindung von Apache Phoenix mit Apache HBase kann nicht hergestellt werden Die Gründe können variieren.

## <a name="cause-incorrect-ip"></a>Ursache: Falsche IP

Falsche IP-Adresse des aktiven ZooKeeper-Knotens.

### <a name="resolution"></a>Lösung

Die IP-Adresse des aktiven ZooKeeper-Knotens kann auf der Ambari-Benutzeroberfläche über die Links zu **HBase** > **Quick Links** > **ZK (Active)**  > **Zookeeper Info** ermittelt werden. Korrigieren Sie die IP-Adresse, falls erforderlich.

---

## <a name="cause-systemcatalog-table-offline"></a>Ursache: Die Tabelle „SYSTEM.CATALOG“ ist offline.

Wenn Sie Befehle wie `!tables` ausführen, erhalten Sie eine Fehlermeldung ähnlich der folgenden:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

Wenn Sie Befehle wie `count 'SYSTEM.CATALOG'` ausführen, erhalten Sie eine Fehlermeldung ähnlich der folgenden:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Lösung

Führen Sie auf der Apache Ambari-Benutzeroberfläche die folgenden Schritte aus, um den HMaster-Dienst auf allen ZooKeeper-Knoten neu zu starten:

1. Wechseln Sie im Abschnitt **Summary** von HBase zu **HBase** > **Active HBase Master** .

1. Starten Sie im Abschnitt **Components** den HBase Master-Dienst neu.

1. Wiederholen Sie diese Schritte für die verbleibenden **Standby HBase Master** -Dienste.

Es kann bis zu fünf Minuten dauern, bis der HBase Master-Dienst stabilisiert und die Wiederherstellung abgeschlossen ist. Sobald die Tabelle `SYSTEM.CATALOG` sich wieder in einem normalen Zustand befindet, sollte das Konnektivitätsproblem mit Apache Phoenix automatisch gelöst werden.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support** . Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.