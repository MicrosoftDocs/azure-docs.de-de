---
title: Sicherung und Replikation für Apache HBase und Phoenix – Azure HDInsight
description: Einrichten der Sicherung und Replikation für Apache HBase und Apache Phoenix in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 3ed55387034a383e402d027fd5cab60c4a59c23c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657039"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Einrichten der Sicherung und Replikation für Apache HBase und Apache Phoenix in HDInsight

Apache HBase unterstützt mehrere Methoden zum Schutz vor Datenverlusten:

* Kopieren des Ordners `hbase`
* Exportieren und Importieren
* Kopieren von Tabellen
* Momentaufnahmen
* Replikation

> [!NOTE]  
> Apache Phoenix speichert seine Metadaten in HBase-Tabellen, sodass die Metadaten gesichert werden, wenn Sie die HBase-Systemkatalogtabellen sichern.

In den folgenden Abschnitten werden die Verwendungsszenarien für die einzelnen Methoden beschrieben.

## <a name="copy-the-hbase-folder"></a>Kopieren des Ordners „hbase“

Bei dieser Methode kopieren Sie alle HBase-Daten, ohne eine Teilmenge der Tabellen oder Spaltenfamilien auswählen zu können. Die im Anschluss erläuterten Methoden bieten bessere Steuerungsmöglichkeiten.

HBase in HDInsight verwendet den bei der Clustererstellung ausgewählten Standardspeicher (entweder Azure Storage-Blobs oder Azure Data Lake Storage). In beiden Fällen speichert HBase seine Daten- und Metadatendateien unter folgendem Pfad:

`/hbase`

* In einem Azure Storage-Konto befindet sich der Ordner `hbase` im Stammverzeichnis des Blobcontainers:

  `wasbs://<containername>@<accountname>.blob.core.windows.net/hbase`

* In Azure Data Lake Storage befindet sich der Ordner `hbase` unter dem Stammpfad, den Sie bei der Clusterbereitstellung angegeben haben. Dieser Stammpfad enthält üblicherweise einen Ordner namens `clusters`, der wiederum einen nach Ihrem HDInsight-Cluster benannten Unterordner enthält:

  `/clusters/<clusterName>/hbase`

In beiden Fällen enthält der Ordner `hbase` alle Daten, die HBase auf den Datenträger geschrieben hat. Die In-Memory-Daten sind jedoch möglicherweise nicht enthalten. Der Ordner kann erst nach Herunterfahren des Clusters als exakte Darstellung der HBase-Daten betrachtet werden.

Nach dem Löschen des Clusters können Sie die Daten entweder an ihrem Speicherort belassen oder sie an einen neuen Speicherort kopieren:

* Erstellen Sie eine neue HDInsight-Instanz, die auf den aktuellen Speicherort verweist. Die neue Instanz wird mit allen vorhandenen Daten erstellt.

* Kopieren Sie den Ordner `hbase` in einen anderen Azure Storage-Blobcontainer oder an einen anderen Data Lake Storage-Speicherort, und starten Sie einen neuen Cluster mit diesen Daten. Verwenden Sie dazu entweder [AzCopy](../../storage/common/storage-use-azcopy-v10.md) (Azure Storage) oder [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) (Data Lake Storage).

## <a name="export-then-import"></a>Exportieren und Importieren

Verwenden Sie im HDInsight-Quellcluster das in HBase enthaltene [Exporthilfsprogramm](https://hbase.apache.org/book.html#export), um Daten aus einer Quelltabelle in den zugeordneten Standardspeicher zu exportieren. Den exportierten Ordner können Sie dann an den Zielspeicherort kopieren und das [Importhilfsprogramm](https://hbase.apache.org/book.html#import) für den HDInsight-Zielcluster ausführen.

Stellen Sie zum Exportieren von Tabellendaten zunächst eine SSH-Verbindung mit dem Hauptknoten des HDInsight-Quellclusters her, und führen Sie dann den folgenden `hbase`-Befehl aus:

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"
```

Das Exportverzeichnis darf nicht bereits vorhanden sein. Beim Tabellennamen wird die Groß- und Kleinschreibung beachtet.

Stellen Sie zum Importieren von Tabellendaten eine SSH-Verbindung mit dem Hauptknoten des HDInsight-Zielclusters her, und führen Sie dann den folgenden `hbase`-Befehl aus:

```console
hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"
```

Die Tabelle muss bereits vorhanden sein.

Geben Sie den vollständigen Exportpfad zum Standardspeicher oder zu einer der zugeordneten Speicheroptionen an. Beispiel für Azure Storage:

`wasbs://<containername>@<accountname>.blob.core.windows.net/<path>`

Syntax in Azure Data Lake Storage Gen2:

`abfs://<containername>@<accountname>.dfs.core.windows.net/<path>`

Syntax in Azure Data Lake Storage Gen1:

`adl://<accountName>.azuredatalakestore.net:443/<path>`

Diese Methode bietet Granularität auf Tabellenebene. Sie können auch einen Datumsbereich für die einzubeziehenden Zeilen angeben und so den Prozess schrittweise ausführen. Jedes Datum wird in Millisekunden seit der Unix-Epoche angegeben.

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>
```

Beachten Sie, dass Sie für jede zu exportierende Zeile die Anzahl von Versionen angeben müssen. Wenn alle Versionen in den Datumsbereich eingeschlossen werden sollen, legen Sie `<numberOfVersions>` auf einen Wert fest, der Ihre maximal möglichen Zeilenversionen übersteigt (beispielsweise 100.000).

## <a name="copy-tables"></a>Kopieren von Tabellen

Das [CopyTable-Hilfsprogramm](https://hbase.apache.org/book.html#copy.table) kopiert Daten zeilenweise aus einer Quelltabelle in eine vorhandene Zieltabelle mit dem gleichen Schema wie die Quelle. Die Zieltabelle kann sich im gleichen Cluster oder in einem anderen HBase-Cluster befinden. Bei den Tabellennamen wird nach Groß- und Kleinschreibung unterschieden.

Wenn Sie CopyTable innerhalb eines Clusters verwenden möchten, stellen Sie eine SSH-Verbindung mit dem Hauptknoten des HDInsight-Quellclusters her, und führen Sie dann den folgenden `hbase`-Befehl aus:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>
```

Wenn Sie mithilfe von CopyTable Daten in eine Tabelle in einem anderen Cluster kopieren möchten, fügen Sie den Switch `peer` mit der Adresse des Zielclusters hinzu:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>
```

Die Zieladresse besteht aus drei Teilen:

`<destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>`

* `<ZooKeeperQuorum>` ist eine kommagetrennte Liste mit Apache ZooKeeper-Knoten. Beispiel:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` ist in HDInsight standardmäßig 2181, und `<ZnodeParent>` ist `/hbase-unsecure`. Die vollständige Angabe für `<destinationAddress>` lautet also wie folgt:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Ausführliche Informationen zum Abrufen dieser Werte aus Ihrem HDInsight-Cluster finden Sie in diesem Artikel unter [Manuelles Erfassen der Apache ZooKeeper-Quorumliste](#manually-collect-the-apache-zookeeper-quorum-list).

Das CopyTable-Hilfsprogramm unterstützt auch Parameter zur Angabe des Zeitraums für die zu kopierenden Zeilen sowie der Teilmenge der zu kopierenden Spaltenfamilien in einer Tabelle. Führen Sie CopyTable ohne Parameter aus, um die vollständige Liste der von CopyTable unterstützten Parameter anzuzeigen:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable
```

CopyTable überprüft den gesamten Inhalt der Quelltabelle, der in die Zieltabelle kopiert wird. Die Ausführung von CopyTable beeinträchtigt daher möglicherweise die Leistung Ihres HBase-Clusters.

> [!NOTE]  
> Wenn Sie das Kopieren von Daten zwischen Tabellen automatisieren möchten, sehen Sie sich das Skript `hdi_copy_table.sh` aus dem GitHub-Repository [Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) an.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Manuelles Erfassen der Apache ZooKeeper-Quorumliste

Wenn sich beide HDInsight-Cluster wie zuvor beschrieben im gleichen virtuellen Netzwerk befinden, findet die interne Hostnamensauflösung automatisch statt. Wenn Sie CopyTable für HDInsight-Cluster in zwei getrennten virtuellen Netzwerken verwenden möchten, die über eine VPN Gateway-Instanz verbunden sind, müssen Sie die Host-IP-Adressen der ZooKeeper-Knoten im Quorum angeben.

Verwenden Sie den folgenden curl-Befehl, um die Quorumhostnamen zu ermitteln:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"
```

Der curl-Befehl ruft ein JSON-Dokument mit HBase-Konfigurationsinformationen ab. Der grep-Befehl gibt lediglich den Eintrag „hbase.zookeeper.quorum“ zurück. Beispiel:

```output
"hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"
```

Der Wert für die Quorumhostnamen ist die gesamte Zeichenfolge nach dem Doppelpunkt.

Verwenden Sie zum Abrufen der IP-Adressen für diese Hosts den folgenden curl-Befehl für die einzelnen Hosts aus der vorherigen Liste:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"
```

In diesem curl-Befehl ist `<zookeeperHostFullName>` der vollständige DNS-Name eines ZooKeeper-Hosts (Beispiel: `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`). Die Ausgabe des Befehls enthält die IP-Adresse für den angegebenen Host. Beispiel:

`100    "ip" : "10.0.0.9",`

Nachdem Sie die IP-Adressen für alle ZooKeeper-Knoten in Ihrem Quorum ermittelt haben, erstellen Sie die Zieladresse neu:

`<destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>`

In unserem Beispiel:

`<destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure`

## <a name="snapshots"></a>Momentaufnahmen

Mithilfe von [Momentaufnahmen](https://hbase.apache.org/book.html#ops.snapshots) können Sie eine Point-in-Time-Sicherung von Daten in Ihrem HBase-Datenspeicher erstellen. Momentaufnahmen verursachen nur einen minimalen Mehraufwand und sind in Sekundenschnelle abgeschlossen, da es sich bei einem Momentaufnahmevorgang im Grunde um einen Metadatenvorgang handelt, bei dem die Namen aller Dateien im Speicher auf einmal erfasst werden. Zum Zeitpunkt der Momentaufnahmenerstellung werden keine tatsächlichen Daten kopiert. Momentaufnahmen basieren auf der Unveränderlichkeit der im HDFS gespeicherten Daten, da Aktualisierungen, Löschungen und Einfügungen jeweils als neue Daten dargestellt werden. Sie können eine Momentaufnahme im gleichen Cluster wiederherstellen (*klonen*) oder in einen anderen Cluster exportieren.

Stellen Sie zum Erstellen einer Momentaufnahme eine SSH-Verbindung mit dem Hauptknoten Ihres HDInsight HBase-Clusters her, und starten Sie die `hbase`-Shell:

```console
hbase shell
```

Verwenden Sie innerhalb der hbase-Shell den snapshot-Befehl mit den Namen der Tabelle und dieser Momentaufnahme:

```console
snapshot '<tableName>', '<snapshotName>'
```

Wenn Sie eine Momentaufnahme auf der Grundlage des Namens innerhalb der `hbase`-Shell wiederherstellen möchten, deaktivieren Sie die Tabelle, stellen Sie die Momentaufnahme wieder her, und aktivieren Sie die Tabelle anschließend wieder:

```console
disable '<tableName>'
restore_snapshot '<snapshotName>'
enable '<tableName>'
```

Verwenden Sie zum Wiederherstellen einer Momentaufnahme in einer neuen Tabelle „clone_snapshot“:

```console
clone_snapshot '<snapshotName>', '<newTableName>'
```

Wenn Sie eine Momentaufnahme zur Verwendung durch einen anderen Cluster in das HDFS exportieren möchten, erstellen Sie zunächst die Momentaufnahme gemäß der Anleitung weiter oben, und verwenden Sie dann das ExportSnapshot-Hilfsprogramm. Führen Sie dieses Hilfsprogramm innerhalb der SSH-Sitzung mit dem Hauptknoten aus (nicht in der `hbase`-Shell):

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>
```

Bei `<hdfsHBaseLocation>` kann es sich um einen beliebigen Speicherort handeln, auf die ihr Quellcluster Zugriff hat, und die Angabe muss auf den hbase-Ordner verweisen, der von Ihrem Zielcluster verwendet wird. Wenn Ihrem Quellcluster also beispielsweise ein sekundäres Azure Storage-Konto zugeordnet ist und dieses Konto Zugriff auf den Container bietet, der vom Standardspeicher des Zielclusters verwendet wird, können Sie den folgenden Befehl verwenden:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Wenn Sie Ihrem Quellcluster kein sekundäres Azure-Speicherkonto zugeordnet haben oder wenn Ihr Quellcluster ein lokaler Cluster (oder ein Nicht-HDI-Cluster) ist, kann es zu Autorisierungsproblemen kommen, wenn Sie versuchen, auf das Speicherkonto Ihres HDI-Clusters zuzugreifen. Um dies zu beheben, geben Sie den Schlüssel zu Ihrem Speicherkonto als Befehlszeilenparameter an, wie im folgenden Beispiel gezeigt. Sie können den Schlüssel zu Ihrem Speicherkonto im Azure-Portal abrufen.

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.myaccount.blob.core.windows.net=mykey -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Wenn Ihr Zielcluster ein ADLS Gen 2-Cluster ist, ändern Sie den vorherigen Befehl gemäß der Konfigurationen, die von ADLS Gen 2 verwendet werden:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.<account_name>.dfs.core.windows.net=<key> -Dfs.azure.account.auth.type.<account_name>.dfs.core.windows.net=SharedKey -Dfs.azure.always.use.https.<account_name>.dfs.core.windows.net=false -Dfs.azure.account.keyprovider.<account_name>.dfs.core.windows.net=org.apache.hadoop.fs.azurebfs.services.SimpleKeyProvider -snapshot 'Snapshot1' -copy-to 'abfs://<container>@<account_name>.dfs.core.windows.net/hbase'
```

Stellen Sie nach dem Exportieren der Momentaufnahme eine SSH-Verbindung mit dem Hauptknoten des Zielclusters her, und stellen Sie die Momentaufnahme mithilfe des Befehls `restore_snapshot` wie zuvor beschrieben wieder her.

Momentaufnahmen stellen eine vollständige Sicherung einer Tabelle zum Zeitpunkt der Ausführung des Befehls `snapshot` dar. Mit Momentaufnahmen können weder inkrementelle Momentaufnahmen nach Zeitfenster erstellt noch Teilmengen einzubeziehender Spaltenfamilien angegeben werden.

## <a name="replication"></a>Replikation

Bei der [HBase-Replikation](https://hbase.apache.org/book.html#_cluster_replication) werden Transaktionen mithilfe von Push automatisch aus einem Quellcluster an einen Zielcluster übertragen. Dabei kommt ein asynchroner Mechanismus mit minimalem Zusatzaufwand für den Quellcluster zum Einsatz. In HDInsight können Sie in folgenden Fällen eine Replikation zwischen Clustern einrichten:

* Quell- und Zielcluster befinden sich im gleichen virtuellen Netzwerk.
* Quell- und Zielcluster befinden sich in unterschiedlichen, über ein VPN-Gateway verbundenen virtuellen Netzwerken, beide Cluster befinden sich jedoch am gleichen geografischen Standort.
* Quell- und Zielcluster befinden sich in unterschiedlichen, über ein VPN-Gateway verbundenen virtuellen Netzwerken, und die einzelnen Cluster befinden sich an unterschiedlichen geografischen Standorten.

Die Einrichtung der Replikation umfasst folgende allgemeine Schritte:

1. Erstellen Sie im Quellcluster die Tabellen, und füllen Sie sie mit Daten.
2. Erstellen Sie im Zielcluster leere Zieltabellen mit dem Schema der Quelltabelle.
3. Registrieren Sie den Zielcluster als Peer beim Quellcluster.
4. Aktivieren Sie die Replikation für die gewünschten Quelltabellen.
5. Kopieren Sie vorhandene Daten aus den Quelltabellen in die Zieltabellen.
6. Durch die Replikation werden neue Datenänderungen an den Quelltabellen automatisch in die Zieltabellen kopiert.

Wenden Sie zum Aktivieren der Replikation in HDInsight eine Skriptaktion auf Ihren aktiven HDInsight-Quellcluster an. Eine exemplarische Vorgehensweise zur Aktivierung der Replikation in Ihrem Cluster finden Sie unter [Einrichten der Apache HBase-Clusterreplikation in virtuellen Azure-Netzwerken](apache-hbase-replication.md). Diesen Artikel können Sie auch heranziehen, wenn Sie anhand von Beispielclustern, die unter Verwendung von Azure Resource Manager-Vorlagen in virtuellen Netzwerken erstellt wurden, mit der Replikation experimentieren möchten. Darüber hinaus enthält der Artikel Anweisungen zum Aktivieren der Replikation von Phoenix-Metadaten.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren von Apache HBase-Replikation](apache-hbase-replication.md)
* [Arbeiten mit dem in HBase enthaltenen Import- und Exporthilfsprogramm](/archive/blogs/data_otaku/working-with-the-hbase-import-and-export-utility)
