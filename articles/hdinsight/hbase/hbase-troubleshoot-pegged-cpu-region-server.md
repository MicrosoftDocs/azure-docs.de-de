---
title: 'Voll ausgelastete CPU im Apache HBase-Cluster: Azure HDInsight'
description: Problembehandlung bei voll ausgelastetem CPU auf Regionsserver im Apache HBase-Cluster in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: bed73c3ccc7f514ffc9ff8f97534ae4b249834ce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017014"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Szenario: Voll ausgelastete CPU auf Regionsserver im Apache HBase-Cluster in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Der Apache HBase-Regionsserverprozess beginnt mit dem Belegen bei einer CPU-Auslastung von fast 200%. Daraufhin werden Warnungen im HBase Master-Prozess und -Cluster ausgelöst werden, dass sie nicht mit voller Kapazität funktionieren.

## <a name="cause"></a>Ursache

Wenn Sie HBase-Cluster v3.4 ausführen, ist vielleicht ein potenzieller Fehler aufgetreten, der durch ein Upgrade des JDK auf Version 1.7.0_151 verursacht wurde. Das aufgetretene Symptom ist: Der Regionsserverprozess beginnt mit dem Belegen bei einer CPU-Auslastung von fast 200%. (Um dies zu überprüfen, führen Sie den Befehl `top` aus. Wenn ein Prozess fast 200% der CPU belegt, rufen Sie dessen PID ab, und bestätigen Sie, dass es sich um einen Regionsserverprozess handelt, indem Sie `ps -aux | grep` ausführen).

## <a name="resolution"></a>Lösung

1. Installieren Sie JDK 1.8 auf ALLEN Knoten des Clusters wie unten beschrieben:

    * Führen Sie die Skriptaktion `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` aus. Wählen Sie die Option aus, die auf allen Knoten ausgeführt werden soll.

    * Alternativ können Sie sich bei jedem einzelnen Knoten anmelden und den Befehl `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` ausführen.

1. Wechseln Sie zur Ambari-Benutzeroberfläche – `https://<clusterdnsname>.azurehdinsight.net`.

1. Navigieren Sie zu **HBase > Configs > Advanced > Advanced** `hbase-env configs`, und ändern Sie die Variable `JAVA_HOME` in `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`. Speichern Sie die Konfigurationsänderung.

1. [Optional, aber empfohlen] [Leeren Sie alle Tabellen im Cluster](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. Starten Sie alle HBase-Dienste, die neu gestartet werden müssen, über die Ambari-Benutzeroberfläche neu.

1. Je nach den Daten im Cluster kann es einige Minuten bis zu einer Stunde dauern, bis der Cluster einen stabilen Zustand erreicht. Sie können bestätigen, dass der Cluster einen stabilen Zustand erreicht hat, indem Sie entweder die HMaster-Benutzeroberfläche (alle Regionsserver sollten aktiv sein) von Ambari aus überprüfen (aktualisieren) oder indem Sie HBase-Shell auf dem Hauptknoten ausführen und dann den Befehl „status“ ausführen.

Um sicherzustellen, dass Ihr Upgrade erfolgreich war, überprüfen Sie, ob die relevanten HBase-Prozesse mit der entsprechenden Java-Version gestartet werden, z.B. beim Regionsserver:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]