---
title: Benutzerdefinierte Java-Funktion (UDF) mit Apache Hive – Azure HDInsight
description: Hier erfahren Sie, wie Sie eine Java-basierte benutzerdefinierte Funktion (UDF) zur Verwendung mit Apache Hive erstellen. Diese benutzerdefinierte Beispielfunktion (UDF) konvertiert eine Tabelle von Textzeichenfolgen in Kleinbuchstaben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: 1e0c16ee7aa66847cc6f3f4618b8090b7ce86663
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533783"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Verwenden einer benutzerdefinierten Java-Funktion mit Apache Hive in HDInsight

Hier erfahren Sie, wie Sie eine Java-basierte benutzerdefinierte Funktion (UDF) zur Verwendung mit Apache Hive erstellen. Die benutzerdefinierte Java-Funktion (UDF) in diesem Beispiel konvertiert eine Tabelle von Textzeichenfolgen in klein geschriebene Zeichen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK), Version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* Ordnungsgemäße [Installation](https://maven.apache.org/install.html) von [Apache Maven](https://maven.apache.org/download.cgi) (gemäß Apache).  Maven ist ein Projekterstellungssystem für Java-Projekte.
* Das [URI-Schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) für Ihren primären Clusterspeicher. Dies ist „wasb://“ für Azure Storage, „abfs://“ für Azure Data Lake Storage Gen2 oder „adl://“ für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Azure Storage aktiviert ist, lautet der URI `wasbs://`.  Siehe auch [Vorschreiben einer sicheren Übertragung in Azure Storage](../../storage/common/storage-require-secure-transfer.md).

* Ein Text-Editor oder eine Java-IDE

    > [!IMPORTANT]  
    > Wenn Sie die Python-Dateien auf einem Windows-Client erstellen, müssen Sie einen Editor verwenden, der als Zeilenende LF verwendet. Wenn Sie nicht sicher sind, ob der Editor LF oder CRLF verwendet, finden Sie im Abschnitt [Problembehandlung](#troubleshooting) Schritte für das Entfernen des CR-Zeichens.

## <a name="test-environment"></a>Testumgebung

Für diesen Artikel wurde ein Computer unter Windows 10 verwendet.  Die Befehle wurden an einer Eingabeaufforderung ausgeführt, und die verschiedenen Dateien wurden mit dem Windows-Editor bearbeitet. Nehmen Sie die Ihrer Umgebung entsprechenden Änderungen vor.

Geben Sie an einer Eingabeaufforderung die folgenden Befehle ein, um eine Arbeitsumgebung zu erstellen:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Erstellen Sie einer Beispiel-UDF in Java

1. Geben Sie zum Erstellen eines neuen Maven-Projekts den folgenden Befehl ein:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Mit diesem Befehl wird ein Verzeichnis mit dem Namen `exampleudf` erstellt, das das Maven-Projekt enthält.

2. Löschen Sie nach der Erstellung des Projekts das Verzeichnis `exampleudf/src/test`, das als Teil des Projekts erstellt wurde. Geben Sie dazu den folgenden Befehl ein:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Geben Sie den folgenden Befehl ein, um `pom.xml` zu öffnen:

    ```cmd
    notepad pom.xml
    ```

    Ersetzen Sie dann den vorhandenen Eintrag `<dependencies>` durch den folgenden XML-Code:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Diese Einträge geben die Version von Hadoop und Hive an, die in HDInsight 3.6 enthalten sind. Informationen zu den in HDInsight enthaltenen Versionen von Hadoop und Hive finden Sie im Dokument zu den [Versionen von HDInsight-Komponenten](../hdinsight-component-versioning.md) .

    Fügen Sie am Ende der Datei den Abschnitt `<build>` der Zeile `</project>` hinzu. Dieser Abschnitt sollte folgenden XML-Code enthalten:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
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
        </plugins>
    </build>
    ```

    Diese Einträge definieren, wie das Projekt erstellt werden soll. Insbesondere die Version von Java, die das Projekt verwendet, und wie Sie ein „Uberjar“ für die Bereitstellung im Cluster zu erstellen.

    Speichern Sie die Datei, nachdem die Änderungen erfolgt sind.

4. Geben Sie den folgenden Befehl ein, um die neue Datei `ExampleUDF.java` zu erstellen und zu öffnen:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Kopieren Sie den folgenden Java-Code, und fügen Sie ihn in die neue Datei ein. Schließen Sie dann die Datei.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Mit diesem Code wird eine UDF implementiert, die einen Zeichenfolgenwert verwendet und die Zeichenfolge in Kleinbuchstaben zurückgibt.

## <a name="build-and-install-the-udf"></a>Erstellen und Installieren der UDF

Ersetzen Sie `sshuser` in den folgenden Befehlen durch den tatsächlichen Benutzernamen, falls dieser abweicht. Ersetzen Sie `mycluster` durch den tatsächlichen Clusternamen.

1. Geben Sie den folgenden Befehl ein, um die UDF zu kompilieren und zu packen:

    ```cmd
    mvn compile package
    ```

    Dieser Befehl erstellt und packt die benutzerdefinierte Funktion in der Datei `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar`.

2. Geben Sie den folgenden `scp`-Befehl ein, um die Datei in den HDInsight-Cluster zu kopieren:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Geben Sie den folgenden Befehl ein, um mithilfe von SSH eine Verbindung mit dem Cluster herzustellen:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Kopieren Sie in der geöffneten SSH-Sitzung die JAR-Datei in den HDInsight-Speicher.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Verwenden der UDF in Hive

1. Geben Sie den folgenden Befehl ein, um den Beeline-Client in der SSH-Sitzung zu starten:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Dieser Befehl setzt voraus, dass Sie die Standardeinstellung **admin** für das Anmeldekonto Ihres Clusters verwendet haben.

2. Sobald Sie bei der Eingabeaufforderung `jdbc:hive2://localhost:10001/>` ankommen, geben Sie Folgendes ein, um die UDF zu Hive hinzuzufügen und sie als Funktion verfügbar zu machen.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Verwenden Sie die UDF, um aus einer Tabelle abgerufene Werte in Zeichenfolgen mit Kleinbuchstaben zu konvertieren.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Durch diese Abfrage wird der Status in der Tabelle ausgewählt und die Zeichenfolge in Kleinbuchstaben umgewandelt. Beide werden dann zusammen mit dem unveränderten Namen angezeigt. Die Ausgabe sieht in etwa wie folgt aus:

    ```output
    +---------------+---------------+--+
    |  exampleudf   |     state     |
    +---------------+---------------+--+
    | california    | California    |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | utah          | Utah          |
    | utah          | Utah          |
    | colorado      | Colorado      |
    +---------------+---------------+--+
    ```

## <a name="troubleshooting"></a>Problembehandlung

Bei der Ausführung des Hive-Auftrags kann ein ähnlicher Fehler wie der folgende Text auftreten:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Dieses Problem kann durch die Zeilenenden in der Python-Datei verursacht werden. Viele Windows-Editoren verwenden als Zeilenende standardmäßig CRLF, Linux-Anwendung erwarten jedoch i. d. R. LF.

Sie können die folgenden PowerShell-Anweisungen verwenden, um die CR-Zeichen zu entfernen, bevor Sie die Datei in HDInsight hochladen:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Nächste Schritte

Andere Möglichkeiten zum Arbeiten mit Hive finden Sie unter [Verwenden von Apache Hive mit HDInsight](hdinsight-use-hive.md).

Weitere Informationen zu benutzerdefinierten Hive-Funktionen finden Sie im Abschnitt [Apache Hive Operators and User-Defined Functions](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (Apache Hive-Operatoren und benutzerdefinierte Funktionen) des Hive-Wikis auf „apache.org“.