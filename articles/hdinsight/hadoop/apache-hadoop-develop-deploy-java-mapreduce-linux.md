---
title: Erstellen von Java MapReduce für Apache Hadoop – Azure HDInsight
description: Erfahren Sie, wie Sie Apache Maven verwenden, um eine Java-basierte MapReduce-Anwendung zu erstellen und anschließend mit Hadoop in HDInsight ausführen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 01/16/2020
ms.openlocfilehash: 8b80616806f846951ebbaffcb2bf31ad77b96266
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996031"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Entwickeln von Java MapReduce-Programmen für Apache Hadoop in HDInsight

Erfahren Sie, wie Sie Apache Maven verwenden, um eine Java-basierte MapReduce-Anwendung zu erstellen und anschließend mit Apache Hadoop in HDInsight ausführen.

## <a name="prerequisites"></a>Voraussetzungen

* [Java Developer Kit (JDK), Version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* Ordnungsgemäße [Installation](https://maven.apache.org/install.html) von [Apache Maven](https://maven.apache.org/download.cgi) (gemäß Apache).  Maven ist ein Projekterstellungssystem für Java-Projekte.

## <a name="configure-development-environment"></a>Konfigurieren der Entwicklungsumgebung

Für diesen Artikel wurde ein Computer unter Windows 10 verwendet. Die Befehle wurden an einer Eingabeaufforderung ausgeführt, und die verschiedenen Dateien wurden mit dem Windows-Editor bearbeitet. Nehmen Sie die Ihrer Umgebung entsprechenden Änderungen vor.

Geben Sie an einer Eingabeaufforderung die folgenden Befehle ein, um eine Arbeitsumgebung zu erstellen:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Erstellen eines Maven-Projekts

1. Geben Sie den folgenden Befehl ein, um ein Maven-Projekt mit dem Namen **wordcountjava** zu erstellen:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Mit diesem Befehl wird ein Verzeichnis erstellt, das den Namen trägt, der mit dem Parameter `artifactID` (in diesem Beispiel **wordcountjava**) festgelegt wurde. Dieses Verzeichnis enthält die folgenden Elemente:

    * `pom.xml` – das [Projektobjektmodell (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html), das die Informationen und Konfigurationsdetails für das Erstellen des Projekts enthält.
    * src\main\java\org\apache\hadoop\examples: Enthält Ihren Anwendungscode.
    * src\test\java\org\apache\hadoop\examples: Enthält Tests für Ihre Anwendung.

1. Entfernen Sie den generierten Beispielcode. Geben Sie die folgenden Befehle ein, um die generierten Test- und Anwendungsdateien (`AppTest.java` und `App.java`) zu löschen:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualisieren des Projektobjektmodells

Eine vollständige Referenz der Datei „pom.xml“ finden Sie unter https://maven.apache.org/pom.html. Geben Sie den folgenden Befehl ein, um `pom.xml` zu öffnen:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Hinzufügen von Abhängigkeiten

Fügen Sie in `pom.xml` im Abschnitt `<dependencies>` den folgenden Text hinzu:

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

Damit werden die erforderlichen Bibliotheken (aufgelistet in &lt;artifactId\>) in bestimmten Versionen (aufgelistet in &lt;version\>) definiert. Beim Kompilieren werden diese Abhängigkeiten aus dem Maven-Standardrepository heruntergeladen. Sie können die [Maven-Repositorysuche](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) verwenden, um weitere Komponenten anzuzeigen.

`<scope>provided</scope>` gibt für Maven an, dass diese Abhängigkeiten nicht mit der Anwendung zusammengepackt werden sollen, da sie durch den HDInsight-Cluster zur Laufzeit bereitgestellt werden.

> [!IMPORTANT]
> Die verwendete Version sollte mit der aktuell in Ihrem Cluster vorhandenen Hadoop-Version übereinstimmen. Weitere Informationen zu Versionen finden Sie im Dokument über [HDInsight-Komponenten und -Versionen](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Buildkonfiguration

Mithilfe von Maven-Plug-Ins können Sie die Buildphasen des Projekts anpassen. Dieser Abschnitt wird zum Hinzufügen von Plug-Ins, Ressourcen und anderen Optionen für die Buildkonfiguration verwendet.

Fügen Sie der Datei `pom.xml` den folgenden Code hinzu, und speichern und schließen Sie die Datei. Dieser Text muss sich in der Datei innerhalb der `<project>...</project>`-Tags befinden, z.B. zwischen `</dependencies>` und `</project>`.

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
                <goals>
                <goal>shade</goal>
                </goals>
        </execution>
        </executions>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

In diesem Abschnitt werden auch das Apache Maven-Compiler-Plug-In und das Apache Maven-Shade-Plug-In konfiguriert. Das Compiler-Plug-In wird zum Kompilieren der Topologie verwendet. Das Shade-Plug-In wird verwendet, um die Lizenzduplizierung in dem von Maven erstellten JAR-Paket zu verhindern. Dieses Plug-In wird verwendet, um einen Fehler des Typs „Doppelte Lizenzdateien“ zur Laufzeit im HDInsight-Cluster zu vermeiden. Wenn das Maven-Shade-Plug-In mit der `ApacheLicenseResourceTransformer`-Implementierung verwendet wird, wird der Fehler vermieden.

Das maven-shade-plugin generiert außerdem ein Uberjar, das alle Abhängigkeiten enthält, die in der Anwendung benötigt werden.

Speichern Sie die Datei `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Erstellen der MapReduce-Anwendung

1. Geben Sie den folgenden Befehl ein, um die neue Datei `WordCount.java` zu erstellen und zu öffnen. Wählen an der Eingabeaufforderung **Ja** aus, um eine neue Datei zu erstellen.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Kopieren Sie den folgenden Java-Code, und fügen Sie ihn in die neue Datei ein. Schließen Sie dann die Datei.

    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```

    Beachten Sie, dass der Paketname `org.apache.hadoop.examples` und der Klassenname `WordCount` ist. Diese Namen werden beim Übermitteln des MapReduce-Auftrags verwendet.

## <a name="build-and-package-the-application"></a>Erstellen und Packen der Anwendung

Verwenden Sie im Verzeichnis `wordcountjava` den folgenden Befehl, um eine JAR-Datei zu erstellen, die die Anwendung enthält:

```cmd
mvn clean package
```

Mit diesem Befehl werden etwaige frühere Erstellungsartefakte entfernt, alle noch nicht installierten Abhängigkeiten heruntergeladen und dann die Anwendung erstellt und gepackt.

Sobald der Befehl abgeschlossen ist, enthält das Verzeichnis `wordcountjava/target` eine Datei mit dem Namen `wordcountjava-1.0-SNAPSHOT.jar`.

> [!NOTE]
> Die Datei `wordcountjava-1.0-SNAPSHOT.jar` ist ein Uberjar, das nicht nur den WordCount-Auftrag enthält, sondern auch Abhängigkeiten, die für den Auftrag zur Laufzeit erforderlich sind.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Hochladen der JAR-Datei und Ausführen von Aufträgen (SSH)

In den folgenden Schritten wird `scp` zum Kopieren der JAR-Datei auf den primären Hauptknoten von Apache HBase im HDInsight-Cluster verwendet. Der Befehl `ssh` dient dann zum Herstellen einer Verbindung mit dem Cluster und direkten Ausführen des Beispiels auf dem Hauptknoten.

1. Laden Sie die JAR-Datei in den Cluster hoch. Ersetzen Sie `CLUSTERNAME` durch Ihren HDInsight-Clusternamen, und geben Sie dann den folgenden Befehl ein:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Verbinden mit dem Cluster Ersetzen Sie `CLUSTERNAME` durch Ihren HDInsight-Clusternamen, und geben Sie dann den folgenden Befehl ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verwenden Sie in der SSH-Sitzung den folgenden Befehl, um die MapReduce-Anwendung auszuführen:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Dieser Befehl startet die Anwendung WordCount MapReduce. Die Eingabedatei ist `/example/data/gutenberg/davinci.txt`, und das Ausgabeverzeichnis ist `/example/data/wordcountout`. Die Eingabedatei und die Ausgabe werden im Standardspeicher für den Cluster gespeichert.

1. Sobald der Auftrag abgeschlossen ist, verwenden Sie den folgenden Befehl zum Anzeigen der Ergebnisse:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Sie sollten eine Liste von Wörtern und Zahlen mit Werten erhalten, die dem folgenden Text ähneln:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie ein Java MapReduce-Auftrag entwickelt wird. In den folgenden Dokumenten finden Sie andere Möglichkeiten zum Arbeiten mit HDInsight.

* [Verwenden von Apache Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)
* [Java Developer Center](https://azure.microsoft.com/develop/java/)