---
title: Fehler durch Apache Hive Zeppelin-Interpreter – Azure HDInsight
description: Der Apache Zeppelin Hive-JDBC-Interpreter zeigt auf die falsche URL in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8e66db96f895d0d29d6c5018478e98c1f39e62ad
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288710"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Szenario: Apache Hive Zeppelin-Interpreter löst einen ZooKeeper-Fehler in Azure HDInsight aus

Dieser Artikel beschreibt Schritte zur Problembehandlung sowie mögliche Lösungen für Probleme bei der Verwendung von Interactive Query-Komponenten in Azure HDInsight-Clustern.

## <a name="issue"></a>Problem

In einem Apache Hive LLAP-Cluster gibt der Zeppelin-Interpreter beim Versuch, eine Abfrage auszuführen, die folgende Fehlermeldung aus:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Ursache

Der Zeppelin Hive-JDBC-Interpreter zeigt auf die falsche URL.

## <a name="resolution"></a>Lösung

1. Navigieren Sie zur Hive-Komponentenzusammenfassung, und kopieren Sie die „Hive JDBC Url“ in die Zwischenablage.

1. Navigieren Sie zu `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`.

1. Bearbeiten Sie die JDBC-Einstellungen: Aktualisieren Sie den Wert von hive.url in die in Schritt 1 kopierte Hive JDBC URL.

1. Speichern Sie die URL, und wiederholen Sie die Abfrage.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]