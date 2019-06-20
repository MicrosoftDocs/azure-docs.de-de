---
title: Apache Storm-Schreibzugriff auf Storage/Data Lake Storage – Azure HDInsight
description: Erfahren Sie, wie Sie mit Apache Storm in den HDFS-kompatiblen Speicher für HDInsight schreiben.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.openlocfilehash: 4ba0c861674eb2308cf1f96c33d0792f3e1a0f94
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683685"
---
# <a name="write-to-apache-hadoop-hdfs-from-apache-storm-on-hdinsight"></a>Schreiben in Apache Hadoop HDFS aus Apache Storm auf HDInsight

Erfahren Sie, wie Sie mit [Apache Storm](https://storm.apache.org/) Daten in den von Apache Storm auf HDInsight verwendeten HDFS-kompatiblen Speicher schreiben. HDInsight kann sowohl Azure Storage als auch Azure Data Lake Storage als HDFS-kompatiblen Speicher verwenden. Storm bietet eine [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html)-Komponente, die Daten in HDFS schreibt. Dieses Dokument enthält Informationen zum Schreiben in jeden Speichertyp von HdfsBolt aus. 

> [!IMPORTANT]  
> Die in diesem Dokument verwendete Beispieltopologie basiert auf Komponenten, die in Storm auf HDInsight enthalten sind. Bei Verwendung mit anderen Apache Storm-Clustern können Änderungen zum Arbeiten mit Azure Data Lake Storage erforderlich sein.

## <a name="get-the-code"></a>Abrufen des Codes

Das Projekt mit dieser Topologie steht unter [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) als Download zur Verfügung.

Zum Kompilieren dieses Projekts benötigen Sie die folgende Konfiguration für Ihre Entwicklungsumgebung:

* [Java JDK 1.8](https://aka.ms/azure-jdks) oder höher HDInsight 3.5 oder höher erfordert Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Bei der Installation von Java und dem JDK auf Ihrer Entwicklungsworkstation können die folgenden Umgebungsvariablen festgelegt werden. Sie sollten dennoch prüfen, ob die Variablen vorhanden sind und korrekte Werte für Ihr System enthalten.

* `JAVA_HOME` – sollte auf das Verzeichnis verweisen, in dem das JDK installiert ist.
* `PATH` – sollte die folgenden Pfade enthalten:
  
    * `JAVA_HOME` (oder entsprechender Pfad)
    * `JAVA_HOME\bin` (oder entsprechender Pfad)
    * Das Verzeichnis, in dem Maven installiert ist.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Verwenden von HdfsBolt mit HDInsight

> [!IMPORTANT]  
> Bevor Sie HdfsBolt mit Storm in HDInsight verwenden, müssen Sie mit einer Skriptaktion erforderliche JAR-Dateien in den `extpath` für Storm kopieren. Weitere Informationen finden Sie im Abschnitt „Konfigurieren des Clusters“.

HdfsBolt verwendet das von Ihnen bereitgestellte Dateischema, um zu verstehen, wie in HDFS geschrieben wird. Verwenden Sie mit HDInsight eines der folgenden Schemas:

* `wasb://`: Wird mit einem Azure Storage-Konto verwendet.
* `abfs://`: Wird mit Azure Data Lake Storage Gen2 verwendet.
* `adl://`: Wird mit Azure Data Lake Storage Gen1 verwendet.

Die folgende Tabelle enthält Beispiele für die Verwendung des Dateischemas für verschiedene Szenarien:

| Schema | Notizen |
| ----- | ----- |
| `wasb:///` | Das Standardspeicherkonto ist ein Blobcontainer in einem Azure Storage-Konto. |
| `abfs:///` | Das Standardspeicherkonto ist ein Verzeichnis in einem Azure Data Lake Storage Gen2-Konto. |
| `adl:///` | Das Standardspeicherkonto ist ein Verzeichnis in Azure Data Lake Storage Gen1. Während der Clustererstellung geben Sie das Verzeichnis in Data Lake Storage an, das den HDFS-Stamm des Clusters darstellt. Beispiel: das `/clusters/myclustername/`-Verzeichnis. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Ein nicht standardmäßiges (zusätzliches) Azure Storage-Konto, das dem Cluster zugeordnet ist. |
| `abfs://CONTAINER@ACCOUNT.dfs.core.windows.net/` | Ein nicht standardmäßiges (zusätzliches) Azure Storage-Konto, das dem Cluster zugeordnet ist. |
| `adl://STORENAME/` | Der Stamm des vom Cluster verwendeten Data Lake Storage. Mit diesem Schema können Sie auf Daten zugreifen, die sich außerhalb des das Clusterdateisystem enthaltenden Verzeichnisses befinden. |

Weitere Informationen finden Sie unter Apache.org in dem Verweis auf [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html).

### <a name="example-configuration"></a>Beispielkonfiguration

Der folgende YAML-Code ist ein Auszug aus der im Beispiel enthaltenen `resources/writetohdfs.yaml`-Datei. Diese Datei definiert die Storm-Topologie mit dem [Flux](https://storm.apache.org/releases/1.1.2/flux.html)-Framework für Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Dieser YAML-Code definiert die folgenden Elemente:

* `syncPolicy`: Definiert, wann Dateien mit dem Dateisystem synchronisiert/darin geleert werden. In diesem Beispiel alle 1.000 Tupel.
* `fileNameFormat`: Definiert das beim Schreiben von Dateien zu verwendende Pfad- und Dateinamensmuster. In diesem Beispiel wird der Pfad zur Laufzeit mithilfe eines Filters bereitgestellt, und die Erweiterung der Datei lautet `.txt`.
* `recordFormat`: Definiert das interne Format der geschriebenen Dateien. In diesem Beispiel werden Felder durch das `|`-Zeichen begrenzt.
* `rotationPolicy`: Definiert das Rotieren der Dateien. In diesem Beispiel erfolgt keine Rotation.
* `hdfs-bolt`: Verwendet die vorherigen Komponenten als Konfigurationsparameter für die `HdfsBolt`-Klasse.

Weitere Informationen zum Flux-Framework finden Sie unter [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>Konfigurieren des Clusters

Standardmäßig schließt Storm in HDInsight nicht die Komponenten ein, die HdfsBolt zur Kommunikation mit Azure Storage oder Data Lake Storage im Klassenpfad von Storm verwendet. Fügen Sie diese Komponenten mit folgender Skriptaktion dem `extlib`-Verzeichnis für Storm auf dem Cluster hinzu:

* Skript-URI: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Knoten für die Anwendung: Nimbus, Supervisor
* Parameter: Keine

Informationen zum Verwenden dieses Skripts mit Ihrem Cluster finden Sie im Dokument [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](./../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="build-and-package-the-topology"></a>Erstellen und Packen der Topologie

1. Laden Sie das Beispielprojekt unter [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) in Ihre Entwicklungsumgebung herunter.

2. Ändern Sie über eine Eingabeaufforderung, ein Terminal oder eine Shellsitzung Verzeichnisse in das Stammverzeichnis des heruntergeladenen Projekts. Erstellen und packen Sie die Topologie mit folgendem Befehl:
   
        mvn compile package
   
    Nach Abschluss des Erstellungs- und Packvorgangs finden Sie ein neues Verzeichnis namens `target` vor, das die Datei `StormToHdfs-1.0-SNAPSHOT.jar` enthält. Diese Datei enthält die kompilierte Topologie.

## <a name="deploy-and-run-the-topology"></a>Bereitstellen und Ausführen der Topologie

1. Führen Sie den folgenden Befehl aus, um die Topologie in den HDInsight-Cluster zu kopieren. Ersetzen Sie **USER** durch den SSH-Benutzernamen, den Sie beim Erstellen des Clusters verwendet haben. Ersetzen Sie **CLUSTERNAME** durch den Namen des Clusters.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    Geben Sie nach Aufforderung das Kennwort ein, das Sie beim Erstellen des SSH-Benutzers für den Cluster verwendet haben. Wenn Sie einen öffentlichen Schlüssel anstelle eines Kennworts verwendet haben, müssen Sie unter Umständen den Parameter `-i` verwenden und den Pfad zum passenden privaten Schlüssel angeben.
   
   > [!NOTE]  
   > Weitere Informationen zum Verwenden von `scp` mit HDInsight finden Sie unter [Verwenden von SSH mit HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Führen Sie nach Abschluss des Uploads den folgenden Befehl aus, um mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster herzustellen. Ersetzen Sie **USER** durch den SSH-Benutzernamen, den Sie beim Erstellen des Clusters verwendet haben. Ersetzen Sie **CLUSTERNAME** durch den Namen des Clusters.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Geben Sie nach Aufforderung das Kennwort ein, das Sie beim Erstellen des SSH-Benutzers für den Cluster verwendet haben. Wenn Sie einen öffentlichen Schlüssel anstelle eines Kennworts verwendet haben, müssen Sie unter Umständen den Parameter `-i` verwenden und den Pfad zum passenden privaten Schlüssel angeben.
   
   Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Verwenden Sie den folgenden Befehl, um eine Datei mit dem Namen `dev.properties` zu erstellen:

        nano dev.properties

4. Verwenden Sie als Inhalt der Datei `dev.properties` den folgenden Text:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]  
    > Dieses Beispiel setzt voraus, dass Ihr Cluster ein Azure Storage-Konto als Standardspeicher verwendet. Wenn Ihr Cluster Azure Data Lake Storage Gen2 verwendet, verwenden Sie stattdessen `hdfs.url: abfs:///`. Wenn Ihr Cluster Azure Data Lake Storage Gen1 verwendet, verwenden Sie stattdessen `hdfs.url: adl:///`.
    
    Verwenden Sie __STRG + X__, um die Datei zu speichern. Geben Sie dann __Y__ ein, und drücken Sie die __EINGABETASTE__. Mit den Werten in dieser Datei bestimmen Sie die Data Lake Storage-URL und den Namen des Verzeichnisses, in das Daten geschrieben werden.

3. Verwenden Sie den folgenden Befehl, um die Topologie zu starten:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Dieser Befehl startet die Topologie mithilfe des Flux-Frameworks, indem sie an den Nimbus-Knoten des Clusters gesendet wird. Die Topologie wird durch die `writetohdfs.yaml`-Datei in der JAR-Datei definiert. Die `dev.properties`-Datei wird als Filter übergeben, und die Werte in der Datei werden von der Topologie gelesen.

## <a name="view-output-data"></a>Anzeigen von Ausgabedaten

Verwenden Sie zum Anzeigen der Daten den folgenden Befehl:

    hdfs dfs -ls /stormdata/

Eine Liste der von dieser Topologie erstellten Dateien wird angezeigt.

Die folgende Liste ist ein Beispiel der von den vorherigen Befehlen zurückgegebenen Daten:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Beenden der Topologie

Storm-Topologien werden ausgeführt, bis sie beendet werden oder der Cluster gelöscht wird. Verwenden Sie den folgenden Befehl, um die Topologie zu beenden:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Löschen des Clusters

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Daten mithilfe von Apache Storm in Azure Storage und Azure Data Lake Storage geschrieben werden, können Sie sich mit anderen [Apache Storm-Beispielen für HDInsight](apache-storm-example-topology.md) beschäftigen.

## <a name="see-also"></a>Weitere Informationen
* [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
