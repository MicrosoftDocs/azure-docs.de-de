---
title: 'Tutorial: HDInsight Apache Storm in Storage – Azure/Data Lake'
description: Tutorial – Erfahren Sie, wie Sie mit Apache Storm in den HDFS-kompatiblen Speicher für Azure HDInsight schreiben.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 8cdcc60e6eeec22e202d670c3fc057711bd432a6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545445"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Tutorial: Schreiben in Apache Hadoop HDFS aus Apache Storm auf Azure HDInsight

Dieses Tutorial zeigt, wie Sie mit Apache Storm Daten in den von Apache Storm auf HDInsight verwendeten HDFS-kompatiblen Speicher schreiben. HDInsight kann sowohl Azure Storage als auch Azure Data Lake Storage als HDFS-kompatiblen Speicher verwenden. Storm bietet eine [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html)-Komponente, die Daten in HDFS schreibt. Dieses Dokument enthält Informationen zum Schreiben in jeden Speichertyp von HdfsBolt aus.

Die in diesem Dokument verwendete Beispieltopologie basiert auf Komponenten, die in Storm auf HDInsight enthalten sind. Bei Verwendung mit anderen Apache Storm-Clustern können Änderungen zum Arbeiten mit Azure Data Lake Storage erforderlich sein.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren des Cluster mit Skriptaktionen
> * Erstellen und Packen der Topologie
> * Bereitstellen und Ausführen der Topologie
> * Anzeigen von Ausgabedaten
> * Beenden der Topologie

## <a name="prerequisites"></a>Voraussetzungen

* [Java Developer Kit (JDK), Version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* Ordnungsgemäße [Installation](https://maven.apache.org/install.html) von [Apache Maven](https://maven.apache.org/download.cgi) (gemäß Apache).  Maven ist ein Projekterstellungssystem für Java-Projekte.

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Das [URI-Schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) für Ihren primären Clusterspeicher. Dies ist `wasb://` für Azure Storage, `abfs://` für Azure Data Lake Storage Gen2 oder `adl://` für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Azure Storage aktiviert ist, lautet der URI `wasbs://`.  Siehe auch [Vorschreiben einer sicheren Übertragung in Azure Storage](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Beispielkonfiguration

Der folgende YAML-Code ist ein Auszug aus der im Beispiel enthaltenen `resources/writetohdfs.yaml`-Datei. Diese Datei definiert die Storm-Topologie mit dem [Flux](https://storm.apache.org/releases/current/flux.html)-Framework für Apache Storm.

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

Standardmäßig schließt Storm in HDInsight nicht die Komponenten ein, die `HdfsBolt` zur Kommunikation mit Azure Storage oder Data Lake Storage im Klassenpfad von Storm verwendet. Fügen Sie diese Komponenten mit folgender Skriptaktion dem `extlib`-Verzeichnis für Storm auf dem Cluster hinzu:

| Eigenschaft | Wert |
|---|---|
|Skripttyp |--Benutzerdefiniert|
|Bash-Skript-URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Knotentyp(en) |Nimbus, Supervisor|
|Parameter |Keine|

Informationen zum Verwenden dieses Skripts mit Ihrem Cluster finden Sie im Dokument [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](./../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="build-and-package-the-topology"></a>Erstellen und Packen der Topologie

1. Laden Sie das Beispielprojekt unter [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) in Ihre Entwicklungsumgebung herunter.

2. Ändern Sie über eine Eingabeaufforderung, ein Terminal oder eine Shellsitzung Verzeichnisse in das Stammverzeichnis des heruntergeladenen Projekts. Erstellen und packen Sie die Topologie mit folgendem Befehl:

    ```cmd
    mvn compile package
    ```

    Nach Abschluss des Erstellungs- und Packvorgangs finden Sie ein neues Verzeichnis namens `target` vor, das die Datei `StormToHdfs-1.0-SNAPSHOT.jar` enthält. Diese Datei enthält die kompilierte Topologie.

## <a name="deploy-and-run-the-topology"></a>Bereitstellen und Ausführen der Topologie

1. Führen Sie den folgenden Befehl aus, um die Topologie in den HDInsight-Cluster zu kopieren. Ersetzen Sie `CLUSTERNAME` durch den Namen des Clusters.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Führen Sie nach Abschluss des Uploads den folgenden Befehl aus, um mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster herzustellen. Ersetzen Sie `CLUSTERNAME` durch den Namen des Clusters.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verwenden Sie den folgenden Befehl, um eine Datei mit dem Namen `dev.properties` zu erstellen:

    ```bash
    nano dev.properties
    ```

1. Verwenden Sie als Inhalt der `dev.properties`-Datei den folgenden Text: Überarbeiten Sie dies je nach Bedarf basierend auf Ihrem [URI-Schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Verwenden Sie __STRG + X__ , um die Datei zu speichern. Geben Sie dann __Y__ ein, und drücken Sie die __EINGABETASTE__ . Mit den Werten in dieser Datei bestimmen Sie die Speicher-URL und den Namen des Verzeichnisses, in das Daten geschrieben werden.

1. Verwenden Sie den folgenden Befehl, um die Topologie zu starten:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Dieser Befehl startet die Topologie mithilfe des Flux-Frameworks, indem sie an den Nimbus-Knoten des Clusters gesendet wird. Die Topologie wird durch die `writetohdfs.yaml`-Datei in der JAR-Datei definiert. Die `dev.properties`-Datei wird als Filter übergeben, und die Werte in der Datei werden von der Topologie gelesen.

## <a name="view-output-data"></a>Anzeigen von Ausgabedaten

Verwenden Sie zum Anzeigen der Daten den folgenden Befehl:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Eine Liste der von dieser Topologie erstellten Dateien wird angezeigt. Die folgende Liste ist ein Beispiel der von den vorherigen Befehlen zurückgegebenen Daten:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Beenden der Topologie

Storm-Topologien werden ausgeführt, bis sie beendet werden oder der Cluster gelöscht wird. Verwenden Sie den folgenden Befehl, um die Topologie zu beenden:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Zum Bereinigen der im Rahmen dieses Tutorials erstellten Ressourcen können Sie die Ressourcengruppe löschen. Dadurch werden auch der zugeordnete HDInsight-Cluster sowie alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

So entfernen Sie die Ressourcengruppe über das Azure-Portal:

1. Erweitern Sie im Azure-Portal das Menü auf der linken Seite, um das Menü mit den Diensten zu öffnen, und klicken Sie auf __Ressourcengruppen__ , um die Liste mit Ihren Ressourcengruppen anzuzeigen.
2. Suchen Sie die zu löschende Ressourcengruppe, und klicken Sie mit der rechten Maustaste rechts neben dem Eintrag auf die Schaltfläche __Mehr__ (...).
3. Klicken Sie auf __Ressourcengruppe löschen__ , und bestätigen Sie den Vorgang.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie mit Apache Storm Daten in den von Apache Storm auf HDInsight verwendeten HDFS-kompatiblen Speicher schreiben.

> [!div class="nextstepaction"]
> Entdecken Sie andere [Apache Storm-Beispiele für HDInsight](apache-storm-example-topology.md).