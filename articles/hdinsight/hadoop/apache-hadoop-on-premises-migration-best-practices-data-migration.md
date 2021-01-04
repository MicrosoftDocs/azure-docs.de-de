---
title: 'Datenmigration: Lokales Apache Hadoop zu Azure HDInsight'
description: Lernen Sie bewährte Methoden für die Datenmigration zum Migrieren von lokalen Hadoop-Clustern zu Azure HDInsight kennen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: d2a2c734b256ad934b7a17d7cefd1783b406e766
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537183"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrieren lokaler Apache Hadoop-Cluster zu Azure HDInsight – Best Practices für die Datenmigration

Dieser Artikel enthält Empfehlungen für die Datenmigration zu Azure HDInsight. Er ist Teil einer Reihe von Artikeln, die bewährte Methoden für die Migration von lokalen Apache Hadoop-Systemen zu Azure HDInsight enthalten.

## <a name="migrate-on-premises-data-to-azure"></a>Migrieren lokaler Daten nach Azure

Es gibt im Wesentlichen zwei Optionen zum Migrieren von Daten vom lokalen System zur Azure-Umgebung:

* Datenübertragung über das Netzwerk mit TLS
    * Internet: Sie können Daten über eine normale Internetverbindung mit diversen Tools an den Azure-Speicher übertragen: Azure Storage-Explorer, AzCopy, Azure PowerShell und Azure-Befehlszeilenschnittstelle. Weitere Informationen finden Sie unter [Verschieben von Daten in und aus Azure Storage](../../storage/common/storage-choose-data-transfer-solution.md).

    * ExpressRoute: ExpressRoute ist ein Azure-Dienst, der es Ihnen ermöglicht, private Verbindungen zwischen Microsoft-Rechenzentren und Ihrer lokalen oder in einer Housingumgebung eingebetteten Infrastruktur zu erstellen. ExpressRoute-Verbindungen verlaufen nicht über das öffentliche Internet und bieten eine höhere Sicherheit, Zuverlässigkeit und Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet. Weitere Informationen finden Sie unter [Erstellen und Ändern einer ExpressRoute-Verbindung](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).

    * Data Box-Onlinedatenübertragung: Data Box Edge und Data Box Gateway sind Produkte zur Onlinedatenübertragung, die als Netzwerkspeichergateways zum Übertragen von Daten zwischen Ihrem Standort und Azure dienen. Data Box Edge, ein lokales Netzwerkgerät, überträgt Daten nach und aus Azure und verwendet KI-fähiges Edge Computing zur Datenverarbeitung. Data Box Gateway ist eine virtuelle Appliance mit Speichergatewayfunktionen. Weitere Informationen finden Sie unter [Azure Data Box-Dokumentation: Onlineübertragung](../../databox-online/index.yml).

* Offlinedatenversand

    Data Box-Offlinedatenübertragung: Wenn das Netzwerk keine Option ist, können Sie mit Data Box-, Data Box Disk- und Data Box Heavy-Geräten große Datenmengen nach Azure übertragen. Diese Geräte zur Offlinedatenübertragung werden zwischen Ihrer Organisation und dem Azure-Rechenzentrum verschickt. Die Geräte nutzen die AES-Verschlüsselung, um Ihre Daten während der Übertragung zu schützen, und werden nach dem Upload einem umfassenden Bereinigungsprozess unterzogen, um Ihre Daten sicher von den Geräten zu löschen. Weitere Informationen zu Data Box-Offlineübertragungsgeräten finden Sie unter [Dokumentation zu Azure Data Box: Offlineübertragung](../../databox/index.yml). Weitere Informationen zur Migration von Hadoop-Clustern finden Sie unter [Verwenden von Azure Data Box zum Migrieren von Daten aus einem lokalen Hadoop Distributed File System-Speicher zu Azure Storage](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

Die folgende Tabelle zeigt die ungefähre Dauer der Datenübertragung basierend auf der Datenmenge und der Netzwerkbandbreite. Verwenden Sie eine Data Box, wenn die Datenmigration mehr als drei Wochen dauern würde.

|Datenmenge | Netzwerkbandbreite<br>of<br>**45 MBit/s (T3)**|Netzwerkbandbreite<br>of<br>**100 MBit/s**|Netzwerkbandbreite<br>of<br>**1 GBit/s**|Netzwerkbandbreite<br>of<br>**10 GBit/s**|
|---|:---:|:---:|:---:|:---:|
|1 TB|2 Tage|1 Tag| 2 Stunden|14 Minuten|
|10 TB|22 Tage|10 Tage|1 Tag|2 Stunden|
|35 TB|76 Tage|34 Tage|3 Tage|8 Stunden|
|80 TB|173 Tage|78 Tage|8 Tage|19 Stunden|
|100 TB|216 Tage|97 Tage|10 Tage|1 Tag|
|200 TB|1 Jahr|194 Tage|19 Tage|2 Tage|
|500 TB|3 Jahre|1 Jahr|49 Tage|5 Tage|
|1 PB|6 Jahre|3 Jahre|97 Tage|10 Tage|
|2 PB|12 Jahre|5 Jahre|194 Tage|19 Tage|

Native Azure-Tools wie z. B. Apache Hadoop DistCp, Azure Data Factory und AzureCp können zur Übertragung von Daten über das Netzwerk verwendet werden. Für denselben Zweck kann auch das Drittanbietertool WANDisco verwendet werden. Für die laufende Datenübertragung von einer lokalen Umgebung in Azure-Speichersysteme können Apache Kafka Mirrormaker und Apache Sqoop verwendet werden.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Überlegungen zur Leistung bei der Verwendung von Apache Hadoop DistCp

DistCp ist ein Apache-Projekt, das einen MapReduce-Zuordnungsauftrag verwendet, um Daten zu übertragen, Fehler zu behandeln und nach Auftreten dieser Fehler für eine Wiederherstellung zu sorgen. Jeder Zuordnungsaufgabe wird eine Liste der Quelldateien zugewiesen. Die Zuordnungsaufgabe kopiert anschließend alle ihr zugewiesenen Dateien an das Ziel. Die Leistung von DistCp kann durch verschiedene Techniken verbessert werden.

### <a name="increase-the-number-of-mappers"></a>Erhöhen der Anzahl von Mappern

DistCp versucht, Zuordnungsaufgaben so zu erstellen, dass mit jeder Aufgabe in etwa dieselbe Anzahl von Bytes kopiert wird. Standardmäßig verwenden DistCp-Aufträge 20 Mapper. Durch die Verwendung von mehr Mappern für DistCp (mit dem Parameter „m“ an der Befehlszeile) wird die Parallelität während der Datenübertragung erhöht und die Dauer der Datenübertragung verringert. Beim Erhöhen der Anzahl von Mappern müssen jedoch zwei Aspekte berücksichtigt werden:

* Die niedrigste Granularität für DistCp ist eine einzelne Datei. Das Angeben einer Anzahl von Mappern, die die Anzahl von Quelldateien übersteigt, hat keinen Nutzen und führt zu einer Vergeudung der verfügbaren Clusterressourcen.

* Berücksichtigen Sie beim Festlegen der Anzahl von Mappern den verfügbaren Yarn-Arbeitsspeicher für den Cluster. Jede Zuordnungsaufgabe wird als Yarn-Container gestartet. Vorausgesetzt, dass keine anderen ressourcenintensiven Workloads im Cluster ausgeführt werden, kann die Anzahl von Mappern mithilfe der folgenden Formel bestimmt werden: m = (Anzahl von Workerknoten \* YARN-Arbeitsspeicher für jeden Workerknoten)/YARN-Containergröße. Wenn jedoch andere Anwendungen Arbeitsspeicher belegen, verwenden Sie nur einen Teil des YARN-Arbeitsspeichers für DistCp-Aufträge.

### <a name="use-more-than-one-distcp-job"></a>Verwenden mehrerer DistCp-Aufträge

Wenn das zu verschiebende Dataset größer ist als 1 TB, verwenden Sie mehr als einen DistCp-Auftrag. Auf diese Weise verringern Sie die Auswirkung von Fehlern. Bei einem Auftragsfehler müssen Sie nur diesen spezifischen Auftrag neu starten und nicht alle Aufträge.

### <a name="consider-splitting-files"></a>Erwägen einer Dateiaufteilung

Wenn eine kleine Anzahl großer Dateien vorliegt, erwägen Sie eine Aufteilung dieser Dateien in 256-MB-Dateiblöcke, um durch mehr Mapper potenziell die parallele Verarbeitung zu erhöhen.

### <a name="use-the-strategy-command-line-parameter"></a>Verwenden des Befehlszeilenparameters „strategy“

Erwägen Sie die Verwendung des Parameters `strategy = dynamic` in der Befehlszeile. Der Standardwert für den Parameter `strategy` lautet `uniform size`, wodurch mit jeder Zuordnungsaufgabe in etwa die gleiche Anzahl von Bytes kopiert wird. Wenn Sie den Parameterwert in `dynamic` ändern, wird die Datei in mehrere Dateiblöcke aufgeteilt. Die Anzahl von Dateiblöcken ist ein Vielfaches der Anzahl von Zuordnungen. Jede Zuordnungsaufgabe ist einem Dateiblock zugewiesen. Nachdem alle Pfade in einem Block verarbeitet wurden, wird der aktuelle Block gelöscht und ein neuer Block angefordert. Der Prozess wird fortgesetzt, bis keine Blöcke mehr verfügbar sind. Durch diesen „dynamischen“ Ansatz können schnellere Zuordnungsaufgaben mehr Pfade verarbeiten als langsamere Zuordnungen. Dies wiederum führt zu einer schnelleren Verarbeitung des DistCp-Auftrags insgesamt.

### <a name="increase-the-number-of-threads"></a>Erhöhen der Anzahl von Threads

Überprüfen Sie, ob das Erhöhen des Parameters `-numListstatusThreads` die Leistung verbessert. Dieser Parameter steuert die Anzahl von Threads, die zum Erstellen der Dateiauflistung verwendet werden. Der Höchstwert für diesen Parameter lautet 40.

### <a name="use-the-output-committer-algorithm"></a>Verwenden des committer-Algorithmus für die Ausgabe

Überprüfen Sie, ob die Übergabe des Parameters `-Dmapreduce.fileoutputcommitter.algorithm.version=2` die DistCp-Leistung verbessert. Dieser committer-Algorithmus für die Ausgabe umfasst Optimierungen in Bezug auf das Schreiben von Ausgabedateien am Ziel. Der folgende Befehl ist ein Beispiel, das die Nutzung der verschiedenen Parameter veranschaulicht:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migration von Metadaten

### <a name="apache-hive"></a>Apache Hive

Der Hive-Metastore kann entweder mithilfe von Skripts oder unter Verwendung der Datenbankreplikation migriert werden.

#### <a name="hive-metastore-migration-using-scripts"></a>Migration des Hive-Metastores mithilfe von Skripts

1. Generieren Sie die Hive-DDLs aus einem lokalen Hive-Metastore. Dieser Schritt kann mit einem [Wrapper-Bash-Skript](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md) ausgeführt werden.
1. Bearbeiten Sie die generierte DDL, um die HDFS-URL durch WASB/ADLS/ABFS-URLs zu ersetzen.
1. Führen Sie die aktualisierte DDL vom HDInsight-Cluster aus für den Metastore aus.
1. Stellen Sie sicher, dass die Versionen des Hive-Metastores in der lokalen Umgebung und der Cloud kompatibel sind.

#### <a name="hive-metastore-migration-using-db-replication"></a>Migration des Hive-Metastores mithilfe der Datenbankreplikation

- Richten Sie die Datenbankreplikation zwischen der lokalen Datenbank für den Hive-Metastore und der HDInsight-Metastore-Datenbank ein.
- Verwenden Sie „Hive MetaTool“, um die HDFS-URL durch WASB/ADLS/ABFS-URLs zu ersetzen. Beispiel:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- Exportieren Sie lokale Ranger-Richtlinien in XML-Dateien.
- Transformieren Sie lokale HDFS-basierte Pfade mit einem Tool wie XSLT in WASB/ADLS.
- Importieren Sie die Richtlinien in Ranger unter HDInsight.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den nächsten Artikel in dieser Reihe:

- [Best Practices für Sicherheit und DevOps bei der Migration lokaler Hadoop-Cluster zu Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-security-devops.md)