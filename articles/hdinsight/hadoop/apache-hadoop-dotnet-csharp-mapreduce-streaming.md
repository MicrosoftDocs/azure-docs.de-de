---
title: Verwenden von C# mit MapReduce für Hadoop in HDInsight – Azure
description: Erfahren Sie, wie Sie C# verwenden, um MapReduce-Lösungen mit Apache Hadoop in Azure HDInsight zu erstellen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: b06f19736c4d50ab7d246a5c71da04ada95b6f98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64727364"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Verwenden von C# mit MapReduce-Streaming auf Apache Hadoop in HDInsight

Erfahren Sie, wie Sie C# verwenden, um eine MapReduce-Lösung in HDInsight zu erstellen.

> [!IMPORTANT]
> Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [HDInsight-Komponentenversionen](../hdinsight-component-versioning.md).

Mit Apache Hadoop-Streaming können Sie MapReduce-Aufträge über ein Skript oder eine ausführbare Datei ausführen. In diesem Beispiel wird .NET verwendet, um die Mapper- und Reducer-Prozesse für eine Lösung zum Zählen von Wörtern zu implementieren.

## <a name="net-on-hdinsight"></a>.NET in HDInsight

__Linux-basierte HDInsight__-Cluster nutzen [Mono (https://mono-project.com)](https://mono-project.com) für die Ausführung von .NET-Anwendungen. Mono-Version 4.2.1 ist in HDInsight Version 3.6 enthalten. Weitere Informationen zu der mit HDInsight bereitgestellten Version von Mono finden Sie unter [HDInsight-Komponentenversionen](../hdinsight-component-versioning.md). 

Weitere Informationen zur Kompatibilität von Mono mit .NET Framework-Versionen finden Sie unter [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) (Kompatibilität von Mono).

## <a name="how-hadoop-streaming-works"></a>Funktionsweise von Hadoop-Datenströmen

Der in diesem Artikel verwendete grundlegende Prozess für das Streaming sieht wie folgt aus:

1. Hadoop übergibt Daten an den Mapper-Prozess (in diesem Beispiel „mapper.exe“) auf STDIN.
2. Der Mapper verarbeitet die Daten und gibt durch Tabstoppzeichen getrennte Schlüssel/Wert-Paare an STDOUT aus.
3. Die Ausgabe wird von Hadoop gelesen und dann an den Reducer-Prozess (in diesem Beispiel „reducer.exe“) auf STDIN übergeben.
4. Der Reducer-Prozess liest die durch Tabstoppzeichen getrennten Schlüssel/Wert-Paare, verarbeitet die Daten und gibt dann das Ergebnis als durch Tabstoppzeichen getrennte Schlüssel/Wert-Paare auf STDOUT aus.
5. Die Ausgabe wird von Hadoop gelesen und in das Ausgabeverzeichnis geschrieben.

Weitere Informationen zum Streaming finden Sie unter [Hadoop-Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Voraussetzungen

* Gute Kenntnisse im Schreiben und Erstellen von C#-Code für .NET Framework 4.5. Für die Schritte in diesem Artikel wird Visual Studio 2017 verwendet.

* Eine Möglichkeit zum Hochladen von EXE-Dateien in den Cluster. In den Schritten in diesem Artikel werden die Data Lake-Tools für Visual Studio zum Hochladen der Dateien in primären Speicher für den Cluster verwendet.

* Azure PowerShell oder ein SSH-Client

* Hadoop für einen HDInsight-Cluster. Weitere Informationen zum Erstellen eines Clusters finden Sie unter [Erstellen eines HDInsight-Clusters](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Erstellen des Mappers

Erstellen Sie in Visual Studio eine neue __Konsolenanwendung__ mit dem Namen __mapper__. Verwenden Sie für die Anwendung den folgenden Code:

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

Nach dem Erstellen der Anwendung erzeugen Sie mit ihrer Hilfe die Datei `/bin/Debug/mapper.exe` im Projektverzeichnis.

## <a name="create-the-reducer"></a>Erstellen des Reducers

Erstellen Sie in Visual Studio eine neue __Konsolenanwendung__ mit dem Namen __reducer__. Verwenden Sie für die Anwendung den folgenden Code:

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

Nach dem Erstellen der Anwendung erzeugen Sie mit ihrer Hilfe die Datei `/bin/Debug/reducer.exe` im Projektverzeichnis.

## <a name="upload-to-storage"></a>Hochladen in den Speicher

1. Öffnen Sie den **Server-Explorer**in Visual Studio.

2. Erweitern Sie erst **Azure** und dann **HDInsight**.

3. Geben Sie bei Aufforderung Ihre Azure-Anmeldeinformationen ein, und klicken Sie auf **Anmelden**.

4. Erweitern Sie den HDInsight-Cluster, in dem Sie diese Anwendung bereitstellen möchten. Ein Eintrag mit dem Text __(Standardspeicherkonto)__ ist aufgeführt.

    ![Anzeige des Speicherkontos für den Cluster im Server-Explorer](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Wenn dieser Eintrag erweitert werden kann, verwenden Sie ein __Azure Storage-Konto__ als Standardspeicher für den Cluster. Um die Dateien im Standardspeicher für den Cluster anzuzeigen, erweitern Sie den Eintrag, und doppelklicken Sie dann auf den __(Standardcontainer)__ .

    * Wenn dieser Eintrag nicht erweitert werden kann, verwenden Sie __Azure Data Lake Storage__ als Standardspeicher für den Cluster. Um die Dateien im Standardspeicher für den Cluster anzuzeigen, doppelklicken Sie auf den Eintrag __(Standardspeicherkonto)__ .

5. Laden Sie die EXE-Dateien mithilfe einer der folgenden Methoden hoch:

   * Wenn Sie ein __Azure Storage-Konto__ verwenden, klicken Sie auf das Symbol für den Upload, und navigieren Sie dann zum Ordner **bin\debug** für das Projekt **mapper**. Wählen Sie anschließend die Datei **mapper.exe** aus, und klicken Sie auf **OK**.

       ![Symbol für den Upload](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
   * Wenn Sie __Azure Data Lake Storage__ verwenden, klicken Sie mit der rechten Maustaste auf einen leeren Bereich in der Dateiliste, und wählen Sie dann __Hochladen__ aus. Wählen Sie anschließend die Datei **mapper.exe** aus, und klicken Sie auf **Öffnen**.

     Sobald der Upload der Datei __mapper.exe__ abgeschlossen ist, wiederholen Sie den Uploadvorgang für die Datei __reducer.exe__.

## <a name="run-a-job-using-an-ssh-session"></a>Ausführen eines Auftrags: Verwenden einer SSH-Sitzung

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verwenden Sie zum Starten des MapReduce-Auftrags einen der folgenden Befehle:

   * Wenn Sie __Data Lake Storage Gen2__ als Standardspeicher verwenden:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * Wenn Sie __Data Lake Storage Gen1__ als Standardspeicher verwenden:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * Wenn Sie __Azure-Speicher__ als Standardspeicher verwenden:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     In der folgenden Liste ist die Funktionsweise der einzelnen Parameter beschrieben:

   * `hadoop-streaming.jar`: Die JAR-Datei, die die MapReduce-Streamingfunktionen enthält.
   * `-files`: Fügt die Dateien `mapper.exe` und `reducer.exe` zu diesem Auftrag hinzu. Die Zeichenfolge `abfs:///`, `adl:///` oder `wasb:///` vor jeder Datei ist der Pfad zum Stamm des Standardspeichers für den Cluster.
   * `-mapper`: Gibt an, welche Datei den Mapper implementiert.
   * `-reducer`: Gibt an, welche Datei den Reducer implementiert.
   * `-input`: Die Eingabedaten.
   * `-output`: Das Ausgabeverzeichnis.

3. Wenn der MapReduce-Auftrag abgeschlossen ist, können Sie die Ergebnisse wie folgt anzeigen:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Der folgende Text ist ein Beispiel der von diesem Befehl zurückgegebenen Daten:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Ausführen eines Auftrags: Verwenden von PowerShell

Verwenden Sie das folgende PowerShell-Skript zum Ausführen eines MapReduce-Auftrags und zum Herunterladen der Ergebnisse.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Dieses Skript fordert Sie auf den Kontonamen und das Kennwort für die Clusteranmeldung sowie den HDInsight-Clusternamen einzugeben. Wenn der Auftrag abgeschlossen wurde, wird die Ausgabe in die Datei `output.txt` heruntergeladen. Der folgende Text ist ein Beispiel für die Daten in der Datei `output.txt`:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwenden von MapReduce mit HDInsight finden Sie unter [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md).

Informationen zur Verwendung von C# mit Hive und Pig finden Sie unter [Verwenden benutzerdefinierter C#-Funktionen mit Apache Hive- und Apache Pig-Streaming für Apache Hadoop in HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Informationen zum Verwenden von C# mit Storm in HDInsight finden Sie unter [Entwickeln von C#-Topologien für Apache Storm mithilfe von Data Lake-Tools für Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
