---
title: Migrieren von Azure HDInsight 3.6-Hive-Workloads zu HDInsight 4.0
description: Erfahren Sie, wie Sie Apache Hive-Workloads in HDInsight 3.6 zu HDInsight 4.0 migrieren.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/24/2019
ms.openlocfilehash: b39279e560cb1738ff9b33ec587562efd2ed4e8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800952"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrieren von Azure HDInsight 3.6-Hive-Workloads zu HDInsight 4.0

In diesem Dokument erfahren Sie, wie Sie Apache Hive- und LLAP-Workloads in HDInsight 3.6 zu HDInsight 4.0 migrieren. HDInsight 4.0 bietet neuere Hive- und LLAP-Features, beispielsweise materialisierte Sichten und die Zwischenspeicherung von Abfrageergebnissen. Wenn Sie Ihre Workloads zu HDInsight 4.0 migrieren, können Sie viele neuere Funktionen von Hive 3 nutzen, die nicht in HDInsight 3.6 verfügbar sind.

In diesem Artikel werden die folgenden Themen behandelt:

* Migration von Hive-Metadaten zu HDInsight 4.0
* Sichere Migration von ACID- und Nicht-ACID-Tabellen
* Beibehaltung von Hive-Sicherheitsrichtlinien in verschiedenen HDInsight-Versionen
* Abfrageausführung und Debuggen von HDInsight 3.6 in HDInsight 4.0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrieren von Apache Hive-Metadaten zu HDInsight 4.0

Ein Vorteil von Hive ist die Möglichkeit, Metadaten in eine externe Datenbank zu exportieren (bezeichnet als Hive-Metastore). Der **Hive-Metastore** ist für das Speichern von Tabellenstatistiken zuständig, einschließlich des Tabellenspeicherorts, der Spaltennamen und der Indexinformationen der Tabelle. Das Datenbankschema des Metastores unterscheidet sich je nach Hive-Version. Gehen Sie wie im Folgenden beschrieben vor, um einen HDInsight 3.6-Hive-Metastore so zu aktualisieren, dass er mit HDInsight 4.0 kompatibel ist.

1. Erstellen Sie eine neue Kopie Ihres externen Metastores. HDInsight 3.6 und HDInsight 4.0 erfordern unterschiedliche Schemas für den Metastore. Die gemeinsame Nutzung eines einzelnen Metastores ist daher nicht möglich. Weitere Informationen zum Anfügen eines externen Metastores an einen HDInsight-Cluster finden Sie unter [Verwenden von externen Metadatenspeichern in Azure HDInsight](../hdinsight-use-external-metadata-stores.md). 
2. Starten Sie eine Skriptaktion für Ihren HDI 3.6-Cluster, in der Sie „Head nodes“ als Knotentyp für die Ausführung angeben. Fügen Sie den folgenden URI in das Textfeld „Bash-Skript-URI“ ein: https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh. Geben Sie in das Textfeld „Argumente“ den Servernamen, die Datenbank, den Benutzernamen und das Kennwort für den **kopierten** Hive-Metastore durch Leerzeichen getrennt ein. Beziehen Sie bei der Angabe des Servernamens nicht „.database.windows.net“ ein.

> [!Warning]
> Das Upgrade zum Konvertieren des HDInsight 3.6-Metadatenschemas in das HDInsight 4.0-Schema kann nicht rückgängig gemacht.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrieren von Hive-Tabellen zu HDInsight 4.0

Nachdem Sie die obigen Schritte zum Migrieren des Hive-Metastores zu HDInsight 4.0 durchgeführt haben, können Sie die im Metastore gespeicherten Tabellen und Datenbanken im HDInsight 4.0-Cluster anzeigen, indem Sie `show tables` oder `show databases` im Cluster ausführen. Informationen zur Abfrageausführung in HDInsight 4.0-Clustern finden Sie unter [Abfrageausführung in verschiedenen HDInsight-Versionen](#query-execution-across-hdinsight-versions).

Auf die eigentlichen Daten in den Tabellen kann jedoch erst zugegriffen werden, wenn der Cluster Zugriff auf die erforderlichen Speicherkonten hat. Führen Sie die folgenden Schritte aus, um sicherzustellen, dass Ihr HDInsight 4.0-Cluster auf die gleichen Daten zugreifen kann wie der alte HDInsight 3.6-Cluster:

1. Ermitteln Sie mithilfe einer DESCRIBE FORMATTED-Abfrage das Azure-Speicherkonto der Tabelle bzw. Datenbank.
2. Wenn Ihr HDInsight 4.0-Cluster bereits ausgeführt wird, fügen Sie das Azure-Speicherkonto über Ambari an den Cluster an. Falls Sie den HDInsight 4.0-Cluster noch nicht erstellt haben, vergewissern Sie sich, dass das Azure-Speicherkonto als primäres oder sekundäres Clusterspeicherkonto angegeben ist. Weitere Informationen zum Hinzufügen von Speicherkonten zu HDInsight-Clustern finden Sie unter [Hinzufügen zusätzlicher Speicherkonten zu HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tabellen werden in HDInsight 3.6 und HDInsight 4.0 unterschiedlich behandelt. Aus diesem Grund ist es nicht möglich, die gleichen Tabellen für Cluster verschiedener Versionen gemeinsam zu nutzen. Wenn Sie HDInsight 3.6 und HDInsight 4.0 gleichzeitig verwenden möchten, benötigen Sie für jede Version separate Kopien der Daten.

Ihre Hive-Workload kann eine Mischung von ACID- und Nicht-ACID-Tabellen (Atomicity, Consistency, Isolation, Durability = Atomarität, Konsistenz, Isolation, Dauerhaftigkeit) enthalten. Ein wesentlicher Unterschied zwischen Hive in HDInsight 3.6 (Hive 2) und Hive in HDInsight 4.0 (Hive 3) ist die ACID-Konformität für Tabellen. In HDInsight 3.6 erfordert Hive-ACID-Konformität eine zusätzliche Konfiguration, in HDInsight 4.0 sind Tabellen jedoch standardmäßig ACID-konform. Die einzige erforderliche Aktion vor der Migration ist das Durchführen einer größeren Komprimierung für die ACID-Tabelle im 3.6-Cluster. Führen Sie in der Hive-Ansicht oder in Beeline die folgende Abfrage aus:

```bash
alter table myacidtable compact 'major';
```

Diese Komprimierung ist erforderlich, weil ACID-Deltas von ACID-Tabellen in HDInsight 3.6 und HDInsight 4.0 unterschiedlich interpretiert werden. Die Komprimierung erzwingt einen kompletten Neuanfang, der Konsistenz garantiert. Abschnitt 4 der [Dokumentation zur Hive-Migration](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) enthält eine Anleitung für die Massenkomprimierung von ACID-Tabellen in HDInsight 3.6.

Nachdem Sie die Schritte zur Migration des Metastores und Komprimierung durchgeführt haben, können Sie das eigentliche Warehouse migrieren. Nach der Migration des Hive-Warehouse weist das HDInsight 4.0-Warehouse die folgenden Eigenschaften auf:

* Externe Tabellen in HDInsight 3.6 sind externe Tabellen in HDInsight 4.0.
* Nicht transaktionale verwaltete Tabellen in HDInsight 3.6 sind externe Tabellen in HDInsight 4.0.
* Transaktionale verwaltete Tabellen in HDInsight 3.6 sind verwaltete Tabellen in HDInsight 4.0.

Möglicherweise müssen Sie die Eigenschaften Ihres Warehouse anpassen, bevor Sie die Migration durchführen. Wenn Sie beispielsweise davon ausgehen, dass Drittanwendungen oder -dienste (z. B. ein HDInsight 3.6-Cluster) auf eine Tabelle zugreifen, muss diese Tabelle nach Abschluss der Migration extern sein. In HDInsight 4.0 sind alle verwalteten Tabellen transaktional. Daher sollte nur von HDInsight 4.0-Clustern auf verwaltete Tabellen in HDInsight 4.0 zugegriffen werden.

Nachdem Sie Ihre Tabelleneigenschaften korrekt festgelegt haben, führen Sie das Migrationstool für Hive-Warehouses auf einem der Clusterhauptknoten über die SSH-Shell aus:

1. Stellen Sie über SSH eine Verbindung mit Ihrem Clusterhauptknoten her. Anweisungen hierzu finden Sie unter [Herstellen einer Verbindung mit HDInsight per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
1. Öffnen Sie als Hive-Benutzer eine Anmelde-Shell, indem Sie `sudo su - hive` ausführen.
1. Führen Sie `ls /usr/hdp` aus, um die Version des Hortonworks Data Platform-Stapels zu ermitteln. In der Ausgabe wird eine Versionszeichenfolge angezeigt, die Sie im nächsten Befehl verwenden.
1. Führen Sie den folgenden Befehl über die Shell aus. Ersetzen Sie `${{STACK_VERSION}}` durch die Versionszeichenfolge aus dem vorherigen Schritt:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

Nach Abschluss der Ausführung des Migrationstools ist Ihr Hive-Warehouse bereit für HDInsight 4.0. 

> [!Important]
> Auf verwaltete Tabellen in HDInsight 4.0 (einschließlich Tabellen, die von 3.6 migriert wurden) sollten keine anderen Dienste oder Anwendungen zugreifen, auch keine HDInsight 3.6-Cluster.

## <a name="secure-hive-across-hdinsight-versions"></a>Sichern von Hive in verschiedenen HDInsight-Versionen

Seit HDInsight 3.6 ist HDInsight in Azure Active Directory und das HDInsight-Enterprise-Sicherheitspaket (ESP) integriert. Das Enterprise-Sicherheitspaket verwendet Kerberos und Apache Ranger, um die Berechtigungen bestimmter Ressourcen im Cluster zu verwalten. Für Hive in HDInsight 3.6 bereitgestellte Ranger-Richtlinien können wie folgt zu HDInsight 4.0 migriert werden:

1. Navigieren Sie in Ihrem HDInsight 3.6-Cluster zum Bereich „Ranger Service Manager“.
2. Navigieren Sie zur Richtlinie namens **HIVE**, und exportieren Sie die Richtlinie in eine JSON-Datei.
3. Stellen Sie sicher, dass alle Benutzer, auf die in der JSON-Datei mit der exportierten Richtlinie verwiesen wird, im neuen Cluster vorhanden sind. Wenn in der JSON-Datei mit der Richtlinie auf einen Benutzer verwiesen wird, der nicht im neuen Cluster vorhanden ist, fügen Sie den Benutzer dem neuen Cluster hinzu, oder entfernen Sie den Verweis aus der Richtlinie.
4. Navigieren Sie in Ihrem HDInsight 4.0-Cluster zum Bereich **Ranger Service Manager**.
5. Navigieren Sie zur Richtlinie namens **HIVE**, und importieren Sie die in Schritt 2 erstellte JSON-Datei mit der Ranger-Richtlinie.

## <a name="query-execution-across-hdinsight-versions"></a>Abfrageausführung in verschiedenen HDInsight-Versionen

Zum Ausführen und Debuggen von Hive-/LLAP-Abfragen in einem HDInsight 3.6-Cluster stehen zwei Möglichkeiten zur Verfügung. HiveCLI bietet eine Befehlszeilenumgebung, und die Tez-Ansicht/Hive-Ansicht stellt einen Workflow mit grafischer Benutzeroberfläche (Graphical User Interface, GUI) bereit.

In HDInsight 4.0 wurde HiveCLI durch Beeline ersetzt. HiveCLI ist ein Thrift-Client für Hive-Server 1, und Beeline ist ein JDBC-Client, der Zugriff auf Hive-Server 2 bietet. Beeline kann auch zum Herstellen einer Verbindung mit einem beliebigen anderen JDBC-kompatiblen Datenbankendpunkt verwendet werden. Beeline ist in HDInsight 4.0 standardmäßig verfügbar und erfordert keinerlei Installation.

In HDInsight 3.6 ist die Ambari-Hive-Ansicht der GUI-Client für die Interaktion mit dem Hive-Server. In HDInsight 4.0 wurde die Hive-Ansicht durch Hortonworks Data Analytics Studio (DAS) ersetzt. DAS ist nicht standardmäßig in HDInsight-Clustern enthalten und kein offiziell unterstütztes Paket. DAS kann jedoch wie folgt auf dem Cluster installiert werden:

Starten Sie eine Skriptaktion für Ihren Cluster, in der Sie „Head nodes“ als Knotentyp für die Ausführung angeben. Fügen Sie den folgenden URI in das Textfeld „Bash-Skript-URI“ ein: https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Data Analytics Studio kann über die URL „https://<clustername>.azurehdinsight.net/das/“ gestartet werden.



Falls die von Ihnen ausgeführten Abfragen nach der Installation von DAS nicht in der Abfrageanzeige angezeigt werden, führen Sie die folgenden Schritte aus:

1. Legen Sie die Konfigurationen für Hive, Tez und DAS wie in diesem [Leitfaden zur Problembehandlung für die DAS-Installation](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html) beschrieben fest.
2. Stellen Sie sicher, dass die folgenden Azure-Speicherverzeichniskonfigurationen Seitenblobs und unter `fs.azure.page.blob.dirs` aufgelistet sind:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Starten Sie auf beiden Hauptknoten Hadoop Distributed File System, Hive, Tez und DAS neu.

## <a name="next-steps"></a>Nächste Schritte

* [Ankündigung von HDInsight 4.0](../hdinsight-version-release.md)
* [HDInsight 4.0 deep dive](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/) (Ausführliche Informationen zu HDInsight 4.0)
* [Hive 3-ACID-Tabellen](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
