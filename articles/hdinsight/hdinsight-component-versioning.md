---
title: Apache Hadoop-Komponenten und -Versionen – Azure HDInsight
description: Lernen Sie die Apache Hadoop-Komponenten und -Versionen in Azure HDInsight kennen.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 08/13/2020
ms.openlocfilehash: d1f4c57986f356f267448ff43de23973534b8126
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100006665"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Für Azure HDInsight verfügbare Apache-Komponenten und -Versionen

In diesem Artikel erfahren Sie mehr über die Komponenten und Versionen der [Apache Hadoop](https://hadoop.apache.org/)-Umgebung in Azure HDInsight und über das Enterprise-Sicherheitspaket. Darüber hinaus erfahren Sie, wie Sie die Hadoop-Komponentenversionen in HDInsight überprüfen.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Verfügbare Apache-Komponenten in verschiedenen Versionen von HDInsight

Azure HDInsight unterstützt mehrere Hadoop-Clusterversionen, die jederzeit bereitgestellt werden können. Seit dem 4. April 2017 war die von Azure HDInsight derzeit als Standardclusterversion verwendete Version die Version 3.6.

Die den HDInsight-Clusterversionen zugeordneten Komponentenversionen sind in der folgenden Tabelle aufgeführt.

> [!NOTE]
> Die Standardversion für den HDInsight-Dienst kann ohne vorherige Ankündigung geändert werden. Wenn eine Versionsabhängigkeit vorliegt, geben Sie die HDInsight-Version an, wenn Sie Ihre Cluster mit dem .NET SDK mit Azure PowerShell und der klassischen Azure-Befehlszeilenschnittstelle erstellen.

| Komponente              | HDInsight 4.0 | HDInsight 3.6 (Standard)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop und YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.1        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 bei ESP Interactive Query) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.1.6         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.4.4         | 2.3.2.                      |
| Apache Livy            | 0.5           | 0.4.                        |
| Apache Kafka           | 2.1.1         | 1.1                         |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4.0: Spark 2.4 und Kafka 2.1 werden vollständig unterstützt. Die Clustertypen Spark 2.3 und Kafka 1.1 werden jedoch nicht unterstützt. HDInsight 3.6: Spark 2.3 und Kafka 1.1 werden vollständig unterstützt.  

## <a name="check-for-current-apache-component-version-information"></a>Überprüfen der Informationen zur aktuellen Apache-Komponentenversion

Die mit den HDInsight-Clusterversionen verknüpften Komponentenversionen der Hadoop-Umgebung können sich bei Updates für HDInsight ändern. Um die Hadoop-Komponenten zu überprüfen und zu ermitteln, welche Versionen für einen Cluster verwendet werden, verwenden Sie die Ambari-REST-API. Mit dem Befehl **GetComponentInformation** rufen Sie Informationen zu Dienstkomponenten ab. Weitere Informationen finden Sie in der [Apache Ambari-Dokumentation](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Versionshinweise

In den [HDInsight-Versionshinweisen](hdinsight-release-notes.md) finden Sie zusätzliche Anmerkungen zu den aktuellen Versionen von HDInsight.

## <a name="supported-hdinsight-versions"></a>Unterstützte HDInsight-Versionen

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Supportablauf und Deaktivierung von HDInsight-Versionen

**Supportablauf** bedeutet, dass Microsoft für die jeweilige HDInsight-Version keinen Support mehr bereitstellt. Die Version ist zudem nicht mehr über das Azure-Portal für die Clustererstellung verfügbar. Diese Versionen können weiterhin mithilfe der Azure CLI oder der verschiedenen SDKs erstellt werden.

**Deaktivierung** bedeutet, dass vorhandene Cluster einer HDInsight-Version weiterhin unverändert ausgeführt werden. Neue Cluster dieser Version können jedoch nicht erstellt werden – auch nicht mithilfe der CLI und SDKs. Auch andere Funktionen auf Steuerungsebene wie die manuelle und automatische Skalierung könnten nach der Deaktivierung der Version möglicherweise nicht mehr verwendet werden. Für deaktivierte Versionen ist kein Support verfügbar.

In den folgenden Tabellen sind die Versionen von HDInsight aufgeführt. Der Ablauf der Unterstützung und die Deaktivierungstermine werden ebenfalls bereitgestellt, wenn sie bekannt sind.

### <a name="available-versions"></a>Verfügbare Versionen

Die Tabelle enthält die im Azure-Portal und über andere Bereitstellungsmethoden wie z.B. PowerShell und .NET SDK verfügbaren Versionen von HDInsight.

| HDInsight-Version | Betriebssystem des virtuellen Computers | Veröffentlichungsdatum | Supportablaufdatum | Deaktivierungstermin | Hochverfügbarkeit |  Verfügbarkeit im Azure-Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24. September 2018 | | |Ja |Ja |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4\. April 2017      | *30. Juni 2021 |30. Juni 2021 |Ja |Ja |

*Wir verlängern den Supportzeitraum für bestimmte HDInsight 3.6-Clustertypen.

| Clustertyp                    | Frameworkversion | Aktuelles Ablaufdatum für den Support        | Neues Ablaufdatum für den Support |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | 31. Dezember 2020                      | 30. Juni 2021               |
| HDInsight 3.6 Spark             | 2.3               | 31. Dezember 2020                      | 30. Juni 2021               |
| HDInsight 3.6 Spark             | 2.2               | Am 30. Juni 2020 eingestellt          |                             |
| HDInsight 3.6 Spark             | 2.1               | Am 30. Juni 2020 eingestellt          |                             |
| HDInsight 3.6 Kafka             | 1.1               | 31. Dezember 2020                      | 30. Juni 2021               |
| HDInsight 3.6 Kafka             | 1.0               | Am 30. Juni 2020 eingestellt         |                             |
| HDInsight 3.6 HBase             | 1.1               | 31. Dezember 2020                      | 30. Juni 2021               |
| HDInsight 3.6 Interactive Query | 2.1               | 31. Dezember 2020                      | 30. Juni 2021               |
| HDInsight 3.6 Storm             | 1.1               | 31. Dezember 2020                      | 30. Juni 2021               |
| HDInsight 3.6 ML Services      | 9.3               | 31. Dezember 2020                      | 31. Dezember 2020                |

> [!NOTE]
> Nachdem die Clusterunterstützung für eine Version abgelaufen ist, ist sie möglicherweise nicht mehr über das Azure-Portal verfügbar. In manchen Fällen stehen Clusterversionen bis zum Zeitpunkt ihrer Deaktivierung weiterhin über den **Version**-Parameter im Windows PowerShell-Befehl [New-AzHDInsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) und das .NET SDK zur Verfügung.

### <a name="retired-versions"></a>Eingestellte Versionen

Diese Tabelle enthält die Versionen von HDInsight, die derzeit nicht im Azure-Portal verfügbar sind.

| HDInsight-Version | HDP-Version | Betriebssystem des virtuellen Computers | Veröffentlichungsdatum | Supportablaufdatum | Deaktivierungstermin | Hochverfügbarkeit |  Verfügbarkeit im Azure-Portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30. September 2016 |5\. September 2017 |28. Juni 2018 |Ja |Nein |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29. März 2016 |29. Dezember 2016 |9\. Januar 2018 |Ja |Nein |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2\. Dezember 2015 |27. Juni 2016 |31. Juli 2018 |Ja |Nein |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2\. Dezember 2015 |27. Juni 2016 |31. Juli 2017 |Ja |Nein |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS oder Windows Server 2012 R2 |18. Februar 2015 |1\. März 2016 |1\. April 2017 |Ja |Nein |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |24. Juni 2014 |18. Mai 2015 |30. Juni 2016 |Ja |Nein |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |11. Februar 2014 |17. September 2014 |30. Juni 2015 |Ja |Nein |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |28. Oktober 2013 |12. Mai 2014 |31. Mai 2015 |Ja |Nein |
| HDInsight 1.6 |HDP 1.1 | |28. Oktober 2013 |26. April 2014 |31. Mai 2015 |Nein |Nein |

> [!NOTE]
> Hochverfügbare Cluster mit zwei Hauptknoten werden standardmäßig für HDInsight Version 2.1 und höher bereitgestellt. Für HDInsight Version 1.6-Cluster sind sie nicht verfügbar.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Die Vereinbarung zum Servicelevel (Service Level Agreement, SLA) für HDInsight-Clusterversionen

Die Vereinbarung zum Servicelevel ist als _Supportfenster_ definiert. Ein Supportfenster ist der Zeitraum, in dem eine HDInsight-Version vom Microsoft-Kundendienst und -Support unterstützt wird. Wenn das _Supportablaufdatum_ der Version verstrichen ist, befindet sich das HDInsight-Cluster außerhalb des Supportfensters. Supportablauf für HDInsight-Version X (nachdem eine neuere X+1-Version verfügbar ist) ist das spätere Datum von:

- **Formel 1:** Addieren Sie 180 Tage zum Releasedatum von HDInsight-Clusterversion X.
- **Formel 2:** Addieren Sie 90 Tage zum Releasedatum von HDInsight-Clusterversion X+1 im Azure-Portal.

Das _Deaktivierungsdatum_ ist das Datum, nach dem die Clusterversion nicht mehr in HDInsight erstellt werden kann. Seit dem 31. Juli 2017 kann die Größe eines HDInsight-Clusters nach dessen Deaktivierungsdatum nicht mehr geändert werden.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standardknotenkonfiguration und Standardgrößen von virtuellen Computern für Cluster

Weitere Informationen dazu, welche SKUs für virtuelle Computer für Ihren Cluster ausgewählt werden sollten, finden Sie in dem Artikel über [Details einer Azure HDInsight-Clusterkonfiguration](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten von Clustern in HDInsight mit Apache Hadoop, Spark, Kafka usw.](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise-Sicherheitspaket](./enterprise-security-package.md)
- [Hortonworks-Versionshinweise im Zusammenhang mit Azure HDInsight-Versionen](./hortonworks-release-notes.md)
- [Verwenden eines Windows-Computers mit Apache Hadoop in HDInsight](hdinsight-hadoop-windows-tools.md)


