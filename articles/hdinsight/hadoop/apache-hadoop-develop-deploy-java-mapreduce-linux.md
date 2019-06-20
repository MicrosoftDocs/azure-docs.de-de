---
title: Erstellen von Java MapReduce für Apache Hadoop – Azure HDInsight
description: Erfahren Sie, wie Sie Apache Maven verwenden, um eine Java-basierte MapReduce-Anwendung zu erstellen und anschließend mit Hadoop in HDInsight ausführen.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: hrasheed
ms.openlocfilehash: 9eb331e303ff64f4316aab0a0af553975bb72fc1
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137480"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Entwickeln von Java MapReduce-Programmen für Apache Hadoop in HDInsight

Erfahren Sie, wie Sie Apache Maven verwenden, um eine Java-basierte MapReduce-Anwendung zu erstellen und anschließend mit Apache Hadoop in HDInsight ausführen.

> [!NOTE]
> Dieses Beispiel wurde kürzlich für HDInsight 3.6 getestet.

## <a name="prerequisites"></a>Voraussetzungen

* [Java JDK](https://www.oracle.com/technetwork/java/javase/downloads/) 8 oder höher (oder eine entsprechende Anwendung wie OpenJDK)
    
    > [!NOTE]
    > HDInsight-Version 3.4 und früher verwenden Java 7. HDInsight 3.5 und höhere Versionen nutzen Java 8.

* [Apache Maven](https://maven.apache.org/)

## <a name="configure-development-environment"></a>Konfigurieren der Entwicklungsumgebung

Bei der Installation von Java und dem JDK können die folgenden Umgebungsvariablen festgelegt werden. Sie sollten dennoch prüfen, ob die Variablen vorhanden sind und korrekte Werte für Ihr System enthalten.

* `JAVA_HOME` – sollte auf das Verzeichnis verweisen, in dem die Java-Laufzeitumgebung (Java Runtime Environment, JRE) installiert ist. Für ein OS X-, Unix- oder Linux-System sollte z.B. ein Wert wie `/usr/lib/jvm/java-7-oracle` verwendet werden. Unter Windows sollte der Wert so ähnlich sein wie `c:\Program Files (x86)\Java\jre1.7`

* `PATH` – sollte die folgenden Pfade enthalten:
  
  * `JAVA_HOME` (oder entsprechender Pfad)

  * `JAVA_HOME\bin` (oder entsprechender Pfad)

  * Das Verzeichnis, in dem Maven installiert ist.

## <a name="create-a-maven-project"></a>Erstellen eines Maven-Projekts

1. Ändern Sie über eine Terminalsitzung oder über die Befehlszeile in Ihrer Entwicklungsumgebung die Verzeichnisse in den Speicherort, an dem Sie dieses Projekt speichern möchten.

2. Verwenden Sie den Befehl `mvn`, der mit Maven installiert wird, um das Gerüst für das Projekt zu erstellen.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Wenn Sie PowerShell verwenden, müssen Sie die `-D`-Parameter in doppelte Anführungszeichen setzen.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Mit diesem Befehl wird ein Verzeichnis erstellt, das den Namen trägt, der mit dem Parameter `artifactID` (in diesem Beispiel **wordcountjava**) festgelegt wurde. Dieses Verzeichnis enthält die folgenden Elemente:

   * `pom.xml` – das [Projektobjektmodell (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html), das die Informationen und Konfigurationsdetails für das Erstellen des Projekts enthält.

   * `src` – das Verzeichnis, das die Anwendung enthält.

3. Löschen Sie die Datei `src/test/java/org/apache/hadoop/examples/apptest.java`. Sie wird in diesem Beispiel nicht verwendet.

## <a name="add-dependencies"></a>Hinzufügen von Abhängigkeiten

1. Bearbeiten Sie die Datei `pom.xml`, und fügen Sie folgenden Text im Abschnitt `<dependencies>` ein:
   
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

2. Fügen Sie der Datei `pom.xml` folgenden Code hinzu. Dieser Text muss sich in der Datei innerhalb der `<project>...</project>`-Tags befinden, z.B. zwischen `</dependencies>` und `</project>`.

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

    Das erste Plug-In konfiguriert das [Maven Shade-Plug-In](https://maven.apache.org/plugins/maven-shade-plugin/), das verwendet wird, um ein Uberjar (wird auch als Fatjar bezeichnet) zu erstellen, das die von der Anwendung benötigten Abhängigkeiten enthält. Zudem wird damit eine Duplizierung von Lizenzen innerhalb des JAR-Pakets verhindert, die bei einigen Systemen Probleme verursachen kann.

    Das zweite Plug-In konfiguriert die Java-Version des Ziels.

    > [!NOTE]
    > HDInsight 3.4 und früher verwenden Java 7. HDInsight 3.5 und höhere Versionen nutzen Java 8.

3. Speichern Sie die Datei `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Erstellen der MapReduce-Anwendung

1. Wechseln Sie zum Verzeichnis `wordcountjava/src/main/java/org/apache/hadoop/examples`, und benennen Sie die Datei `App.java` in `WordCount.java` um.

2. Öffnen Sie die Datei `WordCount.java` in einem Text-Editor, und ersetzen Sie den Inhalt durch folgenden Text:
   
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

3. Speichern Sie die Datei .

## <a name="build-the-application"></a>Erstellen der Anwendung

1. Wechseln Sie zum Verzeichnis `wordcountjava`, sofern Sie es noch nicht aufgerufen haben.

2. Verwenden Sie den folgenden Befehl, um eine JAR-Datei mit der Anwendung zu erstellen:

   ```
   mvn clean package
   ```

    Mit diesem Befehl werden etwaige frühere Erstellungsartefakte entfernt, alle noch nicht installierten Abhängigkeiten heruntergeladen und dann die Anwendung erstellt und gepackt.

3. Sobald der Befehl abgeschlossen ist, enthält das Verzeichnis `wordcountjava/target` eine Datei mit dem Namen `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > Die Datei `wordcountjava-1.0-SNAPSHOT.jar` ist ein Uberjar, das nicht nur den WordCount-Auftrag enthält, sondern auch Abhängigkeiten, die für den Auftrag zur Laufzeit erforderlich sind.

## <a id="upload"></a>Hochladen der JAR-Datei

Verwenden Sie den folgenden Befehl, um die JAR-Datei in den HDInsight-Hauptknoten hochzuladen:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

Ersetzen Sie __USERNAME__ durch Ihren SSH-Benutzernamen für den Cluster. Ersetzen Sie __CLUSTERNAME__ durch den Namen des HDInsight-Clusters.

Dieser Befehl kopiert die Dateien aus dem lokalen System auf den Stammknoten. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a> So führen Sie den MapReduce-Auftrag in Hadoop aus

1. Stellen Sie per SSH eine Verbindung mit HDInsight her. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verwenden Sie in der SSH-Sitzung den folgenden Befehl, um die MapReduce-Anwendung auszuführen:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Dieser Befehl startet die Anwendung WordCount MapReduce. Die Eingabedatei ist `/example/data/gutenberg/davinci.txt`, und das Ausgabeverzeichnis ist `/example/data/wordcountout`. Die Eingabedatei und die Ausgabe werden im Standardspeicher für den Cluster gespeichert.

3. Sobald der Auftrag abgeschlossen ist, verwenden Sie den folgenden Befehl zum Anzeigen der Ergebnisse:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Sie sollten eine Liste von Wörtern und Zahlen mit Werten erhalten, die dem folgenden Text ähneln:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie ein Java MapReduce-Auftrag entwickelt wird. In den folgenden Dokumenten finden Sie andere Möglichkeiten zum Arbeiten mit HDInsight.

* [Verwenden von Apache Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Apache Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)

Weitere Informationen finden Sie außerdem im [Java Developer Center](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-power-query]:apache-hadoop-connect-excel-power-query.md

[powershell-PSCredential]: https://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

