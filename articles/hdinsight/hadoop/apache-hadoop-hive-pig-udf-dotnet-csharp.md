---
title: Verwenden von C# mit Apache Hive und Apache Pig für Apache Hadoop in HDInsight – Azure
description: Hier erfahren Sie, wie Sie benutzerdefinierte C#-Funktionen (User-Defined Functions, UDFs) mit Apache Hive- und Apache Pig-Streaming in Azure HDInsight verwenden.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 31738c43756da14ba6c2c92afbcb2882561c8001
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64722874"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-streaming-on-apache-hadoop-in-hdinsight"></a>Verwenden benutzerdefinierter C#-Funktionen mit Apache Hive- und Apache Pig-Streaming für Apache Hadoop in HDInsight

Hier erfahren Sie, wie Sie benutzerdefinierte C#-Funktionen (User-Defined Functions, UDFs) mit Apache Hive und Apache Pig in HDInsight verwenden.

> [!IMPORTANT]
> Die Schritte in diesem Artikel funktionieren Linux- und Windows-basierten HDInsight-Clustern. Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [HDInsight-Komponentenversionen](../hdinsight-component-versioning.md).

Sowohl Hive als auch Pig kann Daten zur Verarbeitung an externe Anwendungen übergeben. Dieser Prozess wird als _Streaming_ bezeichnet. Wenn Sie eine .NET-Anwendung verwenden, werden die Daten über STDIN an die Anwendung übergeben und die Ergebnisse von der Anwendung über STDOUT zurückgegeben. Zum Lesen und Schreiben von STDIN und STDOUT können Sie `Console.ReadLine()` und `Console.WriteLine()` von einer Konsolenanwendung aus verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Gute Kenntnisse im Schreiben und Erstellen von C#-Code für .NET Framework 4.5.

    * Verwenden Sie eine beliebige IDE. Wir empfehlen [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017 oder [Visual Studio-Code](https://code.visualstudio.com/). Für die Schritte in diesem Artikel wird Visual Studio 2017 verwendet.

* Eine Möglichkeit, EXE-Dateien in den Cluster hochzuladen und Pig- und Hive-Aufträge auszuführen. Es wird empfohlen, die Data Lake-Tools für Visual Studio, Azure PowerShell und die klassische Azure CLI zu verwenden. In den Schritten in diesem Artikel werden die Data Lake-Tools für Visual Studio zum Hochladen der Dateien und zum Ausführen der Hive-Beispielabfrage verwendet.

    Informationen zu anderen Methoden zum Ausführen von Hive-Abfragen und Pig-Aufträgen finden Sie in den folgenden Artikeln:

    * [Verwenden von Apache Hive mit HDInsight](hdinsight-use-hive.md)

    * [Verwenden von Apache Pig mit HDInsight](hdinsight-use-pig.md)

* Hadoop für einen HDInsight-Cluster. Weitere Informationen zum Erstellen eines Clusters finden Sie unter [Erstellen eines HDInsight-Clusters](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET in HDInsight

* __Linux-basierte HDInsight__-Cluster, die [Mono (https://mono-project.com)](https://mono-project.com) für die Ausführung von .NET-Anwendungen nutzen. Mono-Version 4.2.1 ist in HDInsight Version 3.6 enthalten.

    Weitere Informationen zur Kompatibilität von Mono mit .NET Framework-Versionen finden Sie unter [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) (Kompatibilität von Mono).

* __Windows-basierte HDInsight__-Cluster verwenden die Microsoft .NET CLR zum Ausführen von Anwendungen für .NET.

Weitere Informationen zu der mit den verschiedenen HDInsight-Versionen bereitgestellten .NET Framework- und Mono-Versionen finden Sie unter [HDInsight-Komponentenversionen](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Erstellen der C\#-Projekte

### <a name="apache-hive-udf"></a>Benutzerdefinierte Apache Hive-Funktion

1. Öffnen Sie Visual Studio, und erstellen Sie eine Lösung. Wählen Sie als Projekttyp **Konsolen-App (.NET Framework)** aus, und geben Sie dem neuen Projekt den Namen **HiveCSharp**.

    > [!IMPORTANT]
    > Wählen Sie __.NET Framework 4.5__ aus, wenn Sie einen Linux-basierten HDInsight-Cluster verwenden. Weitere Informationen zur Kompatibilität von Mono mit .NET Framework-Versionen finden Sie unter [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) (Kompatibilität von Mono).

2. Ersetzen Sie den Inhalt von **Program.cs** durch den folgenden Code:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Erstellen Sie das Projekt.

### <a name="apache-pig-udf"></a>Benutzerdefinierte Apache Pig-Funktion

1. Öffnen Sie Visual Studio, und erstellen Sie eine Lösung. Wählen Sie als Projekttyp **Konsolenanwendung** aus, und geben Sie dem neuen Projekt den Namen **PigUDF**.

2. Ersetzen Sie den Inhalt der Datei **Program.cs** durch den folgenden Code:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Dieser Code analysiert mit Pig gesendete Zeilen und formatiert Zeilen neu, die mit `java.lang.Exception` beginnen.

3. Speichern Sie **Program.cs**, und erstellen Sie dann das Projekt neu.

## <a name="upload-to-storage"></a>Hochladen in den Speicher

1. Öffnen Sie den **Server-Explorer**in Visual Studio.

2. Erweitern Sie erst **Azure** und dann **HDInsight**.

3. Geben Sie bei Aufforderung Ihre Azure-Anmeldeinformationen ein, und klicken Sie auf **Anmelden**.

4. Erweitern Sie den HDInsight-Cluster, in dem Sie diese Anwendung bereitstellen möchten. Ein Eintrag mit dem Text __(Standardspeicherkonto)__ ist aufgeführt.

    ![Anzeige des Speicherkontos für den Cluster im Server-Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Wenn dieser Eintrag erweitert werden kann, verwenden Sie ein __Azure Storage-Konto__ als Standardspeicher für den Cluster. Um die Dateien im Standardspeicher für den Cluster anzuzeigen, erweitern Sie den Eintrag, und doppelklicken Sie dann auf den __(Standardcontainer)__ .

    * Wenn dieser Eintrag nicht erweitert werden kann, verwenden Sie __Azure Data Lake Storage__ als Standardspeicher für den Cluster. Um die Dateien im Standardspeicher für den Cluster anzuzeigen, doppelklicken Sie auf den Eintrag __(Standardspeicherkonto)__ .

6. Laden Sie die EXE-Dateien mithilfe einer der folgenden Methoden hoch:

   * Wenn Sie ein __Azure-Speicherkonto__ verwenden, klicken Sie auf das Symbol für den Upload, und navigieren Sie dann zum Ordner **bin\debug** für das Projekt **HiveCSharp**. Wählen Sie anschließend die Datei **HiveCSharp.exe** aus, und klicken Sie auf **OK**.

       ![Symbol für den Upload](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
   * Wenn Sie __Azure Data Lake Storage__ verwenden, klicken Sie mit der rechten Maustaste auf einen leeren Bereich in der Dateiliste, und wählen Sie dann __Hochladen__ aus. Wählen Sie anschließend die Datei **HiveCSharp.exe** aus, und klicken Sie auf **Öffnen**.

     Sobald der Upload der Datei __HiveCSharp.exe__ abgeschlossen ist, wiederholen Sie den Uploadvorgang für die Datei __PigUDF.exe__.

## <a name="run-an-apache-hive-query"></a>Ausführen einer Apache Hive-Abfrage

1. Öffnen Sie den **Server-Explorer**in Visual Studio.

2. Erweitern Sie erst **Azure** und dann **HDInsight**.

3. Klicken Sie mit der rechten Maustaste auf den Cluster, in dem Sie die Anwendung **HiveCSharp** bereitgestellt haben, und wählen Sie dann **Hive-Abfrage schreiben**.

4. Verwenden Sie für die Hive-Abfrage den folgenden Text:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Kommentieren Sie die `add file`-Anweisung aus, die dem Typ des für Ihren Cluster verwendeten Standardspeichers entspricht.

    Mit dieser Abfrage werden die Felder `clientid`, `devicemake` und `devicemodel` aus `hivesampletable` ausgewählt und an die Anwendung HiveCSharp.exe übergeben. Die Abfrage erwartet, dass die Anwendung drei Felder zurückgibt, die als `clientid`, `phoneLabel` und `phoneHash` gespeichert werden. Darüber hinaus wird bei der Abfrage davon ausgegangen, dass sich „HiveCSharp.exe“ im Stamm des standardmäßigen Speichercontainers befindet.

5. Klicken Sie auf **Senden**, um den Auftrag an den HDInsight-Cluster zu übermitteln. Das Fenster **Hive-Auftrag – Zusammenfassung** wird geöffnet.

6. Klicken Sie auf **Aktualisieren**, um die Zusammenfassung zu aktualisieren, bis sich der **Auftragsstatus** in **Abgeschlossen** ändert. Klicken Sie auf **Auftragsausgabe**, um die Auftragsausgabe anzuzeigen.

## <a name="run-an-apache-pig-job"></a>Ausführen eines Apache Pig-Auftrags

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Beispiel: `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Weitere Informationen finden Sie unter [Verwenden von SSH mit HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Starten Sie die Pig-Befehlszeile mit einem der folgenden Befehle:

        pig

    > [!IMPORTANT]
    > Bei Verwendung eines Windows-basierten Clusters nutzen Sie stattdessen die folgenden Befehle:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Eine `grunt>`-Eingabeaufforderung wird angezeigt.

3. Geben Sie Folgendes ein, um einen Pig-Auftrag auszuführen, der die .NET Framework-Anwendung verwendet:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    Die `DEFINE`-Anweisung erstellt einen Alias von `streamer` für die Anwendung „pigudf.exe“, und `CACHE` lädt sie aus dem Standardspeicher für den Cluster. Später wird `streamer` mit dem `STREAM`-Operator verwendet, um die einzelnen Zeilen in LOG zu verarbeiten und die Daten als Serie von Spalten zurückzugeben.

    > [!NOTE]
    > Der Anwendungsname, der für das Streaming verwendet wird, muss bei der Aliaserstellung mit \` (Gravis) und bei der Verwendung mit `SHIP` von ' (einfaches Anführungszeichen) umschlossen werden.

4. Nach Eingabe der letzten Zeile sollte der Auftrag gestartet werden. Eine Ausgabe ähnlich folgendem Text wird zurückgegeben:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie eine .NET Framework-Anwendung über Hive und Pig in HDInsight verwenden. Informationen zur Verwendung von Python mit Hive und Pig finden Sie unter [Verwenden von Python mit Apache Hive und Apache Pig in HDInsight](python-udf-hdinsight.md).

Informationen zu anderen Möglichkeiten der Verwendung von Pig und Hive sowie Informationen zur Verwendung von MapReduce finden Sie in diesen Dokumenten:

* [Verwenden von Apache Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Apache Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)
