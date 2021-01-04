---
title: Hochverfügbarkeit mit Apache Kafka – Azure HDInsight
description: Erfahren Sie, wie mit Apache Kafka in Azure HDInsight Hochverfügbarkeit sichergestellt wird. Erfahren Sie, wie Partitionsreplikate in Kafka ausgeglichen werden, damit sie sich in verschiedenen Fehlerdomänen innerhalb der Azure-Region befinden, die HDInsight enthält.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 4504463fa35d5fececbb2ab5895de2965ca4e647
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534412"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Hochverfügbarkeit Ihrer Daten mit Apache Kafka in HDInsight

Erfahren Sie, wie Partitionsreplikate für Apache Kafka-Themen für die Nutzung der zugrunde liegenden Konfiguration des Hardwareracks konfiguriert werden. Diese Konfiguration stellt die Verfügbarkeit von Daten sicher, die in Apache Kafka in HDInsight gespeichert sind.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Fehler- und Updatedomänen bei Apache Kafka

Eine Fehlerdomäne ist eine logische Gruppierung von zugrundeliegender Hardware in einem Azure-Rechenzentrum. Jede Fehlerdomäne verwendet eine Stromquelle und einen Netzwerkswitch gemeinsam. Die virtuellen Computer und verwalteten Datenträger, die die Knoten innerhalb eines HDInsight-Clusters implementieren, werden auf diese Fehlerdomänen verteilt. Diese Architektur schränkt die potenziellen Auswirkungen physischer Hardwarefehler ein.

Jede Azure-Region weist eine bestimmte Anzahl von Fehlerdomänen auf. Eine Liste der Domänen und die Anzahl der Fehlerdomänen, die sie enthalten, finden Sie in der Dokumentation zu [Verfügbarkeitsgruppen](../../virtual-machines/availability.md#availability-sets).

> [!IMPORTANT]  
> Fehlerdomänen sind Kafka nicht bekannt. Beim Erstellen eines Themas in Kafka werden u.U. alle Partitionsreplikate in der gleichen Fehlerdomäne gespeichert. Zur Lösung dieses Problems stellt HDInsight das [Tool zum Ausgleichen von Kafka-Partitionen](https://github.com/hdinsight/hdinsight-kafka-tools) bereit.

## <a name="when-to-rebalance-partition-replicas"></a>Wann sollten Partitionsreplikate ausgeglichen werden?

Um die höchste Verfügbarkeit Ihrer Kafka-Daten sicherzustellen, sollten Sie die Partitionsreplikate für Ihr Thema zu folgenden Zeitpunkten ausgleichen:

* Wenn ein neues Thema oder eine neue Partition erstellt wird

* Wenn Sie einen Cluster hochskalieren

## <a name="replication-factor"></a>Replikationsfaktor

> [!IMPORTANT]  
> Es wird empfohlen, eine Azure-Region mit drei Fehlerdomänen und den Replikationsfaktor 3 zu verwenden.

Wenn Sie eine Region verwenden müssen, die nur zwei Fehlerdomänen enthält, verwenden Sie den Replikationsfaktor 4, um die Replikate gleichmäßig auf die zwei Fehlerdomänen zu verteilen.

Ein Beispiel zum Erstellen von Themen und zum Festlegen des Replikationsfaktors finden Sie im Dokument [Erste Schritte mit Apache Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="how-to-rebalance-partition-replicas"></a>Ausgleichen von Partitionsreplikaten

Verwenden Sie das [Tool zum Ausgleichen von Apache Kafka-Partitionen](https://github.com/hdinsight/hdinsight-kafka-tools), um ausgewählte Themen auszugleichen. Dieses Tool muss über eine SSH-Sitzung für den Hauptknoten des Kafka-Clusters ausgeführt werden.

Weitere Informationen zum Herstellen einer Verbindung mit HDInsight mithilfe von SSH finden Sie im Dokument [Verwenden von SSH mit HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Nächste Schritte

* [Skalierbarkeit von Apache Kafka in HDInsight](apache-kafka-scalability.md)
* [Spiegelung mit Apache Kafka in HDInsight](apache-kafka-mirroring.md)