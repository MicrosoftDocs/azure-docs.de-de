---
title: C#, Apache Hive & Apache Pig auf Apache Hadoop – Azure HDInsight
description: Hier erfahren Sie, wie Sie benutzerdefinierte C#-Funktionen (User-Defined Functions, UDFs) mit Apache Hive- und Apache Pig-Streaming in Azure HDInsight verwenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: e99d68d31f1da4dcb3ef1086a2bbd90f0ab30410
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023124"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Verwenden benutzerdefinierter C#-Funktionen mit Apache Hive und Apache Pig für Apache Hadoop in HDInsight

Hier erfahren Sie, wie Sie benutzerdefinierte C#-Funktionen (User-Defined Functions, UDFs) mit [Apache Hive](https://hive.apache.org) und [Apache Pig](https://pig.apache.org) in HDInsight verwenden.

> [!IMPORTANT]
> Die Schritte in diesem Dokument funktionieren mit Linux-basierten HDInsight-Clustern. Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [HDInsight-Komponentenversionen](../hdinsight-component-versioning.md).

Sowohl Hive als auch Pig kann Daten zur Verarbeitung an externe Anwendungen übergeben. Dieser Prozess wird als _Streaming_ bezeichnet. Wenn Sie eine .NET-Anwendung verwenden, werden die Daten über STDIN an die Anwendung übergeben und die Ergebnisse von der Anwendung über STDOUT zurückgegeben. Zum Lesen und Schreiben von STDIN und STDOUT können Sie `Console.ReadLine()` und `Console.WriteLine()` von einer Konsolenanwendung aus verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Gute Kenntnisse im Schreiben und Erstellen von C#-Code für .NET Framework 4.5.

    Verwenden Sie eine beliebige IDE. Wir empfehlen [Visual Studio](https://www.visualstudio.com/vs) oder [Visual Studio Code](https://code.visualstudio.com/). Für die Schritte in diesem Artikel wird Visual Studio 2019 verwendet.

* Eine Möglichkeit, EXE-Dateien in den Cluster hochzuladen und Pig- und Hive-Aufträge auszuführen. Wir empfehlen die [Data Lake-Tools für Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md), [Azure PowerShell](/powershell/azure) und die [Azure CLI](/cli/azure/install-azure-cli). In den Schritten in diesem Artikel werden die Data Lake-Tools für Visual Studio zum Hochladen der Dateien und zum Ausführen der Hive-Beispielabfrage verwendet.

    Informationen über weitere Möglichkeiten für die Ausführung von Hive-Abfragen finden Sie unter [Was sind Apache Hive und HiveQL in Azure HDInsight?](hdinsight-use-hive.md).

* Hadoop für einen HDInsight-Cluster. Weitere Informationen zum Erstellen eines Clusters finden Sie unter [Erstellen von HDInsight-Clustern](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET in HDInsight

*Linux-basierte HDInsight*-Cluster nutzen [Mono (https://mono-project.com)](https://mono-project.com) für die Ausführung von .NET-Anwendungen. Mono-Version 4.2.1 ist in HDInsight Version 3.6 enthalten.

Weitere Informationen zur Kompatibilität von Mono mit .NET Framework-Versionen finden Sie unter [Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/) (Kompatibilität von Mono).

Weitere Informationen zu der mit den verschiedenen HDInsight-Versionen bereitgestellten .NET Framework- und Mono-Versionen finden Sie unter [HDInsight-Komponentenversionen](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Erstellen der C\#-Projekte

In den folgenden Abschnitten wird beschrieben, wie Sie ein C#-Projekt in Visual Studio für eine benutzerdefinierte Apache Hive- und eine benutzerdefinierte Apache Pig-Funktion erstellen.

### <a name="apache-hive-udf"></a>Benutzerdefinierte Apache Hive-Funktion

So erstellen Sie ein C#-Projekt für eine benutzerdefinierte Apache Hive-Funktion:

1. Starten Sie Visual Studio.

2. Wählen Sie **Neues Projekt erstellen** aus.

3. Wählen Sie im Fenster **Neues Projekt erstellen** die C#-Version der Vorlage **Konsolen-App (.NET Framework)** aus. Wählen Sie **Weiter** aus.

4. Geben Sie im Fenster **Neues Projekt konfigurieren** den **Projektnamen** *HiveCSharp* ein, und navigieren Sie zum **Speicherort** für das neue Projekt, oder erstellen Sie einen. Klicken Sie anschließend auf **Erstellen**.

5. Ersetzen Sie den Inhalt von *Program.cs* in Visual Studio IDE durch den folgenden Code:

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

6. Wählen Sie auf der Menüleiste **Erstellen** > **Projektmappe erstellen** aus, um das Projekt zu erstellen.

7. Schließen Sie die Projektmappe.

### <a name="apache-pig-udf"></a>Benutzerdefinierte Apache Pig-Funktion

So erstellen Sie ein C#-Projekt für eine benutzerdefinierte Apache Hive-Funktion:

1. Öffnen Sie Visual Studio.

2. Wählen Sie im Fenster **Start** die Option **Neues Projekt erstellen** aus.

3. Wählen Sie im Fenster **Neues Projekt erstellen** die C#-Version der Vorlage **Konsolen-App (.NET Framework)** aus. Wählen Sie **Weiter** aus.

4. Geben Sie im Fenster **Neues Projekt konfigurieren** den **Projektnamen** *PigUDF* ein, und navigieren Sie zum **Speicherort** für das neue Projekt oder erstellen Sie einen. Klicken Sie anschließend auf **Erstellen**.

5. Ersetzen Sie den Inhalt von *Program.cs* in Visual Studio IDE durch den folgenden Code:

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

6. Wählen Sie in der Menüleiste **Erstellen** > **Projektmappe erstellen** aus, um das Projekt zu erstellen.

7. Lassen Sie die Projektmappe geöffnet.

## <a name="upload-to-storage"></a>Hochladen in den Speicher

Laden Sie als Nächstes die Hive- und Pig-Anwendungen für benutzerdefinierte Funktionen in einen Speicher eines HDInsight-Clusters hoch.

1. Wählen Sie in Visual Studio **Ansicht** > **Server-Explorer** aus.

1. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf **Azure**, klicken Sie auf **Verbindung mit Microsoft Azure-Abonnement herstellen**, und melden Sie sich an.

1. Erweitern Sie den HDInsight-Cluster, in dem Sie diese Anwendung bereitstellen möchten. Ein Eintrag mit dem Text **(Standardspeicherkonto)** ist aufgeführt.

    ![Standardspeicherkonto, HDInsight-Cluster, Server-Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Wenn dieser Eintrag erweitert werden kann, verwenden Sie ein **Azure Storage-Konto** als Standardspeicher für den Cluster. Um die Dateien im Standardspeicher für den Cluster anzuzeigen, erweitern Sie den Eintrag, und doppelklicken Sie dann auf den **(Standardcontainer)** .

    * Wenn dieser Eintrag nicht erweitert werden kann, verwenden Sie **Azure Data Lake Storage** als Standardspeicher für den Cluster. Um die Dateien im Standardspeicher für den Cluster anzuzeigen, doppelklicken Sie auf den Eintrag **(Standardspeicherkonto)** .

1. Laden Sie die EXE-Dateien mithilfe einer der folgenden Methoden hoch:

    * Wenn Sie ein **Azure Storage-Konto** verwenden, wählen Sie das Symbol **Blob hochladen** aus.

        ![HDInsight-Uploadsymbol für neues Projekt](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Wählen Sie im Dialogfeld **Neue Datei hochladen** unter **Dateiname** die Option **Durchsuchen** aus. Wechseln Sie im Dialogfeld **Blob hochladen** zum Ordner *bin\debug* dieses *HiveCSharp*-Projekts, und wählen Sie dann die Datei *HiveCSharp.exe* aus. Wählen Sie abschließend **Öffnen** und dann **OK** aus, um den Upload abzuschließen.

    * Wenn Sie **Azure Data Lake Storage** verwenden, klicken Sie mit der rechten Maustaste auf einen leeren Bereich in der Dateiliste, und wählen Sie dann **Hochladen** aus. Wählen Sie abschließend die Datei *HiveCSharp.exe* aus und dann **Öffnen**.

    Sobald der Upload der Datei *HiveCSharp.exe* abgeschlossen ist, wiederholen Sie den Uploadvorgang für die Datei *PigUDF.exe*.

## <a name="run-an-apache-hive-query"></a>Ausführen einer Apache Hive-Abfrage

Nun können Sie eine Hive-Abfrage ausführen, die ihre Hive-Anwendung für benutzerdefinierte Funktionen verwendet.

1. Wählen Sie in Visual Studio **Ansicht** > **Server-Explorer** aus.

2. Erweitern Sie erst **Azure** und dann **HDInsight**.

3. Klicken Sie mit der rechten Maustaste auf den Cluster, in dem Sie die Anwendung *HiveCSharp* bereitgestellt haben, und wählen Sie dann **Hive-Abfrage schreiben**.

4. Verwenden Sie für die Hive-Abfrage den folgenden Text:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
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

    Mit dieser Abfrage werden die Felder `clientid`, `devicemake` und `devicemodel` aus `hivesampletable` ausgewählt und an die Anwendung *HiveCSharp.exe* übergeben. Die Abfrage erwartet, dass die Anwendung drei Felder zurückgibt, die als `clientid`, `phoneLabel` und `phoneHash` gespeichert werden. Darüber hinaus wird bei der Abfrage davon ausgegangen, dass sich *HiveCSharp.exe* im Stamm des standardmäßigen Speichercontainers befindet.

5. Ändern Sie die Standardeinstellung **Interaktiv** in **Batch**, und wählen Sie dann **Senden** aus, um den Auftrag an den HDInsight-Cluster zu übermitteln. Das Fenster **Hive-Auftrag – Zusammenfassung** wird geöffnet.

6. Wählen Sie **Aktualisieren** aus, um die Zusammenfassung zu aktualisieren, bis sich der **Auftragsstatus** in **Abgeschlossen** ändert. Wählen Sie **Auftragsausgabe** aus, um die Auftragsausgabe anzuzeigen.

## <a name="run-an-apache-pig-job"></a>Ausführen eines Apache Pig-Auftrags

Sie können auch einen Pig-Auftrag ausführen, der Ihre Pig-Anwendung für benutzerdefinierte Funktionen verwendet.

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. (Führen Sie zum Beispiel den Befehl `ssh sshuser@<clustername>-ssh.azurehdinsight.net` aus.) Weitere Informationen finden Sie unter [Verwenden von SSH mit HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Starten Sie die Pig-Befehlszeile mit dem folgenden Befehl:

    ```shell
    pig
    ```

    Eine `grunt>`-Eingabeaufforderung wird angezeigt.

3. Geben Sie Folgendes ein, um einen Pig-Auftrag auszuführen, der die .NET Framework-Anwendung verwendet:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    Die `DEFINE`-Anweisung erstellt einen Alias von `streamer` für die Anwendung *PigUDF.exe*, und `CACHE` lädt sie aus dem Standardspeicher für den Cluster. Später wird `streamer` mit dem `STREAM`-Operator verwendet, um die einzelnen Zeilen in `LOG` zu verarbeiten und die Daten als Serie von Spalten zurückzugeben.

    > [!NOTE]
    > Der Anwendungsname, der für das Streaming verwendet wird, muss bei der Aliaserstellung mit \` (Graviszeichen) und bei der Verwendung mit `SHIP` von ' (einfaches Anführungszeichen) umschlossen werden.

4. Nach Eingabe der letzten Zeile sollte der Auftrag gestartet werden. Eine Ausgabe ähnlich folgendem Text wird zurückgegeben:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Beenden Sie Pig mit `exit`.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie eine .NET Framework-Anwendung über Hive und Pig in HDInsight verwenden. Informationen zur Verwendung von Python mit Hive und Pig finden Sie unter [Verwenden von Python mit Apache Hive und Apache Pig in HDInsight](python-udf-hdinsight.md).

Weitere Möglichkeiten der Verwendung von Hive sowie Informationen zur Verwendung von MapReduce finden Sie in diesen Artikeln:

* [Verwenden von Apache Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)
* [Pig Latin-Grundlagen](https://pig.apache.org/docs/latest/basic.html)
