---
title: Verwenden von C# mit MapReduce für Hadoop in HDInsight – Azure
description: Erfahren Sie, wie Sie C# verwenden, um MapReduce-Lösungen mit Apache Hadoop in Azure HDInsight zu erstellen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, seoapr2020, devx-track-csharp
ms.date: 04/28/2020
ms.openlocfilehash: 4153b612b4b15883f28517d806acf6b340a94e49
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543048"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Verwenden von C# mit MapReduce-Streaming auf Apache Hadoop in HDInsight

Erfahren Sie, wie Sie C# verwenden, um eine MapReduce-Lösung in HDInsight zu erstellen.

Mit Apache Hadoop-Streaming können Sie MapReduce-Aufträge mithilfe eines Skripts oder einer ausführbaren Datei ausführen. Hier wird .NET verwendet, um die Mapper- und Reducer-Prozesse für eine Lösung zum Zählen von Wörtern zu implementieren.

## <a name="net-on-hdinsight"></a>.NET in HDInsight

HDInsight-Cluster nutzen [Mono (https://mono-project.com)](https://mono-project.com) für die Ausführung von .NET-Anwendungen. Mono-Version 4.2.1 ist in HDInsight Version 3.6 enthalten. Weitere Informationen über die in HDInsight enthaltene Version von Mono finden Sie unter [Verfügbare Apache Hadoop-Komponenten in verschiedenen Versionen von HDInsight](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions).

Weitere Informationen zur Kompatibilität von Mono mit .NET Framework-Versionen finden Sie unter [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) (Kompatibilität von Mono).

## <a name="how-hadoop-streaming-works"></a>Funktionsweise von Hadoop-Datenströmen

Der in diesem Artikel verwendete grundlegende Prozess für das Streaming sieht wie folgt aus:

1. Hadoop übergibt Daten an den Mapper-Prozess (in diesem Beispiel *mapper.exe* ) auf STDIN.
2. Der Mapper verarbeitet die Daten und gibt durch Tabstoppzeichen getrennte Schlüssel/Wert-Paare an STDOUT aus.
3. Die Ausgabe wird von Hadoop gelesen und dann an den Reducer-Prozess (in diesem Beispiel *reducer.exe* ) auf STDIN übergeben.
4. Der Reducer-Prozess liest die durch Tabstoppzeichen getrennten Schlüssel/Wert-Paare, verarbeitet die Daten und gibt dann das Ergebnis als durch Tabstoppzeichen getrennte Schlüssel/Wert-Paare auf STDOUT aus.
5. Die Ausgabe wird von Hadoop gelesen und in das Ausgabeverzeichnis geschrieben.

Weitere Informationen zum Streaming finden Sie unter [Hadoop-Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Voraussetzungen

* Visual Studio.

* Gute Kenntnisse im Schreiben und Erstellen von C#-Code für .NET Framework 4.5.

* Eine Möglichkeit zum Hochladen von EXE-Dateien in den Cluster. In den Schritten in diesem Artikel werden die Data Lake-Tools für Visual Studio zum Hochladen der Dateien in primären Speicher für den Cluster verwendet.

* Bei Verwendung von PowerShell benötigen Sie das [Az-Modul](/powershell/azure/).

* Ein Apache Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Das URI-Schema für Ihren primären Clusterspeicher. Dieses Schema ist für Azure Storage `wasb://`, für Azure Data Lake Storage Gen2 `abfs://` und für Azure Data Lake Storage Gen1 `adl://`. Wenn die sichere Übertragung für Azure Storage oder Data Lake Storage Gen2 aktiviert ist, lautet der URI `wasbs://` bzw. `abfss://`.

## <a name="create-the-mapper"></a>Erstellen des Mappers

Erstellen Sie in Visual Studio eine neue .NET Framework-Konsolenanwendung mit dem Namen *mapper* . Verwenden Sie für die Anwendung den folgenden Code:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Nachdem Sie die Anwendung erstellt haben, führen Sie den Buildvorgang durch, um die Datei */bin/Debug/mapper.exe* im Projektverzeichnis zu erzeugen.

## <a name="create-the-reducer"></a>Erstellen des Reducers

Erstellen Sie in Visual Studio eine neue .NET Framework-Konsolenanwendung mit dem Namen *reducer* . Verwenden Sie für die Anwendung den folgenden Code:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Nachdem Sie die Anwendung erstellt haben, führen Sie den Buildvorgang durch, um die Datei */bin/Debug/reducer.exe* im Projektverzeichnis zu erzeugen.

## <a name="upload-to-storage"></a>Hochladen in den Speicher

Als nächstes müssen Sie die Anwendungen *mapper* und *reducer* in den HDInsight-Speicher hochladen.

1. Wählen Sie in Visual Studio **Ansicht** > **Server-Explorer** aus.

1. Klicken Sie mit der rechten Maustaste auf den Knoten **Azure** , wählen Sie **Verbindung mit Microsoft Azure-Abonnement herstellen...** aus, und schließen Sie den Anmeldevorgang ab.

1. Erweitern Sie den HDInsight-Cluster, in dem Sie diese Anwendung bereitstellen möchten. Ein Eintrag mit dem Text **(Standardspeicherkonto)** ist aufgeführt.

   ![Speicherkonto, HDInsight-Cluster, Server-Explorer, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Wenn der Eintrag **(Standardspeicherkonto)** erweitert werden kann, verwenden Sie ein **Azure Storage-Konto** als Standardspeicher für den Cluster. Um die Dateien im Standardspeicher für den Cluster anzuzeigen, erweitern Sie den Eintrag, und doppelklicken Sie dann auf **(Standardcontainer)** .

   * Wenn der Eintrag **(Standardspeicherkonto)** nicht erweitert werden kann, verwenden Sie **Azure Data Lake Storage** als Standardspeicher für den Cluster. Um die Dateien im Standardspeicher für den Cluster anzuzeigen, doppelklicken Sie auf den Eintrag **(Standardspeicherkonto)** .

1. Laden Sie die EXE-Dateien mithilfe einer der folgenden Methoden hoch:

    * Wenn Sie ein **Azure Storage-Konto** verwenden, wählen Sie das Symbol **Blob hochladen** aus.

        ![HDInsight – Uploadsymbol für das Projekt „mapper“, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Wählen Sie im Dialogfeld **Neue Datei hochladen** unter **Dateiname** die Option **Durchsuchen** aus. Wechseln Sie im Dialogfeld **Blob hochladen** zum Ordner *bin\debug* dieses *mapper* -Projekts, und wählen Sie dann die Datei *mapper.exe* aus. Wählen Sie abschließend **Öffnen** und dann **OK** aus, um den Upload abzuschließen.

    * Wenn Sie **Azure Data Lake Storage** verwenden, klicken Sie mit der rechten Maustaste auf einen leeren Bereich in der Dateiliste, und wählen Sie dann **Hochladen** aus. Wählen Sie abschließend die Datei *mapper.exe* und dann **Öffnen** aus.

    Sobald der Upload der Datei *mapper.exe* abgeschlossen ist, wiederholen Sie den Uploadvorgang für die Datei *reducer.exe* .

## <a name="run-a-job-using-an-ssh-session"></a>Ausführen eines Auftrags: Verwenden einer SSH-Sitzung

Im folgenden Verfahren wird beschrieben, wie Sie einen MapReduce-Auftrag über eine SSH-Sitzung ausführen:

1. Verwenden Sie einen [ssh-Befehl](../hdinsight-hadoop-linux-use-ssh-unix.md) zum Herstellen der Verbindung mit dem Cluster. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verwenden Sie zum Starten des MapReduce-Auftrags einen der folgenden Befehle:

   * Wenn der Standardspeicher **Azure Storage** ist:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Wenn der Standardspeicher **Data Lake Storage Gen1** : ist:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Wenn der Standardspeicher **Data Lake Storage Gen2** : ist:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   In der folgenden Liste wird beschrieben, was die einzelnen Parameter und Optionen darstellen:

   |Parameter | BESCHREIBUNG |
   |---|---|
   |hadoop-streaming.jar|Gibt die JAR-Datei an, die die MapReduce-Streamingfunktionen enthält.|
   |-files|Gibt die Dateien *mapper.exe* und *reducer.exe* für diesen Auftrag an. Die Protokolldeklaration `wasbs:///`, `adl:///` oder `abfs:///` vor jeder Datei ist der Pfad zum Stamm des Standardspeichers für den Cluster.|
   |-mapper|Gibt die Datei an, die den Mapper implementiert.|
   |-reducer|Gibt die Datei an, die den Reducer implementiert.|
   |-input|Gibt die Eingabedaten an.|
   |-output|Gibt das Ausgabeverzeichnis an.|

1. Wenn der MapReduce-Auftrag abgeschlossen ist, können Sie die Ergebnisse mit dem folgenden Befehl anzeigen:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   Der folgende Text ist ein Beispiel der von diesem Befehl zurückgegebenen Daten:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Ausführen eines Auftrags: PowerShell

Verwenden Sie das folgende PowerShell-Skript zum Ausführen eines MapReduce-Auftrags und zum Herunterladen der Ergebnisse.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Dieses Skript fordert Sie auf den Kontonamen und das Kennwort für die Clusteranmeldung sowie den HDInsight-Clusternamen einzugeben. Wenn der Auftrag abgeschlossen wurde, wird die Ausgabe in die Datei *output.txt* heruntergeladen. Der folgende Text ist ein Beispiel für die Daten in der Datei `output.txt`:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md)
* [Verwenden einer benutzerdefinierter C#-Funktion mit Apache Hive und Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Entwickeln von Java MapReduce-Programmen](apache-hadoop-develop-deploy-java-mapreduce-linux.md)