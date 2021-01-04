---
title: Verwendung von Apache Ambari in Azure HDInsight
description: In diesem Artikel wird die Verwendung von Apache Ambari in Azure HDInsight erläutert.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: cfb83c4ec9972fda3813d414583bc73edeef3229
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285970"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Verwendung von Apache Ambari in Azure HDInsight

HDInsight verwendet Apache Ambari für die Bereitstellung und Verwaltung von Clustern. Ambari-Agents werden auf jedem Knoten ausgeführt: Hauptknoten, Workerknoten, Zookeeper-Knoten und Edgeknoten, sofern vorhanden. Der Ambari-Server wird nur auf dem Hauptknoten („hn0“ oder „hn1“) ausgeführt. Es kann immer nur eine Instanz des Ambari-Servers ausgeführt werden. Dies wird durch den HDInsight-Failovercontroller gesteuert. Wenn einer der Hauptknoten zum Zweck eines Neustarts oder für Wartungsmaßnamen heruntergefahren wurde, wird der andere Hauptknoten aktiv, und der Ambari-Server auf dem zweiten Hauptknoten wird gestartet.

Die gesamte Clusterkonfiguration sollte über die [Ambari-Benutzeroberfläche](./hdinsight-hadoop-manage-ambari.md) erfolgen, alle lokalen Änderungen werden beim Neustart des Knotens überschrieben.

## <a name="failover-controller-services"></a>Failovercontrollerdienste

Der HDInsight-Failovercontroller ist auch für die Aktualisierung der IP-Adresse des Hauptknotenhosts zuständig, die auf den aktuellen aktiven Hauptknoten zeigt. Alle Ambari-Agents sind so konfiguriert, dass sie den Zustand und Takt des Controllers an den Hauptknotenhost melden. Der Failovercontroller besteht aus einer Reihe von Diensten, die auf jedem Knoten im Cluster ausgeführt werden. Wenn diese Dienste nicht ausgeführt werden, funktioniert das Hauptknotenfailover möglicherweise nicht ordnungsgemäß, und Sie erhalten einen HTTP 502-Fehler, wenn Sie versuchen, auf den Ambari-Server zuzugreifen.

Eine Möglichkeit zum Überprüfen, welcher Hauptknoten aktiv ist, besteht darin, eine SSH-Verbindung mit einem der Knoten im Cluster herzustellen, dann `ping headnodehost` auszuführen und die IP-Adresse mit den Adressen der beiden Hauptknoten zu vergleichen.

Wenn die Failovercontrollerdienste nicht ausgeführt werden, erfolgt das Hauptknotenfailover möglicherweise nicht ordnungsgemäß, was dazu führen kann, dass der Ambari-Server nicht ausgeführt wird. Um zu überprüfen, ob die Failovercontrollerdienste ausgeführt werden, führen Sie Folgendes aus:

```bash
ps -ef | grep failover
```

## <a name="logs"></a>Protokolle

Auf dem aktiven Hauptknoten können Sie die Ambari-Serverprotokolle hier überprüfen:

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

Auf jedem anderen Knoten im Cluster können Sie die Ambari-Agent-Protokolle hier überprüfen:

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>Dienststartsequenzen

Zum Zeitpunkt des Systemstarts werden die Dienste in dieser Reihenfolge gestartet:

1. Der HDInsight-Agent startet die Failovercontrollerdienste.
1. Die Failovercontrollerdienste starten die Ambari-Agents auf allen Knoten und den Ambari-Server auf dem aktiven Hauptknoten.

## <a name="ambari-database"></a>Ambari-Datenbank

HDInsight erstellt hinter den Kulissen eine Datenbank in SQL-Datenbank als Datenbank für den Ambari-Server. Die standardmäßige [Dienstebene ist S0](../azure-sql/database/elastic-pool-scale.md).

Bei allen Clustern, die bei der Erstellung mehr als 16 Workerknoten umfassen, ist S2 die Dienstebene für die Datenbank.

## <a name="takeaway-points"></a>Die wichtigsten Punkte

Starten oder beenden Sie die Dienste für Ambari-Server oder Ambari-Agents niemals manuell, es sei denn, Sie versuchen, die Dienste zur Umgehung eines Problems neu zu starten. Um ein Failover zu erzwingen, können Sie den aktiven Hauptknoten neu starten.

Ändern Sie die Konfigurationsdateien auf den Clusterknoten niemals manuell – das wird auf der Ambari-Benutzeroberfläche erledigt.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md)
* [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md)

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]